---
title: AWS EC2入门篇
date: 2016-04-11 12:00:08
category: DevOps
tags: [AWS, EC2, IaaS, 云服务, SSH]
---

### 基本介绍

> Amazon Elastic Compute Cloud (Amazon EC2) provides scalable computing capacity in the Amazon Web Services (AWS) cloud.

<!-- more -->

Amazon EC2是一个IaaS云服务，主要提供弹性的计算资源。通俗的讲，就是提供多种类型的虚拟机。EC2也是整个AWS最核心的组成部分，AWS中有许多的服务需要依赖它。在EC2环境中，虚拟机被称为实例，实例的镜像被称为AMI(Amazon Machine Image)。任何的企业和个人都可以选择不同的类型和大小的实例，在很短的时间内创建、启动和运行。主要是根据类型和使用时间收费，即使用多少收多少的费用。

1.EC2 concept & management


2.Instances management
3.Volumes 卷，硬盘， S3，EBS
df -h
df -T
sudo fdisk -l

sudo mkfs.ext4 /dev/xvdf

sudo mount /dev/xvdf /mnt/ebs

sudo umount /dev/xvdf


4.Snapshots 每个快照代表一个卷在一个特定时间点的状态。
5.Key Pairs 安全密钥对
6.Placement Groups
7.Elastic IPs 弹性IP，自己买的IP关联到Instance
8.Load Balancers

参考资料
[1] [Amazon EC2 User Guide for Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html)