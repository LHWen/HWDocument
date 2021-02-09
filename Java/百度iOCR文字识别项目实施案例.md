# 百度OCR文字识别项目实施案例

#### 需求描述

目前有个需要对身份证、银行卡以及营业执照进行拍照上传服务器进行自动获取其证件内容信息，并将获取到的信息回显在页面上，数据保存时上传到数据库保存，对身份证需要获取姓名、身份证号、户籍地址，银行卡需要获取所属银行、卡号，营业执照需要获取信用代码、法人名称、公司名称、公司地址。

#### 技术分析

1.百度OCR
支持证件类型 -- 身份证、银行卡、营业执照、名片、护照、港澳通行证、台湾通行证、户口本、出生医学证明以及自定义模板OCR。

2.阿里云OCR
支持证件类型 -- 身份证、银行卡、营业执照、护照、驾驶证、行驶证、车牌、车辆VIN码、增值税发票、自定义模板OCR。

项目实施选定百度OCR识别技术，原因每月有一定免费识别次数，阿里云的为固定免费试用次数，一旦试用完次数需要付费，在未确定客户是否为此技术付费前提下，暂定使用百度OCR的API。

#### 系统设计

对于百度OCR的API接入选择使用后台Java进行接入。

**开发语言：**
**后端** -- Java 接收前端上传的图片调用百度OCR接口进行识别。
**前端** -- 前端语言随意，接口式调用；自定义拍照页面，对拍照图片进行内容截取进行上传，上传方式跟常规图片上传一致，调用后端接口，拿到后端返回数据信息进行判断赋值回显。

### 以下为具体实现过程

百度OCR文字识别后台接入首先需要获取Access Token。

#### 获取Access Token

文档URL：https://ai.baidu.com/ai-doc/REFERENCE/Ck3dwjhhu

对HTTP API调用者，百度AIP开放平台使用OAuth2.0授权调用开放API，调用API时必须在URL中带上access_token参数，获取Access Token的流程如下：
向授权服务地址https://aip.baidubce.com/oauth/2.0/token 发送请求（推荐使用POST），并在URL中带上以下参数：

```tex
grant_type：必须参数，固定为client_credentials；
client_id：必须参数，应用的API Key；
client_secret：必须参数，应用的Secret Key；
```

例如：
https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id=Va5yQRHlA4Fq5eR3LT0vuXV4&client_secret=0rDSjzQ20XUj5itV6WRtznPQSzr5pVw2&

服务器返回的json文本参数如下：
access_token:  要获取的Access Token
expires_in: Access Token的有效期(秒为单位，一般为1个月)
其他参数忽略
例如：

```json
{
  "refresh_token": "25.b55fe1d287227ca97aab219bb249b8ab.315360000.1798284651.282335-8574074",
  "expires_in": 2592000,
  "scope": "public wise_adapt",
  "session_key": "9mzdDZXu3dENdFZQurfg0Vz8slgSgvvOAUebNFzyzcpQ5EnbxbF+hfG9DQkpUVQdh4p6HbQcAiz5RmuBAja1JJGgIdJI",
  "access_token": "24.6c5e1ff107f0e8bcef8c46d3424a0e78.2592000.1485516651.282335-8574074",
  "session_secret": "dfac94a3489fe9fca7c3221cbf7525ff"
}
```

若请求错误，服务器将返回的json文本包含以下参数：
error:  错误码
error_description: 错误信息描述

| error          | error_description            | 解释             |
| :------------- | ---------------------------- | ---------------- |
| invalid_client | unknown client id            | API Key不正确    |
| invalid_client | Client authentication failed | Secret Key不正确 |

##### Java代码实现获取access_token:

