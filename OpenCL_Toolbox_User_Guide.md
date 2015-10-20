## Notes ##
All of the code provided here is made available to the public via the MIT License. Pretty much, you can use or modify the code to do whatever you want (commercially, non-commercially). You don't even have to pay royalties (unless you really want to), or publish your source code, or even give me credit (though, it would be uber-cool if you did mention my name in the credits as it would inspire me to contribute more work for free in the future). Anyway, regardless, the goal of this project is to speed up the research process and get more people up and running with using OpenCL.

PLEASE REFER TO THE COMMENTS IN THE SOURCE FOR LATEST DOCUMENTATION. This page will be updated slowly..

## Overview ##

The OpenCL toolbox is broken up into two components:
  * the raw interface (mex component): _openclcmd_
  * the matlab class files

The raw interface simply acts as the gateway to all of the available OpenCL functionality. It is simply a mex file that accepts commands to it. To enable additional OpenCL functionality, changes will have to be made to this component. The source files related to this component is in src/openclcmd.cpp

The matlab class files are wrappers that simplify the calling convention for the OpenCL mex files. These files are convenience files that make it easier and somewhat fun to call OpenCL files. Currently, only the .CL files have to be written. In the future, a library of .CL files will be included and the simple matlab operators +, -, =, etc. will be overloaded to use the OpenCL versions.

For the majority of those who are interested in using this toolbox file as is, you can ignore the documentation on _openclcmd_ and solely use the matlab class files.

## _opencl_ ##

_opencl_ class creates a basic interface to the OpenCL device and platform. This class is responsible for setting up the device/platform to use, for adding and building the OpenCL source files, and for forcing a "wait" to occur to allow all operations pending on the GPGPU to finish.

**Note**: You may only create one instance of this class. Multiple instances of this class share the same device state. So, don't attempt to utilize multiple instances of this class. It doesn't provide any value. All cleanup and device release calls are handled automatically when you quit MATLAB or call "clear all".

Please refer to the member functions for additional details:

  * opencl.opencl: constructor
  * opencl.initialize: set OpenCL platform and device
  * opencl.addfile: add OpenCL (.cl) source file to send to GPGPU
  * opencl.build: compile sources added by addfile and send binary to GPGPU
  * opencl.wait: wait for all operations to finish on the GPGPU

Example usage:
```
ocl = opencl();  % Fetch a list of available platforms and devices

disp(ocl);  % Inspect the platform and device you want to use
            % All platforms available are in the member attribute
            % .platforms )

disp(ocl.platforms(1) );

% Within each platform, there there is a list of devices associated with
% the platform. You can view this in the .devices attribute for each
% platform

disp(ocl.platforms(1).devices);
disp(ocl.platforms(1).devices(1));

% Assuming we want to pick platform 1 and device 1 (as viewed above):
platform = 1; 
device = 1;
ocl.initialize(platform, device);

% Now lets load the kernel cl/simple_add.cl
ocl.addfile('cl/simple_add.cl');

% You can add additional files you want to use
% ocl.addfile('cl/another_file.cl');
% ocl.addfile('cl/another_file2.cl');
% Now lets build our kernel and send to the GPU device:
ocl.build();

```

### opencl/opencl( ) constructor ###

Creates an OpenCL object with retrieved information about the available platform and devices. Calling this does not affect the previous state of the OpenCL platform. For example:
```
  ocl = opencl();

```
The member attribute _platforms_ is contains information about the available platform. Example:
```
>> ocl = opencl();
>> ocl.platforms

ans = 

       profile: 'FULL_PROFILE'
          name: 'NVIDIA CUDA'
        vendor: 'NVIDIA Corporation'
       version: 'OpenCL 1.0 CUDA 3.2.1'
    extensions: [1x131 char]
       devices: [1x1 struct]
```
A platform is simply the library that implements the OpenCL functionality. It is installed when you install the OpenCL device driver from the vendor. Each platform can communicate with one or more devices. For example, common platforms are:

  * AMD/ATI Stream platform (which can talk to CPUs and ATI GPGPUs)
  * NVIDIA platform (which can talk with NVIDIA GPGPUs)
  * Intel platform (which can talk to CPUs for now)

Each platform is associated with one or more devices. The device is the physical hardware that contains the GPU/CPU computing device we want to use. You can view details about the devices on the platforms as follows:
```
>> ocl.platforms(1).devices(1)

ans = 

                     profile: 'FULL_PROFILE'
                        name: 'NVS 3100M'
                      vendor: 'NVIDIA Corporation'
                     version: 'OpenCL 1.0 CUDA'
                      driver: '260.19.14'
                  extensions: [1x270 char]
           max_compute_units: 2
    max_work_item_dimensions: 3
         max_work_group_size: 512
         max_work_item_sizes: [512 512 64]
         max_clock_frequency: 1468
             global_mem_size: 536084480
       global_mem_cache_size: 0
    max_constant_buffer_size: 65536
           max_constant_args: 9
              local_mem_size: 16384
```

### opencl/initialize( ) ###
```
initialize(obj)
initialize(obj, PLATFORM)        
initialize(obj, PLATFORM, DEVICES)
```

Initialize OpenCL interface to use the specified platform and devices.
  * **PLATFORM** is the index of the platform in obj.platforms to use (where first index is 1). If unspecified, 1 is the default value and the first platform is used.
  * **DEVICES** is the indices of the devices in the platform to use (where first index is 1). If unspecified, the first device on the selected PLATFORM is used.

**Note**: Calling this function wipes out the previous state of the interface mex and resets the GPGPU state.

Example use:
```
ocl = opencl();
ocl.initialize(1,2)
```

This example sets OpenCL to use platform 1 and device 2. Platform 1 information is available in the member attribute:
```
ocl.platforms(1)
```
and information on device 2 is available in the member attribute:
```
ocl.platforms(1).devices(2)
```

## _openclcmd_ ##

**NOTE:** This section is listed here mainly for those who want to avoid using the matlab class files, for the seasoned developers who want to extend the opencl-toolbox functionality, or  for those of you who are just curious. In any case, this function is called upon by the matlab class files, so understanding this section is not necessary for use. And now onto the documentation:


A list of available platform and devices can be retrieved by calling _openclcmd_
in matlab as follows:
```
out = openclcmd();
```
_out_ now contains a list of platforms available. For example, on my laptop, out contains the following:

```
>> out 

out = 

       profile: 'FULL_PROFILE'
          name: 'NVIDIA CUDA'
        vendor: 'NVIDIA Corporation'
       version: 'OpenCL 1.0 CUDA 3.2.1'
    extensions: [1x131 char]
       devices: [1x1 struct]

```

If you have more than one platform installed, it would be a list of platforms available. This platform has 1 device associated with it. It is listed under devices. Under _openclcmd_, we use the C indexing (zero-based index) to identify
the platform number. Hence, this platform belongs to index = 0. (NOTE: for the matlab class files, we use one-based index since this is natural to matlab arrays. If you are using the matlab class file, _opencl_, you would refer to this platform as index = 1)

Similarly, the first device available is associated with index 0 in C (NOTE: again, the Matlab class files will refer to the index as 1 since MATLAB's first index is 1). Let's see what the first device contains:

```
>> out.devices(1)

ans = 

       profile: 'FULL_PROFILE'
          name: 'NVS 3100M'
        vendor: 'NVIDIA Corporation'
       version: 'OpenCL 1.0 CUDA'
        driver: '260.19.14'
    extensions: [1x131 char]
```

Thus, to initialise this device, one would mak

```
openclcmd( command_string, arg1, arg2, ... )
```

where _command\_string_ and the arguments are specified below:

| **command\_string** | **arguments**      | **description** |
|:--------------------|:-------------------|:----------------|
|  (no string)        | (no args)          | Returns a list of available platforms and devices |
| 'initialize'        | platform, device   || Initializes OpenCL to use the specified platform and device. Platform is the index value of the platform returned in the list|

value that ranges from 0