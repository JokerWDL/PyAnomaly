# Base on the cuda101
FROM nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04

# Build the miniconda3
ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
ENV PATH /opt/conda/bin:$PATH

RUN apt-get update --fix-missing && \
    apt-get install -y wget bzip2 ca-certificates libglib2.0-0 libxext6 libsm6 libxrender1 git mercurial subversion && \
    apt-get clean

RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh && \
    /bin/bash ~/miniconda.sh -b -p /opt/conda && \
    rm ~/miniconda.sh && \
    /opt/conda/bin/conda clean -tipsy && \
    ln -s /opt/conda/etc/profile.d/conda.sh /etc/profile.d/conda.sh && \
    echo ". /opt/conda/etc/profile.d/conda.sh" >> ~/.bashrc && \
    echo "conda activate base" >> ~/.bashrc && \
    find /opt/conda/ -follow -type f -name '*.a' -delete && \
    find /opt/conda/ -follow -type f -name '*.js.map' -delete && \
    /opt/conda/bin/conda clean -afy

# Create a user
ARG USER_ID=1000
RUN useradd -m --no-log-init --system  --uid ${USER_ID} appuser -g sudo \
    && echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers \
    && groupadd -g ${USER_ID} appuser \
    && chown -R appuser:appuser /opt/conda 

USER appuser
WORKDIR /home/appuser
COPY .condarc /home/appuser
# Create the environment
RUN conda create -n pyanomaly python=3.6 \
    && /bin/bash -c "source activate pyanomaly" \
    && pip install pqi \
    && pqi use tuna \
    && conda install pytorch==1.4 torchvision cudatoolkit=10.1 -c pytorch \
    && pip install cupy-cuda101 \
    && pip install 'git+https://github.com/facebookresearch/detectron2.git' 

RUN pip install yacs \
    && pip install torchsnooper \
    && pip install sklearn \
    && pip install opencv-python \
    && pip install colorama \
    && pip install pillow \
    && pip install ipdb

# Get the Pyanomaly
RUN git clone https://github.com/YuhaoCheng/PyAnomaly.git pyanomaly_repo\
    && cd pyanomaly_repo \
    && cd ./script \
    && sh ./install.sh \
    && cd ..

WORKDIR /home/appuser/pyanomaly_repo
# Inital 
# CMD conda run -n pyanomaly
# ENTRYPOINT conda run -n pyanomaly
