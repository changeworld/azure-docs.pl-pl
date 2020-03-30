---
title: Definiowanie samodzielnie potwierdzonego profilu technicznego w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj samodzielnie potwierdzony profil techniczny w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b29b8b0975639e5c5315a55e1382794d7662665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332504"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie samodzielnie potwierdzonego profilu technicznego w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wszystkie interakcje w usłudze Azure Active Directory B2C (Azure AD B2C), gdzie użytkownik ma dostarczyć dane wejściowe są samodzielnie potwierdzone profile techniczne. Na przykład strona rejestracji, strona logowania lub strona resetowania hasła.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C, dla samodzielnie potwierdzonych:`Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

W poniższym przykładzie przedstawiono samodzielnie potwierdzony profil techniczny dla rejestracji poczty e-mail:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Oświadczenia wejściowe

W samodzielnie potwierdzonym profilu technicznym można użyć **inputclaims** i **InputClaimsTransformations** elementów do wstępnego wypełniania wartości oświadczeń, które pojawiają się na stronie samodzielnie twierdził (wyświetl oświadczenia). Na przykład w zasadach profilu edycji journey użytkownika najpierw odczytuje profil użytkownika z usługi katalogowej usługi Azure AD B2C, a następnie samodzielnie potwierdzony profil techniczny ustawia oświadczenia wejściowe z danymi użytkownika przechowywanymi w profilu użytkownika. Oświadczenia te są zbierane z profilu użytkownika, a następnie prezentowane użytkownikowi, który następnie może edytować istniejące dane.

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

## <a name="display-claims"></a>Wyświetlanie oświadczeń

Funkcja oświadczeń wyświetlania jest obecnie w **wersji zapoznawczej**.

**DisplayClaims** element zawiera listę oświadczeń, które mają być prezentowane na ekranie do zbierania danych od użytkownika. Aby wstępnie wypełnić wartości oświadczeń wyświetlania, należy użyć oświadczeń wejściowych, które zostały wcześniej opisane. Element może również zawierać wartość domyślną.

Kolejność oświadczeń w **DisplayClaims** określa kolejność, w której usługa Azure AD B2C renderuje oświadczenia na ekranie. Aby wymusić na użytkowniku podanie wartości określonego oświadczenia, ustaw atrybut **Wymagany** elementu `true` **DisplayClaim** na .

Element **ClaimType** w kolekcji **DisplayClaims** musi ustawić element **UserInputType** na dowolny typ danych wejściowych użytkownika obsługiwany przez usługę Azure AD B2C. Na przykład: `TextBox` lub `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Dodawanie odwołania do displaycontrol

W kolekcji oświadczeń wyświetlania można dołączyć odwołanie do [DisplayControl,](display-controls.md) który został utworzony. Formant wyświetlania jest elementem interfejsu użytkownika, który ma specjalne funkcje i współdziała z usługą zaplecza usługi Azure AD B2C. Umożliwia użytkownikowi wykonywanie akcji na stronie, które wywołują profil techniczny sprawdzania poprawności na zapleczu. Na przykład weryfikowanie adresu e-mail, numeru telefonu lub numeru lojalnościowego klienta.

Poniższy `TechnicalProfile` przykład ilustruje użycie oświadczeń wyświetlania z formantami wyświetlania.

* Pierwsze oświadczenie wyświetlania zawiera `emailVerificationControl` odwołanie do formantu wyświetlania, który zbiera i weryfikuje adres e-mail.
* Piąte oświadczenie wyświetlacza zawiera `phoneVerificationControl` odwołanie do kontrolki wyświetlania, która zbiera i weryfikuje numer telefonu.
* Inne oświadczenia wyświetlania są ClaimTypes do pobrania od użytkownika.

```XML
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Jak wspomniano, oświadczenie wyświetlania z odwołaniem do formantu wyświetlania może uruchomić własną weryfikację, na przykład weryfikując adres e-mail. Ponadto strona samodzielnie potwierdzona obsługuje przy użyciu profilu technicznego sprawdzania poprawności, aby sprawdzić poprawność całej strony, w tym wszelkie dane wejściowe użytkownika (typy oświadczeń lub formanty wyświetlania), przed przejściem do następnego kroku aranżacji.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Ostrożnie połącz użycie oświadczeń wyświetlania i oświadczeń wyjściowych

Jeśli określisz jeden lub więcej **DisplayClaim** elementów w samodzielnie potwierdzone profilu technicznego, należy użyć DisplayClaim dla *każdego* oświadczenia, które mają być wyświetlane na ekranie i zbierać od użytkownika. Żadne oświadczenia wyjściowe nie są wyświetlane przez samodzielnie potwierdzony profil techniczny, który zawiera co najmniej jedno oświadczenie wyświetlania.

