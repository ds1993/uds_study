## 概述
+ 服务请求原语: 由诊断测试仪应用程序内客户端功能使用，用于向诊断应用层传输请求诊断服务相关数据
+ 服务请求-确认: 由诊断测试仪应用程序内客户端功能使用，用于指示服务请求原语传输的数据通过车辆通行总线成功发送
+ 服务指示原语: 由诊断应用层使用，用于将数据传输至ECU诊断应用程序服务器功能
+ 服务响应原语: 由ECU诊断应用程序内服务器功能使用，用于向诊断应用层传输请求诊断服务相关数据
+ 服务响应-确认原语: 由ECU诊断应用程序内服务器功能使用，用于指示服务响应原语传输的数据通过车辆通信总线成功发送
+ 服务确认原语: 由诊断应用层使用，用于向诊断测试仪应用程序内客户端功能传输数据
## 应用层服务格式
+ 应用层服务默认格式: `A_Mtype = 诊断`，客户端通过`A_SA`和`A_TA`地址参数访问所有服务器而对车辆进行配置
+ 应用层服务远程格式: `A_Mtype = 远程诊断`，客户端通过除`A_SA`和`A_TA`地址参数外的参数访问特定服务器而对车辆进行配置
### 服务请求

	service_name.request (
                         A_MType,
                         A_SA,
                         A_TA,
                         A_TA_type,
                         [A_AE],
                         A_Length,
                         A_Data[,parameter 1, ...],
                         )
### 服务指示

	service_name.indication (
                            A_MType,
                            A_SA,
                            A_TA,
                            A_TA_type,
                            [A_AE],
                            A_Length,
                            A_Data[,parameter 1, ...],
                            )
### 服务响应

	service_name.response (
                          A_MType,
                          A_SA,
                          A_TA,
                          A_TA_type,
                          [A_AE],
                          A_Length,
                          A_Data[,parameter 1, ...],
                          )
### 服务确认

	service_name.confirm (
                         A_MType,
                         A_SA,
                         A_TA,
                         A_TA_type,
                         [A_AE],
                         A_Length,
                         A_Data[,parameter 1, ...],
                         )
+ 如果ECU内服务器功能可成功执行请求诊断服务，则肯定响应和肯定确认原语应与第一个服务数据单元使用
+ 如果ECU内服务器功能未成功执行或不能成功实现请求诊断服务，则否定响应和确认原语应与第二个服务数据单元使用
### 服务请求-确认

	service_name.req_confirm (
                             A_Mtype,
                             A_SA,
                             A_TA,
                             A_TA_type,
                             [A_AE],
                             A_Result,
                             )
### 服务响应-确认

	service_name.rsp_confirm (
                             A_Mtype,
                             A_SA,
                             A_TA,
                             A_TA_type,
                             [A_AE],
                             A_Result,
                             )
## 服务数据单元规范
### A\_Mtype，应用层消息类型
类型: 枚举  
范围: 诊断，远程诊断  
`A_Mtype = 诊断`，service\_name primitive应包括参数`A_SA`，`A_TA`，`A_TA_type`  
`A_Mtype = 远程诊断`，service\_name primitive应包括参数`A_SA`，`A_TA`，`A_TA_type`，`A_AE`
### A\_SA，应用层源地址
类型: 2字节无符号整数值  
范围: 0x0000-0xffff
### A\_TA，应用层目标地址
类型: 2字节无符号整数值  
范围: 0x0000-0xffff
两种寻址方法:  

+ 物理寻址  
+ 功能寻址
### A\_TA\_Type
类型: 枚举  
范围: 物理性，功能性
### A\_Result
类型: 枚举  
范围: ok，错误
### A\_Length
类型: 4字节无符号整数值  
范围: 0-(2^23-1)  
本参数包括待传输/接受的数据的长度
### A\_Data
类型: 字节串  
范围: 不适用  
本参数包括高级层次实体待交换的所有数据
### 选用参数-A\_AE，应用层远程地址
类型: 2字节无符号整数  
范围: 0x0000-0xffff
## 应用层协议
### A\_PDU, 应用层协议数据单元

	A_PDU (
          Mtype
          SA,
          TA,
          TA_type,
          [RA,]
          A_Data = A_PCI + [parameter 1, ...],
          Length,
          )
### PCI，协议控制信息
所有服务请求和`A_PCI`第一个字节不等于0x7f的服务响应:

	A_PCI (
          SI
          )
`A_PCI`第一个字节等于0x7f的服务响应:

	A_PCI (
          NR_SI,
          SI
          )
