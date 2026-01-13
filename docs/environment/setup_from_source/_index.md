---
title: "Setup from Source"
weight: 10
bookCollapseSection: false
---

## Setup

#### questions
??? What is min/max Python version required ???

??? What is makehuman2_session.conf ???

#### issues
Setting the MH_HOME_LOCATION environment variable does not work as expected. 

If you set it to point to a directory that doesn't exist yet, it will ignore the setting and default to ```~/makehuman2/``` and then create that directory and copy files into it. Might be better to exit with an error that the MH_HOME_DIRECTORY does not exist.

```
$ cat ~/Library/Application\ Support/MakeHuman/makehuman2/makehuman2.conf 
{
    "apihost": "127.0.0.1",
    "apiport": 12345,
    "basename": null,
    "noSampleBuffers": false,
    "path_error": "/Users/jjones/makehuman2/log",
    "path_home": "/Users/jjones/makehuman2",
    "redirect_messages": false,
    "remember_session": false,
    "theme": "makehuman.qss",
    "units": "metric"
}
```

If you set it to point to a directory that does exist, you get this configuration file as a result:

Here it accepted the setting from the environment variable but notice the path_home entry is missing from the config file...

```
$ cat ~/Library/Application\ Support/MakeHuman/makehuman2/makehuman2.conf 
{
    "apihost": "127.0.0.1",
    "apiport": 12345,
    "basename": null,
    "noSampleBuffers": false,
    "path_error": "/Users/jjones/src/makehuman2/log",
    "redirect_messages": false,
    "remember_session": false,
    "theme": "makehuman.qss",
    "units": "metric"
}
```


## Setup

Before running the app, create the Configuration Folder

### Configuration Folder

#### create Configuration folder

If it doesn't exist yet, create the configuration folder: 

##### MacOs
```
mkdir -p ~/Library/Application\ Support/MakeHuman/makehuman2
```

#### Configuration file

The configuration file lives in the Configuration Folder and provides basic configuration details.

Here you can establish the preferred User Data Path (path_home) as well as keyboard shortcuts and other settings
```
{
    "apihost": "127.0.0.1",
    "apiport": 12345,
    "basename": null,
    "noSampleBuffers": false,
    "path_error": "/Users/foo/makehuman2/log",
    "path_home": "/Users/foo/makehuman2",
    "redirect_messages": false,
    "remember_session": false,
    "theme": "makehuman.qss",
    "units": "metric"
}
```

##### MacOs

```
/Users/foo/Library/Application\ Support/MakeHuman/makehuman2/makehuman2.conf
```

##### Default values

If you start up the app without a configuration file present, a minimal default configuration file is created. Here, on MacOs, it assumes your User Data folder is in the user's home directory (~/makehuman2) and will create that folder if it doesn't exist.

```
{
    "apihost": "127.0.0.1",
    "apiport": 12345,
    "basename": null,
    "noSampleBuffers": false,
    "path_error": "/Users/foo/makehuman2/log",
    "path_home": "/Users/foo/makehuman2",
    "redirect_messages": false,
    "remember_session": false,
    "theme": "makehuman.qss",
    "units": "metric"
}
```

### System Folder

The default System Folder (aka path_sys) will be the place from where the app is launched, e.g. your local source code repo root, e.g.

```/Users/foo/src/makehuman2```

### System Data Folder

The System Data folder (aka path_sysdata) holds system resources.

This is the data folder under System Folder, e.g.

```/Users/foo/src/makehuman2/data```


### User Data Folder

The User Data folder (aka path_home) holds user resources.

If the User Data Folder does not contain the necessary file, then the file will be retrieved from the System Data folder if it exists.

If you are running the app from source, set the User Data Folder (path_home) in your configuration file to point to your local user data folder, e.g.

#### MacOs example:
```
"path_home": "/Users/foo/makehuman2_data",
```

It is also possible to configure the User Data Folder via the ```MH_HOME_LOCATION``` environment variable. ***Todo: what happens if you use that instead of the config file path?***

Note: internally path_userdata points to (path_home/data)

#### Initialization

The first time the app is run, it will create subfolders in the user data folder:

- clothes
- dbcache
- exports
- eyebrows
- eyes
- hair
- poses
- rigs
- skins
- teeth
- tongue
- contarget
- downloads
- expressions
- eyelashes
- grab
- models
- proxy
- shaders
- target
- themes

### Logging

If redirect_messages is enabled in the config file, then logs should appear in 

```<UserDataHome>/log```

***todo: do they?***

### setup virtualenv

```
$ virtualenv venv
```

```
created virtual environment CPython3.13.9.final.0-64 in 185ms
  creator CPython3Posix(dest=/Users/jjones/src/test/makehuman2/venv, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, via=copy, app_data_dir=/Users/jjones/Library/Application Support/virtualenv)
    added seed packages: pip==25.3
  activators BashActivator,CShellActivator,FishActivator,NushellActivator,PowerShellActivator,PythonActivator
```

```
source venv/bin/activate
```

### install requirements

```
pip install -r requirements.txt
```

###
run the program

``` python makehuman.py ```


You should see the app launch

![initial launch](initial_launch_first_time.png)

The first time you launch the app, you must select a base mesh from the selected options in order to enable all the features.












