# 数据库需求描述

该数据库旨在支持一个功能部署和主机管理系统。以下是主要实体及其关系的描述：

## Function（功能）
- 用途：存储可部署的功能信息。
- 字段：
  - name: 功能名称，唯一
  - executor: 执行者

## Host（主机）
- 用途：存储主机信息，包括物理位置和连接详情。
- 字段：
  - pier_name: 码头名称
  - equip_number: 设备编号
  - custom_name: 客户自定义名称（可选）
  - ip: IP地址（可选）
  - port: 端口（可选）
  - ip_lan: 局域网IP地址
  - port_lan: 局域网端口（默认22）
  - user: 用户名
  - password: 密码
- 注意：pier_name、equip_number和custom_name的组合必须唯一

## Deployment（部署）
- 用途：定义功能部署到特定主机的配置，包括跳板机设置。
- 字段：
  - name: 部署名称
  - host: 关联的主机（外键）
  - updatable_function: 可更新的功能（外键）
  - jumpbox1 到 jumpbox5: 最多5个跳板机（外键，可选）
  - jumpbox1_tunnel 到 jumpbox5_tunnel: 每个跳板机的隧道类型（FRP或LAN）
- 注意：name和updatable_function的组合必须唯一

## MiscConfig（杂项配置）
- 用途：存储与特定功能相关的各种配置信息。
- 字段：
  - function: 关联的功能（外键）
  - docker_name: Docker名称
  - remote_root_path: 远程根路径
  - remote_docker_path: 远程Docker路径
  - remote_python_path: 远程Python路径
  - local_python_path: 本地Python路径
- 注意：function和所有路径字段的组合必须唯一

## FileTransfer（文件传输）
- 用途：定义与功能相关的文件传输操作。
- 字段：
  - function: 关联的功能（外键）
  - source: 源路径
  - destination: 目标路径
  - execution_priority: 执行优先级（默认0）
- 注意：function、source和destination的组合必须唯一

## LocalCmd（本地命令）
- 用途：存储与功能相关的本地执行命令。
- 字段：
  - function: 关联的功能（外键）
  - cmd: 命令内容
  - execution_priority: 执行优先级（默认1）
- 注意：function和cmd的组合必须唯一

## RemoteCmd（远程命令）
- 用途：存储与功能相关的远程执行命令。
- 字段：
  - function: 关联的功能（外键）
  - cmd: 命令内容
  - execution_priority: 执行优先级（默认1）
- 注意：function和cmd的组合必须唯一

## CythonizeRepo（Cython仓库）
- 用途：定义需要进行Cython编译的仓库信息。
- 字段：
  - function: 关联的功能（外键）
  - git_url: Git仓库URL
  - package_name: 包名
  - branch: 分支（默认'master'）
  - commit: 提交哈希（默认'HEAD'）
  - execution_priority: 执行优先级（默认-2）
- 注意：function和package_name的组合必须唯一

## StartUp（启动配置）
- 用途：定义功能的启动命令和相关配置。
- 字段：
  - function: 关联的功能（外键）
  - in_docker_host: 在Docker中的主机（外键，可选）
  - name: 启动配置名称
  - cmd: 启动命令
  - restart_now: 是否立即重启（布尔值，默认False）
  - execution_priority: 执行优先级（默认-1）
- 注意：function和name的组合必须唯一

## LabelingData（图像标定）
- 用途：存储与图像标定相关的配置信息。
- 字段：
  - function: 关联的功能（外键）
  - image_path: 图像路径
  - json_path: 标定的JSON文件路径

## 通用注意事项
1. 所有模型都应实现`__str__`方法，以提供人类可读的表示。
2. 使用外键关系来建立模型之间的连接，特别是与Function和Host模型的关联。
3. 某些模型使用execution_priority字段来控制相关操作的执行顺序。
4. 大多数模型都有唯一性约束，确保某些字段组合的唯一性。
5. 某些字段允许为空（null=True）或空白（blank=True），提供了灵活性。
6. 使用choices参数为某些字段（如tunnel字段）定义预设选项。

实现这个数据库结构时，应确保正确设置字段类型、关系和约束，以维护数据的完整性和一致性。