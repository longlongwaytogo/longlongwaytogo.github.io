
---
layout: post
title: OpenVR的Pose获取
categories: [VR]
description: OpenVR的Pose获取
keywords: VR,OpenVR, SteamVR 
---


 OpenVR 下，获取设备位置的方法有两个：WaitGetPoses 和 GetDeviceToAbsoluteTrackingPose。
WaitGetPoses： 会阻塞程序运行，直到底层获取到新的设备姿态位置才可以返回。
GetDeviceToAbsoluteTrackingPose： 则时通过传递预计算设备的位置，来预测新的设备位置。

两个函数都可以获取到设备位置，但作用不同。WaitGetPoses：不仅获取设备位置，同时也负责同步、刷新等工作，如果不调用该函数，即使submit了左右眼的纹理数据，Vive也无法现实提交的数据。而GetDeviceToAbsoluteTrackingPose，是通过传递时间参数，进行预计算，当前设备的预测位置，这样更能对当前的渲染系统提供准确的设备信息。

- **WaitGetPoses** :更新设备的姿态信息
		
		/** Scene applications should call this function to get poses to render with (and optionally poses predicted an additional frame out to use for gameplay).
		* This function will block until "running start" milliseconds before the start of the frame, and should be called at the last moment before needing to
		* start rendering.
		*
		* Return codes:
		*	- IsNotSceneApplication (make sure to call VR_Init with VRApplicaiton_Scene)
		*	- DoNotHaveFocus (some other app has taken focus - this will throttle the call to 10hz to reduce the impact on that app)
		*/
		virtual EVRCompositorError WaitGetPoses( VR_ARRAY_COUNT(unRenderPoseArrayCount) TrackedDevicePose_t* pRenderPoseArray, uint32_t unRenderPoseArrayCount,
		VR_ARRAY_COUNT(unGamePoseArrayCount) TrackedDevicePose_t* pGamePoseArray, uint32_t unGamePoseArrayCount ) = 0;


- **GetDeviceToAbsoluteTrackingPose**

		/** The pose that the tracker thinks that the HMD will be in at the specified number of seconds into the 
			* future. Pass 0 to get the state at the instant the method is called. Most of the time the application should
			* calculate the time until the photons will be emitted from the display and pass that time into the method.
			*
			* This is roughly analogous to the inverse of the view matrix in most applications, though 
			* many games will need to do some additional rotation or translation on top of the rotation
			* and translation provided by the head pose.
			*
			* For devices where bPoseIsValid is true the application can use the pose to position the device
			* in question. The provided array can be any size up to k_unMaxTrackedDeviceCount. 
			*
			* Seated experiences should call this method with TrackingUniverseSeated and receive poses relative
			* to the seated zero pose. Standing experiences should call this method with TrackingUniverseStanding 
			* and receive poses relative to the Chaperone Play Area. TrackingUniverseRawAndUncalibrated should 
			* probably not be used unless the application is the Chaperone calibration tool itself, but will provide
			* poses relative to the hardware-specific coordinate system in the driver.
			*/
			virtual void GetDeviceToAbsoluteTrackingPose( ETrackingUniverseOrigin eOrigin, float fPredictedSecondsToPhotonsFromNow, VR_ARRAY_COUNT(unTrackedDevicePoseArrayCount) TrackedDevicePose_t *pTrackedDevicePoseArray, uint32_t unTrackedDevicePoseArrayCount ) = 0;


[https://github.com/ValveSoftware/openvr/wiki/IVRSystem::GetDeviceToAbsoluteTrackingPose]()
[https://www.reddit.com/r/oculus/comments/3eg5q6/openvr_vs_oculus_sdk_part_2_latency]()