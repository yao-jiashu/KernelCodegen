file(GLOB compute_dag_src ./ComputeDAG/*.cc)
file(GLOB graph_tune_src ./GraphTune/*.cc)
file(GLOB scheduler_src ./Scheduler/*.cc)
file(GLOB auto_tune_src ./AutoTune/*.cc)
file(GLOB codegen_src ./CodeGen/*.cc)

# ComputeDAG dialect
add_subdirectory(IR)

add_library(kcm_runtime 
            ${compute_dag_src}
            ${graph_tune_src} 
            ${scheduler_src} 
            ${auto_tune_src} 
            ${codegen_src}
            KernelCodegen.cc)

#target_compile_options(kcm_runtime PRIVATE -frtti)
# MLIR有自己的一套RTTI，需要关掉才能正确继承MLIR中类
target_compile_options(kcm_runtime PUBLIC -fno-rtti)

set(LLVM_LINK_COMPONENTS
        Core
        Support
        nativecodegen
        OrcJIT
        )

include_directories(${CMAKE_CURRENT_BINARY_DIR})
include_directories(${CMAKE_CURRENT_BINARY_DIR}/include/)
get_property(dialect_libs GLOBAL PROPERTY MLIR_DIALECT_LIBS)
get_property(conversion_libs GLOBAL PROPERTY MLIR_CONVERSION_LIBS)
get_property(translation_libs GLOBAL PROPERTY MLIR_TRANSLATION_LIBS)

target_link_libraries(kcm_runtime
        PUBLIC
        ${dialect_libs}
        ${conversion_libs}
        ${translation_libs}
        MLIRAnalysis
        MLIRCallInterfaces
        MLIRCastInterfaces
        MLIRExecutionEngine
        MLIRIR
        MLIRLLVMCommonConversion
        MLIRLLVMToLLVMIRTranslation
        MLIRMemRefDialect
        MLIRParser
        MLIRPass
        MLIRSideEffectInterfaces
        MLIRTargetLLVMIRExport
        MLIRTransforms
        MLIRNVVMToLLVMIRTranslation
        MLIRToLLVMIRTranslationRegistration
        MLIRTargetLLVMIRImport
        MLIRTargetLLVMIRExport
        MLIRFuncToLLVM
        # MLIREnhance
        MLIRComputeDAG
        )