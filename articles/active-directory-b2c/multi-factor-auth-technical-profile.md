---
title: Profile techniczne usługi Azure MFA w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Odwołanie do zasad niestandardowych dla profilów technicznych usługi Azure Multi-Factor Authentication (MFA) w usłudze Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c9ed0e329b498112feafaf21c34e85ea436cbb77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332817"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiowanie profilu technicznego usługi Azure MFA w zasadach niestandardowych usługi Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę weryfikacji numeru telefonu przy użyciu usługi Azure Multi-Factor Authentication (MFA). Ten profil techniczny służy do generowania i wysyłania kodu na numer telefonu, a następnie weryfikowania kodu. Profil techniczny usługi Azure MFA może również zwrócić komunikat o błędzie.  Profil techniczny sprawdzania poprawności sprawdza poprawność danych dostarczonych przez użytkownika przed kontynuowaniem podróży użytkownika. W profilu technicznym sprawdzania poprawności na stronie z własnym potwierdzeniem jest wyświetlany komunikat o błędzie.

Ten profil techniczny:

- Nie zapewnia interfejsu do interakcji z użytkownikiem. Zamiast tego interfejs użytkownika jest wywoływany z [samodzielnie potwierdzonego](self-asserted-technical-profile.md) profilu technicznego lub [formantu wyświetlania](display-controls.md) jako [profil techniczny sprawdzania poprawności.](validation-technical-profile.md)
- Używa usługi Azure MFA do generowania i wysyłania kodu na numer telefonu, a następnie weryfikuje kod.  
- Sprawdza poprawność numeru telefonu za pomocą wiadomości tekstowych.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

W poniższym przykładzie pokazano profil techniczny usługi Azure MFA:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Wyślij SMS

Pierwszym trybem tego profilu technicznego jest wygenerowanie kodu i wysłanie go. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** element zawiera listę oświadczeń do wysłania do usługi Azure MFA. Można również mapować nazwę oświadczenia na nazwę zdefiniowaną w profilu technicznym usługi MFA.

| ClaimReferenceId (ida) | Wymagany | Opis |
| --------- | -------- | ----------- |
| userPrincipalName | Tak | Identyfikator użytkownika, który jest właścicielem numeru telefonu. |
| phoneNumber | Tak | Numer telefonu, na który chcesz wysłać kod SMS. |
| Companyname | Nie |Nazwa firmy w sms. Jeśli nie podano, używana jest nazwa aplikacji. |
| locale | Nie | Ustawienia regionalne wiadomości SMS. Jeśli nie podano, używane są ustawienia regionalne przeglądarki użytkownika. |

**InputClaimsTransformations** element może zawierać kolekcję **InputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wysłaniem do usługi Azure MFA.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure MFA nie zwraca żadnych **OutputClaims**, w związku z tym nie ma potrzeby określania oświadczeń danych wyjściowych. Można jednak dołączyć oświadczenia, które nie są zwracane przez dostawcę tożsamości `DefaultValue` usługi Azure MFA, o ile ustawisz atrybut.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **OneWaySMS**.  |

#### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Następujące metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych po wysłaniu błędu programu SMS. Metadane powinny być skonfigurowane w [samodzielnie potwierdzonym](self-asserted-technical-profile.md) profilu technicznym. Komunikaty o błędach mogą być [zlokalizowane](localization-string-ids.md#azure-mfa-error-messages).

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| UżytkownikMessageIfCouldntSendSms | Nie | Komunikat o błędzie użytkownika, jeśli podany numer telefonu nie akceptuje wiadomości SMS. |
| UserMessageIfInvalidFormat | Nie | Komunikat o błędzie użytkownika, jeśli podany numer telefonu nie jest prawidłowym numerem telefonu. |
| UżytkownikMessageIfServerError | Nie | Komunikat o błędzie użytkownika, jeśli serwer napotkał błąd wewnętrzny. |
| UserMessageIfThrottled| Nie | Komunikat o błędzie użytkownika, jeśli żądanie zostało ograniczone.|

### <a name="example-send-an-sms"></a>Przykład: wysyłanie wiadomości SMS

W poniższym przykładzie przedstawiono profil techniczny usługi Azure MFA, który jest używany do wysyłania kodu za pośrednictwem wiadomości SMS.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
  <DisplayName>Send Sms</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">OneWaySMS</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
    <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Weryfikowanie kodu

Drugim trybem tego profilu technicznego jest zweryfikowanie kodu. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** element zawiera listę oświadczeń do wysłania do usługi Azure MFA. Można również mapować nazwę oświadczenia na nazwę zdefiniowaną w profilu technicznym usługi MFA.

| ClaimReferenceId (ida) | Wymagany | Opis |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Tak | Ten sam numer telefonu, który był wcześniej używany do wysyłania kodu. Służy również do lokalizowania sesji weryfikacji telefonicznej. |
| verificationCode  | Tak | Kod weryfikacyjny podany przez użytkownika, który ma zostać zweryfikowany |

**InputClaimsTransformations** element może zawierać kolekcję **InputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wywołaniem usługi Azure MFA.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure MFA nie zwraca żadnych **OutputClaims**, w związku z tym nie ma potrzeby określania oświadczeń danych wyjściowych. Można jednak dołączyć oświadczenia, które nie są zwracane przez dostawcę tożsamości `DefaultValue` usługi Azure MFA, o ile ustawisz atrybut.

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **weryfikuj** |

#### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Następujące metadane mogą służyć do konfigurowania komunikatów o błędach wyświetlanych po niepowodzeniu weryfikacji kodu. Metadane powinny być skonfigurowane w [samodzielnie potwierdzonym](self-asserted-technical-profile.md) profilu technicznym. Komunikaty o błędach mogą być [zlokalizowane](localization-string-ids.md#azure-mfa-error-messages).

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| UżytkownikMessageIfMaxAllowedCodeRetryReached| Nie | Komunikat o błędzie użytkownika, jeśli użytkownik próbował kod weryfikacyjny zbyt wiele razy. |
| UżytkownikMessageIfServerError | Nie | Komunikat o błędzie użytkownika, jeśli serwer napotkał błąd wewnętrzny. |
| UserMessageIfThrottled| Nie | Komunikat o błędzie użytkownika, jeśli żądanie jest ograniczone.|
| UżytkownikMessageIfWrongCodeEntered| Nie| Komunikat o błędzie użytkownika, jeśli kod wprowadzony do weryfikacji jest nieprawidłowy.|

### <a name="example-verify-a-code"></a>Przykład: sprawdź kod

W poniższym przykładzie przedstawiono profil techniczny usługi Azure MFA używany do weryfikacji kodu.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
