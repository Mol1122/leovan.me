---
title: ToB 产品用户权限 (User Privileges of ToB Products)
author: 范叶亮
date: '2019-11-24'
slug: user-privileges-of-2b-products
categories:
  - 产品
tags:
  - ToB
  - 2B
  - 用户权限
  - User Privilege
  - RBAC
  - Role-Based Access Control
  - 基于角色的访问控制
  - 用户
  - User
  - 权限
  - Privilege
  - 角色
  - Role
  - 用户组
  - User Group
  - 资源
  - Resources
  - 菜单
  - Menus
  - 功能权限
  - Function Privilege
  - 数据权限
  - Data Privilege
images:
  - /images/cn/2019-11-24-user-privileges-of-2b-products/r-p-r-u-relations.png
  - /images/cn/2019-11-24-user-privileges-of-2b-products/RBAC0.png
  - /images/cn/2019-11-24-user-privileges-of-2b-products/RBAC1.png
  - /images/cn/2019-11-24-user-privileges-of-2b-products/function-and-data-privilege.png
  - /images/cn/2019-11-24-user-privileges-of-2b-products/organization-functions-and-data.png
  - /images/cn/2019-11-24-user-privileges-of-2b-products/function-tree.png
  - /images/cn/2019-11-24-user-privileges-of-2b-products/menu-page-and-module.png
  - /images/cn/2019-11-24-user-privileges-of-2b-products/menu-page-and-module.png
---

用户权限在产品中是一个最基础的功能，它决定了用户在产品中能做些什么。虽然用户对权限的感知并不是很明显，但作为一个系统的基础功能，用户权限可以说会影响产品设计和实现的方方面面。对于不同类型的产品，用户权限的设计也略有差异，ToC 产品中用户之间相对独立，所需要考虑的问题会比 ToB 产品简单不少，本文仅针对 ToB 产品的用户权限给出一些思考。

## Role-Based Access Control

RBAC (Role-Based Access Control，基于角色的访问控制) 是一种已经广泛应用于各种管理系统的权限管理模型 [^ferraiolo1995role]。在 RBAC 中，操作权限与角色之间建立关联，再通过在角色与用户之间建立关联来完成对用户的授权，大大提高了权限控制的灵活性。在 RBAC 中包含几个关键的概念：

{{% blockquote %}}
**资源**：所有可以访问的对象，从交互角度可以理解为：页面，按钮等一切可以操作的对象，从后台接口角度可以理解为：以 Rest 接口为例，资源即为每个 URI。
{{% /blockquote %}}

{{% blockquote %}}
**权限**：获取资源的方式，简单理解即为资源的增删改查。
{{% /blockquote %}}

{{% blockquote %}}
**角色**：一个授权等级的工作职位或职称。
{{% /blockquote %}}

{{% blockquote %}}
**用户**：一名使用者或自动代理人。
{{% /blockquote %}}

资源，权限，角色和用户之间的关系如下图所示：

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/r-p-r-u-relations.png" title="资源-权限-角色-用户关系" >}}

「资源」和「权限」之间为多对多的关系，「权限」和「角色」之间为多对多的关系，「角色」和「用户」之间亦为多对多的关系。

### RBAC0

RBAC0 为 RBAC 中最简单最基础的权限模型，包含了用户，角色，权限之间的关系，如下图所示：

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/RBAC0.png" title="RBAC0" >}}

用户，角色和权限之间都可以是多对多的关系，在系统分工简单权限清晰明确的情况下，用户和角色之间也可能是多对一的关系。

### RBAC1

RBAC1 在 RBAC0 的基础上引入了角色继承的概念，添加了「子角色」，上级角色可以继承下级角色所有的权限。

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/RBAC1.png" title="RBAC1" >}}

例如，一个公司的不同人力资源副总监分管不同的职能，因此具有不同的权限，而人力资源总监则应该具有所有人力资源副总监的权限。同时，人力资源总监也可能具有其他人力资源副总监都不具有的权限。

### RBAC2

RBAC2 在 RBAC0 的基础上增加了一些限制，引入了 SSD (Static Separation of Duty，静态职责分离) 和 DSD (Dynamic Separation of Duty，动态职责分离)。

SSD 主要应用于用户和角色之间，主要的约束包括：

