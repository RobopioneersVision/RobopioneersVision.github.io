---
title: OpenCV 4 图像与视频的保存.md
toc: true
date: 2021-12-22 09:25:00
---
# OpenCV 4 图像与视频的保存

[https://zhuanlan.zhihu.com/p/101383213](https://zhuanlan.zhihu.com/p/101383213)

![](OpenCV 4 图像与视频的保存/1.jpg)

本文首发于“小白学视觉”微信公众号，欢迎关注公众号

本文作者为小白，版权归人民邮电出版社发行所有，禁止转载，侵权必究！

经过几个月的努力，小白终于完成了市面上第一本OpenCV 4入门书籍《OpenCV 4开发详解》。为了更让小伙伴更早的了解最新版的OpenCV 4，小白与出版社沟通，提前在公众号上连载部分内容，请持续关注小白。

在图像处理过程中会生成新的图像，例如将模糊的图像经过算法变得更加清晰，将彩色图像变成灰度图像，同时需要将处理的结果以图像或者视频的形式保存成文件。本节将详细讲述如何将Mat类矩阵保存成图像或者视频文件。

## 2.4.1 图像的保存

OpenCV提供imwrite()函数用于将Mat类矩阵保存成图像文件，该函数的函数原型在代码清单2-30中给出。

```
代码清单2-30 imwrite()函数原型
1.  bool cv :: imwrite(const String& filename,
2.                         InputArray img,
3.                         Const std::vector<int>& params = std::vector<int>()
4.                         )

```

- filename：保存图像的地址和文件名，包含图像格式
- img：将要保存的Mat类矩阵变量
- params：保存图片格式属性设置标志

该函数用于将Mat类矩阵保存成图像文件，如果成功保存，则返回true，否则返回false。可以保存的图像格式参考imread()函数能够读取的图像文件格式，通常使用该函数只能保存8位单通道图像和3通道BGR彩色图像，但是可以通过更改第三个参数保存成不同格式的图像。不同图像格式能够保存的图像位数如下： - 16位无符号（CV_16U）图像可以保存成PNG、JPEG、TIFF格式文件； - 32位浮点（CV_32F）图像可以保存成PFM、TIFF、OpenEXR和Radiance HDR格式文件； - 4通道（Alpha通道）图像可以保存成PNG格式文件。

函数第三个参数在一般情况下不需要填写，保存成指定的文件格式只需要直接在第一个参数后面更改文件后缀即可，但是当需要保存的Mat类矩阵中数据比较特殊时（如16位深度数据），则需要设置第三个参数。第三个参数的设置方式如代码清单2-31中所示，常见的可选择设置标志在表2-6中给出。

```
代码清单2-31 imwrite()函数中第三个参数设置方式
1.  vector <int> compression_params;
2.  compression_params.push_back(IMWRITE_PNG_COMPRESSION);
3.  compression_params.push_back(9);
4.  imwrite(filename, img, compression_params)

```

表2-6 imwrite()函数第三个参数可选择的标志及作用

![](OpenCV 4 图像与视频的保存/2.svg)

为了更好的理解imwrite()函数的使用方式，在代码清单2-32中给出了生成带有Alpha通道的矩阵，并保存成PNG格式图像的程序。程序运行后会生成一个保存了4通道的png格式图像，为了更直观的看到图像结果，我们在图2-8中给出了Image Watch插件中看到的图像和保存成png格式的图像。

```cpp
代码清单2-32 imgWriter.cpp保存图像
1.  #include <opencv2\opencv.hpp>
2.  #include <iostream>
3.  
4.  using   namespace std;
5.  using   namespace cv;
6.  
7.  void AlphaMat(Mat &mat)
8.  {
9.      CV_Assert(mat.channels() == 4);
10.     for (int i = 0; i < mat.rows; ++i)
11.     {
12.         for (int j = 0; j < mat.cols; ++j)
13.         {
14.             Vec4b& bgra = mat.at<Vec4b>(i, j);
15.             bgra[0] = UCHAR_MAX;  // 蓝色通道
16.             bgra[1] = saturate_cast<uchar>((float(mat.cols - j)) / ((float)mat.cols) 
17.                                                                         * UCHAR_MAX);  // 绿色通道 
18.             bgra[2] = saturate_cast<uchar>((float(mat.rows - i)) / ((float)mat.rows)  
19.                                                                          * UCHAR_MAX);  // 红色通道 
20.             bgra[3] = saturate_cast<uchar>(0.5 * (bgra[1] + bgra[2]));  // Alpha通道 
21.         }
22.     }
23. }
24. int main(int agrc, char** agrv)
25. {
26.     // Create mat with alpha channel
27.     Mat mat(480, 640, CV_8UC4);
28.     AlphaMat(mat);
29.     vector<int> compression_params;
30.     compression_params.push_back(IMWRITE_PNG_COMPRESSION);  //PNG格式图像压缩标志
31.     compression_params.push_back(9);  //设置最高压缩质量
32.     bool result = imwrite("alpha.png", mat, compression_params);
33.     if (!result)
34.     {
35.         cout<< "保存成PNG格式图像失败" << endl;
36.         return -1;
37.     }
38.     cout << "保存成功" << endl;
39.     return 0;
40. }

```

图2-8 程序中和保存后的四通道图像（左：Image Watc， 右:：png文件）

## 2.4.2 视频的保存

有时我们需要将多幅图像生成视频，或者直接将摄像头拍摄到的数据保存成视频文件。OpenCV中提供了VideoWrite()类用于实现多张图像保存成视频文件，该类构造函数的原型在代码清单2-33中给出。

```
代码清单2-33 读取视频文件VideoCapture类构造函数
1.  cv :: VideoWriter :: VideoWriter(); //默认构造函数
2.  cv :: VideoWriter :: VideoWriter(const String& filename,
3.                                          int fourcc,
4.                                          double  fps,
5.                                          Size frameSize,
6.                                          bool  isColor=true
7.                                          )

```

- filename：保存视频的地址和文件名，包含视频格式
- int：压缩帧的4字符编解码器代码，详细参数在表2-7给出。
- fps：保存视频的帧率，即视频中每秒图像的张数。
- framSize：视频帧的尺寸
- isColor：保存视频是否为彩色视频

代码清单2-33中的第1行默认构造函数的使用方法与VideoCapture()相同，都是创建一个用于保存视频的数据流，后续通过open()函数设置保存文件名称、编解码器、帧数等一系列参数。第二种构造函数需要输入的第一个参数是需要保存的视频文件名称，第二个函数是编解码器的代码，可以设置的编解码器选项在表中给出，如果赋值“-1”则会自动搜索合适的编解码器，需要注意的是其在OpenCV 4.0版本和OpenCV 4.1版本中的输入方式有一些差别，具体差别在表2-7给出。第三个参数为保存视频的帧率，可以根据需求自由设置，例如实现原视频二倍速播放、原视频慢动作播放等。第四个参数是设置保存的视频文件的尺寸，这里需要注意的时，在设置时一定要与图像的尺寸相同，不然无法保存视频。最后一个参数是设置保存的视频是否是彩色的，程序中，默认的是保存为彩色视频。

该函数与VideoCapture()有很大的相似之处，都可以通过isOpened()函数判断是否成功创建一个视频流，可以通过get()查看视频流中的各种属性。在保存视频时，我们只需要将生成视频的图像一帧一帧通过“<<”操作符（或者write()函数）赋值给视频流即可，最后使用release()关闭视频流。

表2-7 视频编码格式

![](OpenCV 4 图像与视频的保存/3.svg)

为了更好的理解VideoWrite()类的使用方式，代码清单2-34中给出了利用已有视频文件数据或者直接通过摄像头生成新的视频文件的例程。读者需要重点体会VideoWrite()类和VideoCapture()类的相似之处和使用时的注意事项。

```cpp
代码清单2-34 VideoWriter.cpp保存视频文件
1.  #include <opencv2\opencv.hpp>
2.  #include <iostream>
3.  
4.  using namespace cv;
5.  using namespace std;
6.  
7.  int main()
8.  {
9.      Mat img;
10.     VideoCapture video(0);  //使用某个摄像头
11. 
12.     //读取视频
13.     //VideoCapture video;
14.     //video.open("cup.mp4");  
15. 
16.     if (!video.isOpened())  // 判断是否调用成功
17.     {
18.         cout << "打开摄像头失败，请确实摄像头是否安装成功";
19.         return -1;
20.     }
21. 
22.     video >> img;  //获取图像
23.     //检测是否成功获取图像
24.     if (img.empty())   //判断有没有读取图像成功
25.     {
26.         cout << "没有获取到图像" << endl;
27.         return -1;
28.     }
29.     bool isColor = (img.type() == CV_8UC3);  //判断相机（视频）类型是否为彩色
30. 
31.     VideoWriter writer;
32.     int codec = VideoWriter::fourcc('M', 'J', 'P', 'G');  // 选择编码格式
33.     //OpenCV 4.0版本设置编码格式
34.     //int codec = CV_FOURCC('M', 'J', 'P', 'G'); 
35. 
36.     double fps = 25.0;  //设置视频帧率 
37.     string filename = "live.avi";  //保存的视频文件名称
38.     writer.open(filename, codec, fps, img.size(), isColor);  //创建保存视频文件的视频流
39. 
40.     if (!writer.isOpened())   //判断视频流是否创建成功
41.     {
42.         cout << "打开视频文件失败，请确实是否为合法输入" << endl;
43.         return -1;
44.     }
45. 
46.     while (1)
47.     {
48.         //检测是否执行完毕
49.         if (!video.read(img))   //判断能都继续从摄像头或者视频文件中读出一帧图像
50.         {
51.             cout << "摄像头断开连接或者视频读取完成" << endl;
52.             break;
53.         }
54.         writer.write(img);  //把图像写入视频流
55.         //writer << img;
56.         imshow("Live", img);  //显示图像
57.         char c = waitKey(50);
58.         if (c == 27)  //按ESC案件退出视频保存
59.         {
60.             break;
61.         }
62.     }
63.     // 退出程序时刻自动关闭视频流
64.     //video.release();
65.     //writer.release(); 
66.     return 0;
67. }

```

经过几个月的努力，市面上第一本OpenCV 4入门书籍《OpenCV 4开发详解》将春节后由人民邮电出版社发行。如果小伙伴觉得内容有帮助，希望到时候多多支持！

关注小白的小伙伴可以提前看到书中的内容，我们创建了学习交流群，欢迎各位小伙伴添加小白微信加入交流群，添加小白时请备注“学习OpenCV 4”。