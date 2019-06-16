---
title: Logowanie jednokrotne (Biblioteka Microsoft Authentication Library dla języka JavaScript) | Azure
description: Więcej informacji na temat tworzenia funkcji logowania jednokrotnego pojedynczego za pomocą Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js).
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f1f102307256852ac92616c7fb707e0e2739e5d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544152"
---
# <a name="single-sign-on-with-msaljs"></a>Logowanie jednokrotne za pomocą MSAL.js

Pojedynczego logowania jednokrotnego (SSO) umożliwia użytkownikom wprowadzenie poświadczeń raz zalogować się i ustanowienia sesji, które mogą być używane ponownie dla wielu aplikacji bez konieczności uwierzytelnić się ponownie. To zapewnia bezproblemową obsługę dla użytkownika i zmniejsza powtarzanych monity o podanie poświadczeń.

Usługa Azure AD zapewnia możliwości logowania jednokrotnego do aplikacji przez ustawienie pliku cookie sesji, gdy użytkownik jest uwierzytelniany po raz pierwszy. Biblioteki MSAL.js pozwala aplikacjom korzystać to na kilka sposobów.

## <a name="sso-between-browser-tabs"></a>Logowanie Jednokrotne między kartach przeglądarki

Gdy aplikacja jest otwarty w wiele kart i pierwszego logowania użytkownika na jedną kartę, użytkownik jest także zalogowany na innych kartach bez monitów. MSAL.js buforuje identyfikator tokenu dla użytkownika w przeglądarce `localStorage` i będzie zalogować użytkownika do aplikacji na innych kartach open.

Domyślnie używa MSAL.js `sessionStorage` uniemożliwia sesji mogą być współużytkowane przez karty. Aby uzyskać logowanie Jednokrotne między kartami, należy ustawić `cacheLocation` w MSAL.js do `localStorage` jak pokazano poniżej.

