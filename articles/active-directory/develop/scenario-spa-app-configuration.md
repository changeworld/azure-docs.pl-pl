---
title: Konfigurowanie aplikacji jednostronicowej — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (Konfiguracja kodu aplikacji)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 111f8d45fced1ed0e9293c874d7a56c64e9fb0e4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965419"
---
# <a name="single-page-application-code-configuration"></a>Aplikacja jednostronicowa: Konfiguracja kodu

Dowiedz się, jak skonfigurować kod dla aplikacji jednostronicowej (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>Biblioteki MSAL obsługujące niejawny przepływ

Platforma tożsamości firmy Microsoft udostępnia następujące biblioteki Microsoft Authentication Library (MSAL), które umożliwiają obsługę niejawnego przepływu przy użyciu zalecanych przez branżę praktyk zabezpieczeń:  

| Biblioteka MSAL | Opis |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Zwykła biblioteka języka JavaScript do użycia w dowolnej aplikacji sieci Web po stronie klienta, która jest skompilowana za pomocą języka JavaScript lub SPA platform, takich jak kątowy, Vue. js i reaguje. js. |
| ![MSAL kątowy](media/sample-v2-code/logo_angular.png) <br/> [MSAL kątowy](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Otoka podstawowej biblioteki MSAL. js, która upraszcza korzystanie z aplikacji jednostronicowych zbudowanych za pomocą struktury kątowej. Ta biblioteka jest w wersji zapoznawczej i ma [znane problemy](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) z niektórymi wersjami i przeglądarkami kątowymi. |

## <a name="application-code-configuration"></a>Konfiguracja kodu aplikacji

W bibliotece MSAL informacje o rejestracji aplikacji są przesyłane jako Konfiguracja podczas inicjowania biblioteki.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Aby uzyskać więcej informacji na temat konfigurowalnych opcji, zobacz [Inicjowanie aplikacji za pomocą MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Logowanie i wylogowywanie](scenario-spa-sign-in.md)
