CC=gcc
NVCC=nvcc
DEBUG=-g -DDEBUG -pg -O0
OPT = -Ofast -march=native
CFLAGS=-c -std=c99 -Wall $(OPT) -I$(SACHOME)/include -fopenmp -pthread -DNoThreads
LFLAGS=-std=c99 -Wall $(OPT) -fopenmp -pthread
CLIBS=-lm -lfftw3 -lfftw3f -lstdc++

CUFLAGS=-c --use_fast_math -gencode arch=compute_35,code=sm_35 -gencode arch=compute_61,code=sm_61 -Xcompiler -Wall,-O3,-march=native,-fopenmp,-pthread
LUFLAGS=--use_fast_math -gencode arch=compute_35,code=sm_35 -gencode arch=compute_61,code=sm_61 -Xcompiler -Wall,-O3,-march=native,-fopenmp,-pthread
CULIBS=-L/usr/local/cuda/lib64 -lcuda -lcudart -lcufft -lgomp

libsac = $(SACHOME)/lib/sacio.a

all: Filelist2msacs PCC_fullpair_1b PCC_fullpair_1b_cuda

PCC_fullpair_1b_cuda: PCC_fullpair_1b.o FFTapps_cuda.o ccs_cuda.o ReadManySacs.o rotlib.o sph.o
	$(NVCC) $(LUFLAGS) -o PCC_fullpair_1b_cuda PCC_fullpair_1b.o FFTapps_cuda.o ReadManySacs.o rotlib.o sph.o ccs_cuda.o $(libsac) $(CLIBS) $(CULIBS) 

PCC_fullpair_1b: PCC_fullpair_1b.o FFTapps.o ReadManySacs.o rotlib.o sph.o
	$(CC) $(LFLAGS) -o PCC_fullpair_1b PCC_fullpair_1b.o FFTapps.o ReadManySacs.o rotlib.o sph.o $(libsac) $(CLIBS)

PCC_fullpair_1b.o: PCC_fullpair_1b.c 
	$(CC) $(CFLAGS) PCC_fullpair_1b.c

FFTapps.o: FFTapps.c FFTapps.h
	$(CC) $(CFLAGS) FFTapps.c

FFTapps_cuda.o: FFTapps.c FFTapps.h
	$(CC) $(CFLAGS) -DCUDAON -o FFTapps_cuda.o FFTapps.c

rotlib.o: rotlib.c rotlib.h
	$(CC) $(CFLAGS) rotlib.c
	
Filelist2msacs: Filelist2msacs.o ReadManySacs.o
	$(CC) $(LFLAGS) -o Filelist2msacs Filelist2msacs.o ReadManySacs.o $(libsac) $(CLIBS)
	
ReadManySacs.o: ReadManySacs.c ReadManySacs.h
	$(CC) $(CFLAGS) ReadManySacs.c

sph.o: sph.c sph.h
	$(CC) $(CFLAGS) sph.c
	
ccs_cuda.o: ccs_cuda.cu ccs_cuda.h
	$(NVCC) $(CUFLAGS) ccs_cuda.cu
	
install: Filelist2msacs PCC_fullpair_1b
	mkdir -p ../bin
	install -s Filelist2msacs PCC_fullpair_1b ../bin
	if [ -f PCC_fullpair_1b_cuda ]; then install -s PCC_fullpair_1b_cuda ../bin; fi
	
clean:
	rm -rf *o PCC_fullpair_1b PCC_fullpair_1b_cuda Filelist2msacs

