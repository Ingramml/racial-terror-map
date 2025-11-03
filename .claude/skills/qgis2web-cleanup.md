# QGIS2Web Cleanup Skill

**Purpose**: Automated cleanup and modernization of QGIS2Web exports
**Category**: Geospatial / Web Development
**Difficulty**: Intermediate
**Time to Execute**: 10-15 minutes

---

## Skill Overview

This skill provides a systematic approach to cleaning up and enhancing QGIS2Web-generated HTML/JavaScript files, removing unnecessary features, modernizing code structure, and adding responsive design.

---

## When to Use This Skill

Use this skill when you:
- Have a fresh QGIS2Web export that needs enhancement
- Want to remove filter sliders and unnecessary controls
- Need to add responsive design to a generated map
- Want to optimize the code structure
- Are preparing a map for production deployment

**Don't use this skill when:**
- Map is already heavily customized
- You need the filter sliders
- Working with non-QGIS2Web Leaflet maps

---

## Prerequisites

**Required:**
- QGIS2Web-generated index.html file
- Basic understanding of HTML/CSS/JavaScript
- Text editor or IDE

**Optional:**
- Version control (git) for rollback capability
- Backup of original files

---

## Execution Steps

### Step 1: Create Backup
```bash
cp index.html index.html.backup
```

**Why**: Safety net for rollback if needed
**Time**: 5 seconds

---

### Step 2: Analyze Current Structure

**Identify sections to review:**
- [ ] Filter slider code (nouislider)
- [ ] Layer tree controls
- [ ] Popup functions
- [ ] Script loading order
- [ ] CSS styling
- [ ] Data file references

**Use grep to find sections:**
```bash
# Find filter slider code
grep -n "nouislider" index.html

# Find popup functions
grep -n "function pop_" index.html

# Find layer creation
grep -n "L.geoJson" index.html
```

**Time**: 2-3 minutes

---

### Step 3: Remove Unnecessary Features

#### 3.1 Remove Filter Sliders

**Identify:**
```javascript
// Look for code blocks like:
<div id="id_0_options">
<label class="slider">id_0</label>
<div id="slider_id_0"></div>
// ... nouislider initialization
```

**Remove:**
- All slider HTML (`<div id="..._options">...</div>`)
- All nouislider initialization code
- wNumb.js imports if not used elsewhere

**Typical line count**: 150-200 lines removed

**Verification:**
```bash
# Check if sliders removed
grep -c "nouislider" index.html  # Should return 0 or minimal
```

---

#### 3.2 Remove Layer Tree Controls (if using simple layer control)

**Identify:**
```html
<script src="js/L.Control.Layers.Tree.min.js"></script>
<link rel="stylesheet" href="css/L.Control.Layers.Tree.css">
```

**Remove if:**
- Using standard L.control.layers instead
- Files don't exist
- Not needed for your layer structure

**Replace with:**
```javascript
var layerControl = L.control.layers(baseLayers, overlayLayers, {
    position: 'topright',
    collapsed: window.innerWidth < 768  // Auto-collapse on mobile
}).addTo(map);
```

---

#### 3.3 Clean Up Comments

**Remove excessive comments:**
```javascript
// QGIS2Web often adds excessive comments
// Like this one
// And this one
// Remove while keeping important documentation
```

**Keep important comments:**
```javascript
// Layer creation for district boundaries
// Data processing before layer initialization
```

---

### Step 4: Create Separate app.js File

**Create:** `js/app.js`

**Move custom logic here:**
```javascript
// app.js - Custom application logic

// Global variables
window.registrationCounts = {};
window.residentCounts = {};

// Data processing functions
window.initializeCounts = function() {
    // Count features by district
    window.registrationCounts = window.countByDistrict(json_Lineayer_regloc_4);
    window.residentCounts = window.countByDistrict(json_Lineayer_Living_3);
};

window.countByDistrict = function(geojsonData) {
    var counts = {};
    if (geojsonData && geojsonData.features) {
        geojsonData.features.forEach(function(feature) {
            var district = feature.properties.district;
            if (district) {
                counts[district] = (counts[district] || 0) + 1;
            }
        });
    }
    return counts;
};

// Helper functions
window.getRegistrationCount = function(district) {
    return window.registrationCounts[district] || 0;
};

window.getResidentCount = function(district) {
    return window.residentCounts[district] || 0;
};

// Legend function
window.addLegend = function(map) {
    // Legend implementation
};

// Loading overlay function
window.hideLoadingOverlay = function() {
    // Hide loading overlay
};
```

