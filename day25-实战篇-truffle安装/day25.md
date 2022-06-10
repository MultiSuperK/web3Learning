# Truffle开发框架

------

## 工欲善其事，必先利其器

truffle是一个帮助我们快速搭建项目以及辅助开发的武器库

特性包括如下

- 内置合约编译、链接、部署管理功能。
- 对合约可以进行自动化测试。
- 同时支持公有链、私有链以及测试链，轻松调整测试环境。
- 完全基于JavaScript编写的项目和API，易于上手。
- 基于NPM包管理器管理项目，便于添加其他Javascript依赖。
- 命令行集成度高，方便放置在服务器上做持续集成

------

### truffle安装

------

1. ```bash
    node --version  # >=8.3.0
    npm install -g truffle
    npm install -g ganache-cli
    ```

### 初始化项目

```bash
$ cd yourLikePath
$ mkdir truffle-project
$ cd truffle-project
$ truffle unbox metacoin  这一步卡住了，直接去git搜，
$ 或者 truffle init
```

### 测试项目
truffle test
### 部署项目
truffle migrate
### 编译项目
truffle compile