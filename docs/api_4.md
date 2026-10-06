# 哈葡人IP 地址查询 API 使用文档

## 1. 接口简介

本接口用于查询 IP 地址的地理位置与网络信息，数据来源于 `qqwry.ipdb`（IPIP.net IPDB 格式）。支持 IPv4 与 IPv6，支持 JSON 与纯文本两种返回格式，并可按需只返回单个字段。

**适用场景**：网站访客地域分析、内容本地化、日志分析、风控辅助等。

---

## 2. 接口地址

```
https://api.hapuren.cn/api/ip/
```

---

## 3. 请求方式

支持 `GET` 和 `POST`。

- `GET` 参数通过 URL Query 传递
- `POST` 参数通过表单（`application/x-www-form-urlencoded`）传递

---

## 4. 请求参数

| 参数 | 类型 | 必填 | 默认值 | 说明 |
|------|------|------|--------|------|
| `ip` | string | 否 | 请求方 IP | 要查询的 IP 地址，支持 IPv4 和 IPv6。不传时自动查询发起请求的客户端 IP。 |
| `format` | string | 否 | `json` | 返回格式，可选 `json` 或 `text`。 |
| `field` | string | 否 | 空 | 只返回指定字段，例如 `field=city_name`。不传则返回完整数据。 |

**说明**：
- 若同时通过 GET 和 POST 传递同名参数，**GET 优先**。
- `field` 参数区分大小写？不区分，服务端会转为小写处理，但字段名本身以数据文件为准，建议使用小写。

---

## 5. 返回格式

### 5.1 JSON 格式（默认）

```json
{
  "code": 200,
  "msg": "success",
  "ip": "8.8.8.8",
  "data": {
    "country_name": "美国",
    "region_name": "加利福尼亚州",
    "city_name": "山景城",
    "owner_domain": "google.com",
    "isp_domain": "google.com",
    "latitude": "37.4056",
    "longitude": "-122.0775",
    "timezone": "America/Los_Angeles",
    "utc_offset": "-08:00",
    "china_admin_code": "",
    "idd_code": "1",
    "country_code": "US",
    "continent_code": "NA"
  }
}
```

> `data` 内的字段取决于当前使用的 `qqwry.ipdb` 版本，以上仅为常见字段示例。如需了解全部可用字段，可先不传 `field` 获取完整响应。

### 5.2 指定字段返回

请求：`?ip=8.8.8.8&field=city_name`

```json
{
  "code": 200,
  "msg": "success",
  "ip": "8.8.8.8",
  "city_name": "山景城"
}
```

### 5.3 纯文本格式

请求：`?ip=8.8.8.8&format=text`

```
code: 200
msg: success
ip: 8.8.8.8
country_name: 美国
region_name: 加利福尼亚州
city_name: 山景城
...
```

---

## 6. 请求示例

### 6.1 cURL

```bash
# 查询指定 IP 的完整信息
curl "https://api.hapuren.cn/api/ip/?ip=8.8.8.8"

# 查询本机（请求方）IP
curl "https://api.hapuren.cn/api/ip/"

# 只返回城市名
curl "https://api.hapuren.cn/api/ip/?ip=8.8.8.8&field=city_name"

# 返回纯文本
curl "https://api.hapuren.cn/api/ip/?ip=8.8.8.8&format=text"

# POST 方式
curl -X POST "https://api.hapuren.cn/api/ip/" \
     -d "ip=114.114.114.114" \
     -d "field=city_name"
```

### 6.2 JavaScript（浏览器 Fetch）

```javascript
async function queryIp(ip) {
  const url = 'https://api.hapuren.cn/api/ip/' + (ip ? '?ip=' + encodeURIComponent(ip) : '');
  const res = await fetch(url);
  const data = await res.json();

  if (data.code === 200) {
    console.log(data.data); // 完整信息
  } else {
    console.error(data.msg);
  }
}

// 查询指定 IP
queryIp('8.8.8.8');

// 查询本机 IP（不传参数）
queryIp();
```

### 6.3 PHP

