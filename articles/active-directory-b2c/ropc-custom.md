---
title: Konfiguruj przepływ poświadczeń hasła właściciela zasobu w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować przepływ poświadczeń hasła właściciela zasobu w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d86caf5e5c6df29e00f17462f6a06602ff1245d8
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64688860"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Konfigurowanie zasobów przepływ poświadczeń hasła właściciela w usłudze Azure Active Directory B2C za pomocą zasad niestandardowych

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

W usłudze Azure Active Directory (Azure AD) B2C zasobów przepływ poświadczeń hasła właściciela (ROPC) jest przepływ standardowego uwierzytelniania OAuth. W tym przepływie aplikacji, znany także jako podmiotu zależnego wymienia prawidłowe poświadczenia dla tokenów. Poświadczenia obejmują nazwę użytkownika i hasło. Tokeny, zwracane są tokenu identyfikator, token dostępu i token odświeżania.

W usłudze flow ROPC obsługiwane są następujące opcje:

- **Natywny klient** -interakcji z użytkownikiem podczas uwierzytelniania się dzieje, gdy kod jest uruchamiany na urządzeniu po stronie użytkownika.
- **Przepływ klienta publicznego** — tylko poświadczenia użytkownika, które są zbierane przez aplikację są wysyłane w wywołaniu interfejsu API. Poświadczenia aplikacji nie są wysyłane.
- **Dodawanie nowych oświadczeń** — do dodawania nowych oświadczeń, można zmienić identyfikator zawartości tokenu.

Następujące przepływy nie są obsługiwane:

- **Serwer serwer** — system ochrony tożsamości musi niezawodne adres IP zgromadzone od elementu wywołującego (natywny klient) w ramach interakcji. W wywołaniu interfejsu API po stronie serwera jest używany tylko adres IP serwera. Jeśli zbyt wiele operacji logowania zakończy się niepowodzeniem, system ochrony tożsamości może Przyjrzyj się dopuszczalnych adresów IP jako osoba atakująca.
- **Pojedyncza strona aplikacji** -aplikacji frontonu, która jest głównie napisanych w JavaScript. Często aplikacji są zapisywane przy użyciu struktury, takich jak AngularJS, Ember.js lub Durandal.
- **Przepływ poufne klienta** — identyfikator klienta aplikacji jest weryfikowane, ale nie jest klucz tajny aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Rejestrowanie aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
5. Wprowadź nazwę aplikacji, taką jak *ROPC_Auth_app*.
6. Wybierz **nie** dla **interfejsu API sieci Web i aplikacji sieci Web**, a następnie wybierz pozycję **tak** dla **Native client**.
7. Pozostaw inne wartości, ponieważ są, a następnie wybierz **Utwórz**.
8. Wybierz nową aplikację, a następnie zapisz identyfikator aplikacji w celu późniejszego użycia.

##  <a name="create-a-resource-owner-policy"></a>Utwórz zasady właściciela zasobu

1. Otwórz *TrustFrameworkExtensions.xml* pliku.
2. Jeśli nie istnieje już, Dodaj **ClaimsSchema** elementu i jego elementy podrzędne jako pierwszy element w obszarze **BuildingBlocks** elementu:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Po **ClaimsSchema**, Dodaj **ClaimsTransformations** elementu i jego elementów podrzędnych do **BuildingBlocks** elementu:

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Znajdź **ClaimsProvider** element, który ma **DisplayName** z `Local Account SignIn` i dodanie następujących profilu technicznego:

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Zastąp **DefaultValue** z **client_id** i **Identyfikator_zasobu_2** z Identyfikatorem aplikacji w aplikacji ProxyIdentityExperienceFramework, który został utworzony w samouczka dotyczącego wymagań wstępnych.

5. Dodaj następujące **ClaimsProvider** elementów przy użyciu ich profile techniczne do **ClaimsProviders** elementu:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Dodaj **podróży użytkowników** elementu i jego elementów podrzędnych do **elementu TrustFrameworkPolicy** elementu:

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Na **zasady niestandardowe** strony w swojej dzierżawie usługi Azure AD B2C, wybierz opcję **zasady przekazywania**.
8. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
9. Kliknij pozycję **Przekaż**.

