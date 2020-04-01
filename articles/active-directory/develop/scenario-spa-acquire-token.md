---
title: Uzyskiwanie tokenu do wywoływania internetowego interfejsu API (aplikacji jednostronicowych) — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak utworzyć aplikację jednostronicową (uzyskać token do wywołania interfejsu API)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 393c3a06a2366a7d6947faf8bbfe038d6c5982fc
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419664"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Aplikacja jednostronicowa: uzyskaj token do wywoływania interfejsu API

Wzorzec do pozyskiwania tokenów dla interfejsów API z MSAL.js jest `acquireTokenSilent` najpierw spróbować cichego żądania tokenu przy użyciu metody. Gdy ta metoda jest wywoływana, biblioteka najpierw sprawdza pamięć podręczną w magazynie przeglądarki, aby sprawdzić, czy istnieje prawidłowy token i zwraca go. Gdy w pamięci podręcznej nie ma prawidłowego tokenu, wysyła on żądanie cichego tokenu do usługi Azure Active Directory (Azure AD) z ukrytego elementu iframe. Ta metoda umożliwia również biblioteki do odnawiania tokenów. Aby uzyskać więcej informacji na temat sesji logowania jednokrotnego i wartości okresu istnienia tokenu w usłudze Azure AD, zobacz [Okresy istnienia tokenu.](active-directory-configurable-token-lifetimes.md)

Żądania cichego tokenu do usługi Azure AD mogą zakończyć się niepowodzeniem z powodów, takich jak wygasła sesja usługi Azure AD lub zmiana hasła. W takim przypadku można wywołać jedną z interaktywnych metod (która poprosi użytkownika) o uzyskanie tokenów:

* [Wyskakujące okno](#acquire-a-token-with-a-pop-up-window), za pomocą`acquireTokenPopup`
* [Przekierowanie](#acquire-a-token-with-a-redirect), za pomocą`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Wybierz między wyskakującym okienkiem lub przekierowaniem

 Nie można używać zarówno wyskakujących i przekierowywania metod w aplikacji. Wybór między wyskakującym okienkiem lub przekierowaniem zależy od przepływu aplikacji:

* Jeśli nie chcesz, aby użytkownicy odeszli od głównej strony aplikacji podczas uwierzytelniania, zaleciliśmy metodę wyskakunia. Ponieważ przekierowanie uwierzytelniania odbywa się w wyskakującym oknie, stan aplikacji głównej jest zachowywany.

* Jeśli użytkownicy mają ograniczenia przeglądarki lub zasady, w których okna podręczne są wyłączone, można użyć metody przekierowania. Użyj metody przekierowania w przeglądarce Internet Explorer, ponieważ istnieją [znane problemy z wyskakuwaniem okien w programie Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Można ustawić zakresy interfejsu API, które mają token dostępu do uwzględnienia podczas tworzenia żądania tokenu dostępu. Należy zauważyć, że wszystkie żądane zakresy mogą nie być przyznane w tokenie dostępu. To zależy od zgody użytkownika.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Uzyskiwanie tokenu z wyskakującym oknem

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy kod łączy wcześniej opisany wzorzec z metodami wyskakujących wyskakujących pojęć:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

Otoka kątowa MSAL udostępnia interceptor HTTP, który automatycznie uzyskuje tokeny dostępu w trybie dyskretnym i dołącza je do żądań HTTP do interfejsów API.

Zakresy interfejsów API można określić `protectedResourceMap` w opcji konfiguracji. `MsalInterceptor`zażąda tych zakresów podczas automatycznego pozyskiwania tokenów.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Dla powodzenia i niepowodzenia nabycia cichego tokenu MSAL Angular zapewnia wywołania zwrotne, które można subskrybować. Ważne jest również, aby pamiętać o rezygnacji z subskrypcji.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternatywnie można jawnie uzyskać tokeny przy użyciu metod pozyskiwania tokenu, zgodnie z opisem w podstawowej bibliotece MSAL.js.

---

## <a name="acquire-a-token-with-a-redirect"></a>Zdobądź token z przekierowaniem

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy wzorzec jest opisany wcześniej, ale pokazano za pomocą metody przekierowania do nabycia tokenów interaktywnie. Musisz zarejestrować wywołanie zwrotne przekierowania, jak wspomniano wcześniej.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Żądanie oświadczeń opcjonalnych

Oświadczenia opcjonalne można używać do następujących celów:

- Dołącz dodatkowe oświadczenia w tokenach dla aplikacji.
- Zmień zachowanie niektórych oświadczeń, które usługa Azure AD zwraca w tokenach.
- Dodawanie i uzyskiwanie dostępu do oświadczeń niestandardowych dla aplikacji.

Aby zażądać `IdToken`opcjonalnych oświadczeń w programie , `claimsRequest` można `AuthenticationParameters.ts` wysłać obiekt oświadczeń ciągnionych do pola klasy.

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Aby dowiedzieć się więcej, zobacz [Oświadczenia opcjonalne](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Ten kod jest taki sam, jak opisano wcześniej.

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie internetowego interfejsu API](scenario-spa-call-api.md)
