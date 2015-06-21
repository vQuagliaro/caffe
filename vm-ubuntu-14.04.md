## VM Ubuntu 14.04

Based on https://github.com/BVLC/caffe/wiki/Ubuntu-14.04-VirtualBox-VM

### VagrantFile
```shell  
HOSTNAME = "caffe"
 
Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/trusty64"
    config.vm.hostname = HOSTNAME
 
    config.vm.provider "virtualbox" do |v|
        v.name = HOSTNAME
        v.memory = 2048
    end
end
```

### install packages
* Install system updates (3.13.0-32 -> 3.13.0-36)
* Install build essentials:
  * `sudo apt-get install build-essential`
* Install latest version of kernel headers:
  * ``sudo apt-get install linux-headers-`uname -r` ``
* Install dependencies:
  * `sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev protobuf-compiler gfortran libjpeg62 libfreeimage-dev libatlas-base-dev git python-dev python-pip libgoogle-glog-dev libbz2-dev libxml2-dev libxslt-dev libffi-dev libssl-dev libgflags-dev liblmdb-dev python-yaml`
  * `sudo easy_install pillow`

### Cuda 
* Run the CUDA installer:
  * Download deb from https://developer.nvidia.com/cuda-downloads

### Caffe
* Download Caffe:
  * `cd ~`
  * `git clone https://github.com/vQuagliaro/caffe.git`
* Install python dependencies for Caffe:
  * `cd caffe`
  * `cat python/requirements.txt | xargs -L 1 sudo pip install`
* Add a couple of symbolic links for some reason:
  * `sudo ln -s /usr/include/python2.7/ /usr/local/include/python2.7`
  * `sudo ln -s /usr/local/lib/python2.7/dist-packages/numpy/core/include/numpy/ /usr/local/include/python2.7/numpy`
* Create a `Makefile.config` from the example:
  * `cp Makefile.config.example Makefile.config`
  * `vim Makefile.config`  
    * Uncomment the line `# CPU_ONLY := 1`  (In a virtual machine we do not have access to the the GPU)  
    * Under `PYTHON_INCLUDE`, replace `/usr/lib/python2.7/dist-packages/numpy/core/include` with `/usr/local/lib/python2.7/dist-packages/numpy/core/include` (i.e. add `/local`)  
* Compile Caffe:  
  * `make pycaffe`  
  * `make all`  
  * `make test`  
  
### Test ImageNet
* Download the ImageNet Caffe model and labels:
  * `./scripts/download_model_binary.py models/bvlc_reference_caffenet`
  * `./data/ilsvrc12/get_ilsvrc_aux.sh`
* Test your installation by running the ImageNet model on an image of a kitten:
  * `cd ~/caffe` (or whatever you called your Caffe directory)
  * `python python/classify.py --print_results examples/images/cat.jpg foo`
  * Expected result: `[('tabby', '0.27933'), ('tiger cat', '0.21915'), ('Egyptian cat', '0.16064'), ('lynx', '0.12844'), ('kit fox', '0.05155')]`
