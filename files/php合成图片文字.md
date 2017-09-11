## 基本需求

自定义合成图片和文字，可以是多张图片，可以是多行文字，目标是把它们合成一张图片。



## 实现思路



- 可以依靠php 的 GD和图像处理函数 实现。
- 需要确认 mbstring 和 exif 这两个扩展已经安装。
- 实现下载文字样式文件，一般存放：public/file/font/msyhbd.ttf
- 核心代码如下：



```php
        // 1.获取图片资源
        $image = $this->openImage(\Yii::getAlias('@static/' . $imageUrl));

        // 2.加入LOGO
        $logo = $this->openImage($this->getLogo());
        list($width, $height) = getimagesize($this->getLogo());
        $qrsize = 100;
        $qrcodeCopy = imagecreate($qrsize, $qrsize);
        imagecopyresized($qrcodeCopy, $logo, 0,0,0,0, $qrsize, $qrsize, $width, $height);
        imagecopymerge($image, $qrcodeCopy, 10, 10, 0, 0, $qrsize, $qrsize, 100);

        // 3.加入二维码
        $qrcode = $this->openImage($this->getQrCode());
        list($width, $height) = getimagesize($this->getQrCode());
        $qrsize = 100;
        $qrcodeCopy = imagecreate($qrsize, $qrsize);
        imagecopyresized($qrcodeCopy, $qrcode,0,0,0,0, $qrsize, $qrsize, $width, $height);
        imagecopymerge($image, $qrcodeCopy, 610, 1170, 0, 0, $qrsize, $qrsize, 100);

        // 4.加入文字
        $color = imagecolorclosest($image, 19, 130, 20);
        $content = preg_replace("/；/", ';', $content);
        $arr = explode(';', $content);
		// 多行文字处理
        foreach ($arr as $k => $v) {
            $distance = $k*50;
            $fontSize = $k==0 ? 36 : 18;
          imagettftext($image,$fontSize,0,60,$distance,$color,static::getFontLocate(),$v);
        }

        // 5.合成图片
        $result = imagepng($image, $this->getLocate(), 9, PNG_FILTER_NONE);
        if ($result === false) {
            throw new BusinessException('image create failure');
        }

        return static::DOC_LOCATE . '/' . $this->getName();
```



```php
    protected function openImage($location)
    {
        if (exif_imagetype($location) == IMAGETYPE_GIF) {
            return imagecreatefromgif($location);
        }

        if (exif_imagetype($location) == IMAGETYPE_PNG) {
            return imagecreatefrompng($location);
        }
        if (exif_imagetype($location) == IMAGETYPE_JPEG) {
            return imagecreatefromjpeg($location);
        }
        throw new \Exception('unknown type:' . $location);
    }
```

```php
    protected static function getFontLocate()
    {
        return \Yii::getAlias('@static/public/file/font/msyhbd.ttf');
    }
```



## 综述



无论是框架还是原生PHP，上传图片的时候资源都是存放在 $_FILES 中的。



上传图片时，页面 html 需要加上这个属性：

```html
<form ...... enctype="multipart/form-data">
  ...

  <input type='file' name='upload_image'/>
  
  ...
</form>
```