```java
/** 获取access_token 有效期为30天，需要定期更新 */
private String getAccessToken() throws IOException {
	// 缓存中获取access_token
  String rToken = redisUtils.get("baidu.ocr.access_token");
  if (StringUtils.isNotEmpty(rToken)) {
    return rToken;
  }
  String apiKey = "API Key";
  String secretKey = "Secret Key";
  String url = 
    StrUtil.indexedFormat(baiduOcrConfig.getAccessTokenUrl(), 
                          apiKey, secretKey);
  log.info("获取access_token: " + url);
  JSONObject jsonObject = HttpUtil.doGetStr(url);
  AppAssertUtil.isNull(jsonObject, "获取AccessToken返回空信息,url:" + 
                       url);
  String token = jsonObject.getString(accessToken);
  // 保存29天
  redisUtils.set("baidu.ocr.access_token", token, 2505600);
		
  return token;
}
```

#### 1、识别营业执照信息

**文档URL：**https://ai.baidu.com/ai-doc/OCR/sk3h7y3zs

**接口描述：**识别营业执照，并返回关键字段的值，包括单位名称、类型、法人、地址、有效期、证件编号、社会信用代码等。

**接口请求说明：**

请求URL：https://aip.baidubce.com/rest/2.0/ocr/v1/business_license
HTTP方式：POST
URL参数：access_token
Header如下：

| 参数         | 值                                |
| ------------ | --------------------------------- |
| Content-Type | application/x-www-form-urlencoded |

**Body中放置请求参数，参数详情如下：**

| 参数             | 类型   | 是否必须 | 说明                                                         |
| ---------------- | ------ | -------- | ------------------------------------------------------------ |
| image            | string | 是       | 图像数据，base64编码后进行urlencode，要求编码后大小不超过4M，最短边至少15px，最长边最大4096px,支持jpg/jpeg/png/bmp格式 |
| detect_direction | string | 否       | 可选值 true, false是否检测图像朝向，默认不检测，即：false。朝向是指输入图像是正常方向、逆时针旋转90/180/270度 |
| accuracy         | string | 否       | 可选值：normal,high参数选normal或为空使用快速服务；选择high使用高精度服务，但是时延会根据具体图片有相应的增加 |

**返回参数**

| 参数             | 是否必须 | 类型    | 说明                                   |
| ---------------- | -------- | ------- | -------------------------------------- |
| log_id           | 是       | uint64  | 请求标识码，随机数，唯一。             |
| words_result_num | 是       | uint32  | 识别结果数，表示words_result的元素个数 |
| words_result     | 是       | array() | 定位和识别结果数组                     |
| left             | 是       | uint32  | 表示定位位置的长方形左上顶点的水平坐标 |
| top              | 是       | uint32  | 表示定位位置的长方形左上顶点的垂直坐标 |
| width            | 是       | uint32  | 表示定位位置的长方形的宽度             |
| height           | 是       | uint32  | 表示定位位置的长方形的高度             |
| words            | 否       | string  | 识别结果字符串                         |

**返回示例**

```json
{
    "log_id": 490058765,
    "words_result": {
        "单位名称": {
            "location": {
                "left": 500,
                "top": 479,
                "width": 618,
                "height": 54
            },
            "words": "袁氏财团有限公司"
        },
         "类型": {
            "location": {
                "left": 53,
                "top": 64,
                "width": 74,
                "height": 97
            },
            "words": "有限责任公司（自然人独资）"
        },
        "法人": {
            "location": {
                "left": 938,
                "top": 557,
                "width": 94,
                "height": 46
            },
            "words": "袁运筹"
        },
        "地址": {
            "location": {
                "left": 503,
                "top": 644,
                "width": 574,
                "height": 57
            },
            "words": "江苏省南京市中山东路19号"
        },
        "有效期": {
            "location": {
                "left": 779,
                "top": 1108,
                "width": 271,
                "height": 49
            },
            "words": "2015年02月12日"
        },
        "证件编号": {
            "location": {
                "left": 1219,
                "top": 357,
                "width": 466,
                "height": 39
            },
            "words": "苏餐证字(2019)第666602666661号"
        },
        "社会信用代码": {
            "location": {
                "left": 0,
                "top": 0,
                "width": 0,
                "height": 0
            },
            "words": "无"
        }
    },
    "words_result_num": 6
}
```

