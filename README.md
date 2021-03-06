1. Introduction

This user manual presents the guide for implementing the codes for binary classification on a very high-resolution satellite image in case of mapping informal settlements using deep learning through python programming. The project used a U-net model architecture. U-net is one of Fully Convolutional Networks that are used for pixel-based image classification also known as image segmentation. This manual is a follow up to the project report on land cover classification from satellite imagery using python programming. The manual covers six steps followed to implement the project and is aimed to guide the user to implement, use and implement similar project using the same methods and codes.  The project was implemented in Python 3. The project used a computer (workstation) with Intel(R) Core(TM) i7-9750H CPU at 2.60 GHz, with RAM of 16 GB and dual GPU: Nvidia Quadro T1000 and Intel(R) UDH Graphics 630.

1.1. Description of data

The project is implemented using VHR optical image (RGB band) downloaded from Google Earth using the SAS Planet tool (http://www.sasgis.org/sasplaneta/). The VHR image used was downloaded with enough zoom level with sub-meter pixel resolution (0.65 cm). The image covers a part of Kigali city in Rwanda. This implementation is tested on a binary classification to classify area as informal settlement or not  informal. But can be also implemented on multi-classes classification depending on reference data prepared with more classes. The image was acquired in 2020. In addition, the project has used manually prepared labels. These labels serve as reference labels for the image classification and were obtained by visual interpretation of each tile image. The visual interpretation was made by drawing polygons with labels that cover corresponding classes on each tile where two classes were selected, Informal settlement and others and the results was reference maps. The reference map for each tile was obtained by converting corresponding polygons to the file in TIFF format using QGIS 3 software. Pixels belonging to informal settlement were given label of 1 and pixels belonging to others were given label of 0.

1.2. Description of codes structure

The codes start by a block of codes for importing modules and packages which are dependencies libraries for data preparation and training the deep learning model. The following block consists of codes for data access from the storage, opening data and data preparation. This allows the preparation of input data for the model. This is followed by a block of codes for building the model alongside a block of codes indicating the metrics for model behaviour is also be illustrated. The blocks of codes for model definition will follow and hence the codes for model training as well as plotting learning curves. The following blocks of codes will be the blocks of codes for testing the model. In summary, the codes will be structured in the blocks of codes for dataset analysis, dataset preparation, model creation (building), model definition, model training and model evaluation.

1.3. Python packages and modules 

Major python modules required for project are in two groups: modules for data preparation and modules for deep learning. Modules for data preparation include: Geospatial Data Abstraction Library (GDAL) for reading and writing raster and vector data, NumPy library for multi-dimensional arrays and matrices functions to operate these arrays, Matplotlib for plotting, OS library for interacting with data directory, Skimage for image processing and sklearn for splitting training data into train data and test data, and pyrsgis for exporting GeoTIFFS. Modules for deep learning include: Keras, tensorflow and Hdf5 for saving deep learning model. Keras is an open-source library that provides a Python interface for artificial neural networks. Keras is a powerful and easy-to-use free open source Python library for developing and evaluating deep learning models. It acts as an interface for the tensorflow library. In this project, tensorflow will be used in the background which means that functions called in Keras also call tensorflow.

2. Implementation in python programming

4.1. Pre-requisites

To get started, the user need to create a new working environment and install the following modules and packages on the computer:
???	Python 3.6
???	Keras 2.3
???	Tensorflow 1.15 or Tensorflow-gpu 1.15
???	GDAL
???	Matplotlib
???	Hdf5 1.10.5
???	Scikit-image
???	Scikit-learn
???	Pyrsgis

After installing the above libraries, import them in the python scripts referring to the codes attached on this manual. Start by importing libraries for data preparation and follow by importing libraries for deep learning model.

2.2. Data preparation

In this part, the first lines of the codes access the data from the directory, the following code section show the functions that open and read input data as 3-dimentional array using GDAL and codes that print the output shape. This is followed by two functions that visualize input image tiles and labels and codes that call the function to visualize sample input tile and label.
The code section that follows define the size of the input data to the model (patch size) and the number of bands of image. Define the function that split input image and corresponding labels. And assign tiles and then loop through all tiles and labels to sample each tile in a grid wise manner and print number of the results patches. Take a random patch number and call the previously defined functions for visualization of inputs image and tiles to visualize the resulting patch and its associated labels as input to the model.

Then, define and call a function that change label data into one-hot encoding. This serve as a way of working with categorical data in deep learning models since they cannot work with categorical data directly. Hence, split input data into training and test data. This implementation consider 80% data for training and 20% data for testing due to limited availability of training data. The user can run the code to print and inspect the shape of input training data and associated labels as well as input test data and associated labels.

2.3. Training the model

The block of codes for training the model starts with a section of codes that set the parameters for model. In parameters include setting number of bands of input image tiles, number of classes and number of epochs. The following codes section is a function defining the U-Net deep learning model. The model architecture used in this project is a modified U-Net model architecture adapted from Wang et al. (2019).

The model starts by the section of codes defining the encoder part of the model. The model use the MobileNetV2 model as the base model. Hence comes sections of codes for the U-Net model. The actual U-Net model starts by indicating the size of input images followed by convolutional layer with 16 filters each of size 3x3. The batch normalization layer is then used followed by  a relu activation layer and then another convolutional layer with 16 filters of size 3x3, batch normalization layer, relu activation layer and a maxpooling layer of 2x2 size. It is important to know that the output of previous layer is used for the next layer. You can note that similar codes sections repeat 4 times with exponential  growth of convolutional layers from 16 to 32, 64 and 128. The fifth section of codes is also similar to the other with convolutional layers with 256 filters but does not have a maxpooling layer. The above described codes sections made a part of the model called an encoder and then follows codes sections that made the second part of the model called decoder. In decoder parts, codes sections are written in by transposing convolutional layers. Means, the codes sections replicate the encoder in opposite direction and hence use transpose as a transformation to achieve that. Batch normalization help the model to regularize and learn faster.
The codes for a function defining the model are followed by codes section of functions for metrics that helps to access the behaviour of the model. That section is followed by the section of codes that define the optimization, calling the model function and model compilation to have the complete built U-net model followed by a line of codes that visualize the model architecture. 

The line that compile line of codes features a loss function which is a binary cross-entropy. This is due to the problem we want to solve of classifying our image in two classes (informal settlements and non-informal settlements). If the user prefer to implement multi-classes classification, the loss function should be replaced by the categorical cross-entropy. The functions for accessing model behaviour do not play any role in learning but are only useful for assessment of the model performance.
The following codes section is made of the code to save the trained model and a function for training the model and a call to that function for training the model. Training the model takes a long time due to the capacity of the computer being used and the size of the data. The user need to wait until training process is completed. Then comes the blocks of codes for evaluating the model performance. Those blocks are composed of the two codes sections for plotting training curves (accuracy and loss) and the section of codes for evaluating the model performance on training set and test set. 

The last part is made of codes section for inferencing the model on the image tiles for prediction. The first section of codes prepare the image tile to be fed into the model for prediction. The following section of codes is optional and serves as the check for the results of data preparation from the previous section. The last section complies the codes for predicting the input data using the trained model and hence saving the output. Then the user can open the predicted image in a GIS software to check how it looks and do further analysis.
