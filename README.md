## Requirements

- Python 3
- [PyTorch](https://pytorch.org/) tested on 1.4.0, 1.5.0
- json
- PIL
- skimage
- tqdm
- cv2

For visualization

- [trimesh](https://trimsh.org/) with pyembree
- PyOpenGL
- freeglut (use `sudo apt-get install freeglut3-dev` for ubuntu users)
- ffmpeg

Run the following code to install all pip packages:

```sh
pip install -r requirements.txt
```

## Download Pre-trained model

Run the following script to download the pretrained model. The checkpoint is saved under `./checkpoints/`.

```
sh ./scripts/download_trained_model.sh
```

## A Quick Testing

To process images under `./sample_images`, run the following code:

```
sh ./scripts/demo.sh
```

The resulting obj files and rendering will be saved in `./results`. You may use meshlab (http://www.meshlab.net/) to visualize the 3D mesh output (obj file).

## Testing

1. run the following script to get joints for each image for testing (joints are used for image cropping only.). Make sure you correctly set the location of OpenPose binary. Alternatively [colab demo](https://colab.research.google.com/drive/11z58bl3meSzo6kFqkahMa35G5jmh2Wgt) provides more light-weight cropping rectange estimation without requiring openpose.

```
python apps/batch_openpose.py -d {openpose_root_path} -i {path_of_images} -o {path_of_images}
```

2. run the following script to run reconstruction code. Make sure to set `--input_path` to `path_of_images`, `--out_path` to where you want to dump out results, and `--ckpt_path` to the checkpoint. Note that unlike PIFu, PIFuHD doesn't require segmentation mask as input. But if you observe severe artifacts, you may try removing background with off-the-shelf tools such as [removebg](https://www.remove.bg/). If you have `{image_name}_rect.txt` instead of `{image_name}_keypoints.json`, add `--use_rect` flag. For reference, you can take a look at [colab demo](https://colab.research.google.com/drive/11z58bl3meSzo6kFqkahMa35G5jmh2Wgt).

```
python -m apps.simple_test
```

3. optionally, you can also remove artifacts by keeping only the biggest connected component from the mesh reconstruction with the following script. (Warning: the script will overwrite the original obj files.)

```
python apps/clean_mesh.py -f {path_of_objs}
```

## Visualization

To render results with turn-table, run the following code. The rendered animation (.mp4) will be stored under `{path_of_objs}`.

```
python -m apps.render_turntable -f {path_of_objs} -ww {rendering_width} -hh {rendering_height}
# add -g for geometry rendering. default is normal visualization.
```
