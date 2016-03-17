file(MAKE_DIRECTORY ${PROJECT_BINARY_DIR}/storage)

set(STORAGE_SRC
  storage/simple_object_store.cc
  storage/stub_object_store.cc
  )

if (${ENABLE_HDFS})
  set(STORAGE_SRC
    ${STORAGE_SRC}
    storage/hdfs_data_locality_manager.cc
    )
endif (${ENABLE_HDFS})

set(STORAGE_TESTS
  storage/references_test.cc
  storage/stub_object_store_test.cc
)

###############################################################################
# Unit tests

foreach(T IN ITEMS ${STORAGE_TESTS})
  get_filename_component(TEST_NAME ${T} NAME_WE)
  add_executable(${TEST_NAME} ${T}
    $<TARGET_OBJECTS:base>
    $<TARGET_OBJECTS:misc>
    $<TARGET_OBJECTS:storage>)
  target_link_libraries(${TEST_NAME}
    ${spooky-hash_BINARY} ${gtest_LIBRARY} ${gtest_MAIN_LIBRARY}
    ${libhdfs3_LIBRARY} ${Firmament_SHARED_LIBRARIES} glog gflags protobuf)
  if (${ENABLE_HDFS})
    target_link_libraries(${TEST_NAME} ${libhdfs3_LIBRARY})
  endif (${ENABLE_HDFS})
  add_test(${TEST_NAME} ${TEST_NAME})
endforeach(T)