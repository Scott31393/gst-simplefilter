lib_args = common_args + []

gstvideo_dep = dependency('gstreamer-video-1.0',
  fallback: ['gst-plugins-base', 'video_dep'])

opencv_dep = dependency('opencv4',
  required: true)

# sources used to compile this plug-in
plugin_sources = [
  'gstsimplefilter.cpp'
]

shlib = shared_library('gstsimplefilter',
  plugin_sources,
  c_args : lib_args,
  include_directories: [configinc],
  dependencies : [plugin_deps, gstvideo_dep, opencv_dep],
  gnu_symbol_visibility : 'hidden',
  install : true,
  install_dir : plugins_install_dir,
)

# Make this library usable as a Meson subproject.
gst_simplefilter_dep = declare_dependency(
  include_directories: include_directories('.'),
  link_with : shlib)

pkg_mod = import('pkgconfig')
pkg_mod.generate(
  name : 'gst-simplefilter',
  filebase : 'gst-simplefilter',
  description : 'Meson sample project.',
  subdirs : 'src',
  libraries : shlib,
  version : '"@0@"'.format(meson.project_version()),
)