#### 2、识别身份证信息

**文档URL：**https://ai.baidu.com/ai-doc/OCR/rk3h7xzck

**接口描述：**支持对大陆居民二代身份证正反面的所有字段进行结构化识别，包括姓名、性别、民族、出生日期、住址、身份证号、签发机关、有效期限；并支持检测身份证正面的头像信息，并返回其位置及头像切片的 base64 编码。 同时，支持对用户上传的身份证图片进行图像风险和质量检测，可识别图片是否为复印件或临时身份证，是否被翻拍或编辑，是否存在正反颠倒、模糊、欠曝、过曝等质量问题。

**接口请求说明：**

请求URL：https://aip.baidubce.com/rest/2.0/ocr/v1/idcard
HTTP方式：POST
URL参数：access_token
Header如下：

| 参数         | 值                                |
| ------------ | --------------------------------- |
| Content-Type | application/x-www-form-urlencoded |

**Body中放置请求参数，参数详情如下：**

| 参数             | 是否必选 | 类型   | 可选值范围 | 说明                                                         |
| ---------------- | -------- | ------ | ---------- | ------------------------------------------------------------ |
| image            | 是       | string | -          | 图像数据，base64编码后进行urlencode，要求编码后大小不超过4M，最短边至少15px，最长边最大4096px,支持jpg/jpeg/png/bmp格式 |
| id_card_side     | 是       | string | front/back | front：身份证含照片的一面；back：身份证带国徽的一面          |
| detect_direction | 否       | string | true/false | 是否检测图像旋转角度，默认检测，即：true。朝向是指输入图像是正常方向、逆时针旋转90/180/270度。可选值包括: - true：检测旋转角度； - false：不检测旋转角度。 |
| detect_risk      | 否       | string | true/false | 是否开启身份证风险类型(身份证复印件、临时身份证、身份证翻拍、修改过的身份证)功能，默认不开启，即：false。可选值:true-开启；false-不开启 |
| detect_photo     | 否       | string | true/false | 是否检测头像内容，默认不检测。可选值：true-检测头像并返回头像的 base64 编码及位置信息 |
| detect_rectify   | 否       | string | true/false | 是否进行完整性校验，默认为true，需上传各字段内容完善的图片方可识别；如果设置为false，则对于身份证切片（如仅身份证号区域）也可识别 |

**返回参数**

| 字段             | 是否必选 | 类型    | 说明                                                         |
| ---------------- | -------- | ------- | ------------------------------------------------------------ |
| direction        | 否       | int32   | 图像方向，当 detect_direction = true 时，返回该参数。 - -1:未定义， - 0:正向， - 1: 逆时针90度， - 2:逆时针180度， - 3:逆时针270度 |
| image_status     | 是       | string  | normal-识别正常 reversed_side-身份证正反面颠倒 non_idcard-上传的图片中不包含身份证 blurred-身份证模糊 other_type_card-其他类型证照 over_exposure-身份证关键字段反光或过曝 over_dark-身份证欠曝（亮度过低） unknown-未知状态 |
| risk_type        | 否       | string  | 输入参数 detect_risk = true 时，则返回该字段识别身份证类型: normal-正常身份证；copy-复印件；temporary-临时身份证；screen-翻拍；unknown-其他未知情况 |
| edit_tool        | 否       | string  | 如果参数 detect_risk = true 时，则返回此字段。如果检测身份证被编辑过，该字段指定编辑软件名称，如:Adobe Photoshop CC 2014 (Macintosh),如果没有被编辑过则返回值无此参数 |
| log_id           | 是       | uint64  | 唯一的log id，用于问题定位                                   |
| photo            | 否       | string  | 当请求参数 detect_photo = true时返回，头像切图的 base64 编码（无编码头，需自行处理） |
| photo_location   | 否       | array() | 当请求参数 detect_photo = true时返回，头像的位置信息（坐标0点为左上角） |
| words_result     | 是       | array() | 定位和识别结果数组                                           |
| words_result_num | 是       | uint32  | 识别结果数，表示words_result的元素个数                       |
| +location        | 是       | array() | 位置数组（坐标0点为左上角）                                  |
| ++left           | 是       | uint32  | 表示定位位置的长方形左上顶点的水平坐标                       |
| ++top            | 是       | uint32  | 表示定位位置的长方形左上顶点的垂直坐标                       |
| ++width          | 是       | uint32  | 表示定位位置的长方形的宽度                                   |
| ++height         | 是       | uint32  | 表示定位位置的长方形的高度                                   |
| +words           | 否       | string  | 识别结果字符串                                               |

