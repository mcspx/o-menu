# O! Menu (o-menu)
Simple circular menu based on SVG

[DEMO](https://dawiidio.github.io/o-menu-demo/)

![](https://user-images.githubusercontent.com/7998389/34531622-37467548-f0b3-11e7-8d98-55ab43982525.png)

## Installation
```bash
npm i --save o-menu
```
## Usage
Options for menu:

* **menu**:
    * **padding**: *number* - padding for menu
    * **positioningMode**: *string(`relativeToParent`|`relativeToScreen`)* - Tells menu how to positioning on page,
    takes two possible values
      *  `relativeToParent` - **default** menu behavior will be similar
    to css absolute element (menu) positionig in relative parent (menu
    parent element)
      *  `relativeToScreen` - menu behavior will be similar to css `position: absolute`
    * **closeMenuOn**: *string|boolean* - close menu on event, if false close only manually by close method in object returns by oMenu function call
    * **elClass**: *string* - css class which will be bounded to the menu main element
    * **circleDegOrigin**: *number* - default is -90
    * **innerCircleWidth**: *number* - radius width of inner circle
    * **innerCircleContent**: *string* - HTML string, defines what render in inner circle
    * **firstLevelSliceWidth**: *number* - width of first level slices radius
    * **nthLevelSliceWidth**: *number* - width of second level slices radius
    * **menuShowTime**: *number* - time in ms, defines speed of innerCircle show animation
    * **menuHideTime**: *number* - time in ms, defines speed of innerCircle hide animation
    * **styles**: *object* - all styles needs to be written in camel case notation
        * **innerCircle**: *object* - any initial CSS styles for inner circle
        * **hidden**: *object* - styles appiled to element when it is hidden
        * **visible**: *object* - styles appiled to element when it is visible
        * **defaults**: *object* - initial styles for root menu element
* **slice**: *object* - Default options for first level slices
    * **contentSize**: *number* - size of slice content in pixels (it is usualy icon so content is always square thats width = height)
    * **contentMoveX**:*number* - slice content move in pixels at x axis
    * **contentMoveY**:*number* - slice content move in pixels at y axis
    * **parentFillMode**:*number* - Number in range -1 to 1. Negative - color darker, positive - color brighter, 0 - not set.
    Option for nested slices. When it is different from zero fill color will be based on parent's color, but will be darker or brighter depending on the parentFillMode value.
    * **iconDistanceFromInnerCircle**:*number* content distance from inner circle, for nested slices inner circle is radius of parent's slices
    * **content**:*string* - HTML string with content for slice
    * **sliceClass**:*string* - class which will be appiled to slice element
    * **sliceShowTime**:*number* - slice show animation duration
    * **sliceHideTime**:*number* - slice hide animation duration
    * **slices**:*array[object]* - children slices
    * **data**:*any* - data will be passed as property of Event parameter for `on` callback
    * **onClick**: *function* - callback fired when slice was clicked
    * **styles**:*object*
        * **defaults**:*object* - default styles for slice
        * **contentContainer**:*object* - slice content container default styles
        * **hover**:*object*: styles appiled to slice after hover on it
* **nthSlice**: *object* - Default options for second level slices, same as slice
* **slices**: *array[object]* - Array of objects which can contains same options as described in slice, options set here are overwrite these set in slice or nthSlice as default.

### Simple example
```javascript
import oMenu from 'o-menu';

// create menu instance, firstly you need to pass parent element id for menu, secondly default options for menu
const omenu = oMenu('main', {
    menu: {
        innerCircleRadius: 55
    },
    nthSlice: {
        contentSize: 22,
        iconDistanceFromInnerCircle: 5,
        parentFillMode: -0.3,
        styles: {
            contentContainer:{
                fontSize: 22,
                color: '#efefef'
            }
        }
    },
    slice: {
        contentSize : 30,
        iconDistanceFromInnerCircle: 10,
        styles: {
            contentContainer:{
                fontSize: 30,
                color: '#efefef'
            },
        }
    },
    onOpen: onOpenCb,
    onEndCloseAnimation: val => {
        if(val)
            alert(`You want to: ${val}`)
    }
});

omenu.on('sliceEnter', ev => {
    console.log(ev); // => oMenu Event
});

document.body.addEventListener('contextmenu', ev => {
    ev.preventDefault();

    omenu.open(ev, {
       slices :[
           {
               content: 'A',
               styles:{
                   defaults: {
                       fill: '#8BC34A'
                   }
               },
               data: 'send email'
           },
           {
               content: 'B',
               styles:{
                   defaults: {
                       fill: '#F44336'
                   }
               },
               data: 'delete user'
           }
       ]
    });
}

/**
 in const c we have object with three methods for manual open, close and trigger menu
 c = {
    open: function(ev, options){...} - function which opens menu, requires event in first param and dynamic menu options
                                       for second. WARNING! if you open menu manually, onOpen callback will never be called
                                       therfore you need to pass options directly to open method.
    close: function(ev){...} - function which close menu, requires event in first param
    trigger: function(ev){...} - function which triggers menu
 }
*/
```
## API
When `oMenu('main', ...)` function is called, returns object which provides
api for menu management, it looks like:
*  **open(ev, dynamicOptions)** - function which opens menu, requires event in first param and dynamic menu options for
second.
*  **close(ev)** - function which close menu, requires event in first param
*  **trigger(ev)** - function which triggers menu
*  **on(eventName, cb)** - works exactly like well known equivalents from other libraries. Accepts two params -
string with event name and callback function. Callback functions gets in first param oMenu special Event class instance.
List of all available events: `sliceClick`, `sliceEnter`, `sliceLeave`, `openMenu`, `closeMenu`, `hideAnimationEnd`,
`showAnimationEnd`.
oMenu Event shape:

```
{
 "originalEvent": oMenuEvent|Event|null, - original event which triggered action
 "type": string, -  one of mentioned above events
 "data": any|null, - data passed by user in slice definition
 "target": Slice|null, - target slice
 "hasNestedSlices": boolean|number, - if Slice event and target slice has slices
 "isSlice": boolean - if slice then true
}
```


## Full default options
```javascript
{
    menu                : {
        padding : 10,
        elClass : 'circle-menu',
        circleDegOrigin: -90,
        innerCircleWidth: 45,
        innerCircleContent: ``,
        firstLevelSliceWidth: 70,
        nthLevelSliceWidth: 50,
        menuShowTime: 100,
        menuHideTime: 100,
        styles : {
            innerCircle: {
                strokeColor: '',
                strokeWidth: '',
                fill: '#fff',
            },
            hidden : {
                zIndex: -1,
                visibility: 'hidden'
            },
            visible: {
                zIndex: 9999,
                visibility: 'visible'
            },
            defaults: {
                position: 'fixed'
            }
        }
    },
    slice               : {
        contentSize : 38,
        contentMoveX: 0,
        contentMoveY: 0,
        parentFillMode : 0,
        iconDistanceFromInnerCircle: 0,
        content: null,
        sliceClass : 'circle-slice',
        sliceShowTime: 100,
        sliceHideTime: 100,
        slices: [],
        data: null,
        styles: {
            defaults: {
                cursor: 'pointer',
                fill: '#f1f1f1'
            },
            contentContainer: {
                color: 'red',
                fontSize: 38,
                cursor: 'pointer'
            },
            hover: {

            }
        }
    },
    nthSlice            : {
        contentSize : 38,
        contentMoveX: 0,
        contentMoveY: 0,
        parentFillMode : 0,
        iconDistanceFromInnerCircle: 0,
        content: null,
        sliceClass : 'circle-slice',
        sliceShowTime: 100,
        sliceHideTime: 100,
        slices: [],
        data: null,
        styles: {
            defaults: {
                cursor: 'pointer',
                fill: '#f1f1f1'
            },
            contentContainer: {
                color: 'red',
                fontSize: 38,
                cursor: 'pointer'
            },
            hover: {

            }
        }
    },
    slices              : []
}
```
