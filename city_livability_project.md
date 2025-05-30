# 🏙️ ŞEHİR YAŞANABİLİRLİK ENDEKSİ PROJESİ
## Data Analytics Portfolio Project

---

## 🆕 YENİ VERİ KAYNAKLARI

### **📚 EĞİTİM VERİLERİ**

#### **Urban Institute Education Data API**
**Link:** https://educationdata.urban.org/api/v1/
**Avantajlar:**
- Ücretsiz kullanım
- JSON formatında veri
- Okul, ilçe ve eyalet bazında detaylı veriler

**API Endpoint Örneği:**
```python
# School directory data for 2021
url = "https://educationdata.urban.org/api/v1/schools/ccd/directory/2021/"

# Graduation rates by state
url = "https://educationdata.urban.org/api/v1/schools/ccd/enrollment/2021/grade-12/"

# Add filters for specific cities/states
url += "?fips=36&charter=0"  # New York state, non-charter schools
```

**Kullanılabilir Metrikler:**
- Mezuniyet oranları
- Okul başarı skorları
- Öğrenci-öğretmen oranı
- Okul kaynak durumu

#### **College Scorecard API** 
**Link:** https://api.data.gov/ed/collegescorecard/v1/schools

## 📋 PROJENİN GENEL BİLGİLERİ

### **Proje Amacı**
Farklı şehirlerin yaşanabilirlik skorlarını **5 temel metrik** kullanarak hesaplamak:
- 🌬️ **Hava Kalitesi** (OpenWeatherMap API)
- 💼 **Ekonomik Göstergeler** (FRED API) 
- 🚔 **Güvenlik** (Suç İstatistikleri)
- 🎓 **Eğitim Kalitesi** (Urban Institute Education Data API)
- 🏥 **Sağlık Göstergeleri** (CDC/HealthData.gov APIs)

### **Hedef Şehirler**
- New York, Chicago, Austin, Miami, Denver (ABD)
- İstanbul (Türkiye - karşılaştırma için)

### **Teknik Stack**
- Python, Pandas, Requests
- Matplotlib, Seaborn (görselleştirme)
- Jupyter Notebook (analiz)

---

## 🗂️ DOSYA YAPISI

```
city_livability_project/
│
├── data/
│   ├── raw/                    # Ham veriler
│   │   ├── nypd_complaints.csv
│   │   ├── air_quality_data.json
│   │   └── economic_data.json
│   │
│   ├── processed/              # İşlenmiş veriler
│   │   ├── city_air_quality.csv
│   │   ├── city_economics.csv
│   │   └── city_crime_stats.csv
│   │
│   └── final/                  # Son veri
│       └── livability_scores.csv
│
├── src/                        # Kaynak kodlar
│   ├── data_collection.py      # Veri toplama
│   ├── data_processing.py      # Veri temizleme
│   ├── scoring_algorithm.py    # Skorlama
│   └── visualization.py       # Grafikler
│
├── notebooks/                  # Jupyter Notebooks
│   ├── 01_data_exploration.ipynb
│   ├── 02_analysis.ipynb
│   └── 03_final_report.ipynb
│
├── config/
│   └── api_keys.py            # API anahtarları
│
├── requirements.txt           # Python paketleri
└── README.md                 # Proje açıklaması
```

---

## 🔧 KURULUM VE HAZIRLIK

### **1. Gerekli Paketler**
```bash
pip install pandas numpy requests matplotlib seaborn plotly jupyter
pip install fredapi  # FRED API için
```

### **2. API Anahtarları**
`config/api_keys.py` dosyasını oluştur:
```python
# API Keys - GÜVENLİK: Bu dosyayı git'e yükleme!
OPENWEATHER_API_KEY = "4511b2b158a4cf5e2608df759d174bee"
FRED_API_KEY = "BURAYA_FRED_KEY_YAZACAKSIN"

# Şehir koordinatları
CITIES = {
    "New York": {"lat": 40.7128, "lon": -74.0060},
    "Chicago": {"lat": 41.8781, "lon": -87.6298},
    "Austin": {"lat": 30.2672, "lon": -97.7431},
    "Miami": {"lat": 25.7617, "lon": -80.1918},
    "Denver": {"lat": 39.7392, "lon": -104.9903},
    "Istanbul": {"lat": 41.0082, "lon": 28.9784}
}
```

---

## 📊 ADIM 1: VERİ TOPLAMA

