---
description: Comprehensive mobile responsiveness audit for web maps and applications
---

# Mobile Responsiveness Audit Skill

Use this skill when the user asks to:
- Ensure their map/site looks good on mobile
- Audit mobile responsiveness
- Optimize for touch devices
- Check accessibility on mobile

---

## Mobile Audit Checklist

### 1. Touch Target Sizes ✅

**Minimum Standard:** 44x44px (Apple/Google Human Interface Guidelines)

**Check these elements:**
- [ ] Zoom controls (`.leaflet-control-zoom a`)
- [ ] Layer toggles (`.leaflet-control-layers-toggle`)
- [ ] Buttons and interactive elements
- [ ] Cluster badges (`.marker-cluster`)
- [ ] Custom controls

**Fix Pattern:**
```css
.leaflet-control-zoom a {
    width: 44px;
    height: 44px;
    line-height: 44px;
    font-size: 20px;
}

.leaflet-control-layers-toggle {
    width: 44px;
    height: 44px;
}
```

---

### 2. Responsive Breakpoints 📱

**Standard Breakpoints:**
- **Small Mobile:** ≤480px (portrait phones)
- **Mobile:** ≤768px (tablets, large phones)
- **Landscape Mobile:** ≤768px + `orientation: landscape`
- **Desktop:** ≥1024px

**Implementation:**
```css
/* Small mobile optimizations */
@media (max-width: 480px) {
    .popup-narrative {
        padding: 8px;
        font-size: 0.95em;
    }

    .death-toll-highlight {
        padding: 6px 10px;
        font-size: 0.95em;
    }
}

/* Landscape mobile handling */
@media (max-width: 768px) and (orientation: landscape) {
    .map-header {
        padding: 8px 15px;
    }

    .map-title {
        font-size: 1rem;
    }

    .leaflet-popup-content {
        max-width: 300px;
        max-height: 200px;
        overflow-y: auto;
    }
}
```

---

### 3. Touch Feedback & Interaction 👆

**Required for good UX:**
- [ ] Visual feedback on tap (opacity change)
- [ ] Remove default tap highlight
- [ ] Prevent text selection on controls
- [ ] Use `touch-action: manipulation` to prevent double-tap zoom

**Implementation:**
```css
/* Prevent text selection on map controls */
.leaflet-control,
.leaflet-control a,
.marker-cluster {
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    -webkit-tap-highlight-color: transparent;
}

/* Touch feedback */
.leaflet-control-zoom a:active,
.leaflet-control-layers-toggle:active,
.marker-cluster:active {
    opacity: 0.7;
}

/* Ensure tappability */
.marker-cluster {
    cursor: pointer;
    touch-action: manipulation;
}
```

---

### 4. Viewport Configuration 📐

**Check the viewport meta tag:**
```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

**For maps:** Disable pinch zoom (map handles zoom)
**For content sites:** Allow user scaling for accessibility

---

### 5. Typography & Content 📝

**Mobile Typography Best Practices:**
- [ ] Base font size: 14-16px minimum
- [ ] Line height: 1.4-1.6 for readability
- [ ] Reduce heading sizes on mobile
- [ ] Use fluid typography with `clamp()`

**Example:**
```css
@media (max-width: 768px) {
    .map-title {
        font-size: clamp(1.2rem, 4vw, 1.5rem);
    }

    .map-subtitle {
        font-size: clamp(0.8rem, 3vw, 0.9rem);
    }
}
```

---

### 6. Popups & Modals 💬

**Mobile Popup Optimization:**
- [ ] Max-width for readability (250-300px)
- [ ] Max-height with scrolling in landscape
- [ ] Adequate padding (not too cramped)
- [ ] Close button easy to tap

**Implementation:**
```css
@media (max-width: 768px) {
    .leaflet-popup-content-wrapper {
        max-width: 280px;
    }
}

