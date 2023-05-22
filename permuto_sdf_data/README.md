# PermutoSDF-Trans Data

For training and experiments we use the the [DTU] and [NeRF] datasets. \
We also provide our own EasyPBR-based dataset containing the synthetic head. \
All the data used in the paper in a format that is readable by our scripts can be found [here](https://drive.google.com/drive/folders/1IcCtveUyQ4sHwVpdyjLt28rAdHYFHmtT?usp=share_link):

Unzip all the files datasets inside the permuto_sdf_data folder so you end up with a structure like:

```bash
    permuto_sdf_data
    ├── data_DTU
    │   ├── dtu_scan24
    │   ├── dtu_scan37
    │   └── ...
```

Finally you have to open `permuto_sdf_py/paths/data_paths.py` and add another line for where to find the path permuto_sdf_data on your computer. 
So the new line will be something of the form `comp_3": "<THE_PATH_WHERE_YOU_DOWNLOADED_THE_DATA>/permuto_sdf_data",`
Now when calling the training routines from the next sections you can add the flag `--comp_name comp_3` do indicate that you are going to use this path


# Train

<!-- ## Train individual scenes  -->
<!-- We provide script to train individual scenes using the permuto_sdf pipeline  -->

<!-- - #### Train PermutoSDF on a certain scene -->
## Train PermutoSDF on a certain scene
```Shell
./permuto_sdf_py/train_permuto_sdf.py \
--dataset dtu \
--scene dtu_scan24 \
--comp_name comp_1 \
--exp_info default
```
The training will start and on the dtu_scan24 scene and a viewer will appear where you can inspect the training progress.
Other options for this script are:
```Shell
--no_viewer  #disables the OpenGL viewer so you can run the script on a headless machine
--low_res    #loads the images at lower resolution. Useful on machines with lower than 6GB of VRAM
--with_mask  #Uses the binary mask from DTU or BlendedMVS dataset to ignore the background
```
Additionally you can enable saving of the checkpoints and other logging options by setting to true the flag `save_checkpoint` in `./config/train_permuto_sdf.cfg`.\
At any point during training you can click inside the GUI on "control_view" in order to move the camera around the scene. \
<img src="imgs/train_permuto_sdf.png" width="500"/>

## Train NeRF
```Shell
./permuto_sdf_py/train_nerf.py \
--dataset dtu \
--scene dtu_scan24 \
--comp_name comp_3 \
--exp_info default
```
We also provide an example which trains a NeRF-like model using the permutohedral lattice. 


## Serial Training
It is common to want to train on all the objects from the DTU dataset and instead of running various times the `train_permutosdf.py` script we provide a script to run through all the object from the dataset one-by-one so you can leave this script running overnight. 

```Shell
./permuto_sdf_py/experiments/serial_training/serial_train.py \
--dataset dtu \
--with_mask \
--comp_name comp_3 \ 
--exp_info experiment_name
```

# Run on your custom dataset

If you already have images and camera parameters that you computed from structure from motion packages like Colmap:

We provide a small example of creating a custom dataset with [./permuto_sdf_py/experiments/run_custom_dataset/run_custom_dataset.py](./permuto_sdf_py/experiments/run_custom_dataset/run_custom_dataset.py). Running the script with the default values `./run_custom_dataset.py` will attempt to load from the EasyPBR dataset. 
However, the script is ment to be easily modifiable for your purposes. For this you would need images together with camera intrinsics and extrinsics. The script shows how to load the images and the format required for the camera parameters. Most importantly, you need to scale and translate your scene so that your object of interest lies within the bounding sphere of radius 0.5. You have to modify this manually for your dataset using the `--scene_scale` and `--scene_translation` flags as it's difficult to provide a reliable and automatic way of selecting these parameters. I recommend to run first with `run_custom_dataset.py --scene_scale 1.0 --scene_translation 0.0 0.0 0.0 --dataset_path <YOUR_DATASET>` and modify the scale and translation until you get the object inside the bounding sphere. When you run the `run_custom_dataset.py` script it will also print the scale and the origin of all the cameras you defined.  Note that you don't need to get your cameras to be within the bounding sphere, only the object you want reconstructed needs to be contained within it, the cameras can be outside of the sphere.

Running the script should show something like this where the bounding sphere is in the center and in red are the frustums of all the cameras. \
<img src="imgs/custom_data.png" width="500"/>


# Citation
```
@inproceedings{rosu2023permutosdf,
    title={PermutoSDF: Fast Multi-View Reconstruction with 
            Implicit Surfaces using Permutohedral Lattices  },
    author={Radu Alexandru Rosu and Sven Behnke},
    booktitle = {IEEE/CVF Conference on Computer Vision and Pattern Recognition (CVPR)},
    year={2023}
}
```


[permutohedral encoding]: <https://github.com/RaduAlexandru/permutohedral_encoding>
[permutohedral_encoding]: <https://github.com/RaduAlexandru/permutohedral_encoding>
[EasyPBR]: <https://github.com/RaduAlexandru/easy_pbr>
[DataLoaders]: <https://github.com/RaduAlexandru/data_loaders>
[Multiface]: <https://github.com/facebookresearch/multiface>
[DTU]: <https://roboimagedata.compute.dtu.dk/?page_id=36>
[BlendedMVS]: <https://github.com/YoYo000/BlendedMVS>
[APEX]: <https://github.com/NVIDIA/apex>