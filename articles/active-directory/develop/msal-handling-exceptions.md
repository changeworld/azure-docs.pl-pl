---
title: Błędy i wyjątki (MSAL)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, dostęp warunkowy i wyzwania związane z roszczeniami w aplikacjach MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 93d07ab1740da68298478ae2dcc2ab46d8d8362e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884022"
---
# <a name="handle-msal-exceptions-and-errors"></a>Obsługa wyjątków i błędów msal

W tym artykule przedstawiono omówienie różnych typów błędów i zaleceń dotyczących obsługi typowych błędów logowania.

## <a name="msal-error-handling-basics"></a>Podstawy obsługi błędów MSAL

Wyjątki w bibliotece uwierzytelniania firmy Microsoft (MSAL) są przeznaczone dla deweloperów aplikacji do rozwiązywania problemów — nie do wyświetlania użytkownikom końcowym. Komunikaty wyjątków nie są zlokalizowane.

Podczas przetwarzania wyjątków i błędów, można użyć samego typu wyjątku i kodu błędu, aby odróżnić wyjątki.  Aby uzyskać listę kodów błędów, zobacz [Kody błędów uwierzytelniania i autoryzacji](reference-aadsts-error-codes.md).

Podczas logowania mogą wystąpić błędy dotyczące zgód, dostępu warunkowego (usługi MFA, zarządzania urządzeniami, ograniczeń opartych na lokalizacji), wydawania i realizacji tokenów oraz właściwości użytkownika.

Aby uzyskać więcej informacji na temat obsługi błędów aplikacji, zobacz następującą sekcję, która odpowiada używanemu językowi.

## <a name="net"></a>[.NET](#tab/dotnet)

Podczas przetwarzania wyjątków .NET, można użyć samego `ErrorCode` typu wyjątku i elementu członkowskiego do rozróżniania wyjątków. `ErrorCode`wartości są stałymi typu [MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet).

Można również spojrzeć na pola [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)i [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet).

Jeśli [msalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) jest generowany, spróbuj [uwierzytelnianie i autoryzacji kody błędów,](reference-aadsts-error-codes.md) aby sprawdzić, czy kod jest tam wymieniony.

### <a name="common-net-exceptions"></a>Typowe wyjątki .NET

Oto typowe wyjątki, które mogą być zgłaszane i niektóre możliwe środki zaradcze:  

