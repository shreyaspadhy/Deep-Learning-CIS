# Deep Learning at CIS

This repository contains tutorials and resources to get started on Deep Learning at the [Centre for Imaging Science](http://www.cis.jhu.edu) at Johns Hopkins University

### PyTorch with Nvidia Docker and Jupyter Notebook

To get PyTorch running on an iPython Notebook on a CIS server with NVidia GPUs and PyTorch, CUDA, and conda installed, follow these instructions.

From your local CIS machine, ssh into zeta.cis.jhu.edu by typing

```
$ ssh username@zeta.cis.jhu.edu
```

Now, for the purposes of running and testing Python code with PyTorch, we will be using Dockers, which are self-contained environments that come pre-installed with all the dependencies that we need.

To create a docker that you can test and write code on for the foreseeable future, run the following command

```
$ nvidia-docker run --rm -d --name <yourDockerName> -v /path/to/host/directory:/root/workspace -p 8888:8888 -ti --ipc=host pytorch/pytorch:latest
```

Here's what every part of that bash command means.
* We're creating a Docker with Nvidia compatibility called `<yourDockerName>`
* `--rm` recursively copies all files from the official PyTorch repository
* `-v` attaches a folder on your home directory to the Docker, to access data and your personal code
* `-p` is forwarding the port `0.0.0.0:8888` from the docker to your local machine at `0.0.0.0:8888`
* `--ipc=host` can be ignored for now, it simply makes it easier for multithreading operations on PyTorch down the line

Upon running that bash script, you should now have a Docker created on the zeta.cis.jhu.edu server. To check that this is correct, run the following command that displays all current Docker images.

`$ nvidia-docker ps`

You should see your Docker listed as `<yourDockerName>`. Once we have the Docker created, we will connect to it, and then start it. Type the following command

`$ nvidia-docker exec -it <yourDockerName> bash`

You should now be in a directory that looks like `root@<number>:/workspace#`. This is the directory where you'll be working with your Python code, and executing. The easiest way to quickly set up Python and get working on code is through the Jupyter notebook model, which is what we will set up now on the Docker image. Type the following

`$ pip install jupyter`

This should install Jupyter and all its dependencies. To run the Jupyter image, just type

`$ jupyter notebook --ip=0.0.0.0 --allow-root`

This should start the Jupyter notebook with a statement in terminal that states "This Jupyter notebook is running at: `<webaddress>`. Copy that web-address for now, but pasting it to your web-browser won't work yet.

Now, the issue here is that we can't yet open our Jupyter notebook in a web-browser to view and edit it in a pretty GUI. In a new terminal window on your **local machine** at CIS, type the following code to allow the `8888` port to tunnel through from zeta.cis.jhu.edu

`$ ssh -L 8888:0.0.0.0:8888 -f -N username@zeta.cis.jhu.edu`

Once you have this done, you can now open a web-browser on your local machine and navigate to `<webaddress>`, and you should see your Jupyter notebook opened up. To test that PyTorch is installed and GPU functionality is working, type the following Python code and see if it returns `true` 

```python
import torch
torch.cuda.is_available()
```

And we're all set to work with PyTorch!

When you're done working, remember to `logout` from the docker and `logout` from zeta.cis.jhu.edu.

We can take the ssh tunnelling one step further, and work from our personal machines as well. That just involves a second tunnelling to your local machine. To make this work, open a terminal on your personal PC, and type the following.

`$ ssh -L 8888:0.0.0.0:8888 -f -N username@foo.cis.jhu.edu`

Then in your web browser, navigate to `<webaddress>`.

To reinitialize your docker, you should simply type the following commands again

`$ nvidia-docker exec -it <yourDockerName> bash`

`$ jupyter notebook --ip=0.0.0.0 --allow-root`

Sometimes, if you're logging in after a while, you may have to retunnel the `8888` port through zeta.cis.jhu.edu to your local machine (and once again through foo.cis.jhu.edu to your personal machine), by typing the bash script above.

### Additional information about Nvidia Docker

Here's a few other useful commands to work with Nvidia dockers
* `$ nvidia-docker images` lists all the images loaded on your server currently.
* `$ nvidia-docker ps` lists current images
* `$ nvidia-docker cp path/to/your/file <yourDockerName>:/destination` copies files from your local system to your Docker image. By default, the Docker initializes in the `/workspace/` directly, so make sure to copy files into that.
* `$ nvidia-smi` to monitor your GPU usage.

#### References

* https://medium.com/@flavienguillocheau/documenting-docker-with-gpu-deep-learning-for-noobs-2edd350ab2f7
* https://medium.com/@gooshan/for-those-who-had-trouble-in-past-months-of-getting-google-s-tensorflow-to-work-inside-a-docker-9ec7a4df945b
* https://github.com/JihongJu/Jockerfiles
