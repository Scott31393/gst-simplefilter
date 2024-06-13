<p align="left">
  <img width="400" height="150" src="https://upload.wikimedia.org/wikipedia/commons/thumb/d/db/Gstreamer-logo.svg/2560px-Gstreamer-logo.svg.png">
</p>


# Simple Gstreamer Video Element

## Introduction
Simple Gstreamer Element from scratch.
<p align="center">
  <img src="https://gstreamer.freedesktop.org/documentation/tutorials/basic/images/figure-1.png">
</p>

<p align="center">
  <img src="https://gstreamer.freedesktop.org/documentation/application-development/basics/images/filter-element.png">
</p>

Filters and filter-like elements have both input and outputs pads. They operate on data that they receive on their input (sink) pads, and will provide data on their output (source) pads. Examples of such elements are a volume element (filter), a video scaler (convertor), an Ogg demuxer or a Vorbis decoder.

Filter-like elements can have any number of source or sink pads. A video demuxer, for example, would have one sink pad and several (1-N) source pads, one for each elementary stream contained in the container format. Decoders, on the other hand, will only have one source and sink pads.



#### Project Structure:
```
.
├── AUTHORS
├── ChangeLog
├── COPYING
├── meson.build
├── NEWS
├── plugins
│   ├── gstsimplefilter.cpp
│   ├── gstsimplefilter.hpp
│   └── meson.build
├── README.md
└── tools
    ├── gstsimplefilter.cpp
    └── meson.build

2 directories, 11 files
```

References:
 - [GStreamer elements](https://gstreamer.freedesktop.org/documentation/application-development/basics/elements.html#elements)
 - [GStreamer Writer's Guide](https://gstreamer.freedesktop.org/documentation/plugin-development/index.html?gi-language=c)
 - [GstVideoFilter](https://gstreamer.freedesktop.org/documentation/video/gstvideofilter.html?gi-language=c)
 - [GStreamer filter elements](https://gstreamer.freedesktop.org/documentation/application-development/basics/elements.html#filters-convertors-demuxers-muxers-and-codecs)

## Setup GStreamer Build Env
First install dependencies:
```
sudo apt install build-essential python3 git ninja-build python3-pip
```
Install meson from the pip repo:
```
pip3 install --user meson
```
Clone and build GStreamer repos:
```
git clone https://gitlab.freedesktop.org/gstreamer/gst-build
cd gst-build
meson build --buildtype=debug
ninja -C build
```

Use build devenv environment.
This command will create an environment where all tools and plugins built previously are available in the environment as a superset of the system environment with the right environment variables set.

```
ninja -C build devenv
```

Your bash become:
```
[gst-discontinued-for-monorepo] $
```

## Build Gstreamer Video Element
First get the sources and cd inside:
```
$ git clone https://github.com/Scott31393/gst-simplefilter.git
$ cd gst-simplefilter
```
Than build the project. Remember that plugins can be installed locally by using "$HOME" as prefix:

```
$ meson --prefix="$HOME" build/
$ ninja -C build/ install
```

However be advised that the automatic scan of plugins in the user home directory won't work under gst-build devenv.

Instead of add the plugin into your home you can add the plugin path to the GST_PLUGIN_PATH.

```
export GST_PLUGIN_PATH=$GST_PLUGIN_PATH:/path-to/gst-simplefilter/build/plugins/
```


References:
 - [Getting started with GStreamer's gst-build](https://www.collabora.com/news-and-blog/blog/2020/03/19/getting-started-with-gstreamer-gst-build/)

## Use Gstreamer Video Element
To test and use gst simplefilter element just run the following gst-launch pipeline:

```
$ gst-launch-1.0 v4l2src ! videoconvert ! simplefilter ! videoconvert ! xvimagesink
```
Debug the element using:
```
$ GST_DEBUG=simplefilter:5 gst-launch-1.0 v4l2src ! videoconvert ! simplefilter ! videoconvert ! xvimagesink
```


## Create New Element from Scratch
Clone gst-plugins-bad repository:
```
$ git clone https://github.com/GStreamer/gst-plugins-bad.git
```

### Create GStreamer Project from Template
Go inside tools dir and create project from template using gst-project-maker:

```
$ cd gst-plugins-bad/tools
$ ./gst-project-maker "simplefilter"
```
This will create:
```
gst-simplefilter/
├── AUTHORS
├── ChangeLog
├── COPYING
├── meson.build
├── NEWS
├── plugins
│   ├── gstsimplefilter.c
│   ├── gstsimplefilter.h
│   ├── gstsimplefilterplugin.c
│   └── meson.build
├── README
└── tools
    ├── gstsimplefilter.c
    └── meson.build

2 directories, 12 files
```

### Create GStreamer Element from Template
Go inside tools dir and create project from template using gst-element-maker:
```
$ ./gst-element-maker "simplefilter" videofilter
```
This will create:
```
gstsimplefilter.c
gstsimplefilter.h
gstsimplefilter.o
gstsimplefilter.so
```

### Move Create Element into the Create Project
Move create element file into created plugin project directory:

```
$ mv gstsimplefilter.c gst-simplefilter/plugins/
$ mv gstsimplefilter.h gst-simplefilter/plugins/
```

### Cross Compile for arm64
```
source /opt/fslc-xwayland/4.1-snapshot-20240610/environment-setup-cortexa53-crypto-fslc-linux
meson build
ninja -C build
sudo cp build/plugins/libgstsimplefilter.so /targetfs/usr/lib/gstreamer-1.0/
gst-inspect-1.0 simplefilter
```

### Fix Meson Build Files

Since it is an element created by inheriting **videofilter**, it is necessary to add a dependency to meson.build. Other thing is that function ***gst_element_register*** is duplicated, so delete ***simplefilterplugin.c*** file and update plugin meson.build file:

References:
 - [fix plugin meson.build](https://github.com/Scott31393/gst-simplefilter/commit/b5e6b1d8e91da3de2ad47e2e62f2daa73a5387fe)
 - [move from c to cpp](https://github.com/Scott31393/gst-simplefilter/commit/9e16950473c722cdd1c00ff324cd0837eeb89cb7)
