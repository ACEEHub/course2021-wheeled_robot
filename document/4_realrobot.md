# 实物实验Tutorial

## Table of Content

- 连接机器人
- 建图
- 获取数据
- 发送指令
- 其他注意事项

## 连接机器人

1. 修改自己笔记本的IP地址，设为Manual，Address：172.100.100.101，Netmask：255.255.255.0，Gateway：172.100.100.1

2. 网线连接自己的笔记本和机器人，终端ssh进去，ssh成功则说明网络层面上连接机器人成功

   ```bash
   $ ssh jz@172.100.100.100
   ```

   密码：robot123

3. 修改自己的笔记本的`~/.bashrc`文件，在最后一行加入以下命令

   ```bash
   export ROS_MASTER_URI=http://172.100.100.100:11311
   export ROS_IP=172.100.100.101
   ```

4. source一下`~/.bashrc`文件

   ```bash
   $ source ~/.bashrc
   ```

5. 在自己的笔记本查看机器人上的topic，如果能输出topic的信息则说明ROS层面上连接成功

   ```bash
   $ rostopic list
   $ rostopic echo /tf
   ```

## 建图

1. 在机器人上标定IMU，在ssh的界面上输入以下命令（标定好就结束了）

```bash
$ rosrun calib_imu imu_calib
```

2. 在机器人上转发激光雷达的数据，在ssh的界面上输入以下命令（这个会一直跑  ，后面需要再开个ssh）

```bash
$ rosrun topic_tools relay /scan /scan_emma_nav_front
```

3. 打开urg节点，在ssh的界面上输入以下命令

```bash
$ rosrun urg_node urg_node //如果没有urg_node就sudo apt-get install ros-kinetic-urg-node
```

4. 打开tf转发工具，在ssh的界面上输入以下命令（这个会一直跑，后面需要再开个ssh）

```bash
$ cd jz/catkin_ws
$ source devel/setup.bash
$ rosrun my_tf tf_broadcaster
```

5. 建图，如果之前没有建过图或者建图效果太差需要重建，则完成这一步骤，否则跳过。

   首先在ssh的界面打开cartographer_occupancy_grid_node这个节点

```bash
$ rosrun cartographer_ros cartographer_occupancy_grid_node
```

​		然后打开网页，在网页上操作建图



​		建好之后使用map_server存储建好的地图

```bash
$ rosrun map_server map_saver -f mymap map:=/map
```

6. 建好图后，在网页中启动导航模式，在ssh界面启动map_server，之后就可以通过/map 这个topic来读取地图

```bash
$ rosrun map_server map_server mymap.yaml
```

## 获取数据



## 发送指令

//**********动作
只需要往/webService/cmd_vel 发送geometry_msgs/Twist即可



## 其他注意事项

- 机器人速度不能超过0.2m/s，不然容易出事:warning:

- 注意观察机器人左上角的急停按钮，发生突发情况可以按急停按钮

