---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f0e584a4a4a54fc04b5539b56d5c901bfaa42bcc
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293776"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji
Aplikację można zarejestrować na dwa sposoby, zgodnie z opisem w dwóch następnych sekcjach.

### <a name="option-1-express"></a>Opcja 1: Express
1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2.  W **Nazwa aplikacji**, wprowadź nazwę aplikacji.

3. Upewnij się, że **instrukcje konfiguracji** pole wyboru jest wybrany, a następnie wybierz **Utwórz**.

4. Postępuj zgodnie z instrukcjami dotyczącymi uzyskiwania Identyfikatora aplikacji, a następnie wklej go w kodzie.

### <a name="option-2-advanced"></a>Opcja 2: Zaawansowane 
1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. W **Nazwa aplikacji** wprowadź nazwę aplikacji. 

3. Upewnij się, że **instrukcje konfiguracji** pole wyboru jest wyczyszczone, a następnie wybierz **Utwórz**.

4. Wybierz **Dodaj platformy**, wybierz opcję **aplikacji natywnej**, a następnie wybierz pozycję **Zapisz**.

5. W obszarze **aplikacji** > **java** > **{hosta}. { przestrzeń nazw}**, otwórz `MainActivity`. 

6.  Zastąp *[Wprowadź identyfikator aplikacji w tym miejscu]* z aplikacją / identyfikator klienta:

    ```java
    final static String CLIENT_ID = "[Enter the application Id here]";
    ```
<!-- Workaround for Docs conversion bug -->
7. W obszarze **aplikacji** > **manifesty**, otwórz *AndroidManifest.xml* pliku.

8. W `manifest\application`, Dodaj następujące działania. `BrowserTabActivity` Działanie, które umożliwia firmie Microsoft wywołania zwrotnego do aplikacji po jej zakończeniu uwierzytelniania:

    ```xml
    <!--Intent filter to capture System Browser calling back to our app after sign-in-->
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
<!-- Workaround for Docs conversion bug -->
9. W `BrowserTabActivity`, Zastąp `[Enter the application Id here]` z aplikacją / identyfikator klienta.