```php
<?php
function queryIp($ip = '', $field = '') {
    $params = [];
    if ($ip)    $params['ip'] = $ip;
    if ($field) $params['field'] = $field;

    $url = 'https://api.hapuren.cn/api/ip/?' . http_build_query($params);

    $ch = curl_init($url);
    curl_setopt_array($ch, [
        CURLOPT_RETURNTRANSFER => true,
        CURLOPT_TIMEOUT        => 5,
    ]);
    $resp = curl_exec($ch);
    curl_close($ch);

    return json_decode($resp, true);
}

// 查询完整信息
$result = queryIp('8.8.8.8');
print_r($result);

// 只查询城市
$city = queryIp('8.8.8.8', 'city_name');
echo $city['city_name'] ?? '未知';
```

### 6.4 Python

```python
import requests

def query_ip(ip=None, field=None):
    params = {}
    if ip:
        params['ip'] = ip
    if field:
        params['field'] = field

    r = requests.get('https://api.hapuren.cn/api/ip/', params=params, timeout=5)
    return r.json()

# 查询完整信息
print(query_ip('8.8.8.8'))

# 只查询城市
print(query_ip('8.8.8.8', 'city_name'))
```

### 6.5 Node.js（node-fetch）

```javascript
const fetch = require('node-fetch');

async function queryIp(ip) {
  const url = `https://api.hapuren.cn/api/ip/?ip=${encodeURIComponent(ip)}`;
  const res = await fetch(url);
  return res.json();
}

queryIp('8.8.8.8').then(console.log);
```

---

## 7. 错误码

| code | HTTP 状态 | 说明 | 处理建议 |
|------|-----------|------|----------|
| 200 | 200 | 查询成功 | 正常解析 `data` 或指定字段 |
| 400 | 400 | IP 地址格式无效，或 `field` 参数不存在 | 检查 IP 是否合法；若不传 `field` 可获取所有可用字段 |
| 404 | 404 | IP 未在数据库中找到记录 | 可视为未知地域，按业务逻辑兜底 |
| 500 | 500 | 服务器内部错误（如数据文件不可用） | 稍后重试，或联系服务提供方 |

**错误响应示例**：

```json
{
  "code": 400,
  "msg": "Invalid IP address",
  "ip": "abc.def"
}
```

```json
{
  "code": 404,
  "msg": "IP not found",
  "ip": "0.0.0.0"
}
```

```json
{
  "code": 400,
  "msg": "Invalid field",
  "ip": "8.8.8.8"
}
```

```json
{
  "code": 500,
  "msg": "Internal server error"
}
```

---

## 8. 使用注意事项

1. **查询本机 IP**  
   不传 `ip` 参数时，接口会按 `X-Forwarded-For` → `X-Real-IP` → `Client-IP` → `REMOTE_ADDR` 的顺序获取客户端 IP。若您的客户端经过多层代理，建议自行传递真实 IP。

2. **字段可用性**  
   `field` 参数只能传 `data` 中实际存在的字段。不同版本的数据文件字段可能略有差异。建议先不传 `field` 获取完整数据，再确定需要使用的字段名。

3. **跨域支持**  
   接口已开启 CORS（`Access-Control-Allow-Origin: *`），可直接在浏览器前端调用。

4. **请求频率**  
   请合理控制调用频率，避免高频请求对服务造成压力。如有大规模查询需求，建议在服务端做本地缓存或批量处理。

5. **数据时效性**  
   服务端会定期从 CDN 更新 IP 数据库（默认缓存 24 小时），因此查询结果可能存在一定延迟，请知悉。

6. **安全提示**  
   本接口仅用于 IP 地理位置参考，不保证 100% 准确，请勿用于安全鉴权、精确风控等对准确性要求极高的场景。

---

## 9. 常见问题

**Q：为什么返回的 `data` 字段和文档示例不完全一样？**  
A：字段列表由数据文件定义，不同版本可能新增或调整字段。以实际返回为准，也可通过不传 `field` 获取所有字段。

**Q：可以批量查询吗？**  
A：当前接口一次只支持查询一个 IP。批量查询请循环调用，并注意频率控制。

**Q：返回的经纬度可以直接用于地图定位吗？**  
A：经纬度为城市级粗略坐标，可用于大致定位，不建议用于精确导航。

**Q：为什么查询某些 IP 返回 404？**  
A：该 IP 可能属于保留地址、内网地址或数据库未收录的范围。

---

如有其他问题，请联系服务提供方获取支持。
