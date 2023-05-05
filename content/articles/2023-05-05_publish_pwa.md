---
title: Publish PWA to App Stores
date: 2023-05-05
author: Bulin Shaqiri
---

# Publish PWAs to App Stores

## Google Play Store

In order to be able to publish apps to the Google Play Store, you need to create a [Google Play Developer Account](https://support.google.com/googleplay/android-developer/answer/6112435?hl=en#zippy=) that requires a one time registration fee of US$ 25. This fee is charged to help prevent spam and other malicious activities on the platform. Once you've paid the fee, you can publish as many apps as you want under your account without any additional charges.

The Central IT (Zentrale Informatik) of the University of Zurich manages such an account, which it uses to publish its own apps (like UZH Now) as well as apps developed and published by various other units of the University of Zurich. In this sense, if we want to publish the Klicker app in the UZH App Stores, we need to [get in touch](https://www.zi.uzh.ch/en/teaching-and-research/software-elearning/mobile.html) with the Central IT. You can find the full list of published Android apps by UZH [here](https://play.google.com/store/apps/developer?id=Universit%C3%A4t+Z%C3%BCrich).

Moreover, if you want to publish your PWA to Google Play Store, you need to ensure that the PWA meets all of Google Play Store's policies and requirements.

### Package your PWA

[Bubblewrap](https://github.com/GoogleChromeLabs/bubblewrap) is a powerful command-line tool and Node.js library developed by Google that enables developers to easily generate and sign Android apps that contain a PWA. Specifically, Bubblewrap allows developers to create an Android app that wraps their PWA in a WebView component, which provides a full-screen, native-like experience for users. This is made possible through the **Trusted Web Activity** (TWA) feature of the Android operating system, which allows PWAs to be launched from the device's home screen as if they were native apps.

However, while Bubblewrap can configure and build the Android application bundle, developers must also update the web application to fully integrate the PWA with the Android app. This involves configuring a **Digital Asset Links** file to establish a trust relationship between the PWA and the Android app that will be used to display it. The corresponding `assetlinks.json` file contains information about the PWA, such as its name, package name, and digital certificate information (i.e., the signing key fingerprint), and is hosted on the PWA's server. It is particularly placed inside the `.well-known` directory which is located at the root level of the application. The **assetlinks.json** file should therefore be accessible at **my-pwa.origin/.well-known/assetlinks.json.** In this sense, the Android app checks this file to verify that the PWA and Android app are authorized to interact with each other, ensuring a secure and trusted environment for the PWA.

To get started, we need to install Bubblewrap from NPM using `npm install -g @bubblewrap/cli` .

Afterwards, we need run the Bubblewrap CLI to generate the configuration as well as the Android project that we will use for building the Android App Bundle that we will subsequently upload to Google Play. To do so, create a directory where you want to store the outputs of this process and run the following command:

`bubblewrap init --manifest=https://pwa.klicker-prod.bf-app.ch/manifest.json`

Bubblewrap, in this case, is initialized with the location of the PWA's (deployed version!) Web App Manifest file. Based on that, a default configuration is created and an in-console wizard is started. During the interaction with the wizard, the configurations can be adapted.

To be upfront, in order to publish an app on Google's Play Store, the application package must be digitally signed with a certificate, which is also known as the **signing key**. This is a unique self-signed certificate that **_differs from the one used to serve the application over HTTPS and also from the application server keys introduced within the Push Notifactions section_**.

When creating an Android app using Bubblewrap, you will be prompted to provide the path to the signing key. If you have an app that is already listed on the Play Store and you want to update it or create a new version of it using Bubblewrap, you will need to use the **same** signing key that was used for the original app listing. Otherwise, if the app is not listed yet on Play Store and nor is there an existing signing key available, it is then advised to use the default value in Bubblewrap to create a new key, as it is shown in Figure O.

![](https://t4648007.p.clickup-attachments.com/t4648007/e33b3927-d16e-4aed-a6ba-4286b85102ea/image.png)

Figure O: Bubblewrap CLI - Create Signing Key

After completing all steps of the wizard, you will then find a **twa-manifest.json** file that stores the values selected during the wizard interaction (can be used to regenerate Bubblewrap project if needed), many **Android project files** that will be used as source for building application bundles, and, if selected, the **signing key** that needs to be **_securely_** stored.

These created resources can subsequently be used to build the Android App Bundles for the PWA.

This is simply done by running `bubblewrap build` in the terminal and outputs the following two files:

- **app-release-bundle.aab**: The file that needs to be uploaded to Google Play Store
- **app-release-signed.apk**: Android packaging format that allows applications to be installed directly on devices for testing purposes

### Add PWA to Play Store

Once you have packaged your app and also possess a developer account, you can sign in into [Google Play Console](https://play.google.com/console) and create a new app by clicking on the **_Create app_** button. Next, you will be guided through the creation of a new Android app listing where you can define information such as the application name, default language, etc. After creating the new app listing, you will be redirected to the dashboard where you can find a checklist of tasks to be completed in order to set up, start testing, and release your PWA.

It is worth mentioning that the use of the Google [Play App Signing](https://support.google.com/googleplay/android-developer/answer/9842756#zippy=%2Cdescriptions-of-keys-artifacts-and-tools) feature is highly recommended and currently set as default. It allows Google to manage your application's signing key and uses it to sign the application bundles intended for distribution. The good thing is that the key will be stored on Google's secure infrastructure.

In this sense, the initial signing key generated by Bubblewrap becomes now the **upload key** that is used to verify the identity of the app developer and ensure that the app has not been tampered with during the upload process. Also a **new signing key** (generated by Google or provided by user) is now managed by Google and used to sign the app itself. Signing the app with this key creates a unique digital signature that identifies the application and verifies that it has not been modified since it was signed. This process is illustrated in Figure V.

![](https://t4648007.p.clickup-attachments.com/t4648007/3ff31d76-8cbc-4fa3-9a11-88e977eef2e8/image.png)

Figure V: Upload and signing key

When using Google Play App Signing, it is important to note that the PWA's Digital Asset Links must be reconfigured to point to the SHA-256 fingerprint of the certificate used to sign the package that the user receives on their phone, which is associated with the new signing key.

To obtain this SHA-256 fingerprint, developers can navigate to **Releases > Setup > App Integrity** within their application in the Play Console.

Bubblewrap can manage the signature fingerprints you've retrieved and generate the correct Digital Asset Links file for you. To do so, run the following command from within the same directory created during initialization, replacing `<fingerprint>` with the fingerprint copied from the **App Integrity** page:

`bubblewrap fingerprint add <fingerprint>`

This command will add the fingerprint to the application's fingerprint list and generate a new **assetlinks.json** file, with which you then can replace the old one in **root** (/public in Next.js project) **\> .well-known** of the PWA.

## Apple App Store

When publishing apps on the iOS App Store, it becomes evident that the policies and guidelines are much more stringent than those for Google Play. As a result, PWAs may face challenges in meeting the App Store's requirements. For example, in their policies they state that:

> _“Your app should include features, content, and UI that_ **_elevate it beyond a repackaged website_**_. If your app is not particularly useful, unique, or “app-like,” it does not belong on the App Store.”_

These are not the best preconditions. But despite these challenges, the open-source community has come up with a tool called [PWABuilder](https://www.pwabuilder.com/) that helps packaging PWAs for native app stores, among others. It is powered by Bubblewrap and shares the same underlying core. PWABuilder has now also support for creating iOS application packages that can be posted to the Apple App Store. It essentially generates a native Swift app that contains a WebView component, therefore allowing developers to load their PWAs within a native iOS application. However, packaging PWAs for iOS is still an **experimental feature**. In addition to that does Apple not heavily support PWAs natively. In this sense, PWABuilder points out that they cannot guarantee that the PWA is going to be accepted in Apple's App Store.

Nevertheless, we can still give it a try. As opposed to publishing to Google Play we will need to go a few extra steps to get the PWA ready for the App Store. To start with, we need a Mac OSX device with [Xcode](https://developer.apple.com/xcode/) installed on it. Apart from that, a valid PWA and an [Apple Developer account](https://developer.apple.com/programs/) is also required. The developer account for apple, however, is much more expensive as you need to pay an **annual fee** between $US 99 and $US 299 (enterprise version). The [Central IT](https://www.zi.uzh.ch/en/teaching-and-research/software-elearning/mobile.html) of the University of Zurich has already launched some [apps](https://apps.apple.com/us/developer/universit%C3%A4t-z%C3%BCrich/id1055588978) also on Apple's App Store. They provide support for units of the University of Zurich. So, if you want to publish an app to the UZH app store, it makes sense to reach out to the Central IT.

### Package your PWA

Compared to using Bubblewrap's CLI, the GUI solution of PWAbuilder offers a fast and convenient way to package your PWA. All you need to do is navigating to [pwabuilder.com](https://www.pwabuilder.com/), enter the link to your pwa and select the package for the iOS distribution. You'll be redirected to enter some metadata about your app afterwards. At this stage, you should note down the `Bundle ID` information, which will be needed to publish the app to the App Store. What's left in this step is downloading the package.

### Build and publish your app

Before being able to publish the PWA, you will need to locally build the Swift project. To do so, you first need to install **cocoapods** first. This can be done with the following command:

`brew install cocoapods`

Afterwards, you need to unzip the package downloaded from PWABuilder and open a terminal in the `src` directory. In there, run the `pod install` command. As a next step, open the `.xcworkspace` file inside the `src` directory and click on **Product > Build** in Xcode to build the project. You'll now be able to test the PWA on Iphone emulators.

What's left is publishing the app on App Store. To do so, you can follow this step-by-step [guide](https://docs.pwabuilder.com/#/builder/app-store?id=publishing) which provides great assistance for this part.

## Microsoft Store

Out of the three app stores discussed in this article the Microsoft Store seems to be the most welcoming store for PWAs. They have quite some PWAs already listed on their store. Microsoft even used [Bing’s crawling mechanisms](https://www.seroundtable.com/bing-to-crawl-web-for-pwas-24467.html) to search the web for quality PWAs that it could automatically add to its app store. It is therefore no surprise that publishing to Microsoft Store is quite straight forward.

What you need to have is a valid Microsoft account to register a Developer Account in their [Partner Center](https://learn.microsoft.com/en-us/windows/apps/publish/partner-center/open-a-developer-account). There is a one-time fee of US$ 19 for individuals and US$ 99 for companies. I couldn't find any information whether the Central IT of the University of Zurich already has such an account. It therefore makes sense to reach out to them first if you would like to publish an application as part of a unit of the University of Zurich.

### Create an app reservation

After having a valid account that is enrolled in the developer program navigate to the [Partner Center Dashboard](https://partner.microsoft.com/en-us/dashboard/home) where you can sign in with your account. Under **Workspaces** select **Apps and games**. Move on by clicking on **New product > MSIX or PWA** app which then allows you to reserve a product name for your PWA.

As a next step, we click on **Product Management > Product Identity** and save the values for **Package ID**, **Publisher ID**, and **Publisher display name** (see Figure W).

![](https://t4648007.p.clickup-attachments.com/t4648007/e928bf75-7ccc-49c0-b1e7-d737345e1fd4/image.png)

Figure W

### Package your PWA

For packaging your PWA, Microsoft suggests using the already introduced [PWABuilder](https://www.pwabuilder.com/) tool. For the process of packaging the PWA, the information saved in the previous step will be needed.

After downloading the package, a `.zip` file is obtained that contains an `.msixbundle` and a `.classic.appxbundle` file that allow the PWA to be executed on a wide variety of Windows versions.

### Publish your PWA

In order to submit your PWA, you need to sign in again into the [Microsoft Partner Center](https://partner.microsoft.com/en-us/dashboard/home). Next, you select your app and click on the **Start your submission** button (see Figure N).

![](https://t4648007.p.clickup-attachments.com/t4648007/b3b5f810-05b8-47ee-bf43-15bd2cd2574a/image.png)

Figure N

During this process you will be prompted to provide further details (e.g., age rating, pricing, etc.). It is important that for the Packages prompt you provide the previously generated `.msixbundle` and `.classic.appxbundle` files. After submission, it takes usually between 24 to 48 hours to review the application and receive approval
