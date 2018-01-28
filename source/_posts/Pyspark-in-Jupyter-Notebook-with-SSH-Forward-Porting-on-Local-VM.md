---
title: Pyspark in Jupyter Notebook with SSH Forward Porting on Local VM
date: 2017-10-28 16:55:19
tags: 
- BigData
- Pyspark
---

# Pyspark in Jupyter Notebook with SSH Forward Porting on Local VM

This is a note I used to setup Pyspark environment on local VM with vagrant. 

### Step One — Spawn Local VM

Vagrant up a local virtual machine. There is only one line in VagrantFile that needs to change. Comment back the following line. Note, the guest port should be 8888 as the defualt port for Jupyter notebook is 8888. The host port can be whatever you like. 

```
config.vm.network "forwarded_port", guest: 8888, host: 8888
```

It's recommended that you set up a shared folder as always. We need to use it to pass the Spark.tgz file into VM. The corresponding line in VagrantFile is:

```
config.vm.synced_folder "./ShareFile", "/vagrant_data"
```

where `ShareFile ` is a folder I created on the same directory as VagrantFile. You can name and put your sharefolder in whatever manner you like.



### Step Two — Install Needed Packages

Use `vagrant ssh` to shell into your local VM. Install packages using the following command:

```
sudo apt-get update
sudo apt-get install -y python3
sudo apt-get install -y python3-pip
pip3 install py4j
pip3 install jupyter
sudo apt-get install -y default-jre
sudo apt-get install -y scala
```

These commands install python3, pip3, py4j,  Jupyter notebook, Java and Scala respectively. Java and Scala are needed because Spark is written in Scala and Scala is written in Java.



### Step Three — Download Spark Latest Version

Download Spark lateset version for ubuntu <a href="https://spark.apache.org/downloads.html">here</a>. I downloaded [spark-2.2.0-bin-hadoop2.7.tgz](https://www.apache.org/dyn/closer.lua/spark/spark-2.2.0/spark-2.2.0-bin-hadoop2.7.tgz). It should be roughly 200 MB. After downloading, put it in the shared folder.

In the local vm, use `mv` to move the tgz file to the `/home/ubuntu` directory, i.e. the root directory. Type the following command to unpack the file. 

```
sudo tar -zxvf spark-2.2.0-bin-hadoop2.7.tgz
```



### Step Four — Configure the Pyspark Environment

At the root directory of the local VM, use whatever text editor you prefer to open the `.bashrc` file. Copy and paste the following line at the end of the file.

```
export SPARK_HOME='/home/ubuntu/spark-2.2.0-bin-hadoop2.7'
export PATH=$SPARK_HOME:$PATH
export PYTHONPATH=$SPARK_HOME/python:$PYTHONPATH

export PYSPARK_DRIVER_PYTHON=jupyter
export PYSPARK_DRIVER_PYTHON_OPTS='notebook'
```

These lines specify the needed path to find the pyspark module in Jupyter notebook. After editting, type this command in the same directory:

```
source ./bashrc
```

Finally, change the permission of the Spark files. 

```
suco chmod 777 spark-2.2.0-bin-hadoop2.7.tgz
cd spark-2.2.0-bin-hadoop2.7.tgz/
sudo chmod 777 python 
cd python/
sudo chmod 777 pyspark
```

These commands will give the user privileges to access the pyspark module. 



### Step Five — Restart the VM and Check

Now restart the virtual machine. You can exit it and use `vagrant reload` to do so. 

After restart, shell into the local vm and under whatever folder, type the following command to start the jupyter notebook:

```
jupyter notebook --ip=0.0.0.0
```

**Note: the —ip argument is needed to enable forward porting!!**

Now you can open a browser on the host machine. Type in `https://localhost:8888` or whatever host port you have chosen. You should be able to access the Jupyter Notebook in the local vm. 

Then in the notebook, try the following command:

```
import pyspark
```

This should not give any output.

If no error is generated, then congratulation, you have successfully setup !

