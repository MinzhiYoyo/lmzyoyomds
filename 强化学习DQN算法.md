---
title: 强化学习DQN算法
id: 96
date: 2024-12-03 17:09:58
auther: yutanbird
cover: 
excerpt: 无关紧要的摘要
permalink: /archives/rldqn
categories:
 - 人工智能
tags: 
 - dqn
 - qlearning
 - gym
---

# 前言

&emsp;本文是在做完人工智能大作业而写的，部分代码（未完成）可以参考Github。

- 挖金币游戏：[https://github.com/MinzhiYoyo/AI_course_dqn_miningcoin](https://github.com/MinzhiYoyo/AI_course_dqn_miningcoin)
- TSP问题：[https://github.com/MinzhiYoyo/AICourseWord_TSP](https://github.com/MinzhiYoyo/AICourseWord_TSP)
- TSP变种问题：[https://github.com/MinzhiYoyo/AICourseWorkEscapeGame](https://github.com/MinzhiYoyo/AICourseWorkEscapeGame)
- 应用GA解决TSP问题，与本文无关：[https://github.com/MinzhiYoyo/TSPGAsolution](https://github.com/MinzhiYoyo/TSPGAsolution)

# Q Learning

## 算法介绍

&emsp;Q Learning是强化学习的一种方法，设想有一个智能体Agent，在一个环境Environment中行动Action，每次行动后都会更新Agent的状态State。具体到某一步来说，当Agent处于State（s）的时候，应该采取什么样的Action（a）来获得最大的奖励（Rewards，r）呢？答案是，根据Q值来，那么问题来了，这个Q值应该如何获得呢？答案是，Q值存储于一张表格中，也就是Q-table，行表示状态S，列表示动作a，单元格表示在状态S采取动作a能获得的Rewards，也就是Q值，因此Q Learning适用于状态数量确定的，即为$S\times a$种状态，Agent训练的过程便是完善这Q-table的过程。

&emsp;如何来更新这个Q-table呢？首先，初始化这个Q-table（可以全部为0，也可以是其他的），然后按照下面的公式进行迭代，迭代次数可以根据收敛情况来定。
$$
Q_{new}(S_t, a) \leftarrow Q_{old}(S_t, a) + \alpha (r_{t} + \gamma \cdot \max\limits_a Q(S_{t+1}, a) - Q_{old}(S_t, a))
$$
其中，改式子表示Agent在状态$S_t$采取了动作$a$，然后其状态变成了$S_{t+1}$，获得了$r_t$的奖励（Rewards）。

- $Q_{new}(S_t, a)$：表示在这次迭代中更新的Q值，填入到Q-table中的$(S_t, a)$单元格中。

- $Q_{old}(S_t, a)$：表示当前Q-table中已有的Q值。

- $\max\limits_a Q(S_{t+1}, a)$：表示当前Q-table中已有的Q值，为状态$S_{t+1}$对应行所可能采取的所有动作中的最大值。

- $r_t$：表示在状态$S_t$采取动作a的形况下获取的Rewards，从Environment中获取。

- $\alpha$：表示学习率$(0<\alpha \leq 1)$，其越大，说明Agent越注重Environment反馈的收益，否则越注重已有的记忆。其一般设置很小，比如0.01左右。

- $\gamma$：表示衰减系数$(0\leq \gamma \leq 1)$，其越大，Agent越注重未来收益，其越小，Agent越注重眼前收益。以下说明该结论：

    > 不妨简单地把式子看成：$Q(S_t) = r_t + \gamma\cdot Q(S_{t+1})$，其中$Q(S_t)$表示在状态$S_t$能取得的最大收益，同理$Q(S_{t+1})$表示在状态$S_{t+1}$能取得的最大收益。
    >
    > 对这个式子进行展开：$Q(S_t)=r_t+\gamma\cdot Q(S_{t+1})=r_t+\gamma(r_{t+1}+\gamma\cdot Q(S_{t+2}))=r_t+\gamma\cdot r_{t+1}+\gamma^2\cdot r_{t+2}+\gamma^3\cdot r_{t+3}\cdots$
    >
    > 由于$0\leq \gamma \leq 1$，可以看出，$\gamma$越小，随着次数增加，后续的$r_{t+n}$对当前的$Q(S_t)$影响越来越小。
    
- $\epsilon$：表示贪婪度greedy，并没有出现在这个公式中，但其也是实现该算法的一个超参数。当我们在训练中，会选择一个动作。选择这个动作有两种情况，假设当$\epsilon = 0.9$时，有10%的概率随机选一个动作，有90%的概率选择$\max\limits_a Q(S_{t+1}, a)$中的动作a。

## Python 伪代码

```python
# 超参数
gamma = 0.9   # 长期收益率 
alpha = 0.01  # 学习率
epsilon = 0.9 # 贪婪率

# 第一步，初始化Q-table，行为State_size，列为Action_size
q_table = init_table(State_size, Action_size)  # 初始化，一般为0

# 第二步，训练times次
times = 1000
for i in range(times):
    state = env.reset()  # 环境初始化，并获取初始状态
    while True:
        # 第三步，选择动作
        action = choose_action(state)
        # 第四步，获取下一步的状态
        next_state, rewards, done = env.step(action) # next_state表示下一步状态，rewards表示当前动作的奖励，done表示游戏是否结束
        
        # 第五步，进行学习
        learning(state, rewards, action, next_state)
        
        # 第六步，更新状态
        state = next_state
        
        # 判断游戏是否结束
        if done:
            break
        
# 关键函数，选择动作
def choose_action(state):
    p = random.random(0, 1)  # 随机数
    if p < epsilon:  # 贪婪选择
        return max(q_table[state][:])  # 选择最大的值
    else:  # 随机选择
        return action_set.random()  # 随机选择action集合中的一个

# 关键函数，训练
def learning(state, rewards, action, next_state):
    old_q = q_table[state][action]
    max_q_next =  max(q_table[next_state][:])
    q_table[state][action] = old_q + alpha * ( rewards + gamma * max_q_next - old_q )
```

# DQN

## 算法介绍

&emsp;Q Learning适用于状态数足够小的，如果状态数足够大，那么维护一个Q-table以及训练一个Q-table在实现上就变得不现实，那么有没有一种方式来计算Q值呢？答案便是引入神经网络的方式来计算Q值，DQN全称是：Deep Q Netword。其他与Q Leanring不变，变得地方就是Q值的获取。

&emsp;除此之外，DQN引入了经验回放，也就是可以保存一些玩游戏的经验（可以是人玩的，可以是AI自己玩的）。DQN网络计算Q值，会用这些经验回放作为样本来估计，并且DQN引入了两个网络，两个网络结构完全一样，但是更新频率不一样。一个网络实时更新，即每次训练都会更新，称为eval_net。另一个网络则间断更新，称为target_net。也就是说，target_net拥有很久以前的eval_net网络的参数。

***ps：***这里介绍的DQN实际上应该是Nature DQN，而不是原本的DQN，原本的DQN只有一个target_net网络。

## Python 实现伪代码

### 定义经验回放

```python
class ReplayMemory:
    """
    经验回放应该用队列表示，并且需要包含随机取一个batch size大小的功能
    """
    def __init__(self, capacity):
        self.buffer = []
        self.capacity = capacity
        self.position = 0
   	def push(self, state, action, rewards, next_state):
        if len(self.buffer) < self.capacity:
            self.buffer.append((state, action, rewards, next_state))
        self.position = (self.position + 1) % self.capacity
        self.buffer[self.position] = (state, action, rewards, next_state)
    def sample(self, batch_size):
        return random.sample(self.buffer, batch_size)
   	def save(path):
        pass
   	def load(path):
        pass
```

### 定义网络结构

```python
class DQNet(nn.Module):
    """
    这里定义的是四层全连接层，具体情况要根据具体情况定义合适的神经网络
    """
    def __init__(self, input_size, output_size):
        super(DQNet, self).__init__()
        self.fc1 = nn.Linear(input_size, 256)
        self.fc2 = nn.Linear(256, 128)
        self.fc3 = nn.Linear(128, 64)
        self.fc4 = nn.Linear(64, output_size)
	def forward(self, x):
        x = f.relu(self.fc1(x))
        x = f.relu(self.fc2(x))
        x = f.relu(self.fc3(x))
        x = self.fc4(x)
        return x
```

### 定义Agent

```python
class DQNAgent:
    def __init__(state_size, action_size,
                 alpha = 0.01, # 学习率
                 gamma = 0.9, # 长期收益率
                 epsilon = 0.9, # 贪婪率
                 tau = 0.1, # 目标网络更新率
                ):
        # 定义两个网络，eval和target网络
        self.eval_net = DQNet(state_size, action_size)
        self.target_net = DQNet(state_size, action_size)
        self.target_net.load_state_dict(self.eval_net.state_dict())
       	
        # 定义优化器和损失函数
        self.optimizer = optim.Adam(self.eval_net.parameters(), lr=self.alpha)
        self.loss_f = nn.MSELoss()
        
        # 定义经验回放
        self.memory = ReplayMemory(10000)
        
	def choose_action(self, state):
        p = random.random()
        if p < self.epsilon:
            return self.eval_net(state).max(0).indices
        else:
            return random.choise(action_set)
        
    def train(self, batch_size):
        if len(self.memory) < batch_size:
            return
        state, action, rewards, next_state = self.memory.sample(batch_size)  # 随机采样
        state_batch = torch.cat(state)  # 转成batch
        action_batch = torch.cat(action) # 转成batch
        rewards_batch = torch.cat(rewards)
        next_state_batch = torch.cat(next_state)
        
        # 计算估计Q值，q_eval
        q_eval = self.eval_net(state_batch).gather(1, action_batch)
        
        # 计算目标Q值，q_target
        q_next = self.target_net(next_state_batch).detach()
        q_target = rewards_batch + self.gamma * q_next.max(1)[0].view(batch_size, 1)
        
        # 计算损失
        self.loss = self.loss_f(q_eval, q_target)
        
        # 优化模型
        self.optimizer.zero_grad()
        self.loss.backward()
        self.optimizer.step()
        
        # 更新目标网络，有间隔的
        if interval:
            target_net_dict = self.target_net.state_dict()
            eval_net_dict = self.eval_net.state_dict()
            for k in target_net_dict.keys():
                target_net_dict[k] = eval_net_dict[k] * self.tau + target_net_dict[k] * (1 - self.tau)
            self.target_net.load_state_dict(target_net_dict)
```

### 训练过程

```python
times = 1000 # 训练次数
for i in range(times):
    state = env.reset()  # 初始化游戏环境
    while True:
        action = agent.choose_action(state)  # 选择动作
        next_state, rewards, done, info = env.step(action)
        
        # 保存记忆
        agent.memory.push(state, action, rewards, next_state)
        
        # 每隔一定间隔才训练网络
        if interval:
            agent.train(batch_size)
        
        # 更新状态
        state = next_state
```



# Double DQN

&emsp;为了解决DQN存在的问题，即过高的Q值估计，引入了Double DQN的算法，即两个Q值。注意区分DQN中的q_eval和q_target这两个Q值，且看下面介绍。下面这个式子是DQN的q_target计算部分。
$$
q_{target} = r_t + \gamma \cdot \max\limits_a N_{target}(S, a)
$$
而Double DQN与之不同点在于q_target计算部分而已，下面是Doubl DQN的q_target计算部分
$$
a^* = \arg\max\limits_a N_{eval}(S, a)\\
Q_{target}(S_t, a) = r_t + \gamma\cdot N_{target}(S, a^*)
$$
本来默认是选择target_net网络下输入状态S算出的最大值，这事DQN的方法。Double DQN是在eval_net网络下输入状态S算出的最大值对应的动作$a^*$，然后把这个动作以及状态S代入target_net网络下得到q_target。

# **Dueling** DQN

&emsp;这也是DQN的一种优化。

# GYM

&emsp;这事Open AI的一个Python库，点击[官网](https://www.gymlibrary.dev/index.html)可查看详情，旨在搭建一般的游戏环境作为AI的训练环境。点击查看[有关状态集合和动作集合的基础变量](https://www.gymlibrary.dev/api/spaces/)，其封装了一些数据结构类型。点击查看[GYM的基础用法](https://www.gymlibrary.dev/api/core/)，即其核心功能，自主实现的游戏环境需要继承其并且实现起码两个函数`step(action)->state`以及`reset()->state`