---
title: Object Tracking using OpenCV (C++ Python)（使用OpenCV）.md
toc: true
date: 2021-12-22 09:25:00
---
# Object Tracking using OpenCV (C++/Python)（使用OpenCV进行目标跟踪)

[https://www.cnblogs.com/annie22wang/p/9366610.html](https://www.cnblogs.com/annie22wang/p/9366610.html)

本博客翻译搬运自https://www.learnopencv.com/object-tracking-using-opencv-cpp-python，用于初入目标跟踪的新手学习，转贴请注明！

**使用OpenCV进行目标跟踪（C++/Python）**

在本教程里，我们将学习OpenCV3.0中引入的OpenCV跟踪API。我们将学习如何以及何时使用OpenCV3.4.1中提供的7中不同的跟踪器——BOOSTING,MIL,KCF,TLD,MEDIANFLOW,GOTURN和MOSSE。我们还将学习现代跟踪算法背后的一般理论。

我的朋友Boris Babenko完美的解决了这个问题，正如下面的这个完美的实时人脸跟踪器所示！开玩笑的说，下面的这个GIF描述了我们想要的理想物体跟踪器——速度，准确性和面对遮挡的鲁棒性。

![](Object Tracking using OpenCV (C++ Python)（使用OpenCV）/real-time-face-tracking.gif)

OpenCV目标跟踪样例：

如果你没有时间阅读整个教程，那就看看下面这个视频并了解一下用法。但是如果你想系统学习一下目标跟踪，那就完整读下去。

视频地址（需翻墙）：https://www.youtube.com/watch?reload=9&v=61QjSz-oLr8&feature=youtu.be

**什么是目标跟踪？**

简单的说，在一个视频的连续帧中定位目标就称之为跟踪。

这个定义听起来很直接但是在计算机视觉和机器学习领域，跟踪是一个非常广泛的术语，涵盖概念上相似但技术上不同的想法。举个例子，下面所有不同的但却相关的方法均是可用在目标跟踪的研究领域的。

1.密集光流法：这类算法有助于估计视频帧中每个像素的运动矢量。

2.稀疏光流法：这类算法，如Kanade-Lucas-Tomashi(KLT)特征跟踪器，跟踪图像中几个特征点的位置。

3.卡尔曼滤波：这是一个非常流行的信号处理算法，用于根据先前的运动信息预测运动物体的位置。这种算法的早期应用之一是导弹制导！

4.Meanshift和Camshift：这些是用于定位密度函数的最大值的算法。它们也用于跟踪领域。

5.单目标跟踪器：在此类跟踪器中，第一帧使用矩形标记来指示我们要跟踪的对象的位置。然后使用跟踪算法在后续帧中跟踪对象。在大多数实际应用中，这些跟踪器与物体检测器结合使用。

6.多目标跟踪器：在我们有快速物体探测器的情况下，检测每个帧中的多个物体然后运行轨迹查找算法来识别一帧中的哪个矩形对应于下一帧中的矩形是有意义的。

**跟踪VS检测**

如果你曾经使用OpenCV做过面部检测，你就明白它可以实时工作，你可以轻松地检测每一帧中的脸部。那么，你为什么需要首先进行跟踪？让我们探讨一下你可能想要跟踪视频中对象的不同原因，而不仅仅是重复检测。

1.**跟踪比检测快**：通常跟踪算法比检测算法快。理由很简单。当你跟踪在前一帧中检测到的对象时，你对该目标的外观了解很多。你还知道前一帧的位置以及运动的方向和速度。因此在下一帧中，您可以使用所有这些信息来预测下一帧中目标的位置，并围绕目标的预期位置进行小搜索，以准确定位目标。 一个好的跟踪算法将使用它所拥有的关于该目标的所有信息，而检测算法总是从头开始。因此，在设计有效系统时，通常在每第n帧上运行目标检测，而在其间的n-1帧中采用跟踪算法。为什么我们不直接检测第一帧中的目标并随后跟踪？确实，跟踪可以从它拥有的额外信息中获益，但是当它们长时间在障碍物后面或者如果它们移动速度太快以至于跟踪算法无法赶上时，你也可能失去对目标的跟踪。跟踪算法累积错误也很常见，跟踪目标的边界框会慢慢偏离其正在跟踪的目标。为了通过跟踪算法解决这些问题，每隔一段时间运行一次检测算法。检测算法在目标的大量示例上进行训练。因此，他们对目标的一般类有更多的了解。另一方面，跟踪算法更多地了解他们正在跟踪的类的特定实例。

2.**检测失败时跟踪可以提供帮助**：如果你在视频上运行人脸检测器并且人脸被物体遮挡，那么人脸检测大概率会失败。另一方面，优秀的跟踪算法可以解决某种程度的遮挡。在下面的视频中，你可以看到MIL跟踪器的作者Boris Bakenko博士演示MIL跟踪器如何在遮挡下工作

3.**跟踪保留身份**：目标检测的输出是包含目标的矩形数组。但是，目标没有附加标识。例如，在下面的视频中，检测红点的检测器将输出对应于它在帧中检测到的所有点的矩形。在下一帧中，它将输出另一个矩形数组。在第一帧中，特定点可以由阵列中位置10处的举行表示，并且在第二帧中，它可以在位置17处。当在帧上使用检测时，我们不知道哪个矩形对应于哪个目标。另一方面，跟踪提供了一种字面连接点的方法。

视频地址（需翻墙）：[https://www.youtube.com/watch?v=SsiHH_wrwDg](https://www.youtube.com/watch?v=SsiHH_wrwDg)

**OpenCV 跟踪API**

OpenCV3附带了新的跟踪API，其中包括许多单目标跟踪算法的实现。OpenCV 3.4.1中有7种不同的跟踪器——BOOSTING,MIL,KCF,TLD,MEDIANFLOW,GOTURN和MOSSE。

**注意**：OpenCV 3.2包括6种跟踪器——BOOSTING，MIL，TLD，MEDIANFLOW和MOSSE。OpenCV 3.1有5种跟踪器——BOOSTING,MIL,KCF,TLD,MEDIANFLOW。OpenCV 3.0包括4种跟踪器——BOOSTING,MIL,TLD,MEDIANFLOW。

**更新**:在OpenCV 3.3种，跟踪API已更改。请检查代码版本然后使用相应的API。

在我们提供算法的简单描述之前，让我们看看设置和用法。在下面的注释代码中，我们首先通过选择跟踪器类型来设置跟踪器——BOOSTING，MIL，KCF，TLD，MEDIANFLOW，GOTURN或MOSSE。然后我们打开一个视频并读取一帧。我们定义一个包含第一帧目标的边界框，并用第一帧和边界框初始化跟踪器。最后，我们从视频中读取帧并仅在循环中更新跟踪器以获得当前帧的新边界框。随后显示结果。

**C++**

```
#include <opencv2/opencv.hpp>
#include <opencv2/tracking.hpp>
#include <opencv2/core/ocl.hpp>
 
using namespace cv;
using namespace std;
 
// Convert to string
#define SSTR( x ) static_cast< std::ostringstream & >( \
( std::ostringstream() << std::dec << x ) ).str()
 
int main(int argc, char **argv)
{
    // List of tracker types in OpenCV 3.4.1
    string trackerTypes[7] = {"BOOSTING", "MIL", "KCF", "TLD","MEDIANFLOW", "GOTURN", "MOSSE"};
    // vector <string> trackerTypes(types, std::end(types));
 
    // Create a tracker
    string trackerType = trackerTypes[2];
 
    Ptr<Tracker> tracker;
 
    #if (CV_MINOR_VERSION < 3)
    {
        tracker = Tracker::create(trackerType);
    }
    #else
    {
        if (trackerType == "BOOSTING")
            tracker = TrackerBoosting::create();
        if (trackerType == "MIL")
            tracker = TrackerMIL::create();
        if (trackerType == "KCF")
            tracker = TrackerKCF::create();
        if (trackerType == "TLD")
            tracker = TrackerTLD::create();
        if (trackerType == "MEDIANFLOW")
            tracker = TrackerMedianFlow::create();
        if (trackerType == "GOTURN")
            tracker = TrackerGOTURN::create();
        if (trackerType == "MOSSE")
            tracker = TrackerMOSSE::create();
    }
    #endif
    // Read video
    VideoCapture video("videos/chaplin.mp4");
     
    // Exit if video is not opened
    if(!video.isOpened())
    {
        cout << "Could not read video file" << endl; return 1; } // Read first frame Mat frame; bool ok = video.read(frame); // Define initial boundibg box Rect2d bbox(287, 23, 86, 320); // Uncomment the line below to select a different bounding box bbox = selectROI(frame, false); // Display bounding box. rectangle(frame, bbox, Scalar( 255, 0, 0 ), 2, 1 ); imshow("Tracking", frame); tracker->init(frame, bbox);
     
    while(video.read(frame))
    {     
        // Start timer
        double timer = (double)getTickCount();
         
        // Update the tracking result
        bool ok = tracker->update(frame, bbox);
         
        // Calculate Frames per second (FPS)
        float fps = getTickFrequency() / ((double)getTickCount() - timer);
         
        if (ok)
        {
            // Tracking success : Draw the tracked object
            rectangle(frame, bbox, Scalar( 255, 0, 0 ), 2, 1 );
        }
        else
        {
            // Tracking failure detected.
            putText(frame, "Tracking failure detected", Point(100,80), FONT_HERSHEY_SIMPLEX, 0.75, Scalar(0,0,255),2);
        }
         
        // Display tracker type on frame
        putText(frame, trackerType + " Tracker", Point(100,20), FONT_HERSHEY_SIMPLEX, 0.75, Scalar(50,170,50),2);
         
        // Display FPS on frame
        putText(frame, "FPS : " + SSTR(int(fps)), Point(100,50), FONT_HERSHEY_SIMPLEX, 0.75, Scalar(50,170,50), 2);
 
        // Display frame.
        imshow("Tracking", frame);
         
        // Exit if ESC pressed.
        int k = waitKey(1);
        if(k == 27)
        {
            break;
        }
 
    }
}
```

**Python**

```
import cv2
import sys
 
(major_ver, minor_ver, subminor_ver) = (cv2.__version__).split('.')￼
 
if __name__ == '__main__' :
 
    # Set up tracker.
    # Instead of MIL, you can also use
 
    tracker_types = ['BOOSTING', 'MIL','KCF', 'TLD', 'MEDIANFLOW', 'GOTURN', 'MOSSE']
    tracker_type = tracker_types[2]
 
    if int(minor_ver) < 3:
        tracker = cv2.Tracker_create(tracker_type)
    else:
        if tracker_type == 'BOOSTING':
            tracker = cv2.TrackerBoosting_create()
        if tracker_type == 'MIL':
            tracker = cv2.TrackerMIL_create()
        if tracker_type == 'KCF':
            tracker = cv2.TrackerKCF_create()
        if tracker_type == 'TLD':
            tracker = cv2.TrackerTLD_create()
        if tracker_type == 'MEDIANFLOW':
            tracker = cv2.TrackerMedianFlow_create()
        if tracker_type == 'GOTURN':
            tracker = cv2.TrackerGOTURN_create()
        if tracker_type == 'MOSSE':
            tracker = cv2.TrackerMOSSE_create()
 
    # Read video
    video = cv2.VideoCapture("videos/chaplin.mp4")
 
    # Exit if video not opened.
    if not video.isOpened():
        print "Could not open video"
        sys.exit()
 
    # Read first frame.
    ok, frame = video.read()
    if not ok:
        print 'Cannot read video file'
        sys.exit()
     
    # Define an initial bounding box
    bbox = (287, 23, 86, 320)
 
    # Uncomment the line below to select a different bounding box
    bbox = cv2.selectROI(frame, False)
 
    # Initialize tracker with first frame and bounding box
    ok = tracker.init(frame, bbox)
 
    while True:
        # Read a new frame
        ok, frame = video.read()
        if not ok:
            break
         
        # Start timer
        timer = cv2.getTickCount()
 
        # Update tracker
        ok, bbox = tracker.update(frame)
 
        # Calculate Frames per second (FPS)
        fps = cv2.getTickFrequency() / (cv2.getTickCount() - timer);
 
        # Draw bounding box
        if ok:
            # Tracking success
            p1 = (int(bbox[0]), int(bbox[1]))
            p2 = (int(bbox[0] + bbox[2]), int(bbox[1] + bbox[3]))
            cv2.rectangle(frame, p1, p2, (255,0,0), 2, 1)
        else :
            # Tracking failure
            cv2.putText(frame, "Tracking failure detected", (100,80), cv2.FONT_HERSHEY_SIMPLEX, 0.75,(0,0,255),2)
 
        # Display tracker type on frame
        cv2.putText(frame, tracker_type + " Tracker", (100,20), cv2.FONT_HERSHEY_SIMPLEX, 0.75, (50,170,50),2);
     
        # Display FPS on frame
        cv2.putText(frame, "FPS : " + str(int(fps)), (100,50), cv2.FONT_HERSHEY_SIMPLEX, 0.75, (50,170,50), 2);
 
        # Display result
        cv2.imshow("Tracking", frame)
 
        # Exit if ESC pressed
        k = cv2.waitKey(1) & 0xff
        if k == 27 : break
```

**目标跟踪算法**

在本节中，我们将深入研究不同的跟踪算法。目标并不是要对每个跟踪器都有深入的理论理解，而是从实际的角度理解它们。

首先让我解释一下跟踪背后的一些一般原则。 在跟踪中，我们的目标是在当前帧中找到一个对象，因为我们已经在所有（或几乎所有）前一帧中成功跟踪了对象。

由于我们已经跟踪了当前帧的目标，因此我们知道它是如何移动的。 换句话说，我们知道运动模型的参数。 运动模型只是一种奇特的方式，表示你知道前一帧中物体的位置和速度（速度+运动方向）。如果你对该目标一无所知，则可以根据当前运动模型预测新位置，并且你将非常接近目标的新位置。

我们有更多的信息，但是它们仅仅是关于目标的运动的。我们知道目标在每个先前帧中的外观。换句话说，我们可以构建一个外观模型来编码目标的外观。该外观模型可用于在运动模型预测的位置的小领域中搜索，以更准确地预测目标的位置。

运动模型预测目标的大致位置，外观模型精细调整该估计以基于外观提供更准确的估计。

如果目标非常简单并且没有更改它的外观，我们可以使用一个简单的模板作为外观模型并查找该模板。然而，显示并非那么简单。目标的外观可能会发生巨大的变化，为了解决这个问题，在许多现代跟踪器中，外观模型是以在线方式训练的分类器。别怕，让我用简单的术语解释一下。分类器的工作是将图像的矩形区域分类为目标或背景。分类器将图像作为输入，并返回0和1之间的分数，以指示图像块包含目标的概率。当绝对确定图像块是背景时得分为0，当绝对确定图像块是目标时得分为1。在机器学习中，我们使用“在线”一词来指代在运行时即时训练的算法。 离线分类器可能需要数千个示例来训练分类器，但是在线分类器通常在运行时使用极少数示例进行训练。通过将其分为正（目标）和负（目标）示例来训练分类器。 如果你想建立一个用于检测猫的分类器，你可以使用包含猫的数千个图像和数千个不包含猫的图像来训练它。 通过这种方式，分类器学会区分什么是猫而什么不是。 你可以在此处详细了解图像分类。 在构建在线分类器的过程中，我们没有数千个正面和负面类的例子。

让我们看看不同的跟踪算法如何解决在线训练的这个问题。

**BOOSTING跟踪器**

这个跟踪器基于AdaBoost的在线版本——基于HAAR面部检测器在内部使用的算法。需要在运行时使用目标的正面和负面示例训练此分类器。由用户（或另一个目标检测算法）提供的初始边界框被视为对象的正例，并且边界框外的许多图像块被视为背景。 给定新帧，分类器在先前位置的邻域中的每个像素上运行，并且记录分类器的分数。 目标的新位置是得分最大的位置。 所以现在我们又有了一个分类器的正面例子。 随着更多帧进入，分类器将使用此附加数据进行更新

优点：没有。 这个算法已有十年历史了，虽然工作正常，但我找不到使用它的好理由，特别是当其他基于类似原理的高级跟踪器（MIL，KCF）可用时。

缺点：跟踪性能平庸。且它无法可靠地知道跟踪失败的时间。

**MIL跟踪器**

该跟踪器在概念上类似于上述的BOOSTING跟踪器。 最大的区别在于，不是仅考虑目标的当前位置作为正例，而是在当前位置周围的小邻域中查找以产生若干潜在的正例。 你可能认为这是一个坏主意，因为在大多数这些“积极”的例子中，目标不是居中的。这正是MIL拯救的地方。在MIL中，你没有指定正面和负面的例子，而是正面和负面的“包”。 正面“包”中的图像集合并非都是正面的例子。 相反，只有正面包中的一个图像需要是一个正面的例子！ 在我们的示例中，正面包包含以目标当前位置为中心的补丁，以及在其周围的小邻域中的补丁。 即使被跟踪目标的当前位置不准确，当来自当前位置附近的样本被放入正面包中时，该包很可能包含至少一个图像，其中目标很好地居中。 MIL项目主页为喜欢深入了解MIL跟踪器内部工作原理的人提供了更多信息。

MIL项目主页：http://vision.ucsd.edu/~bbabenko/new/project_miltrack.shtml

优点：表现非常好，它不会像BOOSTING跟踪器那样漂移，并且在部分遮挡下可以完成合理的工作，如果你使用的是OpenCV3.0，这可能是你可以使用的最佳跟踪器。但是如果你是用的是更高版本，请考虑使用KCF。

缺点：不能可靠的报告跟踪失败，以及无法从完全遮挡中恢复。

**KCF跟踪器**

KCF表示核相关滤波器，该跟踪器基于前两个跟踪器中提出的想法，利用MIL跟踪器中使用的多个正样本具有大的重叠区域的事实，这种重叠的数据导致一些很好的数学属性，这个跟踪器利用这些属性可以使跟踪更快更准确。

优点：精度和速度都优于MIL，它报告跟踪失败比BOOSTING和MIL更好。 如果你使用的是OpenCV 3.1及更高版本，我建议你在大多数应用程序中使用它。

缺点：无法从完全遮挡中恢复。 未在OpenCV 3.0中实现。

BUG警告：OpenCV 3.1（仅限Python）中存在一个错误，因为返回了错误的边界框。 查看错误报告。 感谢Andrei Cheremskoy指出这一点。

错误报告：https://github.com/opencv/opencv_contrib/issues/640

**TLD跟踪器**

TLD代表tracking，learning和detection。顾名思义，该跟踪器将长期跟踪任务分解为三个部分——（短期）跟踪，学习和检测。作者的论文中指出，“跟踪器在帧与帧之间跟踪对象。检测器定位到目前为止观察到的所有外观，并在必要时纠正跟踪器。 学习估计检测器的错误并更新它以避免将来出现这些错误。“这个跟踪器的输出往往会跳跃一下。 例如，如果你正在跟踪行人并且场景中还有其他行人，则此跟踪器有时可以临时跟踪与你要跟踪的行人不同的行人。 从积极的方面来看，这个跟踪器似乎可以在更大的大小，运动和遮挡上跟踪物体。 如果你有一个视频序列，其中目标隐藏在另一个目标后面，则此跟踪器可能是一个不错的选择。

优点：在多个帧的遮挡下工作效果最佳。 此外，在目标大小变化时跟踪效果最佳。

缺点：很多误报使它几乎无法使用。

**MEDIANFLOW跟踪器**

在内部，该跟踪器及时向前和向后跟踪目标，并测量这两个轨迹之间的差异。 最小化此前向后向错误使他们能够可靠地检测跟踪失败并选择视频序列中的可靠轨迹。

在我的测试中，我发现当动作可预测且很小时，这个跟踪器效果最好。 与其他即使在跟踪明显失败时继续运行的跟踪器不同，此跟踪器也知道跟踪失败的时间。

优点：出色的跟踪失败报告。当运动可预测且没有遮挡时，效果很好。

缺点：目标动作大时跟踪失败。

**GOTURN跟踪器**

在跟踪器类的所有跟踪算法中，这是唯一基于卷积神经网络（CNN）的跟踪算法。 从OpenCV文档中，我们知道它“对视点变化，光照变化和变形具有鲁棒性”。 但它不能很好地处理遮挡。

**注意**：GOTURN是基于CNN的跟踪器，使用caffe模型进行跟踪。 Caffe模型和原始文本文件必须存在于代码所在的目录中。 这些文件也可以从opencv_extra存储库下载，在使用前连接并解压缩。

**MOSSE跟踪器**

误差最小平方和滤波器（MOSSE）使用自适应相关进行目标跟踪，当使用单个帧初始化时产生稳定的相关滤波器。 MOSSE跟踪器对照明，比例，姿势和非刚性变形的变化非常稳健。 它还根据峰值与旁瓣比率检测遮挡，这使得跟踪器能够暂停并在对象重新出现时从中断处继续。 MOSSE跟踪器也以更高的fps（450 fps甚至更高）运行。 为了增加积极性，它也很容易实现，与其他复杂的跟踪器一样准确，速度更快。 但是，在性能方面，它落后于基于深度学习的跟踪器。

**订阅&下载代码**

如果你喜欢这篇文章并想下载此文章中使用的代码（C++和Python）和示例图片，请订阅我的栏目。你还将收到免费的计算机视觉资源指南。在我们的栏目里，我们还分享了用C++/Python编写的OpenCV教程和示例，以及计算机视觉和机器学习的算法和新闻。

订阅栏目：https://bigvisionllc.leadpages.net/leadbox/143948b73f72a2%3A173c9390c346dc/5649050225344512/