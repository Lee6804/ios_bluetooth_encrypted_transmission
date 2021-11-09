# iOS ble蓝牙加密传输
##### 1、流程图
![图片](https://user-images.githubusercontent.com/20941758/140888056-571f6c99-c020-4c54-973a-0a3bb27394fe.png)
##### 2、流程
###### 2.1 发现服务和特征
连接上蓝牙后，SDK执行发现设备服务→发现服务下特征操作。设备为了向下兼容，包含了两个服务：F100和F200。SDK在发现服务的时候需要进行判断，如果发现的服务中包含了F200的服务，则SDK需要执行加密流程。
非加密service：F100，此服务有两个特征characteristic：写特征F101、读特征F102
加密service：F200，此服务有两个特征characteristic：写特征F201、读特征F202
###### 2.2 ECDH（secp192k1）秘钥协商
两端均使用ECDH的方式生成秘钥对，在分别拿到对方的公钥时，再跟自己生成的私钥去进行秘钥协商，得到一个24位的协商结果，也就是AES加解密需要用到的加密key。
iOS配网SDK通过使用第三方库GMObjC完成秘钥协商，具体代码如下：
`使用GMObjC库生成的公钥在前面补04为49位，私钥在前面补了0000000000000000为32位，故在发送客户端公钥给设备时需要去掉补的04，拿到设备发过来的公钥时需要补上04再进行秘钥协商`
###### 2.3 AES加解密