```javascript
const config = {
    auth: {
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Logowanie Jednokrotne między aplikacjami

Podczas uwierzytelniania użytkownika na podstawie pliku cookie sesji jest ustawiana w domenie usługi Azure AD w przeglądarce. MSAL.js zależy od tego pliku cookie sesji w celu zapewnienia logowania jednokrotnego dla użytkownika między różnymi aplikacjami. MSAL.js buforuje identyfikator tokenów i tokenów dostępu użytkownika w magazynie przeglądarki dla domeny aplikacji. W rezultacie zachowanie logowania jednokrotnego różni się w różnych sytuacjach:  

### <a name="applications-on-the-same-domain"></a>Aplikacje na tej samej domenie

Gdy aplikacje są hostowane w tej samej domenie, użytkownik może zalogować się do aplikacji jeden raz i następnie może zostać uwierzytelniony do innych aplikacji bez wyświetlania monitu. MSAL.js korzysta z tokenów buforowanych dla użytkownika w domenie w celu zapewnienia logowania jednokrotnego.

### <a name="applications-on-different-domain"></a>Aplikacje w innej domenie

Gdy aplikacje są hostowane w różnych domenach, tokeny w domenie A pamięci podręcznej nie są dostępne dla MSAL.js w domenie B.

Oznacza to, że gdy użytkownicy zalogowany w domenie i przechodzą do aplikacji w domenie B, będzie można przekierowanie lub zostanie wyświetlony monit o stronę usługi Azure AD. Ponieważ usługa Azure AD ma nadal pliku cookie sesji użytkownika, będzie loguje użytkownika i nie będzie ich ponowne wprowadzenie poświadczeń. Jeśli użytkownik ma wiele kont użytkowników w sesji z usługą Azure AD, użytkownik zostanie wyświetlony monit, wybierz odpowiednie konto do zalogowania.

### <a name="automatically-select-account-on-azure-ad"></a>Automatycznie wybierz konto w usłudze Azure AD

W niektórych przypadkach aplikacja ma dostęp do kontekstu uwierzytelniania użytkownika i chce uniknąć monit wybór konta usługi Azure AD, gdy więcej niż jedno konto jest zalogowany.  Można to zrobić na kilka różnych sposobów:

**Przy użyciu Identyfikatora sesji (SID)**

Identyfikator sesji jest [opcjonalnego roszczenia](active-directory-optional-claims.md) , można skonfigurować w tokeny Identyfikatora. To oświadczenie umożliwia aplikacji do identyfikacji użytkownika w usłudze Azure AD sesji niezależnie od nazwy konta użytkownika lub nazwy użytkownika. Możesz przekazać identyfikator SID parametry żądania, aby `acquireTokenSilent` wywołania. Pozwoli to pominąć wybór konta usługi Azure AD. Identyfikator SID jest powiązany z pliku cookie sesji, a nie obejmujące wiele kontekstów przeglądarki.

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
> Identyfikator SID może służyć tylko w przypadku dyskretne uwierzytelnianie żądań wysyłanych przez `acquireTokenSilent` wywołania MSAL.js.
Procedurę konfigurowania opcjonalnych oświadczeń w manifeście aplikacji można znaleźć [tutaj](active-directory-optional-claims.md).

**Przy użyciu wskazówki logowania**

Jeśli nie masz identyfikatora SID oświadczenia, skonfigurować lub należy pominąć monit wybór konta w wywołań interakcyjnego uwierzytelniania, możesz to zrobić, podając `login_hint` w parametry żądania i opcjonalnie `domain_hint` jako `extraQueryParameters` w MSAL.js metod interaktywnych (`loginPopup`, `loginRedirect`, `acquireTokenPopup` i `acquireTokenRedirect`). Na przykład:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Aby uzyskać wartości login_hint i domain_hint, odczytywanie oświadczenia zwrócone w tokenie identyfikator dla użytkownika.

* **loginHint** powinna być równa `preferred_username` oświadczenia w tokenie identyfikator.

* **Element domain_hint** jest wymagana tylko do przekazania, korzystając z / Common urzędu. Wskazówka dotycząca domeny jest określana przez dzierżawcę ID(tid).  Jeśli `tid` oświadczenia w tokenie identyfikator `9188040d-6c67-4c5b-b112-36a304b66dad` jest konsumentów. W przeciwnym razie jest organizacji.

Odczyt [tutaj](v2-oauth2-implicit-grant-flow.md) więcej informacji na temat wartości wskazówka logowania i wskazówkę dotyczącą domeny.

> [!Note]
> Nie można przekazać identyfikator SID i login_hint, w tym samym czasie. Spowoduje to odpowiedzi na błąd.

## <a name="sso-without-msaljs-login"></a>Usługa rejestracji Jednokrotnej, bez logowania MSAL.js

Zgodnie z projektem MSAL.js wymaga, że metoda logowania jest wywoływana, aby ustanowić kontekst użytkownika przed pobraniem tokenów dla interfejsów API. Ponieważ metod logowania interakcyjnego, użytkownik zobaczy monit.

Brak niektórych przypadków, w których aplikacje mają dostęp do kontekstu użytkownika uwierzytelnionego lub tokenu Identyfikacyjnego za pomocą uwierzytelniania inicjowane w innej aplikacji i chcesz korzystać z logowania jednokrotnego uzyskiwanie tokenów bez pierwsze logowanie przy użyciu MSAL.js.

Na przykład jest: Użytkownik jest zalogowany do nadrzędnej aplikacji sieci web, który hostuje inną aplikację języka JavaScript, uruchomione jako dodatek lub wtyczki.

Środowisko logowania jednokrotnego, w tym scenariuszu można osiągnąć w następujący sposób:

Przekaż `sid` Jeśli jest dostępny (lub `login_hint` i opcjonalnie `domain_hint`) jako parametry do MSAL.js żądania `acquireTokenSilent` wywołań w następujący sposób:

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

## <a name="sso-in-adaljs-to-msaljs-update"></a>Logowanie Jednokrotne w ADAL.js MSAL.js aktualizacji

MSAL.js łączy równoważności funkcji za pomocą ADAL.js do scenariuszy uwierzytelniania usługi Azure AD. Można dokonać migracji z ADAL.js MSAL.js łatwy i aby zapobiec monitowaniu użytkowników, aby zalogować się ponownie biblioteki odczytuje tokenu Identyfikacyjnego reprezentujący sesję użytkownika w pamięci podręcznej ADAL.js i bezproblemowo loguje się użytkownik w MSAL.js.  

Z zalet zachowanie logowania jednokrotnego (SSO) w przypadku aktualizacji z ADAL.js, musisz upewnić się, korzystają z bibliotek `localStorage` do buforowania tokenów. Ustaw `cacheLocation` do `localStorage` MSAL.js i ADAL.js konfiguracji podczas inicjowania w następujący sposób:


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
        clientId: “abcd-ef12-gh34-ikkl-ashdjhlhsdg”
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Gdy to ustawienie jest konfigurowane, MSAL.js będzie można odczytać pamięci podręcznej stanu uwierzytelnionego użytkownika w ADAL.js i używać go w celu zapewnienia logowania jednokrotnego w MSAL.js.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [sesji logowania jednokrotnego i okres istnienia tokenu](active-directory-configurable-token-lifetimes.md) wartości w usłudze Azure AD.
