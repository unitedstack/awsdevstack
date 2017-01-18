# DevStack on AWS

该 Ansible 脚本一次运行将在在 AWS 创建一个由 4 台 EC2 虚拟机节组成的 DevStack 集群，分别是 Controller，Network，Computer1 和 Computer2。

## 使用方法：

1, 设计环境变量：

```bash
export AWS_ACCESS_KEY_ID='abc'
export AWS_SECRET_ACCESS_KEY='def'
export AWS_REGION='ap-northeast-2' # AWS 韩国Region
```


2, 打开 one-click-devstack-on-aws.yml 中设置合适的 Spot Instance 价格，Instance Type 推荐使用c4.4xlarge，及keypair密钥名。

```bash
...
  vars:
    instance_type: c4.4xlarge   # 推荐使用c4.4xlarge。
    keypair: yourkeypair        # 必须设置自己的keypair，否则创建ec2会失败。
    image: ami-c74789a9         # 保持不变，默认为AWS Marketplace的CentOS 7，如果之前没用过该 AMI，需要在Marketplace中接受term：https://aws.amazon.com/marketplace/pp/B00O7WM7QW
    spot_price: 0.113           # 在AWS Console中查看Spot Instance的Price History，根据市场情况设置合理的价格
...
```

3, 升级ansible并运行：
Ansible AWS 相关的 module 只在ansible 2.2以上的版本支持，因此需要先升级ansible到最新版本再运行：
```bash
sudo pip install --upgrade ansible
ansible-playbook one-click-devstack-on-aws.yml -vvv
```
如果中途运行失败退出，需要在AWS Console 中删除 VPC，及对应的 eip 资源（AWS每个账号默认 5 个eip的配额），下次才能正常运行。

4, 打开AWS Console查看资源创建情况，及时登陆各机机器，查看cloud-init运行状态。

```
ssh -i yourkey.pem centos@eip    # 注意 CentOS 镜像的默认用户名是centos
tailf /var/log/cloud-init-output.log
```

5, 测试 & Troubleshooting: 

一切正常的话，找到 Controller 节点的 eip，直接浏览器运行即可获得 OpenStack Dashboard，也可以登陆 Controller 节点运行OpenStack CLI。

```
ssh -i yourkey.pem centos@eip
sudo su -l stack # 切换 stack 账号，方便查看 devstack screen
cd devstack # vim local.conf 查看 devstack 本机配置
screen -r # 查看 devstack 各个screen session。
```




