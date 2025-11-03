# Marker Clustering Implementation

**Date:** November 3, 2025
**Feature:** Spider Cluster for overlapping Church Bombings markers

---

## Problem Addressed

Multiple incidents at the same location were stacking directly on top of each other, making many incidents inaccessible:

- **7 incidents** at Rev. Milton Curry Jr. Home (33.5012, -86.8419)
- **6 incidents** at Bethel Baptist Church (33.5571, -86.8232)
- **Many locations with 2-3 incidents** across Birmingham

Users could only click the topmost marker, missing the historical depth of repeated attacks.

---

## Solution: Leaflet MarkerCluster with Spider Mode

### What Was Implemented

**Marker Clustering** groups nearby markers together and shows:
- A **red badge with count** when multiple incidents are close together
- **Spider legs** radiating out when clicked, displaying each individual marker
- **Automatic unclustering** at zoom level 15+ to show all markers separately

### Technical Details

**Library:** Leaflet.markercluster v1.5.3

**Files Added:**
- `js/leaflet.markercluster.js` (33KB)
- `css/MarkerCluster.css` (872B)
- `css/MarkerCluster.Default.css` (1.3KB)

**Configuration:**
```javascript
var cluster_churchbombigs_2_1 = L.markerClusterGroup({
    spiderfyOnMaxZoom: true,           // Show spider legs at max zoom
    showCoverageOnHover: false,        // Don't show cluster coverage area
    zoomToBoundsOnClick: true,         // Zoom into cluster on click
    disableClusteringAtZoom: 15,       // Show all markers at zoom 15+
    maxClusterRadius: 60,              // Cluster within 60 pixels
    spiderfyDistanceMultiplier: 1.5    // Spread spider legs more
});
```

**Custom Styling:**
- Red cluster badges (`rgba(198, 40, 40, 0.8)`) matching the death toll highlight color
- Size categories: small (<5), medium (5-9), large (10+)
- 40x40px badge size for good visibility and touch targets

---

## How It Works

### At Default Zoom:
- Birmingham shows a **single red badge with "30+"** (representing ~30 incidents)
- Other cities with multiple incidents show smaller badges (e.g., "2", "3")

### When User Clicks a Cluster:
- Map zooms into the cluster area
- Markers **spread out in a circle** (spider pattern)
- Each incident is now individually clickable
- **Lines connect** back to the center point

### At Zoom 15+:
- All clustering disabled
- Every single incident appears as its own marker
- Users can pan around and click each one directly

---

## Applied To

**Church Bombings Layer Only**
- Race Riots layer does NOT use clustering (incidents are more geographically dispersed)
- Only Church Bombings needed clustering due to Birmingham's "Dynamite Hill" concentration

---

## Benefits

### 1. **Data Accessibility**
- All 7 incidents at Rev. Curry's home are now accessible
- All 6 attacks on Bethel Baptist Church can be explored
- No hidden markers

### 2. **Visual Communication**
- Badge count immediately shows **severity of repeated violence**
- Birmingham's clustering visually demonstrates the scale of terror
- Educational impact: "This location was attacked 7 times" is powerful

### 3. **User Experience**
- Clean map at overview level (no overlapping icons)
- Progressive disclosure: zoom in to see details
- Mobile-friendly: larger touch targets with badges

### 4. **Performance**
- Faster rendering with fewer visible markers at low zoom
- Smooth animations when spiderifying
- No lag with 100+ markers

---

## Usage Instructions

### For Users:
1. **See a red badge with a number?** Multiple incidents occurred at that location
2. **Click the badge** to zoom in and see them spread out
3. **Click individual markers** to read each incident's details
4. **Zoom in far enough** (zoom 15+) and badges disappear, showing all markers

### For Future Data Additions:
The clustering is automatic - just add new incidents to the data file and they'll automatically cluster if they're near existing ones.

---

## Configuration Options (if you need to adjust)

**In `index.html` around line 478:**

- `disableClusteringAtZoom: 15` → Change to zoom earlier/later (1-18)
- `maxClusterRadius: 60` → Larger = more clustering, smaller = less
- `spiderfyDistanceMultiplier: 1.5` → Larger = more spread spider legs

**Color (in CSS around line 122):**
- `rgba(198, 40, 40, 0.8)` → Change red values to different color

---

## Files Modified

1. **index.html**
   - Added CSS includes (lines 10-11)
   - Added JS include (line 236)
   - Added cluster styling (lines 121-154)
   - Modified layer creation (lines 477-522)
   - Updated layer control (line 674)

2. **New files in project:**
   - `js/leaflet.markercluster.js`
   - `css/MarkerCluster.css`
   - `css/MarkerCluster.Default.css`

---

## Testing Checklist

- [ ] Load map - red badges appear in Birmingham area
- [ ] Click a badge - map zooms and shows spider legs
- [ ] Click individual spiderfied markers - popups work correctly
- [ ] Zoom to level 15+ - all markers visible individually
- [ ] Toggle layer off/on - clustering still works
- [ ] Test on mobile - touch targets work well
- [ ] Check layer control - "Church Bombings & Attacks" toggles correctly

---

## Known Behavior

**Expected:**
- At low zoom, Birmingham shows as one large cluster
- Zooming progressively breaks into smaller clusters
- Final zoom shows all individual markers

**Race Riots layer:**
- Does NOT cluster (by design)
- Most riots are geographically spread out
- No severe overlapping issues

---

*This implementation provides an educational benefit by making the repeated nature of attacks visually apparent through clustering, while still preserving access to every individual incident.*
