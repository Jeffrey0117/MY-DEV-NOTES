---
title: JS實戰-米特API實際操作(發送站內信)

---

# JS實戰-米特API實際操作(發送站內信)


![image](https://hackmd.io/_uploads/B1v1H9EhT.png)


## 代碼展示
```
fetch("https://meteor.today/mail/send_v2", {
  "headers": {
    "accept": "application/json, text/plain, */*",
    "accept-language": "zh-TW,zh;q=0.9,en-US;q=0.8,en;q=0.7,zh-CN;q=0.6",
    "content-type": "application/json;charset=UTF-8",
    "sec-ch-ua": "\"Not A(Brand\";v=\"99\", \"Google Chrome\";v=\"121\", \"Chromium\";v=\"121\"",
    "sec-ch-ua-mobile": "?0",
    "sec-ch-ua-platform": "\"Windows\"",
    "sec-fetch-dest": "empty",
    "sec-fetch-mode": "cors",
    "sec-fetch-site": "same-origin"
  },
  "referrer": "https://meteor.today/mail",
  "referrerPolicy": "strict-origin-when-cross-origin",
  "body": "{\"title\":\"RE: 您有一封來自活動王的邀請💌\",\"content\":\"肚子餓\\n\",\"mailGroupId\":\"65d6866e6badbb7c9ecad35a\",\"targetId\":\"62901b07234aee7b4582ddfe\"}",
  "method": "POST",
  "mode": "cors",
  "credentials": "include"
});
```

## 觀念說明

![image](https://hackmd.io/_uploads/SyMIBc4np.png)


透過登入兩隻帳號，找到每個user會有的ID，以及站內信找到一個mailGroupID，
再去post就可以成功發送站內信！