# Makefile: mingw makefile for stasm

# Note: high_gui is needed for the apps but not for the Stasm library per se.

LIBRARIES=\
 -lopencv_core$(OPENCV_N)\
 -lopencv_highgui$(OPENCV_N)\
 -lopencv_imgproc$(OPENCV_N)\
 -lopencv_objdetect$(OPENCV_N)

# -Wno-long-long        disables the "long long warnings" for the OpenCV headers
# -Wno-unused-parameter allows virtual func defs that don't use all params
# -Wno-unknown-pragmas  allows OpenMP pragmas without complaint

CFLAGS=-O3 -Wall -Wextra -pedantic\
 -Wno-long-long -Wno-unused-parameter -Wno-unknown-pragmas -Wstrict-aliasing

INCLUDES=-I../stasm  -I../apps

CXX=g++

vpath %.cpp ../stasm ../stasm/MOD_1 ../apps ../apps/swas ../apps/shapefile ../tasm/src

SRC=\
 facedet.cpp\
 initasm.cpp\
 asm.cpp\
 classicdesc.cpp\
 convshape.cpp\
 err.cpp\
 eyedet.cpp\
 eyedist.cpp\
 faceroi.cpp\
 hat.cpp\
 hatdesc.cpp\
 landmarks.cpp\
 misc.cpp\
 pinstart.cpp\
 print.cpp\
 shape17.cpp\
 shapehacks.cpp\
 shapemod.cpp\
 startshape.cpp\
 stasm.cpp\
 stasm_lib.cpp

OBJFILES=$(patsubst %.cpp,obj/%.o,$(SRC))

OBJFILES_MINIMAL=\
	$(OBJFILES) $(patsubst %.cpp,obj/%.o,minimal.cpp)

OBJFILES_MINIMAL2=\
	$(OBJFILES) $(patsubst %.cpp,obj/%.o,minimal2.cpp)

OBJFILES_TEST_STASM_LIB=\
	$(OBJFILES) $(patsubst %.cpp,obj/%.o,test_stasm_lib.cpp)

OBJFILES_TEST_STASM_LIB_ERR=\
	$(OBJFILES) $(patsubst %.cpp,obj/%.o,test_stasm_lib_err.cpp)

OBJFILES_STASM_MAIN=\
	$(OBJFILES) $(patsubst %.cpp,obj/%.o,stasm_main.cpp appmisc.cpp)

OBJFILES_SWAS=\
	$(OBJFILES) $(patsubst %.cpp,obj/%.o,swas.cpp \
 appmisc.cpp shapefile.cpp stasm_regex.cpp fitmeas.cpp fm29.cpp)

OBJFILES_TASM=\
	$(OBJFILES) $(patsubst %.cpp,obj/%.o,\
 appmisc.cpp shapefile.cpp stasm_regex.cpp fitmeas.cpp fm29.cpp \
 appmem.cpp linsolve.cpp \
 tasm.cpp tasmalignshapes.cpp tasmclassic.cpp tasmconf.cpp \
 tasmdescmod.cpp tasmdraw.cpp tasmhat.cpp tasmimpute.cpp tasmlandtab.cpp \
 tasmmeanshape.cpp tasmshapemod.cpp)

DEPENDENCY_FILES=\
	$(patsubst %.o,%.d,\
 $(OBJFILES_MINIMAL)\
 $(OBJFILES_MINIMAL2)\
 $(OBJFILES_TEST_STASM_LIB)\
 $(OBJFILES_TEST_STASM_LIB_ERR)\
 $(OBJFILES_STASM_MAIN)\
 $(OBJFILES_SWAS)\
 $(OBJFILES_TASM))

all:\
 obj\
 minimal$(EXE)\
 minimal2$(EXE)\
 test_stasm_lib$(EXE)\
 test_stasm_lib_err$(EXE)\
 stasm$(EXE)\
 swas$(EXE)\
 tasm$(EXE)\
 mdiff$(EXE)

# Create the directory "obj" if necessary (obj is the directory for .o and .d files)

obj:
	@mkdir -p obj

minimal$(EXE): $(OBJFILES_MINIMAL)
	$(CXX) -o $@ $(CFLAGS) $(OBJFILES_MINIMAL) $(LIBRARIES)

