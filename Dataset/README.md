# EEG dataset files

This directory contains the MATLAB (`.mat`) recordings used by `Pouya_Bastani_TFS1.ipynb`. The notebook reads each file's `Clean_data` variable and assigns the task condition from the filename.

## Expected contents

The current project folder contains **480 recordings** arranged across four conditions, 40 participants, and three trials per condition:

| Condition | Filename prefix |
|---|---|
| Arithmetic | `Arithmetic_sub` |
| Mirror-image | `Mirror_image_sub` |
| Relax | `Relax_sub` |
| Stroop | `Stroop_sub` |

Filenames follow the pattern `<prefix>_<participant>_trial<trial>.mat`, for example `Arithmetic_sub_1_trial1.mat`.

The notebook's saved analysis reports each cleaned recording as 32 channels × 3,200 samples, sampled at 128 Hz (25 seconds). Keep the files in this directory so the notebook's relative `Dataset/` path resolves when it is run from the project root.

## Data access and redistribution

Obtain the recordings from the source provided for the course. Check the source's terms before committing the `.mat` files to a public repository. If redistribution is not permitted, omit the data files from GitHub and provide authorized access or acquisition instructions separately; the notebook cannot be rerun without them.
