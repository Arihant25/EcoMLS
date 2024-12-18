# My Report on Reproducing the EcoMLS Experiment

I began by revisiting the research paper for the EcoMLS experiment and locating the GitHub repository mentioned within it. I first cloned the repository to my WSL environment but quickly remembered that Anaconda was installed on my Windows system. I switched to Windows, cloned the repository again, and began installing the specified Python modules. As the repository did not include a `requirements.txt` file, I manually searched for the correct method to install each required module using Anaconda.

Upon running `app.py`, the program started, but I encountered a `list index out of range` error related to Locust. I then tried running `process.py`, which indicated that PyTorch was required—something that wasn't documented in the repository. After installing PyTorch, I encountered a new issue: an error from PyRAPL:

```
File "C:\Users\Arihant\miniconda3\envs\ecomls\Lib\site-packages\pyRAPL\device_api.py", line 31, in cpu_ids
    api_file = open('/sys/devices/system/cpu/present', 'r')
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
FileNotFoundError: [Errno 2] No such file or directory: '/sys/devices/system/cpu/present'
```

At this point, I deduced that PyRAPL was not compatible with Windows, as it relies on Linux-specific system files. I attempted to bypass this by commenting out the PyRAPL code but was then presented with a continuous "File not exist 0" output. This led me to suspect that the absence of both an `images` and `IMAGES` folder could be contributing to the issue. However, due to Windows' case-insensitivity, I couldn’t create both folders in the same directory.

I repeated the setup process in WSL, but when I tried to initialize PyRAPL, it failed to find the driver. Despite my attempts to search for a solution online, I couldn’t find any relevant information. Based on my own analysis, I concluded that PyRAPL doesn’t work on WSL.

Given that I didn’t have access to a native Linux machine, I resorted to setting up a virtual machine (VM) using VMware Workstation Pro. To obtain the software, I had to create an account on Broadcom’s website and provide personal information. After downloading Ubuntu, I installed it on the VM and set up the environment, which included installing Git, Python, and the necessary modules.

Even after ensuring the presence of both the `IMAGES` and `images` folders with sample images, I continued to encounter the `list index out of range` error. I tried opening the project in VS Code, but the IDE froze due to the large number of files. To address this, I created a `.gitignore` file using the terminal, but the VM continued to struggle with performance.

Upon restarting the VM, I identified that the error occurred in `app.py` due to `self.n` being out of range for the `imageData` list. I added 99 images to the `IMAGES` folder, and the error appeared after a longer interval of time.

When I ran `process.py`, it successfully downloaded the YOLO models but failed at the PyRAPL code due to its absence on the system. Although I couldn’t find anything online, I suspected that PyRAPL may not be supported in virtual machines.

After further troubleshooting, I decided to flash Ubuntu onto a USB drive and boot it from there. I ran the setup process once more, and this time everything worked as expected.

For the experiment, I searched for a suitable dataset. Since I didn’t have access to the 25,000 random images used in the original paper, I opted for the GHIM 10K dataset. However, I encountered storage issues as my 8GB USB drive lacked the space to extract the dataset, so I discarded it.

I borrowed a friend's laptop to continue the experiment. Using 55 high-quality HDR images, I modified `process.py` to add a new scheduling policy attribute with two options: Random and Round Robin. I then ran `app.py` to simulate requests for FIFA 98 World Cup data using Locust. Afterward, I executed `Monitor.py` and `process.py` to log the data in `logs.csv`. Finally, I plotted the data using Jupyter Notebook.

I documented my findings and changes in the README file and submitted a pull request to the repository: [EcoMLS Pull Request](https://github.com/sa4s-serc/EcoMLS/pull/1).

Additionally, I noticed that the 11970th image was missing from the CSV logs, and the PyTorch model was imported in the code but never actually used.