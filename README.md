|[Introduction](#cordova-plugin-ml-text) | [Supported_Platforms](#supported-platforms) | [Installation_Steps](#installation-steps) | [Plugin_Usage](#plugin-usage) | [Working_Examples](#working-examples) | [More_about_us!](#more-about-us)|
|:---:|:------:|:---:|:---:|:---:|:---:|
|||||||


# cordova-plugin-ml-text

> This plugin was made possible because of Google's [ML Kit](https://firebase.google.com/docs/ml-kit/) SDK, as it is a dependency of this plugin. The supported languages are listed [here](https://developers.google.com/vision/android/text-overview). This plugin is absolutely free and will work offline once install is complete. All required files required for Text Recognition are downloaded during install if necessary space is available.

This plugin defines a global `mltext` object, which provides an method that accepts image uri or base64 inputs. If some text was detected in the image, this text will be returned in an object. The imageuri or base64 can be send to the plugin using any another plugin like [cordova-plugin-camera](https://github.com/apache/cordova-plugin-camera) or [cordova-plugin-document-scanner](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner). Although the object is attached to the global scoped `window`, it is not available until after the `deviceready` event.

```
document.addEventListener("deviceready", onDeviceReady, false);
function onDeviceReady() {
console.log(mltext);
}
```

# Supported Platforms

- Android

# Installation Steps

This requires cordova 7.1.0+ , cordova android 6.4.0+

`cordova plugin add cordova-plugin-ml-text`

> Note : This might take a while!

**Optional installation variable for Android**
*MLKIT_TEXT_RECOGNITION_VERSION*
Version of `com.google.android.gms:play-services-mlkit-text-recognition`. This defaults to `16.1.0` but by using this variable you can specify a different version if you like:

`cordova plugin add cordova-plugin-ml-text --variable MLKIT_TEXT_RECOGNITION_VERSION=16.1.0`

Also add the following plugin :- 

- `cordova plugin add cordova-plugin-camera`


**Firebase Setup :-**
This version of the plugin only uses the on-device functionality and no longer requires Firebase.

# Plugin Usage

`mltext.getText(onSuccess, onFail, options);`
- **mltext.getText**
The **`mltext.getText`** function accepts image data as uri or base64 and uses google mobile vision to recognize text and return the recognized text as string on its successcallback.
- **options**
Object that takes the following key - value pairs :-
> Exampe **options** object :-  `{imgType : 0,imgSrc : imageData}` where imageData is obtained from the camera or scan plugin.
- **imgType**
The **`imgType`** parameter can take values 0,1,2,3 or 4 each of which are explained in detail in the table below. `sourceType` is an `Int` within the native code.

| imgType        | imgSrc     | Accuracy      | Recommendation  | Notes       |
| :-------------:   |:-------------:|:-------------:|:-------------:  |:-------------:  |
| 0                 | NORMFILEURI   | Very High     | Recommended     | On android this is same as NORMNATIVEURI |
| 1                 | NORMNATIVEURI | Very High     | Not Recommended (See note below)     | On android this is same as NORMFILEURI |
| 2                 | FASTFILEURI   | Very Low      | Not Recommended | On android this is same as FASTNATIVEURI. Compression allows for faster processing but sacrifices a lot of accuracy. Best used if ocr images will always be extremely large with large text in them. |
| 3                 | FASTNATIVEURI | Very Low      | Not Recommended | On android this is same as FASTFILEURI. Compression allows for faster processing but sacrifices a lot of accuracy. Best used if ocr images will always be extremely large with large text in them. |
| 4                 | BASE64        | Very High     | Not Recommended | Extremely memory intensive and thus not recommended

- **imgSrc**
The plugin accepts image uri or base64 data in **`imgSrc`** which is obtained from another plugin like cordova-plugin-document-scanner or cordova-plugin-camera.  This `imgSrc` is then used by the plugin and via the ML Kit libary, it detects the text on the image. The data required for OCR is initially downloaded when the app is first installed. 

> Example imgSrc for NORMFILEURI or FASTFILEURI as obtained from [camera plugin](https://github.com/apache/cordova-plugin-camera) or [scanner plugin](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner) :- file:///var/mobile/Containers/Data/Application/FF505EA5-F16E-4CBA-8F8B-76A219EDA407/tmp/cdv_photo_001.jpg

> Example imgSrc for NORMNATIVEURI or FASTNATIVEURI as obtained from [camera plugin](https://github.com/apache/cordova-plugin-camera). [scanner plugin](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner) doesn't return this :- assets-library://asset/asset.JPG?id=EFBA7BCD-3031-4646-9874-49368849749A&ext=JPG

- **successCallback**
The return value is sent to the **`successCallback`** callback function, in string format if no errors occured. [Sample object](#example-objects)  received from the successCallback can be found at the very bottom of this readme [Android Sample Object](#android-example-object)). The following image gives a better understanding of Blocks, Lines and Words as used in the return object.

![N|Solid](https://developers.google.com/vision/images/text-structure.png "Difference between blocks, lines and words")

- **errorCallback**
The **`errorCallback`** function returns `Scan Failed: Found no text to scan` if no text was detected on the image. It also return other messages based on the error conditions.

>*Note :- After install the OCR App using this plugin does not need an internet connection for Optical Character Recognition since all the required data is downloaded locally on install.*

You can do whatever you want with the string obtained from this plugin, for example:
- **Render the text** in an `<p>` tag.
- `<p id="pp">nothing yet. wait</p>` in html
- `var element = document.getElementById('pp');
element.innerHTML=recognizedText.blocks.blocktext;` in js

> *Note :- This plugin doesn't handle permissions as it only requires the URIs or Base64 data of images and thus expects the other plugins that provide it the URI or Base64 data to handle permissions.*

# Working Examples
Please use `cordova plugin add cordova-plugin-camera` or `cordova plugin add cordova-plugin-document-scanner` before using the following examples.

>*Note :- The cordova-plugin-mobile-ocr plugin will not automatically download either of these plugins as dependencies (This is because this plugin can be used as standalone plugin which can accept URIs or Base64 data through any method or plugin).*

**Using [cordova-plugin-camera](https://github.com/apache/cordova-plugin-camera)** 
```js 
navigator.camera.getPicture(onSuccess, onFail, { quality: 100, correctOrientation: true });

function onSuccess(imageData) {
      mltext.getText(onSuccess, onFail,{imgType : 0, imgSrc : imageData});
      // for imgType Use 0,1,2,3 or 4
      function onSuccess(recognizedText) {
            //var element = document.getElementById('pp');
            //element.innerHTML=recognizedText.blocks.blocktext;
            //Use above two lines to show recognizedText in html
            console.log(recognizedText);
            alert(recognizedText.blocks.blocktext);
      }
      function onFail(message) {
            alert('Failed because: ' + message);
      }
}
function onFail(message) {
      alert('Failed because: ' + message);
}

```

**Using [cordova-plugin-document-scanner](https://github.com/NeutrinosPlatform/cordova-plugin-document-scanner)** 
>*Note :- base64 and NATIVEURIs won't work with cordova-plugin-document-scanner plugin*
```js 
scan.scanDoc(successCallback, errorCallback, {sourceType : 1, fileName : "myfilename", quality : 1.0, returnBase64 : false}); 

function successCallback(imageURI) {
      mltext.getText(onSuccess, onFail,{imgSrc : imageData}); 
      // for imgType Use 0,2 // 1,3,4 won't work
      function onSuccess(recognizedText) {
            //var element = document.getElementById('pp');
            //element.innerHTML=recognizedText.lines.linetext;
            //Use above two lines to show recognizedText in html
            console.log(recognizedText);
            alert(recognizedText.lines.linetext);
      }
      function onFail(message) {
            alert('Failed because: ' + message);
      }
}
function errorCallback(message) {
      alert('Failed because: ' + message);
}
```

# More about us
Find out more or contact us directly here :- https://www.neutrinos.co/

Facebook :- https://www.facebook.com/Neutrinos.co/ <br/>
LinkedIn :- https://www.linkedin.com/company/25057297/ <br/>
Twitter :- https://twitter.com/Neutrinosco <br/>
Instagram :- https://www.instagram.com/neutrinos.co/

[![N|Solid](https://image4.owler.com/logo/neutrinos_owler_20171023_142541_original.jpg "Neutrinos")](https://www.neutrinos.co/) 

# Example Objects
The five properties text, languages, confidence, points and frame are obtained as arrays and are associated with each other using the index of the array.

>For example :- 
The text **linetext[0]** contains the languages **linelanguages[0]** and have a confidence of **lineconfidence[0]** with **linepoints[0]** and **lineframe [0]**. 

>Refer the examples to see how the points and frame are returned. Points hold four (x,y) point values that can be used to draw a box around each text. The Frame holds the origin x,y value, the height and the width of the rectangle that can be drawn around the text. The x,y value returned from the Frame property usually correspond to x1 and y4 of the Points property. The Points and Frame values can be used to obtain the placement of the text on the image

The basic structure of the object is as follows :- 

> **foundText** was added in plugin version 3.0.0 and above. In earlier plugin versions if image did not contain text the error callback was called. From 3.0.0 onwards all success callbacks will contain the `foundText` key with a boolean value. Letting the user know if a text was present in the image. if `foundText` is false, text was not found and hence the `blocks`, `lines`, `words` keys won't be returned

 - **foundText** - **boolean** value that is true if image contains text else false
 - **blocks**
   - **blocktext** - **Array** that contains each text block
   - **blockpoints** - **Array** of objects of four points each that represent a block drawn around the text
     - x1 - Key (Example to get x1 of the first text block :- recognizedText.blocks.blockpoints[0].x1)
     - y1 - Key
     - x2 - Key
     - y2 - Key
     - x3 - Key
     - y3 - Key
     - x4 - Key
     - y4 - Key
   - **blockframe** - **Array** of objects that contain origin point and size of the rectangle that holds text
     - x - Key (Example to get x from blockframe of the first text block :- recognizedText.blocks.blockframe[0].x)
     - y - Key
     - height - Key
     - width - Key
 - **lines**
   - **linetext** - **Array** that contains each text block
   - **linepoints** - **Array** of objects of four points each that represent a block drawn around the text
        - x1 - Key
        - y1 - Key
        - x2 - Key
        - y2 - Key
        - x3 - Key
        - y3 - Key
        - x4 - Key
        - y4 - Key
   - **lineframe** - **Array** of objects that contain origin point and size of the rectangle that holds text
     - x - Key
     - y - Key
     - height - Key
     - width - Key
 - **words**
   - **wordtext** - **Array** that contains each text block
   - **wordpoints** - **Array** of objects of four points each that represent a block drawn around the text
        - x1 - Key
        - y1 - Key
        - x2 - Key
        - y2 - Key
        - x3 - Key
        - y3 - Key
        - x4 - Key
        - y4 - Key
   - **wordframe** - **Array** of objects that contain origin point and size of the rectangle that holds text
     - x - Key
     - y - Key
     - height - Key
     - width - Key
# Example Object when no text in image
```json
{
  "foundText" : false
}
```
# Android Example Object
```json
{
  "foundText" : true,
  "blocks": {
    "blocktext": [
      "Home",
      "Ins",
      "PgUp",
      "PgDn",
      "Del"
    ],
    "blockpoints": [
      {
        "x1": 270,
        "y1": 346,
        "x2": 652,
        "y2": 346,
        "x3": 652,
        "y3": 468,
        "x4": 270,
        "y4": 468
      },
      {
        "x1": 913,
        "y1": 2459,
        "x2": 1215,
        "y2": 2459,
        "x3": 1215,
        "y3": 2627,
        "x4": 913,
        "y4": 2627
      },
      {
        "x1": 1497,
        "y1": 292,
        "x2": 1907,
        "y2": 292,
        "x3": 1907,
        "y3": 496,
        "x4": 1497,
        "y4": 496
      },
      {
        "x1": 1543,
        "y1": 1722,
        "x2": 1953,
        "y2": 1722,
        "x3": 1953,
        "y3": 1878,
        "x4": 1543,
        "y4": 1878
      },
      {
        "x1": 1659,
        "y1": 2451,
        "x2": 1900,
        "y2": 2451,
        "x3": 1900,
        "y3": 2585,
        "x4": 1659,
        "y4": 2585
      }
    ],
    "blockframe": [
      {
        "x": 270,
        "y": 468,
        "height": 122,
        "width": 382
      },
      {
        "x": 913,
        "y": 2627,
        "height": 168,
        "width": 302
      },
      {
        "x": 1497,
        "y": 496,
        "height": 204,
        "width": 410
      },
      {
        "x": 1543,
        "y": 1878,
        "height": 156,
        "width": 410
      },
      {
        "x": 1659,
        "y": 2585,
        "height": 134,
        "width": 241
      }
    ]
  },
  "lines": {
    "linetext": [
      "Home",
      "Ins",
      "PgUp",
      "PgDn",
      "Del"
    ],
    "linepoints": [
      {
        "x1": 270,
        "y1": 346,
        "x2": 652,
        "y2": 346,
        "x3": 652,
        "y3": 468,
        "x4": 270,
        "y4": 468
      },
      {
        "x1": 913,
        "y1": 2459,
        "x2": 1215,
        "y2": 2459,
        "x3": 1215,
        "y3": 2627,
        "x4": 913,
        "y4": 2627
      },
      {
        "x1": 1497,
        "y1": 292,
        "x2": 1907,
        "y2": 292,
        "x3": 1907,
        "y3": 496,
        "x4": 1497,
        "y4": 496
      },
      {
        "x1": 1543,
        "y1": 1722,
        "x2": 1953,
        "y2": 1722,
        "x3": 1953,
        "y3": 1878,
        "x4": 1543,
        "y4": 1878
      },
      {
        "x1": 1659,
        "y1": 2451,
        "x2": 1900,
        "y2": 2451,
        "x3": 1900,
        "y3": 2585,
        "x4": 1659,
        "y4": 2585
      }
    ],
    "lineframe": [
      {
        "x": 270,
        "y": 468,
        "height": 122,
        "width": 382
      },
      {
        "x": 913,
        "y": 2627,
        "height": 168,
        "width": 302
      },
      {
        "x": 1497,
        "y": 496,
        "height": 204,
        "width": 410
      },
      {
        "x": 1543,
        "y": 1878,
        "height": 156,
        "width": 410
      },
      {
        "x": 1659,
        "y": 2585,
        "height": 134,
        "width": 241
      }
    ]
  },
  "words": {
    "wordtext": [
      "Home",
      "Ins",
      "PgUp",
      "PgDn",
      "Del"
    ],
    "wordpoints": [
      {
        "x1": 270,
        "y1": 346,
        "x2": 652,
        "y2": 346,
        "x3": 652,
        "y3": 468,
        "x4": 270,
        "y4": 468
      },
      {
        "x1": 913,
        "y1": 2459,
        "x2": 1215,
        "y2": 2459,
        "x3": 1215,
        "y3": 2627,
        "x4": 913,
        "y4": 2627
      },
      {
        "x1": 1497,
        "y1": 292,
        "x2": 1907,
        "y2": 292,
        "x3": 1907,
        "y3": 496,
        "x4": 1497,
        "y4": 496
      },
      {
        "x1": 1543,
        "y1": 1722,
        "x2": 1953,
        "y2": 1722,
        "x3": 1953,
        "y3": 1878,
        "x4": 1543,
        "y4": 1878
      },
      {
        "x1": 1659,
        "y1": 2451,
        "x2": 1900,
        "y2": 2451,
        "x3": 1900,
        "y3": 2585,
        "x4": 1659,
        "y4": 2585
      }
    ],
    "wordframe": [
      {
        "x": 270,
        "y": 468,
        "height": 122,
        "width": 382
      },
      {
        "x": 913,
        "y": 2627,
        "height": 168,
        "width": 302
      },
      {
        "x": 1497,
        "y": 496,
        "height": 204,
        "width": 410
      },
      {
        "x": 1543,
        "y": 1878,
        "height": 156,
        "width": 410
      },
      {
        "x": 1659,
        "y": 2585,
        "height": 134,
        "width": 241
      }
    ]
  }
}
```
