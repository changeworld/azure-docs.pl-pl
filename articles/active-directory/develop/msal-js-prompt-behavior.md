---
title: Monit o zachowanie w interaktywnych żądań (Biblioteka Microsoft Authentication Library dla języka JavaScript) | Azure
description: Dowiedz się więcej na temat dostosowywania zachowania monitu w wywołań interakcyjnego przy użyciu Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd0d736345f312f1a1d6f8f029b41429a3e5f0a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544269"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Zachowanie monitu w MSAL.js interaktywnych żądań

Po użytkownik ustanowiła active Azure AD sesji z wieloma kontami użytkowników, strony logowania usługi Azure AD będzie domyślnie monitować użytkownika o wybranie konta przed przejściem do logowania. Użytkownicy nie będą widzieć wybór konta wystąpić, jeśli istnieje tylko jednej sesji uwierzytelnionych z usługą Azure AD.

Biblioteka MSAL.js (począwszy od v0.2.4) nie wysyła parametr monitu podczas interaktywnych żądań (`loginRedirect`, `loginPopup`, `acquireTokenRedirect` i `acquireTokenPopup`), a tym samym nie wymusza każde zachowanie monitu. Dyskretnej żądania tokenu przy użyciu `acquireTokenSilent` metody MSAL.js przekazuje parametr monitu ustaw `none`.

Oparte na scenariusz aplikacji, można kontrolować zachowanie monitu dla interakcyjnych żądań przez ustawienie parametru za pomocą parametrów żądania przekazywane do metody. Na przykład, jeśli chcesz wywołać środowisko wybierania konta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Następujące wartości monitu mogą być przekazywane podczas uwierzytelniania w usłudze Azure AD:

**Zaloguj się:** Ta wartość spowoduje to wymuszenie użytkownika o podanie poświadczeń na żądanie uwierzytelnienia.

**select_account:** Ta wartość będzie udostępniać użytkownikom środowisko wyboru konta, wyświetlanie listy wszystkich kont w sesji.

**wyrażenie zgody:** Wartość ta będzie wywoływać dialog zgody OAuth, który pozwala użytkownikom na przyznawanie uprawnień aplikacji.

**Brak:** Ta wartość będzie upewnij się, że użytkownik nie widzi wszystkie interaktywne monity. Zalecane jest nie przekazuje tę wartość do interaktywnego metod w MSAL.js, ponieważ może mieć nieoczekiwane zachowania. Zamiast tego należy użyć `acquireTokenSilent` metodę, aby osiągnąć wywołań w trybie dyskretnym.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj więcej na temat `prompt` parametru w [przyznawanie niejawne protokołu OAuth 2.0](v2-oauth2-implicit-grant-flow.md) Biblioteka MSAL.js, które używa protokołu.