- 角色互斥约束：同一个用户仅能分配到互斥角色中的一个。例如：财务系统中一个用户不能同时分配会计和审计两种互斥的角色。
- 基数约束：一个用户拥有的角色是有限的，一个角色拥有的权限是有限的。
- 先决条件约束：用户在获取更高的角色之前需先拥有低级的角色。

DSD 主要应用于会话和角色之间动态地限制用户及其拥有的角色和相应的权限。例如：一个用户在系统中拥有两个不同的角色，但在使用系统时只能激活其中一个角色。

### RBAC3

RBAC3 是 RBAC1 和 RBAC2 的合集，既包含了角色分层也包含了相关约束。

## 权限扩展

接下来我们探讨在 RBAC 的基础上在真实的 ToB 产品中又有哪些权限扩展，这里有时我会以「种植」业务的 SaaS 为例进行简略分析。我们将权限又分为「功能权限」和「数据权限」两个部分，如下图所示：

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/function-and-data-privilege.png" title="功能权限和数据权限" >}}

功能权限就是指我们具体能够干些什么，例如：育苗，除草，施肥等等。数据权限是指我们是对谁做这些事情，例如：是对北京的实验大棚除草，还是河北的生产大棚施肥。

ToB 产品的销售对象并非个人，而是一个组织，在此我们引入「组织」的概念：

{{% blockquote %}}
**组织**：用户所隶属的单位，从产品销售角度限定了用户所能使用的功能。
{{% /blockquote %}}

对于一个组织，我们设置有「功能池」和「数据池」。功能池即组织购买的产品的功能集合，一个组织的用户所能够使用的功能受限与此，并由组织的系统管理员分配功能池中的功能。数据池即组织自己的相关数据集合，由组织的管理员对不同的员工进行分配。

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/organization-functions-and-data.png" title="组织功能池和数据池" >}}

对于功能权限和数据权限，均为一个树状的层级关系，以功能权限为例，层级如下图所示：

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/function-tree.png" title="功能权限层级关系树" >}}

功能的层级和菜单的层级有一定的对应关系，层级不易过深，这部分将在下文菜单部分详细介绍。数据的层级则需要根据产品涉及的具体业务做出相应的规范要求，例如：「根」-「种植场」-「种植区」-「种植大棚」-「种植地块」。同时，为了方便层级的扩充，在实现层面上不建议提前把所有层级固定死，可以采用父子层级的形式循环关联。

## 权限分配

在介绍如何根据上述设计进行权限分配前，我们再引入一个概念，「用户组」：

{{% blockquote %}}
**用户组**：具有相同权限的一组用户。
{{% /blockquote %}}

当一个组织中有大量用户需要具有相同的权限时，为了避免给每个用户分配权限导致的繁琐操作，可以将这些具有相同权限的用户归入一个用户组。对该用户组赋予一定的权限，则该用户组下面的所有用户自动具有相应权限，当需要取消其中某些用户权限时，仅需要将其从该用户组中移除即可。理论上，「用户」和「用户组」以及「用户组」和「角色」之间都可以是多对多的关系。

下图为一个最细力度的权限分配示意图：

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/privileges-assignment.png" title="权限分配" >}}

首先，我们暂时限定一个用户仅能够隶属于一个组织，因此该用户所能够具有的最多的权限即为该组织所购买的产品的功能权限和组织自己的全部数据权限。

其次，一个最基本的权限应该是由一个功能权限对应一个数据权限构成的。这里面的功能权限和数据权限可以是任意一个级别的，同时当具有高级别的功能权限或数据权限后，子级别的相应权限自动获得。

最后，权限、角色、用户组和用户之间均可以是多对多的关系。

虽然通过很多概念的抽象使得权限管理变得相对简单，但是由于理论上支持各种细粒度的操作，在一定程度上又会加重权限管理的运维成本。在一个 ToB 产品的前期，组织的管理员可能对整个系统不是很熟悉，这样权限管理的工作会落到内部管理员的头上。当随着产品的不断发展，组织和用户的量级不断变大，内部管理员仅需为每个组织开通一个组织管理员并赋予该组织的所有权限即可，该组织内部的权限管理则交由组织的管理员进行运维即可。

## 菜单，页面和组件

