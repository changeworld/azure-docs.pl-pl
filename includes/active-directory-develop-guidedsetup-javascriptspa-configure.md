---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 569a7e74df3016fae133066607fdc0bc32c0044d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293752"
---
## <a name="register-your-application"></a>Rejestrowanie aplikacji

Istnieje wiele sposobów, aby utworzyć aplikację, wybierz jeden z nich:

### <a name="option-1-register-your-application-express-mode"></a>Opcja 1: Rejestrowanie aplikacji (Tryb ekspresowy)
Teraz musisz zarejestrować aplikację w *portalu rejestracji aplikacji Microsoft*:

1.  Zarejestruj swoją aplikację za pośrednictwem [portalu rejestracji aplikacji firmy Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=singlePageApp&appTech=javascriptSpa&step=configure)
2.  Wprowadź nazwę swojej aplikacji i poczty e-mail
3.  Upewnij się, opcja *instrukcje konfiguracji* jest zaznaczone
4.  Postępuj zgodnie z instrukcjami, aby uzyskać identyfikator aplikacji i wklej go w kodzie

### <a name="option-2-register-your-application-advanced-mode"></a>Opcja 2: Rejestrowanie aplikacji (Tryb zaawansowany)

1. Przejdź do [portalu rejestracji aplikacji Microsoft](https://apps.dev.microsoft.com/portal/register-app) do rejestrowania aplikacji
2. Wprowadź nazwę swojej aplikacji i poczty e-mail 
3. Upewnij się, opcja *instrukcje konfiguracji* nie jest zaznaczone
4.  Kliknij przycisk `Add Platform`, a następnie wybierz pozycję `Web`
5. Dodaj `Redirect URL` odnoszą się do adresu URL aplikacji oparte na serwerze sieci web. Zobacz sekcje poniżej, aby uzyskać instrukcje na temat zestawu / uzyskać adres URL przekierowania w programie Visual Studio i języka Python.
6. Kliknij pozycję *Zapisz*

> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio dotyczącymi uzyskiwania adres URL przekierowania
> Postępuj zgodnie z instrukcjami, aby uzyskać adres URL przekierowania:
> 1.    W *Eksploratora rozwiązań*, wybierz projekt i przyjrzyj się `Properties` okna (Jeśli nie widzisz okna właściwości, naciśnij klawisz `F4`)
> 2.    Skopiuj wartości z `URL` do Schowka:<br/> ![Właściwości projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Przejdź z powrotem do *portalu rejestracji aplikacji* i Wklej wartość jako `Redirect URL` i kliknij przycisk "Zapisz"

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Adres URL przekierowania ustawienia węzła
> Dla środowiska Node.js, można ustawić internetowego port serwera w *server.js* pliku. W tym samouczku korzysta z portu 30662 do odwołania, ale możesz użyć dowolnego portu dostępne. W każdym przypadku postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować adres URL przekierowania w informacje o rejestracji aplikacji:<br/>
> - Przejdź z powrotem do *portalu rejestracji aplikacji* i ustaw `http://localhost:30662/` jako `Redirect URL`, lub użyj `http://localhost:[port]/` Jeśli używasz niestandardowego portu TCP (gdzie *[port]* jest niestandardowy port TCP Liczba) i kliknij przycisk "Zapisz"


#### <a name="configure-your-javascript-spa"></a>Konfigurowanie aplikacji JEDNOSTRONICOWEJ JavaScript

1.  Utwórz plik o nazwie `msalconfig.js` zawierający informacje o rejestracji aplikacji. Jeśli używasz programu Visual Studio wybierz projekt (folder główny projektu), kliknij prawym przyciskiem myszy i wybierz opcję: `Add`  >  `New Item`  >  `JavaScript File`. Nadaj mu nazwę `msalconfig.js`
2.  Dodaj następujący kod, aby Twoje `msalconfig.js` pliku:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
<ol start="3">
<li>
Zastąp <code>Enter_the_Application_Id_here</code> identyfikatorem aplikacji, które właśnie zostało zarejestrowane
</li>
</ol>
