---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 5fe9fe8ced675f68161f0df9f2665b47f9d47ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183742"
---
### <a name="how-to-authenticate-with-a-provider-server-flow"></a><a name="server-auth"></a>Instrukcje: uwierzytelnianie za pomocą dostawcy (przepływ serwera)
Aby usługa Mobile Apps zarządzała procesem uwierzytelniania w aplikacji, musisz zarejestrować swoją aplikację u dostawcy tożsamości. Następnie w usłudze Azure App Service musisz skonfigurować identyfikator aplikacji oraz wpis tajny udostępniony przez dostawcę.
Aby uzyskać więcej informacji, zapoznaj się z samouczkiem [Dodawanie uwierzytelniania do aplikacji](../articles/app-service-mobile/app-service-mobile-cordova-get-started-users.md).

Po zarejestrowaniu dostawcy tożsamości wywołaj metodę `.login()` z nazwą dostawcy. Aby na przykład zalogować się za pomocą Facebooka, użyj następującego kodu:

```javascript
client.login("facebook").done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Prawidłowe wartości dla dostawcy to „aad”, „facebook”, „google”, „microsoftaccount” oraz „twitter”.

> [!NOTE]
> Obecnie uwierzytelnianie za pomocą konta Google nie działa za pośrednictwem przepływu serwera.  Aby uwierzytelnić się za pomocą konta Google, musisz użyć [metody przepływu klienta](#client-auth).

W tym przypadku usługa Azure App Service zarządza przepływem uwierzytelniania OAuth 2.0.  Wyświetla stronę logowania wybranego dostawcy i generuje token uwierzytelniania usługi app service po pomyślnym zalogowaniu się u dostawcy tożsamości. Po zakończeniu swojego działania funkcja logowania zwraca obiekt JSON, który udostępnia zarówno identyfikator użytkownika, jak i token uwierzytelniania usługi App Service, odpowiednio w polach userId oraz authenticationToken. Ten token można zapisać w pamięci podręcznej i ponownie go używać, dopóki nie wygaśnie.

### <a name="how-to-authenticate-with-a-provider-client-flow"></a><a name="client-auth"></a>Instrukcje: uwierzytelnianie za pomocą dostawcy (przepływ klienta)

Aplikacja może również niezależnie skontaktować się z dostawcą tożsamości, a następnie udostępnić zwrócony token usłudze App Service na potrzeby uwierzytelniania. Ten przepływ klienta pozwala zapewnić środowisko logowania jednokrotnego dla użytkowników bądź pobrać dodatkowe dane użytkownika od dostawcy tożsamości.

#### <a name="social-authentication-basic-example"></a>Podstawowy przykład uwierzytelniania przy użyciu sieci społecznościowych

W tym przykładzie na potrzeby uwierzytelniania użyto zestawu SDK klienta usługi Facebook:

```javascript
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now signed in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});

```
W tym przykładzie założono, że token udostępniony przez zestaw SDK danego dostawcy jest przechowywany w zmiennej token.

### <a name="how-to-obtain-information-about-the-authenticated-user"></a><a name="auth-getinfo"></a>Instrukcje: pozyskiwanie informacji o uwierzytelnionym użytkowniku

Dane uwierzytelniania można pobrać z punktu końcowego `/.auth/me` przy użyciu wywołania HTTP z dowolną biblioteką AJAX.  Pamiętaj, aby dla nagłówka `X-ZUMO-AUTH` ustawić swój token uwierzytelniania.  Token uwierzytelniania jest przechowywany w elemencie `client.currentUser.mobileServiceAuthenticationToken`.  Na przykład aby użyć interfejsu API Fetch:

```javascript
var url = client.applicationUrl + '/.auth/me';
var headers = new Headers();
headers.append('X-ZUMO-AUTH', client.currentUser.mobileServiceAuthenticationToken);
fetch(url, { headers: headers })
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Interfejs Fetch jest dostępny jako [pakiet npm](https://www.npmjs.com/package/whatwg-fetch). Ponadto można go pobrać w przeglądarce z witryny [CDNJS](https://cdnjs.com/libraries/fetch). Do pobrania informacji można również użyć biblioteki jQuery lub innego interfejsu API AJAX.  Dane są odbierane w postaci obiektu JSON.
