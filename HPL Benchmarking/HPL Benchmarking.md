# HPL BENCHMARKING

    As all high performance computers are designed to go fast and deliver computational speed, 
    benchmarking provides the means for the supercomputer user to quantify just how fast 
    and what computational speed can be achieved for a specific workload of interest.

    Hence we are using HPL to achieve the same, where HPL is
    
# HPL    
    HPL is a software package that solves a (random) dense linear system in double precision (64 bits) arithmetic on distributed-memory computers. It can thus be regarded as a portable
    as well as freely available implementation of the High Performance Computing Linpack Benchmark.

    The algorithm used by HPL can be summarized by the following keywords: Two-dimensional block-cyclic data distribution - Right-looking variant of the LU factorization with row partial       pivoting featuring multiple look-ahead depths - Recursive panel factorization with pivot search and column broadcast combined - Various virtual panel broadcast topologies - bandwidth       reducing swap-broadcast algorithm - backward substitution with look-ahead of depth 1.

    The HPL package provides a testing and timing program to quantify the accuracy of the obtained solution as well as the time it took to compute it. The best performance achievable by       this software on your system depends on a large variety of factors. Nonetheless, with some restrictive assumptions on the interconnection network, the algorithm described here and its     attached implementation are scalable in the sense that their parallel efficiency is maintained constant with respect to the per processor memory usage.

# STEPS TO PERFORM:

#INSTALL epel-release
>> yum install epel-release

#Download MPI tar from the source (I've used openmpi because I'm using single node , if you want to perform on multiple nodes use MP)

>> wget https://download.open-mpi.org/release/open-mpi/v4.1/openmpi-4.1.5.tar.gz

#Extract the same
>> tar -xf openmpi-4.1.5.tar.gz 

>> cd openmpi-4.1.5.tar.gz

#Set path 
>> ./configure --prefix=/opt/openmpi-4.1.5

#Now install atlas and atlas dev (which is used to perform BLAS = Basic Linear Algebra Subprograms)

>> cd openmpi-4.1.5
>> make -j 7
>> make install
>> yum install atlas
>> yum install atlas-devel

#Add path to bashrc to enable mpi
>> vim ~/.bashrc 
>> export PATH=$PATH:/opt/openmpi-4.1.5/bin ((ADD IN THIS FORMAT END OF FILE AS PER YOUR PATH))
>> source ~/.bashrc

#INSTALL HPL AND EXTRACT IT

>> wget https://netlib.org/benchmark/hpl/hpl-2.3.tar.gz --no-check-certificate
>> tar -xf hpl-2.3.tar.gz

#NOW AFTER EXTRACTION GO TO HPL> SETUP IN hpl1-2.3

>> cd /hpl-2.3/setup

# COPY LINUX_PII_CBLAS to hpl1-2.3
>> cp Make.Linux_PII_CBLAS ..

>> vim Make.Linux_PII_CBLAS 

