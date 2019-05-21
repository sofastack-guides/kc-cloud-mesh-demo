# 部署 BookInfo 应用

### 进入应用列表页面

在左边的菜单中找到 "应用发布"，点击进去：

![](images/bookinfo/menu.png)

在应用服务列表中，注意先通过图中的 namespace 下拉菜单选择所在的 namespace，在本次 demo 中，每个用户都有自己独有的命令空间，名字后缀的编号和用户名编号一致，如 user21 对应 namespace21：

![](images/bookinfo/select_namespace.png)

第一次进入时列表为空。

### 部署 productpage 应用服务

点击"创建"按钮，开始创建 bookinfo 中的 productpage 应用。第一步，填写基本信息：

![](images/bookinfo/productpage-1.png)

请注意：

- 命名空间默认已经选择为 namespace+用户编号，请注意核对，不要修改为其他
- 应用服务名称请填写 "productpage" + 用户编号，如图中的 "productpage21"
- 所属应用请选择 bookinfo
- 负责人随意
- 副本个数：请设置为1，demo中除了 reviews 应用外其他应用都只需要一个副本

第二步，Pod 模版配置：

![](images/bookinfo/productpage-2.png)

- 容器名称默认为 productpage + 编号，注意这个容器名称是在整个workspace内要求唯一，而不仅仅是当前 namespace 之内，因此编号是必须的
- 镜像我们已经准备好，地址为：reg-cnsh.antcloud.alipay.net/middleware/examples-bookinfo-productpage-v1:1.10.1
- CPU配置请注意修改默认配置，默认的请求核数为 1 core，建议修改为 100 millicore
- 内容配置请注意修改默认配置，默认的请求内存为 1 G，建议修改为 256M
- 高级配置可以不用配置，默认即可

第三步，创建访问配置：

![](images/bookinfo/productpage-3.png)

点击"添加服务"，productpage比较特殊，需要添加两个服务，先添加 productpage 服务作为集群内访问使用：

![](images/bookinfo/productpage-4.png)

- 服务名称：特别注意这里要填写为 productpage ，不要后缀编号！不要后缀编号！不要后缀编号！
- 访问方式：集群内访问
- 端口映射：添加一个，如图所示，协议为TCP，访问端口和容器端口都是 9080 

再添加 productpage-public 服务作为公网访问使用：

![](images/bookinfo/productpage-6.png)

- 服务名称：请固定使用 productpage-public 
- 访问方式：选公网访问
- 选择负载均衡：这里我们已经创建好了 SLB，请在下拉菜单中选择对应变化的 SLB，如图中的 slb21
- 端口映射需要添加一个，如下图所示

![](images/bookinfo/productpage-5.png)

- 协议：注意协议一定要选择HTTP
- 转发规则：默认按权重
- 前端端口：19080，这是公网访问时的端口
- 后端端口：9080，这是前面 productpage 的端口

访问配置设置好的样子：

![](images/bookinfo/productpage-7.png)

下一步，部署和调度配置：

![](images/bookinfo/productpage-8.png)

注意开启 "服务网格引流配置"，第一次部署时，直接100%引流到目标服务即可。

在预览界面，确认无误，点击提交。在应用服务创建成功的提示下，选择 "立即发布"，此时会创建发布单：

![](images/bookinfo/productpage-9.png)

点整体发布，进入"发布中" 状态之后，可以点图中的应用服务，进入查看详细的发布情况：

![](images/bookinfo/productpage-10.png)

如果遇到报错：“Task CREATE-[INTERNET_LOAD_BALANCER_SERVICE-productpage-public] failed as no load balancer status of service namespace21/productpage-public”，请重试。

如果遇到报错，"后置任务"中的"发布日志配置"失败，请点击忽略即可。

此时再进入应用服务列表页面，就能看到刚发布成功的 productpage 应用：

![](images/bookinfo/productpage-11.png)

### 部署 details 应用服务

- 应用服务名：details21
- 镜像地址：reg-cnsh.antcloud.alipay.net/middleware/examples-bookinfo-details-v1:1.10.1
- 添加服务时，只需要添加一个 detail 服务即可，切记 服务名称是 details， 不带编号。

![](images/bookinfo/details-1.png)

### 部署 ratings 应用服务

- 应用服务名：ratings
- 镜像地址：reg-cnsh.antcloud.alipay.net/middleware/examples-bookinfo-ratings-v1:1.10.1
- 添加服务时，只需要添加一个 ratings 服务即可，切记 服务名称是 ratings， 不带编号。

 ![](images/bookinfo/ratings-1.png)

### 部署 reviews 应用服务

注意 reviews 的副本数设置为 3.

![](images/bookinfo/reviews-1.png)

镜像地址：reg-cnsh.antcloud.alipay.net/middleware/examples-bookinfo-reviews-v1:1.10.1

添加服务时，只需要添加一个 reviews 服务即可，切记 服务名称是 reviews， 不带编号。

![](images/bookinfo/reviews-2.png)

注意因为有 3 个副本，如果选择 beta 分组，在发布过程中可能会提示 "beta 确认" ，点击确认后会继续发布。

![](images/bookinfo/reviews-3.png)

### BookInfo部署完成

发布完成之后，bookinfo 的四个应用如下图所示：

![](images/bookinfo/all-services.png)

