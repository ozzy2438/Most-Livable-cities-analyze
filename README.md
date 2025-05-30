# 🏙️ Most Livable Cities Analysis

A comprehensive data analytics project analyzing city livability across multiple dimensions using various APIs and data sources.

## 📊 Project Overview

This project evaluates and ranks cities based on five key livability metrics:

- 🌬️ **Air Quality** - Using OpenWeatherMap API
- 💼 **Economic Indicators** - Using FRED API 
- 🚔 **Safety** - Crime statistics analysis
- 🎓 **Education Quality** - School data from Urban Institute API
- 🏥 **Health Indicators** - Healthcare and life expectancy metrics

## 🎯 Target Cities

- **US Cities**: New York, Chicago, Austin, Miami, Denver
- **International**: Istanbul (Turkey) for comparison

## 🛠️ Technical Stack

- **Python** - Core programming language
- **Pandas & NumPy** - Data manipulation and analysis
- **Requests** - API data collection
- **Matplotlib & Seaborn** - Data visualization
- **Jupyter Notebooks** - Interactive analysis

## 📁 Project Structure

```
Most-Livable-cities-analyze/
│
├── city_livability_project.md    # Complete project documentation
├── .cursor/rules/               # Development guidelines
│   ├── api-usage.mdc
│   ├── data-sources.mdc
│   ├── future-development.mdc
│   ├── project-overview.mdc
│   ├── project-structure.mdc
│   ├── scoring-algorithm.mdc
│   └── visualization.mdc
│
└── README.md                    # This file
```

## 🚀 Getting Started

### Prerequisites

```bash
pip install pandas numpy requests matplotlib seaborn plotly jupyter
pip install fredapi  # For economic data
```

### API Keys Required

- OpenWeatherMap API (for air quality data)
- FRED API (for economic indicators)
- Urban Institute Education Data API (free)

### Quick Start

1. **Clone the repository**
```bash
git clone https://github.com/ozzy2438/Most-Livable-cities-analyze.git
cd Most-Livable-cities-analyze
```

2. **Read the complete documentation**
```bash
# Open city_livability_project.md for full implementation details
```

3. **Follow the step-by-step guide**
   - Set up API keys
   - Run data collection scripts
   - Execute scoring algorithm
   - Generate visualizations

## 🧮 Scoring Algorithm

The livability score uses a weighted average approach:

| Metric | Weight | Description |
|--------|--------|-------------|
| Air Quality | 20% | AQI-based scoring |
| Economic | 25% | Unemployment rates |
| Safety | 20% | Crime statistics |
| Education | 20% | Graduation rates |
| Health | 15% | Life expectancy & healthcare |

**Final scores are categorized as:**
- 🥇 Mükemmel (Excellent): 8.5-10.0
- 🥈 Çok İyi (Very Good): 7.5-8.4  
- 🥉 İyi (Good): 6.5-7.4
- 🟨 Orta (Average): 5.5-6.4
- 🟥 Zayıf (Poor): 0.0-5.4

## 📈 Key Features

✅ **Multi-dimensional Analysis** - 5 different data sectors  
✅ **API Integration** - 4+ different data sources  
✅ **Data Normalization** - Handles missing data and scaling  
✅ **Advanced Algorithms** - Multi-criteria decision making (MCDM)  
✅ **Rich Visualizations** - Interactive plots and charts  
✅ **Business Value** - Applicable for location decision making  

## 📊 Expected Outputs

- **CSV Files**: Processed data and final scores
- **Visualizations**: Multi-dimensional analysis charts
- **Jupyter Notebooks**: Interactive analysis reports
- **Documentation**: Complete methodology and implementation

## 🔮 Future Enhancements

### Version 2.0 Plans
- **Real-time Dashboard** with Streamlit/Dash
- **Machine Learning** models for trend prediction
- **Geographic Expansion** to European and Asian cities
- **User Customization** for different demographic preferences
- **Mobile Application** development

### Portfolio Applications
- **GitHub Showcase** for technical skills demonstration
- **LinkedIn Content** creation and professional networking
- **Medium Articles** for methodology explanation
- **Conference Presentations** at data science meetups

## 📚 Documentation

For complete implementation details, code examples, and step-by-step instructions, see:

**[📖 city_livability_project.md](city_livability_project.md)**

This contains:
- Full Python code implementations
- API integration examples
- Data processing workflows
- Visualization scripts
- Setup and execution guide

## 🤝 Contributing

This is a portfolio project, but suggestions and improvements are welcome! Feel free to:

1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## 📄 License

This project is open source and available under the [MIT License](LICENSE).

## 👨‍💻 Author

**Osman Orka** - Data Analyst  
- GitHub: [@ozzy2438](https://github.com/ozzy2438)
- Project: [Most-Livable-cities-analyze](https://github.com/ozzy2438/Most-Livable-cities-analyze)

---

### 🏆 Project Highlights

This project demonstrates:
- **API Integration Skills** - Multiple data source handling
- **Data Engineering** - ETL processes and data pipeline creation  
- **Statistical Analysis** - Multi-criteria scoring algorithms
- **Data Visualization** - Professional chart creation
- **Documentation** - Comprehensive project documentation
- **Business Intelligence** - Actionable insights for decision making

Perfect for showcasing data analytics capabilities in a portfolio! 🚀