FROM ubuntu:22.04

# Set non-interactive frontend to avoid prompts during build
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=UTC

# Install essential packages for Raspberry Pi development
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    python3 \
    python3-dev \
    python3-pip \
    wget \
    nano \
    libhdf5-dev \
    libatlas-base-dev \
    libjpeg-dev \
    libpng-dev \
    curl \
    ssh \
    rsync \
    python3-gpiozero \
    \
    && apt-get clean 

# Install Yocto Project dependencies
RUN apt-get update && apt-get install -y \
    gawk \
    wget \
    git-core \
    diffstat \
    unzip \
    texinfo \
    gcc-multilib \
    build-essential \
    chrpath \
    socat \
    cpio \
    python3-pip \
    python3-pexpect \
    xz-utils \
    debianutils \
    iputils-ping \
    python3-git \
    python3-jinja2 \
    libsdl1.2-dev \
    python3-subunit \
    mesa-common-dev \
    zstd \
    liblz4-tool \
    file \
    locales \
    && apt-get clean

# Install Jenkins dependencies and Jenkins
RUN apt-get update && apt-get install -y \
    openjdk-17-jdk \
    ca-certificates \
    gnupg \
    lsb-release \
    && apt-get clean

# Add Jenkins repository and install Jenkins
RUN curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | gpg --dearmor -o /usr/share/keyrings/jenkins-keyring.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | tee /etc/apt/sources.list.d/jenkins.list > /dev/null \
    && apt-get update \
    && apt-get install -y jenkins \
    && apt-get clean \
    && service jenkins stop \
    && systemctl disable jenkins

RUN pip3 install kas

# Set up working directory
WORKDIR /work

# Set environment variables
ENV PYTHONPATH=/workspace:$PYTHONPATH
ENV JENKINS_HOME=/var/jenkins_home

# Set timezone
ENV TZ=UTC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Create a non-root user with host user ID for permission sync
ARG USER_ID=1000
ARG GROUP_ID=1000

RUN groupadd -g ${GROUP_ID} pi && \
    useradd -u ${USER_ID} -g ${GROUP_ID} -m pi && \
    chown -R pi:pi /work && \
    mkdir -p /var/jenkins_home && \
    chown -R pi:pi /var/jenkins_home

# Configure and generate locales
RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && \
    locale-gen
ENV LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=en_US.UTF-8

# Copy Jenkins startup script
COPY jenkins-start.sh /usr/local/bin/jenkins-start.sh
RUN chmod +x /usr/local/bin/jenkins-start.sh

USER pi

# Default command
CMD ["/bin/bash"]