**返回示例**

```json
{
    "log_id": 2648325511,
    "direction": 0,
    "image_status": "normal",
    "idcard_type": "normal",
    "edit_tool": "Adobe Photoshop CS3 Windows",
    "photo": "/9j/4AAQSkZJRgABA......",
    "photo_location": {
        "width": 1189,
        "top": 638,
        "left": 2248,
        "height": 1483
    },
    "words_result": {
        "住址": {
            "location": {
                "left": 267,
                "top": 453,
                "width": 459,
                "height": 99
            },
            "words": "南京市江宁区弘景大道3889号"
        },
        "公民身份号码": {
            "location": {
                "left": 443,
                "top": 681,
                "width": 589,
                "height": 45
            },
            "words": "330881199904173914"
        },
        "出生": {
            "location": {
                "left": 270,
                "top": 355,
                "width": 357,
                "height": 45
            },
            "words": "19990417"
        },
        "姓名": {
            "location": {
                "left": 267,
                "top": 176,
                "width": 152,
                "height": 50
            },
            "words": "伍云龙"
        },
        "性别": {
            "location": {
                "left": 269,
                "top": 262,
                "width": 33,
                "height": 52
            },
            "words": "男"
        },
        "民族": {
            "location": {
                "left": 492,
                "top": 279,
                "width": 30,
                "height": 37
            },
            "words": "汉"
        }
    },
    "words_result_num": 6
}
```

#### 3、识别银行卡信息

**文档URL：**https://ai.baidu.com/ai-doc/OCR/ak3h7xxg3

**接口描述：**识别银行卡并返回卡号、有效期、发卡行和卡片类型。

**接口请求说明：**

请求URL：https://aip.baidubce.com/rest/2.0/ocr/v1/bankcard
HTTP方式：POST
URL参数：access_token
Header如下：

| 参数         | 值                                |
| ------------ | --------------------------------- |
| Content-Type | application/x-www-form-urlencoded |

**Body中放置请求参数，参数详情如下：**

| 参数  | 类型   | 是否必须 | 说明                                                         |
| ----- | ------ | -------- | ------------------------------------------------------------ |
| image | string | 是       | 图像数据，base64编码后进行urlencode，要求编码后大小不超过4M，最短边至少15px，最长边最大4096px,支持jpg/jpeg/png/bmp格式 |

**返回参数**

| 参数               | 类型   | 是否必须 | 说明                                         |
| ------------------ | ------ | -------- | -------------------------------------------- |
| log_id             | uint64 | 是       | 请求标识码，随机数，唯一。                   |
| result             | object | 是       | 返回结果                                     |
| + bank_card_number | string | 是       | 银行卡卡号                                   |
| + valid_date       | string | 是       | 银行卡卡号                                   |
| + bank_card_type   | uint32 | 是       | 银行卡类型，0:不能识别; 1: 借记卡; 2: 信用卡 |
| + bank_name        | string | 是       | 银行名，不能识别时为空                       |

