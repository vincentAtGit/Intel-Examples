# American Sign Language (ASL) Classification Using Computer Vision & IoT

![American Sign Language (ASL) Classification Using Computer Vision & IoT](images/Sign-Language-Classification.png)

## Introduction

**American Sign Language (ASL) Classification Using Computer Vision & IoT** combines **Computer Vision** and the **Internet of Things** to provide a way to train a neural network with labelled American Sign Language images to translate hand gestures in near real-time.

The project uses the power of the **Intel® Movidius** and uses a custom trained **Inception V3 model** to carry out **image classification** locally. IoT communication is powered by the [IoT JumpWay](https://iot.techbubbletechnologies.com "IoT JumpWay") to communicate with connected devices and applications.

- **Acknowledgement:** Uses code from Intel® **movidius/ncsdk** ([movidius/ncsdk Github](https://github.com/movidius/ncsdk "movidius/ncsdk Github"))
- **Acknowledgement:** Uses code from chesterkuo **imageclassify-movidius** ([imageclassify-movidius Github](https://github.com/chesterkuo/imageclassify-movidius "imageclassify-movidius Github"))

![Intel® Movidius](../Images/movidius.jpg)

## What Will We Do?

1.  Install the [Intel® NCSDK](https://github.com/movidius/ncsdk "Intel® NCSDK") on a Linux development device.
2.  Install the [Intel® NCSDK API](https://github.com/movidius/ncsdk "Intel® NCSDK API") on a Raspberry Pi 3 / UP Squared.
3.  Install the [IoT JumpWay Python MQTT Client](https://github.com/AdamMiltonBarker/JumpWayMQTT "IoT JumpWay Python MQTT Client") on the Raspberry Pi / UP Squared and configure the IoT JumpWay.
4.  Clone & Set Up The Repo.
5.  Prepare your training dataset.
6.  Finetuning your training parameters.
7.  Train Inception V3 ASL Classification model on a local CPU / GPU.
8.  Convert the model to a format suitable for the Movidius.
9. Test the ASL classifier locally on the Linux development device.
10. Live ASL classification.

## Applications

**American Sign Language (ASL) Classification Using Computer Vision & IoT** is made up of 4 core applications:

- **Trainer:** A training program that allows you to train a convolutional neural network using a local CPU / GPU.
- **Evaluator:** An evaluation program for evaluating your model.
- **Classifier:** A classification program for testing your model.
- **Server:** A server that streams the frames of a connected webcam or Realsense camera.

## Python Versions

- Tested in Python 3.5

## Software Requirements

- [Intel® NCSDK](https://github.com/movidius/ncsdk "Intel® NCSDK")
- [Tensorflow](https://www.tensorflow.org/install "Tensorflow") 1.4.0
- [IoT JumpWay Python MQTT Client](https://github.com/iotJumpway/JumpWayMQTT "IoT JumpWay Python MQTT Client")

## Hardware Requirements

- 1 x Intel® Realsense or compatible webcam.
- 1 x [Intel® Movidius](https://www.movidius.com/ "Intel® Movidius").
- 1 x Linux Device for training & converting the trained model to a Movidius friendly model.
- 1 x Raspberry Pi 3 / UP Squared for the classifier / server.

## Install NCSDK On Your Development Device

The first thing you will need to do is to install the **NCSDK** on your development device, this will be used to convert the trained model into a format that is compatible with the Movidius.

```
 $ mkdir -p ~/workspace
 $ cd ~/workspace
 $ git clone https://github.com/movidius/ncsdk.git
 $ cd ~/workspace/ncsdk
 $ make install
```

Next plug your Movidius into your device and issue the following commands:

```
 $ cd ~/workspace/ncsdk
 $ make examples
```

## Install NCSDK On Your Raspberry Pi 3 / UP Squared

![Intel® Movidius](images/UP2.jpg)

Next you will need to install the **NCSDK** on your Raspberry Pi 3 / UP Squared device, this will be used by the classifier to carry out inference on local images or frames received via the camera. Make sure you have the Movidius plugged in.

```
 $ mkdir -p ~/workspace
 $ cd ~/workspace
 $ git clone https://github.com/movidius/ncsdk.git
 $ cd ~/workspace/ncsdk/api/src
 $ make
 $ sudo make install
```
```
 $ cd ~/workspace
 $ git clone https://github.com/movidius/ncappzoo
 $ cd ncappzoo/apps/hello_ncs_py
 $ python3 hello_ncs.py
```

## Getting Started With The IoT JumpWay

There are a few tutorials that you should follow before beginning, especially if it is the first time you have used the **IoT JumpWay Developer Program**. If you do not already have one, you will require an **IoT JumpWay Developer Program developer account**, and some basics to be set up before you can start creating your IoT devices. Visit the following [IoT JumpWay Developer Program Docs (5-10 minute read/setup)](https://github.com/iotJumpWay/IoT-JumpWay-Docs/ "IoT JumpWay Developer Program Docs (5-10 minute read/setup)") and check out the guides that take you through registration and setting up your Location Space, Zones, Devices and Applications (About 5 minutes read).

## Install IoT JumpWay Python MQTT Client On Your Raspberry Pi 3 / UP Squared

Next install the IoT JumpWay Python MQTT Client on your Raspberry Pi 3 / UP Squared. For this you can execute the following command:

```
 $ pip3 install JumpWayMQTT
```

## IoT JumpWay Device Connection Credentials & Settings

- Setup an IoT JumpWay Location Device for IDC Classifier, ensuring you set up a camera node, as you will need the ID of the  camera for the project to work. Once your create your device add the location ID and Zone ID to the **IoTJumpWay** details in the confs file located at **model/confs.json**, also add the device ID and device name exactly, add the MQTT credentials to the **IoTJumpWayMQTT**.

Add your local or public IP and port that will be used for the socket server.

You will need to edit your device and add the rules that will allow it to communicate autonomously with the other devices and applications on the network, but for now, these are the only steps that need doing at this point.

Follow the [IoT JumpWay Developer Program (BETA) Location Device Doc](https://github.com/iotJumpWay/IoT-JumpWay-Docs/blob/master/4-Location-Devices.md "IoT JumpWay Developer Program (BETA) Location Device Doc") to set up your devices.

```
{
    "IoTJumpWay": {
        "Location": 0,
        "Zone": 0,
        "Device": 0,
        "DeviceName" : "",
        "App": 0,
        "AppName": ""
    },
    "Actuators": {},
    "Cameras": [
        {
            "ID": 0,
            "URL": 0,
            "Name": "",
            "SocketIP": "",
            "SocketPort": 0
        }
    ],
    "Sensors": {},
	"IoTJumpWayMQTT": {
        "MQTTUsername": "",
        "MQTTPassword": ""
    },
    "ClassifierSettings":{
        "dataset_dir":"model/train/",
        "log_dir":"model/_logs",
        "log_eval":"model/_logs_eval",
        "classes":"model/classes.txt",
        "labels":"labels.txt",
        "labels_file":"model/train/labels.txt",
        "validation_size":0.3,
        "num_shards":10,
        "random_seed":50,
        "tfrecord_filename":"200label",
        "file_pattern":"200label_%s_*.tfrecord",
        "image_size":299,
        "num_classes":29,
        "num_epochs":60,
        "dev_cloud_epochs":1,
        "test_num_epochs":1,
        "batch_size":10,
        "test_batch_size":36,
        "initial_learning_rate":0.0001,
        "learning_rate_decay_factor":0.96,
        "num_epochs_before_decay":10,
        "NetworkPath":"",
        "InceptionImagePath":"model/test/",
        "InceptionThreshold": 0.54,
        "InceptionGraph":"igraph"
    }
}
```

## Cloning The Repo

You will need to clone this repository to a location on your development terminal. Navigate to the directory you would like to download it to and issue the following commands.

    $ git clone https://github.com/iotJumpway/IoT-JumpWay-Intel-Examples.git

Once you have the repo, you will need to find the files in this folder located in [IoT-JumpWay-Intel-Examples/master/Intel-Movidius/ASL-Classification](https://github.com/iotJumpway/IoT-JumpWay-Intel-Examples/tree/master/Intel-Movidius/ASL-Classification "IoT-JumpWay-Intel-Examples/master/Intel-Movidius/ASL-Classification").

## Preparing Your ASL Training Data

For this tutorial, I used a dataset from Kaggle ( [ASL Alphabet](https://www.kaggle.com/grassknoted/asl-alphabet "ASL Alphabet") ), but you are free to use any dataset you like. Once you decide on your dataset you need to arrange your data into the **model/train** directory. Each subdirectory should be named with integers, I used 0 to 29 to represent each letter in the alphabet plus 3 additional classes representng delete, nothing and space. Each class should have an equal amount of training samples, in my case I had 3000 examples for each class. The data provided is 200px x 200px, as Inception V3 was trained on images of size 299px x 299px, the images are resized to 299px x 299px, ideally the images would be that size already so you may want to try different datasets and see how your results vary.

## Finetuning Your Training Parameters

You can finetune the settings of the network at any time by editing the classifier settings in the **required/confs.json** file.

```
"ClassifierSettings":{
    "dataset_dir":"model/train/",
    "log_dir":"model/_logs",
    "log_eval":"model/_logs_eval",
    "classes":"model/classes.txt",
    "labels":"labels.txt",
    "labels_file":"model/train/labels.txt",
    "validation_size":0.3,
    "num_shards":10,
    "random_seed":50,
    "tfrecord_filename":"200label",
    "file_pattern":"200label_%s_*.tfrecord",
    "image_size":299,
    "num_classes":29,
    "num_epochs":60,
    "dev_cloud_epochs":1,
    "test_num_epochs":1,
    "batch_size":10,
    "test_batch_size":36,
    "initial_learning_rate":0.0001,
    "learning_rate_decay_factor":0.96,
    "num_epochs_before_decay":10,
    "NetworkPath":"",
    "InceptionImagePath":"model/test/",
    "InceptionThreshold": 0.54,
    "InceptionGraph":"igraph"
}
```

## Training Your IDC Model On A Local Device (CPU or GPU)

Once you have prepared your training data and have Tensorflow & NCSDK installed on your device, you are ready to start training. For training I suggest using an NVIDIA GPU equal to or higher than a GTX 1050 ti, I had tested on a 750 ti but it failed due to memory, if you do use a GPU make sure you install Tensorflow GPU and not Tensorflow CPU. To begin training, you simply need to issue the following commands:

```
$ cd ~/IoT-JumpWay-Intel-Examples/master/Intel-Movidius/IDC-Classification/classifier
$ ./Trainer.sh
```

The contents of Trainer.sh are as follows:

```
#TASS Movidius Trainer
pip3 install -r requirements.txt
python3.5 Trainer.py DataSort
python3.5 Trainer.py Train
mvNCCompile model/MovidiusInception.pb -in=input -on=InceptionV3/Predictions/Softmax -o igraph
python3.5 Eval.py
python3.5 Classifier.py InceptionTest
```

1. Install any requirements
2. Sort our training data
3. Train the model
4. Compile the model for Movidius
5. Evaluate
6. Test

## Evaluating & Testing Your Model

Once training has finished and the Movidius compatible graph has been generated, the shell script will execute the evaluation program and start the classifier in test mode.  The output of the program will be shown in your terminal. 

## Bugs/Issues

Please feel free to create issues for bugs and general issues you come across whilst using this or any other Intel® related IoT JumpWay issues. You may also use the issues area to ask for general help whilst using the IoT JumpWay in your IoT projects.

## Contributors

[![Adam Milton-Barker, Intel® Software Innovator](../../images/Intel-Software-Innovator.jpg)](https://github.com/AdamMiltonBarker)

