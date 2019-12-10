---
title: Zdefiniuj profil techniczny z własnym potwierdzeniem w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny z własnym potwierdzeniem w zasadach niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bfa8982fb49b31540d1926bdeb75a96dc1d79cf0
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950905"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj własny profil techniczny w Azure Active Directory B2C zasad niestandardowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wszystkie interakcje w Azure Active Directory B2C (Azure AD B2C), w których użytkownik powinien podać dane wejściowe, są profilami technicznymi z własnym potwierdzeń. Na przykład Strona rejestracji, Strona logowania lub Resetowanie hasła.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **name** elementu **Protocol** musi mieć wartość `Proprietary`. Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C, dla własnego potwierdzeń: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

Poniższy przykład przedstawia profil techniczny z własnym potwierdzeniem do rejestracji w wiadomościach e-mail:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Oświadczenia wejściowe

W profilu technicznym z własnym potwierdzeniem można użyć elementów **InputClaims** i **InputClaimsTransformations** , aby wstępnie wypełnić wartość oświadczeń, które pojawiają się na stronie z możliwością potwierdzenia (oświadczenia wyjściowe). Na przykład w obszarze Edytowanie zasad profilu Użytkownik najpierw odczytuje profil użytkownika z usługi Azure AD B2C Directory, a następnie samodzielnie potwierdzony profil techniczny ustawi oświadczenia wejściowe przy użyciu danych użytkownika przechowywanych w profilu użytkownika. Te oświadczenia są zbierane z profilu użytkownika, a następnie prezentowane użytkownikowi, który może edytować istniejące dane.

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


## <a name="output-claims"></a>Oświadczenia wyjściowe

Element **OutputClaims** zawiera listę oświadczeń, które mają być prezentowane w celu zbierania danych od użytkownika. Aby wstępnie wypełnić oświadczenia wyjściowe z niektórymi wartościami, użyj oświadczeń wejściowych, które zostały wcześniej opisane. Element może również zawierać wartość domyślną. Kolejność oświadczeń w **OutputClaims** kontroluje kolejność, w której Azure AD B2C renderuje oświadczenia na ekranie. Atrybut **DefaultValue** działa tylko wtedy, gdy nie został wcześniej ustawiony. Ale jeśli został on wcześniej ustawiony przed w poprzednim kroku aranżacji, nawet jeśli użytkownik opuści wartość pustą, wartość domyślna nie zacznie obowiązywać. Aby wymusić użycie wartości domyślnej, ustaw atrybut **AlwaysUseDefaultValue** na `true`. Aby wymusić użytkownikowi podanie wartości dla określonego zgłoszenia wyjściowego, należy ustawić **wymagany** atrybut elementu **OutputClaims** , aby `true`.

Element **ClaimType** w kolekcji **OutputClaims** musi określać element **UserInputType** dla dowolnego typu danych wejściowych użytkownika obsługiwanego przez Azure AD B2C, takich jak `TextBox` lub `DropdownSingleSelect`. Lub element **oświadczenie outputclaim** musi mieć ustawioną wartość **DefaultValue**.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

Następujące zgłoszenie wyjściowe jest zawsze ustawione na `live.com`:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Przypadek użycia

Istnieją cztery scenariusze dotyczące oświadczeń wyjściowych:

- **Zbieranie oświadczeń wyjściowych od użytkownika** — w przypadku konieczności zebrania informacji od użytkownika, takich jak Data urodzenia, należy dodać oświadczenie do kolekcji **OutputClaims** . Oświadczenia, które są prezentowane użytkownikowi, muszą określać **UserInputType**, takie jak `TextBox` lub `DropdownSingleSelect`. Jeśli profil techniczny z własnym potwierdzeniem zawiera profil techniczny weryfikacji, który wyprowadza to samo zastrzeżenie, Azure AD B2C nie zaprezentuje tego żądania użytkownikowi. Jeśli nie ma żadnych żadnych roszczeń wyjściowych do zaprezentowania użytkownikowi, Azure AD B2C pomija profil techniczny.
- **Ustawianie wartości domyślnej w ramach zgłoszenia wyjściowego** — bez zbierania danych od użytkownika lub zwracania danych z profilu kontroli technicznej. `LocalAccountSignUpWithLogonEmail` samodzielnie poproszony profil techniczny ustawia **SelfAsserted** do wykonania, aby `true`.
- **Profil techniczny weryfikacji zwraca oświadczenia wyjściowe** — Twój profil techniczny może wywołać profil techniczny weryfikacji, który zwraca pewne oświadczenia. Można chcieć wyrównać oświadczenia i zwrócić je do następnych kroków aranżacji w podróży użytkownika. Na przykład podczas logowania się przy użyciu konta lokalnego profil techniczny z własnym potwierdzeń o nazwie `SelfAsserted-LocalAccountSignin-Email` wywołuje profil techniczny weryfikacji o nazwie `login-NonInteractive`. Ten profil techniczny sprawdza poprawność poświadczeń użytkownika, a także zwraca profil użytkownika. Takie jak "userPrincipalName", "displayName", "podaną nazwę" i "nazwisko".
- **Wyprowadzanie oświadczeń przez transformację oświadczeń wyjściowych**

