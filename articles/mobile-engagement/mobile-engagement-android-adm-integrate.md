---
title: Integracja zestawu SDK systemu Android z usługi Azure Mobile Engagement
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK systemu Android dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a7d719ec-67b3-4be3-9d7f-0b61a57fe978
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 4c3b8f60333e6096411aad8499bb4bfc36e53f3c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-adm-with-engagement"></a>Integrowanie usługi ADM z zaangażowania
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!IMPORTANT]
> Musi występować po integracji procedury opisane w sekcji sposobu integracji usługi Engagement Android dokumentu przed wykonaniem tego przewodnika.
> 
> Ten dokument jest przydatny tylko wtedy, gdy jest już zintegrowana moduł Reach i zamierzasz push Amazon urządzeń. Aby zintegrować kampanie Zasięgowe w aplikacji, przeczytaj najpierw sposobu integracji w systemie Android Reach usługi Engagement.
> 
> 

## <a name="introduction"></a>Wprowadzenie
Integrowanie usługi ADM pozwala aplikacji naciśnięty, gdy przeznaczonych dla urządzeń z systemem Android firmy Amazon.

Ładunki ADM wypychana do zestawu SDK zawsze zawiera `azme` klucza w obiekcie danych. Dlatego jeśli używasz usługi ADM do innych celów w aplikacji można filtrować wypchnięć na podstawie tego klucza.

> [!IMPORTANT]
> Tylko Amazon Kindle urządzeń z systemem Android 4.0.3 lub nowszym są obsługiwane przez usługi Amazon Device Messaging; jednak można je zintegrować ten kod bezpiecznie na innych urządzeniach.
> 
> 

## <a name="sign-up-to-adm"></a>Zarejestruj się, aby usługi ADM
Jeśli nie zostało już przeprowadzone, należy włączyć ADM na Twoje konto Amazon.

Procedura została szczegółowo opisana w: [ <https://developer.amazon.com/sdk/adm/credentials.html> ].

Po zakończeniu procedury, można uzyskać:

* Poświadczeniami OAuth (identyfikator klienta i klucz tajny klienta) dla usługi Engagement można było wypychania urządzenia.
* Klucz interfejsu API, które muszą zostać włączone do aplikacji.

## <a name="sdk-integration"></a>Integracja zestawu SDK
### <a name="managing-device-registrations"></a>Zarządzanie rejestracji urządzenia
Każde urządzenie musi wysłać polecenie rejestracji do serwerów usługi ADM, w przeciwnym razie nie można połączyć.

Jeśli używasz już [biblioteki klienta usługi ADM], a jeszcze [zintegrowane usługi ADM] możesz bezpośrednio przejść do android-sdk-adm-receive.

Jeśli nie ma jeszcze zintegrowane usługi ADM, Engagement ma prostszy sposób, aby je włączyć w aplikacji:

Edytowanie użytkownika `AndroidManifest.xml` pliku:

* Dodaj przestrzeń nazw Amazon plik powinny one zacząć następująco:
  
      <?xml version="1.0" encoding="utf-8"?>
      <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:amazon="http://schemas.amazon.com/apk/res/android"
* Wewnątrz `<application/>` tagu, należy dodać w tej sekcji:
  
      <amazon:enable-feature
         android:name="com.amazon.device.messaging"
         android:required="false"/>
  
      <meta-data android:name="engagement:adm:register" android:value="true" />
* Po dodaniu tagu amazon, może być błąd kompilacji, jeżeli urządzenie docelowe kompilacji projektu jest niższy niż Android 2.1. Należy użyć **Android 2.1 +** kompilacji docelowego (nie martw się, może nadal mieć `minSdkVersion` ustawioną 4).
* Integracja klucz interfejsu API usługi ADM jako zasób, postępując [tej procedury].

Następnie postępuj zgodnie z instrukcjami w następnej sekcji.

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Identyfikator rejestracji usługi Engagement Push komunikacji i odbierane powiadomienia
Aby komunikować się identyfikator rejestracji urządzenia z usługą Push zaangażowania i otrzymywać powiadomienia jej, Dodaj następujący kod do Twojej `AndroidManifest.xml` pliku wewnątrz `<application/>` tagów (nawet jeśli używasz usługi ADM bez zaangażowania):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>

         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

Upewnij się, masz następujące uprawnienia Twojej `AndroidManifest.xml` (przed `</application>` tag).

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## <a name="grant-engagement-oauth-credentials"></a>Poświadczenia GRANT zaangażowania OAuth
Przedstawia poświadczeń OAuth (identyfikator klienta i klucz tajny klienta) w portalu Engagement.

[<https://developer.amazon.com/sdk/adm/credentials.html>]:https://developer.amazon.com/sdk/adm/credentials.html
[biblioteki klienta usługi ADM]:https://developer.amazon.com/sdk/adm/setup.html
[zintegrowane usługi ADM]:https://developer.amazon.com/sdk/adm/integrating-app.html
[tej procedury]:https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
