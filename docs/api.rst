接口
===============

初始化请求参数
------------------
::

    // 构建请求
    HttpRequest httpRequest = HttpUtil.createPost(uri + apiName);
    // 构建请求头
    Map<String ,String> headers = new HashMap<>();
    headers.put("request-id", requestId);
    headers.put("app-id", appId);
    headers.put("nonce",nonce);
    headers.put("signature",signatureData);
    httpRequest.addHeaders(headers);

hash存证（sha256） - /attestation/hash
------------------------------------
用户进行hash存证。

请求参数
^^^^^^^^^^^^^^^
=================  ======================================= ================
参数名 				描述                                    是否可选
=================  ======================================= ================
label               文件标签                                    非必选
hashItemList        HashItem对象列表                            必选
HashItem.fileName   文件名                                      必选
HashItem.hash       文件hash                                    必选

=================  ======================================= ================

返回的data
^^^^^^^^^^^^^^

调用hash存证接口成功后会返回存证id列表

===================  ================================
字段名 				    描述
===================  ================================
list                    bean对象列表
bean.hash               文件hash
bean.ano                存证id
===================  ================================

以java为例::

    // API path
    String apiName = "/attestation/hash";
    HttpRequest httpRequest = createRequestPost(apiName);
    // 构建请求参数
    List<EvidenceHashParam.HashItem> list = new ArrayList<>();
    EvidenceHashParam.HashItem hashInfo1 = new EvidenceHashParam.HashItem();
    hashInfo1.setFileName("test1");
    hashInfo1.setHash("98df1f1dfb3b1a123c1517912dc70447aa61c6be532ac99de973abb6219e1653");
    list.add(hashInfo1);
    EvidenceHashParam evidenceHashParam = new EvidenceHashParam();
    evidenceHashParam.setLabel("标签");
    evidenceHashParam.setHashItemList(list);
    httpRequest.body(JSONUtil.toJsonStr(evidenceHashParam));
    String result;
    try (HttpResponse httpResponse = httpRequest.execute()) {
        result = httpResponse.body();
    }

返回结果示例:
a.接口调用成功，则返回JSON数据示例为：::

    {
      "flag": true,
      "data": {
        "result": [
          {
            "hash": "98df1f1dfb3b1a123c1517912dc70447aa61c6be532ac99de973abb6219e1653",
            "ano": 840175805404684288
          }
        ]
      },
      "statusCode": "000000",
      "errorMessage": "操作成功"
    }

b.接口调用失败，则返回JSON数据示例为：::

    {
      "flag": false,
      "statusCode": "500087",
      "errorMessage": "hash错误"
    }

文件存证  - /attestation/file
------------------------------------
用户进行文件存证。

请求参数
^^^^^^^^^^^^^^^
=================  ======================================= ================
参数名 				描述                                    是否可选
=================  ======================================= ================
label               文件标签                                    非必选
file                文件                                      必选
remark               备注                                    非必选
=================  ======================================= ================

返回的data
^^^^^^^^^^^^^^

调用文件存证接口成功后会返回存证id及文件hash

===================  ================================
字段名 				    描述
===================  ================================
fileHash                文件hash
ano                     存证id
===================  ================================

以java为例::

    // API path
    String apiName = "/attestation/file";
    HttpRequest httpRequest = createRequestPost(apiName);
    // 构建请求参数
    httpRequest.form("file",new File("/tmp/123.jpg"));
    httpRequest.form("label","标签");
    String result;
    try (HttpResponse httpResponse = httpRequest.execute()) {
        result = httpResponse.body();
    }

返回结果示例:
a.接口调用成功，则返回JSON数据示例为：::

    {
      "flag": true,
      "data": {
        "fileHash": "ef83964c1bde029d3e1b5018068a6073aadd97f3ec0eacce3b1cec61321466f0",
        "ano": "867774897051803649"
      },
      "statusCode": "000000",
      "errorMessage": "操作成功"
    }

b.接口调用失败，则返回JSON数据示例为：::

    {
      "flag": false,
      "statusCode": "500003",
      "errorMessage": "文件上传失败,请稍候重试"
    }

网页取证 - /attestation/web
------------------------------------
用户进行网页取证。

请求参数
^^^^^^^^^^^^^^^
=================  ======================================= ================
参数名 				描述                                    是否可选
=================  ======================================= ================
label               文件标签                                    非必选
url                 取证链接                                    必选
name                取证名称                                    必选
remark               备注                                    非必选
=================  ======================================= ================

返回的data
^^^^^^^^^^^^^^

调用网页存证接口成功后会返回存证id及取证链接

===================  ================================
字段名 				    描述
===================  ================================
url                     取证链接
ano                     存证id
===================  ================================