以上我们都是从业务逻辑的角度解释权限控制，接下来我们从用户交互的角度出发简单阐述一下权限控制在用户交互上的体现。下图为一个 ToB 产品的原型界面：

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/menu-page-and-module.png" title="菜单，页面和组件" >}}

在这个界面中，我们粗略的将其中的元素划分为三大块：「菜单」，「页面」和「组件」。这个划分并不是很准确，甚至可以说一定程度上概念之间有重叠，这样划分仅仅是为了更好的对应到上文涉及的相关概念上。

上文中我们提到一个角色的权限可以是任何一个级别的功能权限和数据权限的组合，同时权限、角色、用户组和用户之间都可以是多对多的关系，在此我们假设后台已经通过查询获得到了一个用户的所有权限。

### 菜单

菜单对应到主要是功能权限，但不是所有的功能权限都会以菜单的形式展现出来，因此我们可能还需要维护一套菜单的层级关系以及同功能权限的对应关系，或者我们直接利用功能权限的层级关系，但需要在里面补充上是否作为菜单显示的属性。具有不同权限的用户看到的菜单也会是不一样的。

在菜单设计时，建议的原则是层级不要过深，过深的话从交互上就会导致你的菜单像极了[「箭头型代码」](https://coolshell.cn/articles/17757.html)。

{{< figure src="/images/cn/2019-11-24-user-privileges-of-2b-products/arrow-style-code.jpg" title="箭头型代码" >}}

理论上，所有包含子功能的父功能都不代表一个具体的操作，一般仅仅是为了维护一个分组而创建的。

### 页面

页面并不是浏览器里面的整个页面，这里我们特指不包含菜单的部分。当我们单击一个功能菜单时，对应的页面则会相应的显示出来。当然，我们知道单击按钮 (下文会提到的组件) 也可能会打开一个新的页面，这种情况我们在下文中再展开说明。当我们单击了一个叶子界别的菜单后，其实我们已经使用了一个权限，一般情况下对应的是对一个资源的查询。

### 组件

页面中包含了操作的结果信息，一般情况下这些信息是不可交互的，同时页面中也包含了大量可以交互的地方，这里我们称其中的部分为组件，例如：按钮等。当然还有一些被我们在通常情况下也称之为的组件的元素，例如：输入框，下拉框等等，虽然他们也有交互功能，但是一般情况下他们并不会触发一个权限操作，因此这里我们就先将组件特指那些可以产生权限操作的元素。

一个页面可以包含多个组件，也就是说一个页面并不一定仅对应一个权限操作，可能是多个权限操作的集合。所以简单的说我们可以将功能权限设置的比菜单多一个级别，功能权限的倒数第二级对应叶子菜单，功能权限的最后一级对应页面上的组件。

## 权限运维

权限的精细化管理和运维成本是相互矛盾的，想更加精细化的管理权限必然会增加运维成本。因此一个 ToB 的产品到底使用什么力度的权限管理更加合适呢？我想这没有一个准确的答案，从个人角度给出两点有参考意义的设计原则：

1. 客户需求。ToB 产品吗，最终是要拿来卖的，跪添甲方爸爸就好，甲方爸爸的需求就是合理的需求，无需质疑。说的有点过了，意思其实就是尽可能满足合理客户需求的前提下，制定合适的权限管理系统。
2. 成本合算。有的时候客户也不是很清楚到底需不需要精细化的权限管理，那么问题就又抛回给了产品经理，那么我们需要考虑更多的是当下的实现成本和后期的改造成本。这是一个很现实的考虑，如果业务 KPI 压力比较大，有时候我们宁可承担更高的改造成本也会先实现一套简单的权限管理先用着。但是我个人认为，在研发资源相对充足的前提下还是尽量将权限系统设计完善，因为这是一个系统很底层的部分，甚至会影响后续所有的业务功能逻辑的设计和实现。

权限运维是一个很耗费人力的工作，解决这个问题没有太好的途径。提高用户的交互体验，编写简单易懂的使用说明书，只有将一个组织的权限管理交到组织的管理员手里。才能够真正解放内部管理运维人员的工作时间。

[^ferraiolo1995role]: Ferraiolo, David, Janet Cugini, and D. Richard Kuhn. "Role-based access control (RBAC): Features and motivations." _Proceedings of 11th annual computer security application conference_. 1995.