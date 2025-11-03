# Popup Design Mockups

This file contains HTML/CSS mockups for the map popups that you can easily edit and customize.

---

## Church Bombings Popup

### Current Design in Code

Location in `index.html`: Lines ~333-397 (function `pop_churchbombigs_2_1`)

### Visual Mockup (IMPLEMENTED DESIGN ✅)

```
┌─────────────────────────────────────────┐
│ 16th Street Baptist Church              │ ← Header (dark gray, bold)
│ Birmingham, Alabama • 1963               │ ← Compact location/year (gray, small)
│                                          │
│ Bombing • Church                         │ ← Attack type • Building type
│                                          │
│ ┌─────────────────────────────────────┐ │
│ │ 💔 4 deaths                         │ │ ← Red highlighted box with emoji
│ └─────────────────────────────────────┘ │
│                                          │
│ ┌─────────────────────────────────────┐ │
│ │ On September 15, 1963, members of  │ │ ← Narrative (gray box,
│ │ the KKK planted dynamite at the    │ │   red left border)
│ │ church, killing four young girls.  │ │   No "Historical Account" label
│ └─────────────────────────────────────┘ │
│
└─────────────────────────────────────────┘
```

### Editable Template

```html
<div>
    <div class="popup-header">[CHURCH/BUILDING NAME]</div>

    <div class="popup-field">
        <div class="popup-label">Location</div>
        <div class="popup-value">[CITY], [STATE]</div>
    </div>

    <div class="popup-field">
        <div class="popup-label">Year</div>
        <div class="popup-value">[YEAR]</div>
    </div>

    <div class="popup-field">
        <div class="popup-label">Building Type</div>
        <div class="popup-value">[Church/House/School]</div>
    </div>

    <div class="popup-field">
        <div class="popup-label">Attack Type</div>
        <div class="popup-value">[Bombing/Arson/etc]</div>
    </div>

    <div class="popup-field">
        <div class="death-toll-highlight">Deaths: [NUMBER]</div>
    </div>

    <div class="popup-narrative">
        <div class="popup-label">Historical Account</div>
        <div class="popup-value">[NARRATIVE TEXT]</div>
    </div>

    <div class="popup-field">
        <div class="popup-label">Source</div>
        <div class="popup-value" style="font-size: 0.85em;">[REFERENCE LINK]</div>
    </div>
</div>
```

---

## Race Riots Popup

### Current Design in Code

Location in `index.html`: Lines ~464-524 (function `pop_RaceRiotsSheet1_2`)

### Visual Mockup (IMPLEMENTED DESIGN ✅)

```
┌─────────────────────────────────────────┐
│ Tulsa Race Massacre                      │ ← Header (dark gray, bold)
│ Tulsa, Oklahoma • 1921                   │ ← Compact location/year (gray, small)
│                                          │
│ Race Riot / Massacre                     │ ← Event type
│                                          │
│ ┌─────────────────────────────────────┐ │
│ │ 💔 Official death toll: 36          │ │ ← Red highlighted box with emoji
│ └─────────────────────────────────────┘ │
│ Actual casualties likely higher than     │ ← Context note (italic, gray)
│ official records                         │
│                                          │
│ ┌─────────────────────────────────────┐ │
│ │ Over two days, white mobs attacked  │ │ ← Narrative (gray box,
│ │ the prosperous Black district of    │ │   red left border)
│ │ Greenwood, destroying 35 blocks.    │ │   No label needed
│ └─────────────────────────────────────┘ ││

└─────────────────────────────────────────┘

Special case for zero deaths:
┌─────────────────────────────────────────┐
│ ⚠️ No official deaths recorded          │ ← Orange warning box
│ Note: Actual casualties may have been   │ ← Context (italic, gray)
│ undercounted                             │
└─────────────────────────────────────────┘
```

### Editable Template

```html
<div>
    <div class="popup-header">[EVENT NAME]</div>

    <div class="popup-field">
        <div class="popup-label">Location</div>
        <div class="popup-value">[CITY], [STATE]</div>
    </div>

    <div class="popup-field">
        <div class="popup-label">Year</div>
        <div class="popup-value">[YEAR]</div>
    </div>

    <div class="popup-field">
        <div class="popup-label">Type</div>
        <div class="popup-value">[Race Riot/Massacre/Violence]</div>
    </div>

    <div class="popup-field">
        <div class="death-toll-highlight">Official Death Toll: [NUMBER]</div>
    </div>

    <div class="popup-narrative">
        <div class="popup-label">Historical Account</div>
        <div class="popup-value">[NARRATIVE TEXT]</div>
    </div>

    <div class="popup-field">
        <div class="popup-label">Source</div>
        <div class="popup-value" style="font-size: 0.85em;">[REFERENCE LINK]</div>
    </div>
</div>
```

