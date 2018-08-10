project('nagios-tang', 'c', license: 'GPL3+', version: '6',
        default_options: 'c_std=c99')

add_project_arguments(
  '-Wall',
  '-Wextra',
  '-Werror',
  '-Wstrict-aliasing',
  '-Wchar-subscripts',
  '-Wformat-security',
  '-Wmissing-declarations',
  '-Wmissing-prototypes',
  '-Wnested-externs',
  '-Wpointer-arith',
  '-Wshadow',
  '-Wsign-compare',
  '-Wstrict-prototypes',
  '-Wtype-limits',
  '-Wunused-function',
  '-Wno-missing-field-initializers',
  '-Wno-unused-command-line-argument',
  '-Wno-unused-parameter',
  '-Wno-unknown-pragmas',
  language: 'c'
)

http = meson.get_compiler('c').find_library('http_parser')
jose = dependency('jose', version: '>=8')

libexecdir = join_paths(get_option('prefix'), get_option('libexecdir'))
tang = find_program(join_paths(libexecdir, 'tangd'), required: false)
kgen = find_program(join_paths(libexecdir, 'tangd-keygen'), required: false)
updt = find_program(join_paths(libexecdir, 'tangd-update'), required: false)
actv = find_program('systemd-socket-activate', required: false)
if tang.found() and kgen.found() and updt.found() and actv.found()
  test('tang', find_program(meson.current_source_dir() + '/tang'),
    env: [
      'PATH=' + ':'.join([
        join_paths(get_option('prefix'), get_option('bindir')),
        meson.current_build_dir(),
        libexecdir,
      ])
    ]
  )
else
  warning('Will not run tests due to missing dependencies!')
endif

a2x = find_program('a2x', required: false)
if a2x.found()
  custom_target('nagios-tang.1',
    command: [a2x, '-f', 'manpage', '-D', meson.current_build_dir(), '@INPUT@'],
    install_dir: join_paths(get_option('mandir'), 'man1'),
    input: 'nagios-tang.1.adoc',
    output: 'nagios-tang.1',
    install: true,
  )
else
  warning('Will not install man page due to missing dependencies!')
endif

executable('tang', 'tang.c',
  install_dir: join_paths(get_option('libdir'), 'nagios', 'plugins'),
  dependencies: [http, jose],
  install: true,
)
