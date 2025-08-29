cmake_minimum_required(VERSION 4.0)
cmake_policy(SET CMP0048 NEW)
project(simple_human_description)


if(NOT DISABLE_ROS)
    find_package(ament_cmake REQUIRED)
    set(CATKIN_PACKAGE_SHARE_DESTINATION share/${PROJECT_NAME})
    configure_file(
      "${CMAKE_CURRENT_SOURCE_DIR}/cmake/robot_INSTALL_PREFIX.cmake.in"
      "${CMAKE_CURRENT_BINARY_DIR}/cmake/human_description_INSTALL_PREFIX.cmake"
      @ONLY)
    ament_package(
      CONFIG_EXTRAS
      "${CMAKE_CURRENT_BINARY_DIR}/cmake/human_description_INSTALL_PREFIX.cmake"
    )
else()
  set(CATKIN_PACKAGE_SHARE_DESTINATION share/mc_rtc/${PROJECT_NAME})

  include(CMakePackageConfigHelpers)
  set(VERSION_CONFIG
      "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
  set(PROJECT_CONFIG "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")
  set(CONFIG_INSTALL_DIR "lib/cmake/${PROJECT_NAME}")

  configure_file("${CMAKE_CURRENT_SOURCE_DIR}/cmake/ConfigVersion.cmake.in"
                 "${VERSION_CONFIG}" @ONLY)
  configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Config.cmake.in" "${PROJECT_CONFIG}"
    INSTALL_DESTINATION "${CONFIG_INSTALL_DIR}")

  install(FILES "${VERSION_CONFIG}" "${PROJECT_CONFIG}"
          DESTINATION "${CONFIG_INSTALL_DIR}")
  install(FILES package.xml DESTINATION "${CATKIN_PACKAGE_SHARE_DESTINATION}")
endif()


foreach(dir convex config launch meshes rsdf urdf)
  if(IS_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/${dir})
    install(DIRECTORY ${dir}/
            DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir})
  endif()
endforeach(dir)
