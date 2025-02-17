<h1 align="center">
  <span style="font-size: 40px;">MAexp</span> <!-- 将字体大小从30px调整到40px -->
  <br> <!-- 在描述文字之前的换行保持不变 -->
  A Generic Platform for RL-based Multi-Agent Exploration
</h1>


MAexp, a generic high-efficiency platform designed for multi-agent exploration, encompassing a diverse range of **scenarios** and **MARL algorithms**. The platform is developed in Python to smoothly integrate with existing reinforcement learning algorithms, and it is equally applicable to traditional exploration methods. In an effort to bridge the sim-to-real gap, all maps and agent properties within MAexp are modelled **continuously**, incorporating realistic physics to closely mirror real-world exploration. The framework of MAexp is as follow:

<img src=imgs/platform2_00.png  />

There are four kings of scenarios in MAexp: Random Obstacle, Maze, Indoor and Outdoor.

<img src=imgs/scene_00.png  />

<table><tr>
<td><img src="imgs/1.gif" alt="Description of GIF 1" style="width: 100%;" /></td>
<td><img src="imgs/2.gif" alt="Description of GIF 2" style="width: 100%;" /></td>
</tr></table>




An introduction video can be seen on [Bilibili](https://www.bilibili.com/video/BV12w4m1y7Qm/?vd_source=4a84fda80f7a87775762c3f89840bbfd) or [YouTube](https://youtu.be/wOxqxUpdxlM).


If you find this project useful, please consider giving it a star on GitHub! It helps the project gain visibility and supports the development. Thank you!


## Quick Start

### Installation

```
$ conda create -n maexp python=3.8 # or 3.9
$ conda activate maexp
$ git clone https://github.com/Replicable-MARL/MARLlib.git && cd MARLlib
$ pip install setuptools==65.5.0
$ pip install --user wheel==0.38.0
$ pip install -r requirements.txt
$ pip install protobuf==3.20.0
$ pip install scikit-fmm
$ cd /Path/To/MARLlib/marllib/patch
$ python add_patch.py -y
$ pip install tensorboard
$ pip install einops
$ pip install open3d

## if your torch could not work with cuda, you can try this:
$ pip uninstall torch torchvision torchaudio
$ pip install torch==1.12.1+cu116 torchvision==0.13.1+cu116 torchaudio==0.12.1 --extra-index-url https://download.pytorch.org/whl/cu116
```



### Preparation
(1) **Modify the code** of MARLLib and Ray.
```
$ cd MAexp
$ python modify.py
```

(2) Change the parameters of **Ray** and **MARL algorithms** as follow:

Ray:  `/Path/To/envs/maexp/lib/python3.8/site-packages/marllib/marl/ray/ray.yaml`

```yaml
# ray.yaml
local_mode: False # True for debug mode only
share_policy: "group" #  individual(separate) / group(division) / all(share)
evaluation_interval: 50 # evaluate model every 10 training iterations
framework: "torch" # only for torch
num_workers: 0 # thread number
num_gpus: 1 # gpu to use
num_cpus_per_worker: 5 # cpu allocate to each worker
num_gpus_per_worker: 0.25 # gpu allocate to each worker
checkpoint_freq: 100 # save model every 100 training iterations
checkpoint_end: True # save model at the end of the exp
restore_path: {"model_path": "", "params_path": ""} # load model and params path: 1. resume exp 2. rendering policy
stop_iters: 9999999 # stop training at this iteration
stop_timesteps: 2000000 # stop training at this timesteps
stop_reward: 999999 # stop training at this reward
seed: 321 # ray seed
local_dir: "/Path/To/Your/Folder" #  all results placed
```

MARL algorithms: `/Path/To/envs/maexp/lib/python3.8/site-packages/marllib/marl/algos/hyperparams/common/`

```yaml
# ippo.yaml
algo_args:
  use_gae: True
  lambda: 1.0
  kl_coeff: 0.2
  batch_episode: 2
  num_sgd_iter: 2
  vf_loss_coeff: 1.0
  lr: 0.0001
  entropy_coeff: 0.001
  clip_param: 0.2
  vf_clip_param: 10.0
  batch_mode: "truncate_episodes"
   
# itrpo.yaml
algo_args:
  use_gae: True
  lambda: 1.0
  gamma: 0.99
  batch_episode: 2
  kl_coeff: 0.2
  num_sgd_iter: 2
  grad_clip: 10
  clip_param: 0.2
  vf_loss_coeff: 1.0
  entropy_coeff: 0.001
  vf_clip_param: 10.0
  batch_mode: "truncate_episodes"
  kl_threshold: 0.06
  accept_ratio: 0.5
  critic_lr: 0.001

# mappo.yaml
algo_args:
  use_gae: True
  lambda: 1.0
  kl_coeff: 0.2
  batch_episode: 2
  num_sgd_iter: 2
  vf_loss_coeff: 1.0
  lr: 0.0001
  entropy_coeff: 0.001
  clip_param: 0.2
  vf_clip_param: 10.0
  batch_mode: "truncate_episodes"
  
# matrpo.yaml
algo_args:
  use_gae: True
  lambda: 1.0
  gamma: 0.99
  batch_episode: 2
  kl_coeff: 0.2
  num_sgd_iter: 2
  grad_clip: 10
  clip_param: 0.2
  vf_loss_coeff: 1.0
  entropy_coeff: 0.001
  vf_clip_param: 10.0
  batch_mode: "truncate_episodes"
  kl_threshold: 0.06
  accept_ratio: 0.5
  critic_lr: 0.001
  
# vdppo.yaml
algo_args:
  use_gae: True
  lambda: 1.0
  kl_coeff: 0.2
  batch_episode: 2
  num_sgd_iter: 2
  vf_loss_coeff: 1.0
  lr: 0.0001
  entropy_coeff: 0.001
  clip_param: 0.2
  vf_clip_param: 10.0
  batch_mode: "truncate_episodes"
  mixer: "qmix" # qmix or vdn
  
# vda2c.yaml
algo_args:
  use_gae: True
  lambda: 1.0
  vf_loss_coeff: 1.0
  batch_episode: 2
  batch_mode: "truncate_episodes"
  lr: 0.0001
  entropy_coeff: 0.001
  mixer: "qmix" # vdn
```

(3) Data Preparation

Before you begin, you need to download the dataset and place it in the root directory of the project. Follow these steps:

1. Download the dataset from the provided link: [Google Drive](https://drive.google.com/file/d/1zbehe442sUbfFGGhgNmtvY89QiOmbiPW/view?usp=sharing) or [Baidu Netdisk](https://pan.baidu.com/share/init?surl=n-PYbfAluBkw7HPPGXJfxg&pwd=1234)
2. Extract the downloaded file.
3. Copy or move the dataset to the root directory of this project.



### Training

(1) Change the parameters in the specific scenario yaml file (e.g. If you want to explore in maze, change the `./yaml/maze.yaml`) . **'is_train'** must be 'True'. If you are using GPUs for training, it is recommended to use at least **two**: one for sampling and another for the model itself.

```yaml
device: 'cuda' # Cuda or cpu
num_agent: 3 # number of agents in swarm
is_train: True # if training, use True
algo: 'vda2c' # MARL algorithms, could be [ippo,itrpo,mappo,matrpo,vdppo or vda2c]

Map:
  training_map_num: 1 # how many map we use in the map_list below
  map_resolution: 1.0
  region: 8
  max_global_step: 31
  map_list: ['map1','map19'] # map use in trianing
  scene: 'maze' # scenarios name, could not change
```

(2) Check the parameters in `env_v7.py`. If you need to debug, please turn `local_model` to `True` and `num_work` to `1`.

```python
# num_workers represent the number of parallel environments for sampling; local_model use False for trainin, while use True for debug. 
method.fit(env, model, stop={'episode_reward_mean': 200000, 'timesteps_total': 10000000}, local_mode = False, num_workers = 4, share_policy='all', checkpoint_freq=300)
```

(3) Then run this in the terminal

```
python env_v7.py --yaml_file ./yaml/maze.yaml
```

### Testing

(1) Change the parameters: **'is_train'** must be 'False'.

(2) Change the parameter in 'env_v7.py', add the `params_path` and `model_path`.

```python
# params_path is the training parameters path; model_path is the checkpoint path;local_mode must be 'True'.
method.fit(env, model, stop={'episode_reward_mean': 200000, 'timesteps_total': 10000000}, restore_path={'params_path': "/remote-home/ums_zhushaohao/2023/Multi-agent-Exploration/exp_results/vda2c_vit_crossatt_MAexp/VDA2CTrainer_maexp_MAexp_b92fd_00000_0_2024-03-14_09-17-48/params.json",  # experiment configuration
'model_path': "/remote-home/ums_zhushaohao/2023/Multi-agent-Exploration/exp_results/vda2c_vit_crossatt_MAexp/VDA2CTrainer_maexp_MAexp_ef8e1_00000_0_2024-03-16_10-50-00/checkpoint_011400/checkpoint-11400"},
local_mode=True, num_workers = 0, share_policy='all')
```

(3) Then run this in the terminal

```cmd
python env_v7.py --yaml_file ./yaml/maze.yaml
```

If you want to save the images, use this:

```
python env_v7.py --yaml_file ./yaml/maze.yaml --is_capture
```



### Larger swarm

MAexp can also accommodate a large number of robots, provided that `communication` and `action generation strategies` are properly adjusted to avoid `CUDA out-of-memory` errors while training the policy.
You can visual the environment of random walk strategy with following step:

(1) Please comment out the section in `env_v7.py` where the MARL training is used, and enable the code at the bottom that employs the random walk strategy.

(2) Then run this in the terminal

```
python env_v7.py --yaml_file ./yaml/outdoor_large_swarm.yaml
```

### Citation

If you use MAexp in your research, please cite the [MAexp paper](https://arxiv.org/abs/2404.12824) (accepted by ICRA 2024, see you in Yokohama~).

```tex
@misc{zhu2024maexp,
      title={MAexp: A Generic Platform for RL-based Multi-Agent Exploration}, 
      author={Shaohao Zhu and Jiacheng Zhou and Anjun Chen and Mingming Bai and Jiming Chen and Jinming Xu},
      year={2024},
      eprint={2404.12824},
      archivePrefix={arXiv},
      primaryClass={cs.RO}
}
```

### Author

Shaohao Zhu ([zhushh9@zju.edu.cn](mailto:zhushh9@zju.edu.cn))
