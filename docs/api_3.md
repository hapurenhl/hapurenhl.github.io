# 哈葡人一言（Hitokoto）API 使用文档

## 1. 接口简介

一言 API 提供随机句子获取服务，涵盖动画、漫画、游戏、文学、原创、网络、影视、诗词、网易云、哲学、抖机灵等多种分类。支持纯文本、JSON、JS 三种返回方式，可直接在前端页面一行代码接入。

**适用场景**：网站/博客签名、页面点缀、小程序内容、随机文案展示等。

---

## 2. 接口地址

```
https://api.hapuren.cn/api/yy/
```

---

## 3. 请求方式

支持 `GET` 和 `POST`。

- `GET` 参数通过 URL Query 传递
- `POST` 支持表单（`application/x-www-form-urlencoded`）与数组形式参数
- 其他请求方法返回 `405`

---

## 4. 请求参数

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `c` | string / array | 否 | 全部分类 | 句子分类，可多选。不传则从所有分类中随机。 |
| `encode` | string | 否 | `json` | 返回编码：`text` / `json` / `js`。其他值按 `json` 处理。 |
| `charset` | string | 否 | `utf-8` | 字符集：`utf-8` / `gbk`。其他值按 `utf-8` 处理。`gbk` 不支持与 `js` 同用。 |
| `select` | string | 否 | `.hitokoto` | 当 `encode=js` 时的 DOM 选择器。为空或超过 256 字符时回退为默认值。 |

### 4.1 分类（`c`）取值

| 值 | 分类 | 值 | 分类 |
|----|------|----|------|
| `a` | 动画 | `g` | 其他 |
| `b` | 漫画 | `h` | 影视 |
| `c` | 游戏 | `i` | 诗词 |
| `d` | 文学 | `j` | 网易云 |
| `e` | 原创 | `k` | 哲学 |
| `f` | 来自网络 | `l` | 抖机灵 |

### 4.2 多选写法

支持以下三种写法，效果一致：

```
?c=a&c=c              # GET 重复参数
?c[]=a&c[]=c          # GET 数组形式
POST: c=a&c=c         # POST 表单重复参数
POST: c[]=a&c[]=c     # POST 数组形式
```

### 4.3 分类处理规则

- **不传 `c`**：从全部 12 个分类中随机
- **传入合法分类**：在指定分类中随机（可多选）
- **全部为非法值**：按 `a`（动画）处理
- **部分合法**：只保留合法值参与随机

---

## 5. 返回格式

### 5.1 JSON（默认）

请求：`?encode=json`（或省略 `encode`）

```json
{
  "id": 1234,
  "hitokoto": "人生若只如初见，何事秋风悲画扇。",
  "type": "i",
  "from": "木兰花·拟古决绝词柬友",
  "from_who": "纳兰性德",
  "creator": "",
  "creator_uid": 0,
  "reviewer": 0,
  "uuid": "d8a1f5c2-3b4e-4c9a-9e2f-1234567890ab",
  "commit_from": "web",
  "created_at": "1500000000",
  "length": 16
}
```

| 字段 | 类型 | 说明 |
|------|------|------|
| `id` | int | 句子 ID |
| `hitokoto` | string | 句子正文 |
| `type` | string | 所属分类代码 |
| `from` | string | 出处（作品名 / 书名 / 歌名等） |
| `from_who` | string \| null | 作者 / 歌手，可能为 `null` |
| `creator` | string | 提交者 |
| `creator_uid` | int | 提交者 UID |
| `reviewer` | int | 审核者 ID |
| `uuid` | string | 句子唯一标识 |
| `commit_from` | string | 提交来源 |
| `created_at` | string | 提交时间（Unix 时间戳，秒） |
| `length` | int | 句子长度 |

### 5.2 纯文本

请求：`?encode=text`

```
人生若只如初见，何事秋风悲画扇。
```

响应头：`Content-Type: text/plain; charset=utf-8`

### 5.3 JavaScript（同步内联调用）

请求：`?encode=js&select=.hitokoto`

