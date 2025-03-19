---
sidebar_position: 1
---

# 🔑 Getting User Key

The User Key is used to interact with the TeleStore SDK and the TeleStore and TrexWallet APIs.

To create a new User Key:

1. Open TeleStore via the Telegram bot or the website.
2. Go to the <div className="button">Profile</div> tab.
3. Scroll down and click on the <div className="button">Security</div> button.
4. From the opening menu, select <div className="button">User Keys</div>.
5. Ensure that the key type is set to <div className="checkbox">User Key</div>, and click <div className="button">Send OTP Code</div>.
6. Enter the 6-digit code sent to your email / Telegram / phone.
7. Copy the displayed private key; this is the User Key you will use to work with the SDK and API.
    <div className="important">❗️ The User Key is generated on the client side and is never stored in TeleStore. It will be displayed only once. Make sure that you copy the key and store it safely — whoever possesses the key can control your account. If you suspect that your key has fallen into wrong hands, generate a new key and delete the old one.</div>

| 1–3. Profile ➡ Security                  | 4. User keys                  | 5. Switch is set to User key, sending OTP code | 6. Entering the code          | 7. Copying the generated private key |
|------------------------------------------|-------------------------------|------------------------------------------------|-------------------------------|--------------------------------------|
| ![](/img/docs/create-edit-app-01-01.png) | ![](/img/docs/userkey-01.png) | ![](/img/docs/userkey-02.png)                  | ![](/img/docs/userkey-03.png) | ![](/img/docs/userkey-04.png)        |