If you're having trouble with any of the setup steps (especially the Docker stuff), hopefully this doc will explain it.

**Docker container:** 

Docker containers basically act as a virtual machine with a bunch of pre-installed software (eg, the conda environment and associated Python packages needed for the homework). It's really lightweight and lets you "emulate" an Ubuntu (Linux) machine one your personal computer, no matter what the OS of your machine is. This is nice cuz Windows and MacOS might not like some of the Python packages the homework needs to work, so by emulating Ubuntu, we can install all of that stuff for you and then you can use those packages despite not actually being on Linux.

**Interactive terminal:** 

By running `docker run -it`, it starts up the container you downloaded and enters the container via an interactive terminal (which is what the `-it` does). To indicate this, the terminal command prompt should say `root@some string of letters and numbers` rather than your `base machine's username@base machine's name`. 

**Conda envs:** 

Additionally, the prompt should say `(cs182_hw1)` next to the `root@whatever`. This is to indicate that you've activated the preinstalled conda environment wherein we've installed all the Python packages you need. Any Python stuff you run while this environment is activated should have access to all the packages in the environment: you can try this by typing `python` and then `import numpy`. This should work even if you do not have numpy installed on your host machine. We have set it so that, upon starting up the Docker interactive terminal, it should automatically activate this environment.

**Linux commands:** 

Not only does emulating Ubuntu help you use the Python packages needed for the homework, but it also lets you run Linux commands. For example, `get_dataset.sh` is a bash script, which contains a bunch of Linux commands that Windows cannot run (like `wget`, which downloads stuff). However, by running that command within the interactive terminal, since the Docker is emulating Ubuntu, it will be able to run all the commands. To run a bash script (from Docker interactive terminal), just go to its directory and type `./(name of script).sh`.

If you're unfamiliar with Linux commands, [this](https://www.geeksforgeeks.org/basic-linux-commands/) is a good starter. Realistically, all you will need are: `cd` to change directories, `ls` to list the contents of a directory, and `pwd` to print out which directory you're in. Additionally, if you get a permission error, `chmod u+r+x (name of script).sh` is useful -- it basically just enables permissions to actually run the script.

**Jupyter server and port-forwarding:** 

Jupyter notebooks are nice cuz they allow you to run your Python code interactively and in separate cells (to test different parts without having to run the whole thing). However, to actually use it, you need to start up a Jupyter server (which is what `jupyter notebook whatever` does). This server will be up as long as the command is running (and, while its running, you won't be able to run other commands -- use ctrl or cmd Z to exit the server). By running the server within the container, all your notebooks have access to the conda `cs182_hw1` env and all its packages too! 

If you get errors saying the imports aren't available or you can see your base machine's files in the Jupyter browser file explorer (outside of the stuff mounted to `/contained`, discussed below), you probably accidentally ran the server on your base machine rather than inside the Docker -- just close the server, run the `docker run -it` command again, then run the Jupyter server command again.

The Jupyter server is located at a specific IP and port. That's what the `--ip=0.0.0.0 --port=8888` part of the command specifies. However, since its run inside the Docker container, that port is the Docker container's port. We fix this by just doing port forwarding: the `-p 8888:8888` part of the Docker run command just connects your base machine's port 8888 to that port within the Docker as well, so you will be able to access the Jupyter server on a browser on your base machine by going to the url `localhost:8888`.

**Mounting:** 

Docker is emulating Ubuntu as a self-contained container. Since this is basically its own OS separate from whatever your machine is running, it also has its own file system separate from your own machine's. This is a problem because 1) this file system is reset every time you close the container and 2) that filesystem cannot see any of your base machine's files or vice-versa (which is necessary to actually get e.g., the log files out from your homework code, which you'll need to submit to gradescope).

To solve this issue, we _mount_ the homework repo one your base machine file system to a directory inside the Docker container's file system. That's what `-v /PATH/TO/HW/REPO:/contained` does: it makes it so that `/PATH/TO/HW/REPO` on your base machine's file system is accessible within the Docker container's file system. All you have to do is go to `/contained` in the Docker to see all the homework files. All the other files you see (outside `/contained`) are part of the Docker container's file system. Generally you won't need to touch those.

Additionally, this directory is persistent: even if you close the Docker, none of the changes you've made to that directory will go away. All changes made inside that mounted directory will be saved to the corresponding directory in your base machine's file system and vice-versa. So, after running all the completed Python code for the homework and generating all the submission logs, you can close Docker and go the hw repo on your base machine and you'll be able to see all the logs still, which you can then submit :)

**Future homeworks**: 

For future homeworks, we will probably just release new Docker containers with the proper conda env stuff installed. Using them will be similar to using this one, just download and docker run the new image instead. Hopefully, if you get used to the setup for this homework, you will be able to use future ones with relative ease!
