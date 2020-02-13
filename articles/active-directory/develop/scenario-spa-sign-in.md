---
title: Logowanie jednostronicowej aplikacji & logowania — platforma tożsamości firmy Microsoft | Azure
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
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159968"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Aplikacja jednostronicowa: Logowanie i wylogowywanie

Dowiedz się, jak dodać logowanie do kodu dla aplikacji jednostronicowej.

Aby uzyskać tokeny umożliwiające dostęp do interfejsów API w aplikacji, wymagany jest kontekst uwierzytelnionego użytkownika. Użytkowników można zalogować do aplikacji w MSAL. js na dwa sposoby:

* [Okno podręczne](#sign-in-with-a-pop-up-window), przy użyciu metody `loginPopup`
* [Przekierowywanie](#sign-in-with-redirect)przy użyciu metody `loginRedirect`

Opcjonalnie można również przekazać zakresy interfejsów API, dla których użytkownik musi wyrazić zgodę w czasie logowania.

> [!NOTE]
> Jeśli aplikacja ma już dostęp do kontekstu uwierzytelnionego użytkownika lub tokenu identyfikatora, można pominąć krok logowania i bezpośrednio uzyskać tokeny. Aby uzyskać szczegółowe informacje, zobacz [Logowanie jednokrotne bez logowania MSAL. js](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Wybieranie między podręcznym i przekierowaniami

W aplikacji nie można używać obu metod podręcznych i przekierowania. Wybór między podręcznym i przekierowaniami zależy od przepływu aplikacji:

* Jeśli nie chcesz, aby użytkownicy przechodzą poza główną stronę aplikacji podczas uwierzytelniania, zalecamy wyskakującą metodę. Ponieważ przekierowanie uwierzytelniania odbywa się w oknie podręcznym, stan głównej aplikacji jest zachowywany.

* Jeśli użytkownicy mają ograniczenia przeglądarki lub zasady, w których wyskakujące okienka są wyłączone, można użyć metody redirect. Użyj metody redirect w przeglądarce Internet Explorer, ponieważ występują [znane problemy z wyskakującymi oknami w programie Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Logowanie za pomocą okna podręcznego

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

Otoka kątowa MSAL umożliwia Zabezpieczanie określonych tras w aplikacji przez dodanie `MsalGuard` do definicji trasy. Ta funkcja Guard wywoła metodę w celu zalogowania się, gdy zostanie uzyskany dostęp do tej trasy.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

W przypadku okna podręcznego Włącz opcję konfiguracji `popUp`. Możesz również przekazać zakresy, które wymagają zgody w następujący sposób:

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

## <a name="sign-in-with-redirect"></a>Logowanie przy użyciu przekierowania

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

Metody przekierowania nie zwracają obietnicy ze względu na przejście z głównej aplikacji. Aby przetworzyć zwrócone tokeny i uzyskać do nich dostęp, należy zarejestrować wywołania zwrotne sukcesu i błędów przed wywołaniem metod przekierowania.

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

Kod jest taki sam, jak opisano wcześniej w sekcji dotyczącej logowania przy użyciu okna podręcznego. Domyślny przepływ jest przekierowywany.

> [!NOTE]
> Token identyfikatora nie zawiera zgodnych zakresów i reprezentuje tylko uwierzytelnionego użytkownika. Wysłane zakresy są zwracane w tokenie dostępu, który zostanie pozyskany w następnym kroku.

---

## <a name="sign-out"></a>Wylogowywanie

Biblioteka MSAL zapewnia metodę `logout`, która czyści pamięć podręczną w magazynie przeglądarki i wysyła żądanie wylogowania do Azure Active Directory (Azure AD). Po wylogowaniu Biblioteka domyślnie przekierowuje do strony początkowej aplikacji.

Można skonfigurować identyfikator URI, do którego ma zostać przekierowany po wylogowaniu, ustawiając `postLogoutRedirectUri`. Ten identyfikator URI powinien być również zarejestrowany jako identyfikator URI wylogowania w rejestracji aplikacji.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angulartabangular"></a>[Angular](#tab/angular)

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