**返回示例**

```json
{
    "log_id": 144718895115129615,
    "result": {
        "bank_card_number": "3568 8900 8000 0005",
        "valid_date": "07/21",
        "bank_card_type": 2,
        "bank_name": "招商银行"
    }
}
```

#### 4、iOCR自定义模板文字识别

**文档URL：**https://ai.baidu.com/ai-doc/OCR/0k3h7y8ip

**说明：**若要自定义模板，请参考官方文档进行自定义模板（文档很详细）

**注意: **  自定义模板的使用，对于上传图片数据*<u>在自定义模板不需要使用URLEncode编码，但需要将base64编码中的\n \r \\ 替换 不然报格式错误</u>*，以下是进行自定义模板http请求使用代码（Java）:

```java
public String getIocrInfo() throws IOException {
  // 获取access_token
  String accesst = getAccessToken();
  String iocrUrl = "https://aip.baidubce.com/rest/2.0/solution/v1/iocr/recognise?access_token=" + accesst;
  log.info("获取自定义模板信息url: " + iocrUrl);
  // 获取图片的base64编码
  // 图像数据，base64编码后进行urlencode，要求编码后大小不超过4M，最短边至少15px，最长边最大4096px,支持jpg/jpeg/png/bmp格式
  String imgBase64Str = 
    getImageBaser64("/Users/lhwen/Desktop/yinyezzgs.jpg");
  //自定义模板不需要使用URLEncode编码，但需要将base64编码中的\n \r \\ 替换 不然报格式错误
  imgBase64Str = imgBase64Str.replaceAll("\r\n","");
  imgBase64Str = imgBase64Str.replaceAll("\n","");
  imgBase64Str = imgBase64Str.replaceAll("\\+","%2B");
  // iocr 不需要URLEncoder 编码
  // 企业营业执照 000f882d51540196959ac9beb0f24ddc 高铁车票8260a38a2ddd12db5c75f1c56863e907
  String bordInfo = 
    "templateSign=000f882d51540196959ac9beb0f24ddc&image=" + imgBase64Str;
  JSONObject jsonObject = HttpUtil.doPostStr(iocrUrl, headerName, headerValue, bordInfo);
  AppAssertUtil.isNull(jsonObject, "获取自定义模板信息为空，url:" + iocrUrl);

  log.info("获取自定义模板信息信息: " + jsonObject);

  return jsonObject.getJSONObject("data").getString("logId");
}
```

#### java后台实现百度OCR文字识别api

**http工具类**

```Java
public class HttpUtil {
  /**
     * get请求
     *
     * @param url
     * @return
     * @throws ParseException
     * @throws IOException
     */
    public static JSONObject doGetStr(String url) throws ParseException, IOException {
        CloseableHttpClient client = HttpClients.createDefault();
        HttpGet httpGet = new HttpGet(url);
        JSONObject jsonObject = null;
        CloseableHttpResponse response = client.execute(httpGet);
        HttpEntity entity = response.getEntity();
        if (entity != null) {
            String result = EntityUtils.toString(entity, "UTF-8");
            jsonObject = JSONObject.fromObject(result);

        }
        return jsonObject;
    }

    /**
     * POST请求
     *
     * @param url
     * @param outStr
     * @return
     * @throws ParseException
     * @throws IOException
     */
    public static JSONObject doPostStr(String url,String headerName, String headerValue, String outStr) throws ParseException, IOException {
        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost httpost = new HttpPost(url);
        JSONObject jsonObject = null;
        httpost.setHeader(headerName, headerValue);
        httpost.setEntity(new StringEntity(outStr, "UTF-8"));
        CloseableHttpResponse response = client.execute(httpost);
        String result = EntityUtils.toString(response.getEntity(), "UTF-8");
        jsonObject = JSONObject.fromObject(result);
        return jsonObject;
    }

}
```

**图片base64编码处理**

