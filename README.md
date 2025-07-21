# Sentinel-1 SAR Change Detection

A comprehensive Python package for downloading, preprocessing, and performing change detection on Sentinel-1 SAR images. This package provides a complete workflow from data acquisition through the Copernicus Data Space Ecosystem to advanced change detection analysis with professional visualization capabilities.

## Features

### 🛰️ **Data Acquisition**
- Automated download from Copernicus Data Space Ecosystem (CDSE)
- Support for both GRD and SLC products
- Flexible search parameters (date range, AOI, orbit direction, etc.)
- Batch processing capabilities for multiple time periods

### 🔧 **SAR Preprocessing**
- **Radiometric Calibration**: Convert DN values to backscatter coefficients (σ⁰, β⁰, γ⁰)
- **Speckle Filtering**: Multiple algorithms available
  - Lee filter
  - Frost filter
  - Kuan filter
  - Gamma MAP filter
  - Adaptive filter (automatically selects best method)
- **Multilook Processing**: Reduce speckle through spatial averaging
- **Geometric Correction**: Reproject to target coordinate systems
- **Terrain Correction**: DEM-based geometric correction

### 📊 **Change Detection Methods**
- **Log-Ratio Method**: Optimal for SAR data (recommended)
- **Ratio Method**: Simple intensity ratio
- **Difference Method**: Absolute difference
- **PCA Method**: Principal component analysis (planned)

### 🎯 **Threshold Selection**
- **Otsu's Method**: Automatic threshold selection
- **Statistical Methods**: Mean, percentile-based thresholds
- **Manual Threshold**: User-defined values
- **Post-processing**: Morphological operations, minimum area filtering

### 📈 **Visualization & Reporting**
- Interactive plots for SAR images and change maps
- Before/after comparison visualizations
- Statistical analysis and reporting
- HTML report generation
- Export capabilities (GeoTIFF, PNG, PDF)

## Installation

### Prerequisites

- Python 3.8 or higher
- GDAL/OGR libraries
- Valid Copernicus Data Space Ecosystem account

### Install from Source

```bash
# Clone the repository
git clone https://github.com/manus-ai/sentinel1-change-detection.git
cd sentinel1-change-detection

# Install dependencies
pip install -r requirements.txt

# Install the package
pip install -e .
```

### Dependencies

The package requires the following main dependencies:

- **Geospatial**: `rasterio`, `geopandas`, `shapely`, `pyproj`, `fiona`
- **SAR Processing**: `sentinelsat`, `scikit-image`, `opencv-python`
- **Scientific Computing**: `numpy`, `scipy`, `pandas`
- **Visualization**: `matplotlib`, `seaborn`
- **Configuration**: `pyyaml`, `loguru`

## Quick Start

### 1. Setup Configuration

Create a configuration file or use the default template:

```python
from src.config_manager import ConfigManager

# Create template configuration
config_manager = ConfigManager()
config_manager.create_template_config('my_config.yaml')
```

Update the configuration with your Copernicus Data Space Ecosystem credentials:

```yaml
credentials:
  username: "your_cdse_username"
  password: "your_cdse_password"
```

### 2. Basic Usage

```python
from src.sentinel1_change_detection import Sentinel1ChangeDetection
from src.data_utils import DataUtils

# Define area of interest
bounds = (11.0, 46.0, 11.5, 46.5)  # (min_lon, min_lat, max_lon, max_lat)
aoi_geojson = DataUtils.create_aoi_from_bounds(bounds)

# Define date ranges
date_range_before = ('2023-01-01', '2023-03-31')
date_range_after = ('2023-07-01', '2023-09-30')

# Initialize processor
processor = Sentinel1ChangeDetection('my_config.yaml')

# Process workflow
results = processor.process_workflow(
    aoi_geojson=aoi_geojson,
    date_range=date_range_before,
    max_products=2
)

print(f"Processing successful: {results['success']}")
print(f"Change maps created: {len(results['change_maps'])}")
```

### 3. Advanced Usage

```python
from src.preprocessing import SARPreprocessor
from src.visualization import VisualizationTools

# Custom preprocessing
preprocessor = SARPreprocessor()

# Apply different speckle filters
lee_filtered = preprocessor.lee_filter(sar_image, window_size=5)
adaptive_filtered = preprocessor.adaptive_filter(sar_image, window_size=7)

# Create visualizations
viz = VisualizationTools()
fig = viz.plot_change_detection_results('change_map.tif')
```

## Configuration

The package uses YAML configuration files for flexible parameter management. Key configuration sections include:

### Search Parameters
```yaml
search_parameters:
  product_type: "GRD"          # GRD or SLC
  sensor_mode: "IW"            # IW, EW, SM
  polarization: "VV VH"        # Available polarizations
  orbit_direction: "DESCENDING" # ASCENDING or DESCENDING
  max_products: 10             # Maximum products to download
```

### Processing Parameters
```yaml
processing_parameters:
  radiometric_calibration:
    enabled: true
    calibration_type: "sigma0"  # sigma0, beta0, gamma0
  
  speckle_filtering:
    enabled: true
    filter_type: "lee"          # lee, frost, kuan, gamma_map, adaptive
    window_size: 5              # Filter window size
```

### Change Detection
```yaml
change_detection:
  method: "log_ratio"           # log_ratio, ratio, difference
  threshold_method: "otsu"      # otsu, mean, percentile, manual
  post_processing:
    min_area: 100              # Minimum change area in pixels
```

## Examples

The package includes comprehensive examples:

