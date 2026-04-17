# Alpha Zero General (any game, any framework!)
A simplified, highly flexible, commented and (hopefully) easy to understand implementation of self-play based reinforcement learning based on the AlphaGo Zero paper (Silver et al). It is designed to be easy to adopt for any two-player turn-based adversarial game and any deep learning framework of your choice. A sample implementation has been provided for the game of Othello in PyTorch and Keras. An accompanying tutorial can be found [here](https://suragnair.github.io/posts/alphazero.html). We also have implementations for many other games like GoBang and TicTacToe.

To use a game of your choice, subclass the classes in ```Game.py``` and ```NeuralNet.py``` and implement their functions. Example implementations for Othello can be found in ```othello/OthelloGame.py``` and ```othello/{pytorch,keras}/NNet.py```. 

```Coach.py``` contains the core training loop and ```MCTS.py``` performs the Monte Carlo Tree Search. The parameters for the self-play can be specified in ```main.py```. Additional neural network parameters are in ```othello/{pytorch,keras}/NNet.py``` (cuda flag, batch size, epochs, learning rate etc.). 

To start training a model for Othello:
```bash
python main.py
```
Choose your framework and game in ```main.py```.

### 代码库导览（中文）

如果你想快速理解这个仓库，可以按下面的结构看：

- 根目录核心抽象与训练流程  
  - `Game.py`：定义“游戏环境”统一接口（状态、动作、胜负判断、对称变换等）。  
  - `NeuralNet.py`：定义“神经网络包装器”统一接口（训练、预测、保存/加载模型）。  
  - `MCTS.py`：实现蒙特卡洛树搜索（AlphaZero 决策核心）。  
  - `Coach.py`：实现自对弈训练主循环（采样、训练、模型对比与更新）。  
  - `Arena.py`：让两个玩家/策略对战，用于评估模型强弱。  
  - `main.py`：训练入口；组装 `Game`、`NNet` 和训练参数后启动 `Coach`。  
  - `pit.py`：对弈入口（人机或模型互搏）。  

- 各游戏目录（如 `othello/`、`connect4/`、`tictactoe/`、`gobang/`、`tafl/`、`rts/`、`dotsandboxes/`）  
  每个目录通常包含：  
  - `*Game.py`：该游戏对 `Game` 接口的具体实现；  
  - `keras/`、`pytorch/`（视游戏而定）：该游戏的网络结构与 `NNetWrapper`；  
  - `README.md`：该游戏的单独说明与实验信息。  

- 预训练与环境  
  - `pretrained_models/`：示例/预训练模型。  
  - `requirements.txt`：Python 依赖（含 TensorFlow/Keras 与 PyTorch）。  
  - `docker/`、`setup_env.sh`：容器化运行环境支持。  

关键技术栈与组织方式：

- 算法：AlphaZero 风格“自对弈 + MCTS + 神经网络策略价值联合学习”。  
- 框架：同一套算法抽象支持多游戏、多深度学习后端（Keras / PyTorch）。  
- 设计方式：通过 `Game` 与 `NeuralNet` 抽象解耦“通用训练逻辑”和“具体游戏/模型实现”，因此扩展新游戏时通常只需补齐对应接口实现。  

### Docker Installation
For easy environment setup, we can use [nvidia-docker](https://github.com/NVIDIA/nvidia-docker). Once you have nvidia-docker set up, we can then simply run:
```
./setup_env.sh
```
to set up a (default: pyTorch) Jupyter docker container. We can now open a new terminal and enter:
```
docker exec -ti pytorch_notebook python main.py
```

### Experiments
We trained a PyTorch model for 6x6 Othello (~80 iterations, 100 episodes per iteration and 25 MCTS simulations per turn). This took about 3 days on an NVIDIA Tesla K80. The pretrained model (PyTorch) can be found in ```pretrained_models/othello/pytorch/```. You can play a game against it using ```pit.py```. Below is the performance of the model against a random and a greedy baseline with the number of iterations.
![alt tag](https://github.com/suragnair/alpha-zero-general/raw/master/pretrained_models/6x6.png)

A concise description of our algorithm can be found [here](https://github.com/suragnair/alpha-zero-general/raw/master/pretrained_models/writeup.pdf).

### Citation

If you found this work useful, feel free to cite it as

```
@misc{thakoor2016learning,
  title={Learning to play othello without human knowledge},
  author={Thakoor, Shantanu and Nair, Surag and Jhunjhunwala, Megha},
  year={2016},
  publisher={Stanford University, Final Project Report}
}
```

### Contributing
While the current code is fairly functional, we could benefit from the following contributions:
* Game logic files for more games that follow the specifications in ```Game.py```, along with their neural networks
* Neural networks in other frameworks
* Pre-trained models for different game configurations
* An asynchronous version of the code- parallel processes for self-play, neural net training and model comparison. 
* Asynchronous MCTS as described in the paper

Some extensions have been implented [here](https://github.com/kevaday/alphazero-general).

### Contributors and Credits
* [Shantanu Thakoor](https://github.com/ShantanuThakoor) and [Megha Jhunjhunwala](https://github.com/jjw-megha) helped with core design and implementation.
* [Shantanu Kumar](https://github.com/SourKream) contributed TensorFlow and Keras models for Othello.
* [Evgeny Tyurin](https://github.com/evg-tyurin) contributed rules and a trained model for TicTacToe.
* [MBoss](https://github.com/1424667164) contributed rules and a model for GoBang.
* [Jernej Habjan](https://github.com/JernejHabjan) contributed RTS game.
* [Adam Lawson](https://github.com/goshawk22) contributed rules and a trained model for 3D TicTacToe.
* [Carlos Aguayo](https://github.com/carlos-aguayo) contributed rules and a trained model for Dots and Boxes along with a [JavaScript implementation](https://github.com/carlos-aguayo/carlos-aguayo.github.io/tree/master/alphazero).
* [Robert Ronan](https://github.com/rlronan) contributed rules for Santorini.
* [Plamen Totev](https://github.com/plamentotev) contributed Go Text Protocol player for Othello.

Note: Chainer and TensorFlow v1 versions have been removed but can be found prior to commit [2ad461c](https://github.com/suragnair/alpha-zero-general/tree/2ad461c393ecf446e76f6694b613e394b8eb652f).