Rozważmy poniższy `age` przykład, w którym oświadczenie jest zdefiniowane jako oświadczenie **wyjściowe** w zasadach podstawowych. Przed dodaniem jakichkolwiek oświadczeń wyświetlania do `age` samodzielnie potwierdzonego profilu technicznego oświadczenie jest wyświetlane na ekranie w celu zbierania danych od użytkownika:

```XML
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Jeśli zasada liścia, która dziedziczy `officeNumber` tę bazę, określa następnie jako oświadczenie **wyświetlania:**

```XML
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

Oświadczenie `age` w zasadach podstawowych nie jest już prezentowane użytkownikowi na ekranie - jest skutecznie "ukryte". Aby wyświetlić `age` oświadczenie i zebrać wartość wieku od `age` użytkownika, należy dodać **DisplayClaim**.

## <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element zawiera listę oświadczeń, które mają być zwracane do następnego kroku aranżacji. **DefaultValue** atrybut działa tylko wtedy, gdy oświadczenie nigdy nie został ustawiony. Jeśli został ustawiony w poprzednim kroku aranżacji, wartość domyślna nie zostanie uwzględnionych, nawet jeśli użytkownik pozostawia wartość pustą. Aby wymusić użycie wartości domyślnej, ustaw atrybut **AlwaysUseDefaultValue** na `true`.

Ze względów bezpieczeństwa wartość`UserInputType` oświadczenia `Password`hasła (ustawiona na) jest dostępna tylko dla profili technicznych sprawdzania poprawności profilu technicznego. Nie można użyć oświadczenia hasła w następnych krokach aranżacji. 

> [!NOTE]
> W poprzednich wersjach struktury środowiska tożsamości (IEF) oświadczenia wyjściowe były używane do zbierania danych od użytkownika. Aby zbierać dane od użytkownika, należy użyć **DisplayClaims** kolekcji zamiast.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

### <a name="when-you-should-use-output-claims"></a>Kiedy należy używać oświadczeń wyjściowych

W samodzielnie potwierdzonym profilu technicznym kolekcja oświadczeń wyjściowych zwraca oświadczenia do następnego kroku aranżacji.

Użyj oświadczeń wyjściowych, gdy:

- **Oświadczenia są dane wyjściowe przez transformację oświadczeń wyjściowych**.
- **Ustawianie wartości domyślnej w oświadczeniu wyjściowym** bez zbierania danych od użytkownika lub zwracania danych z profilu technicznego sprawdzania poprawności. Samodzielnie `LocalAccountSignUpWithLogonEmail` potwierdzony profil techniczny ustawia **oświadczenie wykonane-SelfAsserted-Input** na `true`.
- **Profil techniczny sprawdzania poprawności zwraca oświadczenia wyjściowe** — Twój profil techniczny może wywołać profil techniczny sprawdzania poprawności, który zwraca niektóre oświadczenia. Możesz chcieć wyeszła oświadczenia i przywrócić je do następnych kroków aranżacji w podróży użytkownika. Na przykład podczas logowania się przy za pomocą konta lokalnego samodzielnie potwierdzony profil techniczny o nazwie `SelfAsserted-LocalAccountSignin-Email` wywołuje profil techniczny sprawdzania poprawności o nazwie `login-NonInteractive`. Ten profil techniczny sprawdza poprawność poświadczeń użytkownika, a także zwraca profil użytkownika. Takie jak 'userPrincipalName', 'displayName', 'givenName' i 'surName'.
- **Formant wyświetlania zwraca oświadczenia wyjściowe** — Twój profil techniczny może mieć odwołanie do [formantu wyświetlania](display-controls.md). Formant wyświetlania zwraca niektóre oświadczenia, takie jak zweryfikowany adres e-mail. Możesz chcieć wyeszła oświadczenia i przywrócić je do następnych kroków aranżacji w podróży użytkownika. Funkcja sterowania wyświetlaniem jest obecnie w **wersji zapoznawczej**.

W poniższym przykładzie pokazano użycie samodzielnie potwierdzonego profilu technicznego, który używa zarówno oświadczeń wyświetlania, jak i oświadczeń danych wyjściowych.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

## <a name="persist-claims"></a>Utrwalanie oświadczeń

Element PersistedClaims nie jest używany. Samodzielnie potwierdzony profil techniczny nie utrwalił danych do usługi Azure AD B2C. Zamiast tego wywołanie jest do sprawdzania poprawności profilu technicznego, który jest odpowiedzialny za utrwalanie danych. Na przykład zasady rejestracji używa `LocalAccountSignUpWithLogonEmail` samodzielnie potwierdzonego profilu technicznego do zbierania nowego profilu użytkownika. Profil `LocalAccountSignUpWithLogonEmail` techniczny wywołuje profil techniczny sprawdzania poprawności, aby utworzyć konto w usłudze Azure AD B2C.

