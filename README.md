# iOS Reverse Analysis

## 01. iOS jailbreak
https://unc0ver.dev/

使用 `Xcode + iOS App Signer` 方法越狱，关键是需要配`iOS App Signer`的Provisioning Profile   重签名描述文件,请确保证书已被添加. (切勿选择 Re-Sign Only ，无效！)

## 02. 配置ssh免密访问
```
    brew install usbmuxd
    iproxy 2222:22 1234:1234
    scp -P 2222 .ssh/id_rsa.pub root@localhost:/var/root/
    ssh root@localhost -p 2222  ## default passwd:alpine
    
    #  --- next is in the phone ---
    mkdir .ssh
    mv id_rsa.pub .ssh/authorized_keys
```

## 03. debugserver重签
参考：http://events.jianshu.io/p/bf75f5b497e3

- 抓取debugserver，并导出entitlements
```
    scp -P 2222 root@localhost:/Developer/usr/bin/debugserver ./
    ldid -e debugserver > debugserver.entitlements
```
- 添加：
```json
    <key>platform-application</key>
    <true/>
    <key>get-task-allow</key>
    <true/>
    <key>task_for_pid-allow</key>
    <true/>
    <key>run-unsigned-code</key>
    <true/>
    <key>com.apple.system-task-ports</key>
    <true/>
```

- 删除：
```
    <key>com.apple.security.network.server</key>
    <true/>
    <key>com.apple.security.network.client</key>
    <true/>
    <key>seatbelt-profiles</key>
    <array>
        <string>debugserver</string>
    </array>
```

- 重签放回手机：
```
    ldid -Sdebugserver.entitlements debugserver
    sscp -P 2222 debugserver  root@localhost:/usr/bin/
```