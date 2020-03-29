---
title: Usługa Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o konkretnych zagadnieniach podczas korzystania z usługi Azure AD B2C z biblioteką uwierzytelniania firmy Microsoft dla systemu Android (MSAL). Android)
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 0998bb04b0dfc69db4696f2e390cfe259eba6718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696525"
---
# <a name="use-msal-for-android-with-b2c"></a>Użyj MSAL dla Androida z B2C

Biblioteka uwierzytelniania firmy Microsoft (MSAL) umożliwia deweloperom aplikacji uwierzytelnianie użytkowników przy użyciu tożsamości społecznościowych i lokalnych przy użyciu [usługi Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Usługa Azure AD B2C to usługa zarządzania tożsamościami. Służy do dostosowywania i kontrolowania sposobu, w jaki klienci się logują, logują się i zarządzają swoimi profilami podczas korzystania z aplikacji.

## <a name="configure-known-authorities-and-redirect-uri"></a>Konfigurowanie znanych urzędów i przekierowywanie identyfikatora URI

W msal dla systemu Android zasady B2C (podróże użytkownika) są skonfigurowane jako poszczególne urzędy.

Biorąc pod uwagę aplikację B2C, która ma dwie zasady:
- Rejestracja / Logowanie
    * Wywoływana`B2C_1_SISOPolicy`
- Edytuj profil
    * Wywoływana`B2C_1_EditProfile`

Plik konfiguracyjny dla `authorities`aplikacji deklaruje dwa . Po jednym dla każdej zasady. Własnością `type` każdego organu `B2C`jest .

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

Musi `redirect_uri` być zarejestrowany w konfiguracji aplikacji, `AndroidManifest.xml` a także w celu obsługi przekierowania podczas [przepływu udzielania kodu autoryzacji](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code).

## <a name="initialize-ipublicclientapplication"></a>Inicjowanie aplikacji IPublicClientApplication

`IPublicClientApplication`jest skonstruowany przez metodę fabryczną, aby umożliwić konfigurację aplikacji do analizy asynchronicznie.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Interaktywne nabycie tokenu

Aby uzyskać token interaktywnie z MSAL, skompiluj wystąpienie `AcquireTokenParameters` i podaj `acquireToken` go do metody. Poniższe żądanie tokenu `default` korzysta z urzędu.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Ciche odnawianie tokenu

Aby uzyskać token dyskretnie z `AcquireTokenSilentParameters` MSAL, skompiluj wystąpienie i podaj `acquireTokenSilentAsync` go do metody. W `acquireToken` przeciwieństwie do `authority` metody, musi być określony, aby uzyskać token dyskretnie.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Określanie zasad

Ponieważ zasady w B2C są reprezentowane jako oddzielne urzędy, wywoływanie zasad `fromAuthority` innych niż `acquireToken` domyślne jest osiągane przez określenie klauzuli podczas konstruowania lub `acquireTokenSilent` parametrów.  Przykład:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Obsługa zasad zmiany hasła

Przepływ użytkownika rejestracji lub logowania na koncie lokalnym pokazuje '**Zapomniałeś hasła?** Link. Kliknięcie tego łącza nie powoduje automatycznego wyzwalania przepływu użytkownika resetowania hasła.

Zamiast tego kod `AADB2C90118` błędu jest zwracany do aplikacji. Aplikacja powinna obsługiwać ten kod błędu, uruchamiając przepływ określonego użytkownika, który resetuje hasło.

Aby złapać kod błędu resetowania hasła, można `AuthenticationCallback`użyć następującej implementacji wewnątrz:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>Użyj funkcji IAuthenticationResult

Pomyślne nabycie tokenu `IAuthenticationResult` powoduje obiekt. Zawiera token dostępu, oświadczenia użytkownika i metadane.

### <a name="get-the-access-token-and-related-properties"></a>Pobierz token dostępu i powiązane właściwości

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Uzyskaj autoryzowane konto

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>Oświadczenia IdToken

Oświadczenia zwrócone w IdToken są wypełniane przez usługę tokenu zabezpieczającego (STS), a nie przez usługę MSAL. W zależności od dostawcy tożsamości (IdP) używane, niektóre oświadczenia mogą być nieobecne. Niektóre dostawcy usług idps obecnie `preferred_username` nie dostarczają roszczenia. Ponieważ to oświadczenie jest używane przez MSAL do buforowania, wartość zastępcza, `MISSING FROM THE TOKEN RESPONSE`jest używana w jego miejsce. Aby uzyskać więcej informacji na temat oświadczeń B2C IdToken, zobacz [Omówienie tokenów w usłudze Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims).

## <a name="managing-accounts-and-policies"></a>Zarządzanie kontami i zasadami

B2C traktuje każdą politykę jako odrębny organ. W związku z tym tokeny dostępu, tokeny odświeżania i tokeny identyfikatora zwrócone z każdej zasady nie są wymienne. Oznacza to, że `IAccount` każda zasada zwraca oddzielny obiekt, którego tokeny nie mogą być używane do wywoływania innych zasad.

Każda zasada `IAccount` dodaje do pamięci podręcznej dla każdego użytkownika. Jeśli użytkownik zaloguje się do aplikacji i wywołuje dwie zasady, będą mieli dwa `IAccount`s. Aby usunąć tego użytkownika z pamięci `removeAccount()` podręcznej, należy wywołać dla każdej zasady.

Podczas odnawiania tokenów dla `acquireTokenSilent`zasad za `IAccount` pomocą , podaj to samo, `AcquireTokenSilentParameters`co zostało zwrócone z poprzednich wywołań zasad do . Podanie konta zwróconego przez inną zasadę spowoduje błąd.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze Azure Active Directory B2C (Azure AD B2C) na [co to jest usługa Azure Active Directory B2C?](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