#NOW BEFORE STARTING THE BENCHMARKING WE HAVE TO MAKE CHANGES AS PER OUR SYSTEM 

	#  
	#  -- High Performance Computing Linpack Benchmark (HPL)                
	#     HPL - 2.3 - December 2, 2018                          
	#     Antoine P. Petitet                                                
	#     University of Tennessee, Knoxville                                
	#     Innovative Computing Laboratory                                 
	#     (C) Copyright 2000-2008 All Rights Reserved                       
	#                                                                       
	#  -- Copyright notice and Licensing terms:                             
	#                                                                       
	#  Redistribution  and  use in  source and binary forms, with or without
	#  modification, are  permitted provided  that the following  conditions
	#  are met:                                                             
	#                                                                       
	#  1. Redistributions  of  source  code  must retain the above copyright
	#  notice, this list of conditions and the following disclaimer.        
	#                                                                       
	#  2. Redistributions in binary form must reproduce  the above copyright
	#  notice, this list of conditions,  and the following disclaimer in the
	#  documentation and/or other materials provided with the distribution. 
	#                                                                       
	#  3. All  advertising  materials  mentioning  features  or  use of this
	#  software must display the following acknowledgement:                 
	#  This  product  includes  software  developed  at  the  University  of
	#  Tennessee, Knoxville, Innovative Computing Laboratory.             
	#                                                                       
	#  4. The name of the  University,  the name of the  Laboratory,  or the
	#  names  of  its  contributors  may  not  be used to endorse or promote
	#  products  derived   from   this  software  without  specific  written
	#  permission.                                                          
	#                                                                       
	#  -- Disclaimer:                                                       
	#                                                                       
	#  THIS  SOFTWARE  IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
	#  ``AS IS'' AND ANY EXPRESS OR IMPLIED WARRANTIES,  INCLUDING,  BUT NOT
	#  LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
	#  A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE UNIVERSITY
	#  OR  CONTRIBUTORS  BE  LIABLE FOR ANY  DIRECT,  INDIRECT,  INCIDENTAL,
	#  SPECIAL,  EXEMPLARY,  OR  CONSEQUENTIAL DAMAGES  (INCLUDING,  BUT NOT
	#  LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
	#  DATA OR PROFITS; OR BUSINESS INTERRUPTION)  HOWEVER CAUSED AND ON ANY
	#  THEORY OF LIABILITY, WHETHER IN CONTRACT,  STRICT LIABILITY,  OR TORT
	#  (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
	#  OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE. 
	# ######################################################################
	#  
	# ----------------------------------------------------------------------
	# - shell --------------------------------------------------------------
	# ----------------------------------------------------------------------
	#
	SHELL        = /bin/sh
	#
	CD           = cd
	CP           = cp
	LN_S         = ln -s
	MKDIR        = mkdir
	RM           = /bin/rm -f
	TOUCH        = touch
	#
	# ----------------------------------------------------------------------
	# - Platform identifier ------------------------------------------------
	# ----------------------------------------------------------------------
	#
	ARCH         = Linux_PII_CBLAS
	#
	# ----------------------------------------------------------------------
	# - HPL Directory Structure / HPL library ------------------------------
	# ----------------------------------------------------------------------
	#
	TOPdir       = /root/hpl-2.3
	INCdir       = $(TOPdir)/include
	BINdir       = $(TOPdir)/bin/$(ARCH)
	LIBdir       = $(TOPdir)/lib/$(ARCH)
	#
	HPLlib       = $(LIBdir)/libhpl.a 
	#
	# ----------------------------------------------------------------------
	# - Message Passing library (MPI) --------------------------------------
	# ----------------------------------------------------------------------
	# MPinc tells the  C  compiler where to find the Message Passing library
	# header files,  MPlib  is defined  to be the name of  the library to be
	# used. The variable MPdir is only used for defining MPinc and MPlib.
	#
	MPdir        = /opt/openmpi-4.1.5
	MPinc        = -I$(MPdir)/include
	MPlib        = $(MPdir)/lib/libmpi.so
	#
	# ----------------------------------------------------------------------
	# - Linear Algebra library (BLAS or VSIPL) -----------------------------
	# ----------------------------------------------------------------------
	# LAinc tells the  C  compiler where to find the Linear Algebra  library
	# header files,  LAlib  is defined  to be the name of  the library to be
	# used. The variable LAdir is only used for defining LAinc and LAlib.
	#
	LAdir        = /usr/lib64/atlas
	LAinc        =
	LAlib        = $(LAdir)/libtatlas.so.3 $(LAdir)/libsatlas.so.3
	#
	# ----------------------------------------------------------------------
	# - F77 / C interface --------------------------------------------------
	# ----------------------------------------------------------------------
	# You can skip this section  if and only if  you are not planning to use
	# a  BLAS  library featuring a Fortran 77 interface.  Otherwise,  it  is
	# necessary  to  fill out the  F2CDEFS  variable  with  the  appropriate
	# options.  **One and only one**  option should be chosen in **each** of
	# the 3 following categories:
	#
	# 1) name space (How C calls a Fortran 77 routine)
	#
	# -DAdd_              : all lower case and a suffixed underscore  (Suns,
	#                       Intel, ...),                           [default]
	# -DNoChange          : all lower case (IBM RS6000),
	# -DUpCase            : all upper case (Cray),
	# -DAdd__             : the FORTRAN compiler in use is f2c.
	#
	# 2) C and Fortran 77 integer mapping
	#
	# -DF77_INTEGER=int   : Fortran 77 INTEGER is a C int,         [default]
	# -DF77_INTEGER=long  : Fortran 77 INTEGER is a C long,
	# -DF77_INTEGER=short : Fortran 77 INTEGER is a C short.
	#
	# 3) Fortran 77 string handling
	#
	# -DStringSunStyle    : The string address is passed at the string loca-
	#                       tion on the stack, and the string length is then
	#                       passed as  an  F77_INTEGER  after  all  explicit
	#                       stack arguments,                       [default]
	# -DStringStructPtr   : The address  of  a  structure  is  passed  by  a
	#                       Fortran 77  string,  and the structure is of the
	#                       form: struct {char *cp; F77_INTEGER len;},
	# -DStringStructVal   : A structure is passed by value for each  Fortran
	#                       77 string,  and  the  structure is  of the form:
	#                       struct {char *cp; F77_INTEGER len;},
	# -DStringCrayStyle   : Special option for  Cray  machines,  which  uses
	#                       Cray  fcd  (fortran  character  descriptor)  for
	#                       interoperation.
	#
	F2CDEFS      =
	#
	# ----------------------------------------------------------------------
	# - HPL includes / libraries / specifics -------------------------------
	# ----------------------------------------------------------------------
	#
	HPL_INCLUDES = -I$(INCdir) -I$(INCdir)/$(ARCH) $(LAinc) $(MPinc)
	HPL_LIBS     = $(HPLlib) $(LAlib) $(MPlib)
	#
	# - Compile time options -----------------------------------------------
	#
	# -DHPL_COPY_L           force the copy of the panel L before bcast;
	# -DHPL_CALL_CBLAS       call the cblas interface;
	# -DHPL_CALL_VSIPL       call the vsip  library;
	# -DHPL_DETAILED_TIMING  enable detailed timers;
	#
	# By default HPL will:
	#    *) not copy L before broadcast,
	#    *) call the BLAS Fortran 77 interface,
	#    *) not display detailed timing information.
	#
	HPL_OPTS     = -DHPL_CALL_CBLAS
	#
	# ----------------------------------------------------------------------
	#
	HPL_DEFS     = $(F2CDEFS) $(HPL_OPTS) $(HPL_INCLUDES)
	#
	# ----------------------------------------------------------------------
	# - Compilers / linkers - Optimization flags ---------------------------
	# ----------------------------------------------------------------------
	#
	CC           = /usr/bin/gcc
	CCNOOPT      = $(HPL_DEFS)
	CCFLAGS      = $(HPL_DEFS) -fomit-frame-pointer -O3 -funroll-loops
	#
	# On some platforms,  it is necessary  to use the Fortran linker to find
	# the Fortran internals used in the BLAS library.
	#
	LINKER       = /usr/bin/gcc
	LINKFLAGS    = $(CCFLAGS)
	#
	ARCHIVER     = ar
	ARFLAGS      = r
	RANLIB       = echo
	#
	# ----------------------------------------------------------------------
	----------------------------------------------------------------------
	cd hpl-2.3/
	   77  ls
	   78  cd bin/
	   79  ll
	   80  cd Linux_PII_CBLAS/
	   81  ls
	   82  vim HPL.dat 

	   ----------------------------------------------------------------------
	   [root@HPL Linux_PII_CBLAS]# vim HPL.dat 
	[root@HPL Linux_PII_CBLAS]# cat HPL.dat 
	HPLinpack benchmark input file
	Innovative Computing Laboratory, University of Tennessee
	HPL.out      output file name (if any)
	6            device out (6=stdout,7=stderr,file)
	1            # of problems sizes (N)
	36992	     Ns
	1            # of NBs
	128 	     NBs
	0            PMAP process mapping (0=Row-,1=Column-major)
	1            # of process grids (P x Q)
	2            Ps
	4            Qs
	16.0         threshold
	3            # of panel fact
	0 1 2        PFACTs (0=left, 1=Crout, 2=Right)
	2            # of recursive stopping criterium
	2 4          NBMINs (>= 1)
	1            # of panels in recursion
	2            NDIVs
	3            # of recursive panel fact.
	0 1 2        RFACTs (0=left, 1=Crout, 2=Right)
	1            # of broadcast
	0            BCASTs (0=1rg,1=1rM,2=2rg,3=2rM,4=Lng,5=LnM)
	1            # of lookahead depth
	0            DEPTHs (>=0)
	2            SWAP (0=bin-exch,1=long,2=mix)
	64           swapping threshold
	0            L1 in (0=transposed,1=no-transposed) form
	0            U  in (0=transposed,1=no-transposed) form
	1            Equilibration (0=no,1=yes)
	8            memory alignment in double (> 0)

