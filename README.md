# DevStack on AWS

该 Ansible 脚本一次运行将在在 AWS 创建一个由 4 台 EC2 虚拟机节组成的 DevStack 集群，分别是 Controller，Network，Computer1 和 Computer2。

## 使用方法：

1, 设计环境变量：

```bash
export AWS_ACCESS_KEY_ID='abc'
export AWS_SECRET_ACCESS_KEY='def'
export AWS_REGION='ap-northeast-2' # AWS 韩国Region
```


2, 打开 one-click-devstack-on-aws.yml 中设置合适的Spot Instance价格，Instance Type推荐使用c4.4xlarge，及keypair密钥名。

```bash
...
  vars:
    instance_type: c4.4xlarge   # 推荐使用c4.4xlarge。
    keypair: macpro-seoul       # 设置自己的keypair
    image: ami-c74789a9         # 保持不变，默认为CentOS 7
    spot_price: 0.113           # 在AWS Console中查看Spot Instance的Price History，根据市场情况设置合理的价格
...
```

3, 运行：

```bash
ansible-playbook demo_vpc.yml -vvv
```

4, 打开AWS Console查看资源创建情况，及时登陆各机机器，查看cloud-init运行状态。

```
tailf /var/log/cloud-init-output.log
```

5, 测试

一切正常的话，找到 Controller 节点的 eip，直接运行即可获得 OpenStack Dashboard，也可以登陆 Controller 节点运行OpenStack CLI。

