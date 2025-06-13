# Base Image
ARG PARENT_IMAGE
FROM $PARENT_IMAGE

# Args need to be below FROM!
ARG USER_ID
ARG GROUP_ID
ARG PYTHON_VERSION=3.10

ENV NVIDIA_DRIVER_CAPABILITIES all

# Install os-level packages
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    bash-completion \
    build-essential \
    ca-certificates \
    cmake \
    curl \
    git \
    htop \
    libegl1 \
    libxext6 \
    libjpeg-dev \
    libpng-dev  \
    libvulkan1 \
    ffmpeg \
    rsync \
    tmux \
    unzip \
    vim \
    vulkan-utils \
    wget \
    xvfb \
    # lib for SAPIEN rendering
    libglvnd-dev \
    && rm -rf /var/lib/apt/lists/*

# Install (mini) conda
RUN curl -o ~/miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh && \
    chmod +x ~/miniconda.sh && \
    ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda init && \
    /opt/conda/bin/conda install -y python="$PYTHON_VERSION" && \
    /opt/conda/bin/conda clean -ya

ENV PATH /opt/conda/bin:$PATH
SHELL ["/bin/bash", "-c"]

# rlds converter
COPY ./ /rlds_dataset_mod
WORKDIR rlds_dataset_mod
RUN conda env create -f environment_ubuntu.yml

# install gsutil
# Add the Google Cloud SDK distribution URI as a package source
RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list
# Import the Google Cloud public key
RUN curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -
# Update and install the Google Cloud SDK (includes gsutil)
RUN apt-get update && apt-get install -y google-cloud-sdk

# Change permissions
WORKDIR /user
RUN groupadd -g ${GROUP_ID} user
RUN useradd --shell /bin/bash -u ${USER_ID} -g ${GROUP_ID} -o -d /user user
RUN chown -R user:user /user && chmod -R u+w /user
# RUN chown -R user:user / && chmod -R u+w /

# Set python ENV variables
ENV PYTHONUNBUFFERED=1
