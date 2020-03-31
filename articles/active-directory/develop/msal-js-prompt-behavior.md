---
title: Interaktywne zachowanie monitu żądania (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dostosować zachowanie monitów w interakcyjnych połączeniach przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 778e89655019a49a30904fbe8d8e6aedf1833e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695980"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Szybkie zachowanie w interakcyjnych żądaniach msal.js

Gdy użytkownik ustanowił aktywną sesję usługi Azure AD z wieloma kontami użytkowników, strona logowania usługi Azure AD domyślnie monituje użytkownika o wybranie konta przed przystąpieniem do logowania. Użytkownicy nie zobaczą środowiska wyboru konta, jeśli istnieje tylko jedna uwierzytelniona sesja z usługą Azure AD.

Biblioteka MSAL.js (począwszy od wersji 0.2.4) nie wysyła`loginRedirect` `loginPopup`parametru monitu podczas żądań interaktywnych ( , `acquireTokenRedirect` i `acquireTokenPopup`), a tym samym nie wymusza żadnych żądań monitu. W przypadku cichych `acquireTokenSilent` żądań tokenów przy użyciu metody `none`msal.js przekazuje parametr monitu ustawiony na .

Na podstawie scenariusza aplikacji można kontrolować zachowanie monitu dla żądań interaktywnych, ustawiając parametr prompt w parametrach żądania przekazanych do metod. Na przykład, jeśli chcesz wywołać doświadczenie wyboru konta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Następujące wartości monitu mogą być przekazywane podczas uwierzytelniania za pomocą usługi Azure AD:

**zaloguj się:** Ta wartość wymusi użytkownikowi wprowadzenie poświadczeń w żądaniu uwierzytelnienia.

**select_account:** Ta wartość zapewni użytkownikowi doświadczenie wyboru konta z listą wszystkich kont w sesji.

**zgoda:** Ta wartość będzie wywoływać dialog zgody OAuth, który umożliwia użytkownikom udzielanie uprawnień do aplikacji.

**brak:** Ta wartość zapewni, że użytkownik nie zobaczy żadnych interaktywnych monitów. Zaleca się, aby nie przekazywać tej wartości do interaktywnych metod w pliku MSAL.js, ponieważ może ona mieć nieoczekiwane zachowania. Zamiast tego należy `acquireTokenSilent` użyć metody do osiągnięcia cichych połączeń.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej `prompt` o parametrze w protokole [niejawnego przyznania OAuth 2.0,](v2-oauth2-implicit-grant-flow.md) którego używa biblioteka MSAL.js.
