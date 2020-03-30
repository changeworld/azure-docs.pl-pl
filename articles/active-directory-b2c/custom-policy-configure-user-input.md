---
title: Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się, jak dostosować dane wejściowe użytkownika i dodać oświadczenia do podróży rejestracji lub logowania w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473680"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika przy użyciu zasad niestandardowych w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule można zebrać nowy atrybut podczas podróży rejestracji w usłudze Azure Active Directory B2C (Azure AD B2C). Otrzymasz miasto użytkowników, skonfigurujesz je jako rozwijane i określisz, czy jest to wymagane.

> [!NOTE]
> W tym przykładzie użyto wbudowanego oświadczenia "miasto". Zamiast tego można wybrać jeden z obsługiwanych [atrybutów wbudowanych usługi Azure AD B2C](user-profile-attributes.md) lub atrybut niestandardowy. Aby użyć atrybutu niestandardowego, [włącz atrybuty niestandardowe w zasadach](custom-policy-custom-attributes.md). Aby użyć innego atrybutu wbudowanego lub niestandardowego, zastąp "miasto" wybranym atrybutem, na przykład wbudowanym atrybutem *jobTitle* lub atrybutem niestandardowym, takim jak *extension_loyaltyId*.  

Możesz zbierać dane początkowe od użytkowników, korzystając z podróży użytkownika rejestracji lub logowania. Dodatkowe oświadczenia można zebrać później za pomocą podróży użytkownika edycji profilu. Za każdym razem, gdy usługa Azure AD B2C gromadzi informacje bezpośrednio od użytkownika interaktywnie, struktura środowiska tożsamości używa [swojego profilu technicznego.](self-asserted-technical-profile.md) W tym przykładzie:

1. Zdefiniuj roszczenie "miasto". 
1. Zapytaj użytkownika o swoje miasto.
1. Utrwalić miasto do profilu użytkownika w katalogu usługi Azure AD B2C.
1. Przeczytaj oświadczenie o mieście z katalogu usługi Azure AD B2C przy każdym logacji.
1. Po zalogowaniu się lub zarejestrowaniu miasto należy zwrócić miasto do aplikacji strony uzależniającej.  

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w [wprowadzenie do zasad niestandardowych](custom-policy-get-started.md). Musisz mieć działające zasady niestandardowe dotyczące rejestracji i logowania się za pomocą kont społecznościowych i lokalnych.

## <a name="define-a-claim"></a>Definiowanie oświadczenia

Oświadczenie zapewnia tymczasowe przechowywanie danych podczas wykonywania zasad usługi Azure AD B2C. [Schemat oświadczeń](claimsschema.md) jest miejscem, w którym deklarujesz swoje oświadczenia. Do zdefiniowania oświadczenia używane są następujące elementy:

- **DisplayName** — ciąg definiujący etykietę skierowaną do użytkownika.
- [DataType](claimsschema.md#datatype) — typ oświadczenia.
- **UserHelpText** — pomaga użytkownikowi zrozumieć, co jest wymagane.
- [UserInputType](claimsschema.md#userinputtype) — typ formantu wejściowego, taki jak pole tekstowe, wybór radia, lista rozwijana lub wiele zaznaczeń.

Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.

1. Wyszukaj [element BuildingBlocks.](buildingblocks.md) Jeśli element nie istnieje, dodaj go.
1. Znajdź [ClaimsSchema](claimsschema.md) element. Jeśli element nie istnieje, dodaj go.
1. Dodaj oświadczenie miasta do **elementu ClaimsSchema.**  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Dodawanie oświadczenia do interfejsu użytkownika

Następujące profile techniczne są [samodzielnie potwierdzone, wywoływane,](self-asserted-technical-profile.md)gdy użytkownik ma dostarczyć danych wejściowych:

- **LocalAccountSignUpWithLogonEmail** — przepływ rejestracji konta lokalnego.
- **SelfAsserted-Social** — konto federacyjne po raz pierwszy logowania użytkownika.
- **SelfAsserted-ProfileUpdate** — edytuj przepływ profilu.

Aby odebrać reklamację miasta podczas rejestracji, należy je dodać `LocalAccountSignUpWithLogonEmail` jako oświadczenie wyjściowe do profilu technicznego. Zastąp ten profil techniczny w pliku rozszerzenia. Określ całą listę oświadczeń danych wyjściowych, aby kontrolować kolejność, w jakiej oświadczenia są prezentowane na ekranie. Znajdź **ClaimsProviders** element. Dodaj nowe ClaimsProviders w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Aby odebrać roszczenie miasta po pierwszym zalogowaniu się za pomocą konta federacyjnego, należy je dodać jako oświadczenie wyjściowe do profilu `SelfAsserted-Social` technicznego. Aby użytkownicy kont lokalnych i federowanych mogli później edytować dane `SelfAsserted-ProfileUpdate` profilu, dodaj oświadczenie wyjściowe do profilu technicznego. Zastąp te profile techniczne w pliku rozszerzenia. Określ całą listę oświadczeń danych wyjściowych, aby kontrolować kolejność, w jakiej oświadczenia są prezentowane na ekranie. Znajdź **ClaimsProviders** element. Dodaj nowe ClaimsProviders w następujący sposób:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Odczytywanie i pisanie oświadczenia

Następujące profile techniczne to [profile techniczne usługi Active Directory,](active-directory-technical-profile.md)które odczytują i zapisują dane w usłudze Azure Active Directory.  
Służy `PersistedClaims` do zapisywania danych `OutputClaims` w profilu użytkownika i odczytywania danych z profilu użytkownika w odpowiednich profilach technicznych usługi Active Directory.

Zastąp te profile techniczne w pliku rozszerzenia. Znajdź **ClaimsProviders** element.  Dodaj nowe ClaimsProviders w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Dołącz roszczenie do tokenu 

Aby przywrócić roszczenie miasta do aplikacji jednostki uzależniającej, <em> `SocialAndLocalAccounts/` </em> dodaj do pliku oświadczenie wyjściowe. Oświadczenie danych wyjściowych zostanie dodane do tokenu po pomyślnej podróży użytkownika i zostanie wysłane do aplikacji. Zmodyfikuj element profilu technicznego w sekcji jednostki uzależniona, aby dodać miasto jako oświadczenie wyjściowe.
 
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
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testowanie zasad niestandardowych

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
3. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
4. Wybierz **platformę środowiska tożsamości**.
5. Wybierz **pozycję Przekaż zasady niestandardowe**, a następnie przekaż dwa zmienione pliki zasad.
2. Wybierz przekazane zasady rejestracji lub logowania, a następnie kliknij przycisk **Uruchom teraz.**
3. Powinieneś być w stanie zarejestrować się przy użyciu adresu e-mail.

Ekran rejestracji powinien wyglądać podobnie do następującego zrzutu ekranu:

![Zrzut ekranu przedstawiający zmodyfikowaną opcję rejestracji](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Token wysłany z powrotem `city` do aplikacji zawiera oświadczenie.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [ClaimsSchema](claimsschema.md) elementu w odwołaniu IEF.
- Dowiedz się, jak [używać atrybutów niestandardowych w zasadach edycji profilu niestandardowego](custom-policy-custom-attributes.md).
