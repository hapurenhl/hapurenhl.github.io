# 二次元图片（手机版）API接口文档

***

## 一.接口

`https://api.hapuren.cn/api/?id=1`

---

## 二.说明
此接口为自动转跳到jpg图片，由于图片均为4k AI加强画质，所以会有卡顿。

---

## 三.示例
### HTML
```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<title>二次元图片（手机版）</title>
</head>
<body>
<h1>二次元图片（手机版）</h1>
<img src="https://www.hapuren.luoyekj.cn/api/?id=1">
</body>
</html>
```
### PHP
```php
<?php
echo '<img src="https://www.hapuren.luoyekj.cn/api/?id=1">';
?>
```
### PYTHON
```python
#!/usr/bin/python3
# -*- coding: utf-8 -*-

import requests
from PIL import Image
from io import BytesIO

response = requests.get('https://www.hapuren.luoyekj.cn/api/?id=1')
response = response.content

BytesIOObj = BytesIO()
BytesIOObj.write(response)
img = Image.open(BytesIOObj)
img.show()
```
### JAVA
```java
import java.awt.*;
import java.io.IOException;
import java.io.InputStream;
import java.net.URL;
import javax.imageio.ImageIO;
import javax.swing.*;

public class WebImageDisplay extends JFrame {
    private JLabel imageLabel;
    
    public WebImageDisplay() {
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setTitle("Web Image Display");
        setSize(400, 400);
        
        imageLabel = new JLabel();
        imageLabel.setHorizontalAlignment(SwingConstants.CENTER);
        add(imageLabel);
    }
    
    public void displayImage(String imageUrl) {
        try {
            URL url = new URL(imageUrl);
            InputStream is = url.openStream();
            Image image = ImageIO.read(is);
            ImageIcon icon = new ImageIcon(image);
            imageLabel.setIcon(icon);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static void main(String[] args) {
        WebImageDisplay imageDisplay = new WebImageDisplay();
        imageDisplay.displayImage("https://www.hapuren.luoyekj.cn/api/?id=1");
        imageDisplay.setVisible(true);
    }
}
```
### GO
```go
package main

import (
	"fmt"
	"image"
	"image/jpeg"
	_ "image/png"
	"log"
	"net/http"
	"os"
)

func main() {
	response, err := http.Get("https://www.hapuren.luoyekj.cn/api/?id=1")
	if err != nil {
		log.Fatal(err)
	}

	defer response.Body.Close()

	img, _, err := image.Decode(response.Body)
	if err != nil {
		log.Fatal(err)
	}

	file, err := os.Create("image.jpg")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	jpeg.Encode(file, img, nil)

	fmt.Println("Image downloaded successfully!")
}
```
### C
```c
#include <stdio.h>
#include <curl/curl.h>
#include <SDL2/SDL.h>

// 回调函数写入图片数据到文件中
size_t write_image_data(void* ptr, size_t size, size_t nmemb, FILE* stream) {
    return fwrite(ptr, size, nmemb, stream);
}

int main() {
    // 初始化libcurl
    curl_global_init(CURL_GLOBAL_DEFAULT);
    CURL* curl = curl_easy_init();
    if (!curl) {
        fprintf(stderr, "Failed to initialize libcurl\n");
        return 1;
    }
    
    // 设置要下载的图片URL
    const char* url = "https://www.hapuren.luoyekj.cn/api/?id=1";
    
    // 打开文件以写入图片数据
    FILE* file = fopen("image.jpg", "wb");
    if (!file) {
        fprintf(stderr, "Failed to open file\n");
        return 1;
    }
    
    // 配置libcurl请求
    curl_easy_setopt(curl, CURLOPT_URL, url);
    curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, write_image_data);
    curl_easy_setopt(curl, CURLOPT_WRITEDATA, file);
    
    // 发送请求并接收响应
    CURLcode res = curl_easy_perform(curl);
    if (res != CURLE_OK) {
        fprintf(stderr, "Failed to download image: %s\n", curl_easy_strerror(res));
        return 1;
    }
    
    // 关闭文件
    fclose(file);
    
    // 清理和释放资源
    curl_easy_cleanup(curl);
    curl_global_cleanup();
    
    // 使用图形库加载和显示图像
    SDL_Init(SDL_INIT_VIDEO);
    SDL_Window* window = SDL_CreateWindow("Image Display", SDL_WINDOWPOS_UNDEFINED, SDL_WINDOWPOS_UNDEFINED, 800, 600, 0);
    SDL_Renderer* renderer = SDL_CreateRenderer(window, -1, 0);
    SDL_Surface* image = SDL_LoadBMP("image.jpg");
    SDL_Texture* texture = SDL_CreateTextureFromSurface(renderer, image);
    SDL_RenderCopy(renderer, texture, NULL, NULL);
    SDL_RenderPresent(renderer);
    
    // 等待用户退出程序
    SDL_Event event;
    while (SDL_WaitEvent(&event)) {
        if (event.type == SDL_QUIT) {
            break;
        }
    }
    
    // 清理和释放资源
    SDL_DestroyTexture(texture);
    SDL_FreeSurface(image);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window);
    SDL_Quit();
    
    return 0;
}
```
### C++
```c++
#include <iostream>
#include <fstream>
#include <curl/curl.h>
#include <opencv2/opencv.hpp>

size_t write_image_data(void* ptr, size_t size, size_t nmemb, FILE* stream) {
    return fwrite(ptr, size, nmemb, stream);
}

int main() {
    // 初始化libcurl
    curl_global_init(CURL_GLOBAL_DEFAULT);
    CURL* curl = curl_easy_init();
    if (!curl) {
        std::cerr << "Failed to initialize libcurl" << std::endl;
        return 1;
    }
    
    // 设置要下载的图片URL
    const char* url = "https://www.hapuren.luoyekj.cn/api/?id=1";
    
    // 打开文件以写入图片数据
    FILE* file = fopen("image.jpg", "wb");
    if (!file) {
        std::cerr << "Failed to open file" << std::endl;
        return 1;
    }
    
    // 配置libcurl请求
    curl_easy_setopt(curl, CURLOPT_URL, url);
    curl_easy_setopt(curl, CURLOPT_WRITEFUNCTION, write_image_data);
    curl_easy_setopt(curl, CURLOPT_WRITEDATA, file);
    
    // 发送请求并接收响应
    CURLcode res = curl_easy_perform(curl);
    if (res != CURLE_OK) {
        std::cerr << "Failed to download image: " << curl_easy_strerror(res) << std::endl;
        return 1;
    }
    
    // 关闭文件
    fclose(file);
    
    // 清理和释放资源
    curl_easy_cleanup(curl);
    curl_global_cleanup();
    
    // 使用OpenCV加载和显示图像
    cv::Mat image = cv::imread("image.jpg");
    if (image.empty()) {
        std::cerr << "Failed to load image" << std::endl;
        return 1;
    }
    
    cv::namedWindow("Image Display", cv::WINDOW_NORMAL);
    cv::imshow("Image Display", image);
    cv::waitKey(0);
    
    return 0;
}
```
##  提醒
* 本接口已开源
* 本接口适用于CC BY-NC-ND 4.0 协议 和 GP协议
* 本接口提供是PHP随机数，可能有重复！
* 请合理使用接口