@media (max-width: 768px) and (orientation: landscape) {
    .leaflet-popup-content {
        max-height: 200px;
        overflow-y: auto;
    }
}
```

---

### 7. Performance Considerations ⚡

**Mobile Performance Checklist:**
- [ ] Minimize initial payload size
- [ ] Use appropriate image sizes
- [ ] Lazy load non-critical resources
- [ ] Test on 3G/4G throttling
- [ ] Check cluster performance with many markers

---

### 8. Testing Matrix 🧪

**Devices to Test:**

**iOS:**
- [ ] iPhone SE (small screen)
- [ ] iPhone 12/13/14 (standard)
- [ ] iPhone 14 Pro Max (large)
- [ ] iPad (tablet)

**Android:**
- [ ] Small Android phone (≤5.5")
- [ ] Standard Android (6-6.5")
- [ ] Large Android (≥6.7")
- [ ] Android tablet

**Orientations:**
- [ ] Portrait mode
- [ ] Landscape mode

**Browsers:**
- [ ] Safari (iOS)
- [ ] Chrome (Android/iOS)
- [ ] Firefox (Android/iOS)

---

## Quick Audit Script

Use this process when auditing any web map:

1. **Visual Inspection:**
   - Open DevTools mobile emulator
   - Test iPhone SE (smallest), iPhone 14, iPad
   - Check portrait and landscape
   - Toggle layers, zoom controls, click markers

2. **Touch Target Check:**
   - Inspect all interactive elements
   - Measure with DevTools (should be ≥44px)
   - Test on actual device if possible

3. **Responsive CSS:**
   - Check for mobile media queries
   - Verify breakpoints make sense
   - Test edge cases (very small, landscape)

4. **Touch Interaction:**
   - Tap controls - do they respond visually?
   - Can you select text accidentally?
   - Is double-tap zoom disabled?

5. **Content Readability:**
   - Is text large enough (≥14px)?
   - Are popups readable in landscape?
   - Do long narratives scroll properly?

---

## Common Issues & Fixes

### Issue: Controls too small to tap
**Fix:** Increase to 44x44px minimum

### Issue: Popups too tall in landscape
**Fix:** Add max-height with overflow-y: auto

### Issue: Text selection when tapping map
**Fix:** Add user-select: none to controls

### Issue: Pinch zoom interfering with map zoom
**Fix:** Set viewport maximum-scale=1.0, user-scalable=no

### Issue: Cluster badges hard to tap
**Fix:** Ensure 40x40px minimum, add touch-action: manipulation

### Issue: Layout breaks on very small screens
**Fix:** Add @media (max-width: 480px) with reduced padding/fonts

---

## Reporting Format

After audit, report findings in this format:

```
📱 Mobile Responsiveness Audit Complete

✅ PASSING:
- Touch targets meet 44px standard
- Responsive breakpoints implemented
- Touch feedback working
- Popups optimized for mobile

⚠️ IMPROVEMENTS NEEDED:
- [Specific issue found]
- [Recommended fix]

📊 Tested On:
- iPhone SE, 12, 14 Pro Max
- iPad
- Various Android devices
- Portrait and landscape modes

🔧 Changes Made:
- [List modifications if you made them]
```

---

## Code Snippets Library

### Full Mobile CSS Template

```css
/* ===== MOBILE RESPONSIVENESS ===== */

/* Touch target sizes - 44px minimum */
@media (max-width: 768px) {
    .leaflet-control-zoom a {
        width: 44px;
        height: 44px;
        line-height: 44px;
        font-size: 20px;
    }

    .leaflet-control-layers-toggle {
        width: 44px;
        height: 44px;
    }
}

/* Small mobile optimizations */
@media (max-width: 480px) {
    .popup-narrative {
        padding: 8px;
        font-size: 0.95em;
    }

    .death-toll-highlight {
        padding: 6px 10px;
        font-size: 0.95em;
    }

    .marker-cluster {
        cursor: pointer;
        touch-action: manipulation;
    }
}

/* Touch feedback and interaction */
.leaflet-control,
.leaflet-control a,
.marker-cluster {
    -webkit-user-select: none;
    -moz-user-select: none;
    -ms-user-select: none;
    user-select: none;
    -webkit-tap-highlight-color: transparent;
}

.leaflet-control-zoom a:active,
.leaflet-control-layers-toggle:active,
.marker-cluster:active {
    opacity: 0.7;
}

/* Landscape mobile mode */
@media (max-width: 768px) and (orientation: landscape) {
    .map-header {
        padding: 8px 15px;
    }

    .map-title {
        font-size: 1rem;
    }

    .map-subtitle {
        font-size: 0.75rem;
    }

    .leaflet-popup-content {
        max-width: 300px;
        max-height: 200px;
        overflow-y: auto;
    }
}
```

---

**Created:** November 3, 2025
**Use this skill whenever mobile optimization is requested for web maps or applications.**
