project('mimic-cstr_upc_upm_spanish_hts', 'c', default_options : ['c_std=c99'], version: '0')
voice_name = 'cstr_upc_upm_spanish_hts'

build_plugin = get_option('plugin')

sources = [
  'src/cstr_upc_upm_spanish_hts.c',
]

install_data('voices/cstr_upc_upm_spanish_hts.htsvoice',
             install_dir : join_paths(get_option('datadir'), 'ttsmimic', 'voices'))

includes = [include_directories('include'), include_directories('.')]

# Spanish Dependency:
mimic_spanish = dependency('ttsmimic_spanish', required: false)
if mimic_spanish.found()
  mimiccore = dependency('ttsmimic_core')
  mimic_plugin_dir = mimiccore.get_pkgconfig_variable('mimic_plugin_dir')
else
  # We compile it:
  mimic_spanish_sub = subproject('mimic-spanish')
  mimic_spanish = mimic_english_sub.get_variable('ttsmimic_spanish_dep')
  mimiccore = mimic_spanish_sub.get_variable('mimiccore')
  mimic_plugin_dir = mimic_spanish_sub.get_variable('mimic_plugin_dir')
  includes += mimic_spanish_sub.get_variable('includes')
endif

message('Build mimic-@0@ plugin: @1@'.format(voice_name, build_plugin))


es_header_subdir = join_paths('ttsmimic', 'lang', 'es')

conf_data = configuration_data()
conf_data.set('ENABLE_@0@_BUILTIN'.format(voice_name.to_upper()), not build_plugin)

config_h = configure_file(output : 'mimic_@0@_config.h'.format(voice_name),
                          configuration : conf_data, install: true,
                          install_dir : join_paths(get_option('includedir'), es_header_subdir))

sources += [config_h]

voice_lib = library('ttsmimic_@0@'.format(voice_name), sources,
                    include_directories : includes,
                    dependencies: [mimic_spanish, mimiccore],
                    install: true)

voice_dep = declare_dependency(link_with : voice_lib, 
                               include_directories : includes)


if build_plugin
  voice_plugin_lib = library(
    'ttsmimic_@0@_plugin'.format(voice_name),
    ['src/@0@_plugin.c'.format(voice_name), config_h],
    include_directories : includes,
    dependencies: [mimiccore, mimic_spanish, voice_dep],
    install_dir: mimic_plugin_dir,
    install: true)

  voice_plugin_dep = declare_dependency(
    link_with : voice_plugin_lib, 
    include_directories : includes)
endif



############ Headers #################

ttsmimic_headers = ['include/@0@.h'.format(voice_name)]

install_headers(ttsmimic_headers, subdir : es_header_subdir)


########### pkg-config #############

pkg = import('pkgconfig')

pkg.generate(libraries : voice_lib,
             name : 'ttsmimic_@0@'.format(voice_name),
             subdirs: es_header_subdir,
             requires: ['ttsmimic_spanish', 'ttsmimic_core'],
             description: '@0@ support for text to speech synthesis in mimic.'.format(voice_name),
             version: meson.project_version())

