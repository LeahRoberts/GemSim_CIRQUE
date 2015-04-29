Method for generating simulated reads for EC958 fimS OFF and ON orientations
=============================================================================


Theory:
--------

By generating simulated reads from the EC958 genome with the fimS region in either the OFF or ON orientation,
we can create pseudo-sequencing runs with various percentages of the OFF and ON reads in order to test the
specificity of the invertible DNA switch counter script (previously known as DiSCO, but needs a new name).

Percentages to be tested:

1. OFF/ON = 100/0
2. OFF/ON = 80/20
3. OFF/ON = 60/40
4. OFF/ON = 40/60
5. OFF/ON = 80/20
6. OFF/ON = 0/100


We also want to generate invertible DNA regions of different sizes and test detection of inversion here as well:

Inversion sizes:

1. 100 bp inversion
2. 500 bp inversion
3. 700 bp inversion 
4. 1000 bp inversion
5. 2000 bp inversion
6. 3000 bp inversion


Part 1: Method:
----------------

**1. GENERATING SIMULATED READS USING GEMSIM (http://sourceforge.net/projects/gemsim/)**

Following the manual instructions and the given error models in GemSIM, the following commands were run in order
to generate simulated reads:

$ python ./GemReads.py -r EC958.fasta -n 10000000 -l 100 -u 300 -s 20 -m models/ill100v4_p.gzip -c -q 64 -o EC958-OFF -p
$ python ./GemReads.py -r EC958_fimS_ON.fasta -n 10000000 -l 100 -u 300 -s 20 -m models/ill100v4_p.gzip -c -q 64 -o EC958-ON -p

The EC958 fasta files contained either a fimS in the OFF orientation (EC958.fasta) or in the ON orientation (EC958_fimS_ON.fasta).
The ON orientation file was generated by reverse complementing the 314-bp fimS region from the original EC958 genome sequence.


**2. PARSING OF READS TO GIVE DIFFERING PERCENTAGES OF OFF/ON (as indicated above)**

The easiest way to parse the files would be to just take the first ~80%/~20%. However, we cannot be sure that this will give us an 
"equal" distribution of reads across the fimS region. We will try this first and then investigate alternative methods if it doesn't
work.

Testing this seemed to work and gave the desired % of reads mapping to either the OFF or ON orientation.
Unfortunately, my DiSCO script still had "delete original files" coded into it, so I lost the original files and had to generate them again.

**PARSING OF READS:**

The files have 10,000,000 reads, so there are 40,000,000 lines in the files (as each read has a header, sequence, quality score etc.).
The reads are also listed as r1, r2, r3 etc. so taking a random list from the "OFF" file and merging with the "ON" file would be 
slightly harder than first anticipated.
The easier way is to take the first ~80% of lines from one file, and the last ~20% lines from the other file.

To do this:

$ head -32000000 EC958-OFF_fir.fastq > EC958-OFF80_1.fastq
$ tail -8000000 EC958-ON_fir.fastq >> EC958-OFF80_1.fastq
$ head -32000000 EC958-OFF_sec.fastq > EC958-OFF80_2.fastq
$ tail -8000000 EC958-ON_sec.fastq >> EC958-OFF80_2.fastq


**3. RUNNING DiSCO**

The DiSCO package (or whatever it'll be named in the future) is available on github. It is one script, but it requires a few different
files to be constructed before it can be run. Refer to information on github.


**4. RESULTS**

DiSCO was run on all of the "merged simreads" for the OFF/ON inversion test. This test proved successful. All of the files were 
generated and run on the binf-training server in leah@binf-training:~/Projects/GemSIM_fimS_pseudoseq 



Part 2: Method:
---------------

**1. GENERATING PSEUDO-GENOMES WITH DIFFERENT INVERSION SIZES (FROM EC958)**

Need to take a region in EC958 and generate psuedo-references with different inversion sizes.

Take region in sufS (EC958):
*Reason? Arbitrarily chosen*

1. 1821200..1821300 (100 bp)
2. 1821000..1821500 (500 bp)
3. 1820900..1821600 (700 bp)
4. 1820800..1821800 (1000 bp)
5. 1820000..1822000 (2000 bp)
6. 1819000..1822000 (3000 bp)

Need to create extra genomes where these regions are inverted (reverse complemented).
Then, need to generate pseudo-references that have 1000 bp flanking region. This can be done with the newly generated CIRQUE_create_reference.py script.