以java为例::

    // API path
    String apiName = "/attestation/web";
    HttpRequest httpRequest = createRequestPost(apiName);
    // 构建请求参数
    ApiWebAttestationParam apiWebAttestationParam = new ApiWebAttestationParam();
    apiWebAttestationParam.setLabel("标签");
    apiWebAttestationParam.setUrl("https://www.baidu.com");
    apiWebAttestationParam.setName("取证-百度");
    httpRequest.body(JSONUtil.toJsonStr(apiWebAttestationParam));
    String result;
    try (HttpResponse httpResponse = httpRequest.execute()) {
        result = httpResponse.body();
    }

返回结果示例:
a.接口调用成功，则返回JSON数据示例为：::

    {
      "flag": true,
      "data": {
        "url": "https://www.baidu.com",
        "ano": "867775689221611521"
      },
      "statusCode": "000000",
      "errorMessage": "操作成功"
    }

b.接口调用失败，则返回JSON数据示例为：::

    {
      "flag": false,
      "statusCode": "520000",
      "errorMessage": "API套餐不足"
    }


存证列表 - /attestation/list
----------------------

获取存证列表

请求参数
^^^^^^^^^^^^^^^
=================  ============================================ ============
参数名 				描述                                          是否可选
=================  ============================================ ============
attestationType     存证类型 0.file,1.web,8.hash  默认为8             非必选
startTime           开始时间                                         非必选
endTime             结束时间                                         非必选
pageNum             当前页码                                         非必选
pageSize            每页显示数量 最大20                                非必选
fileName            文件名称                                         非必选
fileLabel           文件标签                                         非必选
fileHash            文件hash                                         非必选
channel             存证渠道：1.自助存证  2.api存证                     非必选
=================  ============================================ ============


返回的data
^^^^^^^^^^^^^^

调用存证获取列表接口成功后会返回存证列表

=====================  ===========================================================
字段名 				    描述
=====================  ===========================================================
pageNum                 当前页
pageSize                每页显示数量
pages                   总页数
size                    当前返回数量
total                   总数
list                    存证数据对象info
info.ano                存证编号
info.fileHash           文件hash
info.fileLabel          文件标签
info.fileName           文件名
info.createTime         创建时间
info.attestationType    存证类型 0.file,1.web,8.hash
info.status             1.上链中,2.上链失败,3.上链成功
=====================  ===========================================================


以java为例::

    // API path
    String apiName = "/attestation/list";
    HttpRequest httpRequest = createRequestPost(apiName);
    // 构建请求参数
    Map<String ,Object> body = new HashMap<>();
    httpRequest.body(JSONUtil.toJsonStr(body));
    String result;
    try (HttpResponse httpResponse = httpRequest.execute()) {
        result = httpResponse.body();
    }

返回结果示例:
a.接口调用成功，则返回JSON数据示例为：::

    {
      "flag": true,
      "data": {
        "total": 3,
        "list": [
          {
            "ano": "842425553641676801",
            "fileName": "test1",
            "fileLabel": "标签",
            "createTime": "2023-03-14 15:38:23",
            "fileHash": "98df1f1dfb3b1a123c1517912dc70447aa61c6be532ac99de973abb6219e1653",
            "attestationType": 8,
            "status": 3
          },
          {
            "ano": "842357643523006464",
            "fileName": "test1",
            "fileLabel": "标签",
            "createTime": "2023-03-14 11:08:32",
            "fileHash": "98df1f1dfb3b1a123c1517912dc70447aa61c6be532ac99de973abb6219e1653",
            "attestationType": 8,
            "status": 3
          },
          {
            "ano": "840600367778897920",
            "fileName": "test1",
            "fileLabel": "标签",
            "createTime": "2023-03-09 14:45:11",
            "fileHash": "98df1f1dfb3b1a123c1517912dc70447aa61c6be532ac99de973abb6219e1653",
            "attestationType": 8,
            "status": 3
          }
        ],
        "pageNum": 1,
        "pageSize": 20,
        "size": 3,
        "startRow": 1,
        "endRow": 3,
        "pages": 1,
        "prePage": 0,
        "nextPage": 0,
        "isFirstPage": true,
        "isLastPage": true,
        "hasPreviousPage": false,
        "hasNextPage": false,
        "navigatePages": 8,
        "navigatepageNums": [
          1
        ],
        "navigateFirstPage": 1,
        "navigateLastPage": 1
      },
      "statusCode": "000000",
      "errorMessage": "操作成功"
    }

b.接口调用失败，则返回JSON数据示例为：::

    {
      "flag": false,
      "statusCode": "500000",
      "errorMessage": "系统错误"
    }

详情查询 - /attestation/info
----------------------

查询存证详情。

