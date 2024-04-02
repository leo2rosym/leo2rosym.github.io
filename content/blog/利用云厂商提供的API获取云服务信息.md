---
date: 2024-04-2 09:00:00
tags:
  - python
title: 利用云厂商提供的API获取云服务信息
---

## 华为云 Python SDK
传送门：[https://console.huaweicloud.com/apiexplorer/#/sdkcenter/ECS?lang=Python](https://console.huaweicloud.com/apiexplorer/#/sdkcenter/ECS?lang=Python)

目标：获取华为云ecs服务器信息，输出以下格式：
```
Name                Public IP       Server Flavor   Os         Private IP
ecs-test-5          8.1.23.45      8核 16GB       Linux      192.168.1.23
ecs-test-4          8.2.34.56      8核 16GB       Linux      192.168.2.34
ecs-test-3          8.3.45.67      8核 16GB       Linux      192.168.3.45
ecs-test-2          8.4.56.78      4核 8GB        Linux      192.168.4.56
ecs-test-1          8.5.67.89      8核 16GB       Linux      192.168.5.67
```

华为云sdk代码
```
import json
import os
from huaweicloudsdkcore.auth.credentials import BasicCredentials
from huaweicloudsdkecs.v2 import *
from huaweicloudsdkecs.v2.region.ecs_region import EcsRegion
from huaweicloudsdkcore.exceptions import exceptions

# 你的华为云凭证信息
ak = ''
sk = ''
project_id = ''


# 从server_info字典中提取IP地址信息
def get_ecs_ips(server_info):
    # 定义两个列表来存储固定IP和浮动IP
    fixed_ips = []
    floating_ips = []

    # 遍历addresses键下的内容
    for network_id, ips in server_info.get('addresses', {}).items():
        for ip_info in ips:
            ip_type = ip_info.get('OS-EXT-IPS:type')
            ip_address = ip_info.get('addr')

            # 根据IP类型将IP地址添加到相应的列表中
            if ip_type == 'fixed':
                fixed_ips = ip_address
            elif ip_type == 'floating':
                floating_ips = ip_address

    return fixed_ips, floating_ips


def get_flavor_info(server_info):
    # 提取flavor信息
    flavor = server_info.get('flavor', {})
    # 提取CPU核心数
    vcpus = flavor.get('vcpus', 'Unknown')
    # 提取内存大小，并将MB转换为GB
    ram_mb = flavor.get('ram', '0')
    # 确保ram_mb是数字格式
    ram_mb = int(ram_mb) if ram_mb.isdigit() else 0
    # 转换为GB，注意1GB = 1024MB
    ram_in_gb = ram_mb / 1024

    # 构建规格信息字符串
    flavor_info = f"{vcpus}核 {ram_in_gb:.0f}GB"  # 使用：.0f来格式化为不带小数的整数

    return flavor_info


if __name__ == "__main__":
    credentials = BasicCredentials(ak, sk, project_id)

    # Create the service client
    client = EcsClient.new_builder() \
        .with_credentials(credentials) \
        .with_region(EcsRegion.value_of("ap-southeast-1")) \
        .build()

    try:
        request = ListServersDetailsRequest(limit=200)
        response = client.list_servers_details(request)
        print("Name\tPublic IP\tServer Flavor\tOs\tPrivate IP")

        for server in response.servers:
            server_info = json.loads(str(server))

            fixed_ip_addresses, floating_ip_addresses = get_ecs_ips(server_info)
            flavor_info = get_flavor_info(server_info)

            print(
                f"{server_info['name']}\t{floating_ip_addresses}\t{flavor_info}\t{server_info['metadata']['os_type']}\t{fixed_ip_addresses}")

    except exceptions.ClientRequestException as e:
        print(e.status_code)
        print(e.request_id)
        print(e.error_code)
        print(e.error_msg)

```

阿里云sdk代码
```
# -*- coding: utf-8 -*-
# This file is auto-generated, don't edit it. Thanks.

from aliyunsdkcore.client import AcsClient
from aliyunsdkecs.request.v20140526 import DescribeInstancesRequest
import json


def get_ecs_data(access_key_id, access_key_secret, region_id, max_instances=500):
    cli = AcsClient(access_key_id, access_key_secret, region_id)
    ecs_info = []
    page_size = 50  # 每次请求的最大实例数
    page_number = 1

    while True:
        # 创建请求对象
        res = DescribeInstancesRequest.DescribeInstancesRequest()
        # 设置请求参数
        res.set_accept_format('json')
        res.set_PageNumber(page_number)
        res.set_PageSize(page_size)

        # 发送请求并获取响应
        response = cli.do_action_with_exception(res)

        # 解析响应数据
        result = json.loads(response)
        instances = result.get('Instances', {}).get('Instance', [])

        # 如果没有实例或者获取的实例数小于一页，则结束循环
        if not instances or len(instances) < page_size:
            break

        # 添加实例信息到总列表
        ecs_info.extend(instances)

        # 准备下一次循环
        page_number += 1

        # 如果获取的实例数量达到最大值，则结束循环
        if len(ecs_info) >= max_instances:
            break

    # 定义输出格式模板
    output_template = "{instance_name} {os_type} {server_flavor} {private_ip} {public_ip} {creation_time}"
    print("Name\tOs\tServer Flavor\tPrivateIP\tPublicIP\tCreateTime")
    # 遍历ECS实例信息并输出
    for index, instance in enumerate(ecs_info, 1):
        os_type = instance["OSName"].split()[0]  # 假设操作系统类型是OSName字段的首个单词
        cpu = f"{instance['Cpu']}" if instance['Cpu'] else "N/A"
        memory = instance["Memory"]
        if memory and memory >= 1024:
            memory = f"{memory / 1024:.0f}G"
        elif memory:
            memory = f"{memory:.0f}M"
        else:
            memory = "N/A"
        server_flavor = f"{cpu}核{memory}" if memory else "N/A"
        private_ip = instance["VpcAttributes"]["PrivateIpAddress"]["IpAddress"][0] if \
            instance["VpcAttributes"]["PrivateIpAddress"]["IpAddress"] else "N/A"
        public_ip = instance["PublicIpAddress"]["IpAddress"][0] if instance["PublicIpAddress"]["IpAddress"] else "N/A"
        instance_name = instance["InstanceName"]
        creation_time = instance["CreationTime"]

        # 输出格式化后的信息
        print(output_template.format(
            instance_name=instance_name,
            os_type=os_type,
            server_flavor=server_flavor,
            private_ip=private_ip,
            public_ip=public_ip,
            creation_time=creation_time
        ))


# 使用示例
access_key_id = ''
access_key_secret = ''
get_ecs_data(access_key_id, access_key_secret, 'ap-southeast-1')

```

