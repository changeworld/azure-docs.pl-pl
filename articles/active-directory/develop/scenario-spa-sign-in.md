---
title: Jednostronicowe logowanie & logowania do aplikacji — platforma tożsamości firmy Microsoft | Azure
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159968"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Aplikacja jednostronicowa: Logowanie i wylogowywanie się

Dowiedz się, jak dodać logowanie do kodu aplikacji jednostronicowej.

Aby można było uzyskać tokeny dostępu do interfejsów API w aplikacji, należy uwierzytelniony kontekst użytkownika. Użytkownicy do aplikacji można zalogować na dwa sposoby:

* [Wyskakujące okno](#sign-in-with-a-pop-up-window) `loginPopup` , przy użyciu metody
* [Przekierowanie](#sign-in-with-redirect), `loginRedirect` przy użyciu metody

Opcjonalnie można również przekazać zakresy interfejsów API, dla których użytkownik musi wyrazić zgodę w momencie logowania.

> [!NOTE]
> Jeśli aplikacja ma już dostęp do uwierzytelnionego kontekstu użytkownika lub tokenu identyfikatora, można pominąć krok logowania i bezpośrednio uzyskać tokeny. Aby uzyskać szczegółowe informacje, zobacz [Logowanie sytowe bez logowania msal.js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Wybieranie między wyskakującym okienkiem lub przekierowaniem

Nie można używać zarówno wyskakujących i przekierowywania metod w aplikacji. Wybór między wyskakującym okienkiem lub przekierowaniem zależy od przepływu aplikacji:

* Jeśli nie chcesz, aby użytkownicy odeszli od głównej strony aplikacji podczas uwierzytelniania, zalecamy metodę wyskakunia. Ponieważ przekierowanie uwierzytelniania odbywa się w wyskakującym oknie, stan aplikacji głównej jest zachowywany.

* Jeśli użytkownicy mają ograniczenia przeglądarki lub zasady, w których okna podręczne są wyłączone, można użyć metody przekierowania. Użyj metody przekierowania w przeglądarce Internet Explorer, ponieważ istnieją [znane problemy z wyskakuwaniem okien w programie Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Logowanie się za pomocą wyskakujące okienka

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

Otoka kątowa MSAL umożliwia zabezpieczenie określonych tras w `MsalGuard` aplikacji przez dodanie do definicji trasy. Ta osłona wywoła metodę logowania się, gdy ta trasa jest dostępna.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Aby uzyskać wyskakujące `popUp` okno, włącz opcję konfiguracji. Można również przekazać zakresy, które wymagają zgody w następujący sposób:

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
---

## <a name="sign-in-with-redirect"></a>Logowanie się za pomocą przekierowania

# <a name="javascript"></a>[Javascript](#tab/javascript)

Metody przekierowania nie zwracają obietnicy z powodu odejścia od głównej aplikacji. Aby przetworzyć i uzyskać dostęp do zwróconych tokenów, należy zarejestrować wywołania zwrotne sukcesu i błędów przed wywołaniem metod przekierowania.

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

# <a name="angular"></a>[Angular](#tab/angular)

Kod w tym miejscu jest taki sam, jak opisano wcześniej w sekcji o logowanie się z wyskakującym oknie. Domyślnym przepływem jest przekierowanie.

> [!NOTE]
> Token identyfikatora nie zawiera zakresów zgody i reprezentuje tylko uwierzytelnionego użytkownika. Dozwolone zakresy są zwracane w tokenie dostępu, który zostanie zakupiony w następnym kroku.

---

## <a name="sign-out"></a>Wylogowywanie

Biblioteka MSAL `logout` zawiera metodę, która czyści pamięć podręczną w magazynie przeglądarki i wysyła żądanie wylogowania do usługi Azure Active Directory (Azure AD). Po wylogowaniu biblioteka domyślnie przekierowuje z powrotem do strony początkowej aplikacji.

Identyfikator URI, do którego powinien zostać przekierowany po `postLogoutRedirectUri`wylogowywaniu się, można skonfigurować ustawienie . Ten identyfikator URI powinien być również zarejestrowany jako identyfikator URI wylogowania w rejestracji aplikacji.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

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

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji](scenario-spa-acquire-token.md)
