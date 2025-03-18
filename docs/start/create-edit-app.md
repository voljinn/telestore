---
sidebar_position: 2
---

# 📲 Creating and Editing Applications

## General Information

Each application must have:
- Application URL
- Name
- Description
- Category(-ies)

## Creating an Application URL

1. Open TeleStore via the Telegram bot or website.
2. Go to the <div className="button">Profile</div> tab.
3. Scroll down and click on the <div className="button">Security</div> button.
4. From the opening menu, select <div className="button">Application URLs</div>.
5. Click the <div className="button">+Add New URL</div> button.
6. Then enter:
    - Link Name (it is recommended to use the application name or a similar one)
    - Link to your application or game in the format https://your-link.com
      > If you plan to use webhooks, enable the option <div className="checkbox">✅ Use for webhooks</div>

      > If you plan to use the TeleStore SDK or API for user authentication and registration via TeleStore account, enable the option <div className="checkbox">✅ Use for ext. auth</div>
7. Click the <div className="button">Add</div> button.

## Adding an Application

1. Return to the TeleStore screen and open the <div className="button">My Applications</div> tab.
2. Click <div className="button">+ Add New Application</div>.
3. Fill out the form:
    - *Application Name (will be displayed below the image and indexed by search)
    - *Description (indexed by search)
    - Choose one or more *Categories
    - Enter the link to the Telegram group or channel (if available)
    - Click on the field below (**Name, ID, URL**) and select the previously created application URL.

      > If you want your application to open in an external browser (instead of Telegram's internal browser) when launched from the Telegram version of TeleStore, enable the option <div className="checkbox">✅ Open in external browser</div>.
    - Add 3 images for your application.
    - Click <div className="button">Save Changes</div>.

After that, your application will be sent for moderation, and in case of successful moderation, it will be added to the catalog. Moderation usually takes a few hours.