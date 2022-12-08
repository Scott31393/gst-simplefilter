# Simple Gstreamer element

## Introduction
This project is born to learn how to write a Gstreamer video element from scratch.

## Get Started
Clone gst-plugins-bad repository:
```
$ git clone https://github.com/GStreamer/gst-plugins-bad.git
```

## Create GStreamer Project from Template
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

## Create GStreamer Element from Template
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

## Move Create Element into the Create Project
Move create element file into created plugin project directory:

```
$ mv gstsimplefilter.c gst-simplefilter/plugins/
$ mv gstsimplefilter.h gst-simplefilter/plugins/
```

## Fix Meson Build Files

Since it is an element created by inheriting **videofilter**, it is necessary to add a dependency to meson.build. Other thing is that function ***gst_element_register*** is duplicated, so delete ***simplefilterplugin.c*** file and update plugin meson.build file:

```
diff --git a/plugins/meson.build b/plugins/meson.build
index fda16ed..2cb660d 100644
--- a/plugins/meson.build
+++ b/plugins/meson.build
@@ -1,8 +1,11 @@
 lib_args = common_args + []
 
+gstvideo_dep = dependency('gstreamer-video-1.0',
+  fallback: ['gst-plugins-base', 'video_dep'])
+
+
 # sources used to compile this plug-in
 plugin_sources = [
-  'gstsimplefilterplugin.c',
   'gstsimplefilter.c',
   'gstsimplefilter.h'
 ]
@@ -11,7 +14,7 @@ shlib = shared_library('gstsimplefilter',
   plugin_sources,
   c_args : lib_args,
   include_directories: [configinc],
-  dependencies : plugin_deps,
+  dependencies : [plugin_deps, gstvideo_dep],
   gnu_symbol_visibility : 'hidden',
   install : true,
   install_dir : plugins_install_dir,
```
References:
 - [fix plugin meson.build](https://github.com/Scott31393/gst-simplefilter/commit/b5e6b1d8e91da3de2ad47e2e62f2daa73a5387fe)

## Build the Project
Plugins can be installed locally by using "$HOME" as prefix:

```
$ meson --prefix="$HOME" build/
$ ninja -C build/ install
```

However be advised that the automatic scan of plugins in the user home
directory won't work under gst-build devenv.
