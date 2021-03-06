project('gnome-multi-writer', 'c',
  version : '3.24.1',
  default_options : ['warning_level=1'],
  meson_version : '>=0.37.0'
)

conf = configuration_data()
conf.set('PACKAGE_VERSION', meson.project_version())

# get suported warning flags
test_args = [
  '-Waggregate-return',
  '-Warray-bounds',
  '-Wcast-align',
  '-Wclobbered',
  '-Wdeclaration-after-statement',
  '-Wempty-body',
  '-Wextra',
  '-Wformat=2',
  '-Wformat-nonliteral',
  '-Wformat-security',
  '-Wformat-signedness',
  '-Wignored-qualifiers',
  '-Wimplicit-function-declaration',
  '-Winit-self',
  '-Winline',
  '-Wmissing-declarations',
  '-Wmissing-format-attribute',
  '-Wmissing-include-dirs',
  '-Wmissing-noreturn',
  '-Wmissing-parameter-type',
  '-Wmissing-prototypes',
  '-Wnested-externs',
  '-Wno-discarded-qualifiers',
  '-Wno-missing-field-initializers',
  '-Wno-strict-aliasing',
  '-Wno-suggest-attribute=format',
  '-Wno-unused-parameter',
  '-Wold-style-definition',
  '-Woverride-init',
  '-Wpacked',
  '-Wpointer-arith',
  '-Wredundant-decls',
  '-Wreturn-type',
  '-Wshadow',
  '-Wsign-compare',
  '-Wstrict-aliasing',
  '-Wstrict-prototypes',
  '-Wswitch-default',
  '-Wtype-limits',
  '-Wundef',
  '-Wuninitialized',
  '-Wunused-but-set-variable',
  '-Wwrite-strings'
]
cc = meson.get_compiler('c')
foreach arg: test_args
  if cc.has_argument(arg)
    add_project_arguments(arg, language : 'c')
  endif
endforeach

# enable full RELRO where possible
# FIXME: until https://github.com/mesonbuild/meson/issues/1140 is fixed
global_link_args = []
test_link_args = [
  '-Wl,-z,relro',
  '-Wl,-z,now',
]
foreach arg: test_link_args
  if cc.has_argument(arg)
    global_link_args += arg
  endif
endforeach
add_global_link_arguments(
  global_link_args,
  language: 'c'
)

gtk = dependency('gtk+-3.0', version : '>= 3.11.2')
gio = dependency('gio-2.0')
gusb = dependency('gusb', version : '>= 0.2.7')
udisks = dependency('udisks2')
gudev = dependency('gudev-1.0')
libcanberra = dependency('libcanberra-gtk3', version : '>= 0.10')
polkit = dependency('polkit-gobject-1')

gnome = import('gnome')
i18n = import('i18n')

conf.set_quoted('GETTEXT_PACKAGE', meson.project_name())
conf.set_quoted('VERSION', meson.project_version())
conf.set_quoted('LOCALEDIR',
                join_paths(get_option('prefix'),
                           get_option('localedir')))
conf.set_quoted('LIBEXECDIR',
                join_paths(get_option('prefix'),
                           get_option('libexecdir')))
configure_file(
  output : 'config.h',
  configuration : conf
)

subdir('src')
subdir('man')
subdir('po')
subdir('data')

archiver = find_program('git', required : false)
if archiver.found()
  run_target('dist',
    # git config tar.tar.xz.command "xz -c"
    command: [
      'git', 'archive',
      '--prefix=' + meson.project_name() + '-' + meson.project_version() + '/',
      'HEAD',
      '--format=tar.xz',
      '--output',
      meson.project_name() + '-' + meson.project_version() + '.tar.xz'
    ]
  )
else
  message('git not found, you will not be able to run `ninja dist`')
endif

appstream_util = find_program('appstream-util', required : false)
if appstream_util.found()
  custom_target('NEWS',
    output : 'NEWS',
    input : 'data/appdata/org.gnome.MultiWriter.appdata.xml.in',
    command : [appstream_util, 'appdata-to-news', '@INPUT@'],
    capture : true
  )
endif

# FIXME: remove when https://github.com/mesonbuild/meson/issues/837 fixed
meson.add_install_script('meson_post_install.sh')
