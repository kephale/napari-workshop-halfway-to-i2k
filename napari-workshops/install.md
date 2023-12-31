# Installing and Opening napari

This module covers the following:

* [Installing napari as a Bundled App](#napari-bundled-app-installation)
* [Installing napari as a Python Package](#napari-python-package-installation)
  * [Install an environment manager](#install-an-environment-manager)
  * [Create an environment](#create-an-environment)
  * [Enter or activate the environment](#enter-or-activate-the-environment)
  * [Install napari](#install-napari)
  * [Open and close napari](#open-and-close-napari)
 * [Install the Workshop Browser Plugin](#install-the-workshop-browser-plugin)
 

For this workshop, we recommend the bundle app installation for anyone who is unfamiliar with using terminal. Regardless of which installation method you use, we recommend using the [napari workshop browser plugin](https://www.napari-hub.org/plugins/napari-workshop-browser) to complete the rest of the modules.

## napari Bundled App Installation
The simplest way to install napari is as a bundled app. You can download the installer by visiting the [napari releases page](https://github.com/napari/napari/releases) and downloading the file that corresponds to your operating system and processor from the assets section at the bottom of the release notes.
![bundled app](resources/bundle.png)

- For Windows: `napari-0.4.18-Windows-x86_64.exe`
- For Mac with Intel processor: `napari-0.4.18-macOS-x86_64.pkg`
- For Mac with M1 ("Apple silicon"): `napari-0.4.18-macOS-arm64.pkg`

Open the file and follow the instructions in the installer. Once installation is complete, you can open napari by simply double-clicking the application.
**Note:** napari may take a few minutes to open.

Before continuing, follow the instructions to [install the workshop browser plugin](#install-the-workshop-browser-plugin).

## napari Python Package Installation
Although napari has a bundled app, the most stable installation is to install napari as a python package. This installation method requires some work with terminal or command prompt. Follow the steps below, copying and pasting the commands into terminal/command prompt when noted.

### Install an Environment Manager

![miniconda versions](resources/miniconda-versions.png)  

#### Mac users: 
- Find the processor version: **Apple** > **About this Mac**.  
- Download the [miniconda3](https://docs.conda.io/en/latest/miniconda.html) package file (Intel or M1 depending on your computer).   
**Note:** Please use the package file which is outlined in red in the above graphic and not the bash file.  
- Open the downloaded file and follow the instructions to install.
- Search for terminal and open the terminal.   

#### Windows users:
- Find the processor bit version:  
**Start button** > **Settings** > **System** > **About** shows the processor bit version.
- Download the [miniconda3](https://docs.conda.io/en/latest/miniconda.html) file (64-bit or 32-bit depending on your computer). 
- Open the downloaded file and follow the  instructions to install.
- Click the **Start** button and search for Anaconda.
- Open the **Anaconda Prompt**.

### Create an Environment 
The environment can be named “napari-env” (or any other name you’d like). To create an environment, in terminal (Mac) or Anaconda Prompt (Windows), type: 

```bash
conda create -y -n napari-env -c conda-forge python=3.9
```

### Enter or Activate the Environment 

Enter these commands on the terminal (Mac) or at the Anaconda Prompt (Windows): 

```bash
conda activate napari-env
```

**Note:** The environment is shown on the left end of the command line. In the example below, the environment is called `cytodata`:  

![environment](resources/environment-prompt.png)  

### Install napari 
Type one of the following commands on the terminal (Mac) or at the Anaconda Prompt (Windows) and respond to the prompts:  

* Recommended command: 

  ```bash
  conda install -c conda-forge napari=0.4.18
  ```

   **Note:** 0.4.18 can be replaced with any desired version of napari.

* Alternative command if the above fails:
    ```bash
  pip install “napari[all]”
    ```

### Open and Close napari  
Open napari from the terminal (Mac) or the Anaconda prompt (Windows) by typing: 

```bash
napari
```

**Note:** napari may take some time to open the first time.

OPTIONAL - Try closing and reopening napari.
* To close napari:
    - In the napari top-bar or main menu:   
    **File** > **Close Window**
    - Leave/deactivate the environment in the terminal or Anaconda Prompt by typing:  
    ```conda deactivate ```
* To enter the environment and open napari  
    - At the terminal or Anaconda Prompt type:  
    ```conda activate napari-env```  
    ```napari```

## Install the Workshop Browser Plugin

To follow along with this workshop, we recommend you use the Workshop Browser plugin.
1. Open the napari console using the bottom left, `>_` button.
2. Paste `pip install git+https://github.com/kephale/napari-workshop-browser.git` and hit enter.
3. Restart napari to complete installation.
4. Open the plugin `Plugins > Open a napari workshop (napari-workshop-browswer)`
5. Erase the default URL and enter the URL of this workshop's zip file: `https://github.com/kephale/napari-workshop-halfway-to-i2k/archive/refs/heads/main.zip`
6. Click `Launch workshop`.
7. A local instance of juptyer notebooks should launch with all of the notebooks needed to complete the rest of the modules.

The next topic in this workshop is [visualizing data in napari](visualization.md).  It should take approximately 15 minutes to complete. 