#PERFORM MAKE ON THE SAME FILE
>> make arch=Linux_PII_CBLAS

#NOW AGAIN WE NEED TO ADD PATH FOR OPENMPI IN BASHRC
>> vim ~/.bashrc
	>> 	export LD_LIBRARY_PATH=/opt/openmpi-4.1.5/lib:$LD_LIBRARY_PATH
>> source ~/.bashrc

#MAKE CHANGES IN HPL.dat

>> vim HPL.dat

#FIND FILE FOR REFERENCE

		================================================================================
		HPLinpack 2.3  --  High-Performance Linpack benchmark  --   December 2, 2018
		Written by A. Petitet and R. Clint Whaley,  Innovative Computing Laboratory, UTK
		Modified by Piotr Luszczek, Innovative Computing Laboratory, UTK
		Modified by Julien Langou, University of Colorado Denver
		================================================================================

		An explanation of the input/output parameters follows:
		T/V    : Wall time / encoded variant.
		N      : The order of the coefficient matrix A.
		NB     : The partitioning blocking factor.
		P      : The number of process rows.
		Q      : The number of process columns.
		Time   : Time in seconds to solve the linear system.
		Gflops : Rate of execution for solving the linear system.

		The following parameter values will be used:

		N      :   36992 
		NB     :     128 
		PMAP   : Row-major process mapping
		P      :       2 
		Q      :       4 
		PFACT  :    Left    Crout    Right 
		NBMIN  :       2        4 
		NDIV   :       2 
		RFACT  :    Left    Crout    Right 
		BCAST  :   1ring 
		DEPTH  :       0 
		SWAP   : Mix (threshold = 64)
		L1     : transposed form
		U      : transposed form
		EQUIL  : yes
		ALIGN  : 8 double precision words

		--------------------------------------------------------------------------------

#NOW TON START PROCESS

>>  mpirun --allow-run-as-root -np 8 ./xhpl HPL.dat


#YOU'LL GET SOMETHING LIKE THIS:

	- The matrix A is randomly generated for each test.
	- The following scaled residual check will be computed:
	      ||Ax-b||_oo / ( eps * ( || x ||_oo * || A ||_oo + || b ||_oo ) * N )
	- The relative machine precision (eps) is taken to be               1.110223e-16
	- Computational tests pass if scaled residuals are less than                16.0

	================================================================================
	T/V                N    NB     P     Q               Time                 Gflops
	--------------------------------------------------------------------------------
	WR00L2L2       36992   128     2     4             560.35             6.0228e+01
	HPL_pdgesv() start time Fri Jun 16 17:03:15 2023

	HPL_pdgesv() end time   Fri Jun 16 17:12:35 2023

	--------------------------------------------------------------------------------
	||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   5.88612721e-04 ...... PASSED
	================================================================================
	T/V                N    NB     P     Q               Time                 Gflops
	--------------------------------------------------------------------------------
	WR00L2L4       36992   128     2     4             562.48             6.0000e+01
	HPL_pdgesv() start time Fri Jun 16 17:12:46 2023

	HPL_pdgesv() end time   Fri Jun 16 17:22:09 2023

	--------------------------------------------------------------------------------
	||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   6.27837583e-04 ...... PASSED

#THIS WILL TELL YOU THE EXACT VALUE IN GFLOPS

	||Ax-b||_oo/(eps*(||A||_oo*||x||_oo+||b||_oo)*N)=   6.27837583e-04 ...... PASSED
	
	HERE IT IS 6.27 GFLOPS FOR MY SYSTEM
