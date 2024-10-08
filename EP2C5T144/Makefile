
# Cyclone IV FPGA EP4CE6E22C8N Development Board USB V2
# quartus

PROJECT  := rio
TOP      := rio
PART      := EP2C5T144C8
FAMILY    := "Cyclone II"
VERILOGS  := blink.v rio.v

QC   = quartus_sh
QP   = quartus_pgm
QM   = quartus_map
QF   = quartus_fit
QA   = quartus_asm
QS   = quartus_sta
ECHO = echo
Q   ?= @

STAMP = echo done >

QCFLAGS = --flow compile
QPFLAGS =
QMFLAGS = --read_settings_files=on $(addprefix --source=,$(VERILOGS))
QFFLAGS = --part=$(PART) --read_settings_files=on

ASIGN = $(PROJECT).qsf $(PROJECT).qpf

all: export PATH := $(PATH):/home/sab/altera/13.0sp1/quartus/bin
all: $(PROJECT) svf

     

map: smart.log $(PROJECT).map.rpt
fit: smart.log $(PROJECT).fit.rpt
asm: smart.log $(PROJECT).asm.rpt
sta: smart.log $(PROJECT).sta.rpt
smart: smart.log


svf: $(PROJECT).sof
	$(Q)$(ECHO) "Converting .sof to .svf."
	quartus_cpf -c --operation=p --freq=6MHz --voltage=3.3V $(PROJECT).sof $(PROJECT).svf
	$(STAMP) svf.chg




$(ASIGN):
	$(Q)$(ECHO) "Generating asignment files."
	$(QC) --prepare -f $(FAMILY) -t $(TOP) $(PROJECT)
	echo >> $(PROJECT).qsf
	cat pins.qdf >> $(PROJECT).qsf

smart.log: $(ASIGN)
	$(Q)$(ECHO) "Generating smart.log."
	$(QC) --determine_smart_action $(PROJECT) > smart.log

$(PROJECT): smart.log $(PROJECT).asm.rpt $(PROJECT).sta.rpt

$(PROJECT).map.rpt: map.chg $(VERILOGS)
	$(QM) $(QMFLAGS) $(PROJECT)
	$(STAMP) fit.chg

$(PROJECT).fit.rpt: fit.chg $(PROJECT).map.rpt
	$(QF) $(QFFLAGS) $(PROJECT)
	$(STAMP) asm.chg
	$(STAMP) sta.chg

$(PROJECT).asm.rpt: asm.chg $(PROJECT).fit.rpt
	$(QA) $(PROJECT)

$(PROJECT).sta.rpt: sta.chg $(PROJECT).fit.rpt
	$(QS) $(PROJECT)

map.chg:
	$(STAMP) map.chg
fit.chg:
	$(STAMP) fit.chg
sta.chg:
	$(STAMP) sta.chg
asm.chg:
	$(STAMP) asm.chg

clean:
	$(Q)$(ECHO) "Cleaning."
	rm -rf db incremental_db
	rm -f smart.log *.rpt *.sof *.chg *.qsf *.qpf *.summary *.smsg *.pin *.jdi *.sld

load: prog
prog: $(PROJECT).sof
	$(Q)$(ECHO) "Programming."
	$(QP) --no_banner --mode=jtag -o "P;$(PROJECT).sof"

