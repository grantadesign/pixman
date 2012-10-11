#CMake file for pixman library
set(PIXLIB "pixman")

set(PIXMAN_SRCS #From Makefile.sources
	pixman.c			
	pixman-access.c			
	pixman-access-accessors.c	
	pixman-bits-image.c		
	pixman-combine32.c		
	pixman-combine64.c		
	pixman-conical-gradient.c	
	pixman-cpu.c			
	pixman-edge.c			
	pixman-edge-accessors.c		
	pixman-fast-path.c		
	pixman-general.c		
	pixman-gradient-walker.c	
	pixman-image.c			
	pixman-implementation.c		
	pixman-linear-gradient.c	
	pixman-matrix.c			
	pixman-noop.c			
	pixman-radial-gradient.c	
	pixman-region16.c		
	pixman-region32.c		
	pixman-solid-fill.c		
	pixman-timer.c			
	pixman-trap.c			
	pixman-utils.c
)

set(PIXMAN_HDRS #From Makefile.sources
	pixman.h			
	pixman-accessor.h		
	pixman-combine32.h		
	pixman-combine64.h		
	pixman-compiler.h		
	pixman-edge-imp.h		
	pixman-inlines.h		
	pixman-private.h		
)

set(PIXMAN_SSE_SRCS
    pixman-sse2.c
)

find_package(PERL)

#TODO (the $(RM) commands)?
add_custom_command(OUTPUT ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman/pixman-combine32.c
	COMMAND ${PERL_EXECUTABLE} ARGS ./make-combine.pl 8 < pixman-combine.c.template > pixman-combine32.c #|| ($(RM) pixman-combine32.c; exit 1)
	WORKING_DIRECTORY ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman
)
add_custom_command(OUTPUT ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman/pixman-combine32.h
	COMMAND ${PERL_EXECUTABLE} ./make-combine.pl 8 < pixman-combine.h.template > pixman-combine32.h #|| ($(RM) pixman-combine32.h; exit 1)
	WORKING_DIRECTORY ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman
)
add_custom_command(OUTPUT ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman/pixman-combine64.c
	COMMAND ${PERL_EXECUTABLE} ./make-combine.pl 16 < pixman-combine.c.template > pixman-combine64.c #|| ($(RM) pixman-combine64.c; exit 1)
	WORKING_DIRECTORY ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman
)
add_custom_command(OUTPUT ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman/pixman-combine64.h
	COMMAND ${PERL_EXECUTABLE} ./make-combine.pl 16 < pixman-combine.h.template > pixman-combine64.h #|| ($(RM) pixman-combine64.h; exit 1)
	WORKING_DIRECTORY ${GATEWAYDEPS_SOURCE_DIR}/pixman/pixman
)

set(PIXMAN_COMBINE_SRCS pixman-combine32.c pixman-combine64.c pixman-combine32.h pixman-combine64.h)

add_custom_target(update_combines ALL
	DEPENDS ${PIXMAN_COMBINE_SRCS}
)
SET_PROPERTY(TARGET update_combines PROPERTY FOLDER "pixman")

set_source_files_properties(${PIXMAN_COMBINE_SRCS} pixman-combine32.c PROPERTIES GENERATED TRUE)

# pixman-combine32.c: pixman-combine.c.template pixman-combine32.h make-combine.pl
	# perl ./make-combine.pl 8 < $< > $@ || ($(RM) $@; exit 1)
# pixman-combine32.h: pixman-combine.h.template make-combine.pl
	# perl ./make-combine.pl 8 < $< > $@ || ($(RM) $@; exit 1)

# pixman-combine64.c: pixman-combine.c.template pixman-combine64.h make-combine.pl
	# perl ./make-combine.pl 16 < $< > $@ || ($(RM) $@; exit 1)
# pixman-combine64.h: pixman-combine.h.template make-combine.pl
	# perl ./make-combine.pl 16 < $< > $@ || ($(RM) $@; exit 1)

include_directories(.
                    ${GATEWAYDEPS_SOURCE_DIR}/pthreads)

add_library(${PIXLIB} STATIC ${PIXMAN_SRCS} ${PIXMAN_SSE_SRCS} ${PIXMAN_HDRS})
SET_PROPERTY(TARGET ${PIXLIB} PROPERTY FOLDER "pixman")
add_dependencies(${PIXLIB} update_combines)