请求参数
^^^^^^^^^^^^^^^

=================  ======================================= ================
参数名 				描述                                    是否可选
=================  ======================================= ================
ano                  存证编号                                   必选
=================  ======================================= ================

返回的data
^^^^^^^^^^^^^^

调用存证详情成功后会返回详情数据

=======================  ================================
字段名 				        描述
=======================  ================================
ano                         存证编号
fileHash                    文件hash
label                       文件标签
fileName                    文件名
createTime                  创建时间
attestationType             存证类型 0.file,1.web,8.hash
status                      1.上链中,2.上链失败,3.上链成功
pdfUrl                      存证证书下载地址
blockchainHash              链hash
evidenceUrl                 证据地址
remark                       备注
evidenceName                    取证名称（网页取证）
url                        取证链接（网页取证）
=======================  ================================


以java为例::

    // API path
    String apiName = "/attestation/info";
    HttpRequest httpRequest = createRequestPost(apiName);
    // 构建请求参数
    Map<String ,Object> body = new HashMap<>();
    body.put("id","840175805404684288");
    httpRequest.body(JSONUtil.toJsonStr(body));
    String result;
    try (HttpResponse httpResponse = httpRequest.execute()) {
        result = httpResponse.body();
    }

返回结果示例:
a.接口调用成功，则返回JSON数据示例为：::

    {
      "flag": true,
      "data": {
        "ano": "842357643523006464",
        "status": 3,
        "attestationType": 8,
        "createTime": "2023-03-14 11:08:32",
        "fileHash": "98df1f1dfb3b1a123c1517912dc70447aa61c6be532ac99de973abb6219e1653",
        "blockchainHash": "55f3c66e0d0a8597b9d26d40c793bc01d6f9d03a38a6130eaa1c42c41c2820fd",
        "fileName": "test1",
        "label": "标签",
        "pdfUrl": "https://hbq.obs.cn-east-3.myhuaweicloud.com/staging/pdf/842357643523006464.pdf"
      },
      "statusCode": "000000",
      "errorMessage": "操作成功"
    }

b.接口调用失败，则返回JSON数据示例为：::

    {
      "flag": false,
      "statusCode": "520001",
      "errorMessage": "未查询到数据"
    }

执法记录仪取证 - /attestation/enforcer
------------------------------------
执法记录仪的用户进行执法记录仪取证提交。

请求参数
^^^^^^^^^^^^^^^

=================  ======================================= ================
参数名 				描述                                    是否可选
=================  ======================================= ================
deviceId            执法记录仪编号                               必选
fileHash            文件hash (sha256)                           必选
name                取证名称                                    必选
label               取证标签                                    非必选
evidenceType        取证类型:1拍照，2录像，3录音                  必选
fileSize            文件大小                                    必选
fileName            文件名                                     必选
fileOssKey          文件上传到oss后的key                         必选
address             取证地址                                    必选
startTime           取证开始时间                                 必选
endTime             取证结束时间                                 非必选
saveTime            上传时间                                    必选
=================  ======================================= ================

返回的data
^^^^^^^^^^^^^^

调用执法记录仪取证接口成功后会返回存证id列表

===================  ================================
字段名 				    描述
===================  ================================
attestationId            存证编号
===================  ================================

以java为例::

    // API path
    String apiName = "/attestation/enforcer";
    HttpRequest httpRequest = createRequestPost(apiName);
    // 构建请求参数
    File file = new File("/tmp/123.mp4");
    // uploadOss的实现方法参考java示例
    String ossKey = uploadOss(file);
    SubmitEnforcerRecordParam param = new SubmitEnforcerRecordParam();
    param.setName("test");
    param.setDeviceId("E123456");
    param.setAddress("地址");
    param.setLabel("标签");
    param.setEvidenceType(2);
    param.setStartTime("2023-04-04 13:10:12");
    param.setEndTime("2023-04-04 13:30:12");
    param.setSaveTime("2023-04-04 14:10:10");
    param.setFileHash(SecureUtil.sha256(file));
    param.setFileName(file.getName());
    param.setFileOssKey(ossKey);
    param.setFileSize(file.length());
    httpRequest.body(JSONUtil.toJsonStr(param));
    String result;
    try (HttpResponse httpResponse = httpRequest.execute()) {
        result = httpResponse.body();
    }

返回结果示例:
a.接口调用成功，则返回JSON数据示例为：::

    {
      "flag": true,
      "data": {
        "attestationId": "840175805404684288"
      },
      "statusCode": "000000",
      "errorMessage": "操作成功"
    }

b.接口调用失败，则返回JSON数据示例为：::

    {
      "flag": false,
      "statusCode": "500087",
      "errorMessage": "hash错误"
    }

