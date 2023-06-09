# 使用 Terraform 进行多区域部署

> 原文：<https://medium.com/capital-one-tech/multi-region-deployments-with-terraform-kubernetes-a1f51bb96974?source=collection_archive---------0----------------------->

![](img/450141292d4d9c4245425a878f9c216c.png)

# 概观

在我的上一篇文章[用 Terraform](/capital-one-developers/deploying-multiple-environments-with-terraform-kubernetes-7b7f389e622) 部署多个环境中，我描述了如何在一个项目中使用 Terraform 部署多个环境。从那以后，新的需求被分配给我的项目，我的团队需要实现多区域部署。跨区域与在国家或世界的多个地方拥有数据中心是同一个概念；你在保护自己免受灾难。

这对于团队来说并不意外，因为任何想要构建弹性应用程序和实现灾难恢复的人都应该在不止一个地区进行构建。此外，多区域的额外好处是能够进行全局负载平衡，这意味着如果您在 us-east-1 和 us-west-2 中运行，那么用户将访问地理上离他们最近的应用程序实例。

通过一些设计工作，我们意识到多区域部署可以很容易地实现，因为我们已经使代码可扩展并保持配置独立。事实证明，我们甚至可以在不引入重大变更的情况下实现多区域！

# **延伸设计**

首先添加的是一个新的公开输入，名为 region。以前，这种输入不向最终用户公开，而是硬编码在配置中。这个输入将指定 Kubernetes 集群将被部署到哪个 AWS 区域。我们没有抽象出实际的地区名称，而只是接受简单的值，比如 east 或 west，而是选择用字面上的地区名称作为值；美国东部 1 号或美国西部 2 号。这是一个引起了相当多争论的设计决策，但最终归结为用户确切地知道集群构建在哪里。

更大的变化是可变配置。最初所有的变量都是映射，其中每个键都是环境名；开发、质量保证等。这对于单区域部署很有效，但是对于多区域部署，许多值因区域而异。简单的解决方法是扩展已知工作区的列表，但是这不是一个可伸缩的选项。相反，决定将基于区域具有不同值的所有地图转换为嵌套地图。这意味着区域名称是顶层，在它下面是具有各自值的不同环境。

随着变量结构的改变，还必须改变变量模块的输入，以及引用嵌套映射的代码中的任何变量查找。前者很简单，向模块添加一个区域输入，并向其传递区域变量。尽管后者更深入。不仅要花一些时间来找到代码中需要修改的所有查找，而且要找出如何正确地进行查找也需要一些反复试验。

# 保持向后兼容

以前版本的代码不支持多区域部署，它们只知道默认的 east 区域，并为现在已经过时的变量设计配置了查找。这里的一切都表明这是代码库的一个突破性变化。幸运的是，由于配置与代码完全分离，我们总是配置区域变量，只是没有公开，我们能够保持这种变化向后兼容。

这里的实现将 region 变量默认设置为 us-east-1，保持所有以前版本的代码都可以工作。当我们向用户公开区域变量时，该值现在可以被覆盖，从而允许在配置中找到和使用新的可用的特定于区域的参数。

# 代码中的更改

尽管这与其说是代码变更，不如说是配置变更，但我认为展示一些之前和之后的示例仍然是有价值的。下面是一些代码片段，展示了一些重构的样子:

## *前多区域支持变量:*

```
variable "worker_elb_sg_map" {
  description = "A map from environment to a comma-delimited list of build worker ELB security groups"
  type = "map"default = {
  dev = "sg-9f59278yreuhifbf,sg-be2t43erfce,sg-434fedf2b"
  qa = "sg-e945ygrthdrg,sg-e55tgr54hd,sg-7d34trfwe7"
  staging = "sg-255yg45hedr5,sg-6234tth6,sg-9834tfery4e5t"
  training = "sg-255yerd6h,sg-625t5rqrgy5,sg-98gr54w5g"
  prod = "sg-4c5y65re5,sg-3b35tg4wg,sg-3e3tgrtw4y6"
 }output "worker_elb_security_groups" {
  value = ["${split(",", var.worker_elb_sg_map[var.environment])}"]
}
```

## *后多区域支持变量:*

```
variable "worker_elb_sg_map" {
  description = "A map from environment to a comma-delimited list of build worker ELB security groups"
  type = "map" default = {
    us-east-1 = {
      dev = "sg-9fhjsdf76ef,sg-bksdajfhiece,sg-4487heff0b"
      qa = "sg-e29834hfisb99,sg-e398hfu95,sg-7d398hdsaf7"
      staging = "sg-239uhibwf942,sg-983huh939,sg-99834hh94f"
      training = "sg-250ba552,sg-62b39719,sg-9983h9384hf"
      prod = "sg-4c98hf93uc,sg-3938fh3hb,sg-3e083eiuf"
    } us-west-2 = {
      qa = "sg-6f390f15,sg-1b645761,sg-93484j80e9"
      prod = "sg-0c9384hf973hf,sg-ad2983hudh37,sg-4283h93498j"
    }
  }
}output "worker_elb_security_groups" {
   value = ["${split(",", "${lookup(var.worker_elb_sg_map[var.region], var.environment)}")}"]
}
```

## *前置多区域支持变量模块:*

```
module "variables" {
  source = "git::https://<github url>/<org name>/<repo name>//variables"
  environment = "${local.environment}"
  size = "${local.size}"
}
```

## *后多区域支持变量模块:*

```
module "variables" {
  source = "git::https://<github url>/<org name>/<repo   name>//variables"
  environment = "${local.environment}"
  size = "${local.size}"
  region = "${var.region}"
}
```

# 结果

为了管理期望，我们支持多区域部署的第一个实现是主动/被动的；这意味着只有一个区域将接收流量，而另一个区域则完全是灾难情况下的故障转移。

在实施了这些更改并对我们的构建管道功能和验证代码库的方式进行了一些重要的补充后，我们已经能够可靠地部署到 us-east-1 和 us-west-2 近四个月了。由于部署到不同地区和不同环境的差异只不过是一些输入变量，所以维护一切只需要很少甚至不需要额外的开销。

我对我的团队到目前为止的表现非常满意，但是我们还没有接近完成。接下来的迭代将关注自动故障转移，并最终转向主动/主动实施。我很高兴看到我们发展旅程的下一步。

## 有关系的

*   [建筑特征切换成地形](/capital-one-tech/building-feature-toggles-into-terraform-d75806217647)
*   [使用 Terraform 部署多个环境](/capital-one-tech/deploying-multiple-environments-with-terraform-kubernetes-7b7f389e622)
*   [像对待应用程序一样对待你的 Terraform:第 1 部分——为什么 Terraform 要放在 Docker 容器中？](/capital-one-tech/treating-your-terraform-like-an-application-why-terraform-in-a-docker-container-31e802314b4)
*   [像对待应用一样对待你的地形:第二部分——如何对接地形](/capital-one-tech/treating-your-terraform-like-an-application-how-to-dockerize-terraform-5d7edac741fc)

*声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。*