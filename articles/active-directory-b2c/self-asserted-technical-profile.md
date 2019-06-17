---
title: Zdefiniuj samodzielnie profilu technicznego w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: W przypadku zasad niestandardowych w usłudze Azure Active Directory B2C, należy zdefiniować samodzielnie profilu technicznego.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c728660f1a77c02f1e4b5fdeb467a7dbba4e36a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512662"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj samodzielnie profilu technicznego w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wszystkie interakcje w usługi Azure Active Directory (Azure AD) B2C, gdzie użytkownik powinien zapewniać dane wejściowe są oceniane pod własnym profile techniczne. Na przykład strony resetowania zapisywania strony, strony logowania lub hasło.

## <a name="protocol"></a>Protocol

**Nazwa** atrybutu **protokołu** element musi być równa `Proprietary`. **Obsługi** atrybutu musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługi Azure AD B2C własnym potwierdzone: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Poniższy przykład przedstawia samodzielnie profilu technicznego do obsługi poczty e-mail rejestracji:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>Oświadczeń wejściowych

W samodzielnie profilu technicznego można użyć **InputClaims** i **InputClaimsTransformations** elementy, aby wstępnie wypełnić wartość oświadczenia, które są wyświetlane na stronie samodzielnie (dane wyjściowe oświadczenia). Na przykład w ramach zasad profilu edycji najpierw odczytuje profil użytkownika z usługi Azure AD B2C w podróży użytkownika, a następnie samodzielnie profilu technicznego zestawów oświadczeń wejściowych z dane użytkownika przechowywane w profilu użytkownika. Te oświadczenia są zbierane z profilu użytkownika, a następnie prezentowany użytkownikowi, który następnie można edytować istniejące dane.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Oświadczeń danych wyjściowych

**OutputClaims** element zawiera listę oświadczeń, które mają zostać wyświetlone zbieranie danych przez użytkownika. Aby wstępnie wypełnić oświadczeń danych wyjściowych z niektórych wartości, należy użyć oświadczeń wejściowych, które wcześniej zostały opisane. Element może również zawierać wartość domyślną. Kolejność oświadczenia w **OutputClaims** określa kolejność, że usługa Azure AD B2C renderuje oświadczeń na ekranie. **DefaultValue** atrybutu staje się skuteczny tylko wtedy, gdy oświadczenie nigdy nie została ustawiona przed. Jednak jeśli została ustawiona przed w poprzednim kroku aranżacji, nawet wtedy, gdy użytkownik opuści wartość pusta, wartość domyślna nie zacznie obowiązywać. Aby wymusić użycie wartości domyślnej, należy ustawić **AlwaysUseDefaultValue** atrybutu `true`. Aby wymusić użytkownikowi podanie wartości oświadczeń określonych danych wyjściowych, należy ustawić **wymagane** atrybutu **OutputClaims** elementu `true`.

**Oświadczenia** element **OutputClaims** kolekcji musi ustawić **UserInputType** elementu dla każdego użytkownika danych wejściowych typu obsługiwanego przez usługi Azure AD B2C, takich jak `TextBox`lub `DropdownSingleSelect`. Lub **oświadczenie OutputClaim** należy ustawić element **DefaultValue**.  

**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń danych wyjściowych lub wygenerować nowe.

