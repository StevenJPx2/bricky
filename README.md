# Bricky

> [!note]
> This library is a fork of [macy.js](https://macyjs.com). This library was created as a successor to it was there was no maintenance to it for 4 years.

## Roadmap
- [ ] Typescript support [(#1)](https://github.com/StevenJPx2/bricky/issues/1)
- [ ] `container` option to use actual HTML elements. [(#2)](https://github.com/StevenJPx2/bricky/issues/2)

Bricky is a lightweight dependency-free JavaScript library designed to sort items vertically into columns by finding an optimum layout with a minimum height.

## Installing

Install with npm:

```
npm install bricky
```

Install with Bower:

```
bower install bricky
```

Include via [jsDelivr CDN](https://www.jsdelivr.com/package/npm/bricky):

```html
<script src="https://cdn.jsdelivr.net/npm/bricky@2"></script>
```

## Usage

```javascript
var brickyInstance = Bricky({
  // See below for all available options.
});
```

## Options

#### **container**

_Default: None_

Use this option to specify your target container element to apply Bricky too. All direct children of an element with this selector will become sortable items and a height applied to the target container.

#### **columns**

_Default: `4`_

Define the default amount of columns to work with. Use the `breakAt` option to specify breakpoints for this value.

#### **trueOrder**

_Default: `false`_

Setting this to false will prioritise equalising the height of each column over the order of the items themselves.

#### **margin**

_Default: `0`_
Adjust the margin between columns with a pixel value. Don’t forget you can still apply padding to the elements with standard CSS.


**Note:** Due to the way the container height is calculated, using anything other than integer in the Y property will cause an error.

```
margin: {
  x: 10,
  y: 16
}
```

When declaring the default margin as an object it requires both and x and y values unlike the breakAt object.

#### **waitForImages**

_Default: `false`_
If set to true, Bricky will wait for all images on the page to load before running. Set to `false` by default, it will run every time an image loads.

#### **useOwnImageLoader**

_Default: `false`_

Set this to true if you would prefer to use a different image loaded library.

#### **mobileFirst**

_Default: `false`_

Setting this value to true will alter how the breakAt options will work. Bricky will now work in a mobile first way so the default `columns` will be the default then if for example you have `400: 2` in your breakAt object, if the document is greater or equal to 400px the column count will be 2.

#### **breakAt**

_Default: `None`_
This object allows you to specify how the total number of columns will change based on the width of the viewport. Setting an option to `780: 3` for example will adjust the column count to 3 when the viewport is <= 780px.
If the viewport resizes after the page has loaded, Bricky will rerun to ensure optimum sorting

If the column is set to one then Bricky will remove all styling to leave you to style it perfectly on mobile.

Added in v2.1 breakAt now supports changing margin within these breakpoints.

For example

```
{
  breakAt: {
    760: {
      margin: {
        x: 20,
        y: 10,
      },
      columns: 4
    }
  }
}
```

If you do not need the modify the margin you can leave it as `760: 4` and bricky will set the columns to 4. You can also just define a change in just one marginal direction for example:

```
{
  breakAt: {
    760: {
      margin: {
        x: 20,
      },
      columns: 4
    }
  }
}
```

This would change the xMargin to 20px when screens are smaller than 760, but the instance will use a previously declared y value.

#### **cancelLegacy**

_Default: `false`_ - If enabled this will cause the script to not run on browsers that don't support native Promises and when there isn't a polyfill present.

#### **useContainerForBreakpoints**

_Default: `false`_ - When enabled the breakpoint options are based on the container elements width instead of the document width.

## Methods

#### **Bricky**

_Parameters: `{Object} args - required`_

This is the initializing function. The function takes an object of properties listed above. The only required property is container which would be the _selector_ for the element that contains all the elements you want to be layed out:

```javascript
var bricky = Bricky({
  container: "#bricky-container",
  trueOrder: false,
  waitForImages: false,
  margin: 24,
  columns: 6,
  breakAt: {
    1200: 5,
    940: 3,
    520: 2,
    400: 1,
  },
});
```

From this point on whenever 'Bricky' is specified it is referencing the variable you assign bricky to when making the initial call.

#### **recalculate**

_Parameters: `{Boolean} refresh - can be null` & `{Boolean} loaded -can be null` _

When called this recalculates the entire layout, this becomes useful if you just used ajax to pull in more content:

```javascript
brickyInstance.recalculate();
```

#### **runOnImageLoad**

_Parameters: `{Function} - Function to run on image load` & `{Boolean} If true it will run everytime an image loads`_

runOnImageLoad is a method used to do something each time and image loads or after all images have been loaded. This helps when using Ajax to make sure the layout is worked out correctly when images are loading. Using this in conjunction with the recalculate function makes your layouts look great no matter how long it takes to load in your images:

```javascript
brickyInstance.runOnImageLoad(function () {
  brickyInstance.recalculate(true);
}, true);
```

If you only require it to run once all the images have loaded you can achieve this by ommiting the second parameter as this defaults to false:

```javascript
brickyInstance.runOnImageLoad(function () {
  console.log("I only get called when all images are loaded");
  brickyInstance.recalculate(true, true);
});
```

If you only require the during function to run then only pass it one function:

```javascript
brickyInstance.runOnImageLoad(function () {
  console.log("Every time an image loads I get fired");
  brickyInstance.recalculate(true);
}, true);
```

The callback function receives an event as its first and only property, if you are running the callback on every image load then the function has access to the image that has just loaded.

```javascript
brickyInstance.runOnImageLoad(function (event) {
  if (event.data.img) {
    // note: this parameter can be undefined if it is the final completion event that is emitted.
    console.log(event.data.img);
  }
}, true);
```

#### **remove**

_Parameters: `None`_

Remove does exactly what it says on the tin, it removes all styling and event listeners that Bricky added to the DOM:

```javascript
brickyInstance.remove();
```

#### **reInit**

_Parameters: `None`_

Reinitialises the current bricky instance;

```javascript
brickyInstance.reInit();
```

#### **on**

_Parameters: {String} - Event key, {Function} the function to run when the event occurs_

This would console log when all images are loaded.

```javascript
brickyInstance.on(
  brickyInstance.constants.EVENT_IMAGE_COMPLETE,
  function (ctx) {
    console.log("all images have loaded");
  },
);
```

#### **emit**

_Parameters: {String} - Event key_

Emit an event, although bricky does not utilise most of these events, these are more to trigger your own functions.

---

## _Constants_

Bricky now has some constants available to be used with in the events system. This is to make sure the functions are targetting the correct event as the naming may be subject to change
They are all accessible under `brickyInstance.constants`

Currently available constants

| Key                  | Value                      | Description                                                             |
| -------------------- | -------------------------- | ----------------------------------------------------------------------- |
| EVENT_INITIALIZED    | `'bricky.initialized'`     | This is the event constant for when bricky is initialized/reinitialized |
| EVENT_RECALCULATED   | `'bricky.recalculated'`    | This is the event constant for every time the layout is recalculated    |
| EVENT_IMAGE_LOAD     | `'bricky.images.load'`     | This is the event constant for when an image loads                      |
| EVENT_IMAGE_COMPLETE | `'bricky.images.complete'` | This is the event constant for when all images are complete             |
| EVENT_RESIZE         | `'bricky.resize'`          | This is the event constant for when the document is resized             |

---

## _Notes_

- Browser support for all major browsers excluding IE11
- IE11 requires a Promise polyfill for image loading to work
- To support IE10 a dataset polyfill will have to be added
