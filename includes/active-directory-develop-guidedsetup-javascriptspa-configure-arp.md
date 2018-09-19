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
ms.openlocfilehash: fc06da3b1ad66aa15237a25d2f50374043c860ba
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293514"
---
## <a name="add-the-applications-registration-information-to-your-app"></a>Dodaj informacje o rejestracji aplikacji do aplikacji

W tym kroku należy skonfigurować przekierowywanie adresów URL aplikacji informacje rejestracyjne, a następnie Dodaj identyfikator aplikacji na aplikację JavaScript SPA.

### <a name="configure-redirect-url"></a>Skonfiguruj adres URL przekierowania

Konfigurowanie `Redirect URL` pola za pomocą adresu URL strony index.html oparte na serwerze sieci web, a następnie kliknij przycisk *aktualizacji*.


> #### <a name="visual-studio-instructions-for-obtaining-redirect-url"></a>Visual Studio dotyczącymi uzyskiwania adres URL przekierowania
> Aby uzyskać adres URL przekierowania:
> 1.    W *Eksploratora rozwiązań*, wybierz projekt i przyjrzyj się `Properties` okna (Jeśli nie widzisz okna właściwości, naciśnij klawisz `F4`)
> 2.    Skopiuj wartości z `URL` do Schowka:<br/> ![Właściwości projektu](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)<br />
> 3.    Wklej wartość jako `Redirect URL` u góry tej strony, następnie kliknij przycisk `Update`

<p/>

> #### <a name="setting-redirect-url-for-node"></a>Adres URL przekierowania ustawienia węzła
> Dla środowiska Node.js, można ustawić internetowego port serwera w *server.js* pliku. W tym samouczku korzysta z portu 30662 do odwołania, ale możesz użyć dowolnego portu dostępne. W każdym przypadku aby skonfigurować adres URL przekierowania w aplikacji informacje rejestracyjne, wykonaj następujące instrukcje:<br/>
> Ustaw `http://localhost:30662/` jako `Redirect URL` u góry tej strony lub użyj `http://localhost:[port]/` Jeśli używasz niestandardowego portu TCP (gdzie *[port]* jest niestandardowy numer portu TCP), a następnie kliknij przycisk "Aktualizuj"

### <a name="configure-your-javascript-spa-application"></a>Skonfigurować aplikację JavaScript SPA

1.  Utwórz plik o nazwie `msalconfig.js` zawierający informacje o rejestracji aplikacji. Jeśli używasz programu Visual Studio wybierz projekt (folder główny projektu), kliknij prawym przyciskiem myszy i wybierz opcję: `Add`  >  `New Item`  >  `JavaScript File`. Nadaj mu nazwę `msalconfig.js`
2.  Dodaj następujący kod, aby Twoje `msalconfig.js` pliku:

```javascript
var msalconfig = {
    clientID: "[Enter the application Id here]",
    redirectUri: location.origin
};
``` 
