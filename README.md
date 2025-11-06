# Racial Terror in America: A Historical Map

An interactive web map documenting historical incidents of racial terror in America, including church bombings and race riots. This educational resource aims to preserve the memory of these tragic events and facilitate learning about this critical period in American history.

🌐 **[View Live Map](https://yourusername.github.io/repository-name/)**

## Project Overview

This map visualizes two datasets:
- **Church Bombings** - Acts of terror targeting African American churches and religious buildings
- **Race Riots** - Violent racial conflicts throughout American history

### Features

- 📱 **Fully Responsive** - Works seamlessly on desktop, tablet, and mobile devices
- 🗺️ **Interactive Markers** - Click markers to view detailed historical information
- 📊 **Scaled by Impact** - Marker sizes reflect death tolls
- 📚 **Educational Content** - Each point includes historical narratives and source references
- 🎨 **Clean Design** - Modern, accessible interface for easy navigation

## Data Layers

### Church Bombings
- **Building Types**: Churches, Houses, and other structures
- **Attack Types**: Various forms of racist violence
- **Time Period**: Historical incidents across multiple decades
- **Information Included**: Location, year, death toll, historical narratives, and references

### Race Riots
- **Incident Types**: Various forms of racial violence and conflicts
- **Death Tolls**: Official recorded deaths (marker size indicates severity)
- **Coverage**: Events across the United States
- **Information Included**: Event name, location, year, type, death toll, historical narratives, and sources

## How to Use

1. **Zoom and Pan** - Use mouse or touch gestures to navigate the map
2. **Layer Control** - Click the layers icon (top-right) to toggle data layers
3. **View Details** - Click any marker to see detailed historical information
4. **Mobile** - Touch controls work seamlessly on all devices

## Technologies Used

- **Leaflet.js** - Interactive mapping library
- **QGIS2Web** - Initial map generation from QGIS
- **Google Maps** - Base map tiles
- **Custom CSS** - Responsive design and styling
- **GeoJSON** - Data format for geographic features



## Adding New Data Points

To add new historical events to the map:

1. **Edit Data Files** in `data/` directory:
   - `churchbombigs_2_1.js` - Church bombing incidents
   - `RaceRiotsSheet1_2.js` - Race riot incidents

2. **GeoJSON Format** - Each feature should include:
   ```javascript
   {
     "type": "Feature",
     "geometry": {
       "type": "Point",
       "coordinates": [longitude, latitude]
     },
     "properties": {
       "Name": "Event Name",
       "Year": 1963,
       "City": "City Name",
       "State": "State",
       "Narrive": "Historical narrative...",
       "Death Toll (Official)": 4,
       "Type": "Event Type",
       "Reference": "Source URL or citation"
     }
   }
   ```

3. **Update and Deploy** - Commit changes and push to GitHub

## Educational Use

This map is designed for:
- **Classroom Education** - Teaching American history and civil rights
- **Research** - Academic study of racial violence
- **Public Awareness** - Community education and remembrance
- **Historical Preservation** - Documenting and preserving historical events

## Data Sources

All data points include source references. Users are encouraged to:
- Verify information with primary sources
- Contribute additional verified data
- Report inaccuracies or missing information

## Contributing

Contributions are welcome! To contribute:

1. Fork this repository
2. Add new verified data points or corrections
3. Ensure all additions include proper source citations
4. Submit a pull request with a detailed description

### Contribution Guidelines

- **Accuracy** - All data must be historically verified
- **Sources** - Include credible references for all information
- **Respect** - Handle this sensitive historical content with appropriate gravity
- **Quality** - Maintain consistent data formatting

## Project Structure

```
├── index.html              # Main map file
├── README.md              # This file
├── css/                   # Stylesheets
│   ├── leaflet.css
│   ├── qgis2web.css
│   └── ...
├── js/                    # JavaScript libraries
│   ├── leaflet.js
│   ├── L.Control.Layers.Tree.min.js
│   └── ...
├── data/                  # GeoJSON data files
│   ├── churchbombigs_2_1.js
│   └── RaceRiotsSheet1_2.js
├── markers/               # Map marker icons
├── legend/                # Legend images
└── images/                # Documentation images
```

## Technical Details

### Responsive Breakpoints

- **Desktop**: > 1024px - Full layout with all features
- **Tablet**: 768px - 1024px - Adjusted controls
- **Mobile**: < 768px - Optimized touch interface with larger controls
- **Small Mobile**: < 480px - Compact layout

### Browser Support

- ✅ Chrome/Edge (latest)
- ✅ Firefox (latest)
- ✅ Safari (latest)
- ✅ Mobile Safari (iOS)
- ✅ Chrome Mobile (Android)

## Changelog

### Version 1.0 (November 2025)
- Initial release
- Fully responsive design implemented
- Custom popup designs with improved readability
- Loading overlay for better UX
- Mobile-optimized controls
- Educational context added to interface

## License

This project is intended for educational and historical preservation purposes. Please use responsibly and cite appropriately.

## Acknowledgments

- Historical data compiled from various scholarly sources
- Map technology powered by Leaflet.js and QGIS
- Base maps provided by Google Maps
