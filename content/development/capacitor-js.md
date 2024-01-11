---
title: Capacitor.js - Web Native Apps
date: 2023-09-20
author: Bulin Shaqiri
---

# Capacitor.js - Web Native Apps

Capacitor is a cross-platform runtime that makes it easy to build web apps that run natively on iOS, Android, and the web. It provides a consistent, web-focused set of APIs that allows a web app to access native device features and functionality.

Capacitor is often used in conjunction with frontend frameworks like React, Vue, or Angular to create mobile applications using web technologies.

Adding Capacitor to your web app is quite straight forward. First, the respective packages need to be installed (run `npm install @capacitor/core @capacitor/cli` ) and the capacitor config needs to be created (run `npx cap init` )

As a second step, the respective native platforms of interest need to be installed:

`npm install @capacitor/android @capacitor/ios`

and then added to the project:

`npx cap add android`

`npx cap add ios`

Once that is done, we can simply run `npm run build && npx next export` to export the static assets. However, if your web app is using Next.js's SSR capabilities, the second command won't work. In the case of KlickerUZH, we addressed this by specifying the web app's server URL in the [capacitor.config.ts](https://github.com/uzh-bf/klicker-uzh/blob/v3/apps/frontend-pwa/capacitor.config.ts) file. This configuration packages the web app within the WebView component of the target platform.

Once the native projects are created, we have to sync our web application with the native project. This can be achieved by running `npx cap sync` . Every time a change is made to `capacitor.config.ts` config, the projects need to be synced.

To work on the iOS project, developers need to have XCode installed on their Mac(Book)s.

To open Xcode, you can run `npx cap run ios`

Starting the application is then quite straight forward. You only need to select the simulator of choice and click on the `start` button:

![](https://t4648007.p.clickup-attachments.com/t4648007/0bb68364-5cae-41a6-9de7-c1e1c478cdb7/image.png)

To start the android application, we need to run `npx cap run android` .

To run a devico or emulator on the command-line, you can execute:

`npx cap run android`

Other than for iOS simulators, android emulators run behind a virtual router or firewall service that isolates it from the host machine. This, in turn, means that the emulator cannot detect the host loopback interface (127.0.0.1 or localhost) on the development machine. In order to access the localhost, we need to use the specific network address `10.0.2.2` which serves as a special alias to the host loopback interface.

Since KlickerUZH, for instance, uses custom domains even for local development, things get trickier. This means, that in order to access the localhost behind the custom domain, we need to add new entries with their corresponding mappings to the `/etc/hosts` file of the emulator. This, in turn, is only possible for older emulators that do not have access to Google Play and can be done as outlined in the following:

```plain
1:  cd apps/frontend-pwa
2:  npx cap open android
3:  emulator -avd Pixel_6_Pro_API_28
// on other terminal
4:  adb root
5:  adb remount
6:  adb pull /etc/hosts ./
7:  nano hosts and enter:  10.0.2.2      pwa.klicker.local
                           10.0.2.2      api.klicker.local
8:  adb push host /etc/hosts
9:  adb reboot
```

further links:

- [ ]     [https://developer.apple.com/documentation/xcode/running-your-app-in-simulator-or-on-a-device#Connect-real-devices-to-your-Mac](https://developer.apple.com/documentation/xcode/running-your-app-in-simulator-or-on-a-device#Connect-real-devices-to-your-Mac)
- [ ]     [https://capacitorjs.com/docs/v2/basics/utilities](https://capacitorjs.com/docs/v2/basics/utilities)
- [ ]     [https://capacitorjs.com/docs/guides/push-notifications-firebase#add-the-googleservice-infoplist-file-to-your-ios-app](https://capacitorjs.com/docs/guides/push-notifications-firebase#add-the-googleservice-infoplist-file-to-your-ios-app)
- [ ]     [https://capacitorjs.com/docs/apis/push-notifications#getdeliverednotifications](https://capacitorjs.com/docs/apis/push-notifications#getdeliverednotifications)
