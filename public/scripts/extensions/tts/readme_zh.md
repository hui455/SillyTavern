# 提供商要求（Provider Requirements）。
由于我不知道如何在 JS 中实现接口，或者能否实现，又或者只是我太懒了，这里列出了该扩展运行所需的提供商要求。

### class YourTtsProvider
#### 必需
在扩展 index.js 中导出，并添加到 index.js 的 providers 列表中
1. generateTts(text, voiceId)
2. fetchTtsVoiceObjects()
3. onRefreshClick()
4. checkReady()
5. loadSettings(settingsObject)
6. settings 字段
7. settingsHtml 字段

#### 可选
1. previewTtsVoice()
2. separator 字段
3. processText(text)
4. dispose()

# 要求说明
### generateTts(text, voiceId)
必须返回 `audioData.type in ['audio/mpeg', 'audio/wav', 'audio/x-wav', 'audio/wave', 'audio/webm']`
必须接收要渲染的文本以及用于识别所用语音的 voiceId

### fetchTtsVoiceObjects()
必需。
TTS 扩展用它从提供商获取语音对象列表。
必须返回一个表示可用语音的语音对象列表。
1. name：一个友好的用户可见名称，用于分配给角色。显示在用户旁边的下拉列表中。
2. voice_id：提供商特定的语音 id，用于 fetchTtsGeneration() 调用
3. preview_url：一个指向本地音频文件的 URL，用于试听语音
4. lang：可选的语音字符串

### getVoice(voiceName)
必需。
必须返回一个与提供的 voiceName 匹配的单个语音对象。该语音对象至少必须包含以下内容：
1. name：一个友好的用户可见名称，用于分配给角色。显示在用户旁边的下拉列表中。
2. voice_id：提供商特定的语音 id，用于 fetchTtsGeneration() 调用
3. preview_url：一个指向本地音频文件的 URL，用于试听语音
4. lang：可选的语言标识

### onRefreshClick()
必需。
用户点击此按钮以重新连接/重新初始化所选提供商。
响应用户点击刷新按钮的操作，其目的是将提供商重新初始化为可用状态，例如重试连接或检查是否全部加载完成。

### checkReady()
必需。
无错误返回，让 TTS 扩展知道提供商已就绪。
返回错误则会阻止主 TTS 扩展初始化提供商和 UI。该错误会直接显示在 TTS 扩展的 UI 中。

### loadSettings(settingsObject)
必需。
处理提供商加载时来自 TTS 扩展的输入设置。
在此处放置加载提供商设置的代码。

### settings 字段
必需，用于存储需要保存的任何提供商状态。
该字段中存储的任何内容都会由主扩展在 `saveTtsProviderSettings()` 中自动持久化到 extension_settings[providerName] 下，并在提供商被选中时在 `loadTtsProvider(provider)` 中加载。
TTS 扩展不期望任何特定内容。

### settingsHtml 字段
必需，注入到 TTS 扩展 UI 中。除了添加它之外，TTS 扩展不直接依赖它。

### previewTtsVoice()
可选。
用于处理语音样本预览播放的函数，适用于 fetchTtsVoiceObjects() 响应中没有直接 preview_url 的情况

### separator 字段
可选。
当启用朗读引用文本时使用。
定义用于分隔发送给提供商的各组提取引用文本的字符串。提供商将默认使用 `...` 引入停顿

### processText(text)
可选。
在将输入文本传给 TTS 生成器之前对其应用的一个函数。可以是异步的。

### dispose()
可选。
提供商被切换时用于清理提供商资源的函数。