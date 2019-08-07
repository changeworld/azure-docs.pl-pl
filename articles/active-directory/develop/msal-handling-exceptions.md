---
title: Błędy i wyjątki (MSAL) | Azure
description: Dowiedz się, jak obsługiwać błędy i wyjątki, dostęp warunkowy i wyzwanie oświadczeń w aplikacjach MSAL.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c294e3bd8ac04454c2d715c665e0da4f9a4f4535
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835028"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>Obsługa wyjątków i błędów przy użyciu MSAL
Wyjątki w bibliotece uwierzytelniania firmy Microsoft (MSAL) są przeznaczone dla deweloperów aplikacji do rozwiązywania problemów, a nie do wyświetlania użytkownikom końcowym. Komunikaty o wyjątkach nie są zlokalizowane.

Podczas przetwarzania wyjątków i błędów można użyć samego typu wyjątku i kodu błędu, aby rozróżnić wyjątki.  Aby uzyskać listę kodów błędów, zobacz [kody błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md).

## <a name="net-exceptions"></a>Wyjątki dla platformy .NET
Podczas przetwarzania wyjątków można użyć samego typu wyjątku i `ErrorCode` elementu członkowskiego do rozróżnienia między wyjątkami. Wartości `ErrorCode` są stałymi typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Możesz również mieć zajrzeć do pól [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet), [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Jeśli [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) jest generowany, kod błędu może zawierać kod, który można znaleźć w temacie [uwierzytelnianie i kody błędów autoryzacji](reference-aadsts-error-codes.md).

### <a name="common-exceptions"></a>Typowe wyjątki
Poniżej przedstawiono typowe wyjątki, które mogą zostać zgłoszone i niektóre możliwe środki zaradcze.

| Wyjątek | Kod błędu | Środki zaradcze|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji o IDENTYFIKATORze "{appId}" o nazwie "{nazwa_aplikacji}". Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu.| Musisz najpierw uzyskać zgodę użytkownika. Jeśli nie korzystasz z platformy .NET Core (bez interfejsu użytkownika sieci Web), wywołaj (tylko raz) `AcquireTokeninteractive`. Jeśli korzystasz z platformy .NET Core lub nie chcesz go wykonać `AcquireTokenInteractive`, użytkownik może przejść do adresu URL, aby wyrazić zgodę:. https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read Aby wywołać `AcquireTokenInteractive`:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Użytkownik musi korzystać z uwierzytelniania wieloskładnikowego.| Nie ma ograniczenia — Jeśli uwierzytelnianie wieloskładnikowe jest skonfigurowane dla dzierżawy, a usługi AAD zdecyduje się ją wymusić, należy zawracać do przepływu `AcquireTokenInteractive` interaktywnego, takiego jak lub. `AcquireTokenByDeviceCode`|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Typ grantu nie jest obsługiwany w punktach końcowych */typowe* lub */consumers* . Użyj */Organizations* lub punktu końcowego określonego dla dzierżawy. Użyto */typowe*.| Zgodnie z opisem w komunikacie z usługi Azure AD urząd musi mieć dzierżawę lub inny */Organizations*.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Treść żądania musi zawierać następujący parametr: "client_secret lub client_assertion".| Taka sytuacja może wystąpić, jeśli aplikacja nie została zarejestrowana jako publiczna aplikacja kliencka w usłudze Azure AD. W Azure Portal Edytuj manifest dla aplikacji i ustaw `allowPublicClient` wartość na. `true` |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user komunikat: Nie można zidentyfikować zalogowanego użytkownika| Biblioteka nie mogła wykonać zapytania dotyczącego bieżącego zalogowanego użytkownika systemu Windows lub ten użytkownik nie jest połączony z usługą Active Directory lub AAD (przyłączone do miejsca pracy użytkownicy nie są obsługiwane). Ograniczenie 1: w platformy UWP, sprawdź, czy aplikacja ma następujące możliwości: Uwierzytelnianie przedsiębiorstwa, sieci prywatne (klient i serwer), informacje o koncie użytkownika. Środki zaradcze 2: Zaimplementuj własną logikę, aby pobrać nazwę użytkownika (na john@contoso.comprzykład), a `AcquireTokenByIntegratedWindowsAuth` następnie użyj formularza, który przyjmuje nazwę użytkownika.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ta metoda opiera się na protokole udostępnianym przez Active Directory (AD). Jeśli użytkownik został utworzony w Azure Active Directory bez kopii zapasowej usługi AD ("zarządzany"), ta metoda zakończy się niepowodzeniem. Użytkownicy utworzeni w usłudze AD i korzystający z usługi AAD ("federacyjne") mogą korzystać z tej nieinteraktywnej metody uwierzytelniania. Środki zaradcze Użyj uwierzytelniania interakcyjnego.|

## <a name="javascript-errors"></a>Błędy języka JavaScript

MSAL. js udostępnia obiekty błędów, które są abstrakcyjne i klasyfikuje różne typy typowych błędów i mają interfejs do uzyskiwania dostępu do szczegółowych informacji o błędach, takich jak komunikaty o błędach, aby odpowiednio je obsłużyć.

**Obiekt Error**

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
Rozszerzając klasę błędu, masz dostęp do następujących właściwości:
* **AuthError. komunikat:** Jest to takie samo jak errorMessage.
* **AuthError. Stack:** Ślad stosu dla zgłoszonych błędów. Umożliwia śledzenie do punktu początkowego błędu.

**Typy błędów**

Dostępne są następujące typy błędów:

* *AuthError:* Podstawowa Klasa błędów dla biblioteki MSAL. js, używana również w przypadku nieoczekiwanych błędów.

* *ClientAuthError:* Klasa Error, która oznacza problem z uwierzytelnianiem klienta. Większość błędów, które pochodzą z biblioteki, zostanie ClientAuthErrors. Mogą to być błędy, takie jak wywołanie metody logowania, gdy logowanie jest w toku, użytkownicy anulują logowanie itp.

* *ClientConfigurationError:* Klasa Error rozszerzająca ClientAuthError zgłoszonych przed żądaniami, gdy określone parametry konfiguracji użytkownika są źle sformułowane lub nie istnieją.

* *Błąd servererror* Klasa Error reprezentująca ciągi błędów wysyłane przez serwer uwierzytelniania. Mogą to być błędy, takie jak nieprawidłowe formaty lub parametry żądania albo inne błędy, które uniemożliwiają uwierzytelnienie lub autoryzowanie użytkownika przez serwer.

* *InteractionRequiredAuthError:* Klasa Error rozszerzająca błąd servererror w celu reprezentowania błędów serwera, które wymagają wywołania interaktywnego. Jest to zgłaszane w `acquireTokenSilent` przypadku, gdy użytkownik musi współdziałać z serwerem w celu podania poświadczeń lub zgody na uwierzytelnianie/autoryzację. Kody błędów obejmują "interaction_required", "login_required", "consent_required".

Aby obsłużyć błędy w przepływach uwierzytelniania przy`loginRedirect`użyciu `acquireTokenRedirect`metod redirect (,), należy zarejestrować wywołanie zwrotne, które jest wywoływane z sukcesem lub niepowodzeniem po przekierowaniu przy użyciu `handleRedirectCallback()` metody w następujący sposób:

```javascript
function authCallback(error, response) {
    //handle redirect response
}


var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);

myMSALObj.acquireTokenRedirect(request);
```

Metody dla podskakujących okienek (`loginPopup`, `acquireTokenPopup`) zwracają niesie obietnice zwiększenia, więc można użyć wzorca Promise (. then i. catch), aby je obsłużyć w sposób pokazany:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="interaction-required-errors"></a>Błędy wymagane interakcji

Gdy wymagana jest interakcja interfejsu użytkownika, zwracany jest błąd. Oznacza to, że podjęto próbę użycia nieinteraktywnej metody uzyskiwania tokenu (na przykład `acquireTokenSilent`), ale MSAL nie może wykonać go w trybie dyskretnym. Możliwe przyczyny są następujące:

* musisz się zalogować
* Musisz wyrazić zgodę
* musisz przejść przez środowisko uwierzytelniania wieloskładnikowego.

Korygowanie polega na wywołaniu metody interaktywnej, takiej jak `acquireTokenPopup` lub `acquireTokenRedirect`:

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

## <a name="conditional-access-and-claims-challenges"></a>Wyzwania dotyczące dostępu warunkowego i oświadczeń
W przypadku odzyskania tokenów w trybie dyskretnym aplikacja może otrzymywać błędy w przypadku, gdy interfejs API, do którego próbujesz uzyskać dostęp, wymaga [żądania oświadczeń dostępu warunkowego](conditional-access-dev-guide.md) , takiego jak zasady MFA.

Wzorzec obsługi tego błędu polega na interaktywnej pozyskaniu tokenu przy użyciu MSAL. Interaktywny pozyskiwanie tokenu wyświetla użytkownika i daje im możliwość spełnienia wymaganych zasad dostępu warunkowego.

W niektórych przypadkach podczas wywoływania interfejsu API wymagającego dostępu warunkowego można odebrać wyzwanie oświadczeń w błędzie z interfejsu API. Na przykład jeśli zasady dostępu warunkowego mają mieć urządzenie zarządzane (Intune), błąd będzie wyglądać podobnie jak [AADSTS53000: Urządzenie musi być zarządzane, aby można było uzyskać dostęp do](reference-aadsts-error-codes.md) tego zasobu lub czegoś podobnego. W takim przypadku można przekazać oświadczenia w wywołaniu tokenu pozyskiwania, aby użytkownik był monitowany o spełnienie odpowiednich zasad.

### <a name="net"></a>.NET
Gdy wywoływany jest interfejs API wymagający dostępu warunkowego z MSAL.NET, aplikacja będzie musiała obsługiwać wyjątki wezwania. Zostanie ona wyświetlona jako [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) , gdzie Właściwość [oświadczenia](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) nie będzie pusta.

Aby obsłużyć wyzwanie żądania, należy użyć `.WithClaim()` metody `PublicClientApplicationBuilder` klasy.

### <a name="javascript"></a>JavaScript
W przypadku korzystania z tokenów dyskretnie (przy użyciu `acquireTokenSilent`programu) przy użyciu MSAL. js aplikacja może otrzymywać błędy, gdy do interfejsu API, do którego próbujesz uzyskać dostęp, jest wymagane [wyzwanie żądania dostępu warunkowego](conditional-access-dev-guide.md) , takie jak zasady MFA.

Wzorzec do obsługi tego błędu polega na tym, że wywołanie interaktywne umożliwia uzyskanie tokenu w MSAL. js, takiego `acquireTokenPopup` jak `acquireTokenRedirect` lub, jak w poniższym przykładzie:

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

Interaktywny uzyskiwanie tokenu wyświetla użytkownika i daje im możliwość spełnienia wymaganych zasad dostępu warunkowego.

Gdy wywołujesz interfejs API wymagający dostępu warunkowego, możesz odebrać wyzwanie dotyczące oświadczeń w błędzie z interfejsu API. W takim przypadku można przekazać oświadczenia zwrócone w wyniku błędu jako `extraQueryParameters` wywołanie w celu uzyskania tokenów, aby użytkownik był monitowany o spełnienie odpowiednich zasad:

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>Ponawianie próby po wystąpieniu błędów i wyjątkach

### <a name="http-error-codes-500-600"></a>Kody błędów HTTP 500-600
MSAL.NET implementuje prosty mechanizm ponawiania prób w przypadku błędów z kodami błędów HTTP 500-600.

### <a name="http-429"></a>HTTP 429
Gdy serwer tokenów usług jest zbyt zajęty z powodu "zbyt dużej liczby żądań", zwraca błąd HTTP 429 z wskazówką dotyczącą sytuacji, w której można spróbować ponownie (pole odpowiedzi retry-After) jako opóźnienie w sekundach lub datę.

#### <a name="net"></a>.NET
[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) `System.Net.Http.Headers.HttpResponseHeaders` jako właściwości `namedHeaders`. W związku z tym możesz użyć dodatkowych informacji z kodu błędu, aby zwiększyć niezawodność aplikacji. W przypadku, w którym właśnie została opisana, można użyć `RetryAfterproperty` (typu `RetryConditionHeaderValue`) i obliczeń, kiedy należy ponowić próbę.

Oto przykład dla aplikacji demona (przy użyciu przepływu poświadczeń klienta), ale można ją dostosować do dowolnej metody uzyskiwania tokenu.

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
