MOVIE1 := m140905_042212_sidney_c100564852550000001823085912221377_s1_X0

all: $(MOVIE1).subreads.bam $(MOVIE1).aligned_subreads.cmp.h5 $(MOVIE1).aligned_subreads.bam testdata upstream_testdata

$(MOVIE1).subreads.bam: $(MOVIE1).1.bax.h5
	bax2bam $<
	samtools index $@
	pbindex $@

$(MOVIE1).aligned_subreads.bam: $(MOVIE1).subreads.bam lambdaNEB.fa
	pbalign $^ $@
	samtools index $@
	pbindex $@

$(MOVIE1).aligned_subreads.cmp.h5: $(MOVIE1).1.bax.h5 lambdaNEB.fa
	pbalign-2.3 --forQuiver --metrics IPD,DeletionQV,DeletionTag,InsertionQV,MergeQV,SubstitutionQV $^ $@

testdata:
	dataset.py create --type ReferenceSet --relative datasets/pbalchemysim0.reference.xml datasets/pbalchemysim0.reference.fasta
	bax2bam datasets/pbalchemysim0.bas.h5 -o datasets/pbalchemysim0 --pulsefeatures DeletionTag,DeletionQV,InsertionQV,MergeQV,SubstitutionQV && \
	dataset.py create --type SubreadSet --relative datasets/pbalchemysim0.subread.xml datasets/pbalchemysim0.subreads.bam
	pbalign datasets/pbalchemysim0.subreads.bam datasets/pbalchemysim0.reference.fasta datasets/pbalchemysim0.pbalign.bam && \
	dataset.py create --type AlignmentSet --relative datasets/pbalchemysim0.pbalign.xml datasets/pbalchemysim0.pbalign.bam
	dataset.py split --contigs --chunks 2 datasets/pbalchemysim0.pbalign.xml
	bax2bam datasets/pbalchemysim1.bas.h5 -o datasets/pbalchemysim1 --pulsefeatures DeletionTag,DeletionQV,InsertionQV,MergeQV,SubstitutionQV && \
	dataset.py create --type SubreadSet --relative datasets/pbalchemysim1.subread.xml datasets/pbalchemysim1.subreads.bam
	pbalign datasets/pbalchemysim1.subreads.bam datasets/pbalchemysim0.reference.fasta datasets/pbalchemysim1.pbalign.bam && \
	dataset.py create --type AlignmentSet --relative datasets/pbalchemysim1.pbalign.xml datasets/pbalchemysim1.pbalign.bam
	dataset.py create --type AlignmentSet --relative datasets/pbalchemysim.pbalign.xml datasets/pbalchemysim0.pbalign.bam datasets/pbalchemysim1.pbalign.bam

upstream_testdata:
	dataset.py create --type ReferenceSet --relative datasets/lambda.reference.xml lambdaNEB.fa
	dataset.py create --type AlignmentSet --relative datasets/lambda.pbalign.xml $(MOVIE1).aligned_subreads.bam

xsdupdate:
	cp ../../../doc/FileFormats/examples/datasets/*.xml datasets/.
