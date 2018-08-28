project('nagios-tang', 'c', license: 'GPL3+', version: '7',
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

libexecdir = join_paths(get_option('prefix'), get_option('libexecdir'))

http = meson.get_compiler('c').find_library('http_parser')
jose = dependency('jose', version: '>=8')

actv = find_program('systemd-socket-activate', required: false)
tang = find_program([
  join_paths(libexecdir, 'tangd'),
  '/usr/libexec/tangd',
  '/usr/local/libexec/tangd',
], required: false)
kgen = find_program([
  join_paths(libexecdir, 'tangd-keygen'),
  '/usr/libexec/tangd-keygen',
  '/usr/local/libexec/tangd-keygen',
], required: false)
updt = find_program([
  join_paths(libexecdir, 'tangd-update'),
  '/usr/libexec/tangd-update',
  '/usr/local/libexec/tangd-update',
], required: false)

if tang.found() and kgen.found() and updt.found() and actv.found()
  env = environment()
  env.append('PATH',
    meson.current_build_dir(),
    libexecdir,
    '/usr/libexec',
    '/usr/local/libexec',
    separator: ':'
  )

  test('tang', find_program(meson.current_source_dir() + '/tang'), env: env)
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
