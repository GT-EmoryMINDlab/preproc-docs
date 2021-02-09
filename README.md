# preproc-docs
Documentation and examples for preprocessing fMRI datasets.

# Table of Contents
* 1 - [Installing C-PAC](#section-1)
    * 1.1 [Verify Server OS/Distribution](#section-1-1)
    * 1.2 [Install Python 3 & Pip 3](#section-1-2)
    * 1.3 [Configure Virtual Environment](#section-1-3)
    * 1.4 [Install Docker](#section-1-4)
    * 1.5 [Install C-PAC](#section-1-5)
* 2 - [C-PAC Usage](#section-2)
    * 2.1 [Command Format](#section-2-1)
    * 2.2 [Examples](#section-2-2)
    * 2.3 [Troubleshooting](#section-2-3)
    
    
<a name="section-1"></a>
## Installing C-PAC

<a name="section-1-1"></a>
### 1. Verify Server OS/Distribution
First, verify the operating system and/or distribution of the server that you're trying to install C-PAC on. The Keilholz 
lab servers should be running Linux CentOS 7. If you want to check your Linux distribution, the following command should 
give you identifying output:

    $ cat /etc/*-release

*Note:* the below installation steps currently only support installation on Linux systems that use the *yum* package 
manager (such as CentOS).


<a name="section-1-2"></a>
### 2. Install Python 3 & Pip 3
Check if the system has Python 3 installed:

    $ which python3

If the command produces a non-error output with a valid path to a python executable, then Python 3 is already installed 
on the system. If it is not, it is recommended to install Python 3 via the officially-supported *yum* package. If installed 
this way, pip (the official python package manager) will be installed automatically as a bundle.

*Note:* this command requires root permissions to execute.

    $ sudo yum install python3

Validate the installation of Python 3 and Pip 3 with the following commands:

    $ python3 --version && which python3

    $ pip3 --version && which pip3

The output will give you the installed version and path for each package. If your system already had Python 3 installed, it may 
alias *python3* as *python* and/or *pip3* as *pip*.


<a name="section-1-3"></a>
### 3. Configure Virtual Environment
*Note:* never run anything related to python (including pip) as root. It will most likely break user permissions.

While not strictly necessary to run C-PAC, it is highly recommended to install and configure a virtual environment for 
the underlying python dependencies. Especially on a shared lab server, having a virtual environment for running preprocessing 
pipelines ensures that any python package changes will not unintentionally break other workflows. Virtual environments are 
easiest to configure via the wrapper package *virtualenvwrapper*, which abstracts out some of the configuration.
Install the wrapper package:

    $ pip3 install virtualenvwrapper --user

Verify installation:

    $ virtualenvwrapper

The above command should produce a list of available commands and descriptions of what they do. For the full documenation, 
please refer to: http://virtualenvwrapper.readthedocs.org/en/latest/command_ref.html

Create a new virtual environment for C-PAC installation:

    $ mkvirtualenv <ENV_NAME>

In the future, to use the virtual environment, use the following command:

    $ workon <ENV_NAME>

And to exit the virtual environment, use the following command:

    $ deactivate

If you're ever unsure if you're currently working in your desired virtual environment, you can check if your python executable 
is in your virtualenv directory (e.g., */usr/bin/python3* vs. */home/<USER_NAME>/.virtualenvs/<VENV_NAME>/bin/python3*):

    $ which python3
    
If you have trouble with this section, please see the [Troubleshooting](#section-2-3) section.


<a name="section-1-4"></a>
### 4. Install Docker
To see the full installation steps of Docker, please read the [official documentation](https://docs.docker.com/engine/install).
The Keilholz lab servers should already have Docker installed (if not, please see CentOS instructions [here](https://docs.docker.com/engine/install/centos)).
*Note:* you can also use [Singularity](https://sylabs.io/guides/3.7/user-guide/) as the container platform for C-PAC.

To be able to run C-PAC commands properly with Docker, it is necessary that the process is actively running on the host, 
and that your user has root permissions (or else is added to a docker group by an admin, as shown below).

Verify installation:

    $ docker --version

Confirm Docker is running:
    
    $ systemctl status docker

Start Docker if it is not active:

    $ systemctl start docker
    
Create a group for docker, and add your user to it (for full instructions, see [here](https://docs.docker.com/engine/install/linux-postinstall/)):

    $ sudo groupadd docker
    $ sudo usermod -aG docker <USER_NAME>


<a name="section-1-5"></a>
### 5. Install C-PAC
C-PAC is recommended to be run as a container, either in Docker or Singularity. While one of those two frameworks need to 
be installed on your system, it is recommended to run C-PAC as a python package, instead of directly creating the container 
from the command line. This simplifies the runtime parameters and configuration of the pipeline. We can install the python 
package by entering our new virtual environment and installing via pip:

    $ pip3 install cpac

This may take some time, as there are a large number of underlying package dependencies. When installation is complete, verify:

    $ cpac --version


<a name="section-2"></a>
## C-PAC Usage
<a name="section-2-1"></a>
### 1. Command Format
*Note:* for official documentation, please reference: https://fcp-indi.github.io/docs/latest/user/index#user-guide-index

Commands used to preprocess data vary depending on what you are trying to accomplish. In general, the command you will 
use to test configurations and run pipelines is **cpac run**. There are two configuration files that you should make note of:

    * Pipeline Config - controls all parameters related to pipeline execution
    * Data Config - short file that specifies relative location (.nii paths)

These are YAML files, and can be generated via the *test_config* parameter. To see example configurations, please see downloads here:
https://www.nitrc.org/projects/cpac. To dry-run C-PAC on a dataset and generate configuration files, the minimal command is:

    $ cpac run <DATA_DIR> <OUTPUT_DIR> test_config

This will execute the pre-validation steps and generate a template for the pipeline config and data config, which you can edit
yourself depending on your pipeline requirements and system architecture. The minimal command to actually run the pipeline 
for an individual participant is:

    $ cpac run <DATA_DIR> <OUTPUT_DIR> participant

To specify the pipeline config, use the *--pipeline_file* flag, and to specify the data config, use the *--data_config_file* flag:

    $ cpac run <DATA_DIR> <OUTPUT_DIR> participant --data_config_file <DATA_CONFIG> --pipeline_file <PIPELINE_CONFIG>

These arguments are positional, so ensure you specify them in this order. To abbreivate commands, it is possible to set 
custom bindings on path variables (e.g., */home/user/cpac/configs* -> *configs*) via the **cpac** command. Run **cpac --help** 
or **cpac run --help** to read the full manual pages for configuration options (the latter will run the C-PAC container).

*Note:* **cpac** and **cpac run** are two different commands, with different configuration options.

<a name="section-2-2"></a>
### Examples
The following examples have all directories under a *'cpac'* directory, with *'configs'* for configuration files, *'data'* for the 
input unprocessed fMRI scans, and *'output'* for the location of logs and preprocessed output data. Remember that if you don't 
reference the absolute paths of these directories and don't set custom bindings for the paths, you will need to execute 
the **cpac run** command at the level where the directory *'cpac'* exists.

    # Minimal test_config command:
    $ cpac run cpac/data/s1 cpac/output test_config
    
    # Command to generate a pipeline config using a data config:
    $ cpac run cpac/data/s1 cpac/output test_config --data_config_file cpac/config/data_config.yml
    
    # Minimal participant command:
    $ cpac run cpac/data/s1 cpac/output participant
    
    # Command to preprocess a single subject (s1) with data and pipeline configs
    $ cpac run cpac/data/s1 cpac/output participant --data_config_file cpac/config/data_config.yml --pipeline_file cpac/config/pipeline_config.yml
    
    # Fully configured group command:
    $ cpac run cpac/data/s1 cpac/output group --group_file cpac/config/group_config.yml --data_config_file cpac/config/data_config.yml --pipeline_file cpac/config/pipeline_config.yml

If you'd like to test out any of the above commands on sample data, there is a truncated fMRI sample in the *'cpac_sample_data'* 
folder of *'preproc-docs'*. So using only the files in this repository, you could run the command for a single subject from 
the root of this repository directory like so:

    $ cpac run cpac_sample_data output participant --data_config_file cpac_configs/ex_data_config.yml --pipeline_file cpac_configs/ex_pipeline_config.yml

Note that in this case, you would still need to change the paths in cpac_configs/ex_data_config.yml to the actual location of this 
repository on your computer.

<a name="section-2-3"></a>
### Troubleshooting
Below is a list of the C-PAC errors I've encountered so far, and their causes:

* *Could not create the working directory: /working*
    * Change the 'workingDirectory' variable in pipeline config (ln 55 in the example config).
    
* *OSError: Could not create directory /working/<SUBJECT_NAME>_ses-1*
    * C-PAC can't write to the working directory, try using pre-existing directory such as '/tmp'.
    
* *OSError: Didn't find any files in <SUBJECT_DIR>. Please verify that the path is typed correctly, that you have read access to the directory, and that it is not empty.*
    * The format of the input data is not BIDS compliant, so C-PAC can't find the data files. Either reformat the input 
    data, or use a data config that explicitly states where the anatomical and functional data exists.

* *PermissionError: [Errno 13] Permission denied: '<OUTPUT_DIR>/cpac_pipeline_config_<TIME_STAMP>.yml'*
    * C-PAC has insufficient permission to write output to your specified output directory. If you are specifying input or 
    output on an external drive, try pointing the output directory to somewhere in the user's home directory.

* *FileNotFoundError: File <SUBJECT_DIR>/<NII_FILE> does not exist!*
    * The data config is incorrect or not specified, check 'anat' and 'scan' variables are accurate.
    
If you're having trouble creating virtual environments for python, the following points may be helpful:
    
* Avoid running anything in python as root ('sudo') if possible. If you see a permission error while installing, it could 
be caused by installing something related to python as root. Try uninstalling and reinstalling again.
* The '--user' flag from Section 1.3 is used to install virtulenvwrapper for a singular user. You may otherwise see permission errors.

If the package was installed successfully but you receive an error when attempting to create a virtual environment with 
'mkvirtualenv', your shell may not know the location of the virtualenvwrapper script. In your terminal, you can try to find 
the script and execute it:

    # Find the location of the script
    $ which virtualenvwrapper.sh
    
    # Execute the script, location depends on the path returned above
    $ source /usr/local/bin/virtualenvwrapper.sh

If "which virtualenvwrapper.sh" returns a "virtualenvwrapper.sh not found" message, you may need to configure your PATH 
variable properly, as it probably can't find the parent directory of the virtualenvwrapper installation. You can try something 
like the below command if you know where the virtualenvwrapper.sh is located:

    # Add a new path to your PATH variable, where <USER_NAME> is your username
    
    # Probable MacOS location
    $ export PATH="/home/<USER_NAME>/.local/bin:$PATH"
    
    # Probable Linux location
    $ export PATH="/usr/bin:$PATH"

If you can create virtual environments after running the above commands, you may want to add the script location to your 
shell configuration file. If nothing above solved your issue, two additional environment variables can be specified in your 
shell configuration that may resolve the issue (for bash this is ~/.bashrc, other shells may use a different file).

    export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
    export WORKON_HOME=$HOME/.virtualenvs
    source /usr/local/bin/virtualenvwrapper.sh
