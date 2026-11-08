# 超星学习助手

面向超星学习通的 Tampermonkey 用户脚本，提供视频播放、复制粘贴限制解除、章节测验辅助和自动任务跳转等基础功能。

![Version](https://img.shields.io/badge/version-2.6.13-blue)
![Tampermonkey](https://img.shields.io/badge/Tampermonkey-compatible-2ea44f)
![License](https://img.shields.io/badge/license-MIT-green)

> 本项目仅用于个人学习辅助。请遵守学校规定、课程要求和超星平台使用条款。

## 功能

### 视频学习

- 自动播放视频和音频
- 暂停后自动恢复；切到后台标签页也会继续
- 静音与 1.0～2.0 倍速
- 视频完成后打开章节测验
- 章节测验完成后进入下一节
- 本页全部任务点完成后自动进入下一节（单视频/多视频都支持）
- 识别同一页面内的多个视频任务点，自动跳过已完成项
- 支持任务点位于章节正文 iframe 内的情况
- 只向第一个未完成任务点授予播放权，完成后自动选择下一个
- 你手动点了别的未完成视频时，脚本会跟随你的选择，不会拉回第一个
- 同页面及跨 iframe 播放互斥，避免多个视频来回切换
- 选片有冷却与切换风暴抑制，不会在多个视频之间反复跳
- 只允许当前选中的任务点播放，其他自动拉起的播放器会被暂停
- 检测到平台验证页（9010）时自动停止操作，等待手动完成验证
- 打开页面时若本节视频已完成，自动打开章节测验或进入下一节
- 检测到播放器反复暂停时主动退避，避免触发平台异常校验
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

安装后请在 Tampermonkey 管理面板确认版本号为 **2.6.13**。

## 更新脚本

`raw.githubusercontent.com` 有 CDN 缓存，Tampermonkey 的"检查更新"不一定会马上拉到新版本。如果更新后行为没变化，请按下面的方式手动更新：

1. 打开 Tampermonkey 管理面板，找到"超星学习助手"。
2. 点击"编辑"。
3. 删除编辑器里的全部内容。
4. 打开 [脚本文件页](https://github.com/naije-c/chaoxing-study-helper/blob/main/chaoxing-study-helper.user.js)，点右上角 **Raw**，全选复制（或直接复制本地脚本文件内容），粘贴进去。
5. 按 `Ctrl+S` 保存。
6. 回到列表确认版本号已变化，然后刷新课程页面。

也可以在管理面板的"实用工具"里从文件导入脚本，或删除旧条目后重新安装。

## 快速开始

### 视频学习

1. 进入超星课程的视频任务页。
2. 脚本会自动选择第一个未完成任务点并开始播放；无需手动点视频。
3. 如果面板没有自动开始，再点击面板顶部的"开始自动播放"按钮。
4. 根据需要设置静音、倍速和自动下一节。

已显示"任务点已完成"的视频会被跳过。想手动看别的未完成视频时，直接点那个视频即可，脚本会跟随你的选择。

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

## 常见问题

### 视频不自动播放

1. 先确认 Tampermonkey 里的版本是 **2.6.13**（更新后行为没变化多半是版本没更新）。
2. 刷新课程页面。
3. 看面板提示：
   - "已选择未完成任务…正在等待视频开始"：播放器没有响应，手动点一下视频即可。
   - "浏览器阻止了自动播放"：点一下视频区域或面板按钮。
   - "任务点已完成…"：本节已完成，脚本会尝试进入下一节。
4. 如果任务点位于跨域 iframe 内，脚本无法直接操作播放器，需要手动点一次。

### 切到后台后视频暂停

- 确认面板按钮显示"暂停"（表示脚本运行中）。
- 2.6.6 起会在标签页重新可见时自动恢复；浏览器"省电模式/标签页休眠"可能仍会暂停媒体。
- 如仍失败，可把课程标签页固定，或关闭浏览器的内存节省/标签页休眠选项。

### 视频反复暂停、继续，或出现【9010】操作异常

- 这是播放器与脚本互相抢占播放状态导致的。2.6.6 起脚本检测到连续暂停会主动静默 30 秒，2.6.9 起选片也有冷却和切换风暴抑制，避免在多个视频间反复跳。
- 2.6.10 起，页面出现 `【9010】操作异常` 验证页时脚本会完全停止自动播放/选片，先手动完成验证码。
- 不要同时启用多个同类学习脚本。

### 全部任务点完成后不跳转

- 确认版本是 **2.6.13**，旧版只处理"多视频且所有卡片都还在"的情况。
- 2.6.11 起，平台同步滞后的任务点状态不会再覆盖页面上的"任务点已完成"标记。
- 2.6.12 起，如果左侧目录里当前章节显示"已完成"，即使某个任务点状态滞后也会直接进入下一节。
- 脚本不会在视频仍在播放时跳转；等当前视频结束。
- 如果本页任务点标记读不出来，会先等待约 16 秒确认，再尝试跳转；面板会显示进度。
- 仍然不跳时，看面板是否提示"没有找到章节测验或下一节入口"，那就是页面结构差异，需要反馈 DOM 截图。

### 更新脚本后行为没变

见上面的"更新脚本"一节：手动编辑并全量粘贴，确认版本号变化后刷新页面。

## 使用限制

- 仅支持超星学习通及内嵌超星页面。
- AI 返回的答案不保证正确。
- 模型自报的置信度不等于真实正确率。
- 图片题、公式题、排序题和复杂富文本仍需继续适配。
- 平台改版可能导致题目选择器、编辑器或域名规则失效。
- 字体解码依赖 `cdn.ocsjs.com` 的特征表。
- 防 9010 的"连续暂停 30 秒静默"是设计行为，不是故障。
- 跨域任务 iframe 中的播放器无法被脚本直接控制，只能尽量通过消息协议协调。
- 浏览器省电模式、标签页休眠可能暂停后台媒体播放。
- Tampermonkey 的自动更新受 Raw CDN 缓存影响，升级后请手动确认版本号。

## 开发与更新

仓库文件：

```text
chaoxing-study-helper.user.js   # 用户脚本
README.md                       # 项目说明
LICENSE                         # MIT 许可证
.gitignore                      # 本地忽略规则
```

脚本是单文件 IIFE，无构建步骤、无 npm 依赖，直接由 Tampermonkey 注入。

更新版本时：

1. 修改 `chaoxing-study-helper.user.js`。
2. 提升脚本头部的 `@version`，同步更新本文件顶部的 badge。
3. 语法检查：`node -e "new Function(require('fs').readFileSync('chaoxing-study-helper.user.js','utf8'))"`。
4. 提交并推送到 `main` 分支。
5. **不要假设 Tampermonkey 会自动更新**：Raw CDN 有缓存，用户侧请按"更新脚本"一节手动确认版本。

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






