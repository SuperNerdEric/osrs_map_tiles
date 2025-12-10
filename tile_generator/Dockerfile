FROM python:3.10

# Install system packages
# Install OpenJDK 17 from Adoptium (Eclipse Temurin) openjdk-17-jdk
RUN apt-get update && apt-get install -y \
    wget \
    git \
    temurin-17-jdk \
    maven \
    && rm -rf /var/lib/apt/lists/*

# Install miniconda (you may need ca-certificates, too)
RUN apt-get update && apt-get install -y ca-certificates && rm -rf /var/lib/apt/lists/*
RUN wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
RUN chmod +x Miniconda3-latest-Linux-x86_64.sh
RUN ./Miniconda3-latest-Linux-x86_64.sh -b -p /usr/local/bin/miniconda3
ENV PATH="/usr/local/bin/miniconda3/bin:${PATH}"

# Create a conda env
RUN conda create --name vips python=3.7

# Update shell
SHELL ["conda", "run", "--no-capture-output", "-n", "vips", "/bin/bash", "-c"]

# Install pyvips in environment
RUN conda install --channel conda-forge pyvips

# Copy & install Python dependencies
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# (Optional) if you really need it: conda install -c conda-forge openjdk=11
# but we already installed system JDK

# Clone runelite
RUN git clone https://github.com/runelite/runelite.git /runelite
RUN mvn -f /runelite/pom.xml install -DskipTests

ADD src src
CMD [ "conda", "run", "--no-capture-output", "-n", "vips", "python", "./src/tile_generator.py" ]
