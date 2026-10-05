# Synaptic — Linux 二进制（CI 构建）

代码知识图谱工具：`extract` 建图，`query` / `references` / `affected` / `hazards` / `explain` / `path` 查询。

## 资产选择（先跑 `uname -m`）

| 资产后缀 | 架构 | 链接方式 | 适用 |
|---|---|---|---|
| `-x86_64-unknown-linux-gnu` | x86-64 | 动态（glibc） | 通用 x86_64 Linux / 容器 |
| `-aarch64-unknown-linux-musl` | aarch64 | **静态** | aarch64 通用（musl 与 glibc 环境都能跑） |
| `-aarch64-unknown-linux-gnu` | aarch64 | 动态（glibc） | aarch64 + glibc |

## 用法

```bash
chmod +x synaptic-*
./synaptic-*-x86_64-unknown-linux-gnu --version    # → synaptic <版本>

# 建图 —— ⚠ 在仓库【副本】里跑
git clone <repo-url> /tmp/graph-work && cd /tmp/graph-work
/path/to/synaptic extract --no-resources
# 产出 synaptic-out/{graph.json, GRAPH_REPORT.md, graph.html, tree.html, ...}

# 查询（示例）
synaptic references <符号>    # 全部引用：调用 + import + 继承 + 实现 + 类型使用
synaptic affected <节点>      # 反向影响面（谁依赖它）
synaptic hazards              # 反射 / 动态派发点（提醒：0 dependents ≠ 可安全改动）
synaptic explain <节点>
synaptic query '<关键词>'
```

## ⚠ 建图请用副本，不要在仓库工作树里跑

`extract` 会把输出写进**当前目录**的 `synaptic-out/`。直接在原仓库里跑会**污染它**（生成大量未跟踪文件，有被误提交的风险）。

推荐：`git clone` 到临时目录，或用 `git worktree add /tmp/graph-work`。

## 构建

由 `agent-devkit` CI workflow `build-synaptic` 交叉编译（上游 `synaptic-graph/synaptic` @ master）。
二进制不随仓库跟踪，只在此处以资产形式分发。