```java
private String getImageBaser64(String path) {
  String imgFile = path;
  byte[] data = null;
  // 获取图片字节数组
  try {
    InputStream in = new FileInputStream(imgFile);
    data = new byte[in.available()];
    in.read(data);
    in.close();
  } catch (IOException e){
    e.printStackTrace();
  }
  // 对字节数组base64编码
  return Base64Utils.encodeToString(data);
}

private String getImageBaser64(MultipartFile file) {
  byte[] data = null;
  // 获取图片字节数组
  try {
    // MultipartFile -> File
    Date uploadTime = new Date();
    // 获取文件名
    String fileName = file.getOriginalFilename();
    // 获取文件后缀
    String prefix = fileName.substring(fileName.lastIndexOf("."));
    // 用当前时间戳为文件名
    File convFile = File.createTempFile("" + uploadTime.getTime(), prefix);
    file.transferTo(convFile);
    
    InputStream in = new FileInputStream(convFile);
    data = new byte[in.available()];
    in.read(data);
    in.close();
    // 删除临时文件
    deleteFile(convFile);
  } catch (IOException e){
    e.printStackTrace();
  }
  // 对字节数组base64编码
  return Base64Utils.encodeToString(data);
}

// 删除临时文件
private void deleteFile(File... files) {
  for (File file : files) {
    if (file.exists()) {
      file.delete();
    }
  }
}
```

**调用百度api获取图片文字信息**

```java
public JSONObject ocrImageInfo(MultipartFile[] imageFile, int type) throws IOException {
  // 获取access_token
  String accesst = getAccessToken();
  // 图像数据，base64编码后进行urlencode，要求base64编码和urlencode后大小不超过4M，最短边至少15px，最长边最大4096px,支持jpg/jpeg/png/bmp格式
  String imgBase64Str = getImageBaser64(imageFile[0]);  
  String imgUrlEncode = URLEncoder.encode(imgBase64Str, "UTF-8");
  String bordInfo = "";
  String queryUrl = "";
  String typeMsg = "";
  switch (type) {
    case 1: { // 身份证 
      // id_card_side=front front：身份证含照片的一面；back：身份证带国徽的一面
      bordInfo = "id_card_side=front&image=" + imgUrlEncode;
      queryUrl = "https://aip.baidubce.com/rest/2.0/ocr/v1/idcard?access_token=" + accesst;
      typeMsg = "获取身份证信息";
      break;
    }
    case 2: { // 银行卡
      bordInfo = "image=" + imgUrlEncode;
      queryUrl = "https://aip.baidubce.com/rest/2.0/ocr/v1/bankcard?access_token=" + accesst;
      typeMsg = "获取银行卡信息";
      break;
    }
    case 3: { // 营业执照
      bordInfo = "image=" + imgUrlEncode;
      queryUrl = "https://aip.baidubce.com/rest/2.0/ocr/v1/business_license?access_token=" + accesst;
      typeMsg = "获取营业执照信息:";
      break;
    }
    default: {
      break;
    }
  }
  
  JSONObject jsonObject = HttpUtil.doPostStr(queryUrl, headerName, headerValue, bordInfo);
  AppAssertUtil.isNull(jsonObject, typeMsg + "为空, url:" + queryUrl);
  
  log.info(typeMsg + ": " + jsonObject);
  
  return jsonObject;
}
```

#### 前端实现内容

进行图片拍照裁剪内容区域，判断图片大小（超出4M的图片需要进行适量压缩处理），请求后台接口，拿到返回数据进行数据填充。

前端采用原生与JS交互技术实现页面数据回显，原生进行拍摄与图片的裁剪以及调用后端接口，获取到后端返回的数据进行封装后，将数据传递给前端（调用前端的js方法）

##### 前端使用Vue调用原生方法通知开启相册以及识别类型

