#################################################################
##                                                             ##
##    ██████╗  ██████╗  █████╗                                 ##
##    ██╔══██╗██╔═══██╗██╔══██╗                                ##
##    ██████╔╝██║   ██║███████║                                ##
##    ██╔══██╗██║   ██║██╔══██║                                ##
##    ██║  ██║╚██████╔╝██║  ██║                                ##
##    ╚═╝  ╚═╝ ╚═════╝ ╚═╝  ╚═╝                                ##
##          ██╗      ██████╗  ██████╗ ██╗ ██████╗              ##
##          ██║     ██╔═══██╗██╔════╝ ██║██╔════╝              ##
##          ██║     ██║   ██║██║  ███╗██║██║                   ##
##          ██║     ██║   ██║██║   ██║██║██║                   ##
##          ███████╗╚██████╔╝╚██████╔╝██║╚██████╗              ##
##          ╚══════╝ ╚═════╝  ╚═════╝ ╚═╝ ╚═════╝              ##
##                                                             ##
##    RISC-V Regression Suite Makefile                         ##
##                                                             ##
#################################################################
##                                                             ##
##     Copyright (C) 2015 ROA Logic BV                         ##
##                                                             ##
##   This confidential and proprietary software is provided    ##
##  under license. It may only be used as authorised by a      ##
##  licensing agreement from ROA Logic BV.                     ##
##  No parts may be copied, reproduced, distributed, modified  ##
##  or adapted in any form without prior written consent.      ##
##  This entire notice must be reproduced on all authorised    ##
##  copies.                                                    ##
##                                                             ##
##     THIS SOFTWARE IS PROVIDED ``AS IS'' AND WITHOUT ANY     ##
##  EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED  ##
##  TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS  ##
##  FOR A PARTICULAR PURPOSE. IN NO EVENT SHALL THE AUTHOR     ##
##  OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,        ##
##  INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES   ##
##  (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE  ##
##  GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR       ##
##  BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF ##
##  LIABILITY, WHETHER IN  CONTRACT, STRICT LIABILITY, OR TORT ##
##  (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT ##
##  OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE        ##
##  POSSIBILITY OF SUCH DAMAGE.                                ##
##                                                             ##
#################################################################
#
# 2017-02-22: Update for new test naming
#

all: regression

SIMULATORS = ncsim vcs silos icarus riviera
LINTERS    = $(addsuffix _lint, $(SIMULATORS))
SIMWAVES   = $(addsuffix _waves, $(SIMULATORS))

MS     = -s

ROOT_DIR=../../../..
TST_SRC_DIR=$(ROOT_DIR)/../bench/regression


##########################################################################
#
# Regression variables
#
##########################################################################
#REGRESSION_MEM_LATENCY      = 0 1 2 3 4 5 7 8 15
#REGRESSION_MEM_LATENCY      = 0 1 3 8
REGRESSION_MEM_LATENCY     = 0 1 3
#REGRESSION_XLEN             = 32 64
REGRESSION_XLEN             = 32 64
#REGRESSION_WRITEBUFFER_SIZE = 0 2 8
REGRESSION_WRITEBUFFER_SIZE = 0 2 8
REGRESSION_ICACHE_SIZE = 0 4
REGRESSION_DCACHE_SIZE = 0 4
#REGRESSION_MULT_LATENCY = 3 2 1 0
REGRESSION_MULT_LATENCY = 4


##########################################################################
#
# Tests
#
##########################################################################
XLEN    = 32
HAS_RVC = 0
HAS_U   = 1 
HAS_S   = 1
HAS_H   = 0
HAS_AMO =
HAS_FPU =
HAS_MMU =
HAS_MUL = 1
HAS_DIV = $(HAS_MUL)
CORES   = 1
U_INT_TESTS =	simple							\
		add addi 						\
		and andi 						\
		auipc							\
		beq bge bgeu blt bltu bne				\
		fence_i							\
		jal jalr						\
		lb lbu lh lhu lw 					\
		lui							\
		or ori							\
		sb sh sw						\
		sll slli						\
		slt slti sltiu sltu					\
		sra srai						\
		srl srli						\
		sub							\
		xor xori						\
	$(if $(filter 64, $(XLEN)),					\
 		addiw addw						\
		lwu ld							\
		sd							\
		sllw slliw						\
		sllw slliw						\
		sltu sltiu						\
		sraw sraiw						\
		srlw srliw						\
		subw )
U_RVC_TESTS  =  rvc
U_AMO_TESTS  =	amoadd_w amoand_w amomax_w amomaxu_w			\
		amomin_w amominu_w amoor_w amoxor_w amoswap_w lrsc	\
	$(if $(filter 64, $(XLEN)),					\
  		amoadd_d amoand_d amomax_d amomaxu_d			\
		amomin_d amominu_d amoor_d amoxor_d amoswap_d )
U_MUL_TESTS  =	mul mulh mulhu mulhsu					\
	 $(if $(filter 64, $(XLEN)), mulw )
U_DIV_TESTS  =	div divu rem remu					\
	 $(if $(filter 64,$(XLEN)), divw divuw remw remuw )

#machine mode tests
M_TESTS      =	breakpoint csr illegal ma_addr ma_fetch mcsr sbreak	\
		scall							\
	$(if $(filter 32,$(XLEN)), shamt)

#supervisor mode tests
S_TESTS      =	csr dirty illegal ma_fetch sbreak scall wfi


#User Mode Integer Tests
uitst_lst  =                  $(foreach t, $(U_INT_TESTS),rv$(XLEN)ui-p-$t)
uitst_lst += $(if $(HAS_AMO), $(foreach t, $(U_AMO_TESTS),rv$(XLEN)ua-p-$t))
uitst_lst += $(if $(HAS_MUL), $(foreach t, $(U_MUL_TESTS),rv$(XLEN)um-p-$t))
uitst_lst += $(if $(HAS_DIV), $(foreach t, $(U_DIV_TESTS),rv$(XLEN)um-p-$t))
uitests    = $(if $(HAS_U) > 0, $(uitst_lst))

#User Mode RVC Tests
uctst_lst = $(if $(HAS_RVC), $(foreach t, $(U_RVC_TESTS),rv$(XLEN)uc-p-$t))
uctests   = $(if $(HAS_U) > 0, $(uctst_lst))

#Supervisor Mode Tests
sitst_lst = $(S_TESTS)
sitests   = $(if $(HAS_S) > 0, $(foreach t, $(sitst_lst),rv$(XLEN)si-p-$t))

#Machine Mode Tests
mitst_lst = $(M_TESTS)
mitests   = $(foreach t, $(mitst_lst),rv$(XLEN)mi-p-$t)


#All tests
tests = $(uitests) $(sitests) $(mitests)
#tests = $(sitests)


##########################################################################
#
### Functions
#
############################################################################
add_regression    = $(foreach p,$(REGRESSION_$(1)), $(addprefix $(1)$(p)@, $(2)))
add_regression_if = $(if $(filter $(2), $(subst -, , $(1))), $(call add_regression,$(3),$(4)),$(4))

logs = $(call add_regression,DCACHE_SIZE,				\
       $(call add_regression,ICACHE_SIZE,				\
       $(call add_regression,MEM_LATENCY,				\
       $(call add_regression,WRITEBUFFER_SIZE,				\
       $(call add_regression,XLEN,					\
       $(foreach t,$(tests),						\
         $(call add_regression_if,$t,$(U_MUL_TESTS),MULT_LATENCY,	\
         $t.log								\
         )								\
       )								\
       )								\
       )								\
       )								\
       )								\
       )


regression: $(logs)

%.log:
	$(MAKE) riviera LOG=./log/$(@F)						\
	  PARAMS="DCACHE_SIZE=$(subst DCACHE_SIZE,,$(word 1,$(subst @, ,$*)))		\
	          ICACHE_SIZE=$(subst ICACHE_SIZE,,$(word 2,$(subst @, ,$*)))		\
	          MEM_LATENCY=$(subst MEM_LATENCY,,$(word 3,$(subst @, ,$*))) 		\
	          WRITEBUFFER_SIZE=$(subst WRITEBUFFER_SIZE,,$(word 4,$(subst @, ,$*)))	\
	          XLEN=$(subst XLEN,,$(word 5,$(subst @, ,$*)))				\
	          MULT_LATENCY=$(subst MULT_LATENCY,,$(word 6,$(subst @, ,$*)))		\
	          HAS_U=$(HAS_U) HAS_S=$(HAS_S) HAS_H=$(HAS_H)				\
		  HAS_AMO=$(HAS_AMO) HAS_RVC=$(HAS_RVC) HAS_MULDIV=$(HAS_MUL)		\
	          TECHNOLOGY=$(TECHNOLOGY)						\
	          INIT_FILE=\"$(TST_SRC_DIR)/$(lastword $(subst @, ,$*)).hex\" "


##########################################################################
#
# Includes
#
##########################################################################
-include Makefile.include


##########################################################################
#
# libraries
#
##########################################################################
RTL_LIBS =


##########################################################################
#
# Misc Variables
#
##########################################################################
INCDIRS:=$(INCDIRS)
DEFINES:=$(DEFINES)

shell=/bin/sh


##########################################################################
#
# OVL
#
##########################################################################
ifeq ($(OVL_ASSERT),ON)
    INCDIRS+=$(STD_OVL_DIR)
    DEFINES+=OVL_ASSERT_ON
    LIBDIRS+=$(STD_OVL_DIR)
    LIBEXT +=.vlib

    ifeq ($(OVL_INIT_MSG),ON)
        DEFINES:=OVL_INIT_MSG
    endif
endif


##########################################################################
#
# Make Targets
#
##########################################################################
.PHONY: $(SIMULATORS) $(LINTERS) $(SIMWAVES)
$(SIMULATORS): % : %/Makefile $(TB_PREREQ)
	@$(MAKE) $(MS) -C $@ sim				\
	VLOG="$(abspath $(RTL_VLOG) $(TB_VLOG))"		\
	TECHLIBS="$(TECHLIBS)"					\
	LIBDIRS="$(LIBDIRS)"					\
	LIBEXT="$(LIBEXT)"					\
	VHDL="$(abspath $(RTL_VHDL) $(TB_VHDL))"		\
	INCDIRS="$(abspath $(INCDIRS))"				\
	DEFINES="$(DEFINES)"					\
	TOP=$(TB_TOP)						\
	LOG=$(LOG) PARAMS="$(PARAMS)"

$(SIMWAVES): %_waves : %/Makefile $(TB_PREREQ)
	@$(MAKE) $(MS) -C $(subst _waves,,$@) simw		\
	VLOG="$(abspath $(RTL_VLOG) $(TB_VLOG))"		\
	TECHLIBS="$(TECHLIBS)"					\
	LIBDIRS="$(LIBDIRS)"					\
	LIBEXT="$(LIBEXT)"					\
	VHDL="$(abspath $(RTL_VHDL) $(TB_VHDL))"		\
	INCDIRS="$(abspath $(INCDIRS))"				\
	DEFINES="$(DEFINES)"					\
	TOP=$(TB_TOP)						\
	LOG=$(LOG) PARAMS="$(PARAMS)"

$(LINTERS): %_lint : %/Makefile $(TB_PREREQ)
	@$(MAKE) $(MS) -C $(subst _lint,,$@) lint		\
	VLOG="$(abspath $(RTL_VLOG))"				\
	VHDL="$(abspath $(RTL_VHDL))"				\
	INCDIRS="$(abspath $(INCDIRS))"				\
	DEFINES="$(DEFINES)"					\
	TOP=$(RTL_TOP)


.PHONY: clean distclean mrproper
clean:
	@for f in $(wildcard *); do				\
		if test -d $$f; then $(MAKE) -C $$f clean; fi	\
	done

distclean:
	@rm -rf $(SIMULATORS) Makefile.include $(TB_PREREQ)

mrproper:
	@rm -rf *


##########################################################################
#
# Make simulation structure
#
##########################################################################
Makefile.include:
	@cp ../bin/Makefile.include .

%/Makefile:
	@mkdir -p $*
	@cp ../bin/Makefile.$* $@

$(TB_PREREQ):
	@cp ../bin/$@ $@
