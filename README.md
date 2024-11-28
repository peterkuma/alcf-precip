# ALCF precipitation detection

**This repository is currently in preparation.**

This repository contains code for detecting precipitation in lidar data using
an artificial neural network for use with the [Automatic Lidar and Ceilometer
Framework](https://alcf.peterkuma.net).

The end product of running the code is an ALCF include filter, which can be
used with the `alcf stats` and `alcf auto lidar` commands to filter out
precipitation when calculating statistics. This is useful in order to get an
unbiased comparison with an atmospheric model (climate model, numerical weather
prediction model, or reanalysis). ALCF currently does not support simulating
precipitation. Therefore, excluding precipitation from observations can be
important for the comparison, depending on the overall prevalence of
precipitation. Otherwise, precipitation in observations is often classified as
clouds and contributes to the cloud occurrence statistics, while this
contribution is not present in the model statistics.

Description of this code can be found in the paper [Ship and ground-based lidar
and radiosonde evaluation of Southern Ocean clouds in the storm-resolving
general circulation model ICON and the ERA5 and MERRA-2
reanalyses](https://doi.org/10.5281/zenodo.14071808), Section 2.9.

The training dataset comes from the Vaisala CL51 ceilometer and synoptic
(human-performed) observations on the RV *Polarstern* voyages in the Southern
Ocean between 2010 and 2021, but users of this code can use any other dataset
of lidar and synoptic observations for the training.

Due to large size, the data will be provided separately from this repository
[TODO]. If you want to use this code, it is recommended to start with these
data, and after running it successfully, use the data that you are interested
in classifying. You can either use an ANN trained on the RV *Polarstern*
voyages (`data/ann/all.h5`) to classify samples in your dataset of interest or
train your own ANN if you have colocated lidar and synoptic observations
available.  The supplied ANN may not be suitable for lidars other than the CL51
or geographical locations other than the Southern Ocean. Ideally, this should
be verified on some test data.

## Requirements

The code should work on any standard Linux distribution. It has been developed
and tested on Devuan GNU/Linux 5 (daedalus). Running the code on other Windows
or macOS might be possible, but it has not been tested. On Windows, it might be
possible to run it most easily under the Windows Subsystem for Linux.

The commands are to be run in the terminal, such as GNU Bash. Specifically,
some of the command-line syntax is not compatible with `zsh` (the default shell
on macOS). To use the commands on macOS (untested), it is recommended to start
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

The following commands are run as `./run` *cmd*, where *cmd* is the command
name. "Requires" lists commands that have to be run before running the current
command.

After running the commands, the resulting ALCF filters for each data source
are stored in `data/alcf_filter`. These can be used with the `alcf stats`
and `alcf auto lidar` commands for filtering.

### all

Run all of the commands below in a sequence. Running the whole sequence can be
time-consuming. It may be advisable to run the commands individually instead,
especially if there are any errors.

### weather

Extract cloud types from weather reports.

### alcf

Run the ALCF to produce samples of near-surface backscatter.

### alcf_times

Requires: `weather`

Determine the time periods of the weather types for use with the ACLF.

### prepare_samples

Requires: `alcf_times`

Prepare training samples.

### label_samples

Requires: `prepare_samples`

Label training samples with weather types.

### subsets

Requires: `prepare_samples`

Sort the samples into training, validation, and testing subsets.

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

The same as above, but for ANNs trained without each data source.

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

Create an ALCF filter from precipitation periods.

## License

The code is Copyright © 2023–2024 Peter Kuma and licensed under the MIT license
(see `LICENSE.md`).
