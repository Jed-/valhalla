cmake_minimum_required(VERSION 3.13)

project(valhalla
    VERSION 1.0.0
    DESCRIPTION "A first-person shooter game project which focuses on both old school and casual gameplay"
    HOMEPAGE_URL https://www.valhalla-fps.net
)

find_package(unofficial-enet CONFIG REQUIRED)
find_package(ZLIB REQUIRED)
find_package(maxminddb CONFIG REQUIRED)
find_package(SDL2 CONFIG REQUIRED)
find_package(SDL2_mixer CONFIG REQUIRED)
find_package(SDL2_image CONFIG REQUIRED)
find_package(OpenGL REQUIRED)

add_compile_options(-Wall -Wimplicit-fallthrough -fomit-frame-pointer -ffast-math -fsigned-char -fno-exceptions -fno-rtti)
add_link_options($<$<CONFIG:RELEASE>:-s>) # strip binaries in release mode

# master server
add_executable(valhalla_master
    shared/crypto.cpp
    shared/stream.cpp
    shared/tools.cpp
    engine/command.cpp
    engine/master.cpp
)
target_compile_definitions(valhalla_master PRIVATE -DSTANDALONE)
target_include_directories(valhalla_master PRIVATE shared engine)
target_link_libraries(valhalla_master PRIVATE
    unofficial::enet::enet
    ZLIB::ZLIB
)
add_custom_target(master ALL DEPENDS valhalla_master)

# server
add_executable(valhalla_server
    shared/crypto.cpp
	shared/stream.cpp
	shared/tools.cpp
	engine/command.cpp
	engine/server.cpp
	engine/worldio.cpp
	game/entity.cpp
	game/gameserver.cpp
)
target_compile_definitions(valhalla_server PRIVATE -DSTANDALONE -DHAVE_MAXMINDDB)
target_include_directories(valhalla_server PRIVATE shared engine game)
target_link_libraries(valhalla_server PRIVATE
    unofficial::enet::enet
    ZLIB::ZLIB
    maxminddb::maxminddb
)
add_custom_target(server ALL DEPENDS valhalla_server)

# client
add_executable(valhalla_client
    shared/crypto.cpp
    shared/geom.cpp
    shared/glemu.cpp
    shared/stream.cpp
    shared/tools.cpp
    shared/zip.cpp
    engine/aa.cpp
    engine/bih.cpp
    engine/blend.cpp
    engine/client.cpp
    engine/command.cpp
    engine/console.cpp
    engine/dynlight.cpp
    engine/grass.cpp
    engine/light.cpp
    engine/main.cpp
    engine/material.cpp
    engine/menus.cpp
    engine/movie.cpp
    engine/normal.cpp
    engine/octa.cpp
    engine/octaedit.cpp
    engine/octarender.cpp
    engine/physics.cpp
    engine/pvs.cpp
    engine/rendergl.cpp
    engine/renderlights.cpp
    engine/rendermodel.cpp
    engine/renderparticles.cpp
    engine/rendersky.cpp
    engine/rendertext.cpp
    engine/renderva.cpp
    engine/server.cpp
    engine/serverbrowser.cpp
    engine/shader.cpp
    engine/sound.cpp
    engine/stain.cpp
    engine/texture.cpp
    engine/ui.cpp
    engine/liquid.cpp
    engine/world.cpp
    engine/worldio.cpp
    game/ai.cpp
    game/gameclient.cpp
    game/entity.cpp
    game/game.cpp
    game/render.cpp
    game/scoreboard.cpp
    game/gameserver.cpp
    game/waypoint.cpp
    game/monster.cpp
    game/weapon.cpp
    game/gamephysics.cpp
    game/hud.cpp
    game/projectile.cpp
)
target_compile_options(valhalla_client PRIVATE)
target_include_directories(valhalla_client PRIVATE shared engine game)
target_link_libraries(valhalla_client PRIVATE
    unofficial::enet::enet
    ZLIB::ZLIB
    maxminddb::maxminddb
    $<TARGET_NAME_IF_EXISTS:SDL2::SDL2main>
    $<IF:$<TARGET_EXISTS:SDL2::SDL2>,SDL2::SDL2,SDL2::SDL2-static>
    $<IF:$<TARGET_EXISTS:SDL2_mixer::SDL2_mixer>,SDL2_mixer::SDL2_mixer,SDL2_mixer::SDL2_mixer-static>
    $<IF:$<TARGET_EXISTS:SDL2_image::SDL2_image>,SDL2_image::SDL2_image,SDL2_image::SDL2_image-static>
    OpenGL::GL
)
if(UNIX AND NOT APPLE)
    target_link_libraries(valhalla_client PRIVATE X11)
endif()
add_custom_target(client ALL DEPENDS valhalla_client)