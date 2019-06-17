---
title: Konfigurowanie zmiany hasła, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak umożliwić użytkownikom zmienić swoje hasło, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a49f62b6fc1ea00084266d4c5405f8bf96d034cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509257"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie zmiany hasła, za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W usłudze Azure Active Directory (Azure AD) B2C aby umożliwić użytkowników, którzy są podpisane za pomocą konta lokalnego zmiany hasła bez konieczności potwierdzenia autentyczności ich przez Weryfikacja adresu e-mail. Jeśli sesja wygaśnie przez razem, gdy użytkownik uzyskuje do hasła, zmień przepływ, otrzymuje monit Zaloguj się ponownie. W tym artykule dowiesz się, jak skonfigurować zmianę hasła w [zasady niestandardowe](active-directory-b2c-overview-custom.md). Istnieje również możliwość skonfigurowania [samoobsługowego resetowania haseł](active-directory-b2c-reference-sspr.md) przepływów użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki [wprowadzenie do zasad niestandardowych w Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="add-the-elements"></a>Dodawanie elementów 

1. Otwórz swoje *TrustframeworkExtensions.xml* pliku i Dodaj następujący kod **oświadczenia** elementu z identyfikatorem `oldPassword` do [ClaimsSchema](claimsschema.md) elementu: 

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. A [ClaimsProvider](claimsproviders.md) element zawiera profilu technicznego, który uwierzytelnia użytkownika. Dodaj następujące oświadczeń dostawców w celu **ClaimsProviders** elementu:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Zastąp `IdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji IdentityExperienceFramework, który został utworzony w samouczka dotyczącego wymagań wstępnych. Zastąp `ProxyIdentityExperienceFrameworkAppId` identyfikatorem aplikacji również wcześniej utworzoną aplikację ProxyIdentityExperienceFramework.

3. [UserJourney](userjourneys.md) element Określa ścieżkę, który użytkownik przyjmuje podczas interakcji z aplikacją. Dodaj **podróży użytkowników** elementu, jeśli nie istnieje on **UserJourney** zidentyfikowane jako `PasswordChange`:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Zapisz *TrustFrameworkExtensions.xml* plik zasad.
5. Kopiuj *ProfileEdit.xml* pliku pobrany z pakietu startowego, a następnie nadaj mu nazwę *ProfileEditPasswordChange.xml*.
6. Otwórz nowy plik i zaktualizuj **PolicyId** atrybutu przy użyciu unikatowej wartości. Ta wartość jest nazwę swoich zasad. Na przykład *B2C_1A_profile_edit_password_change*.
7. Modyfikowanie **ReferenceId** atrybutu w `<DefaultUserJourney>` aby dopasować identyfikator nowego podróży użytkownika, który został utworzony. Na przykład *PasswordChange*.
8. Zapisz zmiany.

Można znaleźć zasad przykładowe [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>Testowanie zasad

Podczas testowania aplikacji w usłudze Azure AD B2C, może być przydatne do ma tokenu usługi Azure AD B2C, powrót do `https://jwt.ms` aby mieć możliwość przejrzenia oświadczeń w nim.

### <a name="upload-the-files"></a>Przekaż pliki

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu zawierającego Twoją dzierżawę usługi Azure AD B2C, klikając pozycję **Filtr katalogu i subskrypcji** w górnym menu i wybierając katalog zawierający Twoją dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **struktura środowiska tożsamości**.
5. Na stronie zasad niestandardowych kliknij **zasady przekazywania**.
6. Wybierz **Zastąp zasady Jeśli istnieje**, a następnie wyszukaj i wybierz pozycję *TrustframeworkExtensions.xml* pliku.
7. Kliknij pozycję **Przekaż**.
8. Powtórz kroki od 5 do 7 dla jednostki uzależnionej pliku innych firm, takich jak *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zasady, który został zmodyfikowany. Na przykład *B2C_1A_profile_edit_password_change*.
2. Aby uzyskać **aplikacji**, wybierz swoją aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token, **adres URL odpowiedzi** powinien być wyświetlony `https://jwt.ms`.
3. Kliknij pozycję **Uruchom teraz**. Zaloguj się przy użyciu acouunt wcześniej utworzony. Teraz masz możliwość zmiany hasła. 

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o tym, jak [skonfigurowania złożoności hasła jako za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md). 
