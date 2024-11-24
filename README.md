# Imagination_to_Real

### [🌐 Website](https://www.smilingrobo.com) | [📝 Paper](https://arxiv.org/abs/2411.00083)

**imagination-to-real** Train your robot to do whatever you want using Generative AI

We bring realistic and diverse visual data from generative models to classical physics simulators, enabling robots to
learn highly dynamic tasks like parkour without requiring depth.

`Image_Maker` contains our text-to-image generation code.

---

**Table of Contents**
- [Image_Maker](#make-images-using-image_maker)
  - [Installation](#installation)
    - [Setup conda environment](#1-setup-conda-environment)
    - [Install ComfyUI + Dependencies](#2-install-comfyui--dependencies)
    - [Setting up Models](#3-setting-up-models)
  - [Usage](#usage)
    - [Running the Example Workflow](#running-the-example-workflow)
    - [Adding Your Own Workflows](#adding-your-own-workflows)
    - [Scaling Image Generation](#scaling-image-generation)
- [Train the Robot]()

- [Citation](#citation)

## Make Images using Image_Maker

#### 1. Setup Conda Environment

```bash
conda create -n lucidsim python=3.10
conda activate lucidsim
```

#### 2. Install ComfyUI + Dependencies

For consistency, we recommend
using [this version](https://github.com/comfyanonymous/ComfyUI/tree/ed2fa105ae29af6621232dd8ef622ff1e3346b3f) of
ComfyUI.

```bash
# Choose the CUDA version that your GPU supports. We will use CUDA 12.1
pip install torch==2.1.2 torchvision==0.16.2 torchaudio==2.1.2 --extra-index-url https://download.pytorch.org/whl/cu121

# Installing ComfyUI
git clone https://github.com/comfyanonymous/ComfyUI
cd ComfyUI
git checkout ed2fa105ae29af6621232dd8ef622ff1e3346b3f
pip install -r requirements.txt

# Installing imagination_to_real module
git clone https://github.com/SmilingRobo/imagination-to-real imagination_to_real
cd imagination_to_real
pip install -e .
```

#### 3. Setting up Models

We recommend placing your models outside the `ComfyUI` repo for better housekeeping. For this, you'll need to link your
model paths through a config file. Check out the `configs` folder for a template, where you'll specify locations for
checkpoints, controlnets, and VAEs. For the provided `three_mask_workflow` example, these are the models you'll need:

- [SDXL Turbo 1.0](https://huggingface.co/stabilityai/sdxl-turbo/blob/main/sd_xl_turbo_1.0_fp16.safetensors): place
  under `checkpoints`
- [SDXL Depth ControlNet](https://huggingface.co/diffusers/controlnet-depth-sdxl-1.0): place under `controlnet`
- [SDXL VAE](https://huggingface.co/stabilityai/sdxl-vae): place under `vae`

After cloning this repository, you'll need to add ComfyUI to your `$PYTHONPATH` and link your model paths. We recommend
managing these in a local `.env` file. Then, link the config file you just created.

```bash
export PYTHONPATH=/path/to/ComfyUI:$PYTHONPATH

# See the `configs` folder for a template
export COMFYUI_CONFIG_PATH=/path/to/extra_model_paths.yaml
```

## Usage

imagination_to_real is organized by _workflows_. We include our main workflow called `three_mask_workflow`, which generates an image
given a depth map along with three semantic masks, each coming with a different prompt (for example,
foreground/background/object).

#### Running the Example Workflow

We provide example conditioning images and prompts for `three_mask_workflow` under the `examples` folder, grouped by
scene. To try it out, use:

```bash
python Imagination-to-Real/Image_Maker/scripts/demo_three_mask_workflow.py [--example-name] [--seed] [--save]
```

where `example-name` corresponds to one of the scenes in the `examples/three_mask_workflow` folder, and the `save` flag
writes the output to the corresponding `examples/three_mask_workflow/[example-name]/samples` folder. The script will
randomly select one of our provided prompts.

#### Adding Your Own Workflows

The graphical interface for ComfyUI is very helpful for designing your own workflows. Please see their documentation for
how to do this. By using this
helpful [workflow to python conversion tool](https://github.com/pydn/ComfyUI-to-Python-Extension.git), you can script
your workflows as we've done with `Image_Maker/workflows/three_mask_workflow.py`.

#### Scaling Image Generation

In LucidSim, we use a distributed setup to generate images at scale. We utilize rendering nodes, launched independently
on many machines, that receive and fulfill rendering requests from the physics engine containing prompts and
conditioning images through a task queue (see [Zaku](https://zaku.readthedocs.io/en/latest/)). We hope to release setup
instructions for this in the future, but we have included `Image_Maker/render_node.py` for your reference.






## Citation

If you find our work useful, please consider citing:

```
@inproceedings{yu2024learning,
  title={Learning Visual Parkour from Generated Images},
  author={Alan Yu and Ge Yang and Ran Choi and Yajvan Ravan and John Leonard and Phillip Isola},
  booktitle={8th Annual Conference on Robot Learning},
  year={2024},
}
```
