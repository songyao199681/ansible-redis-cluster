# &#x20; 使用 Ansible 自动部署 Redis 集群！使用 systemd 在同一服务器上部署多个实例，并享受交互式初始化提示。

## 前置条件
- Ansible 安装在运行 playbook 的计算机上。
- 具有 SSH 访问权限的目标服务器。
- 确保目标服务器上打开必要的端口（例如，7001、8002）。

# Usage 用法
## 1.克隆此存储库：
```shell
git clone https://gitee.com/song-yao/remote-shell-script-backup.git
cd /shell/remote-shell-script-backup/ansible_role/ansible-redis-cluster/playbooks/
```
## 2.更新 inventory.ini 和 playbook.yml 文件：

**希望每个实例上有 3 个 Redis 服务器。**
```shell
[redis_servers]
server1 ansible_host=10.0.0.129
server2 ansible_host=10.0.0.130
server3 ansible_host=10.0.0.131
```

## 3.运行 playbook 并等待集群初始化确认
```shell
ansible-playbook -i inventory.ini playbook.yml
```
## 4.等待它完成
```shell
TASK [../roles/redis_cluster : Display Redis cluster initialization command] *******************************************************************
ok: [server1] => {
    "msg": "/usr/local/redis/bin/redis-cli --cluster create 10.0.0.129:7001 10.0.0.129:8002 10.0.0.130:7001 10.0.0.130:8002 10.0.0.131:7001 10.0    .0.131:8002 --cluster-replicas 1 -a 123456 --cluster-yes"
}

TASK [../roles/redis_cluster : Confirm Initialization] *****************************************************************************************
[../roles/redis_cluster : Confirm Initialization]
Redis cluster configuration complete. Do you want to initialize the cluster now? (y/n):
```

## 5.卸载集群
```shell
ansible-playbook -i inventory.ini uninstall.yml
TASK [Confirm uninstallation] **********************************************************************************************************************
[Confirm uninstallation]
Are you sure you want to uninstall Redis on all servers? (y/n):
ok: [server1 -> localhost]

TASK [Set uninstall confirmation as a fact on all hosts] *******************************************************************************************
ok: [server1 -> localhost]

TASK [Fail if uninstallation is not confirmed] *****************************************************************************************************
fatal: [server1]: FAILED! => {"changed": false, "msg": "Uninstallation aborted."}
fatal: [server2]: FAILED! => {"changed": false, "msg": "Uninstallation aborted."}
fatal: [server3]: FAILED! => {"changed": false, "msg": "Uninstallation aborted."}

```
