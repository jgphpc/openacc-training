# Some default options
# These can be overridden via the command line when calling make
# e.g. "make VERSION=01 ACC=yes"
# Do not set both OMP and ACC to yes, as compilation will fail
VERSION=00
OMP=no
OMP_DEV=no
ACC=no
#yes
NTPB=128

PROBLEM_SIZE=3
PRECISION=double

# CCE compilation options
PROFILE_GENERATE=no
ACC_MODEL=default

# You should not need to change anything below this line
FC = ftn

FFLAGS=-DPROBLEM_SIZE=$(PROBLEM_SIZE) -DNTPB=$(NTPB) -O2

ifeq ($(PRECISION),double)
 FFLAGS += -DDOUBLE_PRECISION
 ifeq ($(PROBLEM_SIZE),4)
  FFLAGS += -fpic
 endif
endif

ifeq ($(PE_ENV),CRAY)
 FFLAGS += -hlist=ad

ifneq ($(OMP),yes)
  ifneq ($(OMP_DEV),yes)
  FFLAGS += -hnoomp 
endif
endif

 ifneq ($(ACC),yes)
  FFLAGS += -xacc
 endif

 ifeq ($(PROFILE_GENERATE),yes)
  FFLAGS += -hprofile_generate
 endif

 ifneq ($(ACC_MODEL),default)
  FFLAGS += -hacc_model=$(ACC_MODEL)
 endif
endif

ifeq ($(PE_ENV),PGI)
 FFLAGS += -Minfo=all,ftn -Mpreprocess -fpic 

 ifeq ($(OMP),yes) 
  FFLAGS += -mp
 endif

 ifeq ($(ACC),yes)
# craype-accel-* module sets the correct cuda level
# keepgpu keeps the intermediate CUDA (not very readable)
# keepptx keeps the ptx files
  FFLAGS += -ta=nvidia,keepgpu,keepptx
 endif
endif

%.o: %.F90
	$(FC) $(FFLAGS) -c $<

himeno_v$(VERSION).x: himeno_F_v$(VERSION).o
	$(FC) $(FFLAGS) -o $@ $<

clean:
	@$(RM) himeno_v*.x himeno_v*.x+pat 
	@$(RM) *.lst *.i *.ptx *.cub *.gpu *.cg *.opt *.o *~ *.mod

#EOF
