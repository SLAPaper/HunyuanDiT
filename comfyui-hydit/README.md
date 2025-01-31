# comfyui-hydit

This repository houses a tailored node and workflow designed specifically for HunYuan DIT. The official tests conducted on DDPM, DDIM, and DPMMS have consistently yielded results that align with those obtained through the Diffusers library. However, it's important to note that we cannot assure the consistency of results from other ComfyUI native samplers with the Diffusers inference. We cordially invite users to explore our workflow and are open to receiving any inquiries or suggestions you may have.

## Overview


### Workflow text2image

![Workflow](img/work_diffusers.png)
[workflow_diffusers](workflow/workflow_diffusers.json) file for HunyuanDiT txt2image with diffusers backend.  
![Workflow](img/workflow_ksampler.png)
[workflow_ksampler](workflow/workflow_ksampler.json) file for HunyuanDiT txt2image with ksampler backend.
![Workflow](img/workflow_lora_controlnet.png)
[workflow_lora_controlnet_diffusers](workflow/workflow_lora_controlnet.json) file for HunyuanDiT lora and controlnet model with diffusers backend.


## Usage

Make sure you run the following command inside [ComfyUI](https://github.com/comfyanonymous/ComfyUI) project with our [comfyui-hydit](.) and have correct conda environment.

```shell
# Please use python 3.10 version with cuda 11.7
# Download comfyui code
git clone https://github.com/comfyanonymous/ComfyUI.git

# Install torch, torchvision, torchaudio
pip install torch==2.0.1 torchvision==0.15.2 torchaudio==2.0.2 --index-url https://download.pytorch.org/whl/cu117 --default-timeout=100 future

# Install Comfyui essential python package
cd ComfyUI
pip install -r requirements.txt

# ComfyUI has been successfully installed!


# Move to the ComfyUI custom_nodes folder and copy comfyui-hydit folder from HunyuanDiT Repo.
cd custom_nodes
git clone https://github.com/Tencent/HunyuanDiT.git
cp -r HunyuanDiT/comfyui-hydit ./
rm -rf HunyuanDiT
cd comfyui-hydit

# !!! If using windows system !!!
cd custom_nodes
git clone https://github.com/Tencent/HunyuanDiT.git
xcopy /E /I HunyuanDiT\comfyui-hydit comfyui-hydit
rmdir /S /Q HunyuanDiT
cd comfyui-hydit

# Install some essential python Package.
pip install -r requirements.txt

# Our tool has been successfully installed!

# Go to ComfyUI main folder
cd ../..
# Run the ComfyUI Lauch command
python main.py --listen --port 80

# Running ComfyUI successfully!
```

## Download weights for diffusers mode

```shell
python -m pip install "huggingface_hub[cli]"
mkdir models/hunyuan
huggingface-cli download Tencent-Hunyuan/HunyuanDiT-v1.1 --local-dir ./models/hunyuan/ckpts
huggingface-cli download Tencent-Hunyuan/Distillation-v1.1 pytorch_model_distill.pt --local-dir ./models/hunyuan/ckpts/t2i/model
```

## Download weights for ksampler mode
Download the [clip encoder](https://huggingface.co/Tencent-Hunyuan/HunyuanDiT/blob/main/t2i/clip_text_encoder/pytorch_model.bin) and place it in `ComfyUI/models/clip`  
Download the [mt5](https://huggingface.co/Tencent-Hunyuan/HunyuanDiT/blob/main/t2i/mt5/pytorch_model.bin) and place it in `ComfyUI/models/t5`  
Download the [base model](https://huggingface.co/Tencent-Hunyuan/HunyuanDiT/blob/main/t2i/model/pytorch_model_ema.pt) and place it in `ComfyUI/models/checkpoints`  
Download the [sdxl vae](https://huggingface.co/Tencent-Hunyuan/HunyuanDiT/blob/main/t2i/sdxl-vae-fp16-fix/diffusion_pytorch_model.bin) and place it in `ComfyUI/models/vae`   


## Custom Node
Below I'm trying to document all the nodes, thanks for some good work[[1]](#1)[[2]](#2).
#### HunYuan Pipeline Loader
- Loads the full stack of models needed for HunYuanDiT.  
- **pipeline_folder_name** is the official weight folder path for hunyuan dit including clip_text_encoder, model, mt5, sdxl-vae-fp16-fix and tokenizer.
- **lora** optional to load lora weight.

#### HunYuan Checkpoint Loader
- Loads the base model for HunYuanDiT in ksampler backend.  
- **model_name** is the weight list of comfyui checkpoint folder.


#### HunYuan CLIP Loader
- Loads the clip and mt5 model for HunYuanDiT in ksampler backend.  
- **text_encoder_path** is the weight list of comfyui clip model folder.
- **t5_text_encoder_path** is the weight list of comfyui t5 model folder.

#### HunYuan VAE Loader
- Loads the vae model for HunYuanDiT in ksampler backend.  
- **model_name** is the weight list of comfyui vae model folder.

#### HunYuan Scheduler Loader
- Loads the scheduler algorithm for HunYuanDiT.  
- **Input** is the algorithm name including ddpm, ddim and dpmms.
- **Output** is the instance of diffusers.schedulers.

#### HunYuan Model Makeup
- Assemble the models and scheduler module.  
- **Input** is the instance of StableDiffusionPipeline and diffusers.schedulers.
- **Output** is the updated instance of StableDiffusionPipeline.

#### HunYuan Clip Text Encode
- Assemble the models and scheduler module.  
- **Input** is the string of positive and negative prompts.
- **Output** is the converted string for model.

#### HunYuan Sampler
- Similar with KSampler in ComfyUI.  
- **Input** is the instance of StableDiffusionPipeline and some hyper-parameters for sampling.
- **Output** is the generated image.

#### HunYuan Lora Loader
- Loads the lora model for HunYuanDiT in diffusers backend.  
- **lora_name** is the weight list of comfyui lora folder.

#### HunYuan ControNet Loader
- Loads the controlnet model for HunYuanDiT in diffusers backend.  
- **controlnet_path** is the weight list of comfyui controlnet folder.

## Reference 
<a id="1">[1]</a> 
https://github.com/Limitex/ComfyUI-Diffusers  
<a id="2">[2]</a>
https://github.com/Tencent/HunyuanDiT/pull/59
