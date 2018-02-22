Driver_NULL 模式是SteamVR用于编写其他设备的示例程序，同时也可以使用它来在没有安装VR设备的PC上调试运行我们的程序。以下是streamVR 配置说明：

[https://developer.valvesoftware.com/wiki/SteamVR/steamvr.vrsettings](https://developer.valvesoftware.com/wiki/SteamVR/steamvr.vrsettings)

steamvr.vrsettings is a json file in the format below. To enable a key remove the // in front of the particular line. Usually only low-level programmers need to set any of these keys. This file lives in the "config" directory under Steam.

		{
		   "jsonid" : "vrsettings",
		   "steamvr" : {
				// "ipd": 0.0635,
				// "forcedDriver": "null",
				// "forcedHmd": "",
				// "requireHmd": true,
				// "displayDebug": false,
				// "enableDistortion": true,
				// "displayDebugX": 0,
				// "displayDebugY": 0,	
				// "sendSystemButtonToAllApps": false,
				// "loglevel": 3,
				// "background": "backgrounds/image_%d.png", (now requires fully qualified path)
				// "backgroundCameraHeight": 1.6,
				// "backgroundDomeRadius": 10.0, (0.0 == Infinite)
				// "directMode": true,
				// "usingSpeakers": true, (only works in some Unity games current)
				// "speakersForwardYawOffsetDegrees": 90.0,
				// "renderTargetMultiplier": 1.0
				// ^ Make sure your last entry does not include a trailing comma (use jslint when in doubt to verify format).
			},
		   "driver_lighthouse": {
				// "disableimu": false,
				// "usedisambiguation": "tdm",
				// "disambiguationdebug": 0,
				// "primarybasestation": 0,
				// "lighthousename": "",
				// "maxincidenceangledegrees": 60.0,
				// "uselighthousedirect": true,
				// "dbhistory": false
			},
		   "driver_null" : {
				// drivers are searched in alphabetical order, so you may need steamvr.forcedDriver="null" as well
				// "enable": true,
				// "id": "Null Driver",
				// "serialNumber": "Null 4711",
				// "modelNumber": "Null Model Number",
				// "windowX": 100,
				// "windowY": 100,
				// "windowWidth": 1920,
				// "windowHeight": 1080,
				// "renderWidth": 1344,
				// "renderHeight": 1512,
				// "secondsFromVsyncToPhotons": 0.1,
				// "displayFrequency": 90
		   },
		   "version" : "1"
		}

SteamVR Driver开发文档：
[https://github.com/ValveSoftware/openvr/wiki/Driver-Documentation]()

设置Driver_NULL 模式
启用本地无VR设备运行程序的steamvr.setting 配置：

	{
	    "steamvr": {
	        "background": "D:\\Program Files (x86)\\ViveSetup\\SteamVR\\resources\\backgrounds\\viveNight.png",
			"activateMultipleDrivers" : true,
		   "forcedDriver": "null"
	    },
		"driver_null" : {
			 "enable": true,
			 "id": "Null Driver",
			 "serialNumber": "Null 4711",
			 "modelNumber": "Null Model Number",
			 "windowX": 100,
			 "windowY": 100,
			 "windowWidth": 1920,
			 "windowHeight": 1080,
			 "renderWidth": 1344,
			 "renderHeight": 1512,
			 "secondsFromVsyncToPhotons": 0.1,
			 "displayFrequency": 90
	   }
		
	}


透过使用以上参数配置，就可以模拟vr设备，运行vr程序。

参考：

UE论坛话题：No steamvr tracking without hmd?
[https://answers.unrealengine.com/questions/704671/no-steamvr-tracking-without-hmd.html]()

不花冤枉钱!免VR头显使用Vive追踪器教程: 
[http://www.sohu.com/a/131260636_248541]()