**Add to index.html (after data files, before main script):**
```html
<script src="data/YourData.js"></script>
<script src="js/app.js"></script>  <!-- Add this -->
<script>
// Main map initialization
```

---

### Step 5: Fix Script Loading Order

**Correct order:**
```html
<!-- 1. Libraries -->
<script src="js/leaflet.js"></script>
<script src="js/other-libraries.js"></script>

<!-- 2. Data files -->
<script src="data/Boundaries.js"></script>
<script src="data/Points.js"></script>
<script src="data/Lines.js"></script>

<!-- 3. Custom logic -->
<script src="js/app.js"></script>

<!-- 4. Main initialization -->
<script>
// Initialize data FIRST
if (typeof window.initializeCounts === 'function') {
    window.initializeCounts();
}

// THEN create map and layers
var map = L.map('map', { ... });
// ... rest of initialization
</script>
```

**Common mistake:**
```html
<!-- BAD: app.js loaded after it's needed -->
<script>
var map = L.map('map');
// Uses functions from app.js here...
</script>
<script src="js/app.js"></script>  <!-- Too late! -->
```

---

### Step 6: Add Responsive Design CSS

**Add to `<style>` section:**

```css
/* ===== RESPONSIVE MAP DESIGN ===== */

/* Base map - full viewport */
#map {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
}

/* Responsive header/title */
.map-header {
    position: absolute;
    top: 10px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 1000;
    background: rgba(255, 255, 255, 0.95);
    padding: 15px 30px;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.15);
}

.map-title {
    font-size: clamp(1.2rem, 3vw, 1.8rem);
    font-weight: 600;
    color: #333;
    margin: 0;
    text-align: center;
}

.map-subtitle {
    font-size: clamp(0.85rem, 2vw, 1rem);
    color: #666;
    margin: 5px 0 0 0;
    text-align: center;
}

/* Popups */
.leaflet-popup-content-wrapper {
    border-radius: 8px;
    box-shadow: 0 3px 14px rgba(0,0,0,0.15);
}

.leaflet-popup-content {
    margin: 15px;
    max-width: 400px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

/* Controls */
.leaflet-control-layers {
    max-width: 350px;
}

/* Loading overlay */
#loading-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(255, 255, 255, 0.95);
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 2000;
    transition: opacity 0.3s;
}

/* Legend */
.map-legend {
    background: white;
    padding: 15px;
    border-radius: 8px;
    box-shadow: 0 2px 8px rgba(0,0,0,0.15);
}

.map-legend h4 {
    margin: 0 0 10px 0;
    font-size: 1rem;
    color: #333;
}

.legend-item {
    display: flex;
    align-items: center;
    margin: 8px 0;
    font-size: 0.9rem;
}

.marker-dot {
    width: 12px;
    height: 12px;
    border-radius: 50%;
    margin-right: 8px;
}

.marker-dot.blue { background: #1F458F; }
.marker-dot.green { background: #4CAF50; }

/* ===== TABLET BREAKPOINT ===== */
@media (max-width: 1024px) {
    .map-header {
        padding: 12px 20px;
    }

    .leaflet-control-layers {
        max-width: 300px;
    }
}

/* ===== MOBILE BREAKPOINT ===== */
@media (max-width: 768px) {
    .map-header {
        top: 5px;
        padding: 10px 15px;
        max-width: 90vw;
    }

    .map-title {
        font-size: 1.1rem;
    }

    .map-subtitle {
        font-size: 0.8rem;
    }

    /* Popups */
    .leaflet-popup-content {
        max-width: 280px;
        font-size: 0.9em;
        margin: 12px;
    }

    /* Layer control */
    .leaflet-control-layers {
        max-width: 90vw;
        font-size: 0.9em;
    }

    /* Legend */
    .map-legend {
        padding: 12px;
        font-size: 0.85em;
    }

    /* Larger touch targets */
    .leaflet-control-zoom a {
        width: 40px;
        height: 40px;
        line-height: 40px;
        font-size: 20px;
    }

    .leaflet-control-layers-toggle {
        width: 40px;
        height: 40px;
    }
}

/* ===== SMALL MOBILE ===== */
@media (max-width: 480px) {
    .map-header {
        position: relative;
        transform: none;
        left: 0;
        margin: 5px;
    }

    .leaflet-popup-content {
        max-width: 240px;
    }
}
```

