# cmake との対応

|機能|cmake|meson|備考|
|-|-|-|-|
|project|project|project|トップレベル。visual studio の sln に相当する|
|exe|add_executable|executable|
|lib|add_library|library|
|lib(static)|add_library(some STATIC)|static_library|
|lib(shared)|add_library(some SHARED)|shared_library|
|target cflags| target_compile_options | library, executable(c_args:, cpp_args:), declare_dependency(c_args:, cpp_args:) |
|target cpp std|
|target definition| target_compile_definitions | cflags と区別なし |
|target include| target_include_derectories | library, executable, declare_dependency(include_derectories:) |
|target link| target_link_libraries | library, executable(dependencies:), declare_dependency(link_with:) |
|target libdir|
|global cflags| ADD_COMPILE_OPTIONS | project(default_options: ['c_args=cflags', 'cpp_args=cxxflags']) |
|global cpp std|| project(default_options: ['cpp_std=c++20'])
|global definition| add_definitions | cflags と区別なし
|global include| add_includes |
|global link|
|global libdir|
|install||library, executable(install : true)|
|subdir|add_subdirectory, subdirs|subdir('dirname')|
|option|
|prefix|
|toolchain|
|current|CMAKE_CURRENT_LIST_DIR|meson.current_source_dir()|


