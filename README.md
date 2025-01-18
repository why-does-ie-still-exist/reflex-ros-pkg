# ARCLab Righthand Code

This code runs the slightly broken RightHand that we have at Arclab. It disables the code that runs the preshape joints since for some reason they won't enumerate. To run the code, go to the `docker` folder

```
cd docker
docker build .
```

Now, run the image that was just created. You can see the available images with `docker image ls`

```
docker run -it --device=/dev/ttyUSB0 [whatever image was just generated]
```

Once the shell opens, you can run the main nodes for running the hand with:

```
roslaunch reflex reflex_one.launch
```

Finally, open another shell to the container. You can see running containers with `docker container ls`

```
docker exec -it [whatever container id was started] /bin/bash
```

Now you can run whatever code you want to run the hand. You can start with the demo routine, although it depends on the preshape joints so it crashes after calibration.

```
source /root/catkin_ws/devel/setup.bash
rosrun reflex one_sample_code.py
```