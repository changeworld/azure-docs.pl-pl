---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 038ea48bedeb31416627f99b38ebb083846747e4
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842968"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Istnieje wiele sposobów, aby utworzyć aplikację, wybierz jeden z nich:

### <a name="option-1-register-your-application-express-mode"></a>Opcja 1: Rejestrowanie aplikacji (Tryb ekspresowy)
Teraz musisz zarejestrować aplikację w *portalu rejestracji aplikacji Microsoft*:

1.  Zarejestruj swoją aplikację za pośrednictwem [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure).
2.  Wprowadź nazwę swojej aplikacji i poczty e-mail.
3.  Upewnij się, opcja **instrukcje konfiguracji** jest zaznaczone.
4.  Postępuj zgodnie z instrukcjami, aby uzyskać identyfikator aplikacji i wklej go w kodzie.

### <a name="option-2-register-your-application-advanced-mode"></a>Opcja 2: Rejestrowanie aplikacji (Tryb zaawansowany)

1. Przejdź do [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app), aby zarejestrować aplikację.
2. Wprowadź nazwę swojej aplikacji i poczty e-mail.
3. Upewnij się, opcja **instrukcje konfiguracji** nie jest zaznaczone.
4.  Kliknij przycisk **Dodaj platformy**, a następnie wybierz **Web**.
5. Dodaj **adresu URL przekierowania** , który odpowiada aplikacji adres URL oparty na serwerze sieci web. Zobacz sekcje poniżej, aby uzyskać instrukcje na temat ustawiania i uzyskać adres URL przekierowania w programie Visual Studio i języka Node.
6. Wybierz pozycję **Zapisz**.

> #### <a name="setting-redirect-url-for-node"></a>Adres URL przekierowania ustawienia węzła
> Dla środowiska Node.js, można ustawić internetowego port serwera w *server.js* pliku. W tym samouczku korzysta z portu 30662 do odwołania, ale możesz użyć dowolnego portu dostępne. W każdym przypadku postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować adres URL przekierowania w informacje o rejestracji aplikacji:<br/>
> - Przejdź z powrotem do *portalu rejestracji aplikacji* i ustaw `http://localhost:30662/` jako `Redirect URL`, lub użyj `http://localhost:[port]/` Jeśli używasz niestandardowego portu TCP (gdzie *[port]* jest niestandardowy port TCP Liczba) i kliknij przycisk "Zapisz"

<p/>

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio dotyczącymi uzyskiwania adresu URL przekierowania
> Wykonaj następujące kroki, aby uzyskać adres URL przekierowania:
> 1.    W **Eksploratora rozwiązań**, wybierz projekt i przyjrzyj się **właściwości** okna. Jeśli nie widzisz **właściwości** naciśnij klawisze **F4**.
> 2.    Skopiuj wartości z **adresu URL** do Schowka:<br/> ![Właściwości projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Przejdź z powrotem do *portalu rejestracji aplikacji* i Wklej wartość jako **adresu URL przekierowania** i wybierz **Zapisz**


#### <a name="configure-your-javascript-spa"></a>Konfigurowanie aplikacji JEDNOSTRONICOWEJ JavaScript

1.  W `index.html` plik utworzony podczas konfiguracji projektu, Dodaj informacje o rejestracji aplikacji. Dodaj następujący kod u góry strony, w ramach `<script></script>` tagów w treści swoje `index.html` pliku:

```javascript
var applicationConfig = {
    clientID: "[Enter the application Id here]",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
<ol start="3">
<li>
Zastąp <code>Enter the application Id here</code> identyfikatorem aplikacji, które właśnie zostało zarejestrowane.
</li>
</ol>
