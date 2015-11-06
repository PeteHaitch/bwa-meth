bwa-parclip
========

bwaparclip is an **experimental** a hack based on
[bwameth](https://github.com/brentp/bwa-meth/) to align PAR-CLIP reads without
intermediate temp files. This works for single-end reads and for **paired-end
reads from the directional protocol**. All praise should go Brent Pedersen for
making bwa-meth, and all blame to me for mistakes introduced in bwa-parclip.

Uses the method of *in silico* conversion of all T's to C's in both reference
and reads.

Recovers the original read by attaching it as a comment which **bwa** appends
as a tag to the read.

QuickStart
==========

Without installation, you can use as `python bwaparclip.py` with install, the
command is `bwaparclip.py`.

The commands:

    bwaparclip.py index $REF
    bwaparclip.py --reference $REF some_R1.fastq.gz some_R2.fastq.gz --prefix some.output

will create `some.output.bam` and `some.output.bam.bai`.
To align single end-reads, specify only 1 file.

Installation
============

The following snippet should work for most systems that have samtools and bwa
installed and the ability to install python packages. (Or, you can send this
to your sys-admin). See the dependencies section below for further instructions:

```Shell

    # these 4 lines are only needed if you don't have toolshed installed
    wget https://pypi.python.org/packages/source/t/toolshed/toolshed-0.4.0.tar.gz
    tar xzvf toolshed-0.4.0.tar.gz
    cd toolshed-0.4.0
    sudo python setup.py install

    wget https://github.com/PeteHaitch/bwa-parclip/archive/v0.0.9000.tar.gz
    tar xzvf v0.0.9000.tar.gz
    cd bwa-parclip-0.0.9000/
    sudo python setup.py install

```

After this, you should be able to run: `bwaparclip.py` and see the help.

Dependencies
------------

`bwa-parclip` depends on

 + python 2.7+ (including python3)
   - `toolshed` library. can be installed with:
      * `easy_install toolshed` or
      * `pip install toolshed`

   - if you don't have root or sudo priveleges, you can run
     `python setup.py install --user` from this directory and the bwaparclip.py
     executable will be at: ~/.local/bin/bwaparclip.py

   - if you do have root or sudo run: `[sudo] python setup.py install` from
     this directory

   - users unaccustomed to installing their own python packages should
     download anaconda: https://store.continuum.io/cshop/anaconda/ and
     then install the toolshed module with pip as described above.

 + samtools command on the `$PATH` (https://github.com/samtools/samtools)

 + bwa mem from: https://github.com/lh3/bwa


usage
=====

Index
-----

One time only, you need to index a reference sequence.

    bwaparclip.py index $REFERENCE

If your reference is `some.fasta`, this will create `some.c2t.fasta`
and all of the bwa indexes associated with it.

Align
-----

    bwaparclip.py --threads 16 \
         --prefix $PREFIX \
         --reference $REFERENCE \
         $FQ1 $FQ2

This will create $PREFIX.bam and $PREFIX.bam.bai. The output will pass
Picard-tools ValidateSam and will have the
reads in the correct location (flipped from G => A reference).

Handles clipped alignments and indels correctly. Fastqs can be gzipped
or not.

The command above will be sent to BWA to do the work as something like:

    bwa mem -L 25 -pCM -t 15  $REFERENCE.c2t.fa \
            '<python bwaparclip.py c2t $FQ1 $FQ2'

So the converted reads are streamed directly to bwa and **never written
to disk**. The output from that is modified by `bwa-parclip` and streamed
straight to a bam file.
