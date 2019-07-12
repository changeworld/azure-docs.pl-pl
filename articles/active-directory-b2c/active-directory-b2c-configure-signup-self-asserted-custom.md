---
title: Dodawanie oświadczenia i dostosowywanie z użytkownikiem za pomocą zasad niestandardowych — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować dane wejściowe użytkownika i Dodaj oświadczeń dla podróży rejestracji lub logowania w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ae6d55180785c9407662776a95fcba31f8ba5275
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835204"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie oświadczenia i dostosowywanie danych wejściowych za pomocą zasad niestandardowych w usłudze Azure Active Directory B2C użytkownika

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule Dodaj nowy wpis podane przez użytkownika (oświadczenia) do podróż użytkownika rejestracji w usłudze Azure Active Directory (Azure AD) B2C.  Skonfigurować wpis jako listy rozwijanej i określić, czy jest to wymagane.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w artykule [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md). Przetestuj podróży użytkownika rejestracji lub logowania, aby się zarejestrować nowe konto lokalne, przed kontynuowaniem.

## <a name="add-claims"></a>Dodawanie oświadczeń

Gromadzenia danych początkowych użytkowników odbywa się przy użyciu podróży użytkownika rejestracji lub logowania. Dodatkowe oświadczenia, które można później gromadzić przy użyciu podróży użytkownika edycji profilu. W dowolnym momencie usługi Azure AD B2C interaktywnie zbiera informacje bezpośrednio od użytkownika, struktura środowiska tożsamości używa dostawcy samodzielnie.


### <a name="define-the-claim"></a>Zdefiniuj oświadczenia

Możemy poprosić użytkownika dla ich miasta. Dodaj następujący element do **ClaimsSchema** elementu w pliku zasad TrustFrameworkBase:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Następujące elementy są używane do definiowania oświadczenia:

- **DisplayName** — ciąg, który definiuje etykietę widocznych dla użytkownika.
- **UserHelpText** — ułatwia użytkownikom zrozumienie, co jest wymagane.
- **UserInputType** — może być polem tekstowym, wybór opcji, listy rozwijanej lub wybór wielokrotny.

#### <a name="textbox"></a>TextBox

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

![Kontrolka dropdown przedstawiający kilka opcji wybieranych](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

![Kontrolka pola wyboru wielokrotnego wyboru przedstawiający kilka opcji](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="add-the-claim-to-the-user-journey"></a>Dodaj oświadczenie do podróży użytkownika

1. Dodaj oświadczenie jako `<OutputClaim ClaimTypeReferenceId="city"/>` do `LocalAccountSignUpWithLogonEmail` profilu technicznego znajdującą się w pliku zasad TrustFrameworkBase. Ten profil techniczny używa SelfAssertedAttributeProvider.

    ```xml
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
      </CryptographicKeys>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
        <!-- Optional claims, to be collected from the user -->
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surName" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

2. Dodaj oświadczenie do profilu technicznego UserWriteUsingLogonEmail usługi AAD jako `<PersistedClaim ClaimTypeReferenceId="city" />` do zapisania oświadczenia do katalogu usługi AAD po zebraniu go przez użytkownika. Jeśli nie chcesz zachować oświadczenia w katalogu do użytku w przyszłości, możesz pominąć ten krok.

    ```xml
    <!-- Technical profiles for local accounts -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <!-- Required claims -->
        <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
        <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
        <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
        <!-- Optional claims. -->
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />
        <PersistedClaim ClaimTypeReferenceId="city" />
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

3. Dodaj `<OutputClaim ClaimTypeReferenceId="city" />` oświadczenie profile techniczne, które odczytu z katalogu, gdy użytkownik loguje się.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Required claims -->
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

4. Dodaj `<OutputClaim ClaimTypeReferenceId="city" />` oświadczenia do pliku SignUporSignIn.xml tak, aby to oświadczenie jest wysyłane do aplikacji w tokenie po podróży użytkownika pomyślne.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="identityProvider" />
          <OutputClaim ClaimTypeReferenceId="city" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **rejestracje aplikacji**.
4. Wybierz **struktura środowiska tożsamości (wersja zapoznawcza)** .
5. Wybierz **przekazywanie niestandardowych zasadach**, a następnie przekaż pliki dwóch zasad, które zostały zmienione.
2. Wybierz zasady rejestracji lub logowania, które możesz przekazać i kliknąć pozycję **Uruchom teraz** przycisku.
3. Powinien móc zarejestrować się przy użyciu adresu e-mail.

Ekran rejestracji powinien wyglądać mniej więcej tak:

![Zrzut ekranu przedstawiający modyfikacji opcji rejestracji](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

Zawiera token wysyłanych z powrotem do aplikacji `city` oświadczenia.

```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification"></a>Opcjonalnie: Usuń Weryfikacja adresu e-mail

Aby pominąć weryfikację poczty e-mail, możesz usunąć `PartnerClaimType="Verified.Email"`. W takim przypadku adres e-mail jest wymagana, ale nie jest to zweryfikować, chyba że "Required" = true zostanie usunięty.  Zastanów się, jeśli ta opcja jest odpowiednia dla Twojej przypadki użycia.

Zweryfikowane, adres e-mail jest domyślnie włączone w `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` w pliku zasad TrustFrameworkBase:

```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [Użyj atrybutów niestandardowych w niestandardowym profilu Edytuj zasady](active-directory-b2c-create-custom-attributes-profile-edit-custom.md).
