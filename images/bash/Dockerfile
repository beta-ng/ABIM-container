# Base Image: Use a specific version of bash on a minimal Alpine base
FROM bash:5.2-alpine3.20

# Metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Configuration: Create a symlink to ensure scripts with a `#!/bin/bash` shebang run correctly.
# This provides compatibility as many scripts expect bash at this standard path.
RUN ln -sf /usr/local/bin/bash /bin/bash