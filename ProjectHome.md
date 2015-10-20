The goal of this project is to provide an opensource toolkit that provides seamless integration of Matlab with OpenCL. This toolkit will consist of three parts:
  * a mex backend written in C to allow calls from MATLAB to OpenCL commands
  * a set of matlab class files that represent OpenCL data objects that can be manipulated using standard matlab operators (e.g. overriding minus, plus, rdivide, ldivide, inv, etc)
  * a set of opencl kernel files to implement more complicated algorithms (such as inverse)

What's done:
  * mex backend. supports GPU & CPU devices
  * matlab classes for buffers and kernels.
  * a generic clobject to convert matlab vectors/matrices to buffers

Current To Do List:
  * representation for images and samplers
  * override matlab functions
  * finish documentation