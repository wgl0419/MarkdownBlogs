#Android微信支付
>　　项目要上支付功能，老大让我写一个支付的demo看看效果。支付宝和微信都要。支付宝还好说，有一整套的测试环境（沙箱），一上午就做出来了。
>　　微信就不得了了，从头到尾你得自己写。老大说了，现在后台比较忙。你先自己下单，自己支付，自己···
>　　微信支付-APP支付的整体流程是：通过下单接口向微信的服务器下单，用微信返回的订单信息调支付接口。本身也不难，但是微信的文档啊。是真TMD烂。全网最烂，没有之一。

##1.通过微信的<a href="http://design.1sters.com/#">统一下单</a>接口下单
###1.1配置常量参数
>appId:是微信开放平台！！！里面的AppID（微信开放平台->管理中心->应用详情）
>partnerId:是微信商户平台！！！里的微信支付商户号（微信商户平台->账户中心->商户信息）
>privateKey:是微信商户平台！！！里的API秘钥。秘钥默认是不显示的，只能重置。（微信商户平台->账户中心->API安全）


```java
	val WX_APP_ID = "" //微信分配的APP_ID
    val WX_PARTNER_ID = "" //微信分配的 PARTNER_ID (商户ID)
    val WX_PRIVATE_KEY = "" //微信分配的 PRIVATE_KEY (私钥)

	var ORDER_NUM = ""//订单号
    var NONEC_STR = ""//随机字符串
    var IP = ""//手机ip
    val URL = "www.baidu.com"//回调地址
    val ENCRYPT_WAY = "MD5"//加密方式

```


###1.2下单接口的的请求xml
>　　没错，微信的接口请求参数要求XML格式（日了狗）。你让我一个Android写xml请求（手动黑人问号）。

```java
		var xmlStr = """<xml>
                           <appid>$WX_APP_ID</appid>
                           <body>$ORDER_CONTENT</body>
                           <mch_id>$WX_PARTNER_ID</mch_id>
                           <nonce_str>$NONEC_STR</nonce_str>
                           <notify_url>$URL</notify_url>
                           <out_trade_no>$ORDER_NUM</out_trade_no>
                           <spbill_create_ip>$IP</spbill_create_ip>
                           <total_fee>1</total_fee>
                           <trade_type>APP</trade_type>
                           <sign_type>$ENCRYPT_WAY</sign_type>
                        </xml>
                    """
```

###1.3Retrofit配置
>　　微信官方demo用的是httpclient+eclipse项目（我敲尼玛啊）
>　　我这里用的是Retrofit（之所以没有Rxjava是xml解析比较麻烦，手动解析比较快）

####gradle配置一下
```java
    implementation "com.squareup.retrofit2:retrofit:2.3.0"
	//日志拦截器
    implementation "com.squareup.okhttp3:logging-interceptor:3.5.0"
```

```java
interface RetrofitService {

    companion object {
        val BaseURL: String = "https://api.mch.weixin.qq.com"//访问接口的ip和端口
    }

    @POST("/pay/unifiedorder")
    fun getWxOrderInfo(@Body body: RequestBody): Call<ResponseBody> //获取微信支付订单
}
```




```java
fun getWxOrderInfo(xml: String, callback: Callback<ResponseBody>) {
            //拦截器（打印网络请求log）
            var logInterceptor: HttpLoggingInterceptor = HttpLoggingInterceptor(HttpLogger())
            logInterceptor.level = HttpLoggingInterceptor.Level.BASIC
            var okHttpClient: OkHttpClient = OkHttpClient.Builder()
                    .sslSocketFactory(SSLSocketFactoryUtils.createSSLSocketFactory(), SSLSocketFactoryUtils.createTrustAllManager())//
                    .connectTimeout(10, TimeUnit.SECONDS)//设置超时时间
                    .readTimeout(10, TimeUnit.SECONDS)
                    .writeTimeout(10, TimeUnit.SECONDS)
                    .addInterceptor(logInterceptor)//添加拦截器
                    .build()

            var retrofit = Retrofit.Builder()
                    ?.baseUrl(RetrofitService.BaseURL)
                    ?.addConverterFactory(SimpleXmlConverterFactory.create())
                    ?.client(okHttpClient)
                    ?.build()

            var api = retrofit?.create(RetrofitService::class.java)
            var body = RequestBody.create(MediaType.parse("application/xml;charset=UTF-8"), xml)
            api?.getWxOrderInfo(body)
                    ?.enqueue(callback)
        }
```


