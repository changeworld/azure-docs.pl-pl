---
title: Konfigurowanie zmiany hasła przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak umożliwić użytkownikom zmianę hasła przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2c351f8a95110a32c53c68c5eb6095918578bc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189178"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie zmiany hasła przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W usłudze Azure Active Directory B2C (Azure AD B2C) można umożliwić użytkownikom zalogowanym za pomocą konta lokalnego zmianę hasła bez konieczności potwierdzania ich autentyczności za pomocą weryfikacji poczty e-mail. Jeśli sesja wygaśnie do czasu, gdy użytkownik dotrze do przepływu zmiany hasła, zostanie wyświetlony monit o ponowne zalogowanie się. W tym artykule pokazano, jak skonfigurować zmianę hasła w [zasadach niestandardowych](custom-policy-overview.md). Istnieje również możliwość skonfigurowania [samoobsługowego resetowania hasła](user-flow-self-service-password-reset.md) dla przepływów użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych w usłudze Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>Dodawanie elementów

1. Otwórz plik *TrustframeworkExtensions.xml* i dodaj następujący element **ClaimType** z identyfikatorem `oldPassword` elementu [ClaimsSchema:](claimsschema.md)

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

2. A [ClaimsProvider](claimsproviders.md) element zawiera profil techniczny, który uwierzytelnia użytkownika. Dodaj następujących dostawców oświadczeń do **claimsproviders** elementu:

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

    Zamień `IdentityExperienceFrameworkAppId` na identyfikator aplikacji IdentityExperienceFramework, który został utworzony w samouczku warunek wstępny. Zamień `ProxyIdentityExperienceFrameworkAppId` identyfikator aplikacji aplikacji ProxyIdentityExperienceFramework, który został wcześniej utworzony.

3. [UserJourney](userjourneys.md) Element definiuje ścieżkę, którą użytkownik wykonuje podczas interakcji z aplikacją. Dodaj **userjourneys** element, jeśli nie istnieje z **UserJourney** zidentyfikowane jako: `PasswordChange`

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

4. Zapisz plik zasad *TrustFrameworkExtensions.xml.*
5. Skopiuj pobrany plik *ProfileEdit.xml* wraz z pakietem startowym i nadaj jego nazwę *ProfileEditPasswordChange.xml*.
6. Otwórz nowy plik i zaktualizuj atrybut **PolicyId** o unikatową wartość. Ta wartość jest nazwą zasad. Na przykład *B2C_1A_profile_edit_password_change*.
7. Zmodyfikuj `<DefaultUserJourney>` atrybut **ReferenceId,** aby dopasować identyfikator nowego środowiska podróży użytkownika, który został utworzony. Na przykład *PasswordChange*.
8. Zapisz zmiany.

Przykładowe zasady można znaleźć [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).

## <a name="test-your-policy"></a>Testowanie zasad

Podczas testowania aplikacji w usłudze Azure AD B2C może być przydatne, `https://jwt.ms` aby token usługi Azure AD B2C został zwrócony, aby móc przeglądać oświadczenia w nim.

### <a name="upload-the-files"></a>Przekazywanie plików

1. Zaloguj się do [Portalu Azure](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD B2C, wybierając filtr **subskrypcja katalog +** w górnym menu i wybierając katalog zawierający dzierżawę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **platformę środowiska tożsamości**.
5. Na stronie Zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz **opcję Zastąp zasadę, jeśli istnieje,** a następnie wyszukaj i wybierz plik *TrustframeworkExtensions.xml.*
7. Kliknij pozycję **Przekaż**.
8. Powtórz kroki od 5 do 7 dla pliku jednostki uzależnionej, na przykład *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zmienione zasady. Na przykład *B2C_1A_profile_edit_password_change*.
2. W **przypadku aplikacji**wybierz aplikację, która została wcześniej zarejestrowana. Aby wyświetlić token, powinien `https://jwt.ms`być wyświetlony **adres URL odpowiedzi** .
3. Kliknij pozycję **Uruchom teraz**. Zaloguj się przy tym, że wcześniej utworzono acouunt. Teraz powinieneś mieć możliwość zmiany hasła.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować złożoność haseł przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C](custom-policy-password-complexity.md).