## <a name="create-a-relying-party-file"></a>Utwórz plik strony jednostki uzależnionej

Następnie zaktualizuj plik strony jednostki uzależnionej inicjuje podróży użytkownika, który został utworzony:

1. Utwórz kopię *SignUpOrSignin.xml* plik w katalogu roboczym i zmień jej nazwę na *ROPC_Auth.xml*.
2. Otwórz nowy plik i zmień wartość **PolicyId** atrybutu dla **elementu TrustFrameworkPolicy** do unikatowej wartości. Identyfikator zasad jest nazwę swoich zasad. Na przykład **B2C_1A_ROPC_Auth**.
3. Zmień wartość właściwości **ReferenceId** atrybutu w **DefaultUserJourney** do `ResourceOwnerPasswordCredentials`.
4. Zmiana **OutputClaims** elementu, aby zawierała tylko następujące oświadczeń:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Na **zasady niestandardowe** strony w swojej dzierżawie usługi Azure AD B2C, wybierz opcję **zasady przekazywania**.
6. Włącz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustFrameworkExtensions.xml* pliku.
7. Kliknij pozycję **Przekaż**.

## <a name="test-the-policy"></a>Testowanie zasad

Generowanie wywołanie interfejsu API za pomocą ulubionego interfejsu API aplikacji rozwoju i zapoznać się z odpowiedzią do debugowania zasady. Utworzyć wywołania, tak jak ten przykład, używając następujących informacji jako treść żądania POST:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C.
- Zastąp `B2C_1A_ROPC_Auth` pełną nazwą zasady poświadczeń hasła właściciela zasobu.

| Klucz | Wartość |
| --- | ----- |
| username | `user-account` |
| password | `password1` |
| grant_type | password |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | id_token tokenu |

- Zastąp `user-account` o nazwie konta użytkownika w dzierżawie.
- Zastąp `password1` przy użyciu hasła konta użytkownika.
- Zastąp `application-id` identyfikatorem aplikacji z *ROPC_Auth_app* rejestracji.
- *Offline_access* jest opcjonalne, jeśli chcesz otrzymywać token odświeżania.

Rzeczywiste żądanie POST wygląda następująco:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Pomyślnej odpowiedzi z dostęp w trybie offline będzie wyglądać następująco:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Realizowanie tokenu odświeżania

Skonstruuj wywołanie metody POST, tak jak pokazano poniżej. Użyj informacji w poniższej tabeli jako treść żądania:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Zastąp `your-tenant-name` nazwą dzierżawy usługi Azure AD B2C.
- Zastąp `B2C_1A_ROPC_Auth` pełną nazwą zasady poświadczeń hasła właściciela zasobu.

| Klucz | Wartość |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Zastąp `application-id` identyfikatorem aplikacji z *ROPC_Auth_app* rejestracji.
- Zastąp `refresh-token` z **refresh_token** który została wysłana w poprzedniej odpowiedzi.

Odpowiedź oznaczająca Powodzenie będzie wyglądać następująco:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Za pomocą natywnego zestawu SDK lub uwierzytelniania aplikacji

Usługa Azure AD B2C spełnia standardy poświadczenia hasła właściciela zasobu publicznych klienta OAuth 2.0 i powinien być zgodny z większość zestawów SDK klienta. Aby uzyskać najnowsze informacje, zobacz [natywnego zestawu SDK aplikacji OAuth 2.0 i OpenID Connect, implementacja nowoczesnych najlepsze rozwiązania](https://appauth.io/).

## <a name="next-steps"></a>Kolejne kroki

- Zobacz pełny przykład tego scenariusza w [pakiet startowy niestandardowe zasady usługi Azure Active Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Dowiedz się więcej o tokenów, które są używane przez usługi Azure Active Directory B2C w [Token odwołania](active-directory-b2c-reference-tokens.md).
