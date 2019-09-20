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
### A_Mtype，应用层消息类型
类型: 枚举  
范围: 诊断，远程诊断  
`A_Mtype = 诊断`，service\_name primitive应包括参数`A_SA`，`A_TA`，`A_TA_type`  
`A_Mtype = 远程诊断`，service\_name primitive应包括参数`A_SA`，`A_TA`，`A_TA_type`，`A_AE`
### A_SA，应用层源地址
类型: 2字节无符号整数值  
范围: 0x0000-0xffff
### A_TA，应用层目标地址
类型: 2字节无符号整数值  
范围: 0x0000-0xffff
两种寻址方法:  

+ 物理寻址  
+ 功能寻址
### A_TA_Type
类型: 枚举
范围: 物理性，功能性