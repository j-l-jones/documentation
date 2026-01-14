---
title: "makehuman.qss"
weight: 50
description: "MakeHuman Qt Stylesheet"
---

## makehuman.qss
# MakeHuman Qt Stylesheet

## Overview

The `makehuman.qss` file is a Qt Style Sheet (QSS) file that defines the visual appearance and styling of the MakeHuman II user interface. QSS is similar to Cascading Style Sheets (CSS) used in web development, but specifically designed for Qt applications.

This stylesheet controls the look and feel of all Qt widgets used in the application, including buttons, menus, sliders, labels, list widgets, tree views, and more. It defines colors, borders, padding, margins, backgrounds, and other visual properties.

## File Location

- Theme stylesheet: `/data/themes/makehuman.qss`

## Purpose

The stylesheet serves several key purposes:

- **Consistent Visual Identity**: Establishes a unified dark theme across the entire application
- **User Experience**: Improves readability and usability through careful color choices and contrast
- **Professional Appearance**: Creates a modern, polished interface for 3D modeling work
- **Customization**: Allows easy modification of the UI appearance without code changes

## Color Scheme

The MakeHuman II interface uses a theme with the following primary colors:

| Color | Hex Code | Usage |
|-------|----------|-------|
| Primary Background | `#323232` | Main widget backgrounds |
| Secondary Background | `#414141` | Labels, groupboxes, secondary areas |
| Light Gray | `#d9d9d9` | Buttons, menu bars |
| Medium Gray | `#a3a3a3` | Borders, pressed buttons |
| Dark Gray | `#8a979b` | Frame borders, groupbox titles |
| Orange (Accent) | `#d7801a` | Selections, active sliders, checked buttons |
| White | `#ffffff` | Primary text color |
| Black | `#000000` | Text on light backgrounds, slider grooves |

## Widget Styling

### QWidget (Base Widget)

All widgets inherit these default properties:

```css
QWidget {
    color: #ffffff;
    background-color: #323232;
}
```

### QLabel

Labels use a slightly lighter background than the base widget:

```css
QLabel {
    background-color: #414141;
    margin-top: 6px;
}
```

**Special Label Types:**

- `QLabel#groupbox`: Used as groupbox titles with center alignment, rounded top corners, bold text, and light background

### QGroupBox

Groupboxes provide visual organization with borders and titles:

```css
QGroupBox {
    font: bold 12px;
    background-color: #414141;
    border: 3px solid #8a979b;
    border-radius: 6px;
    padding: 14px 0px 0px 0px;
}
```

**Special GroupBox Types:**

- `QGroupBox#subwindow`: Smaller border (2px), different title styling for sub-windows

### QFrame

Custom frames provide flexible containers:

- `QFrame#groupbox`: Bottom-rounded frame matching groupbox label styling
- `QFrame#gboxnontitle`: Fully rounded frame without title
- `QFrame#gboxseltools`: Selection tools container with rounded corners

### QPushButton

Buttons have three states with distinct appearances:

```css
QPushButton {
    color: #000000;
    background-color: #d9d9d9;
    border: 3px solid #a3a3a3;
    border-radius: 6px;
}

QPushButton::pressed {
    color: #ffffff;
    background-color: #a3a3a3;
}

QPushButton::checked {
    background: #d7801a;
}

QPushButton::disabled {
    background: #808080;
    border-color: #606060;
    color: #a0a0a0;
}
```

### QMenuBar and QMenu

Menu components use light backgrounds with hover effects:

```css
QMenuBar {
    color: #000000;
    background-color: #d9d9d9;
    border: 4px solid #a3a3a3;
    font: bold 14px;
    spacing: 3px;
}
```

**Menu States:**
- Normal: Transparent background
- Selected/Hover: Border highlight
- Pressed: Gray background

### QListWidget

List widgets display items with orange selection highlighting:

```css
QListWidget {
    margin-top: 6px;
    color: white;
    selection-background-color: #d7801a;
}

QListWidget::item:selected {
    background: #d7801a;
}
```

### QTreeView

Tree views for hierarchical data with expandable branches:

```css
QTreeView {
    selection-color: white;
    selection-background-color: #d7801a;
    color: white;
    show-decoration-selected: 1;
}
```

