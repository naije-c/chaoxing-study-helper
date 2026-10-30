# 超星学习助手

## 安装

脚本文件：

```text
chaoxing-study-helper.user.js
```

使用 Tampermonkey 打开以下地址安装：

[安装超星学习助手](https://raw.githubusercontent.com/naije-c/chaoxing-study-helper/main/chaoxing-study-helper.user.js)

## 自动更新

脚本已经从 GitHub Raw 地址检查更新：

```text
https://raw.githubusercontent.com/naije-c/chaoxing-study-helper/main/chaoxing-study-helper.user.js
```

以后更新时只需要：

1. 修改 `chaoxing-study-helper.user.js`。
2. 提升脚本头部的 `@version`。
3. 提交并推送到 `main` 分支。
4. Tampermonkey 会定期检查并自动更新。

## 功能

### 视频功能

- 自动播放视频、音频
- 暂停后自动恢复
- 静音播放
- 1.0～2.0 倍速
- 视频结束后自动打开章节测验
- 没有章节测验时自动进入下一节
- 章节测验完成后自动进入下一节
- 可拖动、可收起的悬浮面板
- 设置在章节之间自动保存

### AI 章节测验

- 自动识别超星章节测验
- 识别 `font-cxsecret` 加密字体并还原题干
- 支持 OpenAI 兼容的 Chat Completions API
- 支持测试 API 连接
- 支持读取 OpenAI 兼容的 /models 模型列表
- 支持单选、多选、判断题
- 支持基础 textarea、text 输入填空题
- AI 批量作答
- 根据题目 ID、题型和选项校验答案
- 填写后检查页面隐藏答案字段
- 支持填写后自动确认并提交

## AI 接口格式

目前只支持 OpenAI 兼容接口：

```text
POST https://api.example.com/v1/chat/completions
Authorization: Bearer <API Key>
Content-Type: application/json
```

请求体使用标准的：

```json
{
  "model": "模型名称",
  "messages": [
    {"role": "system", "content": "..."},
    {"role": "user", "content": "..."}
  ]
}
```

脚本不依赖特定的 AI 厂商，只要接口兼容 Chat Completions 和 `choices[0].message.content` 即可。

## 配置方法

1. 使用已安装的 Tampermonkey 打开仓库中的安装链接。
2. 在安装页面点击“安装”或“重新安装”。
3. 如果已经安装旧版本，安装 GitHub 版本后刷新超星课程页面。
4. 进入超星课程页面，打开右侧悬浮面板。
5. 展开“AI 答题设置”。
6. 填写：
   - API 完整地址
   - API Key
7. 点击“测试连接并获取模型”。
8. 脚本会从 `chat/completions` 自动推导 `/models` 地址，并把返回的模型填入下拉列表。
9. 从列表中选择模型，或手动填写模型名称。
10. 设置最低置信度并点击“保存 AI 配置”。
11. 进入章节测验。
12. 点击“AI 作答本章”。

如果勾选“打开测验后自动作答”并保存，进入章节测验后会自动发起请求。

## API 地址示例

不同服务的完整地址不同，必须填写到 `chat/completions`：

```text
https://api.openai.com/v1/chat/completions
https://api.deepseek.com/chat/completions
https://openrouter.ai/api/v1/chat/completions
```

以服务商文档为准，不要把只有域名或 `/v1` 的地址填进来。

## 字体解码说明

超星部分题干使用 `font-cxsecret` 自定义字体：

- DOM 中保存的是乱码
- 浏览器用自定义字体把它显示成正常中文
- AI 直接读取 DOM 会收到乱码

完整版包含基于字形特征比对的解码器：

1. 读取页面内嵌的 `font-cxsecret` 字体。
2. 下载 OCS 字形特征表。
3. 将乱码字符渲染为 Canvas 字形。
4. 与标准汉字特征进行粗略和精细比对。
5. 建立乱码字符到正常汉字的映射。
6. 替换 DOM 中的题干文本。

字体解码算法参考 OCS 项目，遵循 MIT License。特征表地址：

```text
https://cdn.ocsjs.com/resources/font/font_ref_cx.bin
```

## 安全说明

- API Key 保存在 Tampermonkey 的 GM 存储中。
- API Key 不会通过 `postMessage` 广播给网页 iframe。
- 题目、选项和章节标题会发送到你配置的 AI 服务。
- 不要把 API Key 发到陌生接口。
- 使用 `@connect *` 是为了支持自定义 OpenAI 兼容接口，因此脚本具备访问所配置接口的能力；请自行确认目标地址可信。
- 脚本不会读取 Cookie，也不会绕过验证码或人脸识别。章节测验是否自动提交由 AI 设置控制。

## 使用限制

- 只支持超星学习通及内嵌超星页面。
- AI 返回的答案不保证正确。
- 模型置信度是模型自报值，不等于真实正确率。
- 填空、图片、公式、排序题和复杂富文本编辑器还需要继续适配。
- 如果超星改版，题目选择器或字体加密方式可能变化。
- 如果字体特征表无法下载，AI 答题会停止并提示错误。
- 遇到验证码、人脸识别或视频弹题时，脚本不会代做。

## 建议

- 先使用 1～2 道题的测试课程验证效果。
- 默认保持“最低置信度”在 0.5 以上。
- 自动提交默认开启；如不希望自动提交，可在 AI 设置中取消勾选。
- 高倍速和 AI 自动答题均存在平台使用规则与学术诚信风险，请自行判断是否允许使用。

## 测试连接说明

点击“测试连接并获取模型”时，脚本会：

1. 读取当前填写的 API 地址。
2. 把结尾的 `/chat/completions` 替换为 `/models`。
3. 使用当前 API Key 发送 `Authorization: Bearer ...`。
4. 请求模型列表。
5. 解析常见返回格式：
   - `{"data":[{"id":"模型名"}]}`
   - `{"models":[{"id":"模型名"}]}`
   - 字符串数组
6. 将模型按名称排序并填入下拉列表。

如果模型列表接口不可用，但聊天接口仍可使用，可以手动填写模型名称。测试连接只请求模型列表，不发送测验题目，也不产生聊天生成费用。
## 章节测验完成后自动下一节

完整版会同时检测：

- 视频播放结束
- 章节测验显示“已完成”
- 章节测验出现“本次成绩”
- 当前任务点完成标记

当“开始自动播放”处于运行状态，并且“自动下一节”已勾选时：

1. 章节测验 iframe 检测到完成状态。
2. 向顶层学习页面发送完成通知。
3. 顶层页面等待“结束后等待”中设置的秒数。
4. 点击 `#prevNextFocusNext` 进入下一节。

这样避免了在测验 iframe 内直接寻找外层按钮失败的问题。
## 视频完成后的跳转顺序

当“开始自动播放”处于运行状态，并且“自动下一节”已勾选时，完整版按以下顺序执行：

1. 检测视频 `ended`。
2. 如果视频是在脚本启动前已经播放完毕，则检测 `currentTime / duration >= 99.5%`。
3. 通知顶层学习页面“视频已完成”。
4. 等待“结束后等待”中设置的秒数。
5. 如果顶层页面存在 `#dct2`“章节测验”标签，点击 `#dct2`。
6. 如果没有章节测验标签，则点击 `#prevNextFocusNext` 进入下一节。
7. 章节测验完成后，再由顶层页面点击“下一节”。

这样不会在视频完成后直接跳过章节测验。
## 防止重复 AI 请求

完整版会为每套题目生成签名，签名由题目 ID 和解码后的题干组成。

自动作答成功或尝试后，会把签名记录到 Tampermonkey 存储中：

- 同一套题在 30 分钟内不会再次自动请求 AI。
- 页面重新渲染、刷新或重复扫描不会重复请求。
- 手动点击“AI 作答本章”仍然可以强制重新请求。

## 自动提交

AI 设置中新增：

```text
AI填写完成后自动提交
```

默认开启。只有同时满足以下条件才会提交：

- 页面中的全部题目都已填写。
- 没有题目被跳过。
- 所有答案都通过了隐藏答案字段验证。
- 自动提交选项已勾选。

如果不是全部题目都成功，脚本会停止提交并在面板说明原因。

提交时脚本会点击：

```text
a.btnSubmit / button.btnSubmit / btnBlueSubmit
```

然后检查常见弹窗确认按钮，例如：

```text
.layui-layer-btn0
.popup_con .btn
.el-message-box__btns
[role="dialog"]
```

## 字体解码重复问题修复

部分判断题的 `font-cxsecret` 类位于选项父级 `li` 上，而不是 `.after` 子元素。

旧逻辑只删除子元素的类，父元素仍然带有 `font-cxsecret`，导致每次扫描都重新识别题干。

新逻辑会同时删除：

- 当前解码目标的 `font-cxsecret`
- 最近的父级 `font-cxsecret`

因此识别成功后不会反复进入解码状态。
## 提交确认弹窗处理

超星当前版本的提交流程是：

1. 章节测验 iframe 内点击“提交”。
2. 顶层学习页面显示“确认提交？”弹窗。
3. 顶层弹窗中的提交按钮 ID 为 `#popok`。
4. 点击 `#popok` 后才真正提交。

脚本现在会：

- 在测验 iframe 内点击 `.btnSubmit`。
- 检测同一 iframe 内的确认按钮。
- 如果没有找到，则向顶层页面发送 `confirm-submit` 消息。
- 顶层页面点击 `#popok`。
- 兼容 `#submitBackOk`、`.layui-layer-btn0`、对话框内的“提交/确定/确认”按钮。