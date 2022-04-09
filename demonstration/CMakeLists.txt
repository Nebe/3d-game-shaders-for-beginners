# 			 The readme file already contains instructions on how to build main.cxx on other systems.
# IMPORTANT: Made and tested for Windows. Build can be performed from the MSVC (2022) 
# 			 Main goal is to compile the resulting .exe file, not to set up the MSVC project properly.
#			 This is my first application of CMake, improvements are very welcome, see TODOS.

# USAGE:     cmake -DPANDA_3D_PATH=<path> <path_to_this_CMakeLists.txt>
#			 then open the generated *.sln file, choose Release(!) and press Build(top context menu)->Build
#			 .exe should be used directly after placing it in the "demonstration" directory.

# EXAMPLE:   cmake -DPANDA_3D_PATH="D:\\external_projects\\Panda3D-1.10.11-x64" .\CMakeLists.txt -B.\build_dir
#			 can also be called directly from MSVS after the project directory was imported as an existing project.

# TODOS: would be nice to actually be able to work with the built solution from MSVC and not just being able to compile the target for direct manual use. 
# 		the below command is not setting the debugger working directory properly, not clear why. 
# 		set_property(TARGET ${PROJECT_NAME} PROPERTY VS_DEBUGGER_WORKING_DIRECTORY ..\\${CMAKE_SOURCE_DIR})
# 		Temporary workaround: The resulting (Release) .exe file is output to the parent directory and can be called directly
#		

# minimum version must be provided
cmake_minimum_required(VERSION 3.21)
# must be provided by the caller, see usage example
set(PANDA_3D_PATH "NONE" CACHE PATH "Choose Panda 3D Path")

if(PANDA_3D_PATH STREQUAL "NONE")
	message(FATAL_ERROR "PANDA_3D_PATH was not provided; see comments in the CMakeLists.txt for usage.")
endif()

# defaults the build type to Release
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING "Choose Release or Debug" FORCE)
endif()

# implicitly setting PROJECT_NAME
project (3d-game-shaders-for-beginners)

# "libpthread" was removed since not available on Windows, main.cxx was modified to avoid this dependency.
set(panda_3d_lib_deps "libp3framework" "libpanda" "libpandafx" "libpandaexpress" "libpandaphysics" "libp3dtoolconfig" "libp3dtool") 
# Panda 3D related paths
set(panda_3d_lib_path "${PANDA_3D_PATH}\\lib")
set(panda_3d_include_path "${PANDA_3D_PATH}\\include")
set(panda_3d_python_include_path "${PANDA_3D_PATH}\\python\\include")

# required C++ version
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)
# optimization level
set(CMAKE_CXX_FLAGS_RELEASE "/O2")

# main target source file
add_executable (${PROJECT_NAME} EXCLUDE_FROM_ALL "./src/main.cxx")

set(TARGET ${PROJECT_NAME} EXECUTABLE_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR})

target_include_directories(${PROJECT_NAME} PUBLIC ${panda_3d_include_path})
target_include_directories(${PROJECT_NAME} PUBLIC ${panda_3d_python_include_path})
target_link_directories(${PROJECT_NAME} PUBLIC ${panda_3d_lib_path})
target_link_libraries(${PROJECT_NAME} PUBLIC ${panda_3d_lib_deps})