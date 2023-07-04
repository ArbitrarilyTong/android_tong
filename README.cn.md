<!-- The badge template is from https://ileriayo.github.io/markdown-badges/ -->
<div align = center>

[![ArbitrarilyTong](https://github.com/ArbitrarilyTong/.github/raw/main/github_banner_2.png)][website]

<h1>ArbitrarilyTong | 任意桐</h1>

本项目基于 [LineageOS][lineage]

---

[![Telegram](https://img.shields.io/badge/Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)][telegram]
[![Twitter](https://img.shields.io/badge/Twitter-%231DA1F2.svg?style=for-the-badge&logo=Twitter&logoColor=white)][twitter]
[![Codeberg](https://img.shields.io/badge/Codeberg-2185D0?style=for-the-badge&logo=Codeberg&logoColor=white)][codeberg]
[![GitHub](https://img.shields.io/badge/Devices-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)][devices]

---
</div>

## 准备工作

<details>
<summary>在正式开始编译工作之前，我们需要进行以下准备</summary>

### 1. 安装依赖项

为了构建 crDroid，需要安装一些软件包。

对于 `Ubuntu`：

```sh
sudo apt install bc bison build-essential ccache curl flex g++-multilib gcc-multilib git git-lfs gnupg gperf imagemagick lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5 libncurses5-dev libsdl1.2-dev libssl-dev libwxgtk3.0-gtk3-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev
```

对于 `ArchLinux`：

```sh
yay -S aosp-devel lineageos-devel
```

对于其他发行版，请自行查找相关软件包。

### 2. 获取 Repo

[Repo](http://source.android.com/source/developing.html) 是由 Google 提供的一个工具，可以简化在 Android 源代码环境中使用 [Git](http://git-scm.com/book) 的过程。

在所有发行版中，可以通过以下命令行获取 `repo`：

```bash
# 创建一个存放 Repo 的目录，并将其添加到路径中
mkdir ~/bin
PATH=~/bin:$PATH

# 下载 Repo
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo

# 给 Repo 添加可执行权限
chmod a+x ~/bin/repo
```

对于 `ArchLinux`，您也可以通过 `yay` 安装：

```sh
yay -S repo
```
</details>



## 初始化 Repo

以下分支可用于构建：

- ArbitrarilyTong-13.0（基于 crDroid）

输入以下命令以初始化代码仓库：

```bash
# 创建一个用于存放源文件的目录
# 可以选择任意位置（只要文件系统区分大小写）
mkdir ArbitrarilyTong
cd ArbitrarilyTong

# 启用 Git LFS 功能
git lfs install

# 在创建的目录中安装 Repo
repo init -u https://github.com/ArbitrarilyTong/android_tong.git -b ArbitrarilyTong-13.0 --git-lfs
```

## 同步源代码

在每次需要拉取上游更改时，执行以下操作。请注意，首次运行需要一些时间，因为它将下载所有所需的 Android 源代码文件及其更改历史。

```bash
# 让 Repo 处理所有繁重的工作
repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags
# 如果需要查看错误日志，可以使用 -v 选项
# repo sync -c -j$(nproc --all) --force-sync --no-clone-bundle --no-tags -v
```

## 准备特定于设备的代码

以下设备代号可用于构建：

- thyme（小米10S）

输入以下命令以初始化仓库：

```bash
# 运行以准备设备列表
source build/envsetup.sh
# ... 现在运行
lunch lineage_devicecodename-buildtype
```

## 打开缓存以加快构建速度

如果希望通过运行以下命令加快后续的构建速度，可以使用 `ccache`：

```sh
export USE_CCACHE=1
export CCACHE_EXEC=/usr/bin/ccache
```

并将该行添加到您的 `~/.bashrc` 文件中。然后，通过输入以下内容指定 `ccache` 可以使用的最大磁盘空间：

```sh
ccache -M 50G
```

其中 `50G` 对应于 50GB 的缓存空间。需要运行一次。25GB-100GB 的空间将显着增加构建速度（例如，常规的 1 小时构建时间可以缩短到 20 分钟）。如果只构建一个设备，25GB-50GB 足够。如果计划为多个不共享同一内核源代码的设备进行构建，则应计划 75GB-100GB。此空间将永久占用您的磁盘空间，请考虑这一点。

您还可以启用可选的 `ccache` 压缩。尽管这可能会导致轻微的性能减慢，但它可以增加缓存中适合的文件数。要启用它，运行：

```sh
ccache -o compression=true
```

## 开始构建

现在是开始构建的时候了！输入：

```sh
croot
mka bacon -j$(nproc --all)
```

构建将开始。

## 安装构建结果

假设构建没有错误（构建完成时会显而易见），在构建运行的终端窗口中键入以下命令：

```SH
cd $OUT
```

这里将找到所有创建的文件。其中比较重要的两个文件是：

- `boot.img`，引导映像，包含恢复-ramdisk。
- 以 `ArbitrarilyTong-` 开头的 zip 文件，是 ArbitrarilyTong 安装程序包。

<!-- reference infos -->
[lineage]: https://github.com/LineageOS
<!-- project infos -->
[website]: https://arbitrarilytong.win
[telegram]: https://t.me/ArbitrarilyTong
[twitter]: https://twitter.com/Ninni_kiri_jp
[codeberg]: https://codeberg.org/ArbitrarilyTong
[devices]: https://github.com/ArbitrarilyTong-Devices
[manifest]: https://github.com/ArbitrarilyTong/android