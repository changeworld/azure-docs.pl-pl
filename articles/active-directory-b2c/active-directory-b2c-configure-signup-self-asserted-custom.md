---
title: Modyfikowanie logowania się w zasadach niestandardowych i skonfigurować własny potwierdzone dostawcy | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące dodawania oświadczeń utworzyć konto i skonfigurować dane wejściowe użytkownika
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/29/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9d8f644e819ceb83f0b436789d6d8610ed01f6a6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "34710801"
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Usługa Azure Active Directory B2C: Zmodyfikuj logowania się do dodawania nowych oświadczeń i skonfigurować dane wejściowe użytkownika.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule nowy wpis podane przez użytkownika (oświadczenie) doda do zapisywania podróży użytkownika.  Skonfigurujesz wpis jako listy rozwijanej i umożliwia określenie, czy jest to wymagane.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki w artykule [wprowadzenie zasady niestandardowe](active-directory-b2c-get-started-custom.md).  Przetestuj podróży signup/logowanie użytkownika do zapisywania nowego konta lokalnego, przed kontynuowaniem.


Gromadzenia danych początkowych z użytkowników odbywa się za pośrednictwem signup/signin.  Dodatkowe oświadczenia można zbierać później za pomocą podróże użytkownika edycji profilu. Zastosowano w ramach obsługi tożsamości w dowolnym momencie usługi Azure AD B2C interaktywnie zbiera informacje bezpośrednio od użytkownika, jego `selfasserted provider`. Poniższe kroki mają zastosowanie w dowolnym momencie ten dostawca jest używany.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>Zdefiniuj oświadczenia, jego nazwa wyświetlana i typ danych wejściowych użytkownika
Umożliwia poprosić użytkownika o ich miasta.  Dodaj następujący element do `<ClaimsSchema>` elementu w pliku zasad TrustFrameworkBase:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Dostępne są dodatkowe opcje, które można wprowadzać w tym miejscu dostosować oświadczenia.  Pełny schemat, można znaleźć w temacie **tożsamości środowiska Framework techniczne podręcznik**.  W tym przewodniku wkrótce zostaną opublikowane zamieszczone w tej sekcji.

* `<DisplayName>` ciąg, który definiuje dla użytkownika jest *etykiety*

* `<UserHelpText>` ułatwia użytkownikom zrozumienie, co jest wymagane

* `<UserInputType>` czy ukończona ma następujących opcji:
    * `TextBox`
```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

    * `RadioSingleSelectduration` -Wymusza pojedynczego wyboru.
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

    * `DropdownSingleSelect` — Umożliwia zaznaczenie tylko prawidłowe wartości.

![Zrzut ekranu opcji listy rozwijanej](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


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


* `CheckboxMultiSelect` Umożliwia wybór co najmniej jedną wartość.

![Zrzut ekranu opcji wyboru wielokrotnego](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


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

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Dodawanie oświadczenia do logowania w górę/logowania użytkownika podróży

1. Dodaj oświadczenie jako `<OutputClaim ClaimTypeReferenceId="city"/>` do TechnicalProfile `LocalAccountSignUpWithLogonEmail` (które można znaleźć w pliku zasad TrustFrameworkBase).  Należy zauważyć, że ta TechnicalProfile używa SelfAssertedAttributeProvider.

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

2. Dodawanie oświadczenia do usługi AAD-UserWriteUsingLogonEmail jako `<PersistedClaim ClaimTypeReferenceId="city" />` zapisu oświadczenia do katalogu usługi AAD po zebraniu go przez użytkownika. Może pominąć ten krok, jeśli nie chcesz zachować oświadczeń w katalogu do użytku w przyszłości.

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

3. Dodawanie oświadczenia do TechnicalProfile, która odczytuje z katalogu, gdy użytkownik zaloguje się jako `<OutputClaim ClaimTypeReferenceId="city" />`

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

4. Dodaj `<OutputClaim ClaimTypeReferenceId="city" />` zasad RP pliku SignUporSignIn.xml, więc tego oświadczenia są wysyłane do aplikacji w tokenie po przebieg pomyślne użytkownika.

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

## <a name="test-the-custom-policy-using-run-now"></a>Testowanie zasad niestandardowych za pomocą "Uruchom teraz"

1. Otwórz **bloku usługi Azure AD B2C** i przejdź do **tożsamości środowiska Framework > zasady niestandardowe**.
2. Wybierz zasady niestandardowe przekazywane i kliknij przycisk **Uruchom teraz** przycisku.
3. Należy zalogowanie przy użyciu adresu e-mail.

Ekran rejestracji w trybie testowym powinny wyglądać podobnie do poniższego:

![Zrzut ekranu przedstawiający zmodyfikowane opcją](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  Token do aplikacji będzie zawierają teraz `city` oświadczenia, jak pokazano poniżej
```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="optional-remove-email-verification-from-signup-journey"></a>Opcjonalnie: Usuń e-mail weryfikacji w podróży rejestracji

Aby pominąć Weryfikacja adresu e-mail, tworzenie zasad może być usunięty `PartnerClaimType="Verified.Email"`. Adres e-mail zostanie wymagane, ale nie zweryfikowane, chyba że "Wymagane" = true zostanie usunięta.  Należy rozważyć, jeśli ta opcja jest odpowiednia dla Twojej przypadki użycia!

Zweryfikować adres e-mail jest domyślnie włączone w `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` w pliku zasad TrustFrameworkBase w pakiecie starter:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Kolejne kroki

Dodaj nowe oświadczenie do przepływów dla logowania do kont społecznościowych, zmieniając TechnicalProfiles wymienionych poniżej. Są one używane przez federacyjne/społecznego konto logowania do zapisu i odczytu danych użytkownika za pomocą alternativeSecurityId jako Lokalizator.
```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```
