---
title: Integracja zestawu SDK systemu Android z usługi Azure Mobile Engagement
description: Najnowsze aktualizacje i procedury dotyczące zestawu SDK systemu Android dla usługi Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d72b5014-a22b-4a7f-a470-d2b8145b5b86
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/10/2016
ms.author: piyushjo
ms.openlocfilehash: 21f9c1007c6b7dabc625555c66f267b2b7c579f8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-gcm-with-mobile-engagement"></a>Integrowanie usługi GCM z usługi Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

> [!IMPORTANT]
> Musi występować po integracji procedury opisane w sekcji sposobu integracji usługi Engagement Android dokumentu przed wykonaniem tego przewodnika.
> 
> Ten dokument jest przydatny tylko wtedy, gdy jest już zintegrowana moduł Reach i zamierzasz push urządzenia Google Play. Aby zintegrować kampanie Zasięgowe w aplikacji, przeczytaj najpierw sposobu integracji w systemie Android Reach usługi Engagement.
> 
> 

## <a name="introduction"></a>Wprowadzenie
Integrowanie usługi GCM pozwala aplikacji zostać przeniesiony.

Ładunki GCM wypychana do zestawu SDK zawsze zawiera `azme` klucza w obiekcie danych. Dlatego jeśli używasz usługi GCM dla innego celu w aplikacji można filtrować wypchnięć na podstawie tego klucza.

> [!IMPORTANT]
> Tylko urządzenia z systemem Android w wersji 2.2 lub powyżej, o Google Play zainstalowane i o Google włączono połączenia tła może zostać umieszczony przy użyciu usługi GCM; jednak można je zintegrować kod bezpiecznie na nieobsługiwany urządzenia (tylko używa intencje).
> 
> 

## <a name="create-a-google-cloud-messaging-project-with-api-key"></a>Tworzenie projektu usługi Google Cloud Messaging przy użyciu klucza interfejsu API
[!INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

## <a name="sdk-integration"></a>Integracja zestawu SDK
### <a name="managing-device-registrations"></a>Zarządzanie rejestracji urządzenia
Każde urządzenie musi wysłać polecenie rejestracji do serwerów firmy Google, w przeciwnym razie nie można połączyć.

Urządzenia można także wyrejestrować z powiadomienia usługi GCM (urządzenie jest automatycznie wyrejestrować odinstalowanie aplikacji).

Jeśli nie używasz [zestaw SDK Google Play] lub możesz już wysyłaj zamiar rejestracji samodzielnie, możesz wprowadzić zaangażowania automatyczne rejestrowanie urządzenia za.

Aby je włączyć, Dodaj następujący kod do Twojej `AndroidManifest.xml` pliku wewnątrz `<application/>` tagu:

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### <a name="communicate-registration-id-to-the-engagement-push-service-and-receive-notifications"></a>Identyfikator rejestracji usługi Engagement Push komunikacji i odbierane powiadomienia
Aby komunikować się identyfikator rejestracji urządzenia z usługą Push zaangażowania i otrzymywać powiadomienia jej, Dodaj następujący kod do Twojej `AndroidManifest.xml` pliku wewnątrz `<application/>` tagów (nawet jeśli zarządzasz rejestracji urządzenia samodzielnie):

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

Upewnij się, masz następujące uprawnienia Twojej `AndroidManifest.xml` (po `</application>` tag).

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

## <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>Przyznawanie dostępu usługi Mobile Engagement do klucza interfejsu API usługi GCM
Postępuj zgodnie z [w tym przewodniku](mobile-engagement-android-get-started.md#grant-mobile-engagement-access-to-your-gcm-api-key) udzielenia dostępu usługi Mobile Engagement do klucza interfejsu API usługi GCM.

[zestaw SDK Google Play]:https://developers.google.com/cloud-messaging/android/start
