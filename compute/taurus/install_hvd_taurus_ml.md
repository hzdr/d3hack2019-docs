# Installation guide for pytorch with horovod at taurusml

## Get a node 

```
salloc -N 1 --ntasks-per-node=6 -p ml --time 08:00:00
```

## Login to Node 

### Use squeue for getting the assigned taurusml node 

```
squeue -p ml
```

### Login 

```
ssh <taurusmlN>

```
##Load dependencies :

```
module load modenv/ml
module load OpenMPI/3.1.4-gcccuda-2018b
module load PythonAnaconda/3.6
module load cuDNN/7.1.4.18-fosscuda-2018b
module load CMake/3.11.4-GCCcore-7.3.0
```

## Create conda environment 

```
conda create --name horovod python=3.6 anaconda
source activate horovod
```


##Install Pytorch from Source 

```
git clone --recursiv https://github.com/pytorch/pytorch
cd pytorch 
CXXFLAGS="-I/usr/include" CFLAGS="-I/usr/include" python3.6 setup.py install
cd ~
```

 
##Install further  Horovod dependencies 
 
```
conda install protobuf
conda install absl-py
```

 
##Install Horovod from source
 
```
git clone --recursiv https://github.com/horovod/horovod.git
cd horovod
python3.6 setup.py sdist
 
CXXFLAGS="-I/usr/include" CFLAGS="-I/usr/include" HOROVOD_GPU_ALLREDUCE=MPI\ HOROVOD_WITHOUT_TENSORFLOW=1 HOROVOD_WITH_PYTORCH=1 HOROVOD_WITHOUT_MXNET=1\  python3.6 -m pip install --no-cache-dir dist/horovod-0.18.1.tar.gz
```
 
 

## Example SBATCH-Script

```
#!/bin/bash -l
 
#SBATCH -p ml
#SBATCH -t 00:05:00
#SBATCH --nodes=1
#SBATCH --ntasks=6
#SBATCH --cpus-per-task=26
#SBATCH --gres=gpu:6
#SBATCH --mem=0
#SBATCH -o experiment6.out
 
module load modenv/ml
module load OpenMPI/3.1.4-gcccuda-2018b
module load PythonAnaconda/3.6
module load cuDNN/7.1.4.18-fosscuda-2018b
module load CMake/3.11.4-GCCcore-7.3.0
 
source activate horovod
 
srun --output="output.log" python3.6 -c\					 "import hvd.torch as hvd;
                   hvd.init();
                   print(‘Hello from:’,hvd.rank())”



