---
title: Odwołanie do sterowania wyświetlaniem
titleSuffix: Azure AD B2C
description: Odwołanie do formantów wyświetlania usługi Azure AD B2C. Użyj kontrolek wyświetlania do dostosowywania podróży użytkowników zdefiniowanych w zasadach niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4998fb19e42e123edd57bfcf10931d594ac4cb44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188736"
---
# <a name="display-controls"></a>Kontrolki wyświetlania

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Formant **wyświetlania** to element interfejsu użytkownika, który ma specjalne funkcje i współdziała z usługą zaplecza usługi Azure Active Directory B2C (Azure AD B2C). Umożliwia użytkownikowi wykonywanie akcji na stronie, które wywołują [profil techniczny sprawdzania poprawności](validation-technical-profile.md) na zapleczu. Formanty wyświetlania są wyświetlane na stronie i są odwoływane przez [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md).

Na poniższej ilustracji przedstawiono samodzielnie potwierdzoną stronę rejestracji z dwoma formantami wyświetlania, które sprawdzają poprawność podstawowego i pomocniczego adresu e-mail.

![Przykładowa renderowana kontrolka wyświetlania](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

 W sekcji [Metadane](self-asserted-technical-profile.md#metadata) [samodzielnie potwierdzonego profilu technicznego,](self-asserted-technical-profile.md)do którego odwołuje `DataUri` się [contentdefinition,](contentdefinitions.md) musi być ustawiona na umowę stronicową w wersji 2.0.0 lub nowszej. Przykład:

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Definiowanie elementów sterujących wyświetlania

**DisplayControl** Element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Identyfikator | Tak | Identyfikator, który jest używany dla formantu wyświetlania. Można [się do niego odwoływać](#referencing-display-controls). |
| Typcontrolu userInterface | Tak | Typ formantu wyświetlania. Obecnie obsługiwane jest [VerificationControl](display-control-verification.md) |

**DisplayControl** Element zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| WejściaClaims | 0:1 | **InputClaims** są używane do wstępnego zapełnienia wartość oświadczeń, które mają być zbierane od użytkownika. |
| DisplayClaims (Wyświetlclaims) | 0:1 | **DisplayClaims** są używane do reprezentowania oświadczeń, które mają być zbierane od użytkownika. |
| OutputClaims (Roszczenia wyjściowe) | 0:1 | **OutputClaims** są używane do reprezentowania oświadczeń, które mają być zapisane tymczasowo dla tego **DisplayControl**. |
| Akcje | 0:1 | **Akcje** są używane do listy profilów technicznych sprawdzania poprawności do wywołania dla działań użytkownika dzieje się w front-end. |

### <a name="input-claims"></a>Oświadczenia wejściowe

W formancie wyświetlania można użyć **InputClaims** elementów do wstępnego wypełniania wartości oświadczeń do pobrania od użytkownika na stronie. Wszelkie **InputClaimsTransformations** można zdefiniować w samodzielnie potwierdzonym profilu technicznym, który odwołuje się do tego formantu wyświetlania.

Poniższy przykład wstępnie wypełnia adres e-mail do weryfikacji z adresem już obecny.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Wyświetlanie oświadczeń

Każdy typ formantu wyświetlania wymaga innego zestawu oświadczeń wyświetlania, [oświadczeń danych wyjściowych](#output-claims)i [akcji](#display-control-actions) do wykonania.

Podobnie jak **oświadczenia wyświetlania** zdefiniowane w [samodzielnie potwierdzonym profilu technicznym,](self-asserted-technical-profile.md#display-claims)oświadczenia wyświetlania reprezentują oświadczenia, które mają być zbierane od użytkownika w formancie wyświetlania. Element **ClaimType,** do którego istnieje odwołanie, musi określić element **UserInputType** dla typu `TextBox` danych `DropdownSingleSelect`wejściowych użytkownika obsługiwanego przez usługę Azure AD B2C, na przykład lub . Jeśli wartość oświadczenia wyświetlania jest wymagana przez **akcję,** ustaw `true` atrybut **Wymagane,** aby wymusić na użytkowniku podanie wartości dla określonego oświadczenia wyświetlania.

Niektóre oświadczenia wyświetlania są wymagane dla niektórych typów kontroli wyświetlania. Na przykład **verificationcode** jest wymagany dla kontroli wyświetlania typu **VerificationControl**. Użyj atrybutu **ControlClaimType,** aby określić, który DisplayClaim jest wyznaczony dla tego wymaganego oświadczenia. Przykład:

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Oświadczenia wyjściowe

**Oświadczenia wyjściowe** formantu wyświetlania nie są wysyłane do następnego kroku aranżacji. Są one zapisywane tymczasowo tylko dla bieżącej sesji sterowania wyświetlaniem. Te oświadczenia tymczasowe mogą być współużytkowane między różnymi akcjami tego samego formantu wyświetlania.

Aby wyecblichować oświadczenia wyjściowe do następnego kroku aranżacji, należy użyć **OutputClaims** rzeczywistego samodzielnie potwierdzonego profilu technicznego, który odwołuje się do tego formantu wyświetlania.

### <a name="display-control-actions"></a>Akcje sterowania wyświetlaniem

**Akcje** formantu wyświetlania są procedury, które występują w usłudze Azure AD B2C zaplecza, gdy użytkownik wykonuje pewną akcję po stronie klienta (przeglądarka). Na przykład sprawdzanie poprawności do wykonania, gdy użytkownik wybierze przycisk na stronie.

Akcja definiuje listę **profili technicznych sprawdzania poprawności**. Są one używane do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyświetlania formantu wyświetlania. Profil techniczny sprawdzania poprawności sprawdza poprawność danych wejściowych użytkownika i może zwrócić błąd do użytkownika. Można użyć **ContinueOnError**, **ContinueOnSuccess**i **Warunki wstępne** w akcji kontroli wyświetlania podobne do sposobu, w jaki są używane w sprawdzania poprawności [profilów technicznych](validation-technical-profile.md) w samodzielnie potwierdzonym profilu technicznym.

Poniższy przykład wysyła kod w wiadomości e-mail lub SMS na podstawie wyboru przez użytkownika **oświadczenia mfaType.**

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Odwoływanie się do kontrolek wyświetlania

Formanty wyświetlania są odwoływane w [oświadczeń wyświetlania](self-asserted-technical-profile.md#display-claims) [samodzielnie potwierdzonego profilu technicznego](self-asserted-technical-profile.md).

Przykład:

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