###1.4统一下单
>　　在1.3中已经配置好了网络请求。在1.2中xml请求参数也配置好了，那么就可以直接进行下单了。下面主要说下各个必传参数代表什么（官方文档也写得很清楚，以官方文档为准）。
>　　appid：是微信平台给你的APP项目分配的唯一标识
>　　mch_id：商户号
>　　nonce_str：随机字符串
>　　notify_url：接收微信支付异步通知回调地址（我这里是客户端不需要回调，所有随便填的百度）
>　　body：商品描述
>　　out_trade_no：商户订单号
>　　spbill_create_ip：终端IP（手机ip）
>　　total_fee：订单总金额，单位为分
>　　trade_type：支付类型
>　　sign_type：签名方式
>　　这里重点提一下，微信文档中（统一下单）提到的签名其实就是加密的意思。具体的加密方式呢，<a href="https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=4_3">文档</a>也有详细的介绍而且还提供了<a href="https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=20_1">校验工具</a>。

```java

	    private var api: IWXAPI? = null



		api = WXAPIFactory.createWXAPI(this, null)
        ORDER_NUM = "xiehe_hospital_" + RxTimeTool.getCurTimeMills().toString() + ""
        NONEC_STR = RxTimeTool.getCurTimeMills()?.toString()
        IP = XmlUtil.getIPAddress(this)
        btn_wx_pay?.setOnClickListener {
            Log.e(TAG, ">>>>>>>将该app注册到微信:" + api?.registerApp(APP_ID))
            var xmlStr = """<xml>
                           <appid>$WX_APP_ID</appid>
                           <body>$ORDER_CONTENT</body>
                           <mch_id>$WX_PARTNER_ID</mch_id>
                           <nonce_str>$NONEC_STR</nonce_str>
                           <notify_url>$URL</notify_url>
                           <out_trade_no>$ORDER_NUM</out_trade_no>
                           <spbill_create_ip>$IP</spbill_create_ip>
                           <total_fee>1</total_fee>
                           <trade_type>APP</trade_type>
                           <sign_type>$ENCRYPT_WAY</sign_type>
                        </xml>
                    """

            var orderInfo = XmlUtil.doXMLParse(xmlStr)
            Log.e(TAG, ">>>>>>>orderInfo:" + orderInfo)
            var sign = getSign(orderInfo)
            var xml = """<xml>
                           <appid>$WX_APP_ID</appid>
                           <body>$ORDER_CONTENT</body>
                           <mch_id>$WX_PARTNER_ID</mch_id>
                           <nonce_str>$NONEC_STR</nonce_str>
                           <notify_url>$URL</notify_url>
                           <out_trade_no>$ORDER_NUM</out_trade_no>
                           <spbill_create_ip>$IP</spbill_create_ip>
                           <total_fee>1</total_fee>
                           <trade_type>APP</trade_type>
                           <sign_type>$ENCRYPT_WAY</sign_type>
                            <sign>$sign</sign>
                        </xml>
                    """
            RemoteDateManger.getWxOrderInfo(xml, object : Callback<ResponseBody> {
                override fun onFailure(call: Call<ResponseBody>?, t: Throwable?) {

                }

                override fun onResponse(call: Call<ResponseBody>?, response: Response<ResponseBody>?) {
                    var xmlStr = response?.body()?.string()?.toString()
                    Log.e(RemoteDateManger.TAG, ">>>>>>>xmlStr:" + xmlStr)
                    var orderInfo = XmlUtil.doXMLParse(xmlStr)
                    orderInfo?.remove("result_code")
                    orderInfo?.remove("return_code")
                    orderInfo?.remove("return_msg")
                    var req = PayReq()
                    var map = mutableMapOf<String, String>()
                    req?.appId = orderInfo.get("appid")?.toString()
                    req?.prepayId = orderInfo.get("prepay_id")?.toString()
                    req?.partnerId = WX_PARTNER_ID//商户号
                    req?.packageValue = "Sign=WXPay"
                    req?.timeStamp = RxTimeTool.getCurTimeMills()?.toString()?.substring(0, 10)
                    req?.nonceStr = orderInfo?.get("nonce_str")?.toString()
                    // 参数名ASCII码从小到大排序（字典序）
                    map?.put("appid", req?.appId)
                    map?.put("noncestr", req?.nonceStr)
                    map?.put("partnerid", req?.partnerId)
                    map?.put("prepayid", req?.prepayId)
                    map?.put("package", req?.packageValue)
                    map?.put("timestamp", req?.timeStamp)
                    var sign = getSign(map)
                    req?.sign = sign
                    Toast.makeText(this@MainActivity, "正常调起支付", Toast.LENGTH_SHORT).show()
                    api?.sendReq(req)
                }
            })

        }




private fun getSign(orderInfo: Map<*, *>?): String? {
        var list = XmlUtil.sort(orderInfo as MutableMap<String, String>?)
        var str = ""
        list?.forEach {
            Log.e(TAG, ">>>>>>>list.item:" + it)
            if (!it?.key?.equals("sign")!!) {
                str = str + it + "&"
            }
        }
        Log.e(TAG, ">>>>>>>str1:" + str)
        str = str + "key=$WX_PRIVATE_KEY"
        Log.e(TAG, ">>>>>>>str2:" + str)
        var sign = RxTool.Md5(str)?.toUpperCase()
        Log.e(TAG, ">>>>>>>sign:" + sign)
        return sign
    }


```

>　　上面的代码中，博主直接调起<a href="https://pay.weixin.qq.com/wiki/doc/api/app/app.php?chapter=8_5">微信支付</a>的代码也贴出来了。也就是onResponse中的逻辑。
>　　这里讲一下调起支付的一些注意事项：
>　　1.在微信开放平台中设置应用包名和应用签名。这里重点提一下应用签名。Android中的签名分为两种，一种种是debug签名，另一种是release签名。其实两种签名都可以使用，但是推荐使用release签名。因为你在的项目最终上线肯定是release包。
>　　1.1debug签名就是你通过AndroidStudio直接run到手机中apk包的签名（MD5值）。也就是你本机的 androiddebugkey中的MD5值。<a href="https://blog.csdn.net/qq_24531461/article/details/70214719">如何查看本机androiddebugkey的MD5值</a>。
>　　1.2release签名是你使用.jks文件对apk包签名留下的MD5值。如何查看.jks文件的消息：
>>keytool -v -list -keystore  ******.jks
>
>　　1.3如果你实在分不清两个签名，那么你就去对比MD5值。<a href="https://open.weixin.qq.com/zh_CN/htmledition/res/dev/download/sdk/Gen_Signature_Android.apk">apk包中的MD5值(微信提供的签名工具)</a>和debug签名相同就是debug签名，反之则是release签名。如何查看apk签名：
>>第一步:将apk解压 
第二步:找到META-INF 下的.RSA文件 
第三步:在mac终端或者window控制器上输入命令: 
keytool -printcert -file xxx.RSA回车
其中：MD5的值就是签名的信息

>　　2.数据签名。在调起支付的时候要对
>　　　　　　　　　　　　传入的参数！！！
>　　　　　　　　　　　　传入的参数！！！
>　　　　　　　　　　　　传入的参数！！！
>　　　进行签名（加密），你用到多少参数就对多少参数进行签名（加密），不能多也不能少（多了或少了都会导致签名（加密）失败，支付调起后返回-1）。一般来说<a href="https://open.weixin.qq.com/zh_CN/htmledition/res/dev/download/sdk/Gen_Signature_Android.apk">签名（加密）</a>除sign之外的6个参数，然后将加密值付给sign字段。

```java
public class XmlUtil {
    /*
        * 解析xml,返回第一级元素键值对。如果第一级元素有子节点，则此节点的值是子节点的xml数据。
            * @param strxml
     * @return
             * @throws JDOMException
     * @throws IOException
     */
    @SuppressWarnings({"rawtypes", "unchecked"})
    public static Map doXMLParse(String strxml) {

        Map m = new HashMap();

        try {
            strxml = strxml.replaceFirst("encoding=\".*\"", "encoding=\"UTF-8\"");

            if (null == strxml || "".equals(strxml)) {
                return null;
            }

            InputStream in = new ByteArrayInputStream(strxml.getBytes("UTF-8"));
            SAXBuilder builder = new SAXBuilder();
            Document doc = builder.build(in);
            Element root = doc.getRootElement();
            List list = root.getChildren();
            Iterator it = list.iterator();
            while (it.hasNext()) {
                Element e = (Element) it.next();
                String k = e.getName();
                String v = "";
                List children = e.getChildren();
                if (children.isEmpty()) {
                    v = e.getTextNormalize();
                } else {
                    v = XmlUtil.getChildrenText(children);
                }

                m.put(k, v);
            }

            //关闭流
            in.close();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (JDOMException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

        return m;
    }

    /**
     * 获取子结点的xml
     *
     * @param children
     * @return String
     */
    @SuppressWarnings("rawtypes")
    public static String getChildrenText(List children) {
        StringBuffer sb = new StringBuffer();
        if (!children.isEmpty()) {
            Iterator it = children.iterator();
            while (it.hasNext()) {
                Element e = (Element) it.next();
                String name = e.getName();
                String value = e.getTextNormalize();
                List list = e.getChildren();
                sb.append("<" + name + ">");
                if (!list.isEmpty()) {
                    sb.append(XmlUtil.getChildrenText(list));
                }
                sb.append(value);
                sb.append("</" + name + ">");
            }
        }

        return sb.toString();
    }

    /**
     * 微信支付将请求参数转换为xml格式的String
     *
     * @return
     */
    @SuppressWarnings("rawtypes")
    public static String getRequestXmlQuery(SortedMap<String, String> paramMap) {
        StringBuffer sb = new StringBuffer();
        sb.append("<xml>");
        Set set = paramMap.entrySet();
        Iterator it = set.iterator();
        while (it.hasNext()) {
            Map.Entry entry = (Map.Entry) it.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            sb.append("<" + key + ">" + "<![CDATA[" + value + "]]></" + key + ">");
        }
        sb.append("</xml>");
        return sb.toString();
    }

    /**
     * 微信支付将请求参数转换为xml格式的String
     *
     * @return
     */
    @SuppressWarnings("rawtypes")
    public static String getRequestXml(SortedMap<String, String> paramMap) {
        StringBuffer sb = new StringBuffer();
        sb.append("<xml>");
        Set set = paramMap.entrySet();
        Iterator it = set.iterator();
        while (it.hasNext()) {
            Map.Entry entry = (Map.Entry) it.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            if ("attach".equalsIgnoreCase(key) || "body".equalsIgnoreCase(key) || "sign".equalsIgnoreCase(key)) {
                sb.append("<" + key + ">" + "<![CDATA[" + value + "]]></" + key + ">");
            } else {
                sb.append("<" + key + ">" + value + "</" + key + ">");
            }
        }
        sb.append("</xml>");
        return sb.toString();
    }

    public static List<Map.Entry<String, String>> sort(Map<String, String> map) {
        List<Map.Entry<String, String>> infoIds = new ArrayList<Map.Entry<String, String>>(map.entrySet());
        // 对所有传入参数按照字段名的 ASCII 码从小到大排序（字典序）
        Collections.sort(infoIds, new Comparator<Map.Entry<String, String>>() {

            public int compare(Map.Entry<String, String> o1, Map.Entry<String, String> o2) {
                return (o1.getKey()).toString().compareTo(o2.getKey());
            }
        });
        return infoIds;
    }

    /**
     * 生成签名
     *
     * @param map
     * @return
     */
    public static String getSign(Map<String, String> map) {

        String result = "";
        try {
            List<Map.Entry<String, String>> infoIds = new ArrayList<Map.Entry<String, String>>(map.entrySet());
            // 对所有传入参数按照字段名的 ASCII 码从小到大排序（字典序）
            Collections.sort(infoIds, new Comparator<Map.Entry<String, String>>() {

                public int compare(Map.Entry<String, String> o1, Map.Entry<String, String> o2) {
                    return (o1.getKey()).toString().compareTo(o2.getKey());
                }
            });

            // 构造签名键值对的格式
            StringBuilder sb = new StringBuilder();
            for (Map.Entry<String, String> item : infoIds) {
                if (item.getKey() != null || item.getKey() != "") {
                    String key = item.getKey();
                    String val = item.getValue();
                    if (!(val == "" || val == null)) {
                        sb.append(key + ":" + val + ":");
                    }
                }

            }
//			sb.append(PropertyManager.getProperty("SIGNKEY"));
            result = sb.toString();

            //进行MD5加密
            result = DigestUtils.md5Hex(result).toUpperCase();
        } catch (Exception e) {
            return null;
        }
        return result;
    }


    public static String getIPAddress(Context context) {
        NetworkInfo info = ((ConnectivityManager) context
                .getSystemService(Context.CONNECTIVITY_SERVICE)).getActiveNetworkInfo();
        if (info != null && info.isConnected()) {
            if (info.getType() == ConnectivityManager.TYPE_MOBILE) {//当前使用2G/3G/4G网络
                try {
                    //Enumeration<NetworkInterface> en=NetworkInterface.getNetworkInterfaces();
                    for (Enumeration<NetworkInterface> en = NetworkInterface.getNetworkInterfaces(); en.hasMoreElements(); ) {
                        NetworkInterface intf = en.nextElement();
                        for (Enumeration<InetAddress> enumIpAddr = intf.getInetAddresses(); enumIpAddr.hasMoreElements(); ) {
                            InetAddress inetAddress = enumIpAddr.nextElement();
                            if (!inetAddress.isLoopbackAddress() && inetAddress instanceof Inet4Address) {
                                return inetAddress.getHostAddress();
                            }
                        }
                    }
                } catch (SocketException e) {
                    e.printStackTrace();
                }

            } else if (info.getType() == ConnectivityManager.TYPE_WIFI) {//当前使用无线网络
                WifiManager wifiManager = (WifiManager) context.getSystemService(Context.WIFI_SERVICE);
                WifiInfo wifiInfo = wifiManager.getConnectionInfo();
                String ipAddress = intIP2StringIP(wifiInfo.getIpAddress());//得到IPV4地址
                return ipAddress;
            }
        } else {
            //当前无网络连接,请在设置中打开网络
        }
        return null;
    }

    /**
     * 将得到的int类型的IP转换为String类型
     *
     * @param ip
     * @return
     */
    public static String intIP2StringIP(int ip) {
        return (ip & 0xFF) + "." +
                ((ip >> 8) & 0xFF) + "." +
                ((ip >> 16) & 0xFF) + "." +
                (ip >> 24 & 0xFF);
    }


    //判断当前应用是否是debug状态
    public static boolean isApkInDebug(Context context) {
        try {
            ApplicationInfo info = context.getApplicationInfo();
            return (info.flags & ApplicationInfo.FLAG_DEBUGGABLE) != 0;
        } catch (Exception e) {
            return false;
        }
    }
}

```