---
title: Unikaj ponownego ładowania stron (Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript) | Azure
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
ms.openlocfilehash: c382c78cf631def74272768b78ee489e49820d04
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532836"
---
# <a name="avoid-page-reloads-when-acquiring-and-renewing-tokens-silently-using-msaljs"></a>Unikaj ponownego ładowania stron podczas uzyskiwania i odnawiania tokenów dyskretnie przy użyciu MSAL. js
Biblioteka Microsoft Authentication Library for JavaScript (MSAL. js) używa `iframe` ukrytych elementów w celu pozyskania i odnawiania tokenów dyskretnie w tle. Usługa Azure AD zwraca token z zarejestrowanego elementu redirect_uri określonego w żądaniu tokenu (domyślnie jest to Strona główna aplikacji). Ponieważ odpowiedź jest 302, wynikiem jest kod HTML odpowiadający `redirect_uri` wczytywaniu `iframe`w. `redirect_uri` Zwykle jest to Strona główna, która powoduje ponowne załadowanie.

W innych przypadkach, jeśli przechodzenie do strony głównej aplikacji wymaga uwierzytelnienia, może to prowadzić do zagnieżdżonych `iframe` elementów lub `X-Frame-Options: deny` błędu.

Ponieważ MSAL. js nie może odrzucić 302 wystawionych przez usługę Azure AD i jest wymagany do przetworzenia zwróconego tokenu `redirect_uri` , nie można zapobiec załadowaniu go `iframe`w.

Aby uniknąć ponownego ładowania całej aplikacji lub innych błędów spowodowanych tym problemem, należy postępować zgodnie z poniższymi obejściami.

## <a name="specify-different-html-for-the-iframe"></a>Określ inny kod HTML dla elementu IFRAME

`redirect_uri` Ustaw właściwość konfiguracji na prostą stronę, która nie wymaga uwierzytelniania. Musisz się upewnić, że jest on zgodny z `redirect_uri` zarejestrowaną w Azure Portal. Nie wpłynie to na środowisko logowania użytkownika, ponieważ MSAL zapisuje stronę początkową, gdy użytkownik rozpocznie proces logowania i przekierowuje się z powrotem do dokładnej lokalizacji po zakończeniu logowania.

## <a name="initialization-in-your-main-app-file"></a>Inicjowanie w głównym pliku aplikacji

Jeśli aplikacja jest strukturalna w taki sposób, że istnieje jeden centralny plik języka JavaScript, który definiuje inicjalizację, Routing i inne elementy aplikacji, można warunkowo załadować moduły aplikacji w zależności od tego, czy aplikacja jest ładowana `iframe` w programie, czy nie. Przykład:

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