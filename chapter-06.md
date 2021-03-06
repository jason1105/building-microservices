## 第六章 部署

### 持续集成简介

CI（Continuous Integration）

构建CI的必要条件
- 每天签入代码
- 使用测试验证修改
- 构建失败后，第一时间修改CI

### 把持续集成映射到微服务
微服务中，每个微服务都要有自己的CI。

CI（Continuous Integration）

保证新的代码能够和现有的代码集成。

CI提供代码验证、代码编译、代码测试、打包生成构建物

同步发布（lock-step release) 中，要一次性部署多个服务。
- 使用统一代码库+单块构建：当仅有一个团队在所有的服务上工作的时候，一个单块构建可以满足所有要求。随着项目扩大，一个简单的修改导致所有服务重新构建。
- 使用统一代码库+微服务独立构建：效果更好，但单个代码库可能导致其中的微服务之间的相互耦合。
- 微服务独立代码库+微服务独立构建：效果最好

### 构建流水线和持续交付
CD（Continuous Delivery）

构建流水线：编译及快速测试 → 耗时测试 → 用户验收测试 → 性能测试 → 生产环境

CI和CD的工具是不同的。
CD提供的工具可以对手动部分进行建模。

### 不可避免的意外
项目初期阶段可以使用单块构建，当服务稳定了之后再将构建拆开。

### 平台特定的构建物
Java的WAR、JAR、Ruby的gem、Python的egg、Node.js的NPM等。需要Apache或Nginx的可以使用Puppet、Ansible和Chef等自动化配置管理工具。

### 操作系统构建物
使用 操作系统的软件包管理工具进行构建。
例如：RetHat、CentOS的RPM、Ubuntu的deb、Windows的MSI

### 定制化镜像
Puppet、Ansible和Chef这些工具虽然可以自动运行脚本，但仍需要一遍遍的安装许多软件，有没有更好的办法呢，例如虚拟机镜像。

镜像种类：VMWare、AWS AMI、Vagrant、Rackspace。

Packer（http://www.apcker.io）可以屏蔽镜像的种类，通过Puppet、Ansible和Chef，可以从同一套配置中生成不同种类的镜像。从而可以在开发的时候使用Vagrant，生产环境使用AWS。
1. 将镜像作为构建物
2. 不可变服务器：配置文件保存在版本控制中，通过配置文件生成相应服务，我们可以自动化重建服务，但如果有人登陆到服务中修改了内容，就会导致服务与配置文件不同， 这叫“配置漂移”。所以任何修改都要经过构建流水线来实现。注意：持久化数据也应该保存在其他地方。

### 环境
CD流水线中包含不同的环境：耗时测试、UAT、性能测试、最终生产环境。考虑代码在这些环境上是否能够运行。

当需要为每个微服务考虑多个环境时，事情会更复杂，后面会讨论。

### 服务配置
服务在不同环境中的配置，应当尽量保持差异最小化，如果配置差异很大，有可能某些问题会被限定在某个配置当中。
1. 针对不同的配置产生不同的构建物
2. 只创建一个构建物，配置单独管理

### 服务与主机之间的映射
约定：使用“主机”作为通用的隔离单元

1. 单主机多服务模型：基础设施管理团队的工作量与主机成正比，与服务数量无关。缺点：监控困难，监控服务还是监控机器？服务之间可能产生影响。主机宕机造成的影响很大；服务部署复杂，需要处理服务之间的关系；不利于团队自治；限制了部署构建物的选择，镜像部署是不可能用的了，除非将所有构建物打包进一个构建物中，这是微服务竭力想要避免的。
1. 应用程序容器：例如IIS和Servlet。缺点：限制技术栈，也限制自动化和管理技术的选择。可以使用自包含的微服务构建物，例如：.NET的Nancy，Java的Jetty嵌入式容器（是Dropwizard技术栈的核心）
1. 每个主机一个服务：可以减小微服务的复杂性，但主机数量的增加是一个问题。
1. 平台即服务：Heroku是一个黄金级别的PAAS，不仅能够管理服务的运行，还能以非常简单的方式提供数据库等服务。

### 自动化
针对大量服务器，实现工作自动化：
- 主机控制
- 自动部署服务
- 监控服务
- 收集日志
- 数据库变更

原则：
- 一个微服务一台主机（主机基于物理机或者虚拟机）
- 开发环境的工具链应当和部署环境的工具链完全相同。

#### 关于自动化案例
REA和Gilt

### 从物理机到虚拟机
管理大量主机的关键：将物理机划分为小块。

1. 传统虚拟化技术：下图的标准虚拟化（也叫类型2虚拟化）与基于容器的虚拟化的对比。
可以看到传统虚拟化中hypervisor是集中管理层。它占用了一定的资源。
![](http://a4.qpic.cn/psb?/V120cGPg1BkF5W/pqxfV1wqBHNclkcwM1K5qazKHNaq2eSXAreqyNfYHSo!/c/dDcBAAAAAAAA&ek=1&kp=1&pt=0&bo=oAU4BAAAAAABAL4!&tl=3&vuin=13366824&tm=1536627600&sce=60-2-2&rf=0-0)
2. Vagrant：使用文本文件定义虚拟机，通过该文本文件与团队其他成员分享虚拟机。常用语开发和测试环境。
3. Linux容器：

    分类：
    - Solaris 的 Zones、OpenVZ
    - LXC
    
    特点：
    - 容器是系统进程树的子树
    - 不需要hypervisor
    - 共享内核
    - 启动速度快
    - 轻量，资源利用率更高
    - 容器可与虚拟机一同工作
    
    缺点：
    - 容器中的进程可能与其他容器中、或主机进程发生干扰
    - 需要配置IPTable进行端口映射

4. Docker

- 构建在轻量级容器之上
- 有Registry
- 大量周边技术：CoreOS、Kubernetes、Deis

### 部署一个接口

使用下面参数进行参数化的命令行调用：
- 实体（微服务的名字）
- 版本
- 环境

部署的例子：
  
        // 开发人员本地部署
        $deploy artifact=catalog environment=local version=local

        // CI自动部署  
        `$deploy artifact=catalog environment=ci version=b456`

        // QA手动部署用于测试  
        `$deploy artifact=catalog environment=integrated_qa version=b456`


> 一些工具  
Fabric：Python工具，将命令行映射到函数，结合 Boto（AWS）可以自动化大型AWS环境。  
Capistrano + Boto（AWS）    
PowerShell  + Boto（AWS）  
Hashicorp的Terraform

#### 环境定义
在服务的yaml中定义环境，例如：
* 实例大小
* 凭证（Credential）
* 服务节点数量

#### 微服务自己的信息

    catalog-service:    
        puppet_manifest: catalog.pp
        connectivity:
            - protocol: tcp
              ports: [8080, 8081]
              allowed: [WORLD]

puppet是一个系统配置工具。

### 小结

原则

* 每个微服务能够独立于其他服务进行部署
* 每个微服务使用独立的代码库，和独立的CI
* 每个微服务使用单独的容器
* 自动化一切

推荐一本书：《持续交付》， Jez Humble, Devid Farley