minimal2$(EXE): $(OBJFILES_MINIMAL2)
	$(CXX) -o $@ $(CFLAGS) $(LIBRARIES) $(OBJFILES_MINIMAL2) $(LIBRARIES)

test_stasm_lib$(EXE): $(OBJFILES_TEST_STASM_LIB)
	$(CXX) -o $@ $(CFLAGS) $(OBJFILES_TEST_STASM_LIB) $(LIBRARIES)

test_stasm_lib_err$(EXE): $(OBJFILES_TEST_STASM_LIB_ERR)
	$(CXX) -o $@ $(CFLAGS) $(OBJFILES_TEST_STASM_LIB_ERR) $(LIBRARIES)

stasm$(EXE): $(OBJFILES_STASM_MAIN)
	$(CXX) -o $@ $(CFLAGS) $(OBJFILES_STASM_MAIN) $(LIBRARIES)

swas$(EXE): $(OBJFILES_SWAS)
	$(CXX) -o $@ $(CFLAGS) $(OBJFILES_SWAS) $(LIBRARIES)

tasm$(EXE): $(OBJFILES_TASM)
	$(CXX) -o $@ $(CFLAGS) $(OBJFILES_TASM) $(LIBRARIES)

# Invoke g++ to create dependency files *.d in the obj directory.  
# Then invoke g++ to create object files *.o in the obj directory.

obj/%.o: %.cpp
	$(CXX) -MF obj/$(notdir $(patsubst %.cpp,./%.d,$<)) $< -MM -MT $@ $(CFLAGS) $(INCLUDES)
	$(CXX) -o $@ -c $< $(CFLAGS) $(DEFINES) $(INCLUDES)

# incorporate the dependency files obj/*.d created above
-include $(DEPENDENCY_FILES)

.PHONY: clean
clean:
	-rm -rf obj 
	-rm -f *.exe
	-rm -f minimal.bmp

# mdiff is like diff but ignores [times] in square brackets
mdiff$(EXE): ../apps/mdiff/mdiff.cpp
	$(CXX) -o mdiff$(EXE) $(CFLAGS) ../apps/mdiff/mdiff.cpp

# If any test below fails the make will abort with an error message

test_minimal:
	./minimal
	cmp minimal.bmp ../tests/minimal/minimal.bmp
	@rm minimal.bmp

test_minimal2:
	./minimal2
	cmp minimal2.bmp ../tests/minimal/minimal2.bmp
	@rm minimal2.bmp

test_stasm_lib_multi0_minwidth25:
	./test_stasm_lib 0 25 1 ../tests/data/testmulti.jpg
	./mdiff stasm.log ../tests/stasm_lib/multi0_minwidth25/stasm.log
	@rm stasm.log test_stasm_lib_auto.bmp test_stasm_lib_pinned.bmp

test_stasm_lib_multi1_minwidth25:
	./test_stasm_lib 1 25 1 ../tests/data/testmulti.jpg
	./mdiff stasm.log ../tests/stasm_lib/multi1_minwidth25/stasm.log
	@rm stasm.log test_stasm_lib_auto.bmp

test_stasm_lib_multi0_minwidth10:
	./test_stasm_lib 0 10 1 ../tests/data/testmulti.jpg
	./mdiff stasm.log ../tests/stasm_lib/multi0_minwidth10/stasm.log
	@rm stasm.log test_stasm_lib_auto.bmp

test_stasm_lib_multi1_minwidth10:
	./test_stasm_lib 1 10 1 ../tests/data/testmulti.jpg
	./mdiff stasm.log ../tests/stasm_lib/multi1_minwidth10/stasm.log
	@rm stasm.log test_stasm_lib_auto.bmp

#test_stasm_lib_err:
#	./test_stasm_lib_err
#	./mdiff stasm.log ../tests/mingw/stasm_lib/test_stasm_lib_err.log
#	@rm stasm.log