---

### Step 7: Add Loading Overlay

**Add HTML before map div:**
```html
<div id="loading-overlay">
    <div style="text-align: center;">
        <div style="width: 50px; height: 50px; border: 4px solid #f3f3f3; border-top: 4px solid #0078A8; border-radius: 50%; animation: spin 1s linear infinite; margin: 0 auto 15px;"></div>
        <p style="color: #333; font-size: 1.1rem;">Loading Map...</p>
    </div>
</div>

<style>
@keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}
</style>
```

**Add to app.js:**
```javascript
window.hideLoadingOverlay = function() {
    var overlay = document.getElementById('loading-overlay');
    if (overlay) {
        setTimeout(function() {
            overlay.style.opacity = '0';
            setTimeout(function() {
                overlay.style.display = 'none';
            }, 300);
        }, 500);
    }
};
```

**Call when map ready:**
```javascript
map.whenReady(function() {
    if (typeof window.hideLoadingOverlay === 'function') {
        window.hideLoadingOverlay();
    }
});
```

---

### Step 8: Optimize Popup Functions

**Before (QGIS2Web default):**
```javascript
var popupContent = '<table>\
    <tr>\
        <th scope="row">district</th>\
        <td>' + (feature.properties['district'] !== null ? autolinker.link(feature.properties['district'].toLocaleString()) : '') + '</td>\
    </tr>\
    <!-- 15 more rows... -->\
</table>';
```

**After (Optimized):**
```javascript
var district = feature.properties.district || 'N/A';
var activityPoints = window.getRegistrationCount(district);
var residentialPoints = window.getResidentCount(district);

var movementInsight = '';
if (activityPoints > 0 && residentialPoints === 0) {
    movementInsight = 'Activity Destination - People come here from other districts';
} else if (residentialPoints > 0 && activityPoints === 0) {
    movementInsight = 'Residential Area - Residents travel to other districts';
}

var popupContent = '<div class="district-popup">\
    <h3 style="margin: 0 0 10px 0;">District ' + district + '</h3>\
    \
    <div class="stat-row">\
        <span class="stat-label">🟢 Survey Activity Points:</span>\
        <span class="stat-value">' + activityPoints + '</span>\
    </div>\
    <div style="font-size: 0.85em; color: #666; margin: -4px 0 8px 20px;">\
        Surveys completed in this district\
    </div>\
    \
    <div class="stat-row">\
        <span class="stat-label">🔵 Residential Points:</span>\
        <span class="stat-value">' + residentialPoints + '</span>\
    </div>\
    <div style="font-size: 0.85em; color: #666; margin: -4px 0 12px 20px;">\
        People who live in this district\
    </div>\
    \
    <div style="background: #f0f8ff; padding: 10px; border-radius: 4px; border-left: 4px solid #0078A8; margin: 10px 0;">\
        <div style="font-weight: 600; color: #0078A8;">Movement Pattern:</div>\
        <div style="font-size: 0.9em; color: #333;">' + movementInsight + '</div>\
    </div>\
    \
    <hr style="margin: 12px 0; border: none; border-top: 1px solid #ddd;">\
    \
    <div>\
        <p style="margin: 5px 0; font-weight: 600;">' + (feature.properties.rep_name || '') + '</p>\
        ' + (feature.properties.rep_url ? '<a href="' + feature.properties.rep_url + '" target="_blank">Contact Representative →</a>' : '') + '\
    </div>\
</div>';
```

