# GPU-Accelerated Effect Build Instructions

The AE GPU SDK was added in version 16.0, along with an SDK_Invert_ProcAmp sample effect to demonstrate the APIs.

To build the SDK_Invert_ProcAmp sample, you'll need to install some dependencies first.

## Mac Setup Instructions

The SDK_Invert_ProcAmp plugin uses Boost to process GPU kernel files, so you need to install Boost on your machine. You can install Boost through homebrew, or direct download from boost.org.

1. Install Boost through homebrew or download it directly from [boost.org](https://www.boost.org/releases/latest/).
    - Boost provides the most up-to-date installation walkthrough here: [Getting Started with Boost](https://www.boost.org/doc/user-guide/getting-started.html)
2. Once Boost is installed, obtain the installation path.
    - If installed via homebrew, it will be something like: `/usr/local/Cellar/boost/1.67.0_1/include/boost`
    - If installed from the direct download, something like: `/usr/local/include/boost`
3. Open the SDK_Invert_ProcAmp project in Xcode and go to Settings -> Locations -> Custom Paths
4. Add this entry:
    - Name: `BOOST_BASE_PATH`
    - Display Name: `BOOST`
    - Path: `[Your Boost installation path]`
5. If you see python errors when building, make sure you have python installed for bash (not for zsh). If you have installed python3 for bash but still seeing "python command not found" error, go to Project Settings -> Build Rules and try changing the "python" keyword to "python3"

## Windows Setup Instructions

1. Install Boost from boost.org (latest instructions are [here](https://www.boost.org/doc/user-guide/getting-started.html))
    - Unzip the boost package and run the included bootstrap.bat
    - Then run `.\b2` to build Boost
    - Then run `.\b2 install --prefix=C:\Boost`
2. Install the CUDA SDK from [https://developer.nvidia.com/cuda-downloads](https://developer.nvidia.com/cuda-downloads).
    - Use the CUDA version that matches your target architecture and AE build. AE 27.0.0 uses CUDA 12.8 for x64 and CUDA 13.4 for ARM64.
    - For historical reference, AE 25.4 used CUDA 12.8.
3. Install the latest DirectX Compiler from [https://github.com/microsoft/DirectXShaderCompiler/releases](https://github.com/microsoft/DirectXShaderCompiler/releases)
4. Setup the following system environment variables:
    - CUDA_SDK_BASE_PATH: `[CUDA installation path]`
        - (example: `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v12.8` for x64, or `C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v13.4` for ARM64)
    - Boost_BASE_PATH: `[Boost installation path]`
        - (example: `C:\Boost\include\boost-1_88`)
    - DXC_SDK_BASE_PATH: `[DXC installation path]`
        - (example: `C:\dxc_2023_08_14`)

### Deployment instructions (DirectX):
The DirectXAssets folder generated alongside the binary also has to
be copied for the effect to work. The assets are loaded by the
application at runtime.

PF_OutFlag2_SUPPORTS_DIRECTX_RENDERING has to be set as a
part of Global Flags for DirectX rendering and the corresponding
value has to be updated in the PiPL resource file. The effect will fall
back to the CPU implementation if the flag is missing.

### CUDA Runtime API vs. Driver API:
1. **Utilize CUDA Driver API**

    For best compatibility, we highly recommend utilizing only the CUDA Driver
    API. Unlike the runtime API, the driver API will be compatible with future
    drivers. Please note that the CUDA Runtime API is built to handle/automate
    some of the housekeeping that is exposed and needs to be handled in the
    Driver APIs, so there might be some new steps and code you would need to
    implement to migrate from the Runtime API to the Driver API.

2. **Alternative 1: Statically Link to CUDA Runtime**

    If the CUDA Runtime API is needed then we recommend statically linking the
    CUDA Runtime, cudart_static.lib. This will work with future driver versions.

3. **Alternative 2: Dynamically Link to CUDA Runtime**

    This also works but may be prone to compatibility issues. A compatible CUDA
    Runtime DLL needs to be available on users' systems to work with future
    drivers. Currently After Effects ships a copy of the CUDA Runtime DLL
    corresponding to our recommended CUDA SDK version. This may change in
    future. If you must dynamically link to the CUDA Runtime then we
    recommend shipping a copy of the CUDA Runtime DLL with your plugin and
    use dlopen/LoadLibrary to explicitly load the desired runtimes. For more
    details, see the CUDA Compatibility section of NVIDIA's GPU Management
    and Deployment guide: [https://docs.nvidia.com/deploy/cuda-compatibility/](https://docs.nvidia.com/deploy/cuda-compatibility/)
