<h1>VideoAgent: A Memory-augmented Multimodal Agent for Video Understanding (ECCV 2024)</h1>

# Introduction
This is the official code repository of [VideoAgent: A Memory-augmented Multimodal Agent for Video Understanding
](https://videoagent.github.io/). VideoAgent is a mulit-modal agent that can understand the input video and answer the questions raised by you.

Given a video and a question, VideoAgent has two phases: memory construction phase and inference phase. During the memory construction phase, structured information is extracted from the video and stored in the memory. During the inference phase, a LLM is prompted to use a set of tools interacting with the memory to answer the question.
<p align="center">
<img src="imgs/teaser.png" width=90%>
</p>

# Prerequisites
This project is tested on Ubuntu 20.04 with a NVIDIA RTX 4090(24GB).  


# Installation Guide
Use the following command to create the environment named as videoagent:
```sh
conda env create -f environment.yaml
```

위 방식대로 conda 환경을 만드니... gensim 빌드가 실패했다는 오류가 떴다...
그래서 나는 그냥 `pip install gensim`으로 gensim을 다시 설치하였으며...
gensim-4.3.3 버전이 설치되었음을 확인하였다.


Create the environment of [Video-LLaVA](https://github.com/PKU-YuanGroup/Video-LLaVA) by running the following command:
```sh
git clone https://github.com/PKU-YuanGroup/Video-LLaVA
cd Video-LLaVA
conda create -n videollava python=3.10 -y
conda activate videollava
pip install --upgrade pip  # enable PEP 660 support
pip install -e .
pip install -e ".[train]"
pip install flash-attn==2.7.4.post1 --no-build-isolation
pip install decord opencv-python git+https://github.com/facebookresearch/pytorchvideo.git@28fe037d212663c6a24f373b94cc5d478c8c1a1d
```

위 방식대로 할 때 `pip install flash-attn --no-build-isolation` 코드가 오류가 남...
나는 cuda 11.7 버전 pc에서 실험했을 때 결과임. (Video LLava는 torch 2.0.1을 쓰라고 함.)
-> FlashAttention은 PyTorch 2.2 버전부터 공식적으로 통합되어 성능 향상이 이루어졌습니다. 따라서, PyTorch 2.0.1과 호환되는 FlashAttention의 공식 버전은 제공되지 않을 수 있습니다.
-> 쓰바! 내가 pyproject.toml에 torch 최소 버전 높여버림!!!

Note: Only the conda envrionment named videollava is required for this project, while the Video-LLaVA repository is not required. You can clone Video-LLaVA repository to anywhere you want and build the conda environment named videollava.

Download the ```cache_dir.zip``` and ```tool_models.zip``` from [here](https://zenodo.org/records/11031717) and unzip them to the directory of ```VideoAgent```. This will create two folder ```cache_dir```(the model weights of VideoLLaVA) and ```tool_models```(the model weights of all other models) under ```VideoAgent```.

# Usage
Make sure you are under VideoAgent directory.
Enter your OpenAI api key in ```config/default.yaml```.

First, open a terminal and run:
```sh
conda activate videollava
python video-llava.py
```
This will start a Video-LLaVA server process that will deal with Visual Question Answering request raised by VideoAgent.

Once you see ```ready for connection!``` in the first process, Then, open another terminal and run:
```sh
conda activate videoagent
python demo.py
```
This will create a Gradio demo shown as follows.
<p align="center">
<img src="imgs/demo.png" width=90%>
</p>
You can choose the example videos for inference, or you can also upload your own videos and questions. Once submitted, VideoAgent will start processing your video and store the files under ```preprocess/your_video_name```. After processing the input video, it will answer your question.

The results will provide:
1. the answer to the question
2. the replay with object re-ID of the input video
3. the inference log (chain-of-thought) of VideoAgent

For batch inference, you can run
```sh
conda activate videoagent
python main.py
```

# Citation
If you find our paper and code useful in your research, please consider giving a star ⭐ and citation 📝.
```
@inproceedings{fan2025videoagent,
  title={Videoagent: A memory-augmented multimodal agent for video understanding},
  author={Fan, Yue and Ma, Xiaojian and Wu, Rujie and Du, Yuntao and Li, Jiaqi and Gao, Zhi and Li, Qing},
  booktitle={European Conference on Computer Vision},
  pages={75--92},
  year={2025},
  organization={Springer}
}
```

