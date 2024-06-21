# MindEye2

## Installation

1. Git clone this repository:

```
from huggingface_hub import snapshot_download, hf_hub_download
snapshot_download(repo_id="pscotti/mindeyev2", repo_type = "dataset", revision="main", allow_patterns="*.tar", cache_dir = "./cache",
    local_dir= "your_local_dir", local_dir_use_symlinks = False, resume_download = True)
hf_hub_download(repo_id="pscotti/mindeyev2", local_dir="../data/",filename="betas_all_subj01_fp32.hdf5", repo_type="dataset") # repeat for other subj if needed
hf_hub_download(repo_id="pscotti/mindeyev2", local_dir="../data/", filename="coco_images_224_float16.hdf5", repo_type="dataset")
hf_hub_download(repo_id="pscotti/mindeyev2", local_dir="../data/", filename="betas_all_subj01_fp32_renorm.hdf5", repo_type="dataset")
```

3. Run ```. src/setup.sh``` to install a new "fmri" virtual environment. Make sure the virtual environment is activated with "source fmri/bin/activate".

## Usage

- ```src/Train.ipynb``` trains models (both single-subject and multi-subject). Check the argparser arguments to specify how you want to train the model (e.g., ```--num_sessions=1``` to train with 1-hour of data).
    - Final models used in the paper were trained on an 8xA100 80GB node and will OOM on weaker compute. You can train the model on weaker compute with minimal performance impact by changing certain model arguments: We recommend lowering hidden_dim to 1024 (or even 512), removing the low-level submodule (``--no-blurry_recons``), and lowering the batch size.
    - To train a single-subject model, set ```--no-multi_subject``` and ```--subj=#``` where # is the subject from NSD you wish to train
    - To train a multi-subject model (i.e., pretraining), set ```--multi_subject``` and ```--subj=#``` where # is the one subject out of 8 NSD subjects to **not** include in the pretraining.
    - To fine-tune from a multi-subject model, set ```--no-multi_subject``` and ```--multisubject_ckpt=path_to_your_pretrained_ckpt_folder```
- ```src/recon_inference.ipynb``` will run inference on a pretrained model, outputting tensors of reconstructions/predicted captions/etc.
- ```src/final_evaluations.ipynb``` will visualize reconstructions output from ```src/recon_inference``` and compute quantitative metrics.
- See .slurm files for example scripts for running the .ipynb notebooks as batch jobs submitted to Slurm job scheduling.
