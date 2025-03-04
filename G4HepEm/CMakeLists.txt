# - Build component libraries
add_subdirectory(G4HepEmData)
add_subdirectory(G4HepEmDataJsonIO)
add_subdirectory(G4HepEmInit)
add_subdirectory(G4HepEmRun)

# - Now top level G4HepEm library
set(G4HEPEM_headers
  include/G4HepEmCLHEPRandomEngine.hh
  include/G4HepEmNoProcess.hh
  include/G4HepEmProcess.hh
  include/G4HepEmRunManager.hh
)
set(G4HEPEM_sources
  src/G4HepEmProcess.cc
  src/G4HepEmRunManager.cc
)

if(Geant4_VERSION VERSION_GREATER_EQUAL 11.0)
  set(G4HEPEM_headers ${G4HEPEM_headers}
    include/G4EmTrackingManager.hh
    include/HepEmTrackingManager.hh
  )
  set(G4HEPEM_sources ${G4HEPEM_sources}
    src/G4EmTrackingManager.cc
    src/HepEmTrackingManager.cc
  )
endif()

if(BUILD_STATIC_LIBS)
  add_library(g4HepEm STATIC ${G4HEPEM_sources})
else()
  add_library(g4HepEm SHARED ${G4HEPEM_sources})
endif()

add_library(${PROJECT_NAME}::g4HepEm ALIAS g4HepEm)

target_compile_features(g4HepEm PUBLIC cxx_std_${CMAKE_CXX_STANDARD})

target_include_directories(g4HepEm PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}>)

target_link_libraries(g4HepEm PUBLIC g4HepEmData g4HepEmInit g4HepEmRun) # ${Geant4_LIBRARIES})

## ----------------------------------------------------------------------------
## Install G4HepEm libraries and headers
install(FILES ${G4HEPEM_headers} DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}")
install(TARGETS g4HepEm
  EXPORT ${PROJECT_NAME}Targets
  ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
  LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
  RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}")