### SI，服务标识符
类型: 1字节无符号整数值
范围: 0x00-0xff
### NR\_SI，否定响应服务标识符
类型: 1字节无符号整数值
定值: 0x7f
## 诊断和通信管理功能单元
### 诊断会话控制，0x10
#### 请求消息子功能参数
+ 0x00: 保留值
+ 0x01: 默认会话
+ 0x02: 编程会话
+ 0x03: 扩展诊断会话
+ 0x04: 安全系统诊断会话
+ 0x05-0x3f: 保留值
+ 0x40-0x5f: 由车辆制造商规定
+ 0x60-0x7e: 有系统供应商规定
+ 0x7f: 保留值
### ECU重置服务，0x11
#### 请求消息子功能参数
+ 0x00: 保留值
+ 0x01: 硬重置
+ 0x02: 点火钥匙关闭/重置
+ 0x03: 软重置
+ 0x04: 启用快读断电
+ 0x05: 禁用快速断电
+ 0x06-0x3f: 保留值
+ 0x40-0x5f: 由车辆制造商规定
+ 0x60-0x7e: 有系统供应商规定
+ 0x7f: 保留值
#### 肯定响应
+ byte0: 0x51
+ byte1: sub-function
+ byte2: 断电时间  
用于说明服务器将保持断电序列所需待机序列的最短时间  
0x00-0xfe: 0-254秒断电时间  
0xff: 存在故障，或时间不可用
### 安全访问服务，0x27
#### 请求消息子功能参数
+ 0x00: 保留值
+ 0x01，0x03，0x05，0x07-0x41: 请求种子
+ 0x02，0x04，0x06，0x08-0x42: 发送密钥
+ 0x43-0x5e: 保留值
+ 0x5f: ISO26021-2请求种子
为车载点火装置寿命激活结束定义的不同安全级别的请求种子，符合ISO26021-2中的定义
+ 0x60: ISO26021-2发送密钥
为车载点火装置寿命激活结束定义的不同安全级别的发送密钥，符合ISO26021-2中的定义
+ 0x61-0x7e: 由系统供应商规定
+ 0x7f: 保留值
#### 请求种子消息
+ byte 0: sid，0x27
+ byte 1: sub function
+ byte 3-byte n: securityAccessDataRecord
#### 发送key消息
+ byte 0: sid，0x27
+ byte 1: sub function
+ byte 3-byte n: securityKey
#### 请求消息数据参数
+ securityKey: 通过具体的种子值相对应的安全算法生成的值
+ securityAccessDataRecord: 请求种子信息时使用者可以选择该参数记录向服务器传输数据，其中可能包含在服务器经过验证的客户端标识
#### 肯定响应消息
+ byte 0: sid，0x67
+ byte 1: sub function，securityAccessType
+ byte 3-byte n: securitySeed  
#### 肯定响应消息数据参数
+ securityAccessType: 对请求消息的子功能参数第6至0位的回显
+ securitySeed: 种子参数是服务器发送的数据值，并在计算访问安全所需的密钥时供客户端使用
#### 消息流
+ 服务器处于锁定状态:  
2701->6701->2702->6702
+ 服务器处于解锁状态:  
2701->670100
## 数据传输功能单元
### 按标识符写数据服务，0x2e
#### 请求消息
+ byte 0: sid，0x2e
+ byte 1，byte 2: dataIdentifier
+ byte 3-byte n: dataRecord
#### 请求消息数据参数
+ dataIdentifier: 本参数确定了客户端请求写入的服务器数据记录
+ dataRecord: 本参数提供了与客户端请求写入的dataIdentifier相关的数据记录
#### 肯定响应消息
+ byte 0: WriteDataByIdentifier，0x6e
+ byte 1，byte 2: dataIdentifier，请求对应位置回显
### 清除诊断信息服务，0x14
#### 请求消息
+ byte 0: sid，0x14
+ byte 1-byte 3: groupOfdTC
#### 请求消息数据参数
+ groupOfdTC: 本参数包含一个3字节值，该值可说明DTC组或待清除的特殊DTC
#### 肯定响应消息
+ byte 0: sid, 0x54
## 上传下载功能单元
### 请求下载服务，0x34
#### 请求消息
+ byte 0: sid，0x34
+ byte 1: dataFormatIdentifier
+ byte 2: addressAndLengthFormatIdentifier
+ byte 3-byte m: memoryAdderss
+ byte m+1-byte n: memorySize 
#### 请求消息数据参数
+ dataFormatIdentifier: 此数据参数是一个单字节值，每个半字节均单独编码，高半字节指定压缩法，低半字节指定加密法，字节值由车辆制造商规定
+ addressAndLengthFormatIdentifier: 此参数是一个单字节值，每个半字节均单独编码:  
第7至第4位: memorySize参数的长度  
第3至第0位: memoryAddress参数的长度
+ memoryAdderss: 数据被写入服务器内存的起始地址
+ memorySize: 服务器应利用此参数对比内存大小与执行传输数据服务期间传输的数据总量
#### 肯定响应消息
+ byte 0: sid，0x74
+ byte 1: lengthFormatIdentifier
+ byte 2-byte n: maxNumberOfBlockLength
#### 肯定响应消息数据参数
+ lengthFormatIdentifier: 此数据是一个单字节值，每个半字节均单独编码:  
第7至第4位: maxNumberOfBlockLength参数的长度  
第3至第0位: 文件保留，设置为0
+ maxNumberOfBlockLength: 肯定响应消息利用此参数通知客户端其发出的传输数据请求中应包含多少数据字节
### 请求上传服务，0x35
#### 请求消息
+ byte 0: sid, 0x35
+ byte 1: dataFormatIdentifier
+ byte 2: addressAndLengthFormatIdentifier
+ byte 3-byte m: memoryAdderss
+ byte m+1-byte n: memorySize 
#### 请求消息数据参数
+ dataFormatIdentifier: 此数据参数是一个单字节值，每个半字节均单独编码，高半字节指定压缩法，低半字节指定加密法，字节值由车辆制造商规定
+ addressAndLengthFormatIdentifier: 此参数是一个单字节值，每个半字节均单独编码:  
第7至第4位: memorySize参数的长度  
第3至第0位: memoryAddress参数的长度
+ memoryAdderss: 是服务器内存的起始地址，从该地址检索数据
+ memorySize: 服务器应利用此参数对比内存大小与执行传输数据服务期间传输的数据总量
#### 肯定响应消息
+ byte 0: sid，0x75
+ byte 1: lengthFormatIdentifier
+ byte 2-byte n: maxNumberOfBlockLength
#### 肯定响应消息数据参数
+ lengthFormatIdentifier: 此数据是一个单字节值，每个半字节均单独编码:  
第7至第4位: maxNumberOfBlockLength参数的长度  
第3至第0位: 文件保留，设置为0
+ maxNumberOfBlockLength: 肯定响应消息利用此参数通知客户端服务器发出的传输数据请求肯定响应消息中包含了多少数据字节
### 传输数据服务，0x36
#### 请求消息
+ byte 0: sid，0x36
+ byte 1: blockSequenceCounter
+ byte 2-byte n: transferRequestParameterRecoed
#### 请求消息数据参数
+ blockSequenceCounter: 参数值从0x01开始，第一个传输数据请求在0x34或0x35服务之后，0x36服务每增加一次，参数值加1，参数值为0xff时，blockSequenceCounter翻滚并从0x00开始发出下一条传输数据请求消息
+ transferRequestParameterRecoed: 此参数记录包含服务器要求的支持数据传输的参数，参数格式和长度由车辆制造商规定(下载时，传输数据)
#### 肯定响应消息
+ byte 0: sid，0x76
+ byte 1: blockSequenceCounter
+ byte 2-byte n: transferResponseParameterRecoed
#### 肯定响应消息数据参数
+ blockSequenceCounter: 此参数是请求消息的blockSequenceCounter参数的反射
+ transferResponseParameterRecoed: 此参数应包含客户端支持数据传输要求的参数，参数格式和长度由车辆制造商规定(下载时，transferResponseParameterRecoed可以包含服务器计算出的校验和，上传时，transferResponseParameterRecoed可以包含上传数据)
### 请求传输终止服务，0x37
#### 请求消息
+ byte 0: sid，0x37
+ byte 1-byte n: transferRequestParameterRecoed
#### 请求消息数据参数
+ transferRequestParameterRecoed: 此参数记录包含服务器要求的支持数据传输的参数，参数格式和长度由车辆制造商规定
#### 肯定响应消息
+ byte 0: sid，0x77
+ byte 1-byte n: transferResponseParameterRecoed
#### 肯定响应消息数据参数
transferResponseParameterRecoed: 此参数应包含客户端支持数据传输要求的参数，参数格式和长度由车辆制造商规定
### 数据传输数据流
+ 客户端下载到服务器: 0x34->0x74->0x36->0x76->0x37->0x77
+ 服务器上传到客户端: 0x35->0x75->0x36->0x76->0x37->0x77