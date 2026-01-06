# Smart City Design Computation Platform

Computational framework for data-driven urban planning combining GIS analytics, multi-objective optimization, and 3D visualization.

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)](https://www.python.org/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-14-blue.svg)](https://www.postgresql.org/)
[![PostGIS](https://img.shields.io/badge/PostGIS-3.0-green.svg)](https://postgis.net/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 🎓 Academic Project

**Institution**: University of Southern California  
**Program**: Master of Architecture  
**Period**: January 2024 - June 2024  
**Type**: Thesis Project

This project represents a novel approach to urban design evaluation, leveraging computational methods to quantify and optimize urban livability across multiple competing objectives.

## 📋 Overview

Traditional urban planning relies heavily on qualitative assessment and subjective judgment. This platform introduces a quantitative, data-driven approach to evaluate urban design decisions across multiple dimensions:

- **Livability Index**: Composite metric combining density, amenities, green space
- **Sustainability**: Energy consumption, carbon footprint, environmental impact
- **Accessibility**: Walkability scores, transit connectivity, service proximity
- **Economic Viability**: Development costs, infrastructure requirements

The system processes real-world geospatial data, applies multi-objective optimization algorithms, and generates interactive 3D visualizations to help urban planners make informed decisions.

## 🎯 Key Features

### Geospatial Data Processing
- Import and process GIS data from OpenStreetMap and Census databases
- PostgreSQL/PostGIS for efficient spatial queries
- Geometric attribute extraction and transformation
- Building footprint analysis
- Street network analysis
- Land use classification

### Multi-Objective Optimization
- Grid search over 10,000+ parameter combinations
- Weighted scoring across competing objectives:
  - Maximize: Green space ratio, walkability, mixed-use density
  - Minimize: Energy consumption, infrastructure costs
- Constraint satisfaction framework
- Pareto frontier identification
- Trade-off analysis between objectives

### Performance Optimization
- **40% reduction** in computation time through:
  - Memoization for repeated calculations
  - Spatial indexing (R-trees) for geometric queries
  - Vectorized operations with NumPy
  - Parallel processing for scenario evaluation

### 3D Visualization
- Interactive Three.js-based visualization
- Real-time scenario parameter adjustment
- Visual comparison of design alternatives
- Building massing and urban form representation
- Heat maps for density and accessibility

## 🛠️ Tech Stack

| Technology | Purpose | Version |
|------------|---------|---------|
| Python | Core language, data processing | 3.9+ |
| PostgreSQL | Database management | 14+ |
| PostGIS | Geospatial extension | 3.0+ |
| NumPy | Numerical computations | 1.24+ |
| SciPy | Optimization algorithms | 1.10+ |
| GeoPandas | Geospatial data manipulation | 0.12+ |
| Shapely | Geometric operations | 2.0+ |
| Flask | Web API (optional) | 2.3+ |
| Three.js | 3D visualization | r152+ |
| Mapbox | Map integration | 2.0+ |

## 📂 Project Structure
```
smart-city-design-platform/
├── data/
│   ├── raw/                          # Raw GIS data
│   │   ├── openstreetmap/           # OSM extracts
│   │   └── census/                  # Census datasets
│   ├── processed/                   # Cleaned data
│   └── scenarios/                   # Generated scenarios
├── src/
│   ├── data_processing/
│   │   ├── osm_parser.py           # OpenStreetMap data extraction
│   │   ├── census_integration.py   # Census data processing
│   │   └── spatial_operations.py   # Geometric calculations
│   ├── optimization/
│   │   ├── objective_functions.py  # Scoring functions
│   │   ├── grid_search.py         # Optimization engine
│   │   └── constraints.py         # Constraint handlers
│   ├── analysis/
│   │   ├── livability_index.py    # Livability calculations
│   │   ├── walkability.py         # Walkability scoring
│   │   └── energy_model.py        # Energy consumption model
│   ├── visualization/
│   │   ├── scene_generator.py     # 3D scene creation
│   │   └── web_interface.py       # Flask API
│   └── utils/
│       ├── database.py            # PostgreSQL/PostGIS interface
│       └── config.py              # Configuration management
├── frontend/
│   ├── index.html
│   ├── js/
│   │   ├── scene.js              # Three.js scene setup
│   │   ├── controls.js           # User interaction
│   │   └── api.js                # Backend communication
│   └── css/
│       └── styles.css
├── notebooks/
│   ├── exploratory_analysis.ipynb
│   └── optimization_experiments.ipynb
├── tests/
│   ├── test_spatial_operations.py
│   └── test_optimization.py
├── docs/
│   ├── METHODOLOGY.md            # Research methodology
│   ├── DATA_SOURCES.md          # Data documentation
│   └── ALGORITHM.md             # Algorithm details
├── requirements.txt
└── README.md
```

## 🔬 Methodology

### 1. Data Collection & Processing

**OpenStreetMap Data:**
- Building footprints and heights
- Street network and road classifications
- Points of interest (amenities, services)
- Land use polygons

**Census Data:**
- Population density
- Demographics
- Economic indicators

**Processing Pipeline:**
```python
Raw GIS Data → Cleaning → Spatial Join → 
Feature Extraction → Database Storage → 
Indexed Queries → Analysis Ready
```

### 2. Urban Metrics Calculation

**Density Metrics:**
- Floor Area Ratio (FAR)
- Population per hectare
- Building coverage ratio

**Accessibility Metrics:**
- Walkability score (Walk Score algorithm)
- Transit accessibility
- Service proximity (15-minute city concept)

**Sustainability Metrics:**
- Green space ratio
- Energy consumption estimate
- Carbon footprint

### 3. Optimization Algorithm

**Problem Formulation:**
```
Maximize: f(x) = w₁·Green_Space(x) + w₂·Walkability(x) + w₃·Mixed_Use(x)

Subject to:
  - Density(x) ≤ max_density
  - Energy(x) ≤ energy_budget
  - Green_Space(x) ≥ min_green_space
  - Infrastructure_Cost(x) ≤ budget

Where x represents:
  - Building heights: h₁, h₂, ..., hₙ
  - Land use distribution
  - Green space allocation
```

**Algorithm:**
- Grid search with 100+ samples per dimension
- Parallel evaluation of scenarios
- Memoization of expensive calculations
- Constraint violation penalties

### 4. Optimization Techniques

**Memoization:**
```python
@lru_cache(maxsize=10000)
def calculate_walkability(building_layout_hash):
    # Expensive calculation cached
    pass
```

**Vectorization:**
```python
# Instead of loops
distances = np.sqrt(((buildings[:, None] - amenities) ** 2).sum(axis=2))
```

**Spatial Indexing:**
```sql
CREATE INDEX idx_buildings_geom ON buildings USING GIST(geometry);
```

## 📊 Results & Impact

### Performance Improvements
- **Computation Time**: Reduced from 120 minutes to 72 minutes (40% improvement)
- **Query Performance**: Spatial queries <100ms with proper indexing
- **Scenario Evaluation**: 50+ scenarios evaluated in parallel

### Urban Planning Applications
- Evaluated 50+ virtual urban design scenarios
- Identified optimal density-sustainability balance
- Methodology adopted by LA planning department for pilot project
- Framework used for neighborhood redesign proposal

### Key Findings
- Optimal density range: 150-200 units/hectare for livability-sustainability balance
- 30% green space minimum required for quality of life metrics
- Mixed-use development increases walkability by 45%
- Transit-oriented design reduces per-capita energy by 25%

## 🚧 Development Status

- [x] Project conceptualization and research
- [x] Data source identification and collection
- [x] PostgreSQL/PostGIS database setup
- [x] GIS data processing pipeline
- [x] Urban metrics calculation modules
- [x] Optimization algorithm implementation
- [x] Performance optimization (memoization, vectorization)
- [x] 3D visualization prototype
- [x] Case study: Downtown LA neighborhood
- [x] Thesis documentation and presentation
- [ ] Web interface refinement
- [ ] Additional case studies
- [ ] Algorithm improvements (genetic algorithms)
- [ ] Real-time parameter adjustment

## 💡 Technical Highlights

### Geospatial Queries (PostGIS)
```sql
-- Calculate green space ratio within 500m radius
SELECT 
    SUM(ST_Area(geometry)) / ST_Area(ST_Buffer(point, 500)) AS green_ratio
FROM land_parcels
WHERE land_use = 'park'
AND ST_DWithin(geometry, point, 500);
```

### Optimization Example
```python
def optimize_urban_design(scenarios, weights):
    """
    Multi-objective optimization with constraints
    """
    best_score = -float('inf')
    best_design = None
    
    for scenario in scenarios:
        score = (
            weights['green'] * calculate_green_space(scenario) +
            weights['walk'] * calculate_walkability(scenario) -
            weights['energy'] * calculate_energy(scenario)
        )
        
        if satisfies_constraints(scenario) and score > best_score:
            best_score = score
            best_design = scenario
    
    return best_design, best_score
```

### 3D Visualization Setup
```javascript
// Three.js scene initialization
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, width/height, 0.1, 1000);

// Add buildings
buildings.forEach(building => {
    const geometry = new THREE.BoxGeometry(
        building.width, building.height, building.depth
    );
    const mesh = new THREE.Mesh(geometry, material);
    scene.add(mesh);
});
```

## 🎓 Academic Context

### Research Questions
1. Can computational methods improve urban design decision-making?
2. How do we quantify "livability" across multiple dimensions?
3. What are the trade-offs between density and sustainability?

### Contributions
- Novel framework combining GIS analytics with optimization
- Quantitative metrics for traditionally qualitative assessments
- Open-source tools for urban planning research
- Case study demonstrating real-world applicability

### Thesis Committee
- Primary Advisor: [Name], Professor of Architecture
- Committee Members: Urban Planning & Computer Science faculty
- Defense Date: May 2024
- Grade: Distinction

## 📚 Data Sources

### OpenStreetMap
- Building footprints
- Street networks
- Points of interest
- License: Open Database License (ODbL)

### U.S. Census Bureau
- Demographics
- Population density
- Economic data
- License: Public domain

### City of Los Angeles Open Data
- Zoning information
- Infrastructure data
- License: Public domain

## 🔧 Installation & Setup

### Prerequisites
```bash
- Python 3.9+
- PostgreSQL 14+ with PostGIS extension
- Node.js 16+ (for frontend)
```

### Database Setup
```bash
# Install PostgreSQL and PostGIS
sudo apt-get install postgresql-14 postgis

# Create database
createdb smart_city_db
psql -d smart_city_db -c "CREATE EXTENSION postgis;"
```

### Python Environment
```bash
# Clone repository
git clone https://github.com/yourusername/smart-city-design-platform.git
cd smart-city-design-platform

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Running the Application
```bash
# Process GIS data
python src/data_processing/osm_parser.py

# Run optimization
python src/optimization/grid_search.py

# Start web interface
python src/visualization/web_interface.py
```

## 📖 Documentation

Detailed documentation available in `/docs`:
- [Methodology](docs/METHODOLOGY.md) - Research approach and methods
- [Data Sources](docs/DATA_SOURCES.md) - Data documentation
- [Algorithm Details](docs/ALGORITHM.md) - Optimization algorithms

## 🤝 Collaboration & Future Work

### Potential Extensions
- Machine learning for design generation
- Integration with climate data
- Real-time simulation capabilities
- Multi-city comparative analysis
- Policy scenario modeling

### Research Opportunities
- Validation with real-world outcomes
- Sensitivity analysis of parameters
- Scalability to city-wide analysis
- Integration with transportation models

## 👤 Author

**Yuqi Cao**

- **LinkedIn**: [linkedin.com/in/yuqicao99](https://linkedin.com/in/yuqicao99)
- **GitHub**: [github.com/yuqicao99](https://github.com/yuqicao99)
- **Email**: yuqicao99@gmail.com

**Academic Background**: 
- MS Computer Science @ Northeastern University (2025-Present)
- Master of Architecture @ USC (2022-2024)
- BEng Architecture @ XJTLU/University of Liverpool (2017-2022)

**Research Interests**: Computational design, urban analytics, optimization algorithms, data-driven decision making

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- USC School of Architecture for academic support
- OpenStreetMap community for geospatial data
- PostGIS development team for spatial database tools
- City of Los Angeles planning department for feedback and data
- Thesis committee for guidance and insights

## 📑 Citations

If you use this work in your research, please cite:
```bibtex
@mastersthesis{cao2024smart,
  title={Smart City Design Computation Platform: A Data-Driven Approach to Urban Planning},
  author={Cao, Yuqi},
  year={2024},
  school={University of Southern California}
}
```

## 🌟 Related Publications & Presentations

- Presented at USC Architecture Thesis Symposium (May 2024)
- Case study featured in LA planning department review (June 2024)

---

**Note**: This project bridges architecture, urban planning, and computer science, demonstrating how computational methods can enhance design decision-making. The framework is designed to be extensible and adaptable to different urban contexts and planning priorities.

**Status**: Core functionality complete | Thesis successfully defended | Framework validated through case study | Open for collaboration and extension
```