### Basic Example
```bash
python examples/basic_example.py
```
Demonstrates the complete workflow from data download to change detection.

### Advanced Example
```bash
python examples/advanced_example.py
```
Shows advanced features including:
- Custom preprocessing workflows
- Multiple change detection methods
- Comprehensive visualizations
- Batch processing scenarios

## API Reference

### Main Classes

#### `Sentinel1ChangeDetection`
Main processing class for the complete workflow.

**Key Methods:**
- `connect_to_api()`: Connect to Copernicus Open Access Hub
- `search_products()`: Search for Sentinel-1 products
- `download_products()`: Download selected products
- `process_workflow()`: Execute complete processing workflow

#### `SARPreprocessor`
Specialized SAR preprocessing functions.

**Key Methods:**
- `radiometric_calibration()`: Convert DN to backscatter
- `lee_filter()`, `frost_filter()`, `kuan_filter()`: Speckle filtering
- `multilook_processing()`: Spatial averaging
- `geometric_correction()`: Coordinate transformation

#### `DataUtils`
Utility functions for data handling.

**Key Methods:**
- `create_aoi_from_bounds()`: Create AOI from bounding box
- `create_aoi_from_point()`: Create AOI from center point and buffer
- `validate_geojson()`: Validate GeoJSON format
- `reproject_raster()`: Reproject raster data

#### `VisualizationTools`
Comprehensive visualization capabilities.

**Key Methods:**
- `plot_sar_image()`: Display SAR images
- `plot_change_detection_results()`: Visualize change detection
- `plot_before_after_comparison()`: Before/after comparison
- `create_processing_report()`: Generate HTML reports

#### `ConfigManager`
Configuration management and validation.

**Key Methods:**
- `load_config()`: Load configuration from file
- `save_config()`: Save configuration to file
- `validate_config()`: Validate configuration parameters
- `get_parameter()`, `set_parameter()`: Access configuration values

## Output Files

The package generates various output files:

### Processed Images
- **Calibrated images**: Radiometrically calibrated SAR data
- **Filtered images**: Speckle-filtered SAR data
- **Change maps**: Continuous and binary change detection results

### Visualizations
- **SAR image plots**: Individual SAR image visualizations
- **Change detection plots**: Change map visualizations
- **Comparison plots**: Before/after comparisons
- **Statistical plots**: Processing statistics

### Reports
- **HTML reports**: Comprehensive processing reports
- **Log files**: Detailed processing logs
- **Configuration files**: Processing parameters used

## Best Practices

### Data Selection
1. **Temporal Baseline**: Use appropriate time intervals (weeks to months)
2. **Seasonal Considerations**: Account for seasonal variations
3. **Orbit Consistency**: Use same orbit direction when possible
4. **Weather Conditions**: Consider weather impact on SAR backscatter

### Preprocessing
1. **Calibration**: Always apply radiometric calibration
2. **Speckle Filtering**: Use adaptive filter for unknown conditions
3. **Geometric Correction**: Ensure proper georeferencing
4. **Quality Control**: Check intermediate results

### Change Detection
1. **Method Selection**: Log-ratio method recommended for SAR
2. **Threshold Selection**: Otsu's method for automatic thresholding
3. **Post-processing**: Apply minimum area filtering
4. **Validation**: Visual inspection of results

## Troubleshooting

### Common Issues

#### Authentication Errors
```
Error: Failed to connect to API
```
**Solution**: Verify Copernicus credentials in configuration file.

#### No Products Found
```
Error: No products found
```
**Solutions**:
- Check date range validity
- Verify AOI coordinates
- Adjust search parameters (orbit direction, product type)

#### Memory Issues
```
Error: Out of memory
```
**Solutions**:
- Reduce processing area size
- Enable chunked processing
- Increase system memory

#### Import Errors
```
ModuleNotFoundError: No module named 'rasterio'
```
**Solution**: Install missing dependencies:
```bash
pip install -r requirements.txt
```

### Performance Optimization

1. **Parallel Processing**: Enable multi-threading in configuration
2. **Memory Management**: Use appropriate chunk sizes
3. **Disk Space**: Ensure sufficient storage for downloads
4. **Network**: Stable internet connection for data download

## Contributing

We welcome contributions! Please see our contributing guidelines:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Submit a pull request

### Development Setup

```bash
# Clone repository
git clone https://github.com/manus-ai/sentinel1-change-detection.git
cd sentinel1-change-detection

# Install development dependencies
pip install -e ".[dev]"

# Run tests
pytest tests/

# Format code
black src/ examples/
```

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Citation

If you use this package in your research, please cite:

```bibtex
@software{sentinel1_change_detection,
  title={Sentinel-1 SAR Change Detection Package},
  author={Manus AI},
  year={2025},
  url={https://github.com/manus-ai/sentinel1-change-detection}
}
```

## Support

- **Documentation**: [Read the Docs](https://sentinel1-change-detection.readthedocs.io/)
- **Issues**: [GitHub Issues](https://github.com/manus-ai/sentinel1-change-detection/issues)
- **Discussions**: [GitHub Discussions](https://github.com/manus-ai/sentinel1-change-detection/discussions)
- **Email**: support@manus.ai

## Acknowledgments

- **ESA Copernicus Programme**: For providing free access to Sentinel-1 data
- **SentinelSat**: For the excellent Python API to Copernicus Open Access Hub
- **GDAL/OGR**: For geospatial data processing capabilities
- **Scientific Python Community**: For the foundational libraries

---

**Developed by Manus AI** | **Version 1.0.0** | **2025**