```js
 getScanningData (info) {
   let u = navigator.userAgent
   // android终端
   let isAndroid = u.indexOf('Android')>-1 || u.indexOf('Adr') > -1 
   // ios终端
   let isiOS = !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/) 
   if (isiOS) {
    // 调用iOS wkWebView 方式 JSWITHIOSINTERACTION 为iOS定义的通信名称，iOS端接收到stat是 1002
    window.webkit.messageHandlers.JSWITHIOSINTERACTION.postMessage({
     info: info,
     stat: 1002
    })
   } else if (isAndroid) {
     // 调用安卓的开启相册
     window.androids.openCamera(info)
   }
 },
```

##### Vue.js接收原生调用

1）原生调用Vue.js实现传递数据，先将接受参数方法挂载在Window上

```js
created () {
    window.baiduOCRImageInfo = this.baiduOCRImageInfo
  }
```

2）方法接受数据，data接受json类型

```js
// data 接受接送类型，type为类型 不同类型处理不同数据回显
baiduOCRImageInfo (type, data) {
  if (type === 1 || type === '1') {
    this.saveInfo.cardName = data.name
    this.saveInfo.cardAddr = data.addr
    this.saveInfo.card = data.cardId
    this.compareName()
  }
}
```

##### iOS调用vue.js的接收方法进行数据传递

```objective-c
// 使用block回调处理数据，数据封装使用NSDictionary类型
vc.dataBlock = ^(NSInteger type, NSDictionary * _Nonnull data) {
  // 数据json数列化
  NSData *jData = [NSJSONSerialization dataWithJSONObject:data options:NSJSONWritingPrettyPrinted error:nil];
  // 为了让js能接收到并能使用json处理的数据，进行utf-8编码处理
  NSString *jsonStr = [[NSString alloc] initWithData:jData encoding:NSUTF8StringEncoding];
  // 将调用js方法与传递参数，使用string拼接
  NSString *jsFunc = [NSString stringWithFormat:@"baiduOCRImageInfo(%d,%@)", type, jsonStr];
  // js方法调用，传递参数
  [weakSelf.kWebView evaluateJavaScript:jsFunc completionHandler:nil];
};
```

##### Android调用vue.js的接收方法进行数据传递

```java
Gson gson = new Gson();
// 数据json化
String resultJson = gson.toJson(maps);
if (!TextUtils.isEmpty(resultJson)) {
  // js方法调用，传递参数
mAgentWeb.getJsEntraceAccess().quickCallJs("baiduOCRImageInfo",type+"",resultJson);
}
```



#### 错误码

**文档URL：**https://ai.baidu.com/ai-doc/OCR/dk3h7y5vr

若请求错误，服务器将返回的JSON文本包含以下参数：

**error_code**：错误码
**error_msg**：错误描述信息，帮助理解和解决发生的错误

例如Access Token失效返回：

```text
{
  "error_code": 110,
  "error_msg": "Access token invalid or no longer valid"
}
```

需要重新获取新的Access Token再次请求即可。