test_stasm_main:
	./stasm ../data/testface.jpg
	./mdiff stasm.log ../tests/stasm_main/single/test_stasm_main.log
	cmp testface_stasm.bmp ../tests/stasm_main/single/testface_stasm.bmp
	@rm stasm.log testface_stasm.bmp
	./stasm -m ../tests/data/testmulti.jpg
	./mdiff stasm.log ../tests/stasm_main/multi/test_stasm_main_multi.log
	@rm stasm.log testmulti_stasm.bmp
	./stasm -d -m -s -c ../tests/data/testmulti.jpg
	./mdiff stasm.log ../tests/stasm_main/multi/test_stasm_main_multi_small.log
	cmp testmulti_stasm.bmp ../tests/stasm_main/multi/testmulti_stasm.bmp
	@rm stasm.log testmulti_stasm.bmp
	./stasm -i -n 68 ../data/testface.jpg
	./mdiff stasm.log ../tests/stasm_main/multi/test_stasm_main_68.log
	@rm stasm.log

test_swas:
	./swas -f ../tests/data/testface.shape
	./mdiff stasm.log ../tests/swas/swas.log
	./mdiff fit/testface.fit ../tests/swas/testface.fit
	@rm fit/testface.fit
	cmp testface__ref.bmp ../tests/swas/testface__ref.bmp
	cmp testface_stasm.bmp ../tests/swas/testface_stasm.bmp
	@rm stasm.log testface_stasm.bmp testface__ref.bmp

test_tasm:
	@rm -rf test_tasmout
	./tasm -d test_tasmout -w ../tasm/shapes/muct77_short.shape
	./mdiff test_tasmout/log/tasm.log ../tests/tasm_small77/tasm.log
	./mdiff test_tasmout/log/imputed.shape ../tests/tasm_small77/imputed.shape
	./mdiff test_tasmout/mh/yaw00.mh ../tests/tasm_small77/yaw00.mh
	./mdiff test_tasmout/mh/yaw00_shapemodel.mh ../tests/tasm_small77/yaw00_shapemodel.mh
	./mdiff test_tasmout/mh/yaw00_lev0_p12_classic.mh ../tests/tasm_small77/yaw00_lev0_p12_classic.mh
	./mdiff test_tasmout/mh/yaw00_lev1_p06_classic.mh ../tests/tasm_small77/yaw00_lev1_p06_classic.mh
	./mdiff test_tasmout/mh/yaw00_lev2_p00_classic.mh ../tests/tasm_small77/yaw00_lev2_p00_classic.mh
	./mdiff test_tasmout/mh/yaw00_lev3_p00_classic.mh ../tests/tasm_small77/yaw00_lev3_p00_classic.mh
	./mdiff test_tasmout/mh/yaw00_lev0_p16_hat.mh ../tests/tasm_small77/yaw00_lev0_p16_hat.mh
	./mdiff test_tasmout/log/lev3_p76_classic.desc ../tests/tasm_small77/lev3_p76_classic.desc
	./mdiff test_tasmout/log/lev2_p76_hat.desc ../tests/tasm_small77/lev2_p76_hat.desc
	@# p26 is imputed, so test it below
	./mdiff test_tasmout/mh/yaw00_lev1_p26_hat.mh ../mingw/tasm_small77/yaw00_lev1_p26_hat.mh
	@# p30 is sometimes obscured if eye is closed, so test it below
	./mdiff test_tasmout/mh/yaw00_lev2_p30_hat.mh ../tests/tasm_small77/yaw00_lev2_p30_hat.mh
	cmp test_tasmout/log/landmark00.bmp ../tasm_small77/landmark00.bmp
	cmp test_tasmout/log/landmark30.bmp ../tasm_small77/landmark30.bmp
	cmp test_tasmout/log/meanshape.bmp ../tasm_small77/meanshape.bmp
	cmp test_tasmout/log/shape17.bmp ../tasm_small77/shape17.bmp
	@rm -rf test_tasmout


# TODO test_stasm_lib_err not included below, fails under mingw builds

test:\
 mdiff$(EXE)\
 test_stasm_lib_multi0_minwidth25\
 test_stasm_lib_multi1_minwidth25\
 test_stasm_lib_multi0_minwidth10\
 test_stasm_lib_multi1_minwidth10\
 test_stasm_main\
 test_swas\
 test_tasm

fulltest:\
 test_minimal\
 test_minimal2\
 test
