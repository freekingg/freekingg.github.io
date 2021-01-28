---
title: uniapp原生插件-监听手机短信
date: 2021-01-28 10:42:39
tags:
- uniapp
categories:
- 移动端
---

## 监听手机短信
实时监控手机收到的短信，可获取到发送者的手机号、时间、内容、卡槽等相关信息

## 使用方法
- 下载本插件并存放到nativeplugins目录下
- 在页面中引入这个插件
	``` js
	// 监听短信插件
	const kingReceiceSmsModule = uni.requireNativePlugin("king-receive-sms")
	```
- 调用插件的方法
	``` js
	kingReceiceSmsModule.addCustomizeNotifyMessage(result => {
		<!-- result 为监听到的信息 -->
		var str = JSON.stringify(result);
		console.log(str)
	});
	```
    
 ## 信息格式
``` json
{
	"timestamp":1611751876000,
	"originatingAddress":"+86666666",
	"body":"我是信息内容",
	"subId":0
}
```

- timestamp  接收的时间
- originatingAddress 发送者的号码
- body 信息内容
- subId 卡槽下标

## 注意事项

- 使用前，请确认已经开通 **短信读取权限** 与 **接收新短信权限**，
- 如果没有开通，请引导用户进行授权，可建议使用这个插件 [App权限判断和提示](https://ext.dcloud.net.cn/plugin?id=594)，进行判断开启

<!-- more -->

## java 核心源代码
``` java
package io.king.uniplugin;

import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.os.Build;
import android.os.Bundle;
import android.provider.Telephony;
import android.telephony.SmsMessage;
import android.util.Log;

import java.lang.reflect.Method;
import java.util.HashMap;
import java.util.Map;

public class SmsReceiver extends BroadcastReceiver {

    public static final String TAG = "SmsListenerPackage";
    public static final String SMS_RECEIVED_ACTION = "android.provider.Telephony.SMS_RECEIVED";


    private void receiveMessages(SmsMessage[] messages) throws Exception {
        if (messages == null) {
            return;
        }

        Class ownerClass = Class.forName("android.telephony.SmsMessage");
        Method method = ownerClass.getMethod("getSubId");

        Map<String, String> msg = new HashMap<String, String>(messages.length);
        Map<String, Integer> msgSubId = new HashMap<String, Integer>(messages.length);
        Map<String, Long> msgTime = new HashMap<String, Long>(messages.length);
        for (SmsMessage message : messages) {
            String originatingAddress = message.getOriginatingAddress();

            // Check if index with number exists
            if (!msg.containsKey(originatingAddress)) {
                msg.put(originatingAddress, message.getMessageBody());
                int subId = (int) method.invoke(message);
                msgSubId.put(originatingAddress, subId);
                msgTime.put(originatingAddress, message.getTimestampMillis());
            } else {
                String previousParts = msg.get(originatingAddress);
                String msgString = previousParts + message.getMessageBody();
                msg.put(originatingAddress, msgString);
            }
        }

        for (String sender : msg.keySet()) {
            Log.d(
                    TAG,
                    String.format("%s: %s", sender, msg.get(sender))
            );

            Map<String, Object> receivedMessage = new HashMap<>();

            receivedMessage.put("originatingAddress", sender);
            receivedMessage.put("body", msg.get(sender));
            receivedMessage.put("timestamp", msgTime.get(sender));
            receivedMessage.put("subId", msgSubId.get(sender));

            if (CallBack.getInstance().customizeNotifyMessage != null) {
                CallBack.getInstance().customizeNotifyMessage.onCustomizeNotifyMessage(receivedMessage);
            }
        }
    }

    @Override
    public void onReceive(Context context, Intent intent) {
        SmsMessage[] messages = null;

        if (intent.getAction().equals(SMS_RECEIVED_ACTION)) {
            try {
                final Bundle bundle = intent.getExtras();

                if (bundle == null || !bundle.containsKey("pdus")) {
                    return;
                }

                final Object[] pdus = (Object[]) bundle.get("pdus");
                messages = new SmsMessage[pdus.length];

                for (int i = 0; i < pdus.length; i++) {
                    byte[] pdu = (byte[]) pdus[i];
                    messages[i] = SmsMessage.createFromPdu(pdu);
                    Log.e(TAG,  messages[i].getMessageBody());
                }

            } catch (Exception e) {
                Log.e(TAG, e.getMessage());
            }
        }

        try {
            receiveMessages(messages);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```