---
title: Unikaj ponownego ładowania stron (Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak uniknąć ponownego ładowania strony podczas uzyskiwania i odnawiania tokenów w trybie dyskretnym przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/29/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29edafdc27a3835653f82ec36d576a4871e66155
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803108"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Unikaj ponownego ładowania stron podczas uzyskiwania i odnawiania tokenów dyskretnie przy użyciu MSAL. js
Biblioteka Microsoft Authentication Library for JavaScript (MSAL. js) używa ukrytych elementów `iframe` do uzyskiwania i odnawiania tokenów w trybie dyskretnym w tle. Usługa Azure AD zwraca token z zarejestrowanego elementu redirect_uri określonego w żądaniu tokenu (domyślnie jest to Strona główna aplikacji). Ponieważ odpowiedzią jest 302, wynikiem jest kod HTML odpowiadający `redirect_uri` wczytywanie w `iframe`. Zwykle `redirect_uri` aplikacji jest stroną główną i spowoduje to ponowne załadowanie.

W innych przypadkach, jeśli przechodzenie do strony głównej aplikacji wymaga uwierzytelnienia, może to prowadzić do zagnieżdżenia `iframe` elementów lub `X-Frame-Options: deny` błędu.

Ponieważ MSAL. js nie może odrzucić 302 wystawionych przez usługę Azure AD i jest wymagany do przetworzenia zwróconego tokenu, nie może uniemożliwiać załadowania `redirect_uri` w `iframe`.

Aby uniknąć ponownego ładowania całej aplikacji lub innych błędów spowodowanych tym problemem, należy postępować zgodnie z poniższymi obejściami.

## <a name="specify-different-html-for-the-iframe"></a>Określ inny kod HTML dla elementu IFRAME

Ustaw właściwość `redirect_uri` w pliku config na prostą stronę, która nie wymaga uwierzytelniania. Należy upewnić się, że jest on zgodny z `redirect_uri` zarejestrowanymi w Azure Portal. Nie wpłynie to na środowisko logowania użytkownika, ponieważ MSAL zapisuje stronę początkową, gdy użytkownik rozpocznie proces logowania i przekierowuje się z powrotem do dokładnej lokalizacji po zakończeniu logowania.

## <a name="initialization-in-your-main-app-file"></a>Inicjowanie w głównym pliku aplikacji

Jeśli aplikacja jest strukturalna w taki sposób, że istnieje jeden centralny plik języka JavaScript, który definiuje inicjalizację, Routing i inne elementy aplikacji, można warunkowo załadować moduły aplikacji w zależności od tego, czy aplikacja jest ładowana w `iframe`, czy nie. Na przykład:

W AngularJS: App. js

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

W elemencie kątowym: App. module. TS

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
Dowiedz się więcej o [tworzeniu aplikacji jednostronicowej (Spa)](scenario-spa-overview.md) przy użyciu MSAL. js.