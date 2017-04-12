import os
import sys
import glob
import excons
import excons.tools.boost as boost

env = excons.MakeBaseEnv()

out_incdir = excons.OutputBaseDirectory() + "/lib"
out_libdir = excons.OutputBaseDirectory() + "/lib"

static_build = (excons.GetArgument("yamlcpp-static", 1, int) != 0)

def YamlCppName():
   name = "yaml-cpp"
   if sys.platform == "win32" and static_build:
      name = "lib" + name
   return name + excons.GetArgument("yamlcpp-suffix", "")

def YamlCppPath():
   name = YamlCppName()
   if sys.platform == "win32":
      libname = name + ".lib"
   else:
      libname = name + (".a" if static_build else excons.SharedLibraryLinkExt())
   return out_libdir + "/" + libname

def RequireYamlCpp(env):
   if sys.platform == "win32" and not staticlib:
      env.Append(CPPDEFINES=["YAML_CPP_DLL"])
   env.Append(CPPPATH=[out_incdir])
   env.Append(LIBPATH=[out_libdir])
   excons.Link(env, YamlCppName(), static=static_build, force=True, silent=True)
   boost.Require()(env)


prjs = [
   {  "name": YamlCppName(),
      "alias": "yamlcpp",
      "type": ("sharedlib" if not static_build else "staticlib"),
      "symvis": "default",
      "version": "0.5.3",
      "soname": "lib%s.so.0" % YamlCppName(),
      "install_name": "lib%s.0.dylib" % YamlCppName(),
      "defs": [] if (static_build or sys.platform != "win32") else ["YAML_CPP_DLL", "yaml_cpp_EXPORTS"],
      "cppflags": " /wd4127 /wd4355" if sys.platform == "win32" else "",
      "incdirs": ["include"],
      "srcs": glob.glob("src/*.cpp"),
      "custom": [boost.Require()],
      "install": {"include": ["include/yaml-cpp"]}
   }
]


excons.AddHelpOptions(yamlcpp="""YAMLCPP OPTIONS
  yamlcpp-static=0|1   : Toggle between static or shared library [1]
  yamlcpp-suffix=<str> : Library name suffix                     []""")

excons.DeclareTargets(env, prjs)


Export("YamlCppName YamlCppPath RequireYamlCpp")

