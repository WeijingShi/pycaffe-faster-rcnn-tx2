### Disclaimer
This is a folk from https://github.com/rbgirshick/py-faster-rcnn . Credit goes to the original authors.

### Merge faster-rcnn with latest caffe
If all you want is to try faster RCNN but their code is too old for your awesome new GPU and cuda1000, merging faster-rcnn with the latest caffe could sovle your problem. Thanks to this discussion https://github.com/rbgirshick/py-faster-rcnn/issues/237, here is the solution. 
  ```shell
  # clone python faster-rcnn 
  git clone --recursive https://github.com/rbgirshick/py-faster-rcnn.git
  cd py-faster-rcnn/caffe-fast-rcnn
  # merge with caffe
  git remote add caffe https://github.com/BVLC/caffe.git
  git fetch caffe
  git merge -X theirs caffe/master -m'merge caffe'
  # fix some issues
  sed -i 's/self_.attr("phase") = static_cast<int>(this->phase_);//g' include/caffe/layers/python_layer.hpp
  rm src/caffe/test/test_smooth_L1_loss_layer.cpp
```
### Faster rcnn for nvidia TX2
This caffe faster rcnn is tested on TX2 with Jetpack 3.1 installed. 
If you want to install faster-rcnn on TX2, you can either use this repository or modify original code manually.
  - Use this repository:
  ```shell
  # lots of dependence 
  sudo apt-get update
  sudo apt-get install software-properties-common -y
  sudo add-apt-repository universe
  sudo add-apt-repository multiverse
  sudo apt-get install libboost-dev libboost-all-dev -y
  sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev libatlas-base-dev liblmdb-dev libblas-dev libatlas-  base-dev libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler python-numpy -y
  sudo apt-get install python-pip -y 
  # clone this repository
  git clone --recursive https://github.com/WeijingShi/pycaffe-faster-rcnn-tx2.git
  cd pycaffe-faster-rcnn-tx2/caffe-fast-rcnn
  make all -j $(($(nproc) + 1))
  make test -j $(($(nproc) + 1))
  make runtest -j $(($(nproc) + 1))
  make pycaffe -j $(($(nproc) + 1))
  ```
   If everything goes well, you can follow the steps in https://github.com/rbgirshick/py-faster-rcnn for the rest.
  - Modify manually:
  ```shell
  # lots of dependence 
  sudo apt-get update
  sudo apt-get install software-properties-common -y
  sudo add-apt-repository universe
  sudo add-apt-repository multiverse
  sudo apt-get install libboost-dev libboost-all-dev -y
  sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev libatlas-base-dev liblmdb-dev libblas-dev libatlas-  base-dev libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler python-numpy -y
  sudo apt-get install python-pip -y
  # merge latest caffe
  git clone --recursive https://github.com/rbgirshick/py-faster-rcnn.git
  cd py-faster-rcnn/caffe-fast-rcnn
  git remote add caffe https://github.com/WeijingShi/caffe.git
  git fetch caffe
  git merge -X theirs caffe/master
  sed -i 's/self_.attr("phase") = static_cast<int>(this->phase_);//g' include/caffe/layers/python_layer.hpp
  rm src/caffe/test/test_smooth_L1_loss_layer.cpp
  # edit configuration files
  cp Makefile.config.example Makefile.config
  echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
  sed -i 's/# USE_CUDNN/USE_CUDNN/g' Makefile.config
  sed -i 's/# WITH_PYTHON_LAYER/WITH_PYTHON_LAYER/g' Makefile.config
  sed -i 's/hdf5_hl hdf5/hdf5_serial_hl hdf5_serial/g' Makefile
  sed -i 's/-gencode arch=compute_61,code=sm_61 \\/-gencode arch=compute_61,code=sm_61 \ \\\n \t -gencode arch=compute_62,code=sm_62 \\/g' Makefile.config
  # compile and test
  make all -j $(($(nproc) + 1))
  make test -j $(($(nproc) + 1))
  make runtest -j $(($(nproc) + 1))
  make pycaffe -j $(($(nproc) + 1))
  ```
      Then follow the rest in https://github.com/rbgirshick/py-faster-rcnn 
      
	 

	 
