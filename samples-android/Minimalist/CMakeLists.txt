# Copyright (C) 2017 Google Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
##

cmake_minimum_required(VERSION 3.4.1)

add_library(gpg_sdk STATIC IMPORTED)
set_target_properties(gpg_sdk PROPERTIES IMPORTED_LOCATION
    ${GPG_SDK_PATH}/lib/c++/${ANDROID_ABI}/libgpg.a)

# build native_app_glue as a static lib
add_library(native_app_glue STATIC
    ${ANDROID_NDK}/sources/android/native_app_glue/android_native_app_glue.c)

# Export ANativeActivity_onCreate()
# Refer to: https://github.com/android-ndk/ndk/issues/381.
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -u ANativeActivity_onCreate")

add_library(native-activity
             SHARED
             src/main/cpp/main.cpp
             src/main/cpp/StateManager.cpp
           )

target_include_directories(native-activity PRIVATE
     ${ANDROID_NDK}/sources/android/native_app_glue
     ${GPG_SDK_PATH}/include
)

target_link_libraries(native-activity
                       gpg_sdk
                       native_app_glue
                       log
                       android
                       EGL
                       GLESv1_CM
                       z)
