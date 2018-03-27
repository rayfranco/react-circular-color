# Vue Circular Color picker [![npm](https://img.shields.io/npm/dt/vue-circular-color.svg)](https://www.npmjs.com/package/vue-circular-color)


A circular color picker component also named color-wheel performed with vue and pure svg. Mobile compatible.

![circular color picker](/assets/wheel.png) ![circular color picker](/assets/wheel32.PNG)

# Installation

```bash
npm install vue-circular-color
```

# Usage

```javascript
import CircularColor from 'vue-circular-color';

export default {
  components: {
    CircularColor
  }
}
```

```html
<CircularColor :size="200" @change="handleColorChange" />
```

# Props

| Name | Description |
| ---- | ----------- |
| `size` | Numeric size of the element in pixels. Default: `200` |
| `numberOfSectors` | Number of wheel's sectors. Default: `360` |
| `className` | Classes to apply to the svg element |
| `centerRect` | Whether to display central rectangle with picked color. Default: `false`. |

# Events
| Name | Description |
| ---- | ----------- |
| `change` | Fired when the color is changing |
| `touch` | Fired when the touch status change |

# Development

> __Not ported yet__

To run demo locally on localhost:8080:

```bash
npm install
npm start
```

# Test

> __Not ported yet__

To run test type:

```bash
npm run test
```