---
title: Aplikacja jednostronicowa (konfiguracji kodu aplikacji) — Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji jednostronicowych (konfiguracji kodu aplikacji)
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
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406433"
---
# <a name="single-page-application---code-configuration"></a>Aplikacja jednostronicowa — Konfiguracja kodu

Dowiedz się, jak skonfigurować kodu aplikacji jednostronicowej (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>Biblioteki MSAL obsługi niejawny przepływ

Platforma tożsamości usługi Microsoft oferuje biblioteki MSAL.js w celu zapewnienia wsparcia niejawny przepływ przy użyciu branży zalecane wskazówki dotyczące zabezpieczeń.  

Dostępne są następujące biblioteki obsługi niejawny przepływ:

| Biblioteka MSAL | Opis |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Zwykła biblioteka języka JavaScript do użycia w dowolnej aplikacji sieci web po stronie klienta utworzone przy użyciu języka JavaScript lub SPA struktur, takich jak Angular, Vue.js, React.js itp. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Otoka podstawowej biblioteki MSAL.js można uproszczenie użycia w aplikacji jednostronicowej skompilowanych przy użyciu usługi Angular framework. Ta biblioteka jest w wersji zapoznawczej i ma [znane problemy dotyczące](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) z określonymi wersjami usługi Angular i przeglądarki. |

## <a name="application-code-configuration"></a>Konfiguracja kodu aplikacji

Biblioteki MSAL informacji o rejestracji aplikacji jest przekazywany jako konfiguracji podczas inicjowania biblioteki.

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
Aby uzyskać szczegółowe informacje na temat dostępnych opcji można skonfigurować, zobacz [Inicjowanie aplikacji przy użyciu MSAL.js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Zaloguj się i wylogowania](scenario-spa-sign-in.md)
