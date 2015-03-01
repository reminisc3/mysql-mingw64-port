# mysql-mingw64-port
Port MySQL C/C++ connector to Mingw64

The goal of this project was/is to provide *functional* support of the build/use of the MySQL C/C++ connector with the Mingw64 toolset. 

Out of the box, the MySQL connectors only support visual studio and *namely*, Microsoft proprietary functionality.

Here are some notable changes in the source code:

1) localtime_r/s not supported by Mingw64 (as of now). Changed to regular localtime (Should be thread safe, not re-entrant safe though)
2) __try __catch is not standard C. Implemented this functionality for Mingw64 with libseh: http://www.programmingunlimited.net/siteexec/content.cgi?page=libseh
3) Removed any references to strtok_r/s. MySQL has a custom implementation within their source which will have to do
4) Fixed syntax errors. Mainly syntax issues which prevent compiling with GCC ( will squeak by with MS compiler :p )

** More changes to be noted, work in progress **


