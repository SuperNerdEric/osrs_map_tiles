FROM python:3.10-bookworm

# Install system packages
RUN apt-get update && apt-get install -y \
    wget \
    unzip \
    openjdk-17-jdk \
    && rm -rf /var/lib/apt/lists/*

# Install Gradle (used to build the map image wrapper)
ENV GRADLE_VERSION=8.4
RUN wget -q "https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip" \
    && unzip -q "gradle-${GRADLE_VERSION}-bin.zip" -d /opt \
    && rm "gradle-${GRADLE_VERSION}-bin.zip"
ENV PATH="/opt/gradle-${GRADLE_VERSION}/bin:${PATH}"

# Install miniconda
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
RUN wget -q https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh \
    && chmod +x Miniconda3-latest-Linux-x86_64.sh \
    && ./Miniconda3-latest-Linux-x86_64.sh -b -p /usr/local/bin/miniconda3 \
    && rm Miniconda3-latest-Linux-x86_64.sh
ENV PATH="/usr/local/bin/miniconda3/bin:${PATH}"

RUN conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/main \
    && conda tos accept --override-channels --channel https://repo.anaconda.com/pkgs/r

RUN conda create --name vips python=3.7

SHELL ["conda", "run", "--no-capture-output", "-n", "vips", "/bin/bash", "-c"]

RUN conda install --channel conda-forge pyvips

COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# Build a fat jar that pulls the latest published net.runelite:cache artifact.
# This stays current with new object opcodes in the game cache.
COPY java java
RUN gradle -p java build

ADD src src
CMD [ "conda", "run", "--no-capture-output", "-n", "vips", "python", "./src/tile_generator.py" ]
