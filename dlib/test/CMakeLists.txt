#
# This is a CMake makefile.  You can find the cmake utility and
# information about it at http://www.cmake.org
#

cmake_minimum_required(VERSION 3.8.0)

# create a variable called target_name and set it to the string "dtest"
set (target_name dtest)
PROJECT(${target_name})

# compile the dlib/all/source.cpp file into its own object just to make sure it compiles
set(DLIB_TEST_COMPILE_ALL_SOURCE_CPP ON)

add_subdirectory(.. dlib_build)

# This variable contains a list of all the tests we are building
# into the regression test suite.
set (tests
   example.cpp
   active_learning.cpp
   any.cpp
   any_function.cpp
   array2d.cpp
   array.cpp
   assignment_learning.cpp
   base64.cpp
   bayes_nets.cpp
   bigint.cpp
   binary_search_tree_kernel_1a.cpp
   binary_search_tree_kernel_2a.cpp
   binary_search_tree_mm1.cpp
   binary_search_tree_mm2.cpp
   bridge.cpp
   bsp.cpp
   byte_orderer.cpp
   cca.cpp
   clustering.cpp
   cmd_line_parser.cpp
   cmd_line_parser_wchar_t.cpp
   compress_stream.cpp
   conditioning_class_c.cpp
   conditioning_class.cpp
   config_reader.cpp
   constexpr_if.cpp
   correlation_tracker.cpp
   crc32.cpp
   create_iris_datafile.cpp
   data_io.cpp
   directed_graph.cpp
   discriminant_pca.cpp
   disjoint_subsets.cpp
   disjoint_subsets_sized.cpp
   ekm_and_lisf.cpp
   empirical_kernel_map.cpp
   entropy_coder.cpp
   entropy_encoder_model.cpp
   example_args.cpp
   face.cpp
   fft.cpp
   fhog.cpp
   filtering.cpp
   find_max_factor_graph_nmplp.cpp
   find_max_factor_graph_viterbi.cpp
   geometry.cpp
   graph.cpp
   graph_cuts.cpp
   graph_labeler.cpp
   hash.cpp
   hash_map.cpp
   hash_set.cpp
   hash_table.cpp
   hog_image.cpp
   image.cpp
   invoke.cpp
   iosockstream.cpp
   is_same_object.cpp
   isotonic_regression.cpp
   kcentroid.cpp
   kernel_matrix.cpp
   kmeans.cpp
   learning_to_track.cpp
   least_squares.cpp
   linear_manifold_regularizer.cpp
   lspi.cpp
   lz77_buffer.cpp
   map.cpp
   math.cpp
   matrix2.cpp
   matrix3.cpp
   matrix4.cpp
   matrix_chol.cpp
   matrix.cpp
   matrix_eig.cpp
   matrix_lu.cpp
   matrix_qr.cpp
   max_cost_assignment.cpp
   max_sum_submatrix.cpp
   md5.cpp
   member_function_pointer.cpp
   metaprogramming.cpp
   mpc.cpp
   multithreaded_object.cpp
   numerical_integration.cpp
   object_detector.cpp
   oca.cpp
   one_vs_all_trainer.cpp
   one_vs_one_trainer.cpp
   optimization.cpp
   optimization_test_functions.cpp
   global_optimization.cpp
   opt_qp_solver.cpp
   parallel_for.cpp
   parse.cpp
   pipe.cpp
   pixel.cpp
   probabilistic.cpp
   pyramid_down.cpp
   queue.cpp
   rand.cpp
   ranking.cpp
   read_write_mutex.cpp
   reference_counter.cpp
   rls.cpp
   random_forest.cpp
   sammon.cpp
   scan_image.cpp
   sequence.cpp
   sequence_labeler.cpp
   sequence_segmenter.cpp
   serialize.cpp
   set.cpp
   sldf.cpp
   sliding_buffer.cpp
   sockets2.cpp
   sockets.cpp
   sockstreambuf.cpp
   sparse_vector.cpp
   stack.cpp
   static_map.cpp
   static_set.cpp
   statistics.cpp
   std_vector_c.cpp
   string.cpp
   svm_c_linear.cpp
   svm_c_linear_dcd.cpp
   svm.cpp
   svm_multiclass_linear.cpp
   svm_struct.cpp
   svr_linear_trainer.cpp
   symmetric_matrix_cache.cpp
   thread_pool.cpp
   threads.cpp
   timer.cpp
   tokenizer.cpp
   trust_region.cpp
   tuple.cpp
   type_safe_union.cpp
   vectorstream.cpp
   dnn.cpp
   cublas.cpp
   find_optimal_parameters.cpp
   elastic_net.cpp
   te.cpp
   )


# add all the cpp files we want to compile to this list.  This tells
# cmake that they are part of our target (which is the executable named dtest)
ADD_EXECUTABLE(${target_name} main.cpp tester.cpp ${tests})

if (CMAKE_COMPILER_IS_GNUCXX)
   # Turn on all warnings, and treat them as errors.
   add_compile_options(-W -Wall -Wextra -Wpedantic -Werror)
   add_compile_options(-fdiagnostics-color=always)
   # I don't care about unused testing functions though.  I like to keep them
   # around.  Don't warn about it.
   add_compile_options(-Wno-unused-function)
   add_compile_options(-Wno-strict-overflow)
   add_compile_options(-Wno-maybe-uninitialized)

   if (CMAKE_CXX_COMPILER_VERSION VERSION_EQUAL 11.3.0)
      # GCC 11.3 wrongly yields this warning:
      #  dlib::row_major_layout::layout<double, 0, 2, dlib::memory_manager_stateless_kernel_1<char> >::data’ with nonzero offset 8 [-Werror=free-nonheap-object]
      #   61 |                 delete [] item;
      # Which by inspection of the dlib::row_major_layout::layout class you can see is clearly incorrect, as `data` is most
      # definitely heap allocated.
      add_compile_options(-Wno-free-nonheap-object)
   endif()

elseif (MSVC)
   # Treat warnings as errors.
   add_compile_options(/WX)
else() # basically Clang
   # Treat warnings as errors, but do not turn on all warnings.
   add_compile_options(-W -Werror)
   # This is for the comment in face_detection_ex.cpp that says "faces/*.jpg"
   add_compile_options(-Wno-comment)
endif()


TARGET_LINK_LIBRARIES(${target_name} dlib::dlib )


if (NOT DLIB_NO_GUI_SUPPORT)
   add_subdirectory(gui)
   add_subdirectory(tools)

   if (CMAKE_COMPILER_IS_GNUCXX)
      # The tutorials set some variables that are not used.
      add_definitions("-Wno-unused-but-set-variable")
   endif()

   add_subdirectory(examples)
endif()
