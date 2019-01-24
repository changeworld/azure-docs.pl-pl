---
title: Modyfikowanie logowania się w zasadach niestandardowych i skonfigurować własny potwierdzone dostawcy | Dokumentacja firmy Microsoft
description: Wskazówki dotyczące dodawania oświadczeń zarejestrować się i konfigurowanie danych wejściowych użytkownika
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 326b0289ebb7aa503d41d556bea15017c28f7a1e
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2019
ms.locfileid: "54853720"
---
# <a name="azure-active-directory-b2c-modify-sign-up-to-add-new-claims-and-configure-user-input"></a>Azure Active Directory B2C: Zmodyfikuj logowania się do dodawania nowych oświadczeń i konfigurowanie danych wejściowych użytkownika.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule dodasz nowy wpis podane przez użytkownika (oświadczenia) do podróż użytkownika rejestracji.  Skonfigurujesz wpis jako listę rozwijaną i umożliwia określenie, czy jest to wymagane.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj kroki opisane w artykule [wprowadzenie do zasad niestandardowych](active-directory-b2c-get-started-custom.md).  Przetestuj podróż tworzenia konta/logowania użytkownika na potrzeby rejestracji nowego konta lokalnego, przed kontynuowaniem.


Gromadzenia danych początkowych użytkowników odbywa się za pośrednictwem tworzenia konta/logowania.  Dodatkowe oświadczenia, które można później gromadzić za pośrednictwem podróży użytkownika edycji profilu. W dowolnym momencie usługi Azure AD B2C interaktywnie zbiera informacje bezpośrednio od użytkownika, używa platformy środowiska tożsamości jego `selfasserted provider`. Poniższe kroki mają zastosowanie w dowolnym momencie tego dostawcy jest używany.


## <a name="define-the-claim-its-display-name-and-the-user-input-type"></a>Zdefiniuj oświadczenia, jego nazwę wyświetlaną i typ danych wejściowych użytkownika
Umożliwia, poproś użytkownika o ich miasta.  Dodaj następujący element do `<ClaimsSchema>` elementu w pliku zasad TrustFrameworkBase:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```
Dostępne są dodatkowe opcje, możesz wprowadzić tutaj aby dostosować oświadczenia.  Pełnego schematu można znaleźć **tożsamości środowisko Framework odwołanie podręcznik techniczny**.  Ten przewodnik wkrótce zostaną opublikowane zamieszczone w tej sekcji.

* `<DisplayName>` jest ciągiem, który definiuje użytkownika skierowaną *etykiety*

* `<UserHelpText>` pomaga użytkownikom zrozumieć, co jest wymagane

* `<UserInputType>` zawiera cztery następujące opcje, które przedstawiono poniżej:
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

    * `DropdownSingleSelect` — Umożliwia wybranie tylko prawidłowe wartości.

![Zrzut ekranu: klikanie opcji menu rozwijanego](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)


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

![Zrzut ekranu przedstawiający opcji wyboru wielokrotnego](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)


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

## <a name="add-the-claim-to-the-sign-upsign-in-user-journey"></a>Dodaj oświadczenie do logowania w górę/logowanie w podróży użytkownika

1. Dodaj oświadczenie jako `<OutputClaim ClaimTypeReferenceId="city"/>` do profilu technicznego `LocalAccountSignUpWithLogonEmail` (które można znaleźć w pliku zasad TrustFrameworkBase).  Należy pamiętać, że SelfAssertedAttributeProvider korzysta z tego profilu technicznego.

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

2. Dodawanie oświadczenia do usługi AAD — UserWriteUsingLogonEmail jako `<PersistedClaim ClaimTypeReferenceId="city" />` do zapisania oświadczenia do katalogu usługi AAD po zebraniu go przez użytkownika. Jeśli nie chcesz zachować oświadczenia w katalogu do użytku w przyszłości, możesz pominąć ten krok.

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

3. Dodaj oświadczenie do profilu technicznego, która odczytuje z katalogu, gdy użytkownik zaloguje się jako `<OutputClaim ClaimTypeReferenceId="city" />`

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

4. Dodaj `<OutputClaim ClaimTypeReferenceId="city" />` do jednostki Uzależnionej zasad pliku SignUporSignIn.xml, dzięki czemu tego oświadczenia są wysyłane do aplikacji w tokenie po podróży użytkownika pomyślne.

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

1. Otwórz **bloku usługi Azure AD B2C** i przejdź do **struktura środowiska tożsamości > zasady niestandardowe**.
2. Wybierz zasady niestandardowe, które przekazane i kliknij przycisk **Uruchom teraz** przycisku.
3. Powinien móc zarejestrować się przy użyciu adresu e-mail.

Ekran rejestracji w trybie testowym powinien wyglądać mniej więcej tak:

![Zrzut ekranu przedstawiający modyfikacji opcji rejestracji](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

  Token do aplikacji będzie teraz obejmować `city` oświadczenia, jak pokazano poniżej
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

## <a name="optional-remove-email-verification-from-signup-journey"></a>Opcjonalnie: Usuń Weryfikacja adresu e-mail z podróży rejestracji

Aby pominąć weryfikację poczty e-mail, autor zasad może być usunięty `PartnerClaimType="Verified.Email"`. Adres e-mail zostanie wymagane, ale nie jest to zweryfikować, chyba że "Required" = true zostanie usunięty.  Zastanów się, jeśli ta opcja jest odpowiednia dla Twojej przypadki użycia!

Zweryfikowane, adres e-mail jest domyślnie włączone w `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">` w pliku zasad TrustFrameworkBase w pakiecie początkowy:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Kolejne kroki

Jeśli zasady obsługuje kont społecznościowych, Dodaj nowe oświadczenie do przepływów w dotyczące logowania się do konta w sieci społecznościowej, zmieniając profile techniczne wymienione poniżej. Te oświadczenia są używane przez logowania do konta społecznościowego do zbierania i zapisywania danych przez użytkownika.

1. Znajdź profil techniczny **ubezpieczenia SelfAsserted** i Dodaj oświadczeń wychodzących. Kolejność oświadczenia w **OutputClaims** określa kolejność, że usługa Azure AD B2C renderuje oświadczeń na ekranie. Na przykład `<OutputClaim ClaimTypeReferenceId="city" />`.
2. Znajdź profil techniczny **AAD UserWriteUsingAlternativeSecurityId** i Dodaj oświadczenie utrwalanie. Na przykład `<PersistedClaim ClaimTypeReferenceId="city" />`.
3. Znajdź profil techniczny **AAD UserReadUsingAlternativeSecurityId** i Dodaj oświadczeń wychodzących. Na przykład `<OutputClaim ClaimTypeReferenceId="city" />`.