W poniższym przykładzie profil techniczny z własnymi potwierdzeń `LocalAccountSignUpWithLogonEmail` demonstruje użycie oświadczeń wyjściowych i ustawia polecenie **SelfAsserted-Input** do `true`. `objectId`, `authenticationSource`, `newUser` oświadczenia są danymi wyjściowymi profilu technicznego weryfikacji `AAD-UserWriteUsingLogonEmail` i nie są widoczne dla użytkownika.

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

Jeśli element **PersistedClaims** jest nieobecny, profil techniczny z własnym potwierdzeniem nie utrwala danych do Azure AD B2C. Zamiast tego jest wykonywane wywołanie w profilu technicznym weryfikacji, który jest odpowiedzialny za utrwalanie danych. Na przykład zasady rejestracji wykorzystują `LocalAccountSignUpWithLogonEmail` z własnym profilem technicznym do zbierania nowego profilu użytkownika. Profil techniczny `LocalAccountSignUpWithLogonEmail` wywołuje profil techniczny weryfikacji, aby utworzyć konto w Azure AD B2C.

## <a name="validation-technical-profiles"></a>Sprawdzanie poprawności profilów technicznych

Profil techniczny weryfikacji jest używany do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyjściowych w profilu technicznym, do którego się odwołuje. Oświadczenia wejściowe profilu technicznego weryfikacji muszą pojawić się w oświadczeniach wyjściowych profilu technicznego z potwierdzeniem. Profil techniczny weryfikacji sprawdza poprawność danych wejściowych użytkownika i może zwrócić błąd użytkownika.

Profil techniczny weryfikacji może być dowolnym profilem technicznym w zasadach, takim jak [Azure Active Directory](active-directory-technical-profile.md) lub profile techniczne [interfejsu API REST](restful-technical-profile.md) . W poprzednim przykładzie profil techniczny `LocalAccountSignUpWithLogonEmail` sprawdza, czy signinName nie istnieje w katalogu. W przeciwnym razie sprawdzanie poprawności profilu technicznego spowoduje utworzenie konta lokalnego i zwrócenie identyfikatora objectId, authenticationSource, newUser. Profil techniczny `SelfAsserted-LocalAccountSignin-Email` wywołuje profil techniczny weryfikacji `login-NonInteractive` w celu zweryfikowania poświadczeń użytkownika.

Możesz również wywołać profil techniczny interfejsu API REST z logiką biznesową, zastępować oświadczenia wejściowe lub wzbogacać dane użytkowników, zapewniając dalszą integrację z firmową aplikacją biznesową. Aby uzyskać więcej informacji, zobacz temat [Sprawdzanie poprawności profilu technicznego](validation-technical-profile.md)

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Ustawianie. showContinueButton | Nie | Wyświetla przycisk Kontynuuj. Możliwe wartości: `true` (wartość domyślna) lub `false` |
| Ustawianie. showCancelButton | Nie | Wyświetla przycisk Anuluj. Możliwe wartości: `true` (wartość domyślna) lub `false` |
| ustawienie. operatmode | Nie | Dla strony logowania ta właściwość kontroluje zachowanie pola username, takie jak walidacja danych wejściowych i komunikaty o błędach. Oczekiwane wartości: `Username` lub `Email`. |
| ContentDefinitionReferenceId | Tak | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym profilem technicznym. |
| EnforceEmailVerification | Nie | W przypadku rejestrowania lub edytowania profilu wymusza weryfikację poczty e-mail. Możliwe wartości: `true` (wartość domyślna) lub `false`. |
| setting.showSignupLink | Nie | Wyświetla przycisk rejestracji. Możliwe wartości: `true` (wartość domyślna) lub `false` |
| Ustawianie. retryLimit | Nie | Określa, ile razy użytkownik może próbować podać dane, które są sprawdzane względem profilu technicznego weryfikacji. Na przykład użytkownik próbuje zarejestrować się przy użyciu konta, które już istnieje, i kontynuuje podejmowanie prób aż do osiągnięcia limitu.
| SignUpTarget | Nie | Docelowy identyfikator wymiany programu Exchange. Gdy użytkownik kliknie przycisk rejestracji, Azure AD B2C wykonuje określony identyfikator programu Exchange. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

Element **CryptographicKeys** nie jest używany.













