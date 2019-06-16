---
title: Aplikacja jednostronicowa (Uzyskiwanie tokenu służącego do wywoływania interfejsu API) — Platforma tożsamości firmy Microsoft
description: Informacje o sposobie tworzenia aplikacji jednostronicowych (Uzyskaj token do wywołania interfejsu API)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65138813"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Aplikacja jednostronicowa — uzyskiwanie tokenu służącego do wywoływania interfejsu API

Wzorzec uzyskiwania tokenów dla interfejsów API za pomocą MSAL.js jest, aby najpierw spróbować dyskretnej żądania tokenu przy użyciu `acquireTokenSilent` metody. Gdy ta metoda jest wywoływana, biblioteki najpierw sprawdza, czy pamięci podręcznej w magazynie przeglądarki, aby zobaczyć, czy istnieje prawidłowy token i zwraca go. Gdy w pamięci podręcznej jest nie prawidłowy token, wysyła dyskretnej żądania tokenu do usługi Azure Active Directory (Azure AD) z ukrytym elemencie iframe. Ta metoda umożliwia również biblioteki odnowić tokenów. Aby uzyskać więcej informacji o sesji rejestracji jednokrotnej i wartości okres istnienia tokenu w usłudze Azure AD, zobacz [czas życia tokenu](active-directory-configurable-token-lifetimes.md).

Dyskretnej żądania tokenu do usługi Azure AD może zakończyć się niepowodzeniem dla przyczyn, takich jak Azure wygasłych sesji usługi AD i zmianę hasła. W takim przypadku możesz wywołać jedną z metod interaktywnych, (które zostanie wyświetlony monit) można uzyskać tokenów.

* [Uzyskanie tokenu za pomocą okna podręcznego](#acquire-token-with-a-pop-up-window) przy użyciu `acquireTokenPopup`
* [Uzyskanie tokenu przy użyciu przekierowania](#acquire-token-with-redirect) przy użyciu `acquireTokenRedirect`

**Wybieranie między okno podręczne lub przekierowania doświadczenia**

 Nie można użyć kombinacji metod okno podręczne i Przekierowanie do aplikacji. Wybór między okno podręczne lub przekierowania doświadczenia zależy od przepływu aplikacji.

* Jeśli nie chcesz, aby użytkownik, który chcesz wyjść strony głównej aplikacji podczas uwierzytelniania, zaleca się używanie metod wyskakujących. Ponieważ przekierowania uwierzytelniania odbywa się w oknie podręcznym, stan aplikacji głównej jest zachowywana.

* Istnieją niektórych przypadki, w której użytkownik może być konieczne użycie metody przekierowania. Jeśli użytkownicy aplikacji mają ograniczenia przeglądarki lub zasady gdzie wyskakujące są wyłączone, można użyć metody przekierowania. Zalecane jest również użycie metod przekierowania z przeglądarką Internet Explorer, ponieważ istnieją pewne [znane problemy z programem Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) podczas obsługi wyskakujących okienek.

Można ustawić zakresy interfejsu API, które token dostępu do uwzględnienia podczas tworzenia żądania tokenu dostępu. Pamiętaj, że wszystkie żądane zakresy nie może być przyznane w tokenie dostępu zależy od zgody użytkownika.

## <a name="acquire-token-with-a-pop-up-window"></a>Uzyskanie tokenu za pomocą okna podręcznego

### <a name="javascript"></a>JavaScript

Wzorzec powyżej, przy użyciu metod wyskakującego środowiska:

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

Biblioteka MSAL Angular otoki zapewnia wygodę, dodawania HTTP interceptor `MsalInterceptor` co spowoduje automatyczne uzyskania tokenów dostępu w trybie dyskretnym i dołącz je do żądania HTTP do interfejsów API.

Można określić zakresy dla interfejsów API w `protectedResourceMap` opcji konfiguracji, która MsalInterceptor zażąda podczas automatycznego uzyskiwania tokenów.

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

W przypadku sukcesów i niepowodzeń dyskretnej uzyskanie tokenu MSAL Angular zapewnia wywołania zwrotne, które mogą subskrybować. Jest również pamiętać anulować subskrypcję.

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

Alternatywnie mogą również jawnie uzyskiwać tokeny przy użyciu metody pobierania tokenu, zgodnie z opisem w podstawowej biblioteki MSAL.js.

## <a name="acquire-token-with-redirect"></a>Uzyskanie tokenu przy użyciu przekierowania

### <a name="javascript"></a>JavaScript

Wzorzec jest jak opisano powyżej ale pokazanych za pomocą metody przekierowania można uzyskać tokenu interaktywnie. Należy pamiętać, że konieczne będzie zarejestrowanie wywołania zwrotnego przekierowanie, jak wspomniano powyżej.

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

### <a name="angular"></a>Angular

To jest taka sama, jak opisano powyżej.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wywoływanie interfejsu Web API](scenario-spa-call-api.md)
