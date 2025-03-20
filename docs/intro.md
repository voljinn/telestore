---
sidebar_position: 1
---

# 👋 Intro

Welcome to the developer documentation for the TeleStore marketplace of web applications and web games. Here you'll find instructions on how to register a developer account, properly set up and publish your applications and games, and use the TeleStore API and SDK for payment processing, user analytics, and more.

## Preparation

To publish your application, you need to ensure that:
- Your application or game is accessible through a website link that you own.
- An SSL certificate is installed on your website.

## Requirements for Applications Published on TeleStore and in the Sandbox

<div className="important">❗️All internal payments (in-app monetization) for TeleStore users must be processed through the TeleStore payment system.</div>
<div className="important">❗️It is prohibited to include links to other marketplaces/platforms for online applications and games (e.g., Yandex.Games, VK Games, OK Games, Facebook Games, etc.). The application must be hosted on your own hosting.</div>
<div className="important">❗️Child pornography in any form will result in a permanent ban of the account and applications, with no option to withdraw remaining funds.</div> 

## Recommendations

1. It's best to optimize the layout of your application for both desktop and mobile devices. User agent verification is performed on the developer's side.
2. If your application includes a user registration and authentication mechanism, we recommend adding the ability to register and log in using a TeleStore account.

## TeleStore Payment System

One of the advantages of the TeleStore marketplace is its own payment system, TrexWallet, which allows for worldwide payment acceptance:
- With VISA and MasterCard.
- With MIR cards.
- With cryptocurrency.

The commission for payment processing is 15%. Developers withdraw funds via cryptocurrency.

## TeleStore and TeleStore Sandbox

The TeleStore catalog is available at https://web.tele.store. This is where you will publish applications and games ready for release. Each application will undergo moderation to ensure compliance with requirements.

We also offer registration in the TeleStore Sandbox at https://dev.tele.store:8081. Here you can test applications before publication in the live catalog, verify proper API and SDK integration, check payments, and much more. All applications are published without moderation.

<div className="important">❗️There may be bugs and failures in the Sandbox, as it is temporarily being used to test updates for the live catalog. The Sandbox will soon be moved to a separate stable version, updated to match the live catalog version.</div>