---

## CSS Classes Reference

### Available Classes for Customization

```css
.popup-header {
    /* Main title at top of popup */
    font-size: 1.2em;
    font-weight: 600;
    color: #2c2c2c;
    margin: 0 0 12px 0;
    padding-bottom: 8px;
    border-bottom: 2px solid #e0e0e0;
}

.popup-field {
    /* Container for each field */
    margin: 10px 0;
}

.popup-label {
    /* Field labels (Location, Year, etc) */
    font-weight: 600;
    color: #555;
    font-size: 0.9em;
}

.popup-value {
    /* Field values */
    color: #333;
    margin-top: 3px;
    line-height: 1.4;
}

.popup-narrative {
    /* Gray box for historical narratives */
    background: #f8f8f8;
    padding: 10px;
    border-radius: 4px;
    border-left: 3px solid #d32f2f;
    margin: 10px 0;
}

.death-toll-highlight {
    /* Red highlighted box for death counts */
    background: #ffebee;
    padding: 8px 12px;
    border-radius: 4px;
    border-left: 3px solid #c62828;
    font-weight: 600;
    color: #c62828;
}
```

---

## Customization Guide

### How to Edit Popup Content

1. **Find the popup function** in `index.html`:
   - Church Bombings: `function pop_churchbombigs_2_1`
   - Race Riots: `function pop_RaceRiotsSheet1_2`

2. **Modify the HTML structure** using the templates above

3. **Change colors**:
   - Death toll red: `#c62828` and `#ffebee`
   - Narrative border: `#d32f2f`
   - Header text: `#2c2c2c`
   - Labels: `#555`

4. **Add new fields**:
   ```javascript
   if (props['NewField']) {
       popupContent += '<div class="popup-field">\
           <div class="popup-label">New Field Name</div>\
           <div class="popup-value">' + props['NewField'] + '</div>\
       </div>';
   }
   ```

5. **Remove fields**:
   - Simply delete or comment out the section

---

## Alternative Designs

### Option 1: Compact Design (No Labels)

```html
<div>
    <div class="popup-header">[EVENT NAME]</div>
    <div style="font-size: 0.9em; color: #666;">[CITY], [STATE] • [YEAR]</div>
    <div class="death-toll-highlight">Deaths: [NUMBER]</div>
    <div class="popup-narrative">[NARRATIVE]</div>
</div>
```

### Option 2: Card Design with Icon

```html
<div>
    <div style="display: flex; align-items: center; margin-bottom: 10px;">
        <span style="font-size: 2em; margin-right: 10px;">📍</span>
        <div>
            <div class="popup-header" style="margin: 0;">[EVENT NAME]</div>
            <div style="font-size: 0.85em; color: #666;">[CITY], [STATE]</div>
        </div>
    </div>
    <div class="popup-narrative">[NARRATIVE]</div>
</div>
```

### Option 3: Timeline Style

```html
<div>
    <div style="background: #2c2c2c; color: white; padding: 15px; margin: -15px -15px 15px -15px; border-radius: 8px 8px 0 0;">
        <div style="font-size: 0.8em; opacity: 0.8;">[YEAR]</div>
        <div style="font-size: 1.2em; font-weight: 600;">[EVENT NAME]</div>
        <div style="font-size: 0.9em; margin-top: 5px;">[CITY], [STATE]</div>
    </div>
    <div class="death-toll-highlight">Deaths: [NUMBER]</div>
    <div class="popup-narrative">[NARRATIVE]</div>
</div>
```

---

## Quick Edits

### Change Death Toll Color (from red to another color)

Find `.death-toll-highlight` in the `<style>` section and change:
```css
.death-toll-highlight {
    background: #fff3e0;  /* Orange background */
    border-left: 3px solid #f57c00;  /* Orange border */
    color: #e65100;  /* Orange text */
}
```

### Make Narrative Box Blue Instead of Gray

Find `.popup-narrative` and change:
```css
.popup-narrative {
    background: #e3f2fd;  /* Light blue */
    border-left: 3px solid #1976d2;  /* Blue border */
}
```

### Larger Popup Text

Add to the CSS:
```css
.leaflet-popup-content {
    font-size: 1.1em;  /* 10% larger */
}
```

---

## Testing Your Changes

1. Edit `index.html`
2. Save the file
3. Refresh your browser (Cmd+R on Mac, Ctrl+R on Windows)
4. Click a marker to see the updated popup

---

*Created: November 3, 2025*
*Use this reference to customize your map popups without needing to understand all the code!*
