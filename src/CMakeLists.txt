add_subdirectory(lua)
add_subdirectory(tinyxml)
add_subdirectory(raknet)

add_definitions(-DTIXML_USE_STL)
include_directories(.)

set(common_SRC	
	Global.h
	Clock.cpp Clock.h
	DuelMatch.cpp DuelMatch.h
	ReplayRecorder.cpp ReplayRecorder.h
	NetworkGame.cpp NetworkGame.h
	NetworkMessage.cpp NetworkMessage.h
	GameLogic.cpp GameLogic.h
	PhysicWorld.cpp PhysicWorld.h
	SpeedController.cpp SpeedController.h
	UserConfig.cpp UserConfig.h
	)
	
set (blobby_SRC ${common_SRC}
	Blood.cpp Blood.h
	TextManager.cpp TextManager.h
	main.cpp
	IMGUI.cpp IMGUI.h
	InputDevice.cpp InputDevice.h
	InputManager.cpp InputManager.h
	InputSource.cpp InputSource.h
	LocalInputSource.h
	Player.cpp Player.h
	RenderManager.cpp RenderManager.h
	RenderManagerGL2D.cpp RenderManagerGL2D.h
	RenderManagerGP2X.cpp RenderManagerGP2X.h
	RenderManagerSDL.cpp RenderManagerSDL.h
	ScriptedInputSource.cpp ScriptedInputSource.h	
	BotAPICalculations.cpp BotAPICalculations.h
	SoundManager.cpp SoundManager.h
	Vector.h
	state/State.cpp state/State.h
	state/LocalGameState.cpp state/LocalGameState.h
	state/NetworkState.cpp state/NetworkState.h
	state/OptionsState.cpp state/OptionsState.h
	)
	
set (blobby-server_SRC ${common_SRC}
	DedicatedServer.cpp DedicatedServer.h
	)

find_package(Boost REQUIRED)
find_package(PhysFS REQUIRED)
find_package(SDL REQUIRED)
find_package(OpenGL)

include_directories(
	${Boost_INCLUDE_DIR} ${PHYSFS_INCLUDE_DIR}
	${SDL_INCLUDE_DIR} ${OPENGL_INCLUDE_DIR}
)

if (OPENGL_FOUND)
	add_definitions(-DHAVE_LIBGL)
endif (OPENGL_FOUND)

if (CMAKE_SYSTEM_NAME STREQUAL Windows)
	set(RAKNET_LIBRARIES raknet ws2_32)
else (CMAKE_SYSTEM_NAME STREQUAL Windows)
	set(RAKNET_LIBRARIES raknet)
endif (CMAKE_SYSTEM_NAME STREQUAL Windows)

add_executable(blobby ${blobby_SRC})
target_link_libraries(blobby lua raknet tinyxml ${RAKNET_LIBRARIES} ${PHYSFS_LIBRARY} ${SDL_LIBRARY} ${OPENGL_LIBRARIES})

if (UNIX)
	add_executable(blobby-server ${blobby-server_SRC})
	target_link_libraries(blobby-server lua raknet tinyxml ${RAKNET_LIBRARIES} ${PHYSFS_LIBRARY} ${SDL_LIBRARY})
endif (UNIX)

if (CMAKE_SYSTEM_NAME STREQUAL Windows)
	set_target_properties(blobby PROPERTIES LINK_FLAGS "-mwindows") # disable the console window
endif (CMAKE_SYSTEM_NAME STREQUAL Windows)

if (WIN32)
	install(TARGETS blobby DESTINATION .)
elseif (UNIX)
	install(TARGETS blobby blobby-server DESTINATION bin)
endif (WIN32)
