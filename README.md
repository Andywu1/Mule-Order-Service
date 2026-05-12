# Mule-Order-Service
本项目是基于Mule 4.11+Anypoint Studio 7.24 开发的订单管理系统，实现了订单、商品、支付相关的基础CRUD接口，严格遵循三层架构设计，无额外依赖，可直接编译运行。

一、项目配置说明

1.核心配置

• HTTP端口：8081（可通过application.properties修改，但不建议改动，避免接口测试异常）

• 编码格式：UTF-8（所有XML文件均采用UTF-8编码，避免中文乱码）

• 接口路径：所有接口均以 http://localhost:8081 为基础路径，具体接口见测试部分

• 项目无需额外配置，无需修改pom.xml

2.项目结构

项目采用三层架构设计，分为4个核心XML文件，职责清晰，便于维护和提交检查，具体结构如下：

• src/main/mule/api.xml：入口层，负责HTTP监听和接口路由，统一接收所有请求并分发到对应业务流程

• src/main/mule/experience.xml：体验层，负责请求校验（权限、参数）和数据格式转换，保障请求合法性

• src/main/mule/process.xml：业务流程层，负责核心业务逻辑编排，整合各子流程，实现订单CRUD

• src/main/mule/system.xml：系统层，模拟外部服务调用（商品、支付），实现重试机制，因本地始终无法连接到特定dependency的服务器，会导致编译报错，故无ObjectStore依赖

• src/test/munit/order-test.xml：单元测试文件，满足作业单元测试要求

二、项目运行步骤
1) 导入项目至 Anypoint Studio并编译
2) 可能是Postman版本问题，题目自带的data_samples_candidate.json无法直接导入 我新做了一版类似的json测试文件 路径在\\mule-order-service\src\test\resources\test.json
3) 在Postman里import文件内容 可以直接导入7个api和他们的header以及body(if any)。View more actions -- Run 可以一键跑所有api
4) 按照“创建订单→查询订单→更新订单→删除订单→商品相关→支付”的顺序测试，所有接口均可正常返回结果，无乱码、无报错

三、设计说明

• 架构设计
严格遵循三层架构（体验层→业务流程层→系统层），各层职责分离，符合要求，具体设计
如下：
1. 体验层（experience.xml）：负责前置校验和数据转换，包括Token权限校验（避免未授权访
问）、订单/商品参数校验（确保必填字段不为空）、订单数据格式化（统一返回结构）。
2. 业务流程层（process.xml）：核心业务逻辑中心，整合各子流程，实现订单、商品、支付的CRUD
操作，调用系统层的外部服务，完成业务闭环。
3. 系统层（system.xml）：模拟外部服务调用，包括商品服务（GET /products/{sku}）、支付服务
（POST /payments），并实现重试机制（最多2次重试，间隔500ms），同时处理简单的错误分
级，确保服务调用稳定性。
• 核心功能实现
项目实现了大部分要求的功能，具体如下：
1. 接口路由：通过api.xml实现订单、商品、支付相关接口的路由，支持POST、GET、PUT、
 DELETE四种请求方式，404路径返回统一错误提示。
2. 权限校验：所有接口均需携带Authorization请求头（Bearer Token），未携带则返回401未授权，
满足权限校验要求。
3. 参数校验：对订单orderId、商品s ku等必填字段进行校验，为空则返回400错误提示，确保请求数
据合法。
4. 外部服务调用与重试：调用商品服务和支付服务时，实现最多2次重试，间隔500ms，模拟真实场
景下的服务容错处理。
5. 数据转换：对订单数据进行格式化处理，计算订单总价，统一返回格式，确保响应数据规范。
6. 单元测试：编写单元测试用例，针对订单创建流程进行测试，验证业务流程的正确性，满足作业单
元测试要求。

四.接口说明

所有接口均已适配Mule 4.11，无语法错误，可直接测试，核心接口如下：
• 订单接口：POST /orders（创建订单）、GET /orders/{orderId}（查询订单）、PUT
/orders/{orderId}（更新订单）、DELETE /orders/{orderId}（删除订单）
• 商品接口：POST /products（创建商品）、GET /products/{s ku}（查询商品）
• 支付接口：POST /payments（处理支付）

五.学习资料

B站:https://www.bilibili.com/video/BV1Te411r72K?spm_id_from=333.788.videopod.sections&vd_source=8f87152b9b128ba4046b708f213a39f5
Mule:https://docs.mulesoft.com/mule-runtime/latest/mule-app-dev-hellomule



