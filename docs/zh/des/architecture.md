# 数据交易2.0技术架构

## 2.1 架构图2-1

![](./assets/import3.png)

## 2.2 参与者说明

* Merchant: 商户，即数据买方;

* DES\(Data Exchange Service\): 数据交换服务，提供预扣费、数据暂存等功能;

* Datasource: 数据源，即数据卖方;

## 2.3 过程描述

下面描述一个数据交易的完整流程:

步骤1:【数据源】向DES注册服务
步骤2:【商户】调用_产品信息接口_
步骤3:【DES】返回产品信息以及在线数据源列表
步骤4:【商户】调用_交易创建接口_创建数据交易，指定查询数据源，并使用数据源public\_key对入参进行加密
步骤5:【DES】验证商户签名,验证余额是否充足,创建数据交易，生成唯一request\_id并返回
步骤6:【DES】带上商户发起数据交易消息，向指定数据源发送数据交易请求
步骤7:【数据源】接收请求，解密消息体，处理请求，使用买方public_key加密数据后回传DES
步骤8:【DES】拿到加密数据，存IPFS，调用代理记账
步骤9:【DES】数据和request\_id建立映射
步骤10:【商户】通过request\_id获取数据