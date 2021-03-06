Android SharePreferences加密库
=====
>项目地址：https://github.com/iamMehedi/Secured-Preference-Store

用法
-----------
1.gradle文件添加依赖
```
compile 'online.devliving:securedpreferencestore:latest_version'
```

2.查看latest_version的版本
<br>
<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-6/sp2.png"/></div>

<br>
>sdk版本过低会出现如下报错，将最低版本改为18即可
>


<br>

<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-6/sp1.png"/></div>

3.在application中初始化
```
SecuredPreferenceStore.init(this, object : RecoveryHandler() {
            override fun recover(p0: Exception?, p1: KeyStore?, p2: MutableList<String>?, p3: SharedPreferences?): Boolean {
                return true
            }
        })
var prefStore = SecuredPreferenceStore.getSharedInstance()
```

4.保存
```
prefStore?.Editor()?.putString(resources?.getString(R.string.sp_key_key), "key")?.apply()
```
5.获取
```
var username = prefStore?.getString(resources?.getString(R.string.sp_key_user_name), "")!!

```

6.查看文件

<br>

<div align=center><img  src="https://raw.githubusercontent.com/liangfeng093/MarkdownBlogs/master/res/2018-6/sp3.png"/></div>
```
<?xml version='1.0' encoding='utf-8' standalone='yes' ?>
<map>
    <string name="23AC944BE61D6C1753B9495D074D758D2501396868DAA87C0A4A964DC2666AB5">f5xZoiLr9dIsub8ZaCynyQ==]o8yT2vnNO5u73iUtIOr/Cg==]Tv0g6M5VQCICHJ1darHL0u5j9tEo76GdSJWFPZPVPu4=</string>
    <string name="9305996C79A7C4D58837D14166799048E2E7342A29FF4E25D9F039B2004F8BC3">+emI1ulMEAecZWxkAqrNjQ==]MBJUBIO0cUGh6/PAAz0XCg==]5mq1LvUfz6UBhc/fnGSY8hya8S0lbpPT2uEOrQYYYzw=</string>
    <string name="33188AFFEC74B412765C3C86859DE4620B5427C774D92F9026D95A7A8AAE1F96">UXWHXf37olg6HuTrPMskdjqWcuBN69GtWLgjG+sd8TSnMhuK6+Yx4lF1N+auSHn7r7WS5Dli6mvEKfub71DN65VVlFiJZB9KRnPQcwB0vg5s+Y3p5uMIOzcq/QcjPR8Vi7bSXCU6N1HLj+spjTklVs+n23jrlz57NeAjwh2IgFLbZHkQccUvJR+Sf/jkirJZ+5BRHB3Ll8eDmbyDcZ1a2aaKxL0CsQbE2RpRTrKLUNhlRtZhVLG5rChGZ8pj67JAQW7rz1JuWaqNST2N/huJn2cB1vKz2Xkrfuow+P9TuBXbRGgO8FfkSlf7hcir0vEMqxFXEFoME3p4J+RXsRNwgQ==</string>
    <string name="7283C85FBCDEEC72F7BE3F26855F43C406AAFBE4116B8A8BEA802FA0B0793D3E">uc9eQLbapquzNr7lnORgkg==]arUQYgmQ8RVZqcqAi5SfsQ==]OF7GhorIcx+vo2NIwpv3zvR1p98tz7ADRsmZF3ZIoP8=</string>
    <string name="62D6FBC5B297716C7D7A322E0BE983DBFD90F60538AF09210B4B047BF893B819">lBoA4OIl7tBPM17MvqvygA==]pWA9L1sHKUYTjwtalN2zpg==]iT6+2Wy40OUjPFTs9iSAIo5kKbM1TO61dDpixpu0avo=</string>
    <string name="F82F7CEF78F04099E7C79CDCD0C8997186F7A8D503699EE59B11643FF0E213B0">v8PKmADX2l8HJinwZ04SFg==]XKFDKwAgTdxtt3ds7b9k3A==]ldEv4U5VyjRQWRJdED7iiWOcbWPzE6HBxivJNcjHwC8=</string>
    <string name="575D22A21F87C8E03D09C9A142F768821A01C90805D4E503D217518F9018C74F">2z0wjwy7P4OErhQpI3u9QA==]CXbo7D2SDbHrb9u+syDVGg==]wETDwymBu4wuOoM4FsilXbW8CdXm8JU7cBnh7AUBxOs=</string>
    <string name="AC439037EE764FEC7864A0E6C33FFD05975724322ED14C3FE861E38F6D2218FE">XAY5gBSk4PHRAggPzt5be/4PC36co4WLsP0UONxLaJ8NpdORGAR9pNmaAp9+AK1+sbZ8QFbEW46akLbYzcxFgUnregb9Z2ggeuC+SwTBV1sTU4zKxgyo81xAXKuOe4vN3SNtEbirE9l+ZywOAgi4/PDcmPAvTOvFTzy+WpXmfzydd6hLoL16UBjNj4vdVxMb6FFDckSVIrnQUmkE7voy44JEYS1xHLpUpYyo9Udu4/X09VJ6rh3J5rTUU6oH3GI5NijB/Y0lkObm2C8dVEkTZIrw/0WDj8wQo9RUM3ubzQ41F2z94gzf6BS8yviI4R6ZwP8BGjrOfxDLimScDnlgvw==</string>
    <string name="D340C2F372FC54873E4B1DF6F6D0AA13789DAAA679E7C4ADB73264E595F4175E">d5rqqeOGG2kN/+9Sl8OvJg==]VvPjhedauX8d0seum98a/w==]lPjWxFvmIsZKUDk//RgWJz2iiji+FIFZfDxAIGo43FI=</string>
    <string name="A6F63A5FB10B3BBA180A79F2FC565B1DB2101040CE71EA80692D671857FE2117">/CpJ3LZX0coIwuufdj/1sw==]80It/QWi8XGzs3EYQN6vyA==]IaeIaa//dOGs5tbEHGSE/SuVlHUliYUqkx1Vw5jzEbQ=</string>
    <string name="A8BDF00FFF8843B54413EBCAA4AF06D94D2AFA5C9F3EE27EB4AFEC3E2C883A9C">N3CI+iRjCKoIl7Z/FhWM7Q==]3C1eV/u94VeKd4Dg/HarBA==]f2m+gUJ2Ud9hTTL9Lvw0PyBQRb4cRE85MXJGHerkuPQ=</string>
    <string name="85449A911F96D2FA0CB4EA3491CADDD64BC5939D8216580722F5D0BBD7C3169D">rJbt0S/iQGhA14AZiN7dKA==]qx0jr4bYd8PpuxyhiiYO/A==]h5laOOzMj76uiN71KCBvuOyr0hv6JI88KqVM9HsRi80=</string>
    <string name="5251BD13CF2A11139AA218E50652E220DC58EB9D1C76216BD39689A540BAABD1">TXct4pzcHM+lK8acqr785w==]iCpMIM5D0YJ/7fQuOlIwzQ==]Garlyprlr2ChCS/GCPlSFxwErsx34gPn7vdzdKp+wK0=</string>
    <boolean name="41D7709917874F2FC1A1E28D77BC9BAFDE1FBB4DC7ED123C1FC3111624ECEE80" value="true" />
    <string name="C7B7C5D81A910586418E03796DD5196D99CF85B003905DF2FF1D300F4C17D65C">O42j6tb/Jug9jmj789OHnw==]kYH7eQBbw3u84Ef6S9OLfg==]DVSvjGb2I0XvY2TiCZcmsHkQT9jHGJSkYOpgTLxn25Y=</string>
    <string name="2C70E12B7A0646F92279F427C7B38E7334D8E5389CFF167A1DC30E73F826B683">GFILZ+o7VvmNvnAmksFlug==]pSIWSe+BOODCSP+UEcWVLTIWU9deNnP6hYWxzPGdZplM4Z80WOaMIHRV7NQsSs3B]ia3+zXz7WjjFXPezg9PaxivH4fzrnpjs2+DTcZdVyBU=</string>
    <string name="43DFEBF09621CF7606D97130B27CEDF3BE80CD7CC83A6B8BB5E28A6037EE2281">jy7e3of946i0t0J8yn0akg==]+TG6f/0+K1qj7VmwYJLh5Q==]aidTUIqm8IVol+GdfblGEZHz4MEx5Wy2w9QZd07rvP8=</string>
    <string name="5E82CEAE37C209FF05154CFB8BA8AF1919F4842CF249DF72D444EF630D6F2EB6">lUU/FiPqLIev8CgwVsi+CA==]cvLHJbFJAuPaNoZqK4M/uQ==]3y18+fUDbo+kVZ0LpmijDAUbXlQ4bS6HoCh+0euiPLM=</string>
    <string name="26527177A30520E4F3173D7FDE9210A5D6588C6B9DE69103E9DC427A981E6529">xk5kHz2kqzChmvLHuqmTUg==]kVEBttU8eWZnI7ahWgeIrQ==]Q7Z2LEkelIwmsgu9cHyxjFyoKKEQasuA8R9QdbKQVOI=</string>
</map>

```