| 错误码 | 错误信息                                | 描述                                                         |
| ------ | --------------------------------------- | ------------------------------------------------------------ |
| 1      | Unknown error                           | 服务器内部错误，请再次请求， 如果持续出现此类错误，请在控制台提交工单联系技术支持团队 |
| 2      | Service temporarily unavailable         | 服务暂不可用，请再次请求， 如果持续出现此类错误，请在控制台提交工单联系技术支持团队 |
| 3      | Unsupported openapi method              | 调用的API不存在，请检查后重新尝试                            |
| 4      | Open api request limit reached          | 集群超限额                                                   |
| 6      | No permission to access data            | 无权限访问该用户数据                                         |
| 14     | IAM Certification failed                | IAM鉴权失败，建议用户参照文档自查生成sign的方式是否正确，或换用控制台中ak sk的方式调用 |
| 17     | Open api daily request limit reached    | 每天请求量超限额                                             |
| 18     | Open api qps request limit reached      | QPS超限额                                                    |
| 19     | Open api total request limit reached    | 请求总量超限额                                               |
| 100    | Invalid parameter                       | 无效的access_token参数，请检查后重新尝试                     |
| 110    | Access token invalid or no longer valid | access_token无效                                             |
| 111    | Access token expired                    | access token过期                                             |
| 216100 | invalid param                           | 请求中包含非法参数，请检查后重新尝试                         |
| 216101 | not enough param                        | 缺少必须的参数，请检查参数是否有遗漏                         |
| 216102 | service not support                     | 请求了不支持的服务，请检查调用的url                          |
| 216103 | param too long                          | 请求中某些参数过长，请检查后重新尝试                         |
| 216110 | appid not exist                         | appid不存在，请重新核对信息是否为后台应用列表中的appid       |
| 216200 | empty image                             | 图片为空，请检查后重新尝试                                   |
| 216201 | image format error                      | 上传的图片格式错误，现阶段我们支持的图片格式为：PNG、JPG、JPEG、BMP，请进行转码或更换图片 |
| 216202 | image size error                        | 上传的图片大小错误，现阶段我们支持的图片大小为：base64编码后小于4M，分辨率不高于4096*4096，请重新上传图片 |
| 216630 | recognize error                         | 识别错误，请再次请求，如果持续出现此类错误，请在控制台提交工单联系技术支持团队 |
| 216631 | recognize bank card error               | 识别银行卡错误，出现此问题的原因一般为：您上传的图片非银行卡正面，上传了异形卡的图片或上传的银行卡正品图片不完整 |
| 216633 | recognize idcard error                  | 识别身份证错误，出现此问题的原因一般为：您上传了非身份证图片或您上传的身份证图片不完整 |
| 216634 | detect error                            | 检测错误，请再次请求，如果持续出现此类错误，请在控制台提交工单联系技术支持团队 |
| 282000 | internal error                          | 服务器内部错误，如果您使用的是高精度接口，报这个错误码的原因可能是您上传的图片中文字过多，识别超时导致的，建议您对图片进行切割后再识别，其他情况请再次请求， 如果持续出现此类错误，请在控制台提交工单联系技术支持团队 |
| 282003 | missing parameters: {参数名}            | 请求参数缺失                                                 |
| 282005 | batch processing error                  | 处理批量任务时发生部分或全部错误，请根据具体错误码排查       |
| 282006 | batch task limit reached                | 批量任务处理数量超出限制，请将任务数量减少到10或10以下       |
| 282102 | target detect error                     | 未检测到图片中识别目标，请确保图片中包含对应卡证票据         |
| 282103 | target recognize error                  | 图片目标识别错误，请确保图片中包含对应卡证票据，如果持续出现此类错误，请在控制台提交工单联系技术支持团队 |
| 282110 | urls not exit                           | URL参数不存在，请核对URL后再次提交                           |
| 282111 | url format illegal                      | URL格式非法，请检查url格式是否符合相应接口的入参要求         |
| 282112 | url download timeout                    | url下载超时，请检查url对应的图床/图片无法下载或链路状况不好，您可以重新尝试一下，如果多次尝试后仍不行，建议更换图片地址 |
| 282113 | url response invalid                    | URL返回无效参数                                              |
| 282114 | url size error                          | URL长度超过1024字节或为0                                     |
| 282808 | request id: xxxxx not exist             | request id xxxxx 不存在                                      |
| 282809 | result type error                       | 返回结果请求错误（不属于excel或json）                        |
| 282810 | image recognize error                   | 图像识别                                                     |

#### 另一种实现方式

百度OCR文字识别技术本身就是http接口，可完全交由前端处理，后台不用进行再次数据处理，即前端获取access_token，在调用百度的api时进行图片数据处理（压缩与编码），对获取到的数据进行处理渲染，可绕过后台服务的再次请求。