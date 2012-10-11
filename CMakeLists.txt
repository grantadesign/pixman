add_definitions(
	-DWIN32
	-D_CRT_SECURE_NO_DEPRECATE
	-D_CRT_NONSTD_NO_DEPRECATE
	-DPACKAGE=${PIXLIB}
	-DPACKAGE_VERSION=""
	-DPACKAGE_BUGREPORT=""
    -DUSE_SSE2=1
    -DHAVE_PTHREAD_SETSPECIFIC #Get pixman to use pthread's TLS stuff instead of MSVC __declspec(thread) intrinsics, which don't work on XP.
    #-DUSE_X86_MMX=1 #x64 compiler has problems with MMX.
)

add_subdirectory(pixman)
add_subdirectory(test)