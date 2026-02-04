# Color Harmony Algorithms

## Color Theory Basics

### HSL Color Space
- **H**ue: 0-360° on color wheel
- **S**aturation: 0-100% (gray to vivid)  
- **L**ightness: 0-100% (black to white)

### Implementation Notes
- Work in HSL for color harmony math
- Convert to HEX/RGB for display
- JavaScript: `hsl(${h}, ${s}%, ${l}%)`

## Harmony Types

### 1. Complementary Colors
**Theory:** Colors opposite on color wheel (180° apart)
```javascript
function complementary(baseHue) {
    return [
        baseHue,                    // Base color
        (baseHue + 180) % 360      // Complement
    ];
}
```

### 2. Triadic Colors  
**Theory:** Three colors evenly spaced (120° apart)
```javascript
function triadic(baseHue) {
    return [
        baseHue,
        (baseHue + 120) % 360,
        (baseHue + 240) % 360
    ];
}
```

### 3. Analogous Colors
**Theory:** Colors next to each other (30° apart)
```javascript  
function analogous(baseHue) {
    return [
        (baseHue - 30 + 360) % 360,
        baseHue,
        (baseHue + 30) % 360
    ];
}
```

### 4. Split-Complementary
**Theory:** Base + two colors adjacent to complement
```javascript
function splitComplementary(baseHue) {
    const complement = (baseHue + 180) % 360;
    return [
        baseHue,
        (complement - 30 + 360) % 360,
        (complement + 30) % 360
    ];
}
```

### 5. Tetradic (Rectangle)
**Theory:** Two pairs of complementary colors
```javascript
function tetradic(baseHue) {
    return [
        baseHue,
        (baseHue + 90) % 360,
        (baseHue + 180) % 360,  
        (baseHue + 270) % 360
    ];
}
```

## Palette Generation Strategy

### Base Color Processing
```javascript
function generatePalette(baseHex, harmonyType) {
    // 1. Convert HEX to HSL
    const [h, s, l] = hexToHsl(baseHex);
    
    // 2. Get harmony hues
    const hues = harmonyFunctions[harmonyType](h);
    
    // 3. Create variations
    const palette = [];
    hues.forEach(hue => {
        palette.push({
            hex: hslToHex(hue, s, l),
            hsl: [hue, s, l],
            name: getColorName(hue)
        });
    });
    
    // 4. Add variations (lighter/darker)
    if (palette.length < 5) {
        addVariations(palette);
    }
    
    return palette;
}
```

### Color Variations
For palette padding (to reach 5 colors):
```javascript
function addVariations(palette) {
    const base = palette[0];
    const [h, s, l] = base.hsl;
    
    // Add lighter version
    if (l < 80) {
        palette.push({
            hex: hslToHex(h, s, Math.min(l + 20, 90)),
            type: 'lighter'
        });
    }
    
    // Add darker version  
    if (l > 20) {
        palette.push({
            hex: hslToHex(h, s, Math.max(l - 20, 10)),
            type: 'darker'
        });
    }
}
```

## Color Conversion Functions

### HEX to HSL
```javascript
function hexToHsl(hex) {
    const r = parseInt(hex.substr(1, 2), 16) / 255;
    const g = parseInt(hex.substr(3, 2), 16) / 255;
    const b = parseInt(hex.substr(5, 2), 16) / 255;
    
    const max = Math.max(r, g, b);
    const min = Math.min(r, g, b);
    let h, s, l = (max + min) / 2;
    
    if (max === min) {
        h = s = 0; // achromatic
    } else {
        const d = max - min;
        s = l > 0.5 ? d / (2 - max - min) : d / (max + min);
        
        switch (max) {
            case r: h = (g - b) / d + (g < b ? 6 : 0); break;
            case g: h = (b - r) / d + 2; break;
            case b: h = (r - g) / d + 4; break;
        }
        h /= 6;
    }
    
    return [Math.round(h * 360), Math.round(s * 100), Math.round(l * 100)];
}
```

### HSL to HEX  
```javascript
function hslToHex(h, s, l) {
    h = h / 360;
    s = s / 100;
    l = l / 100;
    
    const hue2rgb = (p, q, t) => {
        if (t < 0) t += 1;
        if (t > 1) t -= 1;
        if (t < 1/6) return p + (q - p) * 6 * t;
        if (t < 1/2) return q;
        if (t < 2/3) return p + (q - p) * (2/3 - t) * 6;
        return p;
    };
    
    const q = l < 0.5 ? l * (1 + s) : l + s - l * s;
    const p = 2 * l - q;
    
    const r = Math.round(hue2rgb(p, q, h + 1/3) * 255);
    const g = Math.round(hue2rgb(p, q, h) * 255);
    const b = Math.round(hue2rgb(p, q, h - 1/3) * 255);
    
    return `#${((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1)}`;
}
```

## Game Dev Specific Features

### Color Blindness Simulation
```javascript
function simulateColorBlindness(hex, type) {
    // Protanopia, Deuteranopia, Tritanopia
    // Matrix transformations for accessibility
}
```

### Export Formats

#### GIMP/Aseprite (.gpl)
```
GIMP Palette
Name: Game Palette
#
255 100  80  Red
100 255  80  Green
 80 100 255  Blue
```

#### CSS Variables
```css
:root {
    --color-primary: #ff6450;
    --color-secondary: #64ff50;
    --color-accent: #5064ff;
}
```

#### Unity C# Class
```csharp
public static class GamePalette {
    public static Color Primary = new Color(1f, 0.39f, 0.31f);
    public static Color Secondary = new Color(0.39f, 1f, 0.31f);
}
```