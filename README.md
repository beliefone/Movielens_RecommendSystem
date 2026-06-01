# MovieLens 1M 协同过滤推荐系统实验

本项目基于 **MovieLens 1M** 评分数据集，实现并对比三种经典 TopN 推荐算法：

- **UserCF**：基于用户的协同过滤
- **User-IIF**：加入热门物品惩罚的 UserCF 改进版本
- **ItemCF**：基于物品的协同过滤

项目重点是比较不同协同过滤方法在 **Precision、Recall、Coverage、Popularity** 四个指标上的表现。

---

## 实验结果

| 算法 | Precision | Recall | Coverage | Popularity |
|---|---:|---:|---:|---:|
| **ItemCF** | **0.3792** | 0.0764 | 0.1729 | 7.1730 |
| **User-IIF** | 0.3529 | **0.1422** | 0.2616 | 6.9818 |
| **UserCF** | 0.3766 | 0.0759 | **0.3175** | **6.9195** |

---

## 结果结论

从当前实验结果看：

- **ItemCF 的 Precision 最高**，推荐得更准，但更偏热门电影。
- **User-IIF 的 Recall 最高**，能找回更多用户在测试集中真实看过的电影。
- **UserCF 的 Coverage 最高**，推荐结果覆盖的电影种类更多。
- **User-IIF 整体更均衡**，在召回能力、覆盖率和热门程度之间取得了比较好的平衡。

如果只看单项指标：

| 关注目标 | 推荐算法 | 原因 |
|---|---|---|
| 推荐更准 | **ItemCF** | Precision 最高 |
| 找回更多用户可能感兴趣的电影 | **User-IIF** | Recall 最高 |
| 推荐结果更多样 | **UserCF** | Coverage 最高 |
| 不想过度推荐热门电影 | **UserCF / User-IIF** | Popularity 更低 |

---

## 项目结构

建议项目结构如下：

```text
.
├── itemcf.py          # ItemCF 推荐算法
├── user_iif.py        # User-IIF 推荐算法
├── usercf.py          # UserCF 推荐算法
└── ml-1m/
    └── ratings.dat    # MovieLens 1M 评分数据
```

数据集下载地址如下：

```
https://grouplens.org/datasets/movielens/1m/
```

代码默认读取的数据路径为：

```python
ratingfile = os.path.join('ml-1m', 'ratings.dat')
```

运行前需要确保 `ratings.dat` 放在 `ml-1m` 文件夹下。

---

## 运行环境

本项目只依赖 Python 标准库，不需要额外安装第三方库。

推荐环境：

```text
Python 3.8+
```

---

## 运行方式

分别运行三个文件即可得到对应算法的评估结果。

```bash
python itemcf.py
python user_iif.py
python usercf.py
```

程序运行过程中会输出数据加载、相似度矩阵构建、推荐进度以及最终评估指标。

---

## 算法简介

### UserCF

UserCF 根据“相似用户喜欢什么”来推荐电影。

简单来说：

> 如果两个用户看过很多相同电影，那么他们可能兴趣相似。  
> 可以把相似用户看过、当前用户没看过的电影推荐给当前用户。

当前参数：

```python
n_sim_user = 20
n_rec_movie = 10
```

---

### User-IIF

User-IIF 是 UserCF 的改进版本。

它认为热门电影不能过度影响用户相似度。因为很多人都看过热门电影，这不一定说明他们兴趣相同，只能说明热门电影真的很热门，人类又一次被流量支配了。

因此 User-IIF 会降低热门电影在相似度计算中的权重，让冷门共同兴趣更有区分度。

当前参数：

```python
n_sim_user = 80
n_rec_movie = 10
```

---

### ItemCF

ItemCF 根据“相似电影”来推荐。

简单来说：

> 如果很多用户同时喜欢电影 A 和电影 B，那么这两部电影可能相似。  
> 用户喜欢电影 A，就可以继续推荐与 A 相似的电影。

当前参数：

```python
n_sim_movie = 20
n_rec_movie = 10
```

---

## 评估指标说明

| 指标 | 含义 | 越高代表 |
|---|---|---|
| **Precision** | 推荐列表中命中测试集的比例 | 推荐更准确 |
| **Recall** | 测试集中被成功推荐出来的比例 | 找回能力更强 |
| **Coverage** | 推荐结果覆盖的电影比例 | 推荐更多样 |
| **Popularity** | 推荐电影的平均热门程度 | 越高越偏热门 |

需要注意：**Popularity 不是越高越好**。它主要用来观察推荐结果是否过度集中在热门电影上。

---
