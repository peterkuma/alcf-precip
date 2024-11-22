# ALCF precipitation detection

**This repository is currently in preparation.**

This repository contains code for detecting precipitation in lidar data using
an artificial neural network for use with the [Automatic Lidar and Ceilometer
Framework](https://alcf.peterkuma.net).

The end product of running the code is and ALCF include filter, which can be
used with the "alcf stats" and "alcf auto lidar" commands to filter out
precipitation when calculating statistics. This is useful in order to get an
unbiased comparison with a model. ALCF currently does not support simulating
precipitation. Therefore, excluding precipitation from observations can be
important for the comparison. Otherwise, precipitation in observations can be
recognized as cloud, and contribute to the cloud occurrence statistics.

Description of this code can be found in the paper [Ship and ground-based lidar
and radiosonde evaluation of Southern Ocean clouds in the storm-resolving
general circulation model ICON and the ERA5 and MERRA-2
reanalyses](https://doi.org/10.5281/zenodo.14071808), Section 2.9.

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

**Note:** Based on your Python distribution, you might have to replace
`python3` with `python`.

This also activates the environment for the current session. After finishing
working with the code, the environment can be deactivated with `deactivate`.

## Commands

The following command are run as `./run` *cmd*, where *cmd* is the command
name. "Requires" lists commands which have to be run before running the current
command.

After running the commands, the resulting ALCF filters for each data source
are stored in `data/alcf_filter`.

### all

Run all of the commands below in a sequence.

### weather

Extract cloud types from weather reports.

### alcf

Run the ALCF to produce samples of near-surface backscatter.

### alcf_times

Requires: `weather`

Determine time periods of the weather types for use with the ACLF.

### prepare_samples

Requires: `alcf_times`

Prepare training samples.

### label_samples

Requires: `prepare_samples`

Label training samples with weather types.

### subsets

Requires: `prepare_samples`

Sort the samples into training, validation and testing subsets.

### train

Requires: `subsets`

Train an ANN.

### train_wo

Requires: `subsets`

Train ANNs without each data source.

### test

Requires: `train`

Test the ANN.

### test_wo

Requires: `train_wo`

The same as above, for for ANNs trained without each data sources.

### pred

Requires: `train`, `alcf`

Run prediction on all data sources.

### plot_roc

Requires: `test`

Plot ROC from test results and calculate thresholds for precipitation
probability.

### precip_ann

Requires: `precip`, `plot_roc`

Calculate precipitation periods from the ANN results.

### alcf_filter

Requires: `precip_ann`

Create ALCF filter from precipitation periods.

## License

The code is Copyright © 2023–2024 Peter Kuma and licensed under the MIT license
(see `LICENSE.md`).
