---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: 2b30f95e050887130db1b2395f51e543a50e25d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250678"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Aplikację można zarejestrować na dwa sposoby, zgodnie z opisem w dwóch następnych sekcjach.

### <a name="option-1-express"></a>Opcja 1: Express

1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=android&step=configure).
2. W **Nazwa aplikacji**, wprowadź nazwę aplikacji.
3. Upewnij się, że **instrukcje konfiguracji** pole wyboru jest wybrany, a następnie wybierz **Utwórz**.
4. Postępuj zgodnie z instrukcjami dotyczącymi uzyskiwania Identyfikatora aplikacji, a następnie wklej go w kodzie.

### <a name="option-2-advanced"></a>Opcja 2: Zaawansowane

1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. W polu **Application Name** (Nazwa aplikacji) wprowadź nazwę aplikacji.
3. Upewnij się, że pole **Guided Setup** (Konfiguracja z przewodnikiem) jest wyczyszczone, a następnie wybierz pozycję **Create** (Utwórz).
4. Wybierz kolejno pozycje **Add platform** (Dodaj platformę), **Native Application** (Aplikacja natywna) i **Save** (Zapisz).
5. W obszarze **aplikacji** > **java** > **{hosta}. { przestrzeń nazw}**, otwórz `MainActivity`. 
6. Zastąp *[Wprowadź identyfikator aplikacji w tym miejscu]* z aplikacją / identyfikator klienta:

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
