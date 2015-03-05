# mysql-mingw64-port
Port MySQL C/C++ connector to Mingw64

Libraries included:
MySQL C++ Connector 1.1.5
MySQL C Connector 6.1.5

The goal of this project was/is to provide *functional* support of the build/use of the MySQL C/C++ connector with the Mingw64 toolset. 

Out of the box, the MySQL connectors only support visual studio and *namely*, Microsoft proprietary functionality.

Pre-built 32/64 bit libraries are included in this repo

------------------------------------------------------------

Here are some notable source changes:

1) localtime_r/s not supported by Mingw64 (as of now). Changed to regular localtime (Should be thread safe, not re-entrant safe though)

2) __try __catch is not standard C. Implemented this functionality for Mingw64 with libseh: http://www.programmingunlimited.net/siteexec/content.cgi?page=libseh

3) Removed any references to strtok_r/s. MySQL has a custom implementation within their source which will have to do

4) Fixed syntax errors. Mainly syntax issues which prevent compiling with GCC ( will squeak by with MS compiler :p )

5) Re-implemented timespec() - skip Mingw64 declaration with #define

6) Fixed i64, LL, ULL references...

---------------------------------------------------

Building:

C++ Connector
--------------
You can use the pre-built binaries with your project. Remember to link with: ws2_32, secur32, mysqlclient

To build Cppconn with CMake:

-------------------------------------
64 bit:
---------------------------------------
1) Include something like this in C/CXX flags: -Ic:/boost -LPATH_TO_LIBMYSQL -DSECURITY_WIN32
2) Link with these libs: -lmysqlclient -lws2_32 -lsecur32

Make sure you have a Mingw64 toolchain installed (I use WinBuilds) and the bin/ in your PATH variable. When generating with CMake, choose the source dir of each project respectively, and use "Mingw Makefiles". The connector c++ library also depend on Boost, so make sure you have Mingw64 compiled Boost somewhere on your box and set the 'Boost_INCLUDE_DIR' CMAKE var to eg: 'C:/boost'

If you get errors while generating, change '_found_header' to the MySQL C connector include dir...eg: 'C:/Program Files/MySQL/MySQL Connector C 6.1/include' (or the source contained in this repo). Otherwise, you will need to modify the cmakelists file and add this dir using include_directories()

--------------------------------------------
32 bit
--------------------------------------------

1 ) Add to linked libs: -lmysqlclient -lwsock32 -lws2_32 -lsecur32
2) Add search path for mysqlclient lib, eg: -L{PATH_TO}MYSQLCLIENT.A
3) Set CMake var '_found_header' to path to MySQL C Connector /include
4) CMake var 'MYSQL_LIB' to path to 'libmysqlclient.a'
5) Configure/generate and build with mingw32-make or (make)
---------------------------

+++++++++++++++++++++++++++++++++

-----------------------------
C Connector
----------------------------

-----------------------
64 bit
----------------------
1) Include search path to libseh header/lib in c/xx flags: -IPATH_TO_LIBSEH\libseh -LPATH_TO_LIBSEH\libseh
2) libseh is included in the c connector source dir
3) Link with : -lseh -lmingwex

-----------------------
32 bit
-------------------------

*note* I could not link the 32 bit DLLs. There is quite a bit of name mangling when trying to link and I decided to just support static libs

1) Include search path for libseh header/libs, eg: -I{PATH_TO_LIBSEH} -L{PATH_TO_LIBSEH}
2) Link C compiler with -lseh32
3) Add  -lws2_32 -lsecur32 to CXX flags
4) Set CMake var 'DISABLED_SHARED' to true
5) Generate/configure and build with mingw32-make or make
