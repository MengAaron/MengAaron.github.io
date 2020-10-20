1. 结果链接 KITTI     result [KITTI](https://docs.google.com/spreadsheets/d/13EjrKI0qY7HKAAKl06CcC9-E0GzXBt-19c5Htp8y0Go/edit?usp=sharing),     Nuscenes reult [Nuscenes](https://docs.google.com/spreadsheets/d/1FrTqvdbo9OSf8xZ0juBbHDHBPM9GlguMFyvQqv0yIr0/edit?usp=sharing)

2. 创建conda环境

3. 1. 安装anaconda3
   2. vim      ~/.bash_profile
   3. if      test -f .bashrc ; then source .bashrc fi
   4. source ~/.bash_profile
   5. conda      create -n openPCDet python=3.6
   6. conda      activate openPCDet

4. 一、安装pytorch

5. 1. nvcc -V查看cuda版本
   2. conda      install pytorch==1.3.1 torchvision==0.4.2 cudatoolkit=10.1 -c pytorch
   3. 实验室的电脑是cuda      10.1不要随便去改版本，也不需要单独安装CUdnn，实验室电脑应该都有。pytorch版本也不要用1.5.0会报fatal error

6. 修改~/.bashrc

7. 1. vim      ~/.bashrc
   2. export CUDA_HOME=/usr/local/cuda
   3. export      LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64
   4. export      PATH=$PATH:$CUDA_HOME/bin
   5.  

8. 一、升级cmake(实验室初始版本是3.5.1)

9. 1. wget[ ](https://github.com/Kitware/CMake/releases/download/v3.14.3/cmake-3.14.3.tar.gz)https://github.com/Kitware/CMake/releases/download/v3.14.3/cmake-3.14.3.tar.gz
   2. tar      -zxvf cmake-3.14.3.tar.gz
   3. cd      cmake-3.14.3
   4. ./bootstrap
   5. make
   6. sudo      make install
   7. hash -r
   8. cmake      –version 查看版本信息
   9. 注意 cmake 版本不要升级到3.18+会有证书问题

10. 一、安装spconv（最容易出现问题的一步，需要多次尝试cmake,pytorch版本组合）

11. 1.  git      clone[ ](https://github.com/traveller59/spconv)https://github.com/traveller59/spconv      --recursive(必须加，否则pybind11安装不上)
    2.  sudo      apt-get install libboost-all-dev
    3. pybind11可能要手动git      clone
    4. python      setup.py bdist_wheel
    5.  cd      dist
    6. pip      install spconv-1.2.1-cp36-cp36m-linux_x86_64.whl
    7. 这里有一个问题需要注意，titan      xp和2080ti显卡不同，如果在2080ti上编译的代码在titan xp出现invalid device function，此时需要删除spconv的build文件夹rm      -rf build&python setup.py clean然后重新在titan xp上安装(建议建立两个conda环境)

12. 安装PCDet

13. 1. git      clone[ ](https://github.com/open-mmlab/OpenPCDet.git)https://github.com/open-mmlab/OpenPCDet.git
    2. cd      OpenPCDet
    3.  pip      install -r requirements.txt
    4.  python      setup.py develop

14. KITTI数据集

15. 1. 下载地址http://www.cvlibs.net/datasets/kitti/eval_object.php?obj_benchmark=3d
    2. left      color,velodyne,label,calibration必须下载，只能用电脑下载然后传到服务器上
    3. road下载地址https://drive.google.com/file/d/1d5mq0RXRnvHPVeKx6Q612z0YRO1t2wAp/view?usp=sharing
    4. 把文件按github上排列
    5. 运行python      -m      pcdet.datasets.kitti.kitti_dataset create_kitti_infos tools/cfgs/dataset_configs/kitti_dataset.yaml

16. 跑second模型

17. 1. conda      activate openPCDet
    2. cd      OpenPCDet
    3. cd tools(必须要进入tools文件夹下运行否则报错，推测是文件中相对路径的原因)
    4. 多gpu命令：CUDA_VISIBLE_DEVICES=${GPU_INDEX}      bash scripts/dist_train.sh ${GPU_NUM} --cfg_file      cfgs/kitti_models/second.yaml（sh不行，可能是ubuntu16中命令设置问题）
    5. 单gpu命令：python      train.py --cfg_file cfgs/kitti_models/second.yaml

18. 运行结果

19. 1. CUDA_VISIBLE_DEVICES=3      python test.py --cfg_file cfgs/kitti_models/second.yaml --batch_size 4      --eval_all
    2. 我测试了epoch80的结果，78.51,52.77，66.17，作者提供的模型是78.62，52.98，67.15
    3. 测试结果只显示val结果，需要修改dataset_configs中的INFO_PATH，在test中添加kitti_infos_test.pkl

 