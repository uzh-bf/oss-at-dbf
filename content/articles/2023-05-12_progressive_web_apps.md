---
title: Progressive Web Applications
date: 2023-05-12
author: Bulin Shaqiri, Florina Vogel
---

# Introduction

Progressive Web Apps (PWAs) have changed the landscape of modern web development, enabling developers to deliver app-like experiences without the need for users to download a native application. PWAs combine the best features of both worlds, offering the performance and user experience of native mobile apps with the reach and flexibility of web applications. These innovative web apps are not built using only a single technology, but instead leverage advanced web technologies to provide fast, reliable, and excellent user experience. The Section **_Building Blocks_** will provide more information in this regards.

But before moving to the next section, let's see why you should consider going for a PWA.

//Todo: Characteristics //TODO: Advantages

# Building Blocks

## Service Workers

Service workers are a fundamental component of PWAs and also a powerful feature of modern web browsers. They are essentially JavaScript files that run in the background, separate from the web page, and act as a network proxy between the browser and the network, as depicted in Figure X.

For instance, when an app requests a resource within the service worker's scope, even when a user is offline, the service worker intercepts the request. It then determines whether to serve the requested resource from its cache, the network as usual, or generate it using a local algorithm. This capability enables PWAs to offer an experience akin to that of native applications, including the ability to work entirely offline.

![](https://t4648007.p.clickup-attachments.com/t4648007/0c1a77d2-272b-492f-b393-8bb29f08f739/image.png)

Figure X: Role of Service Workers

In this sense, service workers enable a range of advanced features in web applications, such as offline support, background synchronization, and push notifications. As hinted before, they operate independently of the web page and run on a separate thread, which basically means that they do not block the main JavaScript execution of the application and therefore do not directly manipulate the [DOM](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model/Introduction). This architecture ensures that service workers do not negatively impact the performance of the progressive web application.

To use a service worker, developers have to register it in their application, define its scope, and implement the necessary event listeners for the desired functionality. Listing X illustrates how the registration can be done.

```javascript
if ("serviceWorker" in navigator) {
  navigator.serviceWorker
    .register("/service-worker.js")
    .then(function (registration) {
      console.log("Service worker successfully registered.")
      return registration
    })
    .catch(function (err) {
      console.error("Unable to register service worker.", err)
    })
}
```

Listing X: Register Service worker

You can then use the Developer Tools in your preferred browser to verify that the Service worker has been registered. Visit, for example, a PWA of your choice and open the Developer Tools in Google Chrome. Then click on the _Application_ tab and select _Service Workers_ in the left pane. In the best case, you'll get a view similar to the one shown in Figure Y. There, under _Status_, you can see that the PWA provided by the [Klicker app](https://pwa.klicker-prod.bf-app.ch/login) has an active and running service worker*.*

![](https://t4648007.p.clickup-attachments.com/t4648007/46defb67-d13b-4f4c-9666-b6e075c724ee/image.png)

Figure Y: Service worker status

## Web App Manifest

A web app manifest is a JSON file that provides important meta-data about the PWA, which enables browsers to understand how the web application should behave when installed on a user's device. It plays a crucial role in making PWAs more app-like and helps enhance the overall user experience. Web app manifests are required by browsers. Without such a file, PWAs can not be installed.

Web app manifest files typically include the name of the PWA as well as a shorter version of the name that can be displayed on the home screen, for instance. Moreover, a short description of the PWA's functionality, a set of icons in various sizes and formats representing the PWA, a start URL, a preferred display mode (e.g., fullscreen, standalone, etc.) and many more information can be provided as well. Listing Y shows a sample manifest file.

```json
{
  "name": "HackerWeb",
  "short_name": "HackerWeb",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#fff",
  "description": "A readable Hacker News app.",
  "icons": [
    {
      "src": "images/touch/homescreen48.png",
      "sizes": "48x48",
      "type": "image/png"
    },
    {
      "src": "images/touch/homescreen192.png",
      "sizes": "192x192",
      "type": "image/png"
    }
  ],
  "related_applications": [
    {
      "platform": "play",
      "url": "https://play.google.com/store/apps/details?id=cheeaun.hackerweb"
    }
  ]
}
```

Listing Y: Sample Web App Manifest

By including such a web app manifest in a PWA, developers enable browsers with the provided information to treat the web applications more like a native app, providing a more engaging and rich user experience. There are also various [tools](https://www.simicart.com/manifest-generator.html/) available, where you can easily create such manifests online and then simply _copy - paste_ it to the root directory of a project. To make browsers aware of the file, it also needs to be linked with all HTML pages. But more about this topic in Section **_PWAs using Next.js_**. The same way as with the service workers, it is possible to check with the Developer Tools whether the manifest loads e.g., the provided icons correctly. This time, by clicking on Application and then on Manifest, the requested information becomes visible (see Figure Z).

![](https://t4648007.p.clickup-attachments.com/t4648007/d36ccf66-1bd3-49d3-ace2-bca6b230d74f/image.png)

Figure Z: Check if icons are loaded correctly in manifest file

## Push Notifications

Push notifications are messages sent by servers to a user's device, even when the web application or browser is currently not active. These notifications play a crucial role in PWAs as they help enhancing user engagement, providing timely updates, and also promoting re-engagement with the app. It is worth noting that [push messages](https://developer.mozilla.org/en-US/docs/Web/API/Push_API) and [notifications](https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API) are different but complementary technologies. Push is designed to deliver new content from the server to the app without requiring any intervention on the client side. Meanwhile, notifications can be used by the service worker to display new information to the user or at least notify them when updates are available.

In the following, the typical steps for implementing push notifications, i.e., pushing a message followed by displaying it as a notification, within PWAs are outlined very briefly. The topic itself is rather comprehensive. If you are interested or feel the need to dive deeper into the details then have a look at the more detailed description in the [Push Notification](https://app.clickup.com/4648007/v/dc/4dv27-3307/4dv27-9207) subpage and study the following resources from [Mozilla](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Re-engageable_Notifications_Push) and [web.dev](https://web.dev/notifications/).

1.  **Get Permission**: To handle push notifications in a PWA, the browser must support service workers and the PushManager interface, which is responsible for subscribing to and unsubscribing from push notifications. Once both requirements are fulfilled, the PWA must then request and obtain permission from the user to send push notifications. This is typically done using a prompt with a button that triggers the `Notification.requestPermission()` method from the Notifications API.
2.  **Subscribe to push notifications**: After the user has opted in to receive push notifications, we can now subscribe the user by calling the `registration.pushManager.subscribe()` method within the service worker file. Upon successful registration, a `Push Subscription` object (see Listing E) is returned that now needs to be stored in the backend.

```javascript

    {
    "endpoint": "https://some.pushservice.com/something-unique",
    "keys": {
    "p256dh": "BIPUL12DLf...",
    "auth":"FPssNDTK..."
    }
    }
```

4.  **Send push notifications**: To send push notifications to a user's device, the web app needs to make a request to the push service that the user is subscribed to. This is also known as the **web push protocol request**, which is usually send from your server. The request itself includes the message, client as well as delivery configurations (e.g., stop attempting to deliver message after 5 minutes). Afterwards, the push service receives and authenticates the request and sends the push message to the appropriate client.
5.  **Receive and display push notifications**: Push services queue requests until the client is online and the message is delivered or expired. When the client's browser receives a push notification, it decrypts the message and triggers a push event in the service worker. Therefore, a corresponding event handler must be set up in the application code to handle the event, which can be done with the `addEventListener()` method illustrated in Listing w.

```javascript
self.addEventListener("push", (event) => {
  const payload = event.data?.text() ?? "no payload"
  event.waitUntil(
    registration.showNotification("ServiceWorker Example", {
      body: payload,
    }),
  )
})
```

Listing w:

## Caching

Caching is the process of storing data in a temporary location, typically on the client-side (e.g., browser cache), so that it can be quickly retrieved when needed without having to make a network request to the server. It is a critical technique in web development to improve the performance of web applications by reducing the number of server requests and therefore minimizing the amount of data that needs to be transferred over the network.

Also in PWAs, caching constitutes a way to store website-related data on the user's device for offline access. Together with other techniques, caching allows serving the stored content locally to users without requiring a network connection. This means that users get access the website content even when internet connection is lost or when there is just poor network connectivity. Being offline-capable is a requirement when it comes to PWAs, but it depends on the specific application whether a full offline experience is needed. Sometimes, it is fine if your PWA shows a custom message stating that internet connection is needed in order to proceed, or simply offers a basic user interface with restricted functionalities.

Anyway, caching in PWAs is typically implemented with the help of **_Service Workers_**. They allow developers to cache the necessary content and resources, including HTML, CSS, JavaScript, images, and many other assets, on the client-side so that they can be served offline or quickly retrieved from the cache instead of making a network request. This is done by leveraging the [Cache Storage API](https://developer.mozilla.org/en-US/docs/Web/API/CacheStorage) that allows to define a set of caches within your origin (i.e., domain).

In the example shown in Listing P, calling the `open()` method on the `caches` object creates a new cache with the name 'pwa-assets'. It then adds resources to the cache using the `cache.add()` method for storing single resources in the cache, and `cache.addAll()` method for storing multiple resources at once.

```javascript
caches.open("pwa-assets").then((cache) => {
  cache.add("styles.css") // it stores only one resource
  cache.addAll(["styles.css", "app.js"]) // it stores two resources
})
```

Listing P: Create a cache and download/store assets

One of the most common scenarios is to store some essential assets during the installation of the service worker. This use-case is illustrated in Listing U. As previously mentioned, we can use the `waitUntil()` method to request the browser to wait for the `addAll()` promise to be resolved and therefore to increase the opportunity of storing the assets.

```javascript
const urlsToCache = ["/", "app.js", "styles.css", "logo.svg"];
self.addEventListener("install", event => {
   event.waitUntil(
      caches.open("pwa-assets")
      .then(cache => {
         return cache.addAll(urlsToCache);
      });
   );
});
```

Listing U: Wait until all specified assets are stored

To leverage caching, we need to add a `fetch` event listener within the same service worker file. As said earlier, it depends on the specific use case what kind of caching-strategy is used. For instance, when a fetch event is intercepted by the service worker, the developer can check whether the requested resource is already cached and return it from the cache if it is. If the resource has not already been cached, it is then possible to fetch it from the network and cache it for future use. This is also known as the **cache-first** strategy and visually illustrated in Figure G.

![](https://t4648007.p.clickup-attachments.com/t4648007/c865c744-61e2-4c83-8925-b570ae47d53f/image.png)

Figure G: Cache-first strategy

As you can see, this strategy prioritizes performance over freshness (i.e., most up-to-date values).

In Listing R, you can see the code that is part of a service worker that intercepts the network requests made by the browser to the server. It does so by using the aforementioned `fetch` event listener, which listens for all incoming network requests. The `event.respondWith()` method is used to override the default network response with a custom response. In this case, the custom response is returned from the cache, which is queried using the `caches.match()` method. This method searches for a match for the incoming `event.request` in the cache storage. If it finds a match, it returns the cached response, otherwise it falls back to the network and fetches the resource from the server.

```javascript
self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then((cachedResponse) => {
      return cachedResponse || fetch(event.request)
    }),
  )
})
```

Listing R: Cache-first implementation

Another common strategy is the **network-first** strategy, which, as the name implies, prioritizes serving content from the network. This means that when the user requests a resource, the Service Worker requests it from the network first. If the network request fails or takes too long, the Service Worker checks if the resource is available in the cache. If it is, the cached version is returned to the user. Such an approach prioritizes updated content rather than performance and is illustrated in Figure L.

![](https://t4648007.p.clickup-attachments.com/t4648007/717dd5ca-a3de-4920-b294-4ec6676d84c9/image.png)

Figure L: Network-first strategy

A possible implementation shows Listing T where, this time, the requested resource is tried to be fetched by the network first and only in case of an error the code will attempt to return a cached version using `caches.match()` .

```javascript
self.addEventListener("fetch", (event) => {
  event.respondWith(
    fetch(event.request).catch((error) => {
      return caches.match(event.request)
    }),
  )
})
```

Listing T: Network-first cache implementation

This section briefly explains the core principles of caching in the context of PWAs. But there are many more interesting features to be explored, such as [updating cached assets](https://web.dev/learn/pwa/update/), libraries like [workbox](https://web.dev/learn/pwa/workbox/) that help managing caches and service workers, or [storage management](https://web.dev/learn/pwa/offline-data/) to build a solid offline experience. If you would like a more comprehensive example, then visit Mozilla's [caching demonstration](https://developer.mozilla.org/en-US/docs/Web/API/Cache#specifications).

# PWAs using Next.js

### Configure Plugin

When you build a new PWA or want to turn an existing Next.js project into a PWA, you need to use the [**next-pwa**](https://www.npmjs.com/package/next-pwa) package. This package is a plugin for the Next.js framework and provides support for creating PWAs with its many features. Therefore, run `npm install next-pwa` to install the dependency.

As a next step, you need to create or update the `next.config.js` file. This file is used to configure the Next.js project to use the **next-pwa** package and therefore enabling PWA features for the application. Listing U shows the current configuration for the Klicker app. If the Node environment is set to 'test', it exports the original `nextConfig` object without configuring the `next-pwa` package. The reason for this will be further explained in Section **_Caveats_**. If it is not in the test environment then it proceeds configuring the plugin. First, it imports the package with `require` and passes an object with various options to further customize the behavior of the plugin. For example, setting `dest: 'public'` specifies the directory where the service worker file and the manifest file should be placed, which is, in this case, in the `public` directory. You can find many more options with its explanations [here](https://github.com/shadowwalker/next-pwa#configuration). Finally, the configured Next.js object is exported. The complete file is available [here](https://github.com/uzh-bf/klicker-uzh/blob/v3/apps/frontend-pwa/next.config.js).

After running `next build`, this will generate two files in your `public` directory: `workbox-*.js` and `sw.js`, which will automatically be served statically.

```javascript
const nextConfig = {...}

if (process.env.NODE_ENV !== 'test') {
  const withPWA = require('next-pwa')({
    dest: 'public',
    skipWaiting: true,
    dynamicStartUrlRedirect: true,
    disable: process.env.NODE_ENV === 'development',
  })
  module.exports = withPWA(nextConfig)
} else {
  module.exports = nextConfig
}
```

Listing U: Configure project to use the next-pwa plugin

### Web App Manifest

The next step is to add a web app manifest file to the `public` directory, as has been already discussed in the **Web App Manifest** section. However, once the file is created you need to make the browser aware that such a file exists. For this reason, you need to add the line displayed in Listing G to your `_document.js` file inside the `pages` directory.

```javascript
...
<link rel="manifest" href="/manifest.json" />
...
```

Listing G: Link manifest file to your application ([complete example](https://github.com/uzh-bf/klicker-uzh/blob/v3/apps/frontend-pwa/src/pages/_document.tsx#:~:text=%3Clink%20rel%3D%22manifest%22%20href%3D%22/manifest.json%22%20/%3E))

### Service Worker Lifecycle

One nice feature of the `next-pwa` package is that the plugin automatically registers the service worker for you. Therefore, you do not need to do this step.

However, the first event that a service worker receives is an `install` event, which is triggered as soon as the service worker is executed. This event is called only once per service worker. If changes are made to the service worker file, the browser treats it as a new service worker and assigns it its own `install` event. Once the service worker is installed and ready to take over, an `activate` event is then triggered. This event allows the service worker to take control over clients, such as browser tabs. However, activation of the new service worker is delayed until the old service worker no longer controls any clients. This ensures that only one version of the service worker runs at a time.

In some cases, such as the Klicker App (see Listing T), the waiting feature is not needed. In this case, an event listener for the `install` event is added, which triggers the `self.skipWaiting()` method. This method replaces the current service worker with the new one as soon as it is installed, allowing it to take over immediately. A second event listener is added for the `activate` event, which reloads all open pages to ensure they use the latest version of the service worker.

```javascript
// Activate a new service worker immediately when available in order to update the app to the newest version
self.addEventListener("install", async () => {
  self.skipWaiting()
})

// Reload each open page to make sure the new service worker is in charge
self.addEventListener("activate", async () => {
  const tabs = await self.clients.matchAll({ type: "window" })
  tabs.forEach((tab) => {
    tab.navigate(tab.url)
  })
})
```

Listing T: Adapt Service Worker Lifecycle To Skip Waiting Phase

### Enable Push Notifications

To check the current subscription status, the Klicker project has implemented the `determineInitialSubscriptionState()` method, which is shown in Listing Q. It is an asynchronous function that checks whether push notifications are supported by the user's browser and returns the current subscription status. If push notifications are not supported because or the user has denied them, the function returns an object with the corresponding information, and also provides suggestions how to enable push notifications in the latter case.

```typescript
async function determineInitialSubscriptionState() {
  if (
    typeof window === "undefined" ||
    !("serviceWorker" in navigator) ||
    !("PushManager" in window)
  ) {
    return {
      disabled: true,
      info: "Push-Benachrichtigungen werden von Ihrem Browser nicht unterstützt. Dies ist z. B. der Fall, wenn Sie ein iPhone benutzen.",
      reg: null,
      sub: null,
    }
  }
  const registration = await navigator.serviceWorker.ready
  const subscription = await registration.pushManager.getSubscription()
  if (Notification.permission === "denied") {
    return {
      disabled: true,
      info: "Sie haben Push-Benachrichtigungen für diese Applikation deaktiviert. Wenn Sie Push-Benachrichtigungen abonnieren möchten, aktivieren Sie diese in Ihrem Browser und laden Sie die Seite neu.",
      reg: registration,
      sub: subscription,
    }
  }
  return {
    disabled: false,
    info: "",
    reg: registration,
    sub: subscription,
  }
}
```

Listing Q: [Method](<https://github.com/uzh-bf/klicker-uzh/blob/v3/apps/frontend-pwa/src/utils/push.ts#:~:text=%7D-,async%20function%20determineInitialSubscriptionState()%20%7B,%7D,-async%20function%20subscribeParticipant>) for determining initial subscription state

This method is then executed inside the `useEffect()` hook when the `index.js` component mounts for the first time.

Once the user has granted permission to receive push notifications, the user then can officially subscribe to the push service by clicking on the button with the bell icon (see Figure R)

![](https://t4648007.p.clickup-attachments.com/t4648007/d0cddcd3-861a-488e-b513-a5efc59ef875/image.png)

Figure R: Subscribe prompt (Button) clicked

Clicking on that button will trigger the `onSubscribeClick()` method that takes as arguments the `subscribed` parameter that indicates whether the user is currently subscribed to push notifications, and `courseId` that represents the ID of the lecture. In the case where `subscribed` is `true` , it means that the user now wants to unsubscribe. This functionality is not yet implemented in Klicker UZH.

In the other case where the user wants to subscribe to push notifications, the code first checks whether there is actually a subscription object available (determined earlier with the `determineInitialSubscriptionState()` method). If so, this means that the user has previously granted permission for push notifications and has an existing subscription object in the browser's storage. In this case, the code will use the existing subscription object to subscribe the user again and without having to prompt the user for permission again. By calling `subscribeToPush` the subscription object along with the courseId is sent and stored in the backend. If there is no subscription object available, the [subscribeParticipant](https://github.com/uzh-bf/klicker-uzh/blob/v3/apps/frontend-pwa/src/utils/push.ts#:~:text=%7D-,async%20function%20subscribeParticipant(,%7D,-//%20TODO%3A%20function%20updateSubscriptionServer) method is called to obtain such an object and saved later on with `subscribeToPush` .

```typescript
async function onSubscribeClick(subscribed: boolean, courseId: string) {
    setUserInfo('')
    // Case 1: User unsubscribed
    if (subscribed) {
      // TODO: updateSubscriptionOnServer(subscription, courseId)
    }
    // Case 2: User subscribed
    else {
      // Case 2a: User already has a push subscription
      if (subscription) {
        subscribeToPush({
          variables: {
            subscriptionObject: subscription,
            courseId,
          },
        })
        // Case 2b: User has no push subscription yet
      } else {
        try {
          const newSubscription = await subscribeParticipant(
            registration!,
            courseId
          )
          setSubscription(newSubscription)
          subscribeToPush({
            variables: {
              subscriptionObject: newSubscription,
              courseId,
            },
          })
        } catch (e) {...}
      }
    }
    return subscription
  }
```

Listing J: (Un)Subscribe a User

### Send Push Notifications

In the Klicker [source code](https://github.com/uzh-bf/klicker-uzh) under **Packages > graphql > src > lib**, there is a file called `push.ts` which is also illustrated in Listing S. As you can see, this file uses the `web-push` library for sending push notifications. To do so, it first initializes a `SUBSCRIPTIONS` array that will store the subscriber's information (i.e., the subscription object and course ID). Afterwards, the application server keys, that are further discussed in this [subpage](https://app.clickup.com/4648007/v/dc/4dv27-3307/4dv27-9207), are configured in order to digitally sign the messages that are sent to the users. The method `sendPushNotifications()` is then responsible for sending push notifications to a list of subscribers. It does so by looping through the `SUBSCRIPTIONS` array and sends a push notification to each subscriber using the `webpush.sendNotification()` method. The subscriber's endpoint URL and keys are passed as arguments along with the notification payload containing a message and a title.

```typescript
import * as dotenv from "dotenv"
import webpush from "web-push"

const SUBSCRIPTIONS: any[] = []

dotenv.config()

webpush.setVapidDetails(
  "mailto:klicker.support@uzh.ch",
  process.env.VAPID_PUBLIC_KEY as string,
  process.env.VAPID_PRIVATE_KEY as string,
)

async function sendPushNotifications() {
  for (let sub of SUBSCRIPTIONS) {
    console.log(sub.username)
    await sleep(500)
    try {
      const result = await webpush.sendNotification(
        {
          endpoint: sub.endpoint,
          keys: {
            auth: sub.auth,
            p256dh: sub.p256dh,
          },
        },
        JSON.stringify({
          message: "Das Microlearning für BFI Woche 5 ist bis morgen um 09:00 verfügbar.",
          title: "KlickerUZH - Neues Microlearning",
        }),
      )
      console.log(result)
    } catch (e) {
      console.log(e)
    }
  }
}

sendPushNotifications()
```

Listing S: Manually Send Push Messages From Backend

As of now, this method is thought of being executed manually. In this sense, by executing the file manually through the CLI, for example, the `sendPushNotifications()` method is triggered. However, it is important to note that the `SUBSCRIPTIONS` array needs to be manually adjusted as well with the correct information.

### Receive and display Push Notifications

To listen to new push messages an event listener has been added to the service worker file of the Klicker app (Listing S). In this sense, when the push event is triggered, the code retrieves the push data (which is assumed to be in JSON format) using the `event.data.json()` method. Then, it uses the `showNotification` method on the `registration` object to display a notification with the push data. The `showNotification` method takes in a notification object with parameters such as the title, message body, icon, and whether the notification should require user interaction.

```javascript
self.addEventListener("push", function (event) {
  console.log(event)
  const data = event.data.json()
  event.waitUntil(
    registration.showNotification(data.title, {
      body: data.message,
      icon: "/manifest-icon-192.maskable.png",
      requireInteraction: true,
    }),
  )
})
```

Listing S: Push Event Listener

1. Moreover, an event listener for the `notificationclick` event is set up, which is triggered when the user clicks on a notification displayed by the service worker. First, this event listener closes the notification by calling `event.notification.close()` . Afterwards, it checks if any windows or tabs are currently open and controlled by the service worker. If so, it focuses on the first one that is currently focused. If no windows or tabs are open, it opens a new window with the root URL of the app.

```javascript
self.addEventListener("notificationclick", function (event) {
  event.notification.close()
  event.waitUntil(
    clients.matchAll({ type: "window", includeUncontrolled: true }).then(function (clientList) {
      if (clientList.length > 0) {
        let client = clientList[0]
        for (let i = 0; i < clientList.length; i++) {
          if (clientList[i].focused) {
            client = clientList[i]
          }
        }
        return client.focus()
      }
      return clients.openWindow("/")
    }),
  )
})
```

### Offline Modus

As of now, there is no use case for the Klicker app to work offline. However, since it is a requirement of PWAs to respond appropriately to lost Internet connection, the Klicker UZH app has implemented a custom response page to be displayed in case of connection lost (see Figure R). This dedicated page provides users with a clear message as opposed to the custom 404 browser error.

![](https://t4648007.p.clickup-attachments.com/t4648007/0e0d8f67-8749-4316-8377-a7598ffd8601/image.png)

Figure R: Custom Offline Page

# Publishing PWAs

PWAs already offer a variety of benefits such as improved performance, offline capabilities or silent updates. On top of that, they have the ability to be installed on users' devices in order to provide app-like experiences without the need of being listed in any app stores. In some cases, however, it is still worth considering publishing PWAs in the various app stores as it can provide additional benefits:

- **Increased visibility**: Publishing your PWA to the app stores can increase the visibility of the app and reach a larger audience. This is due to the nature of app stores, as users are more likely to discover your PWA when browsing through the various app suggestions rather than browsing the web.
- **Better user experience**: Offering to install your PWA from the app stores provides users with a native-like experience, as upon installation, the app appears directly on the home screen with the appropriate icon. This also makes the installation process consistent across all platforms (e.g., on Android, you would need to klick on **_add to home screen_** rather than on **_install_**)
- **Simplify distribution**: App store listings provide a centralized platform for users to discover and install applications. Publishing your PWA to the app stores can therefore simplify the distribution process, especially for new applications.
- **Improved credibility**: Users often associate app store listings with trustworthy, high-quality apps. In this sense, having your PWA listed alongside native apps can help to build trust and increase user confidence.

To publish PWAs to app stores like Google Play Store, Apple App Store or Microsoft Store, the PWA needs first to be **packaged**. In this sense, depending on the platform or distribution channel being used, the specific format of the package varies (e.g., Android app bundle for Google Play or MSI installer package for Microsoft Store). Fortunately, the open-source community has come up with a nice [tool](https://www.pwabuilder.com/) that simplifies the process of packaging PWAs as native applications for different platforms. Upon successful packaging, the bundle can then be **published** according to the store's rules and guidelines. However, it is important to note that creating a corresponding paid developer account is necessary to publish on these different stores.

The subpage [Publish PWAs to App Stores](https://app.clickup.com/4648007/v/dc/4dv27-3307/4dv27-9227) provides further detailed guidelines on how to publish to the different app stores.

# Caveats

Although PWAs offer several advantages over traditional web applications, there are still some potential caveats which we need to consider. There is a limited support for older browsers, for instance. This means that some browsers may not support all the features that are required for a PWA, which can limit the target group and require additional effort for fallback options.

Moreover, while PWAs have access to some of the device APIs such as the camera or location, the level of access is still limited compared to native mobile apps. Depending on the type of PWA, this can result in reduced functionality and performance. A full list of current PWA capabilities can be found [here](https://whatpwacando.today/).

As we have already seen in the previous section, publishing PWAs to the various stores can increase the discoverability of PWAs, among other benefits. However, the publishing process is not always straight forward, especially if you want to publish on Apple's App Store, since Apple is not a great supporter of this idea.

Moreover, there can be issues during automated testing, as it was the case for the automated cypress tests of Klicker UZH. Headless mode is a mode in which a browser operates without a visible GUI and is often used for automated testing such as with [Cypress](https://app.clickup.com/4648007/v/dc/4dv27-3307/4dv27-7367). In this sense, although the browser still performs all the usual operations without displaying them on the screen service workers still rely on certain functionality that is not available in headless mode, therefore causing service worker not to function properly. This is the reason why we disable the PWA mode in the `test` environment due to several difficulties encountered during the automated testing.

Lastly, since PWAs are hosted on the web, there is always a risk of security vulnerabilities, which can compromise the data and privacy of users.

It seems that push notifications are currently not supported on iOS ([https://www.izooto.com/blog/ios-safari-push-notifications](https://www.izooto.com/blog/ios-safari-push-notifications)). Support might be added in 2023

# Further Resources

//TODO: further insightful learning materials (articles & youtube videos)

## PWA

- Easy to understand tutorial that shows the basics of creating a simple PWA with React:

[https://www.youtube.com/watch?v=IaJqMcOMuDM](https://www.youtube.com/watch?v=IaJqMcOMuDM)

- Very basic tutorial on working with next-pwa with link to other resources in the end:

[https://www.youtube.com/watch?v=ARNN_zmrwcw](https://www.youtube.com/watch?v=ARNN_zmrwcw)

- Example how to add a custom service-worker to a project using next-pwa: [https://github.com/shadowwalker/next-pwa/tree/master/examples/custom-worker](https://github.com/shadowwalker/next-pwa/tree/master/examples/custom-worker)
- Example of a custom service worker: [https://medium.com/proximity-labs/building-a-next-js-pwa-using-next-pwa-and-service-worker-a7acb0ea54bc](https://medium.com/proximity-labs/building-a-next-js-pwa-using-next-pwa-and-service-worker-a7acb0ea54bc)

## Push Notifications

> A more detailed article on push notifications is available [here](2023-04-27_push_notifications.md)

- Google's explanation on how to send and receive push notifications:

[https://www.youtube.com/watch?v=N9zpRvFRmj8](https://www.youtube.com/watch?v=N9zpRvFRmj8)

- MDN's expanation of how to send and receive push notifications:[https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Re-engageable_Notifications_Push](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Re-engageable_Notifications_Push)

- Example with next-pwa and push notifications: [https://github.com/shadowwalker/next-pwa/tree/master/examples/web-push](https://github.com/shadowwalker/next-pwa/tree/master/examples/web-push)
- How to turn a Webapp into a PWA? The bare minimum you need:
  - 1. Use HTTPS
  - 2. Your app needs to register a service worker with a fetch event handler (e.g. to deal with caching, have the site respond with 200 when the user is offline and send push notifications) 3) a manifest file with a set of home screen icons in different sizes (attention: specifying icons in the standard way might not work on older versions of iOS. Instead, logos need to be specified in the publig index.html)

## Publishing PWAs to App Stores

> A more detailed article on publishing PWA is available [here](2023-05-05_publish_pwa.md)

[https://www.youtube.com/watch?v=LELAuNihs18&t=1s](https://www.youtube.com/watch?v=LELAuNihs18&t=1s)

- A very good talk about TWAs by Google - for the ones that want to dive deeper into this topic:

[https://www.youtube.com/watch?v=6lHBw3F4cWs&t=6s](https://www.youtube.com/watch?v=6lHBw3F4cWs&t=6s)

### Google Play Store

- A short [Guide](https://developers.google.com/codelabs/pwa-in-play#0) from Google
- A very short [Guide](https://chromeos.dev/en/publish/pwa-in-play) from ChromeOS Team but with two additional videos (see below):

[https://www.youtube.com/watch?v=ddbHp8tGBwQ](https://www.youtube.com/watch?v=ddbHp8tGBwQ)

[https://www.youtube.com/watch?v=3bAQPnxLd4c&t=253s](https://www.youtube.com/watch?v=3bAQPnxLd4c&t=253s)

- Fireship explaining TWA and publishing their PWA to Google Play (they don't use Bubblewrap here, so it is slightly more complicated):

[https://www.youtube.com/watch?v=7JDFjeMvxos](https://www.youtube.com/watch?v=7JDFjeMvxos)

### Apple App Store

As we have seen, Apple does is not heavily support this functionality. In the following are some links that provide at least some guidance on how one can try to publish PWAs to the App Store:

- [https://blog.pwabuilder.com/posts/publish-your-pwa-to-the-ios-app-store/](https://blog.pwabuilder.com/posts/publish-your-pwa-to-the-ios-app-store/)
- [https://docs.pwabuilder.com/#/builder/app-store?id=prerequisites](https://docs.pwabuilder.com/#/builder/app-store?id=prerequisites)
- [https://aureatelabs.com/blog/get-pwa-on-apple-store/](https://aureatelabs.com/blog/get-pwa-on-apple-store/)

### Microsoft Store

The store with the easiest procedure to publish PWAs:

- [https://learn.microsoft.com/en-us/microsoft-edge/progressive-web-apps-chromium/how-to/microsoft-store](https://learn.microsoft.com/en-us/microsoft-edge/progressive-web-apps-chromium/how-to/microsoft-store)

## Visual Studio Code

Potentially useful VS Code extension:

- [https://marketplace.visualstudio.com/items?itemName=PWABuilder.pwa-studio](https://marketplace.visualstudio.com/items?itemName=PWABuilder.pwa-studio)