```javascript
(function hitokoto(){var hitokoto="人生若只如初见，何事秋风悲画扇。";var dom=document.querySelector(".hitokoto");Array.isArray(dom)?dom[0].innerText=hitokoto:dom.innerText=hitokoto;})()
```

响应头：`Content-Type: application/javascript; charset=utf-8`

> 该脚本会同步执行，将句子写入选择器匹配的第一个元素的 `innerText`。

---

## 6. 使用示例

### 6.1 浏览器中直接访问

```
https://api.hapuren.cn/api/yy/
https://api.hapuren.cn/api/yy/?c=a
https://api.hapuren.cn/api/yy/?c=a&c=c
https://api.hapuren.cn/api/yy/?encode=text
```

### 6.2 HTML 一行接入（JS 编码）

最简写法，无需任何 JavaScript 代码：

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>一言示例</title>
</head>
<body>
  <p class="hitokoto"></p>

  <!-- 默认选择器 .hitokoto -->
  <script src="https://api.hapuren.cn/api/yy/?encode=js"></script>
</body>
</html>
```

自定义选择器：

```html
<p id="sentence"></p>
<script src="https://api.hapuren.cn/api/yy/?encode=js&select=%23sentence"></script>
```

> 选择器中的特殊字符需 URL 编码，例如 `#sentence` → `%23sentence`。

指定分类：

```html
<script src="https://api.hapuren.cn/api/yy/?encode=js&c=i&select=.hitokoto"></script>
```

### 6.3 JavaScript（Fetch，JSON）

```javascript
async function getHitokoto(categories = []) {
  const params = new URLSearchParams();
  categories.forEach(c => params.append('c', c));
  params.append('encode', 'json');

  const res = await fetch('https://api.hapuren.cn/api/yy/?' + params.toString());
  const data = await res.json();

  console.log(data.hitokoto);
  console.log('——', data.from_who, '《' + data.from + '》');
  return data;
}

// 随机全部分类
getHitokoto();

// 指定分类（动画 + 游戏）
getHitokoto(['a', 'c']);
```

### 6.4 cURL

```bash
# 随机一言（JSON）
curl "https://api.hapuren.cn/api/yy/"

# 纯文本
curl "https://api.hapuren.cn/api/yy/?encode=text"

# 指定分类（诗词 + 文学）
curl "https://api.hapuren.cn/api/yy/?c=i&c=d"

# POST 方式
curl -X POST "https://api.hapuren.cn/api/yy/" \
     -d "c=a" \
     -d "c=c" \
     -d "encode=json"
```

### 6.5 PHP

```php
<?php
function getHitokoto(array $categories = []) {
    $params = [];
    foreach ($categories as $c) {
        $params[] = 'c=' . urlencode($c);
    }
    $params[] = 'encode=json';

    $url = 'https://api.hapuren.cn/api/yy/?' . implode('&', $params);

    $ch = curl_init($url);
    curl_setopt_array($ch, [
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_TIMEOUT        => 5,
    ]);
    $resp = curl_exec($ch);
    curl_close($ch);

    return json_decode($resp, true);
}

// 随机一言
$one = getHitokoto();
echo $one['hitokoto'] . "\n";
echo '—— ' . ($one['from_who'] ?? '佚名') . '《' . $one['from'] . '》';

// 指定分类（诗词）
$poem = getHitokoto(['i']);
print_r($poem);
```

### 6.6 Python

```python
import requests

def get_hitokoto(categories=None):
    params = []
    if categories:
        for c in categories:
            params.append(('c', c))
    params.append(('encode', 'json'))

    r = requests.get('https://api.hapuren.cn/api/yy/', params=params, timeout=5)
    return r.json()

# 随机
print(get_hitokoto()['hitokoto'])

# 指定分类
print(get_hitokoto(['a', 'c']))
```

### 6.7 Node.js

```javascript
const fetch = require('node-fetch');

async function getHitokoto(categories = []) {
  const params = new URLSearchParams();
  categories.forEach(c => params.append('c', c));
  params.append('encode', 'json');

  const res = await fetch('https://api.hapuren.cn/api/yy/?' + params.toString());
  return res.json();
}

getHitokoto(['i']).then(data => {
  console.log(data.hitokoto);
});
```

