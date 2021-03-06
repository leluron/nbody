find_package(GLM REQUIRED)
find_package(GLFW REQUIRED)
find_package(GLEW)
set(OpenGL_GL_PREFERENCE GLVND)
find_package(OpenGL)
find_package(Vulkan)

set(COMMON_SOURCE 
	nbody.cpp 
	camera.cpp 
	sim_param.cpp 
	gen.cpp)

set(OPENGL_SOURCE 
	renderer_gl.cpp 
	shader.cpp)

set(VULKAN_SOURCE 
	renderer_vk.cpp 
	vulkan_app.cpp)

project (nbody)

if (GLEW_FOUND AND OPENGL_FOUND)
	add_executable(nbodygl ${COMMON_SOURCE} ${OPENGL_SOURCE})
	target_compile_definitions(nbodygl PRIVATE -DUSE_OPENGL)

	target_compile_features(nbodygl PRIVATE cxx_auto_type cxx_nullptr cxx_range_for)

	target_include_directories(nbodygl PRIVATE 
		${GLM_INCLUDE_DIRS}
		${GLFW_INCLUDE_DIR}
		${GLEW_INCLUDE_DIR}
		${OPENGL_INCLUDE_DIR})

	target_link_libraries(nbodygl ${GLFW_LIBRARIES} ${GLEW_LIBRARY} ${OPENGL_LIBRARY})
endif()

if (VULKAN_FOUND)
	add_executable(nbodyvk ${COMMON_SOURCE} ${VULKAN_SOURCE})
	set(COMPILE_DEFS -DUSE_VULKAN)

	if (CMAKE_BUILD_TYPE MATCHES Debug)
		message("Validation layers activated")
		set(COMPILE_DEFS ${COMPILE_DEFS} -DUSE_VALIDATION_LAYERS)
	endif()

	target_compile_definitions(nbodyvk PRIVATE ${COMPILE_DEFS})

	target_compile_features(nbodyvk PRIVATE cxx_auto_type cxx_nullptr cxx_range_for)

	target_include_directories(nbodyvk PRIVATE 
		${GLM_INCLUDE_DIRS}
		${GLFW_INCLUDE_DIR}
		${VULKAN_INCLUDE_DIR})

	target_link_libraries(nbodyvk ${GLFW_LIBRARIES} ${VULKAN_LIBRARY})
endif()