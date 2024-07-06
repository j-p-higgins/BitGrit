cmake_minimum_required(VERSION 3.21)
 
set(PROJECT_NAME "BitGrit")
set(CMAKE_OSX_ARCHITECTURES "arm64;x86_64" CACHE STRING "Build architectures for macOS")
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.11" CACHE STRING "Minimum macOS version required")
 
project("${PROJECT_NAME}" VERSION 1.0.0)
 
set(formats "VST3" "Standalone")
 
if(APPLE)
    list(APPEND formats "AU")
endif()
 
add_subdirectory(External/JUCE)
 
juce_add_plugin("${PROJECT_NAME}"
    VERSION "1.0.0"
    COMPANY_NAME "Jonathan Higgins"
    PLUGIN_MANUFACTURER_CODE "JPHi"
    PLUGIN_CODE "JH02"
    FORMATS ${formats}
    COPY_PLUGIN_AFTER_BUILD FALSE
    IS_SYNTH FALSE     
    NEEDS_MIDI_INPUT FALSE
    NEEDS_MIDI_OUTPUT FALSE
    IS_MIDI_EFFECT FALSE
    PRODUCT_NAME "${PROJECT_NAME}")
 
juce_generate_juce_header("${PROJECT_NAME}")
 
include_directories(
    "${CMAKE_CURRENT_SOURCE_DIR}/RnboExport/rnbo"
    "${CMAKE_CURRENT_SOURCE_DIR}/RnboExport/rnbo/common")
 
target_sources("${PROJECT_NAME}"
    PRIVATE
        RnboExport/rnbo_bitgrit.cpp
        RnboExport/rnbo/RNBO.cpp
        Source/PluginProcessor.cpp
        Source/PluginEditor.cpp)
 
target_compile_features("${PROJECT_NAME}" PUBLIC cxx_std_20)
 
target_compile_definitions("${PROJECT_NAME}"
    PUBLIC
        JUCE_WEB_BROWSER=0
        JUCE_USE_CURL=0
        JUCE_VST3_CAN_REPLACE_VST2=0
	JUCE_DISPLAY_SPLASH_SCREEN=0)
 
target_link_libraries("${PROJECT_NAME}"
    PRIVATE
        juce::juce_audio_utils
        juce::juce_recommended_config_flags
        juce::juce_recommended_lto_flags
        juce::juce_recommended_warning_flags)