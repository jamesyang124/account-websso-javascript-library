# Viewport Util for VIVEPORT Desktop

## Usage

If the related web pages from account or VIVEPORT store domain, this util could help VIVEPORT Desktop to resize embedded browser window, you can check it's default `expectHeight` and `expectWidth`  in pixel unit:

```javascript
console.log(window.ViewportUtil.expectHeight);
// 800

console.log(window.ViewportUtil.expectWidth);
// 1024
```

Or configure it if layout changed:

```javascript
window.ViewportUtil.expectHeight = 1920;
window.ViewportUtil.expectWidth = 1080;
```

Then page provider should manually call the `update` function to trigger resizing if resizing script registered:

```javascript
window.ViewportUtil.update();
```

{% hint style="warning" %}
`expectHeight` and `expectWidth` setter will set as input value or remain current value if input value is not a number.
{% endhint %}

## Register Script

Once the auth lib is loaded or in account.htcvive.com domain's infinity auth flow, register resize function, this function will get `(height, width)` parameters as util's `expectHeight` and `expectWidth` property, so you can change the window size based on this info:

```javascript
// assume related page already loaded
// https://account.htcvive.com/infinity/lib.js
// or in account.htcvive.com domain's infinity auth flow

const exampleResizeScript = function(height, width) {
    // include C# injection script
    if (height > 1920) {
       // ....
    }
};

window.ViewportUtil.register(exampleResizeScript);
```

## Update by Script

After script registered, update window size by the script:

```javascript
window.ViewportUtil.update();
// should change window size by injected script
```

