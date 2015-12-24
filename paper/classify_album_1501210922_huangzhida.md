## 各类图片按时刻、旅行、故事归类的研究与实现(1501210922 黄志达)

### 姓名：黄志达
### 学号：1501210922

#### 研究背景：由于系统自带的相册除了按时间归集之外，就没有其它类型的归集；但用户也想看到其他类型的归集，如旅行相册或故事相册等，或者是使用机器学习来识别图片是属于什么类型的相片。

###  1.sqlite数据库设计
| 名称 | 类型 |
| -- | -- |
| album_id | string  (pimary key)|
| album_name | string |
| album_status | int |
| album_type | int |
| album_address | string |
| album_time_str | string |
| start_time | string |
| end_time | string |

###  2.获取安装系统的所有图片

###  3.Android调用C++库

###  4.底层C++归集算法

