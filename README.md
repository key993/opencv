<img width="1410" height="1205" alt="image" src="https://github.com/user-attachments/assets/06097dc4-2c7f-49ac-8bb8-ab8955f6dc44" />

src="https://github.com/user-attachments/assets/0f5e0208-b2d6-4dd2-aa69-53a9da2c9da0" />## 效果展示
- 原始图像与识别结果：![Uploading image.png…]()


  ![装甲板灯条识别结果](<img width="1410" height="1205" alt="image" src="https://github.com/user-attachments/assets/256bfe80-5edf-407e-af19-d012a857eb78" />
)
- 预处理阶段效果：
  灰度图像 | 二值化图像
  :---: | :---:
  ![灰度图像](<img width="1410" height="1205" alt="image" src="https://github.com/user-attachments/assets/9ce0b33c-13a7-4555-9b84-41d51f5ed383" />
) | ![二值化图像](<img width="1410" height="1205" alt="image" src="https://github.com/user-attachments/assets/6418b991-86f8-42f2-a113-3c09660d4a2a" />
)

## 环境依赖
- 开发语言：C++
- 依赖库：OpenCV 4.x及以上版本
- 编译环境：Visual Studio 2022 / 其他支持C++的编译器

## 代码结构说明
```cpp
#include <opencv2/opencv.hpp>
#include <opencv2/highgui.hpp>
#include <opencv2/imgproc.hpp>
#include <iostream>

using namespace cv;
using namespace std;

int main()
{
    // 1. 图像读取与合法性校验
    string img_path = "Resources/armor.jpg";
    Mat img = imread(img_path);
    if (img.empty())
    {
        cout << "Error: Image load failed!" << endl;
        return -1;
    }

    // 2. 图像预处理
    Mat img_gray;
    cvtColor(img, img_gray, COLOR_BGR2GRAY);

    Mat img_binary;
    threshold(img_gray, img_binary, 200, 255, THRESH_BINARY);

    Mat kernel = getStructuringElement(MORPH_RECT, Size(3, 3));
    morphologyEx(img_binary, img_binary, MORPH_CLOSE, kernel);

    // 3. 轮廓检测与灯条筛选
    vector<vector<Point>> contours;
    vector<Vec4i> hierarchy;
    findContours(img_binary, contours, hierarchy, RETR_EXTERNAL, CHAIN_APPROX_SIMPLE);

    Mat img_result = img.clone();
    for (size_t i = 0; i < contours.size(); i++)
    {
        Rect rect = boundingRect(contours[i]);
        double aspect_ratio = (double)rect.height / rect.width;

        if (aspect_ratio > 3 && rect.area() > 50)
        {
            rectangle(img_result, rect, Scalar(0, 255, 0), 2);
        }
    }

    // 4. 结果可视化
    imshow("Original Image", img);
    imshow("Grayscale Image", img_gray);
    imshow("Binary Image", img_binary);
    imshow("Light Bar Detection", img_result);

    waitKey(0);
    destroyAllWindows();
    return 0;
}
