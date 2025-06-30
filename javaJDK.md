### **1. 确保安装 JDK**
`keytool` 是 JDK 的一部分，因此需要先安装 JDK 并配置环境变量。
- **Windows**：从 [Oracle JDK](https://www.oracle.com/java/technologies/javase-downloads.html) 或 [OpenJDK](https://adoptium.net/) 下载并安装 JDK。
- **macOS/Linux**：使用包管理器安装（如 `brew install openjdk` 或 `apt install openjdk-8-jdk`）。

验证安装：
```bash
# 检查 Java 版本
java -version

# 检查 keytool 是否可用
keytool -help
```

---

### **2. 使用 `keytool` 生成密钥对**
打开终端（Windows 使用 `cmd` 或 PowerShell，macOS/Linux 使用 Terminal），运行以下命令：

```bash
keytool -genkeypair -v
  -keystore siyuan.keystore.jks
  -alias d
  -keyalg RSA
  -keysize 2048
  -validity 10000
```
如果报错，每行结尾加空格 \

#### **参数说明**：
- `-keystore my-release-key.keystore`：生成的密钥库文件名（可自定义）。
- `-alias my-key-alias`：密钥对的别名（后续签名时会用到）。
- `-keyalg RSA`：指定算法为 RSA。
- `-keysize 2048`：密钥长度为 2048 位。
- `-validity 10000`：证书有效期为 10000 天（约 27 年）。

---

### **3. 填写信息**
运行命令后，`keytool` 会提示你输入以下信息，最后回复Y确认：
1. **Keystore 密码**：设置密钥库的密码（例如 `123456`，建议使用强密码）。
2. **密钥密码**：为密钥本身设置密码（可与 keystore 密码相同）。
3. **证书信息**：
   - 姓名和姓氏（What is your first and last name?）
   - 组织单位（Organizational Unit）
   - 组织名称（Organization）
   - 城市/地区（City or Locality）
   - 州/省（State or Province）
   - 国家代码（Country Code，例如 `CN`）

> ⚠️ **注意**：这些信息会嵌入到证书中，但不会影响密钥的功能。填写时可随意填写（例如 `Unknown`）。

---

### **4. 验证生成的密钥**
生成完成后，可以通过以下命令查看密钥库信息：
```bash
keytool -list -v -keystore my-release-key.keystore
```
输入 keystore 密码后，会显示密钥的详细信息（包括有效期、算法等）。

---

### **5. 使用密钥签名 APK**
生成的 `.keystore` 文件可用于 Android 应用签名：
1. **手动签名**（使用 `apksigner`）：
   ```bash
   apksigner sign --ks my-release-key.keystore app-release-unsigned.apk
   ```
2. **在 Android Studio 中使用**：
   - 打开项目，选择 `Build > Generate Signed Bundle / APK`。
   - 选择 `APK` 或 `Android App Bundle`。
   - 点击 `Create new...`，然后选择生成的 `.keystore` 文件并填写密码和别名。

---

### **6. 常见问题**
- **忘记密码怎么办**？  
  如果忘记 keystore 或密钥密码，无法恢复，只能重新生成新的密钥。
  
- **密钥用途**：  
  生成的 `.keystore` 文件用于 Android 应用签名，确保应用更新时使用相同的签名密钥。

- **安全性**：  
  将 `.keystore` 文件保存在安全的地方，并避免泄露密码。

---

### **总结**
通过 `keytool` 生成密钥对的流程如下：
1. 安装 JDK。
2. 使用 `keytool` 命令生成 `.keystore` 文件。
3. 填写必要的信息和密码。
4. 验证生成的密钥。
5. 在 Android 项目中使用该密钥签名 APK。

这种方法完全独立于 Android Studio，适用于任何需要生成签名密钥的场景。