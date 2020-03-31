---
title: Unikaj ponownego ładowania strony (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak uniknąć ponownego ładowania strony podczas pomijania i odnawiania tokenów w trybie dyskretnym przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/29/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 63944a5a9af34c2d4cf98eeb870a730df49654e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084959"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Unikaj ponownego ładowania strony podczas pomijania i odnawiania tokenów po cichu przy użyciu pliku MSAL.js
Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript `iframe` (MSAL.js) używa ukrytych elementów do uzyskiwania i odnawiania tokenów w trybie cichym w tle. Usługa Azure AD zwraca token z powrotem do zarejestrowanego redirect_uri określony w żądaniu tokenu (domyślnie jest to strona główna aplikacji). Ponieważ odpowiedź jest 302, powoduje HTML odpowiadające `redirect_uri` coraz załadowany w `iframe`. Zazwyczaj aplikacja `redirect_uri` jest stroną główną, co powoduje, że do ponownego załadowania.

W innych przypadkach, jeśli przejście do strony głównej aplikacji wymaga uwierzytelnienia, może to prowadzić do zagnieżdżonych `iframe` elementów lub `X-Frame-Options: deny` błędu.

Ponieważ msal.js nie można odrzucić 302 wydane przez usługę Azure AD i `redirect_uri` jest wymagane do `iframe`przetwarzania zwróconego tokenu, nie można zapobiec ładowaniu w .

Aby uniknąć ponownego ładowania całej aplikacji lub innych błędów spowodowanych z tego powodu, wykonaj te obejścia.

## <a name="specify-different-html-for-the-iframe"></a>Określanie innego kodu HTML dla elementu iframe

Ustaw `redirect_uri` właściwość na config na prostą stronę, która nie wymaga uwierzytelniania. Musisz upewnić się, że `redirect_uri` jest zgodny z zarejestrowanym w witrynie Azure portal. Nie wpłynie to na proces logowania użytkownika, ponieważ MSAL zapisuje stronę początkową, gdy użytkownik rozpoczyna proces logowania i przekierowuje z powrotem do dokładnej lokalizacji po zakończeniu logowania.

## <a name="initialization-in-your-main-app-file"></a>Inicjowanie w głównym pliku aplikacji

Jeśli aplikacja jest skonstruowana w taki sposób, że istnieje jeden centralny plik Javascript, który definiuje inicjowanie aplikacji, routing i inne rzeczy, można warunkowo załadować moduły aplikacji na podstawie tego, czy aplikacja jest ładowanie w `iframe` lub nie. Przykład:

W AngularJS: app.js

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

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [tworzeniu aplikacji jednostronicowej (SPA)](scenario-spa-overview.md) przy użyciu pliku MSAL.js.