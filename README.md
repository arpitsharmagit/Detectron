# Detectron Customized

Detectron is released under the [Apache 2.0 license](https://github.com/facebookresearch/detectron/blob/master/LICENSE). See the [NOTICE](https://github.com/facebookresearch/detectron/blob/master/NOTICE) file for additional details.

## Installation 

sudo apt-get update
sudo apt-get upgrade

### Add the ppa repo for NVIDIA graphics driver
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update

### Install the recommended driver (currently nvidia-390)
sudo ubuntu-drivers autoinstall
sudo reboot

#check if drivers were installed
nvidia-smi

wget -O cuda_8.0.61_375.26_linux-run https://drive.google.com/open?id=1k_FdTU39MBpBI73FmDDUCGCGHfvTej2K 
sudo chmod +x cuda_9.0.176_384.81_linux-run
./cuda_9.0.176_384.81_linux-run

# Install cuDNN
wget -O cudnn-9.0-linux-x64-v7.tgz https://drive.google.com/open?id=1nhAMvluxJIjEeamuMETEi36J7nGNrZNw
tar -zxvf cudnn-9.0-linux-x64-v7.tgz 

# copy libs to /usr/local/cuda folder
sudo cp -P cuda/include/cudnn.h /usr/local/cuda/include
sudo cp -P cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

# Install Caffe2
sudo apt-get update
sudo apt-get install -y --no-install-recommends \
      build-essential \
      cmake \
      git \
      libgoogle-glog-dev \
      libgtest-dev \
      libiomp-dev \
      libleveldb-dev \
      liblmdb-dev \
      libopencv-dev \
      libopenmpi-dev \
      libsnappy-dev \
      libprotobuf-dev \
      openmpi-bin \
      openmpi-doc \
      protobuf-compiler \
      python-dev \
      python-pip                          
sudo pip install \
      future \
      numpy \
      protobuf

# for Ubuntu 16.04
sudo apt-get install -y --no-install-recommends libgflags-dev

git clone --recursive https://github.com/caffe2/caffe2.git && cd caffe2

# This will build Caffe2 in an isolated directory so that Caffe2 source is
# unaffected
mkdir build && cd build

# This configures the build and finds which libraries it will include in the
# Caffe2 installation. The output of this command is very helpful in debugging
cmake ..

# This actually builds and installs Caffe2 from makefiles generated from the
# above configuration step
sudo make install

cd ~ && python -c 'from caffe2.python import core' 2>/dev/null && echo "Success" || echo "Failure"

python caffe2/python/operator_test/relu_op_test.py

python2 -c 'from caffe2.python import workspace; print(workspace.NumCudaDevices())'

# add to  ~/.bashrc
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64"
export CUDA_HOME=/usr/local/cuda
alias python2=python
export PATH=/usr/bin:$PATH

export PYTHONPATH=/usr/local:/home/global/detectron/lib
export PYTHONPATH="/home/global/caffe2/build:$PYTHONPATH"
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
export COCOAPI=/home/global/cocoapi

# COCOAPI=/path/to/clone/cocoapi
git clone https://github.com/cocodataset/cocoapi.git $COCOAPI
cd $COCOAPI/PythonAPI
# Install into global site-packages
make install
# Alternatively, if you do not have permissions or prefer
# not to install the COCO API into global site-packages
python2 setup.py install --user

# DETECTRON=/path/to/clone/detectron
git clone https://github.com/facebookresearch/detectron $DETECTRON
cd $DETECTRON/lib && make
python2 $DETECTRON/tests/test_spatial_narrow_as_op.py

## Running on PC (ubuntu)
python tools/infer_simple.py --cfg configs/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml --output-dir /home/global/Downloads/detectron-visualizations --image-ext jpg --wts https://s3-us-west-2.amazonaws.com/detectron/35861858/12_2017_baselines/e2e_mask_rcnn_R-101-FPN_2x.yaml.02_32_51.SgT4y1cO/output/train/coco_2014_train:coco_2014_valminusminival/generalized_rcnn/model_final.pkl demo

