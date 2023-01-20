# pyenv
You may need to have multiple versions of python running on your system due to different requirements from different software.

To make this happen, watch this 10min youtube video [How to Install and Run Multiple Python Versions on macOS | pyenv & virtualenv Setup Tutorial](https://youtu.be/31WU0Dhw4sk) and you will know how to install pyenv on macOs and how to configure it (globally, or locally for a project). 

Quick guide after installation:

```
pyenv versions #list python versions you installed so far with pyenv
pyenv install --list | grep "3\.[89]" #list available versions to install w filter
pyenv install -v 3.9.9 #install a version
pyenv global 3.7.9 #make an installed version global
pyenv local 3.9.9 #cd to a project & make a specific version local for that project
which python #double check 'which' locates correctly the python executable from pyenv
python -m venv .venv #in project dir, creates virtualenv in .venv dir
source .venv/bin/activate #activate virtualenv
```