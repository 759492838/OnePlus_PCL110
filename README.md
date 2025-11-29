# 一、准备工作
# 1.1 系统要求
# 推荐使用Ubuntu 18.04/20.04（64位），其他Linux发行版需对应调整依赖安装命令
# 至少100GB空闲磁盘空间（源码+编译产物）
# 8GB以上内存（推荐16GB+，编译更流畅）
# 稳定的网络连接（需同步大量源码）
# 1.2 安装依赖包
# 首先安装编译所需的基础依赖工具，执行以下命令：

echo "=== 安装依赖包 ==="
sudo apt update
sudo apt install -y git-core gnupg flex bison build-essential zip curl \
zlib1g-dev gcc-multilib g++-multilib libc6-dev-i386 lib32ncurses5-dev \
x11proto-core-dev libx11-dev lib32z1-dev libgl1-mesa-dev libxml2-utils \
xsltproc unzip fontconfig libncurses5 libncurses5-dev openjdk-8-jdk
# 1.3 Python版本设置
# 由于该TWRP源码基于Python 2开发，必须使用Python 2（不兼容Python 3），需将系统默认Python设置为Python 2：

#安装Python 2
sudo apt install -y python2

#建立软链接（若系统默认是Python 3）
sudo ln -sf /usr/bin/python2 /usr/bin/python

#验证版本（应显示Python 2.x.x）
python --version
content_copy
# 1.4 安装Repo工具
Repo是Android源码管理工具，用于同步多个Git仓库，安装方法：

# 下载repo工具
mkdir -p ~/bin
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo

# 添加到环境变量
echo 'export PATH=~/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
# 二、获取TWRP源码
# 2.1 初始化源码仓库
# 使用minimal-manifest-twrp/platform_manifest_twrp_omni作为源码清单，支持TWRP 9.0版本编译。

方式1：完整克隆（适合需要历史提交的场景）
#创建工作目录
mkdir twrp-omni && cd twrp-omni

#初始化仓库（指定TWRP 14.1分支）
repo init -u https://github.com/minimal-manifest-twrp/platform_manifest_twrp_aosp.git -b twrp-14.1
方式2：浅克隆（节省空间，仅拉取最新提交）
# 创建工作目录
mkdir twrp-omni && cd twrp-omni

# 浅克隆初始化（--depth=1表示仅拉取1层提交）
repo init --depth=1 -u https://github.com/minimal-manifest-twrp/platform_manifest_twrp_omni.git -b twrp-9.0
# 2.2 同步源码（repo sync）
初始化完成后，同步所有源码仓库：

# 开始同步（-j参数指定线程数，建议设为CPU核心数*2）
repo sync -j8
注意：同步过程可能耗时较长（取决于网络速度），若中途失败可重新执行repo sync -j8继续同步。
# 三、使用Twrpdtgen一键生成设备树
Twrpdtgen（TWRP Device Tree Generator）是一款自动生成设备树的工具，可基于设备的boot.img或recovery.img生成基础设备树，简化适配流程。

# 3.1 安装Twrpdtgen
#安装依赖

sudo apt install -y python3-pip

sudo pip3 install twrpdtgen
3.2 生成设备树

步骤1：获取设备的boot.img

从设备中提取：通过adb pull /dev/block/bootdevice/by-name/boot boot.img

或从官方ROM固件中解压获取

步骤2：生成设备树

#进入源码的设备目录

cd twrp-omni/device

#使用twrpdtgen生成设备树（替换<manufacturer>为厂商名，<device>为设备型号，<path_to_boot.img>为boot.img路径）

python3 -m twrpdtgen -o <manufacturer>/<device> <path_to_boot.img>

示例（为一加设备生成树）：

python3 -m twrpdtgen -o OnePlus/PCL110 boot.img

生成的设备树会位于device/<manufacturer>/<device>目录，包含Android.mk、twrp.fstab等关键文件，可根据实际需求进一步修改。

# 四、编译TWRP
# 4.1 进入编译环境
#回到源码根目录
cd twrp-omni

#加载编译环境变量
. build/envsetup.sh


# 剩下的参考https://cuoxianxu.cn/index.php/archives/41/


# 视频参考https://www.bilibili.com/video/BV13YbhzAEdC?vd_source=9ffa5571dde9c009e7466b4dc8f2e11b
