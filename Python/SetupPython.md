# Python

## Installation

Following this: https://stackoverflow.com/questions/41427500/creating-a-virtualenv-with-preinstalled-packages-as-in-requirements-txt
and this: https://packaging.python.org/en/latest/tutorials/installing-packages/

  - Install python (https://www.python.org/downloads/)
    - Set `py` allias: `Set-Alias 'py' 'C:\Local\Python3_12\python.exe'`
	  + This can be done in powershell profile at `$HOME\Documents\PowerShell\Microsoft.PowerShell_profile.ps1`
	  + More details can be found at: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7.4
    - For new instalation, ensure pip, setuptools, and wheel are up to date: `py -m pip install --upgrade pip setuptools wheel`
  - Install virtual environment: `py -m pip install virtualenv`
  - Create virtual env.: `py -m virtualenv venv`
  - Activate virtual env.: `venv/Scripts/activate.ps1`
    + on Linux/WSL: `source venv/Scripts/activate`
  
### Setup python environment

The quickest way is when you have a `requirements.txt` file:
  - To install the requirements in the current virutal env: `pip install -r requirements.txt`
    - The **requirements.txt** file is created by `python -m pip freeze > requirements.txt`
    - Notebook then can be started with: `jupyter notebook`
  
#### Jupyter notebook setup from scratch

This assume the virtual environment has been created and actived
  - Install Jupyter notebook `pip install notebook`
  - To add current active environment to jupyter as "**DN4**" (see: https://anbasile.github.io/posts/2017-06-25-jupyter-venv/)
    ```shell
    # Should already installed
    # pip install ipykernel
	
    ipython kernel install --user --name=DN4
	```
    + to remove an installed kernel: 
	```
	# get the list of kernel
	jupyter kernelspec list
	
	# removed the <unwanted-kernel>
	jupyter kernelspec uninstall <unwanted-kernel>
	```
  - To generate the configuration file 
    ```shell
    # The configuration file will be "C:\Users\<UserName>\.jupyter\jupyter_server_config.py"
    jupyter server --generate-config
    # If received PowerShell response Jupyter command 'jupyter-server' not found
    # can try to install Anaconda Powershell and execute the jupyter server command again, which usually will work.
	```
  - To make jupyter start at the git checkout
    + Edit the "jupyter_server_config" file to set c.ServerApp.root_dir as sample below
	  ```text
      c.ServerApp.root_dir = 'c:/git/wtg'
	  ```
	+ Start the jupyter notebook from command line (not windows link): `jupyter notebook`