# WSL开发环境配置 v0.1

## WSL是什么？

WSL(Windows Subsystem for Linux)是一种虚拟化技术，在WSL2中利用了hyper-v作为虚拟化平台，来解决Linux应用在Windows环境中的兼容性问题。具体可以参考阅读[官方文档](https://docs.microsoft.com/en-us/windows/wsl/about)

### 为什么用WSL？

相比起使用传统的方式，在Windows下运行一个完整的虚拟机，使用WSL降低了虚拟化所带来的资源消耗与性能损失。另一方面，他又提供了一个很方便的Linux开发环境，包括使用Linux命令行工具，编译工具链（如gcc，clang等等），以及访问Linux文件系统等。

### WSL1 vs WSL2

简单的来说，WSL1与WSL2采用了不同的技术架构。WSL1主要采用的是系统调用的重映射，将Linux应用调用的系统调用替代为Windows的系统调用。但是这也导致了使用WSL1的局限性，比如说一部分系统调用并没有被实现。而WSL2则采用了Type-1的虚拟化技术,使用hyper-v来管理Linux内核。因此，WSL2其实包含了一个较为完整的Linux内核，所以兼容了Linux当前内核版本所有的系统调用。

但是相较于WSL1，倘若从WSL访问Windows的文件系统则会产生额外的开销。并且也有一些特殊情况下，可能使用WSL1会更方便，（比如访问串口，使用老版本的VMware、VirtualBox，主机的内存非常小，或者需要使得WSL的ip地址和主机处于同一个子网中等）。在MS的官网里可以找到这部分的解决方案，以及更详细的讲解。具体说明可以阅读[官方文档](https://docs.microsoft.com/en-us/windows/wsl/compare-versions)

## 安装与使用

安装WSL的步骤取决于当前使用的Windows版本。查看当前Windows版本的方法有很多，其中一种是用`Windows Key + R`打开**运行对话框**， 然后输入`winver`。

### 简单的安装步骤（适用于高版本的Windows）

**注意：Windows版本必须在Windows 10  2004 版本或以上（Build number 19041或以上）或者 Windows 11！**

在新版本的Windows中，我们可以通过MS自带的工具实现一键式安装。安装成功后，就可以同时使用WSL1与WSL2。
- 具体安装步骤可以参考[MS的官方文档](https://docs.microsoft.com/en-us/windows/wsl/install)
- 在安装完成以后，可以用`wsl -l -v`来检查安装是否成功。默认的Linux发行版是Ubuntu。

### 手动安装步骤（适用于老版本的Windows）

我在拿到我们发的电脑时，安装的是**Windows 10 version 1909 build number 18363.535**。在这个版本下，我们可以使用WSL1但是无法使用WSL2。

### 使用WSL1

<a name="Enable-WSL"></a>使用WSL1，只需要我们打开Windows里面的Windows Subsystem for Linux选项。具体步骤可以参考：
- 打开“控制面板”
- 打开“程序”
- 打开“启用或关闭Windows功能”
- 打开“Windows Subsystem for Linux”选项
- 重启
- 然后在Microsoft store里面搜索并安装你喜欢的Linux发行版，比如Ubuntu，Kali等。

<a name="Install-Distro"></a>你也可以参考：
- [安装WSL1](https://www.windowscentral.com/install-windows-subsystem-linux-windows-10)

### 使用WSL2

#### WSL2的系统要求

- 对于x64的系统来说，需要**1903**版本或更高，对应的build number要**18362**或更高。但是如果版本刚好是**1903**或者**1909**的话，需要进一步保证build umber在**18362.1049**或者**18363.1049**及以上！
- 对于ARM64系统来说需要版本 **2004**及以上, build number **19041**及以上。
- 具体请参考微软官方文档：
  - [运行WSL2的要求](https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-2---check-requirements-for-running-wsl-2)

倘若当前的版本达不到以上文档所要求的，那么必须先升级Windows。

#### 升级Windows

##### 通过Windows自带的升级工具升级
- 具体步骤请参考[微软官方文档](https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-2---check-requirements-for-running-wsl-2)

##### 通过uupdump下载镜像文件升级/安装
在官网上选择合适的Windows版本，然后通过uupdump工具来下载组装镜像。工具包中的README有详细的使用方法。
- [uupdump官网](uupdump.net)

##### 通过cumulative update包进行手动升级
这种方法比较升级快速，但是过程稍微复杂一些，首先我们需要在[MS官网的更新发布日志](https://support.microsoft.com/en-us/topic/windows-10-update-history-e6058e7c-4116-38f1-b984-4fcacfba5e5d)里面定位到我们当前的版本，然后确认下一个需要升级到的版本，在文档中找到需要的SSU（Sevice Stack Updates）与LCU（Latest Cumulative Update）的**KB number**。最后在[MS Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx)里面通过**KB number**找到对应的安装包下载并安装。

### 从WSL1升级WSL2

**注意：这一步必须满足上面所提及的兄台那个版本要求。**

在升级WSL2之前，我们首先需要确认启动两个Windows功能：
1. **hyper-v**
2. **virtual machine platform**

一种启动方式是，在“控制面版”找到“程序”找到“启用或关闭Windows功能”可以参考[开启WSL功能](#Enable-WSL)，然后开启“hype-v”与“虚拟机平台”选项。

具体，可以参考[官方文档](https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-3---enable-virtual-machine-feature), 如果遇到错误，请先参考[virtualization capabilities](https://docs.microsoft.com/en-us/windows/wsl/troubleshooting#error-0x80370102-the-virtual-machine-could-not-be-started-because-a-required-feature-is-not-installed)确保虚拟化功能时候可用。

#### 升级WSL的kernel

下载[安装包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)更新Linux内核。具体步骤可以参考[官方文档](https://docs.microsoft.com/en-us/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package)

#### 从WSL1转换为WSL2

最后一步，我们需要下载一个Linux发行版，可以参考[上面提到的方法](#Install-Distro)，或者将已有的发行版的运行转换成WSL2版本。
如果已经拥有了一个发行版，那么可以在**Powershell**里使用:
```powershell
# 找到你的发行版信息
$wsl -l -v
# 版本转换
$wsl --set-version <your_distro_name> 2
```