Następujące oświadczeń wychodzących jest zawsze równa `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Przypadek użycia

Istnieją cztery scenariusze oświadczeń danych wyjściowych:

- **Zbieranie danych wyjściowych oświadczeń użytkownika** — należy zebrać informacje od użytkownika, takie jak data urodzenia, należy dodać zastrzeżenia **OutputClaims** kolekcji. Należy określić oświadczenia, które są prezentowane użytkownikowi **UserInputType**, takich jak `TextBox` lub `DropdownSingleSelect`. Jeśli samodzielnie profil techniczny zawiera profilu technicznego sprawdzania poprawności, które generuje to samo oświadczenie, usługi Azure AD B2C nie są wyświetlane oświadczenia dla użytkownika. W przypadku nie żadnych oświadczeń wychodzących do zaprezentowania użytkownikowi usługi Azure AD B2C pomija profilu technicznego.
- **Ustawianie wartości domyślnej w oświadczenie wyjściowego** — bez zbierania danych przez użytkownika lub zwracania danych z profilu technicznego sprawdzania poprawności. `LocalAccountSignUpWithLogonEmail` Samodzielnie określonych zestawów profilu technicznego **wykonywane SelfAsserted-wprowadzania** oświadczenie do `true`.
- **Profil techniczny weryfikacji zwraca oświadczeń danych wyjściowych** -profilu technicznego może wywołać techniczne profil sprawdzania poprawności i zwraca niektóre oświadczenia. Możesz chcieć przetwarzany oświadczenia i zwraca je do następnych kroków aranżacji w podróży użytkownika. Na przykład podczas logowania się przy użyciu lokalnego konta, samodzielnie profilu technicznego o nazwie `SelfAsserted-LocalAccountSignin-Email` wywołuje profilu technicznego sprawdzania poprawności, o nazwie `login-NonInteractive`. Ten profil techniczny weryfikuje poświadczenia użytkownika i zwraca również wartość profilu użytkownika. Np. "userPrincipalName", "displayName", "imię" i "nazwisko".
- **Dane wyjściowe oświadczenia za pomocą przekształcania oświadczeń danych wyjściowych**

W poniższym przykładzie `LocalAccountSignUpWithLogonEmail` własnym potwierdzone profilu technicznego demonstruje użycie oświadczeń danych wyjściowych i zestawy **wykonywane SelfAsserted-wprowadzania** do `true`. `objectId`, `authenticationSource`, `newUser` Oświadczenia są dane wyjściowe `AAD-UserWriteUsingLogonEmail` weryfikacji technicznej profilowania i nie są wyświetlane użytkownikowi.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
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
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Utrwalanie oświadczeń

Jeśli **PersistedClaims** element jest nieobecne, samodzielnie profilu technicznego nie przechowuje danych do usługi Azure AD B2C. Zamiast tego wykonywane jest wywołanie, do profilu technicznego weryfikacji, który jest odpowiedzialny za przechowywanie danych. Na przykład zasady tworzenia konta używa `LocalAccountSignUpWithLogonEmail` własnym potwierdzone profilu technicznego, aby zebrać nowy profil użytkownika. `LocalAccountSignUpWithLogonEmail` Profilu technicznego wywołuje profilu technicznego sprawdzania poprawności, aby utworzyć konto w usłudze Azure AD B2C.

## <a name="validation-technical-profiles"></a>Sprawdzanie poprawności profile techniczne

Profil techniczne sprawdzania poprawności jest używany do weryfikowania niektórych lub wszystkich oświadczeń danych wyjściowych odwołujący się profilu technicznego. Oświadczeń wejściowych profilu technicznego weryfikacji musi znajdować się w oświadczeń danych wyjściowych samodzielnie profilu technicznego. Profil techniczny weryfikacji sprawdza poprawność danych wejściowych użytkownika i może zwrócić błąd do użytkownika. 

Profil techniczny sprawdzania poprawności może być dowolnym profilu technicznego w zasadach, taką jak [usługi Azure Active Directory](active-directory-technical-profile.md) lub [interfejsu API REST](restful-technical-profile.md) profile techniczne. W poprzednim przykładzie `LocalAccountSignUpWithLogonEmail` profilu technicznego weryfikuje, że signinName nie istnieje w katalogu. W przeciwnym razie profilu technicznego weryfikacji, tworzy konto lokalne i zwraca objectId authenticationSource, newUser. `SelfAsserted-LocalAccountSignin-Email` Wywołania profilu technicznego `login-NonInteractive` profilu technicznego sprawdzania poprawności do sprawdzania poprawności poświadczeń użytkownika.

Możesz również wywołanie interfejsu API REST profilu technicznego z logikę biznesową, Zastąp oświadczeń wejściowych lub Wzbogacanie danych użytkownika dzięki dalszej integracji z usługą firmowa aplikacja line-of-business. Aby uzyskać więcej informacji, zobacz [profilu technicznego sprawdzania poprawności](validation-technical-profile.md)

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| setting.showContinueButton | Nie | Wyświetla przycisk Kontynuuj. Możliwe wartości: `true` (ustawienie domyślne) lub `false` |
| setting.showCancelButton | Nie | Wyświetla przycisk Anuluj. Możliwe wartości: `true` (ustawienie domyślne) lub `false` |
| setting.operatingMode | Nie | Na stronie logowania ta właściwość określa zachowanie pola Nazwa użytkownika, na przykład sprawdzenie poprawności danych wejściowych i komunikaty o błędach. Oczekiwane wartości: `Username` lub `Email`. |
| ContentDefinitionReferenceId | Tak | Identyfikator [zawartości definicji](contentdefinitions.md) skojarzony z tym profilem Technical Preview. |
| EnforceEmailVerification | Nie | Do tworzenia konta lub edytowania profilu, wymusza Weryfikacja adresu e-mail. Możliwe wartości: `true` (ustawienie domyślne) lub `false`. | 
| setting.showSignupLink | Nie | Wyświetla przycisk tworzenia konta. Możliwe wartości: `true` (ustawienie domyślne) lub `false` |
| setting.retryLimit | Nie | Określa, ile razy użytkownik może próbować udostępniania danych, które jest sprawdzana względem profilu technicznego sprawdzania poprawności. Na przykład użytkownik próbuje zarejestrować się przy użyciu konta, które już istnieje i utrzymuje podjęcie próby, aż do osiągnięcia limitu.
| SignUpTarget | Nie | Rejestracja docelowy identyfikator programu exchange. Po kliknięciu przycisku Utwórz konto usługi Azure AD B2C wykonuje identyfikator określonego programu exchange. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKeys** element nie jest używany.