---

### Step 9: Test and Validate

**Functional Testing:**
- [ ] Map loads without errors (check console)
- [ ] All layers display correctly
- [ ] Popups open and show correct data
- [ ] Layer control works
- [ ] Zoom controls work
- [ ] Base map switcher works (if present)

**Responsive Testing:**
- [ ] Desktop (> 1024px): Full layout
- [ ] Tablet (768-1024px): Adjusted layout
- [ ] Mobile (< 768px): Mobile-optimized
- [ ] Small mobile (< 480px): Compact view

**Performance Testing:**
```bash
# Check file sizes
ls -lh index.html
ls -lh js/app.js

# Lines of code removed
wc -l index.html.backup
wc -l index.html
# Calculate difference
```

**Browser Testing:**
- [ ] Chrome/Edge
- [ ] Firefox
- [ ] Safari
- [ ] Mobile Safari (iOS)
- [ ] Chrome Mobile (Android)

---

### Step 10: Document Changes

**Create CLEANUP_SUMMARY.md:**
```markdown
# QGIS2Web Cleanup Summary

## Changes Made

### Code Removed
- Filter sliders (nouislider): ~160 lines
- Layer tree controls: ~15 lines
- Excessive comments: ~30 lines
**Total removed: ~205 lines**

### Code Added
- app.js custom logic: ~100 lines
- Responsive CSS: ~280 lines
- Loading overlay: ~30 lines
**Total added: ~410 lines**

### Improvements
- ✅ Responsive design implemented
- ✅ Custom data processing
- ✅ Improved popup design
- ✅ Better script organization
- ✅ Mobile-optimized

### Performance
- Load time: < 2 seconds
- Mobile-friendly: Yes
- Accessibility: Improved

### Browser Compatibility
- Chrome: ✅
- Firefox: ✅
- Safari: ✅
- Mobile: ✅
```

---

## Expected Outcomes

### Before Cleanup
- ❌ 160+ lines of unused filter slider code
- ❌ Generic data table popups
- ❌ No responsive design
- ❌ Poor mobile experience
- ❌ Scattered custom logic in main file
- ❌ No loading indicators

### After Cleanup
- ✅ Streamlined codebase (~200 lines removed)
- ✅ Custom, narrative popups
- ✅ Fully responsive design
- ✅ Mobile-optimized experience
- ✅ Organized code structure (app.js)
- ✅ Loading overlay for better UX
- ✅ Faster load times
- ✅ Production-ready code

---

## Troubleshooting

### Issue: Map doesn't load after cleanup
**Solution:**
```bash
# Restore from backup
cp index.html.backup index.html

# Check console for errors
# Verify script loading order
# Ensure all file paths correct
```

### Issue: Popups show undefined
**Solution:**
```javascript
// Check initializeCounts() called BEFORE layer creation
// Verify window namespace used for global functions
// Add console.log() to debug data loading
```

### Issue: Responsive CSS not working
**Solution:**
```html
<!-- Ensure viewport meta tag present -->
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

---

## Skill Mastery Checklist

**Beginner:**
- [ ] Can remove filter sliders
- [ ] Can add basic responsive CSS
- [ ] Can create app.js file

**Intermediate:**
- [ ] Can reorganize script loading order
- [ ] Can redesign popups
- [ ] Can add loading overlays
- [ ] Can optimize for mobile

**Advanced:**
- [ ] Can extract complex logic patterns
- [ ] Can implement custom data processing
- [ ] Can create reusable components
- [ ] Can optimize for performance

---

## Time Investment

**Initial Learning:** 30-45 minutes
**Per Project Execution:** 10-15 minutes
**ROI:** Significant improvement in code quality and UX

---

## Related Skills
- Popup Design Skill
- Responsive Web Design
- JavaScript Data Processing
- Leaflet.js Customization

---

**Skill Status**: ✅ Production Ready
**Version**: 1.0
**Last Updated**: October 2025

---

**Tags**: #qgis2web #cleanup #optimization #responsive-design #web-mapping
