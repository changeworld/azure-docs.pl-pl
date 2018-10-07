---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: fe2b02b2495b4f37cbc90e1ddbeaca43b41d008c
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842871"
---
## <a name="add-the-applications-registration-to-your-code"></a>Dodawanie rejestracji aplikacji w kodzie

W tym kroku należy dodać aplikację / identyfikator klienta do projektu.

1.  Otwórz `MainActivity` (w obszarze `app`  >  `java`  >  *`{host}.{namespace}`*)
2.  Zastąp wiersz rozpoczynający się od `final static String CLIENT_ID` za pomocą ciągu:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Otwórz: `app` > `manifests` > `AndroidManifest.xml`
4. Dodaj następujące działania na `manifest\application`. `BrowserTabActivity` Umożliwia firmie Microsoft w celu wywołania zwrotnego do aplikacji po zakończeniu uwierzytelniania:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

