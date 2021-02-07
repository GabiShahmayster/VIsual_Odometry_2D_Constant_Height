# VIsual_Odometry_2D_Constant_Height

This project estimates the 2D motion (translation and rotation) of a camera, which is constrained to move on a plane (upper plane, for example).
The plane of the camera's motion is assumed to be parallel to another plane (bottom plane, for example), which is observed by the camera.
As an illustration, a monocular camera could be moving on the upper plane and observing an eqidistant lower plane.
It is assumed that the distance between the plances (i.e height of the upper plane, above the lower plane) is known.
It is assumed that the lower plane contains a rich-enough and distinct-enough texture to allow detection and tracking locally distinct visual features (such as ORB).
It is assumed that the intrinsic parameters of the robot's camera have been calibrated (using Zhang's method or any other calibration method).

A visual odometry solution has been implemented, which could be treated as a naive VSLAM solution, with a tracking/mapping/localization stages:
1. (Tracking) Visual features are tracked between consecutive frames (frames N+1 and N, i.e current and previous frame)
2. (Mapping) Each visual feature (2D keypoint) is 3D localized (analytically) on the "lower plane", by inverting the perspective projection and using the known distance between the planes.
This mapping is performed using the keypoints from the previous frame, i.e it represents the map as seen by the camera at frame N. The camera pose 
w.r.t to this map is [R|t]=[I|0].
3. (Localization) The set of localized 3D features (map points) is used to estimate the extrinsic parameters (i.e translation and rotation) of the camera
w.r.t to the map generated by the previous step, i.e localize the camera, at frame N+1, w.r.t to the map. The camera pose is the relative motion between frames N+1 
and N. The estimation of camera pose is performed using the PnP, with Ransac outlier detection.
4. The absolute motion of the camera, w.r.t frame 1, is tracked by accumulating the relative motion between frames.

The solution has been tested using several frames generated by the open-source 3D graphics software Blender (https://www.blender.org).
Several motion types have been tested:
1. Linear translation
2. Angular rotation
3. Combined linear translation and angular rotation

Several texture types have been tested, with varying degrees of success:
1. Rich-textured mosaic (Success)
2. Hardwood floor (Fail)

An attempt was made to introduce a "self-tuning" capability, to cope with low-featured scenes, by employing the following strategy:
1. Attempt to track 1000 ORB features
2. if 1 fails -> Attempt to track 500 ORB features (assume that it's a low-featured scene, so that stage 1 was looking for too many features)
3. if 2 failes -> Attempt to use Lucas-Kanade sparse optical-flow, instead of ORB features (assume that it's a very low-feature or repetetive scene)

![Image description](https://github.com/GabiShahmayster/VIsual_Odometry_2D_Constant_Height/blob/master/tracking_example.png)