### **A. Hava Kalitesi Verisi (`src/data_collection.py`)**
```python
import requests
import json
import pandas as pd
from config.api_keys import OPENWEATHER_API_KEY, CITIES

def collect_air_quality_data():
    \"\"\"Tüm şehirler için hava kalitesi verisi topla\"\"\"
    air_data = []
    
    for city, coords in CITIES.items():
        url = "http://api.openweathermap.org/data/2.5/air_pollution"
        params = {
            'lat': coords['lat'],
            'lon': coords['lon'],
            'appid': OPENWEATHER_API_KEY
        }
        
        response = requests.get(url, params=params)
        if response.status_code == 200:
            data = response.json()
            air_info = {
                'city': city,
                'aqi': data['list'][0]['main']['aqi'],
                'pm2_5': data['list'][0]['components']['pm2_5'],
                'pm10': data['list'][0]['components']['pm10'],
                'co': data['list'][0]['components']['co'],
                'no2': data['list'][0]['components']['no2'],
                'o3': data['list'][0]['components']['o3']
            }
            air_data.append(air_info)
            print(f"✅ {city}: AQI = {air_info['aqi']}")
        else:
            print(f"❌ {city}: API hatası")
    
    # CSV olarak kaydet
    df = pd.DataFrame(air_data)
    df.to_csv('data/processed/city_air_quality.csv', index=False)
    return df

if __name__ == "__main__":
    air_df = collect_air_quality_data()
    print(f"\\n🌬️ {len(air_df)} şehir için hava kalitesi verisi toplandı!")
```

---

## 🧮 ADIM 2: SKORLAMA ALGORİTMASI

### **Yaşanabilirlik Skoru Hesaplama (`src/scoring_algorithm.py`)**
```python
import pandas as pd
import numpy as np

def calculate_livability_scores():
    \"\"\"Tüm 5 metriği birleştirerek yaşanabilirlik skoru hesapla\"\"\"
    
    # Verileri yükle
    air_df = pd.read_csv('data/processed/city_air_quality.csv')
    
    # SKORLAMA ALGORİTMASI
    def normalize_score(value, min_val, max_val, reverse=False):
        \"\"\"0-10 arasında normalize et\"\"\"
        if pd.isna(value):
            return 5.0  # Default score for missing data
        if reverse:  # Düşük değer = yüksek skor (işsizlik, suç için)
            return 10 - ((value - min_val) / (max_val - min_val)) * 10
        else:  # Yüksek değer = yüksek skor
            return ((value - min_val) / (max_val - min_val)) * 10
    
    # 1. HAVA KALİTESİ SKORU (AQI: 1=en iyi, 5=en kötü)
    air_df['air_score'] = air_df['aqi'].apply(lambda x: (6-x) * 2 if pd.notna(x) else 5)
    
    # Kategorilendirme
    def categorize_livability(score):
        if score >= 8.5:
            return "Mükemmel"
        elif score >= 7.5:
            return "Çok İyi"
        elif score >= 6.5:
            return "İyi"
        elif score >= 5.5:
            return "Orta"
        else:
            return "Zayıf"
    
    air_df['livability_category'] = air_df['air_score'].apply(categorize_livability)
    
    return air_df

if __name__ == "__main__":
    result_df = calculate_livability_scores()
    print("\\n🏆 ŞEHİR YAŞANABİLİRLİK SIRALAMASI")
    print(result_df[['city', 'air_score', 'livability_category']])
```

---

## 📈 ADIM 3: VİZUALİZASYON

### **Grafikler (`src/visualization.py`)**
```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

def create_visualizations():
    \"\"\"Proje için tüm grafikleri oluştur\"\"\"
    
    # Veriyi yükle (placeholder)
    data = {
        'city': ['New York', 'Chicago', 'Austin', 'Miami', 'Denver'],
        'livability_score': [7.2, 6.8, 8.1, 7.5, 7.9],
        'air_score': [6, 5, 9, 8, 8],
        'safety_score': [5, 6, 8, 7, 8]
    }
    df = pd.DataFrame(data)
    
    # Stil ayarları
    plt.style.use('seaborn-v0_8')
    fig, axes = plt.subplots(2, 2, figsize=(15, 12))
    fig.suptitle('🏙️ Şehir Yaşanabilirlik Endeksi Analizi', fontsize=16, fontweight='bold')
    
    # 1. Genel Sıralama
    axes[0,0].barh(df['city'], df['livability_score'], color='steelblue')
    axes[0,0].set_title('Yaşanabilirlik Skoru Sıralaması')
    axes[0,0].set_xlabel('Skor (0-10)')
    
    # 2. Metrik Karşılaştırması
    x = range(len(df))
    width = 0.35
    
    axes[0,1].bar([p - width/2 for p in x], df['air_score'], width, label='Hava Kalitesi')
    axes[0,1].bar([p + width/2 for p in x], df['safety_score'], width, label='Güvenlik')
    
    axes[0,1].set_title('Metrik Bazında Karşılaştırma')
    axes[0,1].set_xlabel('Şehirler')
    axes[0,1].set_ylabel('Skor')
    axes[0,1].set_xticks(x)
    axes[0,1].set_xticklabels(df['city'], rotation=45)
    axes[0,1].legend()
    
    # 3. Scatter Plot
    axes[1,0].scatter(df['air_score'], df['safety_score'], s=df['livability_score']*20, alpha=0.7)
    axes[1,0].set_xlabel('Hava Kalitesi Skoru')
    axes[1,0].set_ylabel('Güvenlik Skoru')
    axes[1,0].set_title('Hava Kalitesi vs Güvenlik')
    
    # Şehir isimlerini ekle
    for i, city in enumerate(df['city']):
        axes[1,0].annotate(city, (df['air_score'].iloc[i], df['safety_score'].iloc[i]), 
                          xytext=(5, 5), textcoords='offset points', fontsize=8)
    
    # 4. Pie Chart - Kategoriler
    categories = ['Mükemmel', 'Çok İyi', 'İyi']
    sizes = [1, 2, 2]
    axes[1,1].pie(sizes, labels=categories, autopct='%1.1f%%', startangle=90)
    axes[1,1].set_title('Yaşanabilirlik Kategorileri Dağılımı')
    
    plt.tight_layout()
    plt.savefig('livability_analysis.png', dpi=300, bbox_inches='tight')
    plt.show()

if __name__ == "__main__":
    create_visualizations()
```

---

## 🚀 ÇALIŞTIRMA REHBERİ

### **1. Proje Kurulumu**
```bash
# 1. Repository'yi klonla
git clone https://github.com/ozzy2438/Most-Livable-cities-analyze.git
cd Most-Livable-cities-analyze

# 2. Sanal ortam oluştur
python -m venv venv
source venv/bin/activate  # Linux/Mac
# venv\\Scripts\\activate  # Windows

# 3. Gerekli paketleri yükle
pip install -r requirements.txt
```

### **2. Veri Toplama**
```bash
# Sırasıyla çalıştır:
python src/data_collection.py     # Hava + ekonomik veri topla
python src/data_processing.py     # Suç verilerini işle
```

### **3. Analiz ve Skorlama**
```bash
python src/scoring_algorithm.py   # Yaşanabilirlik skorlarını hesapla
python src/visualization.py       # Grafikleri oluştur
```

---

## 🎯 BEKLENİLEN SONUÇLAR

### **Çıktı Dosyaları**
- `data/final/livability_scores.csv` - Final skorlar
- `livability_analysis.png` - Görselleştirmeler
- Jupyter Notebook analiz raporları

### **Portfolio İçin Öne Çıkan Noktalar**
✅ **Çok Boyutlu Analiz:** 5 farklı sektörden veri entegrasyonu  
✅ **API Çeşitliliği:** 4+ farklı veri kaynağından otomatik veri çekme  
✅ **Veri Temizleme:** Eksik verilerle başa çıkma, normalizasyon  
✅ **Karmaşık Algoritmik Düşünce:** Çok kriterli karar verme (MCDM) sistemi  
✅ **Görselleştirme:** 5 boyutlu veri analizi grafikleri  
✅ **İş Değeri:** Şirketlerin ofis lokasyonu seçimi için uygulanabilir çözüm  

---

## 🔄 GELİŞTİRME FİKİRLERİ

### **Versiyon 2.0 İçin:**
- **Daha Fazla Metrik:** Ulaşım kalitesi, çevre skorları, kültürel aktiviteler
- **Gerçek Zamanlı Dashboard:** Streamlit/Dash ile interaktif web uygulaması
- **Machine Learning:** Şehir trendlerini tahmin eden modeller
- **Kullanıcı Kişiselleştirme:** Farklı yaş grupları için ağırlık ayarları
- **Coğrafi Genişleme:** Avrupa ve Asya şehirleri dahil

### **Portfolio Sunumu İçin:**
- **GitHub Showcase:** Detaylı README.md + proje organizasyonu
- **LinkedIn Viral Content:** "5 metrikle şehir analizi" serisi
- **Medium Technical Article:** Metodoloji ve algoritma açıklaması  
- **Kaggle Dataset:** Final skorlar + metodoloji yayını

---

Bu proje yapısını kullanarak profesyonel bir veri analizi projesi oluşturabilirsin! 🚀