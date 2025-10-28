# Advanced Polygon Splitter for QGIS

![QGIS Plugin](https://img.shields.io/badge/QGIS-3.16+-green.svg)
![License](https://img.shields.io/badge/license-GPL--2.0-blue.svg)

An advanced QGIS plugin that combines automatic grid-based and manual interactive polygon splitting methods. Perfect for land parceling, environmental sampling, urban planning, and spatial analysis.

## Features

### Two Splitting Modes

#### **1. Automatic Method**
- **Grid-Based Division**: Creates square-ish polygons using sophisticated algorithms
- **Strip Division**: Divides polygons into parallel strips
- **Four Cut Directions**: Left-to-Right, Right-to-Left, Bottom-to-Top, Top-to-Bottom
- **Custom Split Angle**: Optional angle override for precise control

#### **2. Manual Method**
- **Edge Selection**: Click on polygon edge to set direction and starting point
- **Line Drawing**: Draw a two-point line to specify angle and starting side
- **Smart Snapping**: Auto-snaps to vertices from all visible layers
- **Visual Feedback**: Real-time rubber band preview

### 📊 Target Configuration

- **Equal Area Mode**: Split into parts of specified area (e.g., 1000 m²)
- **Equal Parts Mode**: Split into N equal parts (e.g., 5 pieces)
- **Remainder Handling**: Option to absorb remainder into parts or leave as offcut

### ✨ Additional Features

- Preserves all original attributes
- Adds metadata fields (ID, UUID, area, method)
- Automatic area labeling on output
- Geodetic-accurate measurements
- Multi-CRS support
- Handles complex and concave polygons

---

## Installation

### From QGIS Plugin Repository (Recommended)
1. Open QGIS
2. Go to `Plugins` → `Manage and Install Plugins`
3. Search for "Advanced Polygon Splitter"
4. Click `Install Plugin`

### Manual Installation
1. Download the plugin ZIP file
2. Go to `Plugins` → `Manage and Install Plugins` → `Install from ZIP`
3. Select the downloaded ZIP file
4. Click `Install Plugin`

---

## Usage Guide

### Quick Start

1. **Select a Polygon**
   - Load a vector layer with polygon features
   - Select exactly one polygon
   - Click the plugin icon or go to `Vector` → `Advanced Polygon Splitter`

2. **Choose Method**
   - **Automatic**: For grid-based or strip divisions
   - **Manual**: For interactive direction control

3. **Configure Parameters**
   - Set target area or number of parts
   - Choose cut direction
   - Adjust additional options

4. **Execute**
   - Click OK to split the polygon
   - Result layer appears with area labels

---

## Method Details

### Automatic Method

#### Strip Division
```
Direction: Strips
Optional: Custom Split Angle

Result:
┌────────────┐
│   Part 1   │  ← Strip 1
├────────────┤
│   Part 2   │  ← Strip 2
├────────────┤
│   Part 3   │  ← Strip 3
└────────────┘
```

**Best for:**
- Simple linear divisions
- Fastest processing
- Land strips or transects

#### Grid Division
```
Direction: Left to Right / Right to Left / etc.
Optional: Custom Split Angle

Result:
┌────┬────┬────┐
│ P1 │ P2 │ P3 │  ← Row 1
├────┼────┼────┤
│ P4 │ P5 │ P6 │  ← Row 2
└────┴────┴────┘
```

**Best for:**
- Square/rectangular cells
- Sampling grids
- Grid-based analysis

### Manual Method

#### Edge Selection
1. Click "Select Direction" button
2. Click on any edge of the selected polygon
3. Direction and starting point are automatically determined
4. Click OK to split

**Best for:**
- Aligning with existing features
- Natural boundary divisions
- Following terrain features

#### Line Drawing
1. Click "Select Direction" button
2. Click two points to draw direction line (snaps to visible layers)
3. Click on polygon to choose starting side
4. Click OK to split

**Best for:**
- Custom angle alignment
- Infrastructure alignment (roads, utilities)
- Precise directional control

---

## Parameters Reference

| Parameter | Description | Values |
|-----------|-------------|--------|
| **Splitting Method** | Automatic or Manual | Automatic / Manual |
| **Target Type** | Area or Parts | Equal Area / Equal Parts |
| **Target Value** | Size of each part | Numeric value |
| **Cut Direction** (Auto) | How to divide | Strips / L→R / R→L / B→T / T→B |
| **Custom Angle** (Auto) | Override angle | -180° to 180° (optional) |
| **Selection Method** (Manual) | How to set direction | Edge Click / Line Draw |
| **Absorb Remainder** | Merge small offcut | Checked / Unchecked |

---

## Output Features

Each output polygon includes:

### Original Attributes
All attributes from the source feature are preserved

### New Attributes
- `split_id`: Sequential ID (1, 2, 3, ...)
- `split_uuid`: Unique identifier (UUID v4)
- `split_area`: Calculated area in project units
- `split_method`: Method used (automatic/manual)

### Visual Features
- Area labels automatically displayed
- Color-coded by default QGIS styling
- Ready for further analysis

---

## Tips & Best Practices

### For Best Results

✅ **Use Projected CRS**: Always work with projected coordinate systems (not WGS84)
✅ **Simplify Complex Polygons**: Simplify very complex geometries if splitting fails
✅ **Choose Appropriate Target**: Set realistic target areas based on polygon size
✅ **Enable Snapping**: For manual mode, enable snapping to visible layers
✅ **Check Topology**: Ensure source polygon is valid (use "Fix Geometries" tool)

### Performance Considerations

- **Large Polygons**: Grid method may be slower than strips
- **Many Parts**: >100 parts may take longer to process
- **Complex Shapes**: Concave polygons may require multiple attempts

### Common Issues

**Problem**: "Invalid geometry selected"
- **Solution**: Use `Vector` → `Geometry Tools` → `Fix Geometries`

**Problem**: Direction selection not working
- **Solution**: Ensure layer is in edit mode disabled, ensure polygon is selected

**Problem**: Areas don't match exactly
- **Solution**: This is normal - remainder is distributed or left as offcut

---

## Technical Details

### Algorithms

- **Automatic Grid**: Modified Brent's method with binary search
- **Strip Division**: Binary search with linear cuts
- **Manual**: Geometry rotation with angle-based splitting

### Dependencies

- QGIS 3.16+
- PyQt5
- Standard QGIS Python libraries

### Coordinate Systems

- Supports all projected CRS
- Geodetic measurements using ellipsoid calculations
- Geographic CRS not recommended (use projected instead)

### Accuracy

- Area calculations accurate to project precision
- Binary search precision: 20 iterations
- Tolerance: 1% of target area

---

## Examples

### Example 1: Agricultural Field Division

**Scenario**: Divide 50,000 m² field into 1,000 m² plots

**Steps**:
1. Select field polygon
2. Choose **Automatic Method**
3. Select **Equal Area**: 1000 m²
4. Direction: **Strips**
5. Check **Absorb remainder**
6. Click OK

**Result**: ~50 plots of approximately 1,000 m² each

### Example 2: Sampling Grid

**Scenario**: Create 4x4 sampling grid

**Steps**:
1. Select study area polygon
2. Choose **Automatic Method**
3. Select **Equal Parts**: 16
4. Direction: **Left to Right (Grid)**
5. Uncheck **Absorb remainder**
6. Click OK

**Result**: 16 square-ish cells plus possible small offcut

### Example 3: Road-Aligned Parcels

**Scenario**: Divide land parcel parallel to existing road

**Steps**:
1. Select parcel polygon
2. Choose **Manual Method**
3. Selection Method: **Draw a two-point line**
4. Click **Select Direction**
5. Draw line along road (snaps to road layer vertices)
6. Click on polygon to choose starting side
7. Set target area
8. Click OK

**Result**: Parcels aligned with road direction

### Example 4: Natural Boundary Division

**Scenario**: Split property along visible boundary feature

**Steps**:
1. Select property polygon
2. Choose **Manual Method**
3. Selection Method: **Click an edge on the polygon**
4. Click **Select Direction**
5. Click on the edge that follows natural boundary
6. Set number of parts
7. Click OK

**Result**: Division perpendicular to selected edge

---

## Comparison with Original Plugins

### vs. Polygon Divider

| Feature | Polygon Divider | Advanced Splitter |
|---------|----------------|-------------------|
| Grid Division | ✅ Yes | ✅ Yes (Enhanced) |
| Strip Division | ❌ No | ✅ Yes |
| Manual Direction | ❌ No | ✅ Yes |
| Interactive Selection | ❌ No | ✅ Yes |
| Snapping Support | ❌ No | ✅ Yes |
| Background Processing | ✅ Yes | ⚠️ Synchronous |
| Code Quality | ⚠️ Deprecated functions | ✅ Modern QGIS 3 |
| Error Recovery | ✅ Advanced | ✅ Standard |

### vs. Equalyzer

| Feature | Equalyzer | Advanced Splitter |
|---------|-----------|-------------------|
| Strip Division | ✅ Yes | ✅ Yes (Same) |
| Grid Division | ❌ No | ✅ Yes |
| Edge Selection | ❌ No | ✅ Yes |
| Line Drawing | ✅ Yes | ✅ Yes (Same) |
| Snapping | ✅ Yes | ✅ Yes (Same) |
| Automatic Mode | ❌ No | ✅ Yes |
| Custom Angles | ❌ No | ✅ Yes |

**Advantage**: Combines best features of both plugins with enhancements!

---

## Troubleshooting

### Plugin Won't Load

**Check**:
- QGIS version is 3.16 or higher
- Python error messages in QGIS Python console
- Plugin files are in correct directory

**Solution**: Reinstall plugin, check QGIS logs

### Splitting Fails

**Common Causes**:
1. Invalid geometry → Use Fix Geometries tool
2. Geographic CRS → Reproject to projected CRS
3. Target area too small → Increase target area
4. Polygon too complex → Simplify geometry

### Direction Selection Not Working

**Check**:
- Polygon is selected
- Layer is visible
- Dialog is hidden (check taskbar)
- Map tool is active (check cursor)

**Solution**: Reopen dialog, ensure proper selection

### Performance Issues

**If slow**:
- Reduce number of parts
- Simplify polygon first
- Use strip mode instead of grid
- Close other applications

### Unexpected Results

**Check**:
- Target area vs polygon area ratio
- Direction setting matches intent
- Remainder handling option
- CRS units (meters vs feet)

---

## Development

### File Structure

```
advanced_polygon_splitter/
├── __init__.py                      # Plugin entry point
├── metadata.txt                     # Plugin metadata
├── polygon_splitter_integrated.py   # Main plugin class
├── splitter_dialog.py               # UI dialog
├── map_tools.py                     # Interactive map tools
├── splitter_engine.py               # Core splitting algorithms
├── icon.png                         # Plugin icon
└── README.md                        # Documentation
```

### Code Architecture

```
PolygonSplitterIntegrated (Main)
    ├── SplitterDialog (UI)
    │   ├── Method selection
    │   ├── Parameter inputs
    │   └── Validation
    │
    ├── Map Tools (Interactive)
    │   ├── EdgeSelectTool
    │   ├── LineDrawTool
    │   └── PointSelectTool
    │
    └── SplitterEngine (Core)
        ├── split_strips()
        ├── split_grid()
        ├── split_manual()
        └── Helper methods
```

### Contributing

Contributions welcome! Please:
1. Fork the repository
2. Create feature branch
3. Follow existing code style
4. Add tests for new features
5. Submit pull request

### Testing

To test the plugin:
1. Install in QGIS
2. Load test data (various polygon types)
3. Test each mode and option
4. Verify output geometries
5. Check attribute preservation

---

## FAQ

**Q: Can I split multiple polygons at once?**
A: Currently no, select one polygon at a time. Consider using QGIS Processing batch mode.

**Q: Why do areas not match exactly?**
A: Remainder is either absorbed into parts or left as offcut. This is by design.

**Q: Can I customize output field names?**
A: Not in UI, but you can modify code in `splitter_engine.py`

**Q: Does it work with multipart polygons?**
A: Yes, but they're decomposed first. Each part is split independently.

**Q: Can I undo the split?**
A: The operation creates a new layer, original remains unchanged.

**Q: What's the maximum number of parts?**
A: Limited to 1000 to prevent performance issues.

**Q: Does it preserve holes in polygons?**
A: Yes, holes (donuts) are preserved in the splitting process.

**Q: Can I use it with 3D geometries?**
A: Works with PolygonZ, but elevation is preserved, not used in calculations.

---

## License

This plugin is free software licensed under GPL v2.0 or later.

```
This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.
```

---

## Credits

### Integrated From

- **Polygon Divider** by Jonny Huck (Flying Turtle Ltd / Lune Geographic)
  - Original grid-based algorithm
  - Brent's method implementation

- **Equalyzer** by Abel Koszeghy
  - Strip division algorithm
  - Interactive map tools
  - Snapping functionality

### Enhanced By

This integrated version combines and enhances both plugins with:
- Unified UI for both methods
- Enhanced error handling
- Modern QGIS 3 API
- Additional features (custom angles, edge selection)
- Improved documentation

### Acknowledgments

- Original Polygon Divider funded by Zero Waste Scotland Ltd and Deutsche Forestservice GMBH
- Thanks to QGIS community for API documentation and support

---

## Support

### Getting Help

- **Issues**: Report bugs on GitHub issue tracker
- **Questions**: Use QGIS Stack Exchange with tag `polygon-splitter`
- **Email**: Contact plugin maintainers

### Reporting Bugs

Please include:
1. QGIS version
2. Plugin version
3. Steps to reproduce
4. Error messages
5. Sample data (if possible)

### Feature Requests

Submit feature requests on GitHub with:
- Clear description
- Use case explanation
- Expected behavior
- Priority level

---

## Changelog

### Version 2.0 (Current)
- ✨ Integrated Polygon Divider and Equalyzer
- ✨ Added automatic grid method
- ✨ Added manual edge selection
- ✨ Added custom angle support
- ✨ Enhanced UI with method switching
- ✨ Improved error handling
- 🐛 Fixed geometry cleaning issues
- 📝 Comprehensive documentation

### Version 1.0 (Equalyzer)
- Initial release with strip division
- Line drawing tool
- Snapping support

### Version 0.10 (Polygon Divider)
- QGIS 3.32 compatibility
- Grid-based splitting
- Four cut directions

---

## Roadmap

### Planned Features

- [ ] Batch processing support
- [ ] Custom output field configuration
- [ ] Processing algorithm integration
- [ ] Hexagonal grid option
- [ ] Weighted area distribution
- [ ] Save/load preset configurations
- [ ] Export direction as vector layer
- [ ] Undo/redo support
- [ ] Preview mode before execution

### Future Improvements

- Async processing for large polygons
- GPU acceleration for complex geometries
- Machine learning for optimal direction
- Integration with other QGIS tools

---

## Contact

- **Project**: Advanced Polygon Splitter
- **Repository**: GitHub (link)
- **Maintainer**: Integration Team
- **Email**: info@polygonsplitter.com

---

**Made with ❤️ for the QGIS community**

*Transform your polygon workflows from frustrating to frictionless!*
