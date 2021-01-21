# Win32droid - box86 & Wine based Win32 emulator for Android
----
## Before we start with the explanations, I want to give a huge shout-out to ptitSeb (https://github.com/ptitSeb) and the Wine developers, who developed the x86 Linux Userspace Emulator box86 and the Windows compatibility layer respectively. Without the constant dedication to box86 by ptitSeb and his efforts to get Wine x86 to run on box86, aswell as the constant improvements to Wine by the Wine devs, this project would have never been possible.
## FAQ:
### What is Win32droid?
Win32droid is an experimental software to emulate Windows x86 games on **_rooted_** Android ARM/ARM64 devices, based on the OpenSource projects Wine and box86

### How does it work?
Win32droid uses BusyBox for Android to chroot into an Ubuntu Focal environment which has box86, Wine x86 and Mesa LLVMpipe preinstalled. 
Afterwards, after having chrooted into the environment, the Win32droid app executes a customized shell command to launch box86 with Wine on your device while simultaneously launching XServer XSDL as the X Server backend for Wine to render it's screen on.
box86 is used as the x86 emulation backend in order to be able to use Wine x86 and Wine is used as a translation layer for Windows software on Linux. Graphics are currently processed
via the Mesa LLVMpipe software rendering OpenGL backend combined with Wine's D3D wrappers.

### In what state is Win32droid at the moment?
Currently, some Windows x86 programs and games should boot or run. 
I haven't tested much software on it yet but during my testing Cinebench R11.5 and Silent Hill 2 could boot and run on the emulator. 
To get a better idea of what might be able to run or boot, please refer to the box86 and Wine compatibility lists.

### How fast is the emulator?
Don't expect this to be fast. While the box86 dynamic recompiler is quite decent, it doesn't seem to be as fast as ExaGear but this might change in the future. 
The main bottleneck at the moment is the graphics backend though. 
Currently only software rendering via Mesa LLVMpipe is supported, so games, especially 3D ones will run **_SLOW_**. 
Don't expect to run GTA IV or something similar on this emulator.<br/>
As a reference you can take the following results that I got on a Snapdragon 865 device:<br/>
* Silent Hill 2, safe settings (minimum graphics, 640x480) runs at a _stunning_ 8-13 FPS in the intro scene (restroom scene & outside)<br/>
* Cinebench R11.5 32 Bit Mode, Multicore CPU benchmark: Score = 0.88

### Will 3D Acceleration be supported in the future?
I would like to implement it, but at the moment I can't do much about it. I am a simple dev, who has recently gotten into Linux and Java coding. I don't know how to code in C/C++ (yet) and I don't have any graphics API knowledge (OpenGL, Vulkan, ...),
so don't keep your expectations too high. I am currently also busy with my university studies, so I can't dedicate much time (if at all) to this project and even less into acquiring and mastering a new coding language and graphics API. <br/>
The only viable possibility that I know of to **_maybe_** get 3D hardware acceleration would be to compile and install Mesa DRM/DRI in order to be able to use the GPU directly, however not a lot of devices currently support DRM. Although my device (Snapdragon 865 SoC) has DRM, it requires Mesa to be compiled for Freedreno with a KGSL DRM backend, since Qualcomm currently uses a custom DRM implementation on Android, however the Mesa compilation for the KGSL target currently fails, so I have no way of testing it. Another idea would be to get some kind of GL-over-the-network software to run but at the moment the projects that can accomplish this are in a really bad state and/or abandoned, so it won't be possible to use this method in the near future.

### ETA for xyz WEN?
No, there will be no ETAs whatsoever. Like many other devs already say/said, ETAs and people asking every second for an "ETA WEN?" have been a plague and only slowed down development or even
made some devs quit if it was too toxic. Please understand that we also have lives, a family, jobs/studies, etc. We aren't some aliens sitting 24/7 in front of the PC and spitting out code
as everyone pleases and commands.

----
## Known Issues:
* Software requiring DirectX 9 (and probably also DX10/11) currently crashes / doesn't boot at all.
* Software requiring OpenGL crashes or only displays a black screen. A temporary workaround for this can be achieved by downloading Mesa for Windows (32-Bit) from Federico Dossena (https://fdossena.com/?p=mesa/index.frag), pasting the downloaded opengl32.dll file into the directory where your .exe requiring OpenGL is located and then overwriting opengl32.dll in winecfg as "native then builtin". Please note that this will be even slower than natively running Mesa LLVMpipe since now OpenGL is being software rendered **_through_** box86 which means that you basically do software rendering (slow) ontop of an emulator (a lot slower).
* Mesa LLVMpipe seems to have some problems with the Wine D3D wrapper, causing random crashes/freezes (e.g.: Silent Hill 2 crashes after a few seconds to a few minutes max.).
* Running the Wine Desktop with another resolution than the game's default full screen resolution might cause crashes.
* Passing resolution arguments to XServer XSDL isn't supported, so setting up a custom resolution profile in XServer XSDL is required before changing the Wine Desktop resolution, otherwise Wine just renders a small window.
* Running XServer XSDL and Wine at different resolutions will cause problems, so please **make sure to run Wine and XServerXSDL at the _same_ resolution**.
* Some installers don't work, so it is **_recommended_** to provide your program in a **_portable format_** if possible (e.g.: portable .exe, game preinstalled in folder format).

----
## Goals & future plans:
### Short-term goals
* Fix bugs of course :)
* Try to clean up the rootfs OBB, to shrink its size and consume less phone storage
* Implement Mesa GLES2 LLVMpipe & GL4ES as an alternative for Desktop GL (2.1) support
* Try to implement Mesa DRM KGSL as soon as it is fixed to attempt to get GPU acceleration to work on Qualcomm devices with DRM

### Mid-term goals
* Implement Proot in order to make the app compatible with non-rooted devices
* Provide a better UI and/or rewrite it and create a proper options menu
* Try to get the LOAX-Server project to work with Win32droid, to provide hardware acceleration via OpenGL ES 2 and combine it with a wrapper for Desktop OpenGL (like GL4ES, Regal or virgl-vtest as GL to GLES translators)

### Long-term goals
* Learn C/C++ and OpenGL to be able to port virgl or a similar project to Android in order to provide a solid, hardware accelerated OpenGL backend for games.

----
## Setup:
* Download the ubuntu.focal.armhf.rootfs.obb file from the releases tab and put it in the /storage/emulated/0/Android/obb/com.grima04.win32droid directory. Create the directory if necessary.
* Download and the application APK from the newest release tab and install it
* Launch the app and wait for the initial setup to complete (You should be able to see the extraction process in the Terminal text field at the bottom)
* When everything is set, press the WINE button to launch Wine. To enable OpenGL, enable "Use software rendering" before pressing the WINE button
* To monitor CPU usage and framerates in a graph, enable "Use Gallium HUD"
* To launch the Wine configuration or registry edit window, press WINECFG or REGEDIT respectively

----
## Gallery:
* Silent Hill 2
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Title_Screen.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Intro_Scene_Mirror.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Urinal_Camera.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Mirror_2.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Outside_1.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Outside_2.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Car.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Map.jpg?raw=true)
![](https://github.com/Grima04/Win32droid/blob/master/Gallery/Silent_Hill_2_Toluca_Lake_Sign.jpg?raw=true)
