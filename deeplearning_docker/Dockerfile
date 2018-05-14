FROM nvidia/cuda:8.0-cudnn6-devel-ubuntu16.04 

#RUN echo "deb http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list

ENV APT_INTALL="apt-get install -y --no-install-recommends"
RUN apt-get update && $APT_INTALL \
         build-essential \
         cmake \
         git \
         openssh-client \
         curl \
         vim-gtk \
         tmux \
         unzip \
         ca-certificates \
         libjpeg-dev \
         libopenexr-dev \
         libpng-dev \
         sudo
         

         

RUN echo "deb http://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/nvidia-ml.list
ENV CUDNN_VERSION 6.0.21
LABEL com.nvidia.cudnn.version="${CUDNN_VERSION}"

RUN apt-get update && $APT_INTALL --allow-unauthenticated \
            libcudnn6=$CUDNN_VERSION-1+cuda8.0 \
            libcudnn6-dev=$CUDNN_VERSION-1+cuda8.0  \
                  libnccl2=2.0.5-2+cuda8.0 \
         libnccl-dev=2.0.5-2+cuda8.0 \
         libcupti-dev
         
ENV PYTHON_LIBS="numpy matplotlib h5py scipy scikit-image Pillow tensorboardX"

ENV PIP3I="python3 -m pip install "
ENV PIP2I="python -m pip install "

RUN $APT_INTALL \
    python3 python3-pip python3-dev python3-tk python3-pil.imagetk
RUN $PIP3I --upgrade pip
RUN $PIP3I setuptools
RUN $PIP3I $PYTHON_LIBS
RUN curl -L https://github.com/jamesbowman/openexrpython/zipball/f6fb5bc8cb79744029067cdfb16cc3db9f8cca9f/ -o openexrpython.zip && unzip  openexrpython.zip -d openexrpython && rm openexrpython.zip && cd openexrpython && python3 -m  easy_install -U openexr && cd .. && rm -r openexrpython
RUN $PIP3I http://download.pytorch.org/whl/cu80/torch-0.2.0.post3-cp35-cp35m-manylinux1_x86_64.whl torchvision

RUN $APT_INTALL \
    python python-pip python-dev python-tk
RUN $PIP2I --upgrade pip
RUN $PIP2I setuptools
RUN $PIP2I $PYTHON_LIBS
RUN $PIP2I OpenExr
RUN $PIP2I  http://download.pytorch.org/whl/cu80/torch-0.2.0.post3-cp27-cp27mu-manylinux1_x86_64.whl 
RUN $PIP2I torchvision

RUN $PIP2I tensorflow-gpu
RUN $PIP3I tensorflow-gpu

ENV LD_LIBRARY_PATH /usr/local/cuda/extras/CUPTI/lib64:$LD_LIBRARY_PATH

RUN mkdir /hooks
COPY startup.sh /hooks/startup.sh
COPY stop.sh /hooks/stop.sh
