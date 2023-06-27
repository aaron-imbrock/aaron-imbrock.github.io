---
title: "Automatically reloading modules in IPython3"
date: 2023-06-27T09:03:20-08:00
draft: false
---
## How to automatically reload modules in IPython

IPython is a powerful tool with two primary uses. It can be used as an integrated development environment for Python, similar but more feature-rich than IDLE, or it can be used for Jupyter notebooks. We're not going to cover notebooks as I rarely use them but this post would still apply.

I use ipython for rapidly iterating on ideas and new coding projects. A common workflow involves a python editor open on one side of the screen and ipython in a terminal open on the other side.
Until recently, after making a file change I'd close and reopen ipython as module imports are cached.

Since you can't just reimport a module and expect to get a fresh version what do we do?  Thankfully ipython has a built-in way to handle this use-case and it works great.

## Installation

Just to be thorough, let's first cover the installation of ipython. Moving forward when I refer to `ipython` you should type `ipython3`.
Debian Stable ships with an older version of ipython. Though I didn't test it I expect pip might result in a newer version. Brew would be another option.

#### Debian / Ubuntu

```shell
sudo apt install ipython3
```

#### Pip

```shell
pip3 install ipython
```

Check the docs for the [full install notes](https://ipython.org/install.html).

## Autoreload

Let's begin, shall we?

### Create a profile

First, create the config for the default profile:

```shell
$ ipython3 profile create
[ProfileCreate] Generating default config file: '/home/aimbrock/.ipython/profile_default/ipython_config.py'
```

The ipython folder lives at `~/.ipython` and our default profile config file is `~/.ipython/profile_default/ipython_config.py`. Interestingly, while the config file is over 1000 lines everything is commented out.

```shell
.ipython/
    ├── extensions
    ├── nbextensions
    └── profile_default
        ├── db
        ├── history.sqlite
        ├── ipython_config.py
        ├── log
        ├── pid
        ├── profile_default.ipy
        ├── security
        └── startup
            └── README
```

### Edit the profile

Secondly, now open the config file and make the necessary changes:

```shell
$ vim ~/.ipython/profile_default/ipython_config.py 
```

Search for `c.InteractiveShellApp.exec_lines` and in this example we need to change lines 35 and 39:

```shell
  33 ## lines of code to run at IPython startup.
  34 #  Default: []
  35 # c.InteractiveShellApp.exec_lines = []
  36 
  37 ## A list of dotted module names of IPython extensions to load.
  38 #  Default: []
  39 # c.InteractiveShellApp.extensions = []
  40 
```

Like so:

```shell
  33 ## lines of code to run at IPython startup.
  34 #  Default: []
  35 c.InteractiveShellApp.exec_lines = ['%load_ext autoreload', '%autoreload 2']
  36 
  37 ## A list of dotted module names of IPython extensions to load.
  38 #  Default: []
  39 c.InteractiveShellApp.extensions = ['autoreload']
  40 
```

Finally open `ipython3` to check for typos. Notice the autoreload comment.

```shell
$ ipython3
Python 3.9.2 (default, Feb 28 2021, 17:03:44) 
Type 'copyright', 'credits' or 'license' for more information
IPython 7.20.0 -- An enhanced Interactive Python. Type '?' for help.
The autoreload extension is already loaded. To reload it, use:
  %reload_ext autoreload

In [1]: 
```

### Example

Great. So we've made our changes so now let's use it!

Here we have a file `point.py` with a class called `Point` that has a `__repr__`.
We're going to change the text of the return statement and show how ipython instantly picks up the changes.

```shell
$ ls
point.py  test_point.py
$ cat point.py 
class Point:
    """Creates a point class with x, y, and z properties"""
...
    def __repr__(self):
        """Return human-readable representation of class."""
        class_name = type(self).__name__
        return f"{class_name}(x={self.x}, y={self.y}, z={self.z})"
...
```

In the same directory as the file, open ipython. Again, the file is `point.py` and the class is `Point`.

I made a small change in the file so the output of ```__repr__``` is different, and saved the file in my editor.

```shell
$ ipython3
Python 3.9.2 (default, Feb 28 2021, 17:03:44) 
Type 'copyright', 'credits' or 'license' for more information
IPython 7.20.0 -- An enhanced Interactive Python. Type '?' for help.
The autoreload extension is already loaded. To reload it, use:
  %reload_ext autoreload

In [1]: from point import Point

In [2]: p1 = Point(1,2,3)

In [3]: p1
Out[3]: Point(x=1, y=2, z=3)

In [4]: p1
Out[4]: I am Point(x=1, y=2, z=3)
```

To get ipython to update the instance variable `p1` ***all we had to do is save the file***.

### A Few Things to Remember

* I've noticed that Ipython will only update after a file save, and that makes sense. Occasionally a class needs to be reinstantiated but usually not.
* Even with VSCode's autosave enabled a manual Ctrl-S is necessary to see an immediate update.
* Debian Stable ships with an outdated version of ipython. I didn't test it but pip install might result in a newer version. Brew would be another option.

### Links

1. [Introduction to IPython configuration](https://ipython.org/ipython-doc/3/config/intro.html)
2. [Ipython Autoreload](https://ipython.org/ipython-doc/3/config/extensions/autoreload.html)