| Wyjątek | Kod błędu | Środki zaradcze|
| --- | --- | --- |
| [MsalUiRequiredEktacja](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: Użytkownik lub administrator nie wyraził zgody na korzystanie z aplikacji o identyfikatorze "{appId}" o nazwie "{appName}". Wyślij interaktywne żądanie autoryzacji dla tego użytkownika i zasobu.| Najpierw musisz uzyskać zgodę użytkownika. Jeśli nie używasz programu .NET Core (który nie ma żadnego interfejsu `AcquireTokeninteractive`użytkownika sieci Web), zadzwoń (tylko raz). Jeśli używasz rdzenia .NET lub nie `AcquireTokenInteractive`chcesz tego robić, użytkownik może przejść `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`do adresu URL, aby wyrazić zgodę: . aby `AcquireTokenInteractive`zadzwonić:`app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredEktacja](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: Użytkownik jest zobowiązany do korzystania z uwierzytelniania wieloskładnikowego (MFA).| Nie ma żadnych środków zaradczych. Jeśli usługa MFA jest skonfigurowana dla dzierżawy, a usługa Azure Active Directory (AAD) `AcquireTokenInteractive` zdecyduje `AcquireTokenByDeviceCode`się ją wymusić, należy przywrócić do przepływu interaktywnego, takiego jak lub .|
| [MsalServiceException (Nieekceptiona msalserviceexception)](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: Typ dotacji nie jest obsługiwany przez punkty końcowe */common* lub */consumers.* Użyj */organizacje* lub punktu końcowego specyficzne dla dzierżawy. Użyto */common*.| Jak wyjaśniono w komunikacie z usługi Azure AD, urząd musi mieć dzierżawę lub w inny sposób */organizacje.*|
| [MsalServiceException (Nieekceptiona msalserviceexception)](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: Treść żądania musi zawierać `client_secret or client_assertion`następujący parametr: .| Ten wyjątek można zrzucić, jeśli aplikacja nie została zarejestrowana jako publiczna aplikacja kliencka w usłudze Azure AD. W witrynie Azure portal edytuj manifest `allowPublicClient` dla `true`aplikacji i ustaw na . |
| [MsalClientException (Z wyjątkiem biuletynu"](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`: Nie można zidentyfikować zalogowanego użytkownika| Biblioteka nie może zbadać bieżącego zalogowanego użytkownika systemu Windows lub ten użytkownik nie jest przyłączony do usługi AD lub usługi AAD (użytkownicy przyłączeni do miejsca pracy nie są obsługiwani). Ograniczenie 1: na platformie UNIWERSALNEJ systemu Windows sprawdź, czy aplikacja ma następujące możliwości: Uwierzytelnianie przedsiębiorstwa, Sieci prywatne (klient i serwer), Informacje o koncie użytkownika. Łagodzenie 2: Zaimplementuj własną logikę, aby `AcquireTokenByIntegratedWindowsAuth` pobrać nazwę użytkownika (na przykład) i użyć formularza, john@contoso.comktóry przyjmuje w nazwie użytkownika.|
| [MsalClientException (Z wyjątkiem biuletynu"](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| Ta metoda opiera się na protokole udostępniane przez usługi Active Directory (AD). Jeśli użytkownik został utworzony w usłudze Azure Active Directory bez kopii zapasowej usługi AD ("zarządzany" użytkownik), ta metoda zakończy się niepowodzeniem. Użytkownicy utworzyli w ucho i wspierani przez AAD ("federowani" użytkownicy) mogą korzystać z tej nieinteraktywnej metody uwierzytelniania. Środki zaradcze: użyj uwierzytelniania interaktywnego.|

### `MsalUiRequiredException`

Jednym ze wspólnych kodów stanu zwróconych `AcquireTokenSilent()` `MsalError.InvalidGrantError`z MSAL.NET podczas wywoływania jest . Ten kod stanu oznacza, że aplikacja powinna wywołać bibliotekę uwierzytelniania ponownie, ale w trybie interaktywnym (AcquireTokenInteractive lub AcquireTokenByDeviceCodeFlow dla publicznych aplikacji klienckich i wykonać wyzwanie w aplikacjach sieci Web). Jest tak, ponieważ dodatkowa interakcja użytkownika jest wymagana przed wystawieniem tokenu uwierzytelniania.

W większości przypadków, gdy `AcquireTokenSilent` kończy się niepowodzeniem, to dlatego, że pamięć podręczna tokenów nie ma tokenów pasujących do żądania. Tokeny dostępu wygasają w `AcquireTokenSilent` ciągu 1 godziny i spróbuje pobrać nowy na podstawie tokenu odświeżania (w warunkach OAuth2 jest to przepływ "Token odświeżania"). Ten przepływ może również zakończyć się niepowodzeniem z różnych powodów, na przykład jeśli administrator dzierżawy konfiguruje bardziej rygorystyczne zasady logowania. 

Interakcja ma na celu, aby użytkownik zrobił akcję. Niektóre z tych warunków są łatwe do rozwiązania dla użytkowników (na przykład zaakceptować warunki użytkowania za pomocą jednego kliknięcia), a niektóre nie mogą być rozwiązane przy użyciu bieżącej konfiguracji (na przykład dany komputer musi połączyć się z określoną siecią firmową). Niektóre z nich pomagają użytkownikowi w skonfigurowaniu uwierzytelniania wieloskładnikowego lub instalują program Microsoft Authenticator na swoim urządzeniu.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException`wyliczanie klasyfikacji

Usługa MSAL `Classification` udostępnia pole, które można odczytać, aby zapewnić lepsze środowisko użytkownika, na przykład, aby poinformować użytkownika, że ich hasło wygasło lub że będą musieli udzielić zgody na korzystanie z niektórych zasobów. Obsługiwane wartości są częścią `UiRequiredExceptionClassification` wyliczenia:

| Klasyfikacja    | Znaczenie           | Zalecana obsługa |
|-------------------|-------------------|----------------------|
| PodstawoweAkcje | Warunek można rozwiązać przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Wywołaj AcquireTokenInteractively(). |
| Dodatkowa akcją | Warunek można rozwiązać przez dodatkową interakcję korygułą z systemem, poza przepływem uwierzytelniania interaktywnego. | Wywołanie AcquireTokenInteractively(), aby wyświetlić komunikat wyjaśniający działania naprawcze. Aplikacja wywołująca może zdecydować się na ukrycie przepływów, które wymagają additional_action, jeśli jest mało prawdopodobne, aby użytkownik wykonać akcję korygującej. |
| KomunikatOnly      | W tej chwili nie można rozwiązać warunku. Uruchomienie interaktywnego przepływu uwierzytelniania wyświetli komunikat wyjaśniający warunek. | Wywołanie AcquireTokenInteractively(), aby wyświetlić komunikat wyjaśniający warunek. AcquireTokenInteractively() zwróci userCanceled błąd po użytkownik czyta komunikat i zamyka okno. Aplikacja wywołująca może zdecydować się na ukrycie przepływów, które powodują message_only, jeśli jest mało prawdopodobne, aby użytkownik skorzystał z wiadomości.|
| Wymagana zgoda  | Brak zgody użytkownika lub została odwołana. | Zadzwoń do AcquireTokenInteractively(), aby użytkownik wyraził zgodę. |
| UserPasswordExpired | Hasło użytkownika wygasło. | Wywołaj acquireTokenInteractively(), aby użytkownik mógł zresetować swoje hasło. |
| PromptNeverFailed (Nieupowiedził)| Interaktywne uwierzytelnianie zostało wywołane z parametrem prompt=never, zmuszając MSAL do polegania na plikach cookie przeglądarki i nie wyświetlania przeglądarki. To się nie udało. | Wywołaj AcquireTokenInteractively() bez prompt.none |
| AcquireTokenSilentFailed | Zestaw MSAL SDK nie ma wystarczającej ilości informacji, aby pobrać token z pamięci podręcznej. Może to być spowodowane tym, że w pamięci podręcznej nie ma żadnych tokenów lub nie znaleziono konta. Komunikat o błędzie zawiera więcej szczegółów.  | Wywołaj AcquireTokenInteractively(). |
| Brak    | Dalsze szczegóły nie są podane. Warunek może być rozwiązany przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Wywołaj AcquireTokenInteractively(). |

## <a name="net-code-example"></a>Przykład kodu platformy .NET

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js udostępnia obiekty błędów, które abstrakcyjne i klasyfikują różne typy typowych błędów. Zapewnia również interfejs, aby uzyskać dostęp do szczegółowych informacji o błędach, takich jak komunikaty o błędach do ich obsługi odpowiednio.

### <a name="error-object"></a>Obiekt błędu

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

Rozszerzając klasę błędów, masz dostęp do następujących właściwości:
- `AuthError.message`: Taki `errorMessage`sam jak w .
- `AuthError.stack`: Ślad stosu dla zgłoszonych błędów.

### <a name="error-types"></a>Typy błędów

Dostępne są następujące typy błędów:

- `AuthError`: Podstawowa klasa błędów dla biblioteki MSAL.js, również używana do błędów nieoczekiwanych.

- `ClientAuthError`: Klasa error, która oznacza problem z uwierzytelnianiem klienta. Większość błędów, które pochodzą z biblioteki będzie ClientAuthErrors. Błędy te wynikają z rzeczy, takich jak wywołanie metody logowania, gdy logowanie jest już w toku, użytkownik anuluje logowania i tak dalej.

- `ClientConfigurationError`: Error class, `ClientAuthError` extends thrown before requests are made when the given user config parameters are malformed or missing.

- `ServerError`: Klasa Error reprezentuje ciągi błędów wysyłane przez serwer uwierzytelniania. Mogą to być błędy, takie jak nieprawidłowe formaty lub parametry żądań lub wszelkie inne błędy uniemożliwiające serwerowi uwierzytelnianie lub autoryzowanie użytkownika.

- `InteractionRequiredAuthError`: Klasa Error, `ServerError` rozszerza się do reprezentowania błędów serwera, które wymagają wywołania interaktywnego. Ten błąd jest `acquireTokenSilent` generowany przez jeśli użytkownik jest wymagany do interakcji z serwerem w celu podania poświadczeń lub zgody na uwierzytelnianie/autoryzację. Kody `"interaction_required"`błędów obejmują , `"login_required"`i `"consent_required"`.

W przypadku obsługi błędów w`loginRedirect` `acquireTokenRedirect`przepływach uwierzytelniania za pomocą metod przekierowania ( , ), należy `handleRedirectCallback()` zarejestrować wywołanie zwrotne, które jest wywoływane z powodzeniem lub niepowodzeniem po przekierowaniu przy użyciu metody w następujący sposób:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Metody wyskakujących`loginPopup`doświadczeń `acquireTokenPopup`( , ) zwraca obietnice, dzięki czemu można użyć wzorca obietnicy (.then i .catch) do obsługi ich w sposób pokazany:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Błędy wymagające interakcji

Błąd jest zwracany podczas próby użycia nieinteraktywnej `acquireTokenSilent`metody pozyskiwania tokenu, takiej jak , ale msal nie mógł tego zrobić po cichu.

Możliwe przyczyny to:

- musisz się zalogować
- musisz wyrazić zgodę
- musisz przejść przez środowisko uwierzytelniania wieloskładnikowego.

Środki zaradcze polega na wywołaniu `acquireTokenPopup` `acquireTokenRedirect`metody interaktywnej, takiej jak:

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

## <a name="python"></a>[Python](#tab/python)

W msal dla języka Python większość błędów są przekazywane jako wartość zwracana z wywołania interfejsu API. Błąd jest reprezentowany jako słownik zawierający odpowiedź JSON z platformy tożsamości firmy Microsoft.

* Pomyślna odpowiedź `"access_token"` zawiera klucz. Format odpowiedzi jest zdefiniowany przez protokół OAuth2. Aby uzyskać więcej informacji, zobacz [5.1 Pomyślna odpowiedź](https://tools.ietf.org/html/rfc6749#section-5.1)
* Odpowiedź na `"error"` błąd `"error_description"`zawiera i zwykle . Format odpowiedzi jest zdefiniowany przez protokół OAuth2. Aby uzyskać więcej informacji, zobacz [5.2 Odpowiedź na błąd](https://tools.ietf.org/html/rfc6749#section-5.2)

Po zwróceniu błędu `"error_description"` klucz zawiera komunikat czytelny dla człowieka; który z kolei zazwyczaj zawiera kod błędu platformy tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje na temat różnych kodów błędów, zobacz [Kody błędów uwierzytelniania i autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes).

W msal dla języka Python wyjątki są rzadkie, ponieważ większość błędów są obsługiwane przez zwrócenie wartości błędu. Wyjątek `ValueError` jest zgłaszany tylko wtedy, gdy występuje problem z próbą użycia biblioteki — na przykład gdy parametry interfejsu API są zniekształcone.

## <a name="java"></a>[Java](#tab/java)

W msal dla javy istnieją trzy `MsalClientException`rodzaje `MsalServiceException`wyjątków: , , i `MsalInteractionRequiredException`; wszystkie, które `MsalException`dziedziczą po .

- `MsalClientException`jest generowany, gdy wystąpi błąd, który jest lokalny w bibliotece lub urządzeniu.
- `MsalServiceException`jest generowany, gdy usługa bezpiecznego tokenu (STS) zwraca odpowiedź na błąd lub występuje inny błąd sieciowy.
- `MsalInteractionRequiredException`jest generowany, gdy interakcja interfejsu użytkownika jest wymagana do uwierzytelniania, aby zakończyć się pomyślnie.

### <a name="msalserviceexception"></a>MsalServiceException (Nieekceptiona msalserviceexception)

`MsalServiceException`udostępnia nagłówki HTTP zwrócone w żądaniach do STS. Dostęp do nich za pośrednictwem`MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException (Wymagana ekspercja msalinteractionexception)

Jednym ze wspólnych kodów stanu zwróconych `AcquireTokenSilently()` `InvalidGrantError`z MSAL dla Języka Java podczas wywoływania jest . Oznacza to, że dodatkowa interakcja użytkownika jest wymagana przed wystawieniem tokenu uwierzytelniania. Aplikacja powinna wywołać bibliotekę uwierzytelniania ponownie, `AuthorizationCodeParameters` `DeviceCodeParameters` ale w trybie interaktywnym, wysyłając lub dla publicznych aplikacji klienckich.

W większości przypadków, gdy `AcquireTokenSilently` kończy się niepowodzeniem, to dlatego, że pamięć podręczna tokenu nie ma tokenu pasującego do żądania. Tokeny dostępu wygasają `AcquireTokenSilently` w ciągu jednej godziny i spróbuje uzyskać nowy na podstawie tokenu odświeżania. W terminach OAuth2 jest to przepływ tokenu odświeżania. Ten przepływ może również zakończyć się niepowodzeniem z różnych powodów, takich jak gdy administrator dzierżawy konfiguruje bardziej rygorystyczne zasady logowania.

Niektóre warunki, które powodują ten błąd są łatwe do rozwiązania dla użytkowników. Na przykład może być konieczne zaakceptowanie Warunków użytkowania. A może żądanie nie może być spełnione przy bieżącej konfiguracji, ponieważ komputer musi połączyć się z określoną siecią firmową.

MSAL udostępnia `reason` pole, którego można użyć, aby zapewnić lepsze środowisko użytkownika. Na przykład `reason` pole może prowadzić do poinformowania użytkownika, że jego hasło wygasło lub że będzie musiał wyrazić zgodę na korzystanie z niektórych zasobów. Obsługiwane wartości są częścią `InteractionRequiredExceptionReason` wyliczenia:

| Przyczyna | Znaczenie | Zalecana obsługa |
|---------|-----------|-----------------------------|
| `BasicAction` | Warunek można rozwiązać przez interakcję użytkownika podczas interaktywnego przepływu uwierzytelniania | Połączenie `acquireToken` z parametrami interaktywnymi |
| `AdditionalAction` | Warunek można rozwiązać przez dodatkową interakcję korygułą z systemem poza przepływem uwierzytelniania interaktywnego. | Wywołanie `acquireToken` z parametrów interaktywnych, aby wyświetlić komunikat, który wyjaśnia działania naprawcze do podjęcia. Aplikacja wywołująca może zdecydować się na ukrycie przepływów, które wymagają dodatkowej akcji, jeśli jest mało prawdopodobne, aby użytkownik wykonać akcję korygującej. |
| `MessageOnly` | W tej chwili nie można rozwiązać warunku. Uruchom przepływ uwierzytelniania interaktywnego, aby wyświetlić komunikat wyjaśniający warunek. | Wywołanie `acquireToken` z parametrów interaktywnych, aby wyświetlić komunikat, który wyjaśnia warunek. `acquireToken`zwróci błąd `UserCanceled` po odczytyniu wiadomości przez użytkownika i zamknięciu okna. Aplikacja może zdecydować się na ukrycie przepływów, które powodują komunikat, jeśli jest mało prawdopodobne, aby użytkownik skorzystał z wiadomości. |
| `ConsentRequired`| Brak zgody użytkownika lub została odwołana. |Zadzwoń `acquireToken` z parametrami interaktywnymi, aby użytkownik mógł wyrazić zgodę. |
| `UserPasswordExpired` | Hasło użytkownika wygasło. | Zadzwoń `acquireToken` z parametrem interaktywnym, aby użytkownik mógł zresetować swoje hasło |
| `None` |  Szczegółowe informacje podano. Warunek może zostać rozwiązany przez interakcję użytkownika podczas przepływu uwierzytelniania interaktywnego. | Połączenie `acquireToken` z parametrami interaktywnymi |

### <a name="code-example"></a>Przykład kodu

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

Pełna lista błędów MSAL dla systemu iOS i macOS jest wymieniona w [wenięcie MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Wszystkie błędy wywoływane przez `MSALErrorDomain` msal są zwracane z domeną.

W przypadku błędów systemowych `NSError` msal zwraca oryginał z interfejsu API systemu. Na przykład jeśli pobieranie tokenu nie powiedzie się z powodu braku `NSURLErrorDomain` łączności `NSURLErrorNotConnectedToInternet` sieciowej, msal zwraca błąd z domeną i kodem.

Zaleca się obsługę co najmniej dwóch następujących błędów MSAL po stronie klienta:

- `MSALErrorInteractionRequired`: Użytkownik musi wykonać interaktywne żądanie. Istnieje wiele warunków, które mogą prowadzić do tego błędu, takich jak wygasła sesja uwierzytelniania lub potrzeba dodatkowych wymagań uwierzytelniania. Wywołanie interfejsu API nabycia interaktywnego tokenu MSAL w celu odzyskania. 

- `MSALErrorServerDeclinedScopes`: Niektóre lub wszystkie zakresy zostały odrzucone. Zdecyduj, czy kontynuować tylko przyznane zakresy, czy zatrzymać proces logowania.

> [!NOTE]
> Wyliczenia `MSALInternalError` powinny być używane tylko do odwołania i debugowania. Nie próbuj automatycznie obsługiwać tych błędów w czasie wykonywania. Jeśli aplikacja napotka którykolwiek z `MSALInternalError`błędów, które wchodzą w zakres , można wyświetlić ogólny użytkownik stoi komunikat wyjaśniający, co się stało.

Oznacza to `MSALInternalErrorBrokerResponseNotReceived` na przykład, że użytkownik nie ukończył uwierzytelniania i ręcznie zwrócił do aplikacji. W takim przypadku aplikacja powinna wyświetlać ogólny komunikat o błędzie wyjaśniający, że uwierzytelnianie nie zostało ukończone i sugerować, że spróbuje się ponownie uwierzytelnić.

Poniższy przykładowy kod Objective-C pokazuje najlepsze rozwiązania dotyczące obsługi niektórych typowych warunków błędu:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Dostęp warunkowy i wyzwania związane z roszczeniami

Podczas uzyskiwania tokenów w trybie dyskretnym aplikacja może otrzymywać błędy, gdy [wyzwanie oświadczeń dostępu warunkowego,](../azuread-dev/conditional-access-dev-guide.md) takie jak zasady usługi MFA, jest wymagane przez interfejs API, do którego próbujesz uzyskać dostęp.

Wzorzec obsługi tego błędu jest interaktywnie uzyskać token przy użyciu msal. Interaktywne pobieranie tokenu monituje użytkownika i daje mu możliwość spełnienia wymaganych zasad dostępu warunkowego.

W niektórych przypadkach podczas wywoływania interfejsu API wymagającego dostępu warunkowego, można otrzymać wyzwanie roszczeń w błędzie z interfejsu API. Jeśli na przykład zasady dostępu warunkowego mają mieć urządzenie zarządzane (Intune), błąd będzie podobny do [AADSTS53000: Urządzenie musi być zarządzane w celu uzyskania dostępu do tego zasobu](reference-aadsts-error-codes.md) lub czegoś podobnego. W takim przypadku można przekazać oświadczenia w wywołaniu tokenu nabycia, tak aby użytkownik został poproszony o spełnienie odpowiednich zasad.

### <a name="net"></a>.NET

Podczas wywoływania interfejsu API wymagającego dostępu warunkowego od MSAL.NET aplikacja będzie musiała obsługiwać wyjątki od żądań. Spowoduje to wyświetlenie jako [MsalServiceException,](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) gdzie [Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) właściwość nie będzie pusta.

Aby obsłużyć żądanie roszczenia, `.WithClaim()` musisz użyć `PublicClientApplicationBuilder` metody klasy.

### <a name="javascript"></a>JavaScript

Podczas uzyskiwania tokenów `acquireTokenSilent`po cichu (przy użyciu) przy użyciu msal.js, aplikacja może otrzymywać błędy, gdy [wyzwanie oświadczeń dostępu warunkowego,](../azuread-dev/conditional-access-dev-guide.md) takie jak zasady usługi MFA jest wymagane przez interfejs API, który próbujesz uzyskać dostęp.

Wzorzec do obsługi tego błędu jest do interaktywnego wywołania uzyskania `acquireTokenPopup` `acquireTokenRedirect` tokenu w MSAL.js, takich jak lub jak w poniższym przykładzie:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

Interaktywne pobieranie tokenu monituje użytkownika i daje mu możliwość spełnienia wymaganych zasad dostępu warunkowego.

Podczas wywoływania interfejsu API wymagającego dostępu warunkowego, można otrzymać wyzwanie roszczeń w błędzie z interfejsu API. W takim przypadku można przekazać oświadczenia zwrócone `claimsRequest` w błędzie `AuthenticationParameters.ts` do pola klasy, aby spełnić odpowiednie zasady. 

Aby uzyskać więcej informacji, zobacz [Żądanie dodatkowych roszczeń.](active-directory-optional-claims.md)

### <a name="msal-for-ios-and-macos"></a>Biblioteka MSAL dla systemów iOS i macOS

Msal dla systemu iOS i macOS umożliwia żądanie określonych oświadczeń w scenariuszach nabycia tokenów interaktywnych i cichych.

Aby zażądać oświadczeń `claimsRequest` `MSALSilentTokenParameters` niestandardowych, należy określić w lub `MSALInteractiveTokenParameters`.

Aby uzyskać więcej informacji, zobacz [Żądanie oświadczeń niestandardowych przy użyciu usługi MSAL dla systemu iOS i macOS.](request-custom-claims.md)

## <a name="retrying-after-errors-and-exceptions"></a>Ponowienie próby po błędach i wyjątkach

Oczekuje się, że zaimplementujesz własne zasady ponawiania podczas wywoływania usługi MSAL. MSAL wywołuje protokół HTTP z usługą AAD i mogą wystąpić sporadyczne awarie, na przykład sieć może upaść lub serwer jest przeciążony.  

### <a name="http-error-codes-500-600"></a>Kody błędów HTTP 500-600

MSAL.NET implementuje prosty mechanizm ponawiania błędów z kodami błędów HTTP 500-600.

### <a name="http-429"></a>HTTP 429

Gdy serwer tokenu usługi (STS) jest przeciążony zbyt dużą ą ą liczbę żądań, zwraca błąd `Retry-After` HTTP 429 z podpowiedzią o tym, jak długo można spróbować ponownie w polu odpowiedzi.

### <a name="net"></a>.NET

`System.Net.Http.Headers.HttpResponseHeaders` [Powierzchnie MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) jako `namedHeaders`właściwość . Można użyć dodatkowych informacji z kodu błędu, aby zwiększyć niezawodność aplikacji. W opisanym przypadku można użyć `RetryAfterproperty` (typu) `RetryConditionHeaderValue`i obliczyć, kiedy ponowić próbę.

Oto przykład dla aplikacji demona przy użyciu przepływu poświadczeń klienta. Można dostosować to do dowolnej z metod uzyskiwania tokenu.

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
