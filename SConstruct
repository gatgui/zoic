import sys
import excons
from excons.tools import arnold

excons.SetArgument("use-c++11", 1)

env = excons.MakeBaseEnv()

# Arnold 4.2.9.0 provides api to read texture data

defs = []
incdirs = []
libdirs = []
libs = []

arniver = arnold.Version()
if arniver[0] < 4 or (arniver[0] == 4 and (arniver[1] < 2 or (arniver[1] == 2 and arniver[3] < 9))):
    oiio_inc, oiio_lib = excons.GetDirs("oiio", noexc=False)
    incdirs.append(oiio_inc)
    libdirs.append(oiio_lib)
    libs.append("OpenImageIO")
else:
    defs.append("NO_OIIO")

zoic = {"name": "zoic",
        "type": "dynamicmodule",
        "ext": arnold.PluginExt(),
        "srcs": ["src/zoic.cpp"],
        "defs": defs,
        "incdirs": incdirs,
        "libdirs": libdirs,
        "libs": libs,
        "install": {"": ["bin/zoic.mtd"]},
        "custom": [arnold.Require]}

excons.DeclareTargets(env, [zoic])
