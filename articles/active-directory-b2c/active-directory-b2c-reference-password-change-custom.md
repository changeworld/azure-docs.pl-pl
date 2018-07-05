---
title: Zmiany haseł w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Temat pokazująca, jak skonfigurować zmiany haseł przez użytkowników w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 028d10b5c005be2db7cfd9c5ca5210ab55f0592a
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448139"
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Usługa Azure Active Directory B2C: Skonfiguruj zmiany hasła w zasadach niestandardowych  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Przy użyciu funkcji zmiany hasła zalogowanego konsumentów (przy użyciu kont lokalnych) mogą zmieniać hasła bez konieczności potwierdzenia autentyczności ich przez Weryfikacja adresu e-mail, zgodnie z opisem w [samoobsługowego resetowania haseł usługi flow.](active-directory-b2c-reference-sspr.md) Jeśli sesja wygaśnie według czasu pobiera konsumenta hasło przepływ zmian, użytkownik jest monitowany o ponowne zarejestrowanie. 

## <a name="prerequisites"></a>Wymagania wstępne

Dzierżawy usługi Azure AD B2C skonfigurowany tak, aby ukończyć konta lokalnego konta-dokonywania/logowania, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Jak skonfigurować zmianę hasła w zasadach niestandardowych

Aby skonfigurować zmiany hasła w zasadach niestandardowych należy wprowadzić następujące zmiany w zasadach zaufania framework rozszerzenia 

## <a name="define-a-claimtype-oldpassword"></a>Zdefiniuj oświadczenia "oldPassword"

Musi zawierać ogólną strukturę zasad niestandardowych `ClaimsSchema`i zdefiniuj nowy `ClaimType` "oldPassword, tak jak pokazano poniżej, 

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

Przeznaczenie tych elementów jest następująca:

- `ClaimsSchema` Definiuje, które oświadczenia jest weryfikowany.  W takim przypadku zostanie zweryfikowana "stare hasło". 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Dodawanie dostawcy oświadczeń zmiany hasła ze swoimi elementami pomocnicze

Oświadczenia, które będą dostawcy o zmianie hasła

1. Uwierzytelnianie użytkownika przed stare hasło
2. A jeśli 'nowe hasło' pasuje do "Potwierdź nowe hasło", ta wartość jest przechowywana w magazynu danych usługi B2C i dlatego pomyślnie zmieniono hasło. 

![img](images/passwordchange.jpg)

Dodaj następującego dostawcy oświadczeń zasad dotyczących rozszerzeń. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
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



### <a name="add-the-application-ids-to-your-custom-policy"></a>Dodawanie identyfikatorów aplikacji do zdefiniowania zasad niestandardowych

Dodaj identyfikatory aplikacji do pliku rozszerzenia (`TrustFrameworkExtensions.xml`):

1. W pliku rozszerzeń (TrustFrameworkExtensions.xml) można znaleźć elementu `<TechnicalProfile Id="login-NonInteractive">` i `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Zastąp wszystkie wystąpienia zmiennej `IdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md). Oto przykład:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Zastąp wszystkie wystąpienia zmiennej `ProxyIdentityExperienceFrameworkAppId` z Identyfikatorem aplikacji w aplikacji platformy środowiska tożsamości serwera Proxy, zgodnie z opisem w [wprowadzenie](active-directory-b2c-get-started-custom.md).

4. Zapisz plik rozszerzenia.



## <a name="create-a-password-change-user-journey"></a>Utwórz podróży użytkownika Zmień hasło

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

Po zakończeniu modyfikowania pliku rozszerzenia. Zapisz, a następnie przekaż ten plik. Upewnij się, że wszystkie sprawdzenia powiodło się.



## <a name="create-a-relying-party-rp-file"></a>Utwórz plik jednostki uzależnionej strona (RP)

Następnie zaktualizuj plik innych firm (RP) jednostki uzależnionej, który inicjuje podróży użytkownika, który został utworzony:

1. Utwórz kopię ProfileEdit.xml w katalogu roboczym. Następnie zmień jego nazwę (na przykład PasswordChange.xml).
2. Otwórz nowy plik i zaktualizuj `PolicyId` atrybutu dla `<TrustFrameworkPolicy>` przy użyciu unikatowej wartości. To jest nazwa zasady (na przykład PasswordChange).
3. Modyfikowanie `ReferenceId` atrybutu w `<DefaultUserJourney>` do dopasowania `Id` nowe podróży użytkownika, który został utworzony (na przykład PasswordChange).
4. Zapisz zmiany, a następnie przekazać plik.
5. Aby przetestować zasad niestandardowych, który został przekazany, w witrynie Azure portal, przejdź do bloku zasady, a następnie kliknij **Uruchom teraz**.




## <a name="link-to-password-change-sample-policy"></a>Link do przykładowej zmiany hasła

Można znaleźć zasad przykładowe [tutaj](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 










