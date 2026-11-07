# 超星学习助手

面向超星学习通的 Tampermonkey 用户脚本，提供视频播放、复制粘贴限制解除、章节测验辅助和自动任务跳转等基础功能。

![Version](https://img.shields.io/badge/version-2.6.1-blue)
![Tampermonkey](https://img.shields.io/badge/Tampermonkey-compatible-2ea44f)
![License](https://img.shields.io/badge/license-MIT-green)

> 本项目仅用于个人学习辅助。请遵守学校规定、课程要求和超星平台使用条款。

## 功能

### 视频学习

- 自动播放视频和音频
- 暂停后自动恢复
- 静音与 1.0～2.0 倍速
- 视频完成后打开章节测验
- 章节测验完成后进入下一节
- 识别同一页面内的多个视频任务点，自动跳过已完成项
- 只向第一个未完成任务点授予播放权，完成后自动选择下一个
- 同页面及跨 iframe 播放互斥，避免多个视频来回切换
- 可拖动、可收起的悬浮面板

### 复制粘贴辅助

- 恢复页面文字选择
- 解除页面 `oncopy`、`onpaste`、`onkeydown` 限制
- 强制恢复 `user-select: text`
- 兼容超星 UEditor 富文本编辑器
- 移除 UEditor 的 `beforepaste` 拦截
- 支持可选的 UEditor 弹窗编辑回退

### AI 章节测验

- 识别超星加密题干字体 `font-cxsecret`
- 支持 OpenAI 兼容的 Chat Completions API
- 支持测试连接和读取 `/models` 模型列表
- 支持单选、多选、判断和基础填空题
- 校验题目、选项和隐藏答案字段
- 防止重复请求同一套题目
- 可选自动确认并提交

## 安装

1. 安装 [Tampermonkey](https://www.tampermonkey.net/) 或 ScriptCat。
2. 点击下面的安装链接。
3. 在脚本管理器中确认安装。

[安装超星学习助手](https://raw.githubusercontent.com/naije-c/chaoxing-study-helper/main/chaoxing-study-helper.user.js)

脚本地址：

```text
https://raw.githubusercontent.com/naije-c/chaoxing-study-helper/main/chaoxing-study-helper.user.js
```

## 快速开始

### 视频学习

1. 进入超星课程的视频任务页。
2. 在右侧面板点击“开始自动播放”。
3. 根据需要设置静音、倍速和自动下一节。

### 复制粘贴

脚本会自动启用复制粘贴限制解除，无需手动操作。

如果 UEditor 仍然无法正常粘贴：

1. 勾选面板中的“UEditor 弹窗编辑”。
2. 点击答题框。
3. 在弹出编辑框中粘贴并编辑内容。
4. 点击“写回编辑器”。

### 配置 AI

1. 展开“AI 答题设置”。
2. 填写完整的 Chat Completions 接口地址，例如：

```text
https://api.deepseek.com/chat/completions
```

3. 填写 API Key。
4. 点击“测试连接并获取模型”。
5. 选择模型，或手动填写模型名称。
6. 设置最低置信度。
7. 点击“保存 AI 配置”。

如果勾选“打开测验后自动作答”，进入章节测验后会自动发起请求。

## AI 接口格式

支持 OpenAI 兼容的 Chat Completions API：

```http
POST https://api.example.com/v1/chat/completions
Authorization: Bearer <API Key>
Content-Type: application/json
```

脚本会读取：

```text
choices[0].message.content
```

并将模型返回的 JSON 答案与页面题目、选项和题型进行校验。

## 工作流程

```text
视频完成
→ 打开章节测验
→ 识别并解码题干
→ 请求 AI
→ 校验答案并填写
→ 可选自动提交
→ 测验完成
→ 进入下一节
```

多视频页面中，顶层页面会协调播放权，只允许一个可见视频保持播放。

## 支持的域名

脚本支持超星主域名、常见学习通子域名和原版 OCS 中的超星兼容域名：

```text
*.chaoxing.com
mooc1.chaoxing.com
mooc1-1.chaoxing.com
mooc1-2.chaoxing.com
mooc-ans.chaoxing.com
mooc2-ans.chaoxing.com
i.chaoxing.com
*.edu.cn
*.org.cn
*.xueyinonline.com
*.hnsyu.net
*.qutjxjy.cn
*.ynny.cn
*.hnvist.cn
*.fjlecb.cn
*.gdhkmooc.com
*.cugbonline.cn
*.zjelib.cn
*.cqrspx.cn
*.neauce.com
*.zhihui-yun.com
*.cqie.cn
*.ccqmxx.com
*.jxgmxy.com
*.jnzyjsxy.cn
*.smjyxy.cn
*.sslibrary.com
*.xuexi365.com
```

对于 `*.edu.cn`、`*.org.cn` 等宽泛域名，脚本会额外检测超星页面特征，避免在无关网站运行。

## 安全与隐私

- API Key 保存在 Tampermonkey 的 GM 存储中。
- API Key 不会通过 `postMessage` 广播给网页 iframe。
- 题目、选项和章节标题会发送到用户配置的 AI 服务。
- 请使用可信的 API 地址，不要向陌生接口发送 Key。
- 脚本不会读取 Cookie，也不会绕过验证码或人脸识别。
- 自动提交可能导致错误答案被提交，请谨慎开启。

## 使用限制

- 仅支持超星学习通及内嵌超星页面。
- AI 返回的答案不保证正确。
- 模型自报的置信度不等于真实正确率。
- 图片题、公式题、排序题和复杂富文本仍需继续适配。
- 平台改版可能导致题目选择器、编辑器或域名规则失效。
- 字体解码依赖 `cdn.ocsjs.com` 的特征表。

## 开发与更新

仓库文件：

```text
chaoxing-study-helper.user.js   # 用户脚本
README.md                       # 项目说明
LICENSE                         # MIT 许可证
.gitignore                      # 本地忽略规则
```

更新版本时：

1. 修改 `chaoxing-study-helper.user.js`。
2. 提升脚本头部的 `@version`。
3. 提交并推送到 `main` 分支。
4. Tampermonkey 定期检查 `@updateURL` 并自动更新。

```powershell
git add chaoxing-study-helper.user.js README.md
git commit -m "Update script"
git push
```

## 致谢

- 加密字体解码算法参考 [OCS](https://github.com/ocsjs/ocsjs)，遵循 MIT License。
- 感谢所有提交问题和改进建议的使用者。

## License

[MIT](LICENSE)