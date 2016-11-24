# 1. 安装NVIDIA驱动：


## 1.1 首先查看当前机子里装的NVIDIA的默认驱动：

```lspci | grep -i NVI```

会显示类似的信息：

```
02:00.0 VGA compatible controller: NVIDIA Corporation Device 17c2 (rev a1)
02:00.1 Audio device: NVIDIA Corporation Device 0fb0 (rev a1)
03:00.0 VGA compatible controller: NVIDIA Corporation Device 17c2 (rev a1)
03:00.1 Audio device: NVIDIA Corporation Device 0fb0 (rev a1)
82:00.0 VGA compatible controller: NVIDIA Corporation Device 17c2 (rev a1)
82:00.1 Audio device: NVIDIA Corporation Device 0fb0 (rev a1)
83:00.0 VGA compatible controller: NVIDIA Corporation Device 17c2 (rev a1)
83:00.1 Audio device: NVIDIA Corporation Device 0fb0 (rev a1)
```

TitanX驱动使用346.96（NVIDIA-Linux-x86_64-346.96.run）和352.30（NVIDIA-Linux-x86_64-352.30.run）版本，Ubuntu和CentOS通用

## 1.2 卸载默认的驱动nouveau

```
rmmod nouveau
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist-nouveau.conf
```

## 1.3 停止桌面

```
/etc/init.d/lightdm stop
```

## 1.4 安装NVIDIA的驱动


```
./NVIDIA-Linux-x86_64-346.96.run -a -s -Z --no-opengl-files
```

-a 接受license
-s 静默安装
-Z 关闭nouveau
-- no-opengl-files 不安装opengl相关驱动程序
--kernel-source-path kernel路径     


# 2. 安装CUDA

## 2.1 安装cuda
  
```
./cuda_7.5.18_linux.run --no-opengl-libs --toolkit  --samples --samplespath=/usr/local/cuda-7.5 -silent
```

--no-opengl-libs不安装opengl库
--toolkit安装cuda tookit
--samples 安装cuda samples
--samplespath 指定cuda samples路径
--kernel-source-path指定kernel路径（/usr/src/linux-headers-$(uname -r)


## 2.2 添加cuda到环境变量

```
echo "export LD_LIBRARY_PATH=/usr/local/cuda/lib:/usr/local/cuda/lib64/:/usr/local/cuda-7.0/lib:/usr/local/cuda-7.0/lib64/:$LD_LIBRARY_PATH" >> /etc/profile

echo "export PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/cuda-7.0/bin:/usr/local/cuda/bin:$PATH" >> /etc/profile
```

## 2.3(optional) 另一个安装cuda的方法，个人认为更加简单有效

```
sudo dpkg -i xxxxxxxx-cuda-xxxxxx.deb
suod apt-get update
sudo apt-get install cuda
```

sudo dpkg -i xxxxxxxx-cuda-xxxxxx.deb : 放在软件源中
suod apt-get update : 更新源，注意查看版本是否更新成功
sudo apt-get install cuda : 直接从源安装，可以自动屏蔽掉冲突等问题，reboot就可以了