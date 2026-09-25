# Hurrican 3D

[![Sponsor](https://img.shields.io/badge/Sponsor-SgtBilko76-ea4aaa?logo=githubsponsors&logoColor=white)](https://github.com/sponsors/SgtBilko76)

A standalone Meta Quest port (Quest 2 / 3 / 3S / Pro) of [Hurrican](https://www.winterworks.de/project/hurrican/), the freeware run-and-gun inspired by Turrican.

The game is played on a large world-locked virtual screen. Its draw layers (sky, parallax planes, tiles, playfield, overlays, HUD) are composited per eye at different depths for a stereoscopic "diorama" look. The Touch controllers are fully mapped, with rumble, and can be remapped in *Options → Define Buttons*.

## Install

1. Enable developer mode on your Quest.
2. Download the APK from the [Releases](../../releases) page.
3. Sideload it with [SideQuest](https://sidequestvr.com/) or `adb install -r <file>.apk`.
4. Launch it from *Library → Unknown Sources*.

All game data is inside the APK. The APK is signed with a debug key, so uninstall any previous differently-signed build first.

## Controls

| Input | Action |
|---|---|
| Left stick | Move / look up / duck |
| Right stick | Look up / down |
| A | Jump |
| Right trigger | Shoot |
| B | Lightning |
| X | Powerline |
| Y | Grenade |
| Left trigger | Smart bomb |
| Right grip | Cycle weapon |
| Menu (left controller) | Pause / back |
| Click both sticks | Recenter the screen |

## Tuning

Screen size and distance, 3D depth strength and refresh rate are set in `files/vr.cfg` in the app's private storage. Delete the file to restore the defaults. The default depth strength is 0.10.

Build instructions are in the *Building for Meta Quest* section below.

---

## Original Hurrican README

> Below is the upstream project's README, kept for credits, licensing and desktop build instructions.


[![CI build](https://github.com/HurricanGame/Hurrican/actions/workflows/build.yml/badge.svg)](https://github.com/HurricanGame/Hurrican/actions/workflows/build.yml)

A fork of Hurrican, freeware jump and shoot game created by Poke53280, with SDL2 enabled by default, support for libopenmpt and CRT simulation.
Also uses XDG compliant data/config paths on UNIX.
Additional userlevels from http://turricanforever.de included.

Original code by Eiswuxe (Poke53280) [[Winterworks](https://www.winterworks.de/project/hurrican/)]  
Further work by [Pickle136](https://sourceforge.net/projects/hurrican/), Stefan Schmidt ([thrimbor](https://github.com/thrimbor/Hurrican)) and Leandro Nini ([drfiemost](https://github.com/drfiemost/Hurrican))  
CRT simulation partially based on [CRT effect - Shadertoy, Unity](https://luka712.github.io/2018/07/21/CRT-effect-Shadertoy-Unity/) article from luka712's blog

![screenshot](https://github.com/HurricanGame/Hurrican/wiki/images/level1.png)

---

#### Dependencies

The code depends on SDL2 (or the old deprecated SDL) with the image and mixer components, and libepoxy.
Optionally libopenmpt can be used for the music in place of the standard from SDL_mixer (see below).
A compiler with c++17 support is required.

#### Building

The code can be built using cmake (tested on Linux and MinGW)

    git clone --recurse-submodules https://github.com/HurricanGame/Hurrican.git
    cd Hurrican/Hurrican
    mkdir build && cd build
    cmake -DCMAKE_BUILD_TYPE=Release ..
    cmake --build .

The following build options are available:

Predefined platforms
* -DPLATFORM=<RPI|GCW|RDU|RDU2|PANDORA> : Compile for the specified platform, setting the correct options

OpenGL Options
* -DRENDERER=GL1          : Use the OpenGL 1.X code (fixed pipline)
* -DRENDERER=GLES1        : Use the OpenGL 1.X code with ES compatible
* -DRENDERER=GL2          : Use the OpenGL 2.0 code (programable pipline) [this is the default if not specified]
* -DRENDERER=GLES2        : Use the OpenGL 2.0 code with ES compatible
* -DRENDERER=GL3          : Use the OpenGL 3.0 code (programable pipline)
* -DRENDERER=GLES3        : Use the OpenGL 3.0 code with ES compatible
* -DFBO=ON                : Add FBO support, allow screen to be scaled to arbitrary dimensions, available only with GL2 or GL3 and enabled by default
* -DDEFAULT_SCREENBPP=<16|24|32> : Set the default screen depth, 32 if not specified

Sound
* -DOPENMPT=ON            : Use the libopenmpt code for music (SDL2_mixer uses libmodplug while SDL_mixer uses the lower quality mikmod engine)

Generic
* -DFAST_RANDOM=OFF             : Use standard C random function in place of the fast [LCG](https://en.wikipedia.org/wiki/Linear_congruential_generator)
* -DFAST_TRIG=ON                : Use fast approximation for trigonometric functions
* -DDISABLE_EXCEPTIONS=ON       : Disable exception handling to reduce binary size
* -DUSE_PRECOMPILED_HEADERS=ON  : Enable pre-compiled headers for better compile time

Debug
* -DDISABLE_MEMPOOLING=ON : Bypass pooled memory manager
* -DCMAKE_BUILD_TYPE=<Asan|Ubsan>: Enable the Address or Undefined Behaviour Sanitizer

#### Building for Meta Quest (standalone VR APK)

The `Hurrican/android` Gradle project builds a standalone Quest APK: the game is presented on a
large virtual screen whose draw layers (sky, parallax planes, tiles, sprites, overlays, HUD) are
composited per eye at different depths for a stereoscopic "diorama" effect, and the Touch
controllers are mapped to the game (remappable in *Options → Define Buttons*).

Requirements: Android SDK with NDK 27, CMake 3.22 (SDK component), JDK 17, a Quest 2/3/Pro.
SDL2, SDL2_image and SDL2_mixer (with libxmp for the tracker music) are built from the
submodules; the OpenXR loader comes from the Khronos Maven AAR via prefab.

    git clone --recurse-submodules https://github.com/HurricanGame/Hurrican.git
    cd Hurrican/Hurrican/android
    ./gradlew assembleQuestDebug               # or assemblePicoDebug for PICO 4 / Neo 3 headsets;
                                               # -PhurricanPlatform=ANDROID for a flat, non-VR test build
    adb install -r app/build/outputs/apk/quest/debug/app-quest-debug.apk

The game data is packed into the APK and extracted to the app's private storage on first
start. Settings, savegames and `vr.cfg` (virtual screen size/distance, depth strength,
refresh rate) live there too:
`/sdcard/Android/data/com.hurricangame.quest/files` is *not* used; use
`adb shell run-as com.hurricangame.quest` to inspect `files/`.

Default controls: left stick = move / look up / duck, right stick = look up/down,
A = jump, right trigger = shoot, B = lightning, X = powerline, Y = grenade,
left trigger = smart bomb, right grip = cycle weapon, Menu (left controller) = pause / back,
click both sticks = recenter the screen.

#### Running

To launch Hurrican, go back under the Hurrican folder

    cd ..
    ./build/hurrican

To see the available command line options use the `--help` argument

    ./build/hurrican --help

or check online at https://github.com/HurricanGame/Hurrican/wiki/Help
