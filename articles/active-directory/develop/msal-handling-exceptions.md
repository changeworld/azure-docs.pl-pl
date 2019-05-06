---
title: Błędy i wyjątki (MSAL) | Azure
description: Dowiedz się, jak obsługa błędów i wyjątków, dostęp warunkowy i oświadczenia rzuć wyzwanie w aplikacjach biblioteki MSAL.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 7c9a578cb3c3a59ae6bba13e585188020f35f03a
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080940"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Obsługa wyjątków i błędów z zastosowaniem biblioteki MSAL
Wyjątki w Microsoft Authentication Library (MSAL) są przeznaczone dla deweloperów aplikacji rozwiązać, a nie w celu wyświetlania użytkownikom końcowym. Nie są lokalizowane komunikaty o wyjątkach.

Podczas przetwarzania, wyjątki i błędy, można użyć samego typu wyjątku i kod błędu: rozróżnienie między wyjątków.  Aby uzyskać listę kodów błędów, zobacz [kody błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Wyjątki platformy .NET
Podczas przetwarzania wyjątki, można użyć samego typu wyjątku i `ErrorCode` elementu członkowskiego, aby rozróżniać wyjątków. Wartości `ErrorCode` to stałe typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet#fields).

Mogą też istnieć przyjrzeć się pola [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet#fields), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet#fields).

Jeśli [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) jest zgłaszany błąd kodu może zawierać kod, który można znaleźć w [kody błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Typowe wyjątki
Poniżej przedstawiono typowe wyjątki, które może zostać wygenerowany i niektóre możliwe środki zaradcze.

| Wyjątek | Kod błędu | Środki zaradcze|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Użytkownik albo administrator nie wyraził zgody na używanie aplikacji o identyfikatorze {appId} o nazwie "{appName}". Wyślij żądanie interaktywnej autoryzacji dla tego użytkownika i zasobu.| Musisz pobrać pierwszy zgody użytkownika. Jeśli nie używasz platformy .NET Core, (która nie ma żadnych interfejsów użytkownika sieci Web), wywołaj (tylko raz) `AcquireTokeninteractive`. Jeśli używasz platformy .NET core, lub nie chcesz zrobić `AcquireTokenInteractive`, użytkownik może przejść do adresu URL, aby wyrazić zgodę: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read . Aby wywołać `AcquireTokenInteractive`: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Wymagane jest wprowadzenie przez użytkownika do korzystania z uwierzytelniania Multi-Factor Authentication.| Nie ma bez ograniczania ryzyka — Jeśli skonfigurowano usługę MFA dla Twojej dzierżawy i AAD decyduje się na jej wymusić, musisz powrót do interaktywnego przepływu takich jak `AcquireTokenInteractive` lub `AcquireTokenByDeviceCode`.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) |AADSTS90010: Typ udzielania nie jest obsługiwana przez */common* lub */consumers* punktów końcowych. Użyj */organizations* lub punktem końcowym specyficznym dla dzierżawy. Użyte */common*.| Jak wyjaśniono w wiadomości z usługi Azure AD, musi mieć dzierżawę urzędowi lub w inny sposób */organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet#fields) | AADSTS70002: Treść żądania musi zawierać następujący parametr: "client_secret lub client_assertion".| Może to nastąpić, jeśli aplikacja nie został zarejestrowany jako aplikację kliencką publicznych w usłudze Azure AD. W witrynie Azure portal, edytowanie manifestu dla aplikacji i zestaw `allowPublicClient` do `true`. |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user komunikat: Nie można zidentyfikować zalogowanego użytkownika| Biblioteki nie mógł zbadać Windows bieżącego zalogowanego użytkownika lub ten użytkownik nie jest AD lub dołączonych do usługi AAD (użytkownicy dołączonym do miejsca pracy nie są obsługiwane). Ograniczenie 1: na platformie UWP, sprawdź, czy aplikacja ma następujące możliwości: Uwierzytelnianie w przedsiębiorstwie, sieci prywatne (klient i serwer), informacje o koncie użytkownika. Ograniczenie 2: Implementuje logikę można pobrać nazwy użytkownika (na przykład john@contoso.com) i używać `AcquireTokenByIntegratedWindowsAuth` formularz, który przyjmuje nazwę użytkownika.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ta metoda korzysta protokół udostępnianych przez Active Directory (AD). Jeśli użytkownik został utworzony w usłudze Azure Active Directory bez kopii AD (na użytkownika "zarządzany"), ta metoda nie powiedzie się. Użytkownicy utworzeni w AD i wspierana przez usługi AAD (użytkownicy "federacyjni") mogą korzystać z tej metody nieinterakcyjnych Authentication. Środki zaradcze: Uwierzytelnianie interakcyjne.|

## <a name="javascript-errors"></a>Błędy języka JavaScript

MSAL.js zawiera obiekty błędów, które abstrakcyjnej i klasyfikowania różnych typów typowych błędów i mają dostęp szczegółowe informacje o błędach, takie jak komunikaty o błędach odpowiednio je obsłużyć do interfejsu.

**Error — obiekt**

```javascript                                
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```                
Rozszerzając Klasa błędów, masz dostęp do następujących właściwości:
* **AuthError.message:** To jest taka sama jak komunikat o błędzie.
* **AuthError.stack:** Ślad stosu dla zgłoszonych błędów. Umożliwia śledzenie do punktu pochodzenia wystąpienia błędu.

**Typy błędów**

Dostępne są następujące typy błędów:

* *AuthError:* Klasa podstawowa błąd biblioteki MSAL.js, używane również do nieoczekiwanych błędów.

* *ClientAuthError:* Klasa błędów, oznacza problem z uwierzytelnianiem klienta. Większość błędów, które pochodzą z biblioteki będzie ClientAuthErrors. Mogą to być błędy, takie jak wywołanie metody logowania, gdy login jest w toku, użytkownicy anulowanie logowania itp.

* *ClientConfigurationError:* Klasa błędów rozszerzanie ClientAuthError zgłoszony przed żądania są wykonywane, gdy parametry konfiguracji danego użytkownika jest źle sformułowany lub jego brak.

* *Błąd ServerError:* Błąd klasy do reprezentowania ciągi błędów, wysłanych przez serwer uwierzytelniania. Może to być błędy, takie jak formaty nieprawidłowe żądanie lub parametrów lub inne błędy, które uniemożliwiają z serwera uwierzytelniania lub autoryzacji użytkownika.

* *InteractionRequiredAuthError:* Klasa błąd rozszerzanie błąd ServerError reprezentującego błędy serwera, które wymagają wywołań interakcyjnego. To jest generowany przez `acquireTokenSilent` Jeśli wymagane jest wprowadzenie przez użytkownika do interakcji z serwerem o podanie poświadczeń lub wyrazić zgodę dla uwierzytelniania/autoryzacji. Kody błędów należą "interaction_required", "login_required", "consent_required".

W przypadku obsługi błędów w przepływów uwierzytelniania za pomocą przekierowania metody (`loginRedirect`, `acquireTokenRedirect`), musisz zarejestrować sukcesów i niepowodzeń wywołań zwrotnych do wywołania po użyciu przekierowania `handleRedirectCallbacks()` metody w następujący sposób:

```javascript
function acquireTokenRedirectCallBack(response) {
    // success response
}

function  acquireTokenErrorRedirectCallBack(error) {
    console.log(error);
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallbacks(acquireTokenRedirectCallBack, acquireTokenErrorRedirectCallBack);

myMSALObj.acquireTokenRedirect(request);
```

Metody obsługi wyskakujących (`loginPopup`, `acquireTokenPopup`) zwracają obietnic, dzięki czemu można użyć wzorca promise (.then i .catch), aby obsługiwać je, jak pokazano:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Błędy interakcji ze strony

Błąd jest zwracany, gdy wymagana jest interakcja interfejsu użytkownika. Oznacza to, że podjęto próbę użycia nieinterakcyjnych metody pobierania tokenu (na przykład `acquireTokenSilent`), ale MSAL nie może go dyskretnie. Możliwe przyczyny są następujące:

* Musisz się zarejestrować
* musisz wyrazić zgodę
* Musisz przejść przez środowisko usługi Multi-Factor authentication.

Wyjściem jest wywołać metodę interakcyjnego, taką jak `acquireTokenPopup` lub `acquireTokenRedirect`:

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="conditional-access-and-claims-challenges"></a>Warunkowego dostępu i oświadczenia wyzwania
Podczas uzyskiwania tokenów w trybie dyskretnym, aplikacja może otrzymywać komunikaty o błędach podczas [dostępu warunkowego oświadczeń wyzwanie](conditional-access-dev-guide.md#scenario-single-page-app-spa-using-adaljs) takie jak zasady MFA jest wymagana przez interfejs API próbujesz uzyskać dostęp.

Wzorzec do obsługi tego błędu jest interaktywnie uzyskać token z zastosowaniem biblioteki MSAL. Interaktywnie pobierania tokenu monituje użytkownika i daje im możliwość spełnić wymagania zasad dostępu warunkowego wymagany.

W niektórych przypadkach podczas wywoływania interfejsu API wymaga dostępu warunkowego może odbierać żądania oświadczeń w błędzie z interfejsu API. Związane z wystąpieniem, jeśli zasady dostępu warunkowego jest zapewnienie zarządzanego urządzenia (Intune) błąd podobny do [AADSTS53000: Urządzenie nie jest wymagane do zarządzania dostępu do tego zasobu](reference-aadsts-error-codes.md) lub innego ogranicznika. W takim przypadku mogą przesyłać oświadczenia w wywołaniu tokenu nabywania, więc, że użytkownik jest monitowany o spełniają odpowiednie zasady.

### <a name="net"></a>.NET
Podczas wywoływania interfejsu API wymaga dostępu warunkowego z platformy MSAL.NET, aplikacja musi obsługiwać wyjątki żądania oświadczeń. Zostanie ona wyświetlona jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) gdzie [oświadczeń](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) właściwość będzie pusta.

Aby obsłużyć żądania oświadczeń, musisz użyć `.WithClaim()` metody `PublicClientApplicationBuilder` klasy.

### <a name="javascript"></a>JavaScript
Podczas uzyskiwania tokenów w trybie dyskretnym (przy użyciu `acquireTokenSilent`) przy użyciu MSAL.js, aplikacja może otrzymywać komunikaty o błędach podczas [dostępu warunkowego oświadczeń wyzwanie](conditional-access-dev-guide.md#scenario-single-page-app-spa-using-adaljs) takie jak zasady MFA jest wymagana przez interfejs API próbujesz uzyskać dostęp.

Wzorzec do obsługi tego błędu jest wykonywanie wywołania interfejsu interaktywnych, takich jak uzyskanie tokenu w MSAL.js `acquireTokenPopup` lub `acquireTokenRedirect` jak w poniższym przykładzie:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

Interaktywnie uzyskiwania tokenu monituje użytkownika i daje im możliwość spełnić wymagania zasad dostępu warunkowego wymagany.

Podczas wywoływania interfejsu API wymaga dostępu warunkowego, może odbierać żądania oświadczeń w wyniku błędu z interfejsu API. W takim przypadku można przekazać oświadczenia zwrócone w wyniku błędu, jako `extraQueryParameters` w wywołaniu uzyskać tokeny, dzięki czemu użytkownik jest monitowany o spełniają odpowiednie zasady:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Ponawia próbę po błędy i wyjątki

### <a name="http-error-codes-500-600"></a>Kody błędów HTTP 500-600
Platformy MSAL.NET implementuje prosty ponawiania — po mechanizm błędy z powodu błędu HTTP kody 500 do 600.

### <a name="http-429"></a>HTTP 429
Gdy usługa serwera tokenów (STS) jest zbyt zajęty z powodu "zbyt wiele żądań", zwraca błąd HTTP 429 wskazówkę o tym, kiedy to możliwe spróbuj ponownie (pole Retry-After odpowiedzi) jako opóźnienie w ciągu kilku sekund lub wartość typu date.

#### <a name="net"></a>.NET
[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) powierzchnie wyjątek `System.Net.Http.Headers.HttpResponseHeaders` jako właściwość `namedHeaders`. W związku z tym można wykorzystać dodatkowe informacje z kodu błędu w celu zwiększenia niezawodności aplikacji. W tym przypadku właśnie opisanego, można użyć `RetryAfterproperty` (typu `RetryConditionHeaderValue`) i zasobów obliczeniowych, kiedy należy ponowić próbę.

Oto przykład dla aplikacji demona (przy użyciu przepływu poświadczeń klienta), ale można przystosować do dowolnej z metod pobierania tokenu.

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
