# Data Sources Documentation

## Overview

This project integrates multiple geospatial and demographic datasets to enable comprehensive urban analysis.

## Primary Data Sources

### 1. OpenStreetMap (OSM)

**Type**: Geospatial data  
**License**: Open Database License (ODbL)  
**URL**: https://www.openstreetmap.org/

**Datasets Used**:
- Building footprints (polygon geometries)
- Building heights (estimated from levels)
- Street network (road classifications)
- Points of interest (amenities, services)
- Land use classifications

**Access Method**:
```python
# Using osmnx library
import osmnx as ox

# Download building footprints
buildings = ox.geometries_from_place(
    "Downtown Los Angeles, California",
    tags={'building': True}
)

# Download street network
streets = ox.graph_from_place(
    "Downtown Los Angeles, California",
    network_type='walk'
)
```

**Data Volume**: ~15,000 buildings, 2,500 km of streets

### 2. U.S. Census Bureau

**Type**: Demographic and economic data  
**License**: Public domain  
**URL**: https://www.census.gov/

**Datasets Used**:
- Population density (block level)
- Demographics (age, income, education)
- Housing characteristics
- Economic indicators

**Access Method**:
```python
# Using census library
from census import Census

c = Census(API_KEY)
data = c.acs5.state_county_tract(
    fields=['B01001_001E', 'B19013_001E'],  # Population, Median Income
    state_fips='06',
    county_fips='037',
    tract='*'
)
```

### 3. City of Los Angeles Open Data

**Type**: Municipal data  
**License**: Public domain  
**URL**: https://data.lacity.org/

**Datasets Used**:
- Zoning designations
- Development permits
- Infrastructure locations
- Environmental data

## Data Processing Pipeline

### Step 1: Data Extraction
```python
def extract_osm_data(location, tags):
    """Extract OSM data for specified location"""
    gdf = ox.geometries_from_place(location, tags)
    return gdf

def extract_census_data(state, county):
    """Extract Census data"""
    c = Census(API_KEY)
    return c.acs5.state_county(fields, state, county)
```

### Step 2: Data Cleaning
```python
def clean_buildings(buildings_gdf):
    """Clean building data"""
    # Remove invalid geometries
    buildings_gdf = buildings_gdf[buildings_gdf.geometry.is_valid]
    
    # Estimate missing heights
    buildings_gdf['height'] = buildings_gdf.apply(
        lambda row: row.get('height', row.get('levels', 1) * 3.5),
        axis=1
    )
    
    return buildings_gdf
```

### Step 3: Spatial Integration
```python
def spatial_join_census_buildings(buildings, census_tracts):
    """Join census data to buildings"""
    # Spatial join
    joined = gpd.sjoin(
        buildings, 
        census_tracts, 
        how='left', 
        predicate='within'
    )
    return joined
```

### Step 4: Database Storage
```sql
-- Create tables
CREATE TABLE buildings (
    id SERIAL PRIMARY KEY,
    osm_id BIGINT,
    geometry GEOMETRY(Polygon, 4326),
    height FLOAT,
    land_use VARCHAR(50),
    census_tract VARCHAR(20)
);

CREATE INDEX idx_buildings_geom 
ON buildings USING GIST(geometry);

-- Import data
COPY buildings FROM 'buildings.csv' 
WITH (FORMAT CSV, HEADER true);
```

## Data Quality

### Completeness

| Dataset | Coverage | Completeness |
|---------|----------|--------------|
| OSM Buildings | 95% | High |
| OSM Streets | 98% | Very High |
| OSM POIs | 80% | Medium |
| Census Data | 100% | Complete |

### Accuracy

- **Spatial Accuracy**: ±5 meters (OSM GPS accuracy)
- **Height Estimates**: ±1.5 meters (estimated from levels)
- **Census Data**: Official government statistics

### Temporal Coverage

- **OSM Data**: Latest available (updated continuously)
- **Census Data**: 2020 Census, 2019 ACS 5-year estimates
- **City Data**: 2023 updates

## Data Transformations

### Coordinate Systems
```python
# Convert to appropriate projection
buildings = buildings.to_crs(epsg=3857)  # Web Mercator
census = census.to_crs(epsg=3857)

# For area calculations
buildings_utm = buildings.to_crs(epsg=32611)  # UTM Zone 11N
```

### Geometric Operations
```python
# Buffer for proximity analysis
buildings['buffer_500m'] = buildings.geometry.buffer(500)

# Calculate areas
buildings['area_m2'] = buildings.geometry.area

# Centroids for point analysis
buildings['centroid'] = buildings.geometry.centroid
```

## Derived Metrics

### Density Metrics
```python
def calculate_far(buildings, plot_area):
    """Calculate Floor Area Ratio"""
    total_floor_area = (buildings['area'] * buildings['floors']).sum()
    return total_floor_area / plot_area
```

### Accessibility Metrics
```python
def calculate_walkability(location, amenities, street_network):
    """Calculate walkability score"""
    # Count amenities within walking distance
    nearby = amenities[amenities.distance(location) < 800]  # 10min walk
    
    # Weight by importance
    score = sum(nearby['weight'])
    
    return min(100, score)  # Cap at 100
```

## Data Limitations

### OpenStreetMap
- Incomplete building heights in some areas
- Varying quality across regions
- Some POI data may be outdated

### Census Data
- Aggregated at tract level (privacy)
- 5-year estimates have lag
- Some economic data may be estimates

### Mitigations
- Height estimation using typical floor heights
- Validation against satellite imagery
- Cross-reference multiple data sources
- Document assumptions in analysis

## Data Updates

### Frequency
- OSM: Downloaded fresh for each analysis
- Census: Use latest available release
- City data: Check for updates quarterly

### Version Control
```python
DATA_VERSIONS = {
    'osm': '2024-01-06',
    'census': '2020-acs5',
    'city': '2023-Q4'
}
```

## Ethical Considerations

### Privacy
- Census data aggregated to protect individual privacy
- No personal information stored or analyzed
- Anonymized building data only

### Bias
- OSM data may have geographic bias (better coverage in urban areas)
- Census sampling may undercount certain populations
- Validation against multiple sources to reduce bias

## Citations
```bibtex
@misc{osm2024,
  author = {{OpenStreetMap Contributors}},
  title = {Planet dump},
  year = {2024},
  url = {https://www.openstreetmap.org}
}

@misc{census2020,
  author = {{U.S. Census Bureau}},
  title = {2020 Census},
  year = {2020},
  url = {https://www.census.gov}
}
```

---

**Last Updated**: January 2026  
**Maintained By**: Yuqi Cao
