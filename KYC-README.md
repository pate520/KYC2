# 🔐 BITDU KYC H5 身份认证页面

## 📋 项目概述

这是一个为 BITDU 平台开发的 H5 KYC（Know Your Customer）身份认证页面，用于替换之前使用的 Sumsub 第三方服务。该页面专为移动端 WebView 集成设计，提供完整的身份认证流程。

## ✨ 功能特性

### 🌍 居住国家或地区选择
- 支持搜索功能的下拉选择器
- 显示格式：英文国家/地区名 + (本土语言名称)
- 每个选项显示对应的国旗图标
- 包含主要国家和地区（中国、美国、日本、韩国等）

### 📄 证件类型选择
- **默认选中**：护照
- **支持类型**：身份证、驾驶证、永久居留证
- 根据选择的证件类型动态调整后续上传要求

### 👤 个人信息输入
- 名字（First Name）
- 姓氏（Last Name）
- 证件号码
- 实时表单验证和错误提示

### 📸 证件照片上传
根据选择的证件类型，动态显示需要上传的照片：

| 证件类型 | 上传要求 |
|---------|---------|
| 护照 | 个人信息页 + 头像页 |
| 身份证 | 正面 + 反面 |
| 驾驶证 | 正面 + 反面 |
| 永久居留证 | 正面 + 反面 |

- 文件大小限制：最大 5MB
- 支持格式：JPG、PNG
- 图片预览和重新上传功能

## 🚀 快速开始

### 1. 文件结构
```
kyc-h5.html          # 主页面文件（包含所有代码）
test-kyc.html        # 测试页面
KYC-README.md        # 说明文档
```

### 2. 本地测试
1. 直接在浏览器中打开 `test-kyc.html`
2. 点击"打开 KYC 页面"按钮进行测试
3. 或者直接打开 `kyc-h5.html` 进行功能测试

### 3. 移动端测试
- 使用浏览器的开发者工具切换到移动端模式
- 或在测试页面中使用"移动端模拟器"功能

## 📱 Android 集成

### WebView 配置
```java
WebView webView = findViewById(R.id.webview);
WebSettings webSettings = webView.getSettings();
webSettings.setJavaScriptEnabled(true);
webSettings.setDomStorageEnabled(true);
webSettings.setAllowFileAccess(true);

// 加载 KYC 页面
webView.loadUrl("file:///android_asset/kyc-h5.html");
```

### JavaScript Bridge 通信

#### 1. 监听 KYC 提交成功
```java
webView.addJavascriptInterface(new Object() {
    @JavascriptInterface
    public void onKYCSubmitSuccess(String data) {
        // 处理 KYC 提交成功
        Log.d("KYC", "提交数据: " + data);
        // 跳转到结果页面或关闭 WebView
    }
}, "Android");
```

#### 2. 向 H5 发送初始数据
```java
// 在页面加载完成后发送用户数据
webView.evaluateJavascript(
    "window.postMessage({" +
    "  type: 'INIT_KYC_DATA'," +
    "  userData: {" +
    "    firstName: 'John'," +
    "    lastName: 'Doe'," +
    "    country: 'CN'" +
    "  }" +
    "}, '*');", null
);
```

### 替换 Sumsub SDK
在现有的 `KycUtils.java` 中，将 `launchSdk` 方法替换为：

```java
public void launchKYCH5(String accessToken, Activity context) {
    Intent intent = new Intent(context, KYCWebViewActivity.class);
    intent.putExtra("access_token", accessToken);
    context.startActivity(intent);
}
```

## 🎨 界面设计

### 响应式布局
- 最大宽度：480px（移动端优化）
- 自适应不同屏幕尺寸
- 触摸友好的交互设计

### 用户体验
- 多步骤表单，减少认知负担
- 进度条显示当前进度
- 平滑的页面切换动画
- 清晰的错误提示和验证反馈

## 🔧 技术栈

- **前端框架**：Vue.js 2.x
- **UI 组件库**：Element UI
- **样式**：CSS3 + 响应式设计
- **文件上传**：HTML5 File API
- **通信**：PostMessage API

## 📝 表单验证规则

### 名字/姓氏验证
- 必填字段
- 最少2个字符
- 只允许字母、中文和空格

### 证件号码验证
- **身份证**：18位标准格式验证
- **护照**：6-20位字母和数字
- **其他证件**：最少6位字符

### 文件上传验证
- 文件类型：仅支持图片格式
- 文件大小：最大5MB
- 必须上传所有必需的证件照片

## 🔄 数据流程

1. **初始化**：接收来自原生 APP 的用户数据
2. **表单填写**：用户逐步完成4个步骤
3. **实时验证**：每个字段失焦时进行验证
4. **文件上传**：本地预览 + 服务器上传
5. **提交确认**：显示所有信息供用户确认
6. **结果通知**：通过 PostMessage 通知原生 APP

## 🚨 注意事项

### 安全考虑
- 所有敏感数据传输需要 HTTPS
- 文件上传需要服务端验证
- 证件照片需要安全存储

### 性能优化
- 图片上传前进行压缩
- 使用 CDN 加载第三方库
- 避免内存泄漏

### 兼容性
- 支持 Android 5.0+ WebView
- 支持主流移动浏览器
- 优雅降级处理

## 🔮 后续开发

### 待实现功能
1. **后端 API 集成**
   - 文件上传接口
   - 数据提交接口
   - 状态查询接口

2. **增强功能**
   - 人脸识别验证
   - OCR 自动识别证件信息
   - 多语言支持

3. **性能优化**
   - 图片压缩和裁剪
   - 离线缓存支持
   - 加载性能优化

## 📞 技术支持

如有问题或需要技术支持，请联系开发团队。

---

**版本**：v1.0.0  
**更新时间**：2024年8月  
**开发者**：BITDU 技术团队
