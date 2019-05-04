project('nanogui', ['cpp'], 
         version       : '0.1.0',  
         license       : ['BSD3'],
         meson_version : '>= 0.49.0')




name = 'nanogui'




system     = build_machine.system()
cpu        = build_machine.cpu()
cpu_family = build_machine.cpu_family()

cppc    = meson.get_compiler('cpp')
cppc_id = cppc.get_id() 

lib_type       = get_option('default_library')
build_examples = get_option('build_examples')

includes  = []
sources   = []
cpp_args  = []
link_args = []




if system not in ['windows']
    error('Script (meson.build) not prepared for your platform yet.')
endif

if system == 'windows' and cppc_id not in ['msvc']
    error('Script (meson.build) not prepared for your compiler yet.')
endif




includes += [include_directories('include'), include_directories('src')] 

sources = ['src/glutil.cpp',
           'src/common.cpp',
           'src/widget.cpp',
           'src/theme.cpp',
           'src/layout.cpp',
           'src/screen.cpp',
           'src/label.cpp',
           'src/window.cpp',
           'src/popup.cpp',
           'src/checkbox.cpp',
           'src/button.cpp',
           'src/popupbutton.cpp',
           'src/combobox.cpp',
           'src/progressbar.cpp',
           'src/slider.cpp',
           'src/messagedialog.cpp',
           'src/textbox.cpp',
           'src/imagepanel.cpp',
           'src/imageview.cpp',
           'src/vscrollpanel.cpp',
           'src/colorwheel.cpp',
           'src/colorpicker.cpp',
           'src/graph.cpp',
           'src/stackedwidget.cpp',
           'src/tabheader.cpp',
           'src/tabwidget.cpp',
           'src/glcanvas.cpp',
           'src/serializer.cpp']

cpp_args    = ['-DNANOGUI_GLAD',
               '-DEIGEN_DONT_ALIGN']




eigen_project = subproject('eigen')
eigen_dep     = eigen_project.get_variable('eigen_dep')

glfw_project = subproject('glfw')
glfw_dep     = glfw_project.get_variable('glfw_dep')

nanovg_project = subproject('nanovg')
nanovg_dep     = nanovg_project.get_variable('nanovg_dep')

glad_gl_project = subproject('glad-gl-3.3-core')
glad_gl_dep     = glad_gl_project.get_variable('glad_gl_dep')

bin2c_project = subproject('bin2c-1.0.0')
bin2c_cmd     = bin2c_project.get_variable('bin2c_cmd')

if system == 'windows'
    opengl_dep = cppc.find_library('opengl32', required: true)
endif




nanogui_resources = custom_target('nanogui_resources',
  input : ['resources/entypo.ttf', 
           'resources/Roboto-Bold.ttf', 
           'resources/Roboto-Regular.ttf'],
  output : ['nanogui_resources.h', 
            'nanogui_resources.cpp'],
  command : [bin2c_cmd, 
            '--header', '@OUTPUT0@', 
            '--output', '@OUTPUT1@', 
            '--name', 'entypo_ttf', '@INPUT0@', 
            '--name', 'roboto_bold_ttf', '@INPUT1@', 
            '--name', 'roboto_regular_ttf', '@INPUT2@'])

nanogui_resources_dep = declare_dependency( 
    sources : nanogui_resources
)




if lib_type == 'static' or lib_type == 'both' 
    static_lib_name        = name
    static_lib_name_prefix = []
    static_lib_name_suffix = []

    if system == 'windows' and cppc_id == 'msvc'  
        static_lib_name       += '_static'
        static_lib_name_prefix = ''
        static_lib_name_suffix = 'lib'
    endif

    nanogui_static = static_library(static_lib_name,
                                    name_prefix         : static_lib_name_prefix, 
                                    name_suffix         : static_lib_name_suffix,
                                    include_directories : includes,
                                    sources             : sources,
                                    cpp_args            : cpp_args + ['-DNANOGUI_BUILD'], 
                                    link_args           : link_args,
                                    dependencies        : [nanogui_resources_dep, eigen_dep, glfw_dep, nanovg_dep, glad_gl_dep],
                                    install             : true)
      
    nanogui_static_dep = declare_dependency(include_directories : includes,
                                            compile_args        : cpp_args,
                                            link_with           : nanogui_static,
                                            dependencies        : [eigen_dep, glfw_dep, nanovg_dep, opengl_dep, glad_gl_dep])
endif




if lib_type == 'shared' or lib_type == 'both'
    shared_lib_name = name 

    nanogui_shared = shared_library(shared_lib_name,
                                    include_directories : includes,
                                    sources             : sources,
                                    cpp_args            : cpp_args + ['-DNANOGUI_BUILD', '-DNANOGUI_SHARED'], 
                                    link_args           : link_args,
                                    dependencies        : [nanogui_resources_dep, eigen_dep, glfw_dep, nanovg_dep, glad_gl_dep],
                                    install             : true)

    nanogui_shared_dep = declare_dependency(include_directories : includes,
                                            compile_args        : cpp_args + ['-DNANOGUI_SHARED'],
                                            link_with           : nanogui_shared,
                                            dependencies        : [eigen_dep, glfw_dep, nanovg_dep, opengl_dep, glad_gl_dep])
endif




if lib_type == 'static'
    nanogui_dep = nanogui_static_dep
elif lib_type == 'shared' or lib_type == 'both'
    nanogui_dep = nanogui_shared_dep
endif




if get_option('build_examples')
  if not meson.is_subproject()
    executable('example1' 
               ,sources      : ['src/example1.cpp']
               ,dependencies : [nanogui_dep]
               ,install      : true)

    executable('example2' 
               ,sources      : ['src/example2.cpp']
               ,dependencies : [nanogui_dep]
               ,install      : true)

    executable('example3' 
               ,sources      : ['src/example3.cpp']
               ,dependencies : [nanogui_dep]
               ,install      : true)

    executable('example4' 
               ,sources      : ['src/example4.cpp']
               ,dependencies : [nanogui_dep]
               ,install      : true)

    install_subdir('resources/icons', install_dir : 'resources')    
  else
    warning('The option ' + name + ':' + 'build_examples' + ' not work in subproject.')
  endif 
endif




install_subdir('include/nanogui', install_dir : 'include')
install_data(['LICENSE.txt'],
               rename      : ['LICENSE.BSD'],
               install_dir :  'share/licenses/nanogui')