# ALCF precipitation detection

This repository contains code for detecting precipitation in lidar data using
an artificial neural network for use with the [Automatic Lidar and Ceilometer
Framework](https://alcf.peterkuma.net).

The code is currently in development.

## Requirements

The code should work on any standard Linux distribution. It has been developed
and tested on Devuan GNU/Linux 5 (daedalus). Running the code on other Windows
or macOS might be possible, but has not been tested. On Windows, it might be
possible to run it most easily under the Windows Subsystem for Linux.

The commands are to be run in the terminal, such as GNU Bash. Specifically,
some of the command-line syntax is not compatible with `zsh` (the default shell
on macOS). The use the commands on macOS (untested), it is recommended to start
the `bash` shell first.

The version numbers are advisory, and the code might work with earlier versions
as well.

- Python >= 3.11

Python packages:

- alcf >= 2.0.1
- aquarius_time >= 0.4.0
- ds_format >= 4.1.0
- matplotlib >= 3.7.2
- numpy >= 1.24.2
- pst-format >= 2.0.0
- tensorflow >= 2.13.0

To install the required packages on Debian-based Linux distributions:

```sh
apt install python3
```

To avoid compatibility issues, it is recommended to install the specific
versions of the required Python packages (listed in the file
`requirements.txt`) in a Python virtual environment:

```sh
python3 -m venv venv
. venv/bin/activate
pip3 install -r requirements.txt
```

This also activates the environment for the current session. After finishing
working with the code, the environment can be deactivated with `deactivate`.

## Commands

