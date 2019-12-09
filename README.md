
### Requirements

* Windows or Linux
* **CMake >= 3.8** for modern CUDA support: https://cmake.org/download/
* **CUDA 10.0**: https://developer.nvidia.com/cuda-toolkit-archive (on Linux do [Post-installation Actions](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#post-installation-actions))
* **OpenCV >= 2.4**: use your preferred package manager (brew, apt), build from source using [vcpkg](https://github.com/Microsoft/vcpkg) or download from [OpenCV official site](https://opencv.org/releases.html) (on Windows set system variable `OpenCV_DIR` = `C:\opencv\build` - where are the `include` and `x64` folders [image](https://user-images.githubusercontent.com/4096485/53249516-5130f480-36c9-11e9-8238-a6e82e48c6f2.png))
* **cuDNN >= 7.0 for CUDA 10.0** https://developer.nvidia.com/rdp/cudnn-archive (on **Linux** copy `cudnn.h`,`libcudnn.so`... as desribed here https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installlinux-tar , on **Windows** copy `cudnn.h`,`cudnn64_7.dll`, `cudnn64_7.lib` as desribed here https://docs.nvidia.com/deeplearning/sdk/cudnn-install/index.html#installwindows )
* **GPU with CC >= 3.0**: https://en.wikipedia.org/wiki/CUDA#GPUs_supported
* on Linux **GCC or Clang**, on Windows **MSVC 2015/2017/2019** https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community


### How to compile on Linux (using `cmake`)

The `CMakeLists.txt` will attempt to find installed optional dependencies like
CUDA, cudnn, ZED and build against those. It will also create a shared object
library file to use `darknet` for code development.

Do inside the cloned repository:

```
mkdir build-release
cd build-release
cmake ..
make
make install
```

### How to compile on Linux (using `make`)

Just do `make` in the darknet directory.
Before make, you can set such options in the `Makefile`: [link](https://github.com/AlexeyAB/darknet/blob/9c1b9a2cf6363546c152251be578a21f3c3caec6/Makefile#L1)

* `GPU=1` to build with CUDA to accelerate by using GPU (CUDA should be in `/usr/local/cuda`)
* `CUDNN=1` to build with cuDNN v5-v7 to accelerate training by using GPU (cuDNN should be in `/usr/local/cudnn`)
* `CUDNN_HALF=1` to build for Tensor Cores (on Titan V / Tesla V100 / DGX-2 and later) speedup Detection 3x, Training 2x
* `OPENCV=1` to build with OpenCV 4.x/3.x/2.4.x - allows to detect on video files and video streams from network cameras or web-cams
* `DEBUG=1` to bould debug version of Yolo
* `OPENMP=1` to build with OpenMP support to accelerate Yolo by using multi-core CPU
* `LIBSO=1` to build a library `darknet.so` and binary runable file `uselib` that uses this library. Or you can try to run so `LD_LIBRARY_PATH=./:$LD_LIBRARY_PATH ./uselib test.mp4` How to use this SO-library from your own code - you can look at C++ example: https://github.com/AlexeyAB/darknet/blob/master/src/yolo_console_dll.cpp
    or use in such a way: `LD_LIBRARY_PATH=./:$LD_LIBRARY_PATH ./uselib data/coco.names cfg/yolov3.cfg yolov3.weights test.mp4`
* `ZED_CAMERA=1` to build a library with ZED-3D-camera support (should be ZED SDK installed), then run
    `LD_LIBRARY_PATH=./:$LD_LIBRARY_PATH ./uselib data/coco.names cfg/yolov3.cfg yolov3.weights zed_camera`

To run Darknet on Linux use examples from this article, just use `./darknet` instead of `darknet.exe`, i.e. use this command: `./darknet detector test ./cfg/coco.data ./cfg/yolov3.cfg ./yolov3.weights`

### How to compile on Windows (using `CMake-GUI`)

This is the recommended approach to build Darknet on Windows if you have already
installed Visual Studio 2015/2017/2019, CUDA > 10.0, cuDNN > 7.0, and
OpenCV > 2.4.

Use `CMake-GUI` as shown here on this [**IMAGE**](https://user-images.githubusercontent.com/4096485/55107892-6becf380-50e3-11e9-9a0a-556a943c429a.png):

1. Configure
2. Optional platform for generator (Set: x64)
3. Finish
4. Generate
5. Open Project
6. Set: x64 & Release
7. Build
8. Build solution

### How to compile on Windows (using `vcpkg`)

If you have already installed Visual Studio 2015/2017/2019, CUDA > 10.0,
cuDNN > 7.0, OpenCV > 2.4, then to compile Darknet it is recommended to use
[CMake-GUI](#how-to-compile-on-windows-using-cmake-gui).

Otherwise, follow these steps:

1. Install or update Visual Studio to at least version 2017, making sure to have it fully patched (run again the installer if not sure to automatically update to latest version). If you need to install from scratch, download VS from here: [Visual Studio Community](http://visualstudio.com)

2. Install CUDA and cuDNN

3. Install `git` and `cmake`. Make sure they are on the Path at least for the current account

4. Install [vcpkg](https://github.com/Microsoft/vcpkg) and try to install a test library to make sure everything is working, for example `vcpkg install opengl`

5. Define an environment variables, `VCPKG_ROOT`, pointing to the install path of `vcpkg`

6. Define another environment variable, with name `VCPKG_DEFAULT_TRIPLET` and value `x64-windows`

7. Open Powershell and type these commands:

```PowerShell
PS \>                  cd $env:VCPKG_ROOT
PS Code\vcpkg>         .\vcpkg install pthreads opencv[ffmpeg] #replace with opencv[cuda,ffmpeg] in case you want to use cuda-accelerated openCV
```

8.  Open Powershell, go to the `darknet` folder and build with the command `.\build.ps1`. If you want to use Visual Studio, you will find two custom solutions created for you by CMake after the build, one in `build_win_debug` and the other in `build_win_release`, containing all the appropriate config flags for your system.

### How to compile on Windows (legacy way)

1. If you have **CUDA 10.0, cuDNN 7.4 and OpenCV 3.x** (with paths: `C:\opencv_3.0\opencv\build\include` & `C:\opencv_3.0\opencv\build\x64\vc14\lib`), then open `build\darknet\darknet.sln`, set **x64** and **Release** https://hsto.org/webt/uh/fk/-e/uhfk-eb0q-hwd9hsxhrikbokd6u.jpeg and do the: Build -> Build darknet. Also add Windows system variable `CUDNN` with path to CUDNN: https://user-images.githubusercontent.com/4096485/53249764-019ef880-36ca-11e9-8ffe-d9cf47e7e462.jpg

    1.1. Find files `opencv_world320.dll` and `opencv_ffmpeg320_64.dll` (or `opencv_world340.dll` and `opencv_ffmpeg340_64.dll`) in `C:\opencv_3.0\opencv\build\x64\vc14\bin` and put it near with `darknet.exe`
    
    1.2 Check that there are `bin` and `include` folders in the `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0` if aren't, then copy them to this folder from the path where is CUDA installed
    
    1.3. To install CUDNN (speedup neural network), do the following:
      
    * download and install **cuDNN v7.4.1 for CUDA 10.0**: https://developer.nvidia.com/rdp/cudnn-archive
      
    * add Windows system variable `CUDNN` with path to CUDNN: https://user-images.githubusercontent.com/4096485/53249764-019ef880-36ca-11e9-8ffe-d9cf47e7e462.jpg
    
    * copy file `cudnn64_7.dll` to the folder `\build\darknet\x64` near with `darknet.exe`
    
    1.4. If you want to build **without CUDNN** then: open `\darknet.sln` -> (right click on project) -> properties  -> C/C++ -> Preprocessor -> Preprocessor Definitions, and remove this: `CUDNN;`

2. If you have other version of **CUDA (not 10.0)** then open `build\darknet\darknet.vcxproj` by using Notepad, find 2 places with "CUDA 10.0" and change it to your CUDA-version. Then open `\darknet.sln` -> (right click on project) -> properties  -> CUDA C/C++ -> Device and remove there `;compute_75,sm_75`. Then do step 1

3. If you **don't have GPU**, but have **OpenCV 3.0** (with paths: `C:\opencv_3.0\opencv\build\include` & `C:\opencv_3.0\opencv\build\x64\vc14\lib`), then open `build\darknet\darknet_no_gpu.sln`, set **x64** and **Release**, and do the: Build -> Build darknet_no_gpu

4. If you have **OpenCV 2.4.13** instead of 3.0 then you should change paths after `\darknet.sln` is opened

    4.1 (right click on project) -> properties  -> C/C++ -> General -> Additional Include Directories:  `C:\opencv_2.4.13\opencv\build\include`
  
    4.2 (right click on project) -> properties  -> Linker -> General -> Additional Library Directories: `C:\opencv_2.4.13\opencv\build\x64\vc14\lib`
    
5. If you have GPU with Tensor Cores (nVidia Titan V / Tesla V100 / DGX-2 and later) speedup Detection 3x, Training 2x:
    `\darknet.sln` -> (right click on project) -> properties -> C/C++ -> Preprocessor -> Preprocessor Definitions, and add here: `CUDNN_HALF;`
    
    **Note:** CUDA must be installed only after Visual Studio has been installed.

