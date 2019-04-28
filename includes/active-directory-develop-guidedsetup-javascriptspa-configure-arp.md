---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 7a6fa45d0f4ea09fbc84d3185aa0e58db165ac19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60300619"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Dodaj informacje o rejestracji aplikacji do aplikacji

W tym kroku należy skonfigurować adres URL przekierowania aplikacji informacje rejestracyjne, a następnie Dodaj identyfikator aplikacji na aplikację JavaScript SPA.

### <a name="configure-redirect-url"></a>Skonfiguruj adres URL przekierowania

Konfigurowanie `Redirect URL` pola za pomocą adresu URL strony index.html oparte na serwerze sieci web, a następnie kliknij przycisk *aktualizacji*.

> #### <a name="visual-studio-instructions-for-obtaining-the-redirect-url"></a>Visual Studio dotyczącymi uzyskiwania adresu URL przekierowania
> Wykonaj następujące kroki, aby uzyskać adres URL przekierowania:
> 1. W **Eksploratora rozwiązań**, wybierz projekt i przyjrzyj się **właściwości** okna. Jeśli nie widzisz **właściwości** naciśnij klawisze **F4**.
> 2. Skopiuj wartości z **adresu URL** do Schowka:<br/> ![Właściwości projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3. Wklej wartość jako **adresu URL przekierowania** u góry to strony, a następnie wybierz pozycję **aktualizacji**.

<p>

> #### <a name="setting-redirect-url-for-node"></a>Adres URL przekierowania ustawienia węzła
> Dla środowiska Node.js, można ustawić internetowego port serwera w *server.js* pliku. W tym samouczku korzysta z portu 30662 odwołania, ale można użyć dostępny port. Postępuj zgodnie z poniższymi instrukcjami, aby skonfigurować adres URL przekierowania w informacje o rejestracji aplikacji:<br/>
> Ustaw `http://localhost:30662/` jako **adresu URL przekierowania** u góry tej strony lub użyj `http://localhost:[port]/` Jeśli używasz niestandardowego portu TCP (gdzie *[port]* jest niestandardowy numer portu TCP), a następnie kliknij przycisk  **Aktualizacja**

### <a name="configure-your-javascript-spa-application"></a>Skonfigurować aplikację JavaScript SPA

1. W `index.html` plik utworzony podczas konfiguracji projektu, Dodaj informacje o rejestracji aplikacji. Dodaj następujący kod u góry strony, w ramach `<script></script>` tagów w treści swoje `index.html` pliku:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/common",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
