I wrote all commands during my installation down so I thought this might help with you're bug research. I've seen that you solved it. But maybe this can help others as a guideline. Good luck to everyone!

System information
Amazon AWS Instance g3.4xlarge (NVIDIA Tesla M60)
Ubuntu 16.04
Installation
I used the Caffe2 documentation and the Detectron documentation as a guideline

Update NVIDIA driver
Download driver

sudo dpkg -i nvidia-diag-driver-local-repo-ubuntu1604_375.66-1_amd64.deb
sudo apt-get update && sudo apt-get install wget -y --no-install-recommends
Installing Dependencies
sudo apt-get update
sudo apt-get install -y --no-install-recommends build-essential cmake git libgoogle-glog-dev libgtest-dev libiomp-dev libleveldb-dev liblmdb-dev libopencv-dev libopenmpi-dev libsnappy-dev libprotobuf-dev openmpi-bin openmpi-doc protobuf-compiler python-dev python-pip
sudo pip install setuptools future numpy protobuf enum networkx
sudo apt-get install -y --no-install-recommends libgflags-dev
Install CUDA
Link: http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/
Search for the newest version. Use if possible cuda 8. I used: cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget "http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/..." //add selected file name
sudo dpkg -i cuda-repo-ubuntu1604_8.0.61-1_amd64.deb
sudo apt-get update
sudo apt-get install cuda-8-0
Install cuDNN
Search for the newest version: https://developer.nvidia.com/cudnn

wget http://developer.download.nvidia.com/compute/redist/cudnn/v5.1/cudnn-8.0-linux-x64-v5.1.tgz
sudo tar -xzf cudnn-8.0-linux-x64-v5.1.tgz -C /usr/local
rm cudnn-8.0-linux-x64-v5.1.tgz && sudo ldconfig
Install Caffe2
Because of the rapid development of Caffe2, you might need to reset the repository to the last build passing version. You can check this on the Caffe2 GitHub repository. Click on the build passing/failing icon in the top of the README.md. On left you can check the last build and select the last passing build. Click on the build and copy the commit number.

Go to your selected installation directory: cd directory
git clone --recursive https://github.com/caffe2/caffe2.git && cd caffe2
git reset --hard $COMMIT_NUMBER //unnecessary when builds passing
sudo make -j16 //exchange 16 by your number of cores
cd build && sudo make install
Set environment variables
add these lines to .bashrc:

export PYTHONPATH=/usr/local
export PYTHONPATH=$PYTHONPATH:/home/ubuntu/caffe2/build
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
Test Caffe2 & GPU
Test your Caffe2 installation:

python2 -c 'from caffe2.python import core' 2>/dev/null && echo "Success" || echo "Failure"
Test the GPU Installation. The return value needs to be bigger than 0:

python2 -c 'from caffe2.python import workspace; print(workspace.NumCudaDevices())'
Install Detectron dependencies
sudo pip install numpy>=1.13 pyyaml>=3.12 matplotlib opencv-python>=3.2 setuptools Cython mock scipy
sudo git clone https://github.com/cocodataset/cocoapi.git $WANTED-DIRECTORY/cocoapi
cd $WANTED-DIRECTORY/cocoapi/PythonApi
sudo -H make install
sudo python setup.py install
add line to .bashrc:

export COCOAPI=$WANTED-DIRECTORY/cocoapi
Don't forget to download the data from the coco website in this directory. Structure as described in the data README.md

Install Detectron
git clone https://github.com/facebookresearch/detectron detectron
cd detectron/lib && sudo make -j16
check Installation

python2 detectron/tests/test_spatial_narrow_as_op.py
Hope I could help some people :)