**Features:**
- Custom expand/collapse icon: `data/icons/expand_orange.png`
- Orange selection background matching the accent color

### QCheckBox and QRadioButton

Form controls with custom indicators:

**QCheckBox:**
- Background matches secondary color (`#414141`)
- Unchecked indicator uses dark background (`#323232`)
- Disabled state changes text color to black

**QRadioButton:**
- Checked: Light gray indicator with rounded corners
- Unchecked: Dark gray indicator with rounded corners

### QSlider

Sliders come in both horizontal and vertical orientations:

#### Horizontal Sliders

```css
QSlider::horizontal {
    color: black;
    background-color: #a3a3a3;
    padding-top: -3px;
    padding-bottom: -3px;
    border: 2px solid #a3a3a3;
    border-radius: 5px;
}
```

**Components:**
- **Groove**: Black background with 14px height
- **Handle**: 24px wide custom image (`data/icons/slider.png`)
- **Sub-page**: Orange fill showing slider value
- **Disabled state**: Gray appearance

#### Vertical Sliders

Similar styling with orientation adjustments:
- **Groove**: 14px width
- **Handle**: 24px tall custom image (`data/icons/vslider.png`)
- **Add-page**: Orange fill (opposite of horizontal)

### QProgressBar

Progress bars show operation completion:

```css
QProgressBar {
    background-color: #000000;
    border: 2px solid #a3a3a3;
    border-radius: 5px;
    text-align: center;
}

QProgressBar::chunk {
    background-color: #d7801a;
}
```

### QTableView and QHeaderView

Tables use dark backgrounds with light text:

```css
QTableView {
    background-color: #323232;
    color: white;
}

QHeaderView {
    background-color: #5c5c5c;
}
```

### QTabBar

Tab navigation with selection highlighting:

```css
QTabBar::tab {
    background-color: #5c5c5c;
    border: 2px solid #a3a3a3;
    border-radius: 5px;
    padding: 3px 2px;
    margin-left: 2px;
}

QTabBar::tab:selected {
    border-color: #d7801a;
}
```

### QToolTip

Tooltips use bright orange background for visibility:

```css
QToolTip {
    border: 1px solid black;
    background-color: #ffa02f;
    padding: 1px;
    border-radius: 3px;
    opacity: 255;
}
```

## Customization

### Modifying the Theme

To customize the MakeHuman II appearance:

1. **Edit the QSS file**: Open `/data/themes/makehuman.qss` in a text editor
2. **Modify colors**: Change hex color values to your preference
3. **Adjust sizing**: Modify padding, margins, border widths, and border-radius values
4. **Change fonts**: Update font properties (bold, size)
5. **Save and restart**: Changes take effect when MakeHuman is restarted

## Technical Details

### Qt Stylesheet Syntax

QSS follows CSS syntax with Qt-specific extensions:

```css
WidgetType#objectName::sub-control:state {
    property: value;
}
```

**Components:**
- `WidgetType`: Qt widget class (e.g., QPushButton)
- `#objectName`: Optional object name selector
- `::sub-control`: Optional sub-control (e.g., ::handle, ::groove)
- `:state`: Optional state selector (e.g., :pressed, :disabled)

### Image Resources

The stylesheet references several image files:

- `data/icons/expand_orange.png`: Tree view expand/collapse indicator
- `data/icons/slider.png`: Horizontal slider handle
- `data/icons/vslider.png`: Vertical slider handle

These images must be present in the specified paths for proper rendering.

### Property Types

Common QSS property types used:

- **Colors**: Hex format (`#rrggbb`) or named colors
- **Lengths**: Pixels (px implicit)
- **Borders**: Width style color (e.g., `3px solid #a3a3a3`)
- **Fonts**: Style weight size family (e.g., `bold 14px`)
- **Alignment**: Qt alignment flags (e.g., `AlignCenter`)

## References

- [Qt Style Sheets Documentation](https://doc.qt.io/qt-5/stylesheet.html)
- [Qt Style Sheets Reference](https://doc.qt.io/qt-5/stylesheet-reference.html)
- [Qt Widget Classes](https://doc.qt.io/qt-5/widget-classes.html)

## Example

[makehuman.qss](makehuman.qss)