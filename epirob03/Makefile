MAIN = vision

#VPATH += text
#VPATH += images

# this is useful for developing on tiny laptop screens
# normally should be commented out
GEOMETRY=-geometry +0+0

OUTPUT = out

#MAIN = $(patsubst %.tex, %, $(wildcard *.tex))
SUBMAIN = $(wildcard *.tex) $(wildcard *.sty)
JPEGS = $(wildcard *.jpg)
GIFS = $(wildcard *.gif)
PNGS = $(wildcard *.png)
EPSSRCS = $(wildcard *.epssrc)
PRNSRCS = $(wildcard *.prn)
PSSRCIS = $(wildcard *.pssrci)
PSSRCS = $(wildcard *.pssrc)
EPSPREV = $(wildcard *.eps)
EPSJPEG = $(patsubst %.jpg, %.eps, $(JPEGS))
EPSGIF = $(patsubst %.gif, %.eps, $(GIFS))
EPSPNG = $(patsubst %.png, %.eps, $(PNGS))
EPSSRC = $(patsubst %.epssrc, %.eps, $(EPSSRCS))
PSSRC = $(patsubst %.pssrc, %.eps, $(PSSRCS))  $(patsubst %.prn, %.eps, $(PRNSRCS))
PSSRCI = $(patsubst %.pssrci, %.eps, $(PSSRCIS))
EPS = $(EPSPREV) $(EPSJPEG) $(EPSGIF) $(EPSPNG) $(EPSSRC) $(PSSRC) $(PSSRCI)

all: $(MAIN).dvi

%.pssrc: %.prn
	cp $< $@

%.eps: %.jpg
	convert $^ eps2:$@.tmp
	sed "s/statusdict/%%%statusdict/g" < $@.tmp > $@.tmp2
	sed "s/EndData/EndBinary/g" < $@.tmp2 > $@
	rm -f $@.tmp $@.tmp2

%.eps: %.gif
	convert $^ eps:$@.tmp
	sed "s/statusdict/%%%statusdict/g" < $@.tmp > $@.tmp2
	sed "s/EndData/EndBinary/g" < $@.tmp2 > $@
	rm -f $@.tmp $@.tmp2

%.eps: %.png
	convert $^ eps2:$@.tmp
	sed "s/statusdict/%%%statusdict/g" < $@.tmp > $@.tmp2
	sed "s/EndData/EndBinary/g" < $@.tmp2 > $@
	rm -f $@.tmp $@.tmp2

%.eps: %.epssrc
	cp $^ $@

%.eps: %.pssrc
	grep -q "Title: Microsoft PowerPoint" $< && ps2eps $< || ps2eps -f -C  -n -c $<
	( sed "s/statusdict/%%%statusdict/g" | sed "s/^(%%\[[^ ]/%%(%%\[/" | grep -v "1 dict dup" | grep -v "ImagingBBox null" | sed "s/: N 111 /: %%N 111 /"  | sed "s/: N 803 /: %%N 803 /" ) < $<.eps > $@
	rm -f $^.eps

#%.eps: %.pssrc
#	ps2eps $<
#	( sed "s/statusdict/%%%statusdict/g" | sed "s/^(%%\[[^ ]/%%(%%\[/" | sed "s/: N 111 /: %%N 111 /" ) < $<.eps > $@
#	rm -f $^.eps

%.eps: %.pssrci
	ps2epsi $<
	sed "s/statusdict/%%%statusdict/g" < $<.epsi > $@
	rm -f $<.epsi
#	mv $^.epsi $@

%.bbl: %.bib
	bibtex $*

%.pdf: %.ps
	ps2pdf $< $@
#	distill -v -compatlevel 2.1 -colordownsample on -graydownsample on -monodownsample on $^
#	distill -compatlevel 2.1 -colordownsample off -graydownsample off -monodownsample off $^

dd: $(MAIN).dvi
	xdvi $(GEOMETRY) $(MAIN).dvi

# need appropriate focus rules on win manager for this to work
#rep:
#	sh -c "while true do\
#	 ( make vision.dvi; cp vision.dvi no-focus.dvi; ( xdvi no-focus.dvi & ); echo Hi | tee rep; while -e rep do; ( make | grep latex && rm rep ); sleep 1; done; )  done"

d: $(MAIN).dvi
	xdvi -noghostscript $(GEOMETRY) $(MAIN).dvi
#	xdvi $(MAIN).dvi

gv: $(MAIN).ps
	gv $(GEOMETRY) $(MAIN).ps

p: $(MAIN).ps

$(MAIN).half.ps: $(MAIN).ps
	mpage -2 < $< > $@

p2: $(MAIN).half.ps

pdf2: $(MAIN).half.pdf

b: $(MAIN).bbl

fb:
	rm -f $(MAIN).bbl
	make $(MAIN).bbl

f:
	latex $(MAIN)

wc:
	cat *.tex | wc

bounce:
	make fb ; make f ; make fb ; make f ; make fb ; make f

$(MAIN).dvi: $(MAIN).tex $(EPS) $(MAIN).bbl $(SUBMAIN)
	latex $(MAIN).tex

force:
	latex $(MAIN).tex

$(MAIN).ps: $(MAIN).dvi
#	dvips -t a4 -f $< > $@
	dvips -t letter -f $< > $@

short:
	cp defs-iros.tex defs.tex
	make bounce

long:
	cp defs-epi.tex defs.tex
	make bounce

commit:
	cvs commit -m "Makefile-generated commit"

c: commit

update:
	cvs update

u: update

# only works on a sun, with distill installed
pdf: $(MAIN).pdf

clean:
	rm -f $(MAIN).dvi $(MAIN).ps

