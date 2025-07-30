# 哈葡人互联一言API接口文档

## 连接

API连接:`https://api.hapuren.cn/ht/`

## 请求参数

| 参数 | 值 | 可选 | 说明 | 默认值 |
| :----: | :----: | :----: | :----: | :----: |
| wj | 见后表 | 是 | 句子类型 | a |
| type | 见后表 | 是 | 返回格式 | json |

### 句子类型（参数wj）

| 参数 | 说明 |
| :----: | :----: |
| a | 动画 |
| b | 漫画 |
| c | 游戏 |
| d | 文学 |
| e | 原创 |
| f | 来自网络 |
| g | 其他 |
| h | 影视 |
| i | 诗词 |
| j | 网易云 |
| k | 哲学 |
| l | 抖机灵 |

### 返回格式（参数type）

| 参数 | 说明 |
| :----: | :----: |
| json | json格式返回 |
| txt | txt文本返回（只返回一句话） |

## 返回信息

| 返回参数名称 | 描述 |
| :----: | :----: |
| id           | 一言标识                                                 |
| hitokoto     | 一言正文。编码方式 unicode。使用 utf-8。                      |
| type         | 类型。请参考第三节参数的表格                                    |
| from         | 一言的出处                                                |
| from_who     | 一言的作者                                                |
| creator      | 添加者                                                   |
| creator_uid  | 添加者用户标识                                             |
| reviewer     | 审核员标识                                                |
| uuid         | 一言唯一标识；可以链接到https://hitokoto.cn?uid=[uuid]查看这个一言的完整信息 |
| commit_from  | 提交方式                                                 |
| created_at   | 添加时间                                                 |
| length       | 句子长度                                                 |

## 教程

### 链接例句

`https://api.hapuren.cn/ht/`

`https://api.hapuren.cn/ht/?wj=a&type=json`

### 演示（可刷新查看）

![](https://api.hapuren.cn/ht/)

## 致谢

感谢`hotokoto.一言`提供的语言包

hotokoto.一言 连接:[点我](https://hitokoto.cn/)

本接口遵循寻 Apache-2.0 license 协议

协议原文:[https://github.com/hitokoto-osc/hitokoto-api/blob/master/LICENSE](https://github.com/hitokoto-osc/hitokoto-api/blob/master/LICENSE)