# Install Guide v0.11 #
**Version** 0.11<br>
<b>Date:</b> 01.20.2011<br>
<b>Website:</b> <a href='http://code.google.com/p/opencl-toolbox'>http://code.google.com/p/opencl-toolbox</a> <br>
<b>SVN:</b> <a href='http://opencl-toolbox.googlecode.com/svn/trunk/'>http://opencl-toolbox.googlecode.com/svn/trunk/</a> <br>

<h2>Requirements</h2>

<ul><li>OpenCL 1.1+ device driver platform<br>
<ul><li>e.g. you can download and install one from:<br>
<ul><li>AMD/ATI: <a href='http://developer.amd.com/zones/OpenCLZone'>http://developer.amd.com/zones/OpenCLZone</a>
</li><li>Intel: <a href='http://software.intel.com/en-us/articles/intel-opencl-sdk'>http://software.intel.com/en-us/articles/intel-opencl-sdk</a>
</li><li>NVIDIA: <a href='http://www.nvidia.com/opencl'>http://www.nvidia.com/opencl</a>
</li></ul></li></ul></li><li>MATLAB <a href='https://code.google.com/p/opencl-toolbox/source/detail?r=2009'>R2009</a> or later (I've only tested this on <a href='https://code.google.com/p/opencl-toolbox/source/detail?r=2010'>R2010</a>)<br>
</li><li>C++ compiler (such as g++ or Visual Studio )</li></ul>

<h2>Installation</h2>

1. In MATLAB, open the make.m file and edit the opencl_include_dir<br>
and opencl_lib_dir to point to the proper folders.<br>
<br>
<ul><li><i>opencl_include_dir</i> : is the directory containing CL/cl.h<br>
<ul><li>e.g. Linux: /usr/include, /sw/ati-stream-sdk-v2.2-lnx64/include/<br>
</li><li>e.g. Windows: C:\Program Files\ATI Stream\include</li></ul></li></ul>

<ul><li><i>opencl_lib_dir</i>:  is the directory containing libOpenCL.so (or OpenCL.lib in Windows)<br>
<ul><li>e.g. Linux: /usr/lib , /sw/ati-stream-sdk-v2.2-lnx64/lib/x86_64<br>
</li><li>e.g. Windows: C:\Program Files\ATI Stream\lib\x86</li></ul></li></ul>

2. In MATLAB, execute make (either press F5 or type in the matlab console):<br>
<pre><code>    make<br>
</code></pre>
3. Add this folder to your toolbox path. Now we're ready to go!