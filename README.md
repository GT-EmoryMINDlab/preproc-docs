# preproc-docs
Documentation and examples for preprocessing fMRI datasets.

# C-PAC Installation & Usage

## Installing C-PAC

### 1. Verify Server OS/Distribution
First, verify the operating system and/or distribution of the server that you're trying to install C-PAC on. The Keilholz 
lab servers should be running Linux CentOS 7. If you want to check your Linux distribution, the following command should 
give you identifying output:

    $ cat /etc/*-release

*Note:* the below installation steps currently only support installation on Linux systems that use the *yum* package 
manager (such as CentOS).


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


### 3. Configure Virtual Environment
*Note:* never run anything related to python (including pip) as root. It will most likely break user permissions.

While not strictly necessary to run C-PAC, it is highly recommended to install and configure a virtual environment for 
the underlying python dependencies. Especially on a shared lab server, having a virtual environment for running preprocessing 
pipelines ensures that any python package changes will not unintentionally break other workflows. Virtual environments are 
easiest to configure via the wrapper package *virtualenvwrapper*, which abstracts out some of the configuration.
Install the wrapper package:

    $ pip3 install virtualenvwrapper

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


### 4. Install C-PAC
C-PAC is recommended to be run as a container, either in Docker or Singularity. While one of those two frameworks need to 
be installed on your system, it is recommended to run C-PAC as a python package, instead of directly creating the container 
from the command line. This simplifies the runtime parameters and configuration of the pipeline. We can install the python 
package by entering our new virtual environment and installing via pip:

    $ pip3 install cpac

This may take some time, as there are a large number of underlying package dependencies. When installation is complete, verify:

    $ cpac --version

## C-PAC Usage *--Please upload config scripts and sample data.*
*Note:* for official documentation, please reference: https://fcp-indi.github.io/docs/latest/user/index#user-guide-index

Commands used to preprocess data vary depending on what you are trying to accomplish. In general, the command you will 
use to test configurations and run pipelines is **cpac run**. There are two configuration files that you should make note of:

    * Pipeline Config - controls all parameters related to pipeline execution
    * Data Config - short file that specifies relative location (.nii paths)

These are YAML files, and can be generated via **test_config**. To see example configurations, please see downloads here:
https://www.nitrc.org/projects/cpac. To dry-run C-PAC on a dataset and generate configuration files, the minimal command is:

    $ cpac run <DATA_DIR> <OUTPUT_DIR> test_config   

This will execute the pre-validation steps and generate a template for the pipeline config and data config, which you can edit
yourself depending on your pipeline requirements and system architecture. The minimal command to actually run the pipeline 
for an individual participant is:

    $ cpac run <DATA_DIR> <OUTPUT_DIR> participant

To specify pipeline config, use the *--pipeline_file* flag, and to specify the data config, use the *--data_config_file* flag:

    $ cpac run <DATA_DIR> <OUTPUT_DIR> participant --data_config_file <DATA_CONFIG> --pipeline_file <PIPELINE_CONFIG>

These arguments are positional, so ensure you specify them in this order. To abbreivate commands, it is possible to set 
custom bindings on path variables (e.g., */home/user/cpac/configs* -> *configs*) via the **cpac** command. For full command 
configurable options, run **cpac --help** or **cpac run --help** for the full manual pages (the latter will run the C-PAC container).

*Note:* **cpac** and **cpac run** are two different commands, with different configuration options.

### Example: *--Please put the specific filenames and dir.*
 $ cpac run <DATA_DIR> <OUTPUT_DIR> test_config  
 $ cpac run <DATA_DIR> <OUTPUT_DIR> participant
 $ cpac run <DATA_DIR> <OUTPUT_DIR> participant --data_config_file <DATA_CONFIG> --pipeline_file <PIPELINE_CONFIG>

### Troubleshooting
TODO: Compile a table of all errors encountered and their apparent root causes.


