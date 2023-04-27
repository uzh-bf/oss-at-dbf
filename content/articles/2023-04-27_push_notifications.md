---
title: Web Push Notifications
date: 2023-04-27
author: Bulin Shaqiri
---

## Introduction

Push notifications are messages sent by servers to a user's device, even when the web application or browser is currently not active. These notifications play a crucial role in PWAs as they help enhancing user engagement, providing timely updates, and also promoting re-engagement with the app.

It is worth noting that [push messages](https://developer.mozilla.org/en-US/docs/Web/API/Push_API) and [notifications](https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API) are different but complementary technologies. Push is designed to deliver new content from the server to the app without requiring any intervention on the client side. Meanwhile, notifications can be used by the service worker to display new information to the user or at least notify them when updates are available.

In the following, the typical steps for implementing push notifications, i.e., pushing a message followed by displaying it as a notification, within PWAs are outlined. The topic itself is rather comprehensive. If you are interested or feel the need to dive deeper into the details then study the following resources from [Mozilla](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Re-engageable_Notifications_Push) and [web.dev](https://web.dev/notifications/).

## **Get permission**

In order to be able to handle push notifications, the PWA needs to have a service worker registered. To do so, visit Section **_Service Workers_** in the parent page **_Progressive Web Apps_**. If the browser does not support service workers, then the feature becomes not available for this browser. In addition, another check, shown in Listing 1, must be performed to determine whether the push functionality is actually supported for a browser. Specifically, it checks for **_PushManager_** interface in the browser, which is responsible for subscribing to and unsubscribing from push notifications.

```javascript
if (!("PushManager" in window)) {
  // Push isn't supported on this browser, disable or hide UI.
  return
}
```

Listing 1: Check for PushManager existence

Once both requirements are fulfilled, the PWA has first to request and obtain permission from a user in order to be able to send push notification. This is usually done with the help of a button next to a _"Do you want to receive push notification"_ prompt, which will then typically trigger the `Notification.requestPermission()` method as shown in the sample Listing 2.

```javascript
Notification.requestPermission().then(function (result) {
  // Request permission from the user to display push notifications
  // Possible results: 'granted', 'default', 'denied',

  if (result === "granted") {
    console.log("Push notifications are enabled!")
    // Do something if permission is granted, such as subscribing to push
    // notifications
  } else {
    console.log("Push notifications are disabled.")
    // Do something else if permission is not granted, such as disabling push
    // notifications
  }
})
```

Listing 2: Request permission to send notifications

## Subscribe to push notifications

Once the service worker is ready and the user has opted in to receive push notifications, it's time to start the subscription process. This is done with the help of the JavaScript Push API. Specifically, a user can be subscribed to receive push notifications by calling the `registration.pushManager.subscribe()` method.

In the example provided in Listing 3, the `subscribeUserToPush()` function registers the service worker and subscribes the user to push notifications. The `subscribe()` method sets the `userVisibleOnly` option to `true` in the `subscribeOptions` object, which means that all push notifications will be shown to the user each time a push message is received. **_Silent push_** notifications are not widely supported by modern browsers to ensure privacy and prevent misuse. This means that this option has always to be set to `true` .

The `applicationServerKey` option is also set in the `subscribeOptions` object. Application server keys are a unique set of keys consisting of [a public and a private key](https://www.youtube.com/watch?v=AQDCe585Lnc) that identifies the server and allows the push service (will be explained later on) to know which server is sending push notifications. It's important to highlight that the public key is passed to the `subscribe()` method and the private key is **always** kept private!

```javascript
function subscribeUserToPush() {
  return navigator.serviceWorker
    .register("/service-worker.js")
    .then(function (registration) {
      const subscribeOptions = {
        userVisibleOnly: true,
        applicationServerKey: urlBase64ToUint8Array(
          "BEl62iUYgUivxIkv69yViEuiBIa-Ib9-SkvMeAtA3LFgDzkrxZJjSgSnfckjBJuBkr3qBUYIHBQFLXYp5Nksh8U",
        ),
      }

      return registration.pushManager.subscribe(subscribeOptions)
    })
    .then(function (pushSubscription) {
      console.log("Received PushSubscription: ", JSON.stringify(pushSubscription))
      return pushSubscription
    })
}
```

Listing 3: Subscribe user to notifications

To generate a pair of application server keys (which are also known as VAPID keys), you can run `./node_modules/.bin/web-push generate-vapid-keys` in your terminal at the root directory of your project, considering that you are using the [web-push](https://www.npmjs.com/package/web-push) library. The output should look similar as in Figure 1.

![](https://t4648007.p.clickup-attachments.com/t4648007/a52ca69f-7674-4f55-9d8d-b9debcedc604/image.png)

Figure 1: Generated VAPID keys for Klicker DEV environment

Visit [this](https://iamstepaul.hashnode.dev/generating-a-secure-vapid-key-for-a-nodejs-project#heading-using-the-web-push-library) website to check for other possibilities.

To better understand the rest of this article, we need to go a little ahead and look behind the scenes. When the `subscribe()` method of the `PushManager` is called, the `PushManager` then contacts the so-called `push service` to create a new subscription for the user. The push service, in turn, is a server that sends push notifications to user devices. It acts as an intermediary between the web app or website that sends the push notification and the user's device and handles also the delivery of the message to the device, such as a browser.

If the browser happens to be offline, the message is then queued until the browser comes online again. Browsers are free to choose any push service they want. From a development perspective, this does not matter because the API call for push services is [**standardized**](https://datatracker.ietf.org/doc/html/rfc8030), which means that every push service expects the same API call.

Once the push service receives the subscription request from the `PushManager`, it generates a unique endpoint URL for the user's browser and sends it back to the `PushManager`. The `PushManager` creates a `PushSubscription` (see Listing 4) object that contains the endpoint URL and other information needed to send push notifications to the user. This `PushSubscription` object is returned as the value of the `subscribe()` method.

After successful subscription, the `PushSubscription` object serves as an identifier for the user's device. It contains all the necessary information required to send push messages to the device, including the push service's endpoint URL and the keys used to encrypt the message data sent with a push message.

```javascript
{
      "endpoint": "https://some.pushservice.com/something-unique",
      "keys": {
        "p256dh": "BIPUL12DLf...",
        "auth":"FPssNDTK..."
      }
    }
```

Listing 4: Push Subscription Object created by Push Manager

Finally, this PushSubscription object also needs to be stored in the backend. This can be realized with an approach like the one shown in Listing 5. The server should then save the data into a database.

```javascript
function sendSubscriptionToBackEnd(subscription) {
  return fetch("/api/save-subscription/", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify(subscription),
  })
    .then(function (response) {
      if (!response.ok) {
        throw new Error("Bad status code from server.")
      }

      return response.json()
    })
    .then(function (responseData) {
      if (!(responseData.data && responseData.data.success)) {
        throw new Error("Bad response from server.")
      }
    })
}
```

Listing 5: Store Push Subscription Object in backend

## Send push notifications

As mentioned earlier, the web app does not send push notifications directly to the user's device, but instead, makes a request to the respective push service the targeted user has been subscribed to. This is also known as a **web push protocol request**, which is usually send from your server.

Nowadays, there are various [libraries](https://github.com/web-push-libs/) that you can use to send such requests, which should, in summary, include the message, the client you want to send the message, as well as further configurations how the message should be delivered (e.g., stop attempting to deliver the message after 5 minutes). Afterwards, the push service receives and authenticates the request and routes the push message to the appropriate client.

To send the web push protocol request to the correct push service, you need to retrieve the `endpoint` url from the `PushSubscription` object. In Listing 4, the domain of this URL (i.e., [some.pushservice.com](http://some.pushservice.com/)) refers to the respective push service whereas the path of the URL (i.e., something-unique) is the client identifier that points to the desired client we want the message to be pushed to.

To ensure the security of data sent to the push service, it's important to encrypt it with the keys specified in the `PushSubscription`. Remember that browsers are free to choose any push service they want, which might also cause security related issues. With this measure, we prevent the services from reading the content of the messages by simply encrypting the message with the key provided by the browser's `PushManager` . To further prevent unauthorized users from generally sending messages to your users, it is also recommended to [digitally sign](https://www.youtube.com/watch?v=AYdikYrLKRc) web push protocol requests. This includes generating private and public keys once (i.e., the aforementioned application server keys or [VAPID](https://datatracker.ietf.org/doc/html/draft-thomson-webpush-vapid-02) keys), providing the public key when subscribing a client, and signing JSON information with the private key for authentication. When the push service receives the request, it uses the public key to authenticate the signed information.

## Receive and display push notifications

Push services keep requests queued until the client comes online and the message is delivered or the expiration date of the message is reached. In the first case, when the client's browser receives a push notification, it first decrypts the message and dispatches a `push` event to the service worker. This means that we also need to set up a corresponding event handler in our application code in order to be able to know that a message is received. Listing 6 shows an example how this can be done.

It's important to note that this code snippet needs to be added to the service worker file located in you PWA directory. In this sense, `self` refers to the service worker itself and a new `push` event listener has been added to it with the help of the `addEventListener()` method . In this simple example, when a push notification is received, the event listener checks if the event has any data. If it does, the data is parsed as a JSON string and then logged to the console. If the push event has no data, a message is logged to the console indicating that there is no data available.

```javascript
self.addEventListener("push", function (event) {
  if (event.data) {
    // Parses data as JSON string and returns an Object
    console.log("This push event has data: ", event.data.json())
  } else {
    console.log("This push event has no data.")
  }
})
```

Listing 6: Add an Event Listener for Push Events

However, another thing which is important to know about service workers is that they can be terminated at any time by the browser if there are no ongoing tasks. But in some cases, they need to perform tasks that take time to complete, such as sending push notifications or caching resources. In such cases, it is advised to make use of the `waitUntil()` method from the [Service Worker API](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) that allows the service worker to keep running until a specific event has completed (i.e., until all promises passed to `waitUntil()` have been resolved or rejected). This ensures that the service worker can complete its tasks without being prematurely terminated.

Listing 7 provides an example that keeps the service worker alive until the notification has been shown to the user. Inside the `waitUntil()` method, the `showNotification()` method is called on the service worker's `registration` object to display the notification to the user. In this example, the `showNotification()` method takes two arguments, namely the title of the notification, which is set to "ServiceWorker Example", and an _options_ object that contains the body of the notification, which is set to the payload extracted from the push notification.

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

Listing 7: Prevent Service Worker to be terminated prematurely

If you want to learn more about how to influence the [notification behavior](https://web.dev/push-notifications-notification-behaviour/), such as the `notificationclick` event, or simply customize the [display of notifications](https://web.dev/push-notifications-display-a-notification/), click on the corresponding links.