### 6.8 GBK 编码（适用于 GBK 站点）

```
https://api.hapuren.cn/api/yy/?encode=text&charset=gbk
```

```html
<!-- GBK 页面中内联 -->
<script src="https://api.hapuren.cn/api/yy/?encode=json&charset=gbk"></script>
```

> 注意：`charset=gbk` 与 `encode=js` 不兼容，若同时指定，服务端会自动回退为 `utf-8`。

---

## 7. 响应头说明

| 响应头 | 说明 |
|--------|------|
| `Content-Type` | 根据 `encode` 与 `charset` 动态设置 |
| `Access-Control-Allow-Origin` | `*`，允许跨域调用 |
| `Cache-Control` | `no-cache`，避免中间缓存导致每次返回相同内容 |

---

## 8. 错误码

| HTTP 状态 | 返回内容 | 说明 |
|-----------|----------|------|
| 200 | 正常返回 | 请求成功 |
| 405 | `{"code":405,"message":"method not allowed"}` | 使用了非 GET / POST 的请求方法 |
| 413 | `{"code":413,"message":"request entity too large"}` | POST 表单体积超过 10KB |
| 500 | `{"code":500,"message":"category data not found"}` | 分类数据获取失败且无可用缓存 |

错误响应统一为 JSON 格式：

```json
{
  "code": 405,
  "message": "method not allowed"
}
```

---

## 9. 注意事项

1. **分类参数解析**  
   多选时请使用 `c=a&c=c` 或 `c[]=a&c[]=c` 形式。若使用 `c=a,c` 这种逗号分隔写法，服务端不会拆分，会被视为非法值。

2. **非法分类回退**  
   - 部分非法：只保留合法分类参与随机
   - 全部非法：按 `a`（动画）处理
   - 不传 `c`：全部分类随机

3. **编码与字符集**  
   - `encode` 取值：`text` / `json` / `js`，其他值按 `json` 处理
   - `charset` 取值：`utf-8` / `gbk`，其他值按 `utf-8` 处理
   - `js` 与 `gbk` 不兼容，同时指定时自动回退为 `utf-8`

4. **JS 内联接入的 XSS 防护**  
   `encode=js` 输出时已对 `<` `>` `&` `'` `"` 做 HTML 实体转义，可安全内联到 HTML 中。

5. **数据来源与缓存**  
   - 数据通过 HTTP 从 CDN 拉取，本地缓存有效期为 24 小时
   - CDN 拉取失败时会回退使用过期缓存
   - 若数据源地址需要调整，可通过环境变量 `YY_DATA_URL` 覆盖

6. **请求频率**  
   请合理控制调用频率，避免高频请求对服务端造成压力。建议在页面级别做节流（如每次刷新页面才请求一次）。

7. **字符集选择**  
   现代站点建议统一使用 `utf-8`，仅在 GBK 老站点中才需显式指定 `charset=gbk`。

---

## 10. 常见问题

**Q：可以直接在 `<script>` 标签中引入吗？**  
A：可以。`encode=js` 就是为内联引入设计的，会同步把句子写入指定选择器的元素中。

**Q：`select` 参数中的 `#` 和 `.` 需要编码吗？**  
A：在 URL 中 `#` 是锚点符号，必须编码为 `%23`；`.` 可以保留。

**Q：为什么每次请求返回的句子都不一样？**  
A：接口会从指定分类中随机选取一条返回，属于预期行为。响应头已设置 `Cache-Control: no-cache`，避免被中间层缓存。

**Q：如何只获取诗词分类？**  
A：请求 `?c=i` 即可，也可多选如 `?c=i&c=d`。

**Q：返回的 `from_who` 有时是 `null`？**  
A：部分句子的作者信息缺失，字段可能为 `null`，请在代码中做好空值处理。

**Q：可以批量获取多条吗？**  
A：当前接口一次只返回一条，批量请循环调用，并注意频率控制。

---

如有其他问题，请联系服务提供方获取支持。