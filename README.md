# Usage

1. Follow the [link](https://github.com/microsoft/vcpkg#quick-start-windows) to install `vcpkg` and integrate install with `Visual Studio`

2. Open the project with **Visual Studio 2019 version 16.8 or higher**

3. If anything **go wrong**, then do it yourself:

    * Open the [Store Link](https://www.microsoft.com/store/productId/9MZ1SNWT0N5D) to install `Powershell Core`

    * Start the `Powershell Core` and use `cmake` to auto install dependencies:

        > PS> cd ./grpcsample

        > PS> mkdir build

        > PS> cd build

        > PS> cmake ..  -T host=x86 -A win32 -DCMAKE_TOOLCHAIN_FILE=[path/to/vcpkg]/scripts/buildsystems/vcpkg.cmake
       
    * Or start the `Powershell Core` and use `vcpkg` to manualy install dependencies:

        > PS> cd ./grpcsample

        > PS> [path/to/vcpkg]/vcpkg.exe install --feature-flagsmanifests
    