# srez

Machine creation of plausible speculative images, via deep learning. `srez` uses 4x4 image inputs to create 64x64 image outputs. The created image represents a plausible theory of what the input's actual subject might have been. 

Here's a random, non cherry-picked, example of what `srez` and a neural network can do using deep learning. This below example was produced after training the neural network for 3 hours on a GTX 1080 GPU, equivalent to 130,000 batches or about 10 epochs.

From left to right:

  1. Column 1 is 16x16 input (created for demo purposes by downsampling a 64x64 image)

  2. Column 2 is output of standard bicubic interpolation

  3. Column 3 is 64x64 output of neural net

  4. Column 4 is ground truth image used to produce column 1

![Example output](srez_sample_output.png)

Image creation is determined based on whatever dataset of images ("training set") has been fed to the neural net. Different configulations or different training data will result in the neural net forming different theories and thus create different outputs. For example, compare columns 3 & 4.

  * The training set was mainly composed of well-illuminated faces looking straight ahead. Thus an output's quality is poorer when an imput face is at an angle, poorly illuminated, or partially occluded by eyeglasses or hands.

  * The training set was [Large-scale CelebFaces Attributes (CelebA) Dataset](http://mmlab.ie.cuhk.edu.hk/projects/CelebA.html) is a large-scale face attributes dataset with more than 200K celebrity images. Alternate datasets will yield different outputs. Many other public [face datasets](https://github.com/betars/Face-Resources) are available, including [Labeled Faces in the Wild](http://vis-www.cs.umass.edu/lfw/) which is frequently used in deep learning competitions.


# How it works

In essence the architecture is a DCGAN where the input to the generator network is the 16x16 image rather than a multinomial gaussian distribution.

In addition to that the loss function of the generator has a term that measures the L1 difference between the 16x16 input and downscaled version of the image produced by the generator.

The adversarial term of the loss function ensures the generator produces plausible faces, while the L1 term ensures that those faces resemble the low-res input data. We have found that this L1 term greatly accelerates the convergence of the network during the first batches and also appears to prevent the generator from getting stuck in a poor local solution.

Finally, the generator network relies on ResNet modules as we've found them to train substantially faster than more old-fashioned architectures. The adversarial network is much simpler as the use of ResNet modules did not provide an advantage during our experimentation.

# Requirements

You will need Python 3 with Tensorflow, numpy, scipy and [moviepy](http://zulko.github.io/moviepy/). See `requirements.txt` for details.


# Training the model

After you have the required software above you will also need the `Large-scale CelebFaces Attributes (CelebA) Dataset`. The model expects the `Align&Cropped Images` version. Extract all images to a subfolder named `dataset`. I.e. `srez/dataset/lotsoffiles.jpg`.

Training with default settings: `python3 srez_main.py --run train`. The script will periodically output an example batch in PNG format onto the `srez/train` folder, and checkpoint data will be stored in the `srez/checkpoint` folder.

After the network has trained you can also produce an animation showing the evolution of the output by running `python3 srez_main.py --run demo`.

# About the author

[LinkedIn profile of David Garcia](https://ca.linkedin.com/in/david-garcia-70913311).
