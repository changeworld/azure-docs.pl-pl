---
title: Aplikacja jednostronicowa (Uzyskiwanie tokenu do wywoływania interfejsu API) — platforma tożsamości firmy Microsoft
description: Dowiedz się, jak utworzyć aplikację jednostronicową (uzyskać token, aby wywołać interfejs API)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135709"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Aplikacja jednostronicowa — uzyskiwanie tokenu do wywoływania interfejsu API

Wzorcem uzyskiwania tokenów dla interfejsów API z MSAL. js jest najpierw próba żądania tokenu dyskretnego przy użyciu `acquireTokenSilent` metody. Po wywołaniu tej metody Biblioteka najpierw sprawdza pamięć podręczną w magazynie przeglądarki, aby sprawdzić, czy istnieje prawidłowy token i zwraca go. Gdy w pamięci podręcznej nie ma prawidłowego tokenu, wysyła żądanie tokenu dyskretnego do Azure Active Directory (Azure AD) z ukrytego elementu iframe. Ta metoda umożliwia również bibliotece odnawianie tokenów. Aby uzyskać więcej informacji na temat sesji logowania jednokrotnego i okresu istnienia tokenu w usłudze Azure AD, zobacz [okresy istnienia tokenu](active-directory-configurable-token-lifetimes.md).

Żądania tokenu dyskretnego do usługi Azure AD mogą się nie powieść z pewnych powodów, takich jak wygasła sesja usługi Azure AD lub zmiana hasła. W takim przypadku można wywołać jedną z metod interaktywnych (co spowoduje wyświetlenie monitu użytkownika) o uzyskanie tokenów.

* [Uzyskiwanie tokenu za pomocą okna](#acquire-token-with-a-pop-up-window) podręcznego przy użyciu`acquireTokenPopup`
* [Pozyskiwanie tokenu z](#acquire-token-with-redirect) przekierowaniami przy użyciu`acquireTokenRedirect`

**Wybieranie między podręcznym i przekierowaniami**

 Nie można użyć kombinacji podmenu podręcznych i metod przekierowania w aplikacji. Wybór między podręcznym i przekierowaniami zależy od przepływu aplikacji.

* Jeśli nie chcesz, aby użytkownik przechodził do strony głównej aplikacji podczas uwierzytelniania, zaleca się korzystanie z wyskakujących metod. Ponieważ przekierowanie uwierzytelniania odbywa się w oknie podręcznym, stan głównej aplikacji jest zachowywany.

* Istnieją pewne przypadki, w których może być konieczne użycie metod przekierowania. Jeśli użytkownicy aplikacji mają ograniczenia przeglądarki lub zasady, w których okna wyskakujące są wyłączone, można użyć metod przekierowania. Zaleca się również korzystanie z metod przekierowania z przeglądarką Internet Explorer, ponieważ występują pewne [znane problemy z programem Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) podczas obsługi wyskakujących okienek.

Można ustawić zakresy interfejsów API, które mają być uwzględniane przez token dostępu podczas kompilowania żądania tokenu dostępu. Należy zauważyć, że wszystkie żądane zakresy nie mogą być przyznawane w tokenie dostępu i są zależne od zgody użytkownika.

## <a name="acquire-token-with-a-pop-up-window"></a>Uzyskiwanie tokenu za pomocą okna podręcznego

### <a name="javascript"></a>JavaScript

Powyższy wzorzec przy użyciu metod dla wyskakujących okienek:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
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

### <a name="angular"></a>Angular

Otoka kątowa MSAL zapewnia wygodę dodawania interceptora HTTP, która automatycznie uzyskuje tokeny dostępu w trybie dyskretnym i dołącza je do żądań HTTP do interfejsów API.

W `protectedResourceMap` opcji konfiguracji można określić zakresy interfejsów API, które MsalInterceptor będą żądać podczas automatycznego uzyskiwania tokenów.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

W przypadku powodzenia i niepowodzenia pozyskiwania tokenów dyskretnych MSAL kątowy zapewnia wywołania zwrotne, które można subskrybować. Ważne jest również, aby pamiętać o anulowaniu subskrypcji.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Alternatywnie można również jawnie uzyskać tokeny przy użyciu metod uzyskiwania tokenu zgodnie z opisem w podstawowej bibliotece MSAL. js.

## <a name="acquire-token-with-redirect"></a>Uzyskiwanie tokenu przy użyciu przekierowania

### <a name="javascript"></a>JavaScript

Wzorzec jest tak jak opisano powyżej, ale przedstawia metodę Redirect, aby interaktywnie uzyskać token. Należy zarejestrować wywołanie zwrotne przekierowania, jak wspomniano powyżej.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-for-optional-claims"></a>Żądanie dotyczące opcjonalnych oświadczeń
Możesz zażądać opcjonalnych oświadczeń w aplikacji, aby określić, które dodatkowe oświadczenia mają być zawarte w tokenach aplikacji. Aby zażądać opcjonalnych oświadczeń w id_token, można wysłać obiekt oświadczeń skonwertowanej do pola claimsRequest klasy AuthenticationParameters. TS.

Możesz użyć opcjonalnych oświadczeń do następujących celów:

- W celu uwzględnienia dodatkowych oświadczeń w tokenach aplikacji.
- Zmień zachowanie niektórych oświadczeń zwracanych przez usługę Azure AD w tokenach.
- Dodawanie niestandardowych oświadczeń do aplikacji i uzyskiwanie do nich dostępu.


### <a name="javascript"></a>JavaScript
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
Aby dowiedzieć się więcej na temat opcjonalnych oświadczeń, Wyewidencjonuj [opcjonalne oświadczenia](active-directory-optional-claims.md)


### <a name="angular"></a>Angular

Jest to takie samo, jak opisano powyżej.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu API sieci Web](scenario-spa-call-api.md)
