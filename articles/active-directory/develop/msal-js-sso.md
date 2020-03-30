---
title: Logowanie jednokrotne (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o tworzeniu środowiska logowania jednokrotnego przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262856"
---
# <a name="single-sign-on-with-msaljs"></a>Logowanie jednokrotne za pomocą biblioteki MSAL.js

Logowanie jednokrotne (Logowanie jednokrotne) umożliwia użytkownikom wprowadzenie poświadczeń raz do zalogowania się i ustanowienia sesji, która może być ponownie używana w wielu aplikacjach bez konieczności ponownego uwierzytelniania. Zapewnia to bezproblemowe środowisko dla użytkownika i zmniejsza powtarzające się monity o poświadczenia.

Usługa Azure AD udostępnia aplikacjom funkcje SSO, ustawiając plik cookie sesji, gdy użytkownik uwierzytelnia się po raz pierwszy. Biblioteka MSAL.js umożliwia aplikacjom korzystanie z tego na kilka sposobów.

## <a name="sso-between-browser-tabs"></a>Przysyłanie między kartami przeglądarki

Gdy aplikacja jest otwarta w wielu kartach i najpierw zalogujesz się na jednej karcie, użytkownik jest również zalogowany na innych kartach bez monitu. Msal.js buforuje token identyfikatora dla użytkownika `localStorage` w przeglądarce i zaloguje użytkownika do aplikacji na innych otwartych kartach.

Domyślnie msal.js `sessionStorage` używa, który nie zezwala na sesji do współużytkowane między kartami. Aby uzyskać funkcję SSO między kartami, upewnij się, że `cacheLocation` w pliku MSAL.js ustawiono na tak, `localStorage` jak pokazano poniżej.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>SSO między aplikacjami

Gdy użytkownik uwierzytelnia się, plik cookie sesji jest ustawiany w domenie usługi Azure AD w przeglądarce. MSAL.js opiera się na tym pliku cookie sesji, aby zapewnić sytą dla użytkownika między różnymi aplikacjami. Msal.js również buforuje tokeny identyfikatorów i tokeny dostępu użytkownika w magazynie przeglądarki na domenę aplikacji. W rezultacie zachowanie samego siebie różni się w różnych przypadkach:  

### <a name="applications-on-the-same-domain"></a>Aplikacje w tej samej domenie

Gdy aplikacje są hostowane w tej samej domenie, użytkownik może zalogować się do aplikacji raz, a następnie uzyskać uwierzytelnienie do innych aplikacji bez monitu. Msal.js wykorzystuje tokeny buforowane dla użytkownika w domenie, aby zapewnić sytą alokację.

### <a name="applications-on-different-domain"></a>Aplikacje w różnych domenach

Gdy aplikacje są hostowane w różnych domenach, tokeny buforowane w domenie A nie mogą być dostępne dla msal.js w domenie B.

Oznacza to, że gdy użytkownicy zalogowani w domenie A przejdź do aplikacji w domenie B, zostaną przekierowani lub monitowani za pomocą strony usługi Azure AD. Ponieważ usługa Azure AD nadal ma plik cookie sesji użytkownika, będzie logować się do użytkownika i nie będzie musiał ponownie wprowadzić poświadczenia. Jeśli użytkownik ma wiele kont użytkowników w sesji z usługą Azure AD, użytkownik zostanie poproszony o wybranie odpowiedniego konta, aby się zalogować.

### <a name="automatically-select-account-on-azure-ad"></a>Automatyczne wybieranie konta w usłudze Azure AD

W niektórych przypadkach aplikacja ma dostęp do kontekstu uwierzytelniania użytkownika i chce uniknąć monitu o wybór konta usługi Azure AD po zalogowaniu się na wiele kont.  Można to zrobić na kilka różnych sposobów:

**Używanie identyfikatora sesji (SID)**

Identyfikator sesji to [opcjonalne oświadczenie,](active-directory-optional-claims.md) które można skonfigurować w tokenach identyfikatorów. To oświadczenie umożliwia aplikacji identyfikowanie sesji usługi Azure AD użytkownika niezależnie od nazwy konta lub nazwy użytkownika użytkownika. Identyfikator SID w parametrach żądania `acquireTokenSilent` można przekazać do wywołania. Umożliwi to usługę Azure AD ominięcie wyboru konta. Identyfikator SID jest powiązany z plikiem cookie sesji i nie będzie przechodził przez konteksty przeglądarki.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> Identyfikator SID może być używany tylko `acquireTokenSilent` z cichymi żądaniami uwierzytelniania wykonanymi przez wywołanie w pliku MSAL.js.
Kroki konfigurowania oświadczeń opcjonalnych można znaleźć w manifeście aplikacji [tutaj](active-directory-optional-claims.md).

**Korzystanie z wskazówki logowania**

Jeśli nie masz skonfigurowanego oświadczenia SID lub musisz pominąć monit o wybór konta w `login_hint` interakcyjnych wywołaniach uwierzytelniania, możesz to zrobić, podając parametry żądania i opcjonalnie `domain_hint` jak `extraQueryParameters` w interaktywnych metodach MSAL.js`loginPopup`( , `loginRedirect`, `acquireTokenPopup` i `acquireTokenRedirect`). Przykład:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Można uzyskać wartości dla login_hint i domain_hint, odczytywanie oświadczeń zwróconych w tokenie identyfikatora dla użytkownika.

* **loginHint** należy ustawić `preferred_username` na oświadczenie w tokenie identyfikatora.

* **domain_hint** jest wymagane tylko do przekazania podczas korzystania z /common authority. Wskazówka domeny jest określana przez identyfikator dzierżawy(tid).  Jeśli `tid` oświadczenie w tokenie `9188040d-6c67-4c5b-b112-36a304b66dad` identyfikatora jest to konsumenci. W przeciwnym razie jest to organizacja.

Przeczytaj [tutaj,](v2-oauth2-implicit-grant-flow.md) aby uzyskać więcej informacji na temat wartości wskazówek dotyczących logowania i wskazówki dotyczące domeny.

> [!Note]
> Nie można przekazać identyfikatora SID i login_hint w tym samym czasie. Spowoduje to odpowiedź na błąd.

## <a name="sso-without-msaljs-login"></a>Logowanie SSO bez logowania do msal.js

Zgodnie z projektem MSAL.js wymaga, aby metoda logowania jest wywoływana w celu ustanowienia kontekstu użytkownika przed uzyskaniem tokenów dla interfejsów API. Ponieważ metody logowania są interaktywne, użytkownik widzi monit.

Istnieją pewne przypadki, w których aplikacje mają dostęp do kontekstu uwierzytelnionego użytkownika lub tokenu identyfikatora za pośrednictwem uwierzytelniania zainicjowanego w innej aplikacji i chcą wykorzystać logowanie jednośmiękowe do uzyskiwania tokenów bez uprzedniego logowania za pośrednictwem pliku MSAL.js.

Przykładem tego jest: Użytkownik jest zalogowany do nadrzędnej aplikacji sieci web, która obsługuje inną aplikację JavaScript działającą jako dodatek lub wtyczka.

Doświadczenie samoso w tym scenariuszu można osiągnąć w następujący sposób:

Przekaż `sid` if available `login_hint` (lub `domain_hint`opcjonalnie) jako parametry żądania do `acquireTokenSilent` wywołania MSAL.js w następujący sposób:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>SSO w ADAL.js do aktualizacji MSAL.js

Program MSAL.js przynosi parzystość funkcji z plikem ADAL.js dla scenariuszy uwierzytelniania usługi Azure AD. Aby ułatwić migrację z pliku ADAL.js do pliku MSAL.js i uniknąć monitowania użytkowników o ponowne zalogowanie się, biblioteka odczytuje token identyfikatora reprezentujący sesję użytkownika w pamięci podręcznej ADAL.js i bezproblemowo loguje się w u użytkownika w pliku MSAL.js.  

Aby skorzystać z zachowania logowania jednokrotnego podczas aktualizowania z usługi ADAL.js, należy upewnić `localStorage` się, że biblioteki są używane do buforowania tokenów. Ustaw `cacheLocation` na `localStorage` obu w konfiguracji MSAL.js i ADAL.js podczas inicjowania w następujący sposób:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Po skonfigurowaniu usługa MSAL.js będzie mogła odczytać stan pamięci podręcznej uwierzytelnionego użytkownika w pliku ADAL.js i użyć go do zapewnienia identyfikatora jednoświadczanego w pliku MSAL.js.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [sesji logowania jednokrotnego i](active-directory-configurable-token-lifetimes.md) wartości okresu istnienia tokenu w usłudze Azure AD.
