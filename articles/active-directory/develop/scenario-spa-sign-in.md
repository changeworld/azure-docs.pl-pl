---
title: Aplikacja jednostronicowa (logowanie) — Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji jednostronicowych (Zaloguj się)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138803"
---
# <a name="single-page-application---sign-in"></a>Aplikacja jednostronicowa — logowanie

Dowiedz się, jak dodać logowanie do kodu aplikacji jednostronicowej.

Zanim będzie możliwe uzyskanie tokenów dostęp do interfejsów API w aplikacji, konieczne będzie uprawnieniami uwierzytelnionego użytkownika. Można logowania użytkowników do aplikacji w MSAL.js na dwa sposoby:

* [Zaloguj się przy użyciu wyskakującego okienka](#sign-in-with-a-pop-up-window) przy użyciu `loginPopup` — metoda
* [Zaloguj się przy użyciu przekierowania](#sign-in-with-redirect) przy użyciu `loginRedirect` — metoda

Można również przekazać zakresy interfejsów API, dla którego trzeba posiadać użytkownika o zgodę podczas logowania.

> [!NOTE]
> Jeśli aplikacja już ma dostęp do kontekstu użytkownika uwierzytelnionego lub identyfikator tokenu, można pominąć ten krok logowania i bezpośrednio uzyskać tokeny. Aby uzyskać więcej informacji, zobacz [logowania jednokrotnego bez logowania msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Wybieranie między okno podręczne lub przekierowania doświadczenia

Nie można użyć kombinacji metod okno podręczne i Przekierowanie do aplikacji. Wybór między okno podręczne lub przekierowania doświadczenia zależy od przepływu aplikacji.

* Jeśli nie chcesz, aby użytkownik, który chcesz wyjść strony głównej aplikacji podczas uwierzytelniania, zaleca się używanie metod wyskakujących. Ponieważ przekierowania uwierzytelniania odbywa się w oknie podręcznym, stan aplikacji głównej jest zachowywana.

* Istnieją niektórych przypadki, w której użytkownik może być konieczne użycie metody przekierowania. Jeśli użytkownicy aplikacji mają ograniczenia przeglądarki lub zasady gdzie wyskakujące są wyłączone, można użyć metody przekierowania. Za pomocą metody przekierowanie przeglądarki Internet Explorer, ponieważ istnieją pewne [znane problemy z programem Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) podczas obsługi wyskakujących okienek.

## <a name="sign-in-with-a-pop-up-window"></a>Zaloguj się za pomocą okna podręcznego

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

Biblioteka MSAL Angular otoki umożliwia zabezpieczenie określonej trasy w aplikacji, po prostu dodając `MsalGuard` do definicji trasy. Ta ochrona wywoła metodę logowania podczas uzyskiwania dostępu do tej trasy.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Okno podręczne środowisko pracy, należy włączyć `popUp` opcji konfiguracji. Można również przekazać zakresów, które wymagają zgody w następujący sposób:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Zaloguj się przy użyciu przekierowania

### <a name="javascript"></a>JavaScript

Metody przekierowania nie zwracają obietnicą ze względu na nawigacji od głównej aplikacji. Do przetwarzania i zwrócony tokeny dostępu, należy zarejestrować sukcesach i błędach wywołania zwrotne przed wywołaniem metody przekierowania.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Kod w tym miejscu jest taka sama, jak opisano powyżej, w obszarze Zaloguj się przy użyciu sekcji oknie podręcznym. Przepływ domyślny jest przekierowania.

> [!NOTE]
> Identyfikator tokenu nie zawiera zatwierdzonej zakresy i tylko reprezentuje uwierzytelnionego użytkownika. Które wyrażono zgodę zakresy są zwracane w tokenie dostępu, który będzie pobrać w kolejnym kroku.

## <a name="sign-out"></a>Wyloguj

Biblioteka MSAL zapewnia `logout` metodę, która spowoduje wyczyszczenie pamięci podręcznej w magazynie przeglądarki, a następnie wysyła Wyloguj żądania do usługi Azure AD. Po wylogowaniu się zostanie przekierowany do strony początkowej aplikacji domyślnie.

Można skonfigurować identyfikator URI, do którego powinien spowodować przekierowanie po znaku na zewnątrz, ustawiając `postLogoutRedirectUri`. Ten identyfikator URI powinien być zarejestrowany również jako identyfikator URI wylogowania w Twojej rejestracji aplikacji.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji](scenario-spa-acquire-token.md)
