# 虎课视频批量下载

## 实现方法

### 视频解密

m3u8链接中自带key和iv，aes-128解密KEY,然后aes-128解密视频

解密代码

```
def getm3u8(app_id,tx_file_id,token):
    key = '0000000000000000'
    overlayKey = key.encode().hex()
    overlayIv = key.encode().hex()
    geturl = f'https://playvideo.qcloud.com/getplayinfo/v4/{app_id}/{tx_file_id}?psign={token}&overlayKey={overlayKey}&overlayIv={overlayIv}'
    response_get = requests.get(geturl).json()
    # 得到3个参数 drmToken title m3u8列表
    drmToken = response_get['media']['streamingInfo']['drmToken']
    title = response_get['media']['basicInfo']['name']
    playlisturl = response_get['media']['streamingInfo']['drmOutput'][0]['url']
 
    headersurl = '/'.join(playlisturl.split('/')[:-1]) + '/'
    parms = playlisturl.split('/')[-1]
    playlisturl = headersurl + 'voddrm.token.' + drmToken + '.' + parms
    m3u8url = headersurl + requests.get(playlisturl).text.split('\n')[-2]
 
    # 得到 key
    m3u8text = requests.get(m3u8url).text
    keyurl = re.findall('(?<=METHOD=AES-128,URI=").+?(?=")', m3u8text)[0]
    # print(keyurl)
    encryptkey = requests.get(keyurl).content
    cryptor = AES.new(key=key.encode(), mode=AES.MODE_CBC, iv=key.encode())
 
    decryptkey = cryptor.decrypt(encryptkey)
         # base64编码的解密key
    decryptkey = base64.b64encode(decryptkey).decode()
 
    print(title, m3u8url,decryptkey)
```



### 批量爬取

python 实现

### m3u8下载

https://github.com/Nchujx/m3u8down

## 成品

https://github.com/Nchujx/huke/releases

[![WE9fFs.jpg](https://z3.ax1x.com/2021/07/13/WE9fFs.jpg)](https://imgtu.com/i/WE9fFs)

视频教学： https://github.com/Nchujx/huke/blob/main/%E8%A7%86%E9%A2%91%E6%95%99%E5%AD%A6.mp4





