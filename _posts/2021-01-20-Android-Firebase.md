---
title: "[Android] FCM Background Push"
excerpt: "FCM 푸시를 백그라운드에서 받고싶을 때"
toc: true
toc_sticky: true
date: 2021-01-20T22:00:00+09:00
published: true

categories:
    - Android
tags:
    - FCM
---

오늘 FCM 관련하여 작업 중에 알게 된 정보를 정리하는 글입니다.

일단 나는 FCM을 처음 써보았고 FCM에서 보내는 Push 타입에 따라 앱의 포그라운드와 백그라운드가 서로 다르게 동작한다는 것을 알았습니다.

처음에는 FCM 콘솔에서 푸시를 보내면 `FirebaseMessagingService`에서 `onMessageReceived` 메소드으로 가는지 알았어요. 그래서 `onMessageReceived` 에서 Dialog를 띄우는 작업을 했지만 Dialog는 뜨지않았고 FCM 콘솔에서 보낸 푸시는 `onMessageReceived` 으로 가지않는것을 알게되었습니다....

> 그래서 구글에 FCM push type으로 검색을 하였고 [JS LEE 블로그](https://justbobby.tistory.com/3)를 참조했어요.

# FCM Push Type

FCM Push Type에는 총 세가지가 있는데
- Notification
- Data
- Notification + Data

여기서 FCM 콘솔에서 보내는 방식은 첫번째와 세번째 방식이에요. 하나씩 보도록 하죠

## Notification

FCM 콘솔에서 푸시를 보낼때 푸시 제목과 내용을 적는데 이 정보들을 Notification에 담아서 푸시를 보내게되요. 푸시를 받은 앱은 포그라운드냐 백그라운드냐에 따라서 동작이 달라집니다.

- 포그라운드인 경우에는 `onMessageReceived` 으로 가게되어 받은 데이터를 커스텀하여 푸시를 띄울 수 있습니다. 이때 정보들은 `RemoteMessage.notification.title` 와 `body`으로 데이터를 뽑을 수 있어요.

- 백그라운드인 경우에는 `onMessageReceived` 으로 가지않고... 자동으로 title과 body를 뽑아와 푸시를 띄워주게되요. 이러게되면 푸시를 커스텀 할 수 없습니다.

만약 백그라운드에서 푸시를 커스텀하고 싶으면 두번째 방법인 Data 커스텀해서 보내는 방법을 써야합니다.

## Data

FCM 콘솔에서 Data를 추가하여 푸시를 보내기 위해서는 마지막에있는 옵션에서 Custom Data에 key, value을 적어서 보내면됩니다. 하지만 FCM에서는 Notification body가 필수이기 때문에 Notification + Data 방식인 세번째 방식이 되요. 

> 그래서 Custom Data만 보내기 위해서는 FCM 콘솔으로 보낼수없고 별도의 서버에서 직접 구현해야됩니다.

Custom Data만 보내는 방식은 포그라운드나 백그라운드나 동작 방식은 같아요. 둘다 `onMessageReceived` 으로 Data를 받고 `RemoteMessage.data["key"]` 으로 value를 뽑을 수 있습니다.

이렇게 뽑은 Data으로 푸시 알림을 커스텀 할 수 있습니다. 제가 푸시 Dialog를 띄우는것은 두번째 방법이에요.

## Notification + Data

앞에서 말했듯이 FCM 콘솔에서 Custom Data를 포함해서 보내는 방식이 세번째 방식이에요. 이 방식도 포그라운드와 백그라운드의 동작 방식은 첫번째 방식과 똑같습니다.

포그라운드에서 첫번째 방식과 다른 점은 title과 body이외의 Custom Data를 받을 수 있다는 것이에요.

백그라운드에서 다른 점은 똑같이 자동으로 푸시가 띄워지지만 푸시를 클릭을 했을때 Activity으로 이동하게 되면 `intent.getStringExtra("key")` 으로 value를 추출할 수 있습니다.