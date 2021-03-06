gst_check_sources = [
  'gstbufferstraw.c',
  'gstcheck.c',
  'gstconsistencychecker.c',
  'gstharness.c',
  'gsttestclock.c',
]
gst_check_headers = [
  'check.h',
  'check-prelude.h',
  'gstbufferstraw.h',
  'gstcheck.h',
  'gstconsistencychecker.h',
  'gstharness.h',
  'gsttestclock.h',
]
install_headers(gst_check_headers, subdir : 'gstreamer-1.0/gst/check/')


check_cdata = configuration_data()

check_cdata.set('ENABLE_SUBUNIT', 0)
check_cdata.set('CHECK_MAJOR_VERSION', 0)
check_cdata.set('CHECK_MINOR_VERSION', 9)
check_cdata.set('CHECK_MICRO_VERSION', 14)
if host_system != 'windows'
  check_cdata.set('HAVE_FORK', 1)
else
  check_cdata.set('HAVE_FORK', 0)
endif

subdir('libcheck')

configure_file(input : 'libcheck/check.h.in',
  output : 'internal-check.h',
  install_dir : join_paths(get_option('includedir'), 'gstreamer-1.0/gst/check'),
  configuration : check_cdata)

gst_check = library('gstcheck-@0@'.format(apiversion),
  gst_check_sources,
  c_args : gst_c_args + ['-UG_DISABLE_ASSERT', '-DBUILDING_GST_CHECK'],
  version : libversion,
  soversion : soversion,
  darwin_versions : osxversion,
  install : true,
  include_directories : [configinc, libsinc],
  link_with : [libcheck],
  dependencies : [gobject_dep, glib_dep, gst_dep],
)

pkgconfig.generate(gst_check,
  libraries : [libgst],
  variables : pkgconfig_variables,
  subdirs : pkgconfig_subdirs,
  name : 'gstreamer-check-1.0',
  description : 'Unit testing helper library for GStreamer modules',
)

gst_check_gen_sources = []

if build_gir
  gst_gir_extra_args = gir_init_section + [ '--c-include=gst/check/check.h' ]
  gst_check_gir = gnome.generate_gir(gst_check,
    sources : gst_check_sources + gst_check_headers,
    namespace : 'GstCheck',
    nsversion : apiversion,
    identifier_prefix : 'Gst',
    symbol_prefix : 'gst',
    export_packages : 'gstreamer-check-1.0',
    dependencies : [gst_dep],
    include_directories : [configinc, libsinc, privinc],
    includes : ['GLib-2.0', 'GObject-2.0', 'GModule-2.0', 'Gst-1.0'],
    install : true,
    extra_args : gst_gir_extra_args,
  )
  gst_check_gen_sources += gst_check_gir
endif

gst_check_dep = declare_dependency(link_with : gst_check,
  include_directories : [libsinc],
  dependencies : [gst_dep],
  sources : gst_check_gen_sources)

meson.override_dependency('gstreamer-check-1.0', gst_check_dep)
