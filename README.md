# tolua_runtime + lua-protobuf
**protobuf的lua支持比较好的有pbc和protoc-gen-lua，但是两者都只支持protobuf 2，而且有不支持消息嵌套、不支持int64及性能方面的问题。<br>
Now,the tolua_runtime-protobuf3,it both support protobuf version 2 and 3 syntax and semantics.**
## Libs
**tolua_runtime**<br>
https://github.com/topameng/tolua_runtime<br>
**lua-protobuf**<br>
https://github.com/starwing/lua-protobuf<br>
## Build
pc: build_win32.sh build_win64.h  (mingw32/mingw64 + luajit2.1) <br>
android: build_arm.sh build_x86.sh (mingw32 + luajit2.1) <br>
mac: build_osx.sh (xcode + luac5.1.5 for luajit can't run on unity5) <br>
ios: build_ios.sh (xcode + luajit2.1) <br>
<br>
安装msys2，其包含mingw32和mingw64，用它执行相应的shell脚本，即可编译出dll和so库。参考：<br>
https://www.jianshu.com/p/5a35602adef8<br>
https://blog.csdn.net/yhjsspz/article/details/79884792<br>
## Example
C#:

```
#if !UNITY_EDITOR && UNITY_IPHONE
        const string LUADLL = "__Internal";
#else
        const string LUADLL = "tolua";
#endif

[DllImport(LUADLL, CallingConvention = CallingConvention.Cdecl)]
public static extern int luaopen_luapb(IntPtr L);

// LuaState OpenLibs
m_LuaState.OpenLibs(LuaDLL.luaopen_luapb);
```
<br>

lua(pb):

```
    local luapb = require "luapb"

    assert(luapb.loadfile "F:\\Main\\Assets\\lua\\addressbook.proto.pb")  

    local person = { 
    name = "Alice",
    id = 12345,
    phones = {
            { number = "1301234567" },
            { number = "87654321", type = "WORK" },
        }
    }

    local data = assert(luapb.encode("tutorial.Person", person))
    local msg = assert(luapb.decode("tutorial.Person", data))
 
    log(require "serpent".block(msg))
```

lua(proto):

```
    local protoc = require "protoc"
    protoc:addpath('Assets/Lua')

    assert(protoc:loadfile "addressbook.proto")

    local person = { 
    name = "Alice",
    id = 12345,
    phones = {
            { number = "1301234567" },
            { number = "87654321", type = "WORK" },
        }
    }

    local data = assert(luapb.encode("tutorial.Person", person))
    local msg = assert(luapb.decode("tutorial.Person", data))

    log(require "serpent".block(msg))
```

