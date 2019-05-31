---
title: Uniknąć przeładowania strony (Biblioteka Microsoft Authentication Library dla języka JavaScript) | Azure
description: Dowiedz się, jak uniknąć przeładowania strony nabywania i odnawianie tokenów dyskretne przy użyciu Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 162811221e6dde89ad11f358b2ec8f32f3c82522
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420471"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Uniknąć przeładowania strony nabywania i odnawianie tokeny, w trybie dyskretnym przy użyciu MSAL.js
Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) używa ukryte `iframe` elementy do uzyskania i odnawiania tokenów w trybie dyskretnym w tle. Usługa Azure AD zwraca token z powrotem do zarejestrowanych redirect_uri, określony w żądaniu tokenu (domyślnie jest to strony głównej aplikacji). Ponieważ odpowiedzi 302, powoduje to HTML odpowiadający `redirect_uri` ładowany w `iframe`. Zazwyczaj aplikacji `redirect_uri` strony głównej i to powoduje, że załadować ponownie.

W innych przypadkach, jeśli przejście do strony głównej aplikacji wymaga uwierzytelnienia, jego może prowadzić do zagnieżdżonych `iframe` elementów lub `X-Frame-Options: deny` błędu.

Ponieważ MSAL.js nie można odrzucić 302 wystawione przez usługę Azure AD i jest wymagany do procesu zwrócony token, nie można zapobiec `redirect_uri` z ładowany w `iframe`.

Aby uniknąć całej aplikacji, ponownie załadować ponownie lub inne błędy spowodowane ze względu na to, wykonaj te rozwiązania problemu.

## <a name="specify-different-html-for-the-iframe"></a>Określ inny kod HTML dla elementu iframe

Ustaw `redirect_uri` właściwość konfiguracji do to prosta strona, która nie wymaga uwierzytelniania. Musisz upewnić się, czy jest on zgodny z `redirect_uri` zarejestrowany w witrynie Azure portal. Nie dotyczy to logowania użytkownika jako biblioteki MSAL zapisuje strony początkowej, gdy użytkownik rozpoczyna się proces logowania i przekierowuje do dokładnej lokalizacji po ukończeniu logowania.

## <a name="initialization-in-your-main-app-file"></a>Inicjowanie pliku głównego aplikacji

Jeśli aplikacja ma strukturę taki sposób, że istnieje jeden centralny plik Javascript, który definiuje inicjowania aplikacji, routingu i inne rzeczy, warunkowo można załadować aplikacji moduły oparte na tego, czy aplikacja jest ładowany w `iframe` czy nie. Na przykład:

In AngularJS: app.js

```javascript
// Check that the window is an iframe and not popup
if (window !== window.parent && !window.opener) {
angular.module('todoApp', ['ui.router', 'MsalAngular'])
    .config(['$httpProvider', 'msalAuthenticationServiceProvider','$locationProvider', function ($httpProvider, msalProvider,$locationProvider) {
        msalProvider.init(
            // msal configuration
        );

        $locationProvider.html5Mode(false).hashPrefix('');
    }]);
}
else {
    angular.module('todoApp', ['ui.router', 'MsalAngular'])
        .config(['$stateProvider', '$httpProvider', 'msalAuthenticationServiceProvider', '$locationProvider', function ($stateProvider, $httpProvider, msalProvider, $locationProvider) {
            $stateProvider.state("Home", {
                url: '/Home',
                controller: "homeCtrl",
                templateUrl: "/App/Views/Home.html",
            }).state("TodoList", {
                url: '/TodoList',
                controller: "todoListCtrl",
                templateUrl: "/App/Views/TodoList.html",
                requireLogin: true
            })

            $locationProvider.html5Mode(false).hashPrefix('');

            msalProvider.init(
                // msal configuration
            );
        }]);
}
```

W Angular: app.module.ts

```javascript
// Imports...
@NgModule({
  declarations: [
    AppComponent,
    MsalComponent,
    MainMenuComponent,
    AccountMenuComponent,
    OsNavComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    ServiceWorkerModule.register('ngsw-worker.js', { enabled: environment.production }),
    MsalModule.forRoot(environment.MsalConfig),
    SuiModule,
    PagesModule
  ],
  providers: [
    HttpServiceHelper,
    {provide: HTTP_INTERCEPTORS, useClass: MsalInterceptor, multi: true},
    AuthService
  ],
  entryComponents: [
    AppComponent,
    MsalComponent
  ]
})
export class AppModule {
  constructor() {
    console.log('APP Module Constructor!');
  }

  ngDoBootstrap(ref: ApplicationRef) {
    if (window !== window.parent && !window.opener)
    {
      console.log("Bootstrap: MSAL");
      ref.bootstrap(MsalComponent);
    }
    else
    {
    //this.router.resetConfig(RouterModule);
      console.log("Bootstrap: App");
      ref.bootstrap(AppComponent);
    }
  }
}
```

MsalComponent:

```javascript
import { Component} from '@angular/core';
import { MsalService } from '@azure/msal-angular';

// This component is used only to avoid Angular reload
// when doing acquireTokenSilent()

@Component({
  selector: 'app-root',
  template: '',
})
export class MsalComponent {
  constructor(private Msal: MsalService) {
  }
}
```

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [Kompilowanie aplikacji jednostronicowej (SPA)](scenario-spa-overview.md) przy użyciu MSAL.js.