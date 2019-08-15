---
title: Aplikacja jednostronicowa (logowanie) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację jednostronicową (logowanie)
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
ms.openlocfilehash: 7bf614a5523e78fc72918db973ef8d738a171fff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031781"
---
# <a name="single-page-application---sign-in"></a>Jednostronicowa aplikacja — logowanie

Dowiedz się, jak dodać logowanie do kodu dla aplikacji jednostronicowej.

Aby można było uzyskać tokeny dostępu do interfejsów API w aplikacji, wymagany jest kontekst uwierzytelnionego użytkownika. Użytkowników można zalogować do aplikacji w MSAL. js na dwa sposoby:

* [Logowanie za pomocą okna](#sign-in-with-a-pop-up-window) podręcznego przy użyciu `loginPopup` metody
* [Logowanie przy](#sign-in-with-redirect) użyciu `loginRedirect` metody redirect

Opcjonalnie można również przekazać zakresy interfejsów API, dla których użytkownik musi wyrazić zgodę w momencie logowania.

> [!NOTE]
> Jeśli aplikacja ma już dostęp do kontekstu uwierzytelnionego użytkownika lub tokenu identyfikatora, można pominąć krok logowania i bezpośrednio uzyskać tokeny. Aby uzyskać więcej informacji, zobacz [Logowanie jednokrotne bez logowania msal. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Wybieranie między podręcznym i przekierowaniami

Nie można użyć kombinacji podmenu podręcznych i metod przekierowania w aplikacji. Wybór między podręcznym i przekierowaniami zależy od przepływu aplikacji.

* Jeśli nie chcesz, aby użytkownik przechodził do strony głównej aplikacji podczas uwierzytelniania, zaleca się korzystanie z wyskakujących metod. Ponieważ przekierowanie uwierzytelniania odbywa się w oknie podręcznym, stan głównej aplikacji jest zachowywany.

* Istnieją pewne przypadki, w których może być konieczne użycie metod przekierowania. Jeśli użytkownicy aplikacji mają ograniczenia przeglądarki lub zasady, w których okna wyskakujące są wyłączone, można użyć metod przekierowania. Użyj metod przekierowania z przeglądarką Internet Explorer, ponieważ występują pewne [znane problemy z programem Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) podczas obsługi wyskakujących okienek.

## <a name="sign-in-with-a-pop-up-window"></a>Zaloguj się przy użyciu okna podręcznego

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
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

Otoka kątowa MSAL umożliwia Zabezpieczanie określonych tras w aplikacji przez jedynie dodanie `MsalGuard` do definicji trasy. Ta funkcja Guard wywoła metodę w celu zalogowania się, gdy zostanie uzyskany dostęp do tej trasy.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

W przypadku okna podręcznego Włącz `popUp` opcję konfiguracji. Możesz również przekazać zakresy, które wymagają zgody w następujący sposób:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Logowanie przy użyciu przekierowania

### <a name="javascript"></a>JavaScript

Metody przekierowania nie zwracają obietnicy ze względu na nawigację z głównej aplikacji. Aby przetworzyć zwrócone tokeny i uzyskać do nich dostęp, należy zarejestrować wywołania zwrotne sukcesu i błędów przed wywołaniem metod przekierowania.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

Kod jest taki sam, jak opisano powyżej, w sekcji Zaloguj się przy użyciu okna podręcznego. Domyślny przepływ jest przekierowywany.

> [!NOTE]
> Token identyfikatora nie zawiera zgodnych zakresów i reprezentuje tylko uwierzytelnionego użytkownika. Wysłane zakresy są zwracane w tokenie dostępu, który zostanie pozyskany w następnym kroku.

## <a name="sign-out"></a>Wyloguj

Biblioteka MSAL zapewnia `logout` metodę, która spowoduje wyczyszczenie pamięci podręcznej w magazynie przeglądarki i wysłanie żądania wylogowania do usługi Azure AD. Po wylogowaniu następuje przekierowanie z powrotem do strony początkowej aplikacji.

Można skonfigurować identyfikator URI, do którego ma zostać przekierowany po wylogowaniu, ustawiając `postLogoutRedirectUri`. Ten identyfikator URI powinien być również zarejestrowany jako identyfikator URI wylogowania w rejestracji aplikacji.

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

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji](scenario-spa-acquire-token.md)
