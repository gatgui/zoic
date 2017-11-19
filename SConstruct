import os
import sys
import glob
import excons
from excons.tools import arnold


version = "2.0.0"

env = excons.MakeBaseEnv()

if sys.platform != "win32":
    env.Append(CPPFLAGS=" -Wno-unused-parameter -Wno-unused-variable")

defs = []
incdirs = []
libdirs = []
libs = []

# Zeno specific flags
if excons.GetArgument("draw", 0, int) != 0:
    defs.append("_DRAW")
if excons.GetArgument("work", 0, int) != 0:
    defs.append("_WORK")
if excons.GetArgument("macbook", 0, int) != 0:
    defs.append("_MACBOOK")

# Arnold 4.2.9.0 provides api AiTextureLoad to read texture data
# Arnold 4.2.10.0 adds a new parameter to the function above
arniver = arnold.Version(asString=False)
if arniver[0] < 4 or (arniver[0] == 4 and (arniver[1] < 2 or (arniver[1] == 2 and arniver[2] < 10))):
    print("Arnold 4.2.10.0 at least required")
    sys.exit(1)

arnicompat = "%d.%d" % (arniver[0], arniver[1])

zoic = {"name": "zoic",
        "type": "dynamicmodule",
        "prefix": "arnold/%s" % arnicompat,
        "ext": arnold.PluginExt(),
        "srcs": ["src/zoic.cpp"],
        "defs": defs,
        "incdirs": incdirs,
        "libdirs": libdirs,
        "libs": libs,
        "custom": [arnold.Require]}

targets = excons.DeclareTargets(env, [zoic])

out_prefix = excons.OutputBaseDirectory() + "/"

targets["mtd"] = env.Install(out_prefix + "arnold/" + arnicompat, "src/zoic.mtd")
targets["maya"]  = env.Install(out_prefix + "maya", glob.glob("maya/*"))
targets["c4d"]  = env.Install(out_prefix + "c4d", glob.glob("c4d/*"))
targets["ldata"] = env.Install(out_prefix + "data/lenses", glob.glob("lenses_tabular/*.dat"))
targets["bdata"] = env.Install(out_prefix + "data/bokeh", glob.glob("bokeh_images/*.jpg"))

env.Depends(targets["zoic"], targets["mtd"])
env.Depends(targets["zoic"], targets["maya"])
env.Depends(targets["zoic"], targets["c4d"])
env.Depends(targets["zoic"], targets["ldata"])
env.Depends(targets["zoic"], targets["bdata"])

excons.EcosystemDist(env, "zoic.env",
                     {"zoic": "/arnold/%s/%s" % (arnicompat, excons.EcosystemPlatform()),
                      "mtd": "/arnold/%s/%s" % (arnicompat, excons.EcosystemPlatform()),
                      "maya": "/maya",
                      "c4d": "/c4d",
                      "ldata": "/data/lenses",
                      "bdata": "/data/bokeh"},
                     targets=targets)

Default(targets["zoic"])
