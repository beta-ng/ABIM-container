# Base Image
FROM ubuntu:noble

# Metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Install essential dependencies for Arriba and its downstream analysis
RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update -y && \
    apt-get install -y --no-install-recommends \
        wget \
        ca-certificates \
        rna-star \
        samtools \
        r-base \
        r-cran-circlize \
        r-bioc-genomicalignments \
        r-bioc-genomicranges \
        libxml2 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Download and extract Arriba, then remove the database to be populated by the wrapper script
RUN wget -qO - 'https://github.com/suhrig/arriba/releases/download/v2.5.1/arriba_v2.5.1.tar.gz' \
        | tar -xzf - --exclude='arriba*/.git' && \
    rm -rf /arriba_v2.5.1/database/*

# Create a wrapper script to download reference files
RUN echo '#!/bin/bash\n\
cd /references\n\
/arriba*/download_references.sh $1 && \\\n\
ASSEMBLY=$(sed -e "s/viral+.*//" -e "s/+.*//" <<<"$1") && \\\n\
cp /arriba*/database/*$ASSEMBLY* /references' > /usr/local/bin/download_references.sh && \
    chmod a+x /usr/local/bin/download_references.sh

# Create the main wrapper script to execute Arriba fusion detection
RUN echo '#!/bin/bash\n\
cd /output && \\\n\
/arriba*/run_arriba.sh \
    /references/STAR_index_* \
    /references/*.gtf \
    /references/*.fa \
    /references/blacklist_*.tsv.gz \
    /references/known_fusions_*.tsv.gz \
    /references/protein_domains_*.gff3 \
    ${THREADS-8} \
    /read1.fastq.gz \
    $(ls /read2.fastq.gz 2> /dev/null)' > /usr/local/bin/arriba.sh && \
    chmod a+x /usr/local/bin/arriba.sh

# Create a wrapper script to draw fusion visualizations
RUN echo '#!/bin/bash\n\
Rscript /arriba*/draw_fusions.R \
    --annotation=$(ls /references/*.gtf) \
    --fusions=/fusions.tsv \
    --output=/output/fusions.pdf \
    --proteinDomains=$(ls /references/protein_domains_*.gff3) \
    --alignments=/Aligned.sortedByCoord.out.bam \
    --cytobands=$(ls /references/cytobands_*.tsv)' > /usr/local/bin/draw_fusions.sh && \
    chmod a+x /usr/local/bin/draw_fusions.sh