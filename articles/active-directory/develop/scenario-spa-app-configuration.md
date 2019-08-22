---
title: Aplikacja jednostronicowa (Konfiguracja kodu aplikacji) — platforma tożsamości firmy Microsoft
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
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891534"
---
# <a name="single-page-application---code-configuration"></a>Jednostronicowa konfiguracja kodu aplikacji

Dowiedz się, jak skonfigurować kod dla aplikacji jednostronicowej (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Biblioteki MSAL obsługujące niejawny przepływ

Platforma tożsamości firmy Microsoft udostępnia bibliotekę MSAL. js do obsługi niejawnego przepływu przy użyciu zalecanych praktyk bezpiecznych.  

Biblioteki obsługujące niejawny przepływ to:

| Biblioteka MSAL | Opis |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Zwykła biblioteka języka JavaScript do użycia w dowolnej aplikacji sieci Web po stronie klienta skompilowanej przy użyciu języka JavaScript lub SPA platform, takich jak kątowy, Vue. js, reaguje. js itp. |
| ![MSAL kątowy](media/sample-v2-code/logo_angular.png) <br/> [MSAL kątowy](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Otoka podstawowej biblioteki MSAL. js, która upraszcza korzystanie z aplikacji jednostronicowych zbudowanych przy użyciu struktury kątowej. Ta biblioteka jest w wersji zapoznawczej i ma [znane problemy](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) z niektórymi wersjami i przeglądarkami kątowymi. |

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
Aby uzyskać więcej informacji na temat dostępnych opcji konfigurowalnych, zobacz [Inicjowanie aplikacji za pomocą MSAL. js](msal-js-initializing-client-applications.md).

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
