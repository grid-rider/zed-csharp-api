# Tutorial 6 : Object Detection - image

This tutorial shows how to use the object detection module.
It will draw bounding box around detected objects and display the image in a OpengL window.
We assume that you have read the tutorial 1 and successfully opened your ZED.

## Getting started

- First, download the latest version of the ZED SDK on [stereolabs.com](https://www.stereolabs.com).
- For more information, read the ZED [API documentation](https://www.stereolabs.com/developers/documentation/API/).

# Code overview
## Create a camera

As with previous tutorial, we create, configure and open the ZED. Here we show how to set a resolution and a framerate. 

If you want to use the SVO input, you need to specify both the Input type and the SVO file path  : 

```
 init_params.inputType = sl.INPUT_TYPE.INPUT_TYPE_SVO;
 init_params.pathSVO =  "D:/mySVOfile.svo";
```

## Enable object detection

Once the camera is opened, we must enable object detection module to retrieve the detected objects.
If we want object tracking, we also need to enable position tracking in order to be able to track objects with a moving camera.

```
Quaternion quat = Quaternion.Identity;
Vector3 vec = Vector3.Zero;

// Enable Tracking
err = zedCamera.EnableTracking(ref quat, ref vec);
if (err != ERROR_CODE.SUCCESS)
    Environment.Exit(-1);


// Enable Object Detection
dll_ObjectDetectionParameters object_detection_parameters = new dll_ObjectDetectionParameters();
object_detection_parameters.detectionModel = sl.DETECTION_MODEL.MULTI_CLASS_BOX;
object_detection_parameters.enableObjectTracking = true;
err = zedCamera.EnableObjectsDetection(ref object_detection_parameters);
if (err != ERROR_CODE.SUCCESS)
    Environment.Exit(-1);
```

## Capture data

Now that the ZED is opened, we can now capture the images coming from it.
We create a loop that capture images continously

To capture an image and process it, you need to call `zedCamera.grab()` function.
Each time you want a new image, you need to call this function. if grab() returns ERROR_CODE.SUCCESS, a new image has been capture and is now available. Otherwise, you can check the status of grab() which will tell you if there is no new frame available (depending on the framerate of the camera) or if something wrong happened.

Once grab has been done, you can get all the data provided with the ZED SDK. 

In this tutorial, we want to retrieve objects from the object detection module.

```
// Retrieve Objects from Object detection
err  = zedCamera.RetrieveObjectsDetectionData(ref obj_runtime_parameters, ref object_frame);
```