## <a name="validation-technical-profiles"></a>Zatwierdzanie profili technicznych

Profil techniczny sprawdzania poprawności służy do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyjściowych odwołującego się do profilu technicznego. Oświadczenia wejściowe profilu technicznego sprawdzania poprawności muszą znajdować się w oświadczeń wyjściowych samodzielnie potwierdzonego profilu technicznego. Profil techniczny sprawdzania poprawności sprawdza poprawność danych wejściowych użytkownika i może zwrócić błąd do użytkownika.

Profil techniczny sprawdzania poprawności może być dowolnym profilem technicznym w zasadach, takim jak [usługa Azure Active Directory](active-directory-technical-profile.md) lub profile techniczne [interfejsu API REST.](restful-technical-profile.md) W poprzednim przykładzie `LocalAccountSignUpWithLogonEmail` profil techniczny sprawdza, czy signinName nie istnieje w katalogu. Jeśli nie, profil techniczny sprawdzania poprawności tworzy konto lokalne i zwraca objectId, authenticationSource, newUser. Profil `SelfAsserted-LocalAccountSignin-Email` techniczny wywołuje `login-NonInteractive` profil techniczny sprawdzania poprawności, aby sprawdzić poprawność poświadczeń użytkownika.

Można również wywołać profil techniczny interfejsu API REST z logiką biznesową, zastąpić oświadczenia wejściowe lub wzbogacić dane użytkownika, aby dalej integrować się z aplikacją biznesową. Aby uzyskać więcej informacji, zobacz [Sprawdzanie poprawności profilu technicznego](validation-technical-profile.md)

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| Nie | Dla strony logowania ta właściwość kontroluje zachowanie pola nazwy użytkownika, takie jak sprawdzanie poprawności danych wejściowych i komunikaty o błędach. Oczekiwane `Username` wartości: lub `Email`.  |
| AllowGenerationOfClaimsWithNullValues| Nie| Zezwalaj na generowanie oświadczenia o wartości null. Na przykład w przypadku, gdy użytkownik nie zaznacza pola wyboru.|
| ContentDefinitionReferenceId (ida) | Tak | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym profilem technicznym. |
| Egzekwacja EmailVerification | Nie | W przypadku rejestracji lub edycji profilu wymusza weryfikację poczty e-mail. Możliwe wartości: `true` (domyślnie) lub `false`. |
| setting.retryLimit | Nie | Określa, ile razy użytkownik może próbować podać dane, które są sprawdzane względem profilu technicznego sprawdzania poprawności. Na przykład użytkownik próbuje zarejestrować się przy za pomocą konta, które już istnieje i próbuje, aż do osiągnięcia limitu.
| Zarejestrujtarget <sup>1</sup>| Nie | Identyfikator wymiany docelowej rejestracji. Gdy użytkownik kliknie przycisk rejestracji, usługa Azure AD B2C wykonuje określony identyfikator wymiany. |
| setting.showCancelButton | Nie | Wyświetla przycisk anuluj. Możliwe wartości: `true` (domyślnie) lub`false` |
| setting.showKontynuj przycisk | Nie | Wyświetla przycisk kontynuuj. Możliwe wartości: `true` (domyślnie) lub`false` |
| setting.showSignupLink <sup>2</sup>| Nie | Wyświetla przycisk rejestracji. Możliwe wartości: `true` (domyślnie) lub`false` |
| setting.forgotPasswordLinkLocation <sup>2</sup>| Nie| Wyświetla łącze zapomniane hasło. Możliwe wartości: `AfterInput` (domyślnie) łącze jest wyświetlane u `None` dołu strony lub usuwa łącze zapomniane hasło.|
| setting.enableRememberMe <sup>2</sup>| Nie| Wyświetla pole wyboru [Zachowaj mnie zalogowane.](custom-policy-keep-me-signed-in.md) Możliwe wartości: `true` `false` , lub (domyślnie). |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy [rozpoznawanie oświadczeń](claim-resolver-overview.md) jest uwzględnione w profilu technicznym. Możliwe wartości: `true` `false`  , lub (domyślnie). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw to na `true`. |

Uwagi:
1. Dostępne dla definicji zawartości `unifiedssp` [DataUri](contentdefinitions.md#datauri) typu , lub `unifiedssd`.
1. Dostępne dla definicji zawartości `unifiedssp` [DataUri](contentdefinitions.md#datauri) typu , lub `unifiedssd`. [Układ strony w wersji](page-layout.md) 1.1.0 i powyżej.

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKey element** nie jest używany.
