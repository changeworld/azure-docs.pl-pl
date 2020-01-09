---
title: Profile techniczne usługi Azure MFA w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Niestandardowe informacje dotyczące zasad dla profilów technicznych usługi Azure Multi-Factor Authentication (MFA) w Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8aaea6b2afb4d89e6e667edba0eeba2f4ddcca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480218"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Zdefiniuj profil techniczny usługi Azure MFA w zasadach niestandardowych Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę weryfikowania numeru telefonu przy użyciu usługi Azure Multi-Factor Authentication (MFA). Ten profil techniczny służy do generowania i wysyłania kodu do numeru telefonu, a następnie sprawdzania kodu.

Profil techniczny usługi Azure MFA może również zwrócić komunikat o błędzie. Integrację z usługą Azure MFA można zaprojektować za pomocą **weryfikacji profilu technicznego**. Profil techniczny weryfikacji wywołuje usługę Azure MFA. Profil techniczny weryfikacji weryfikuje dane dostarczone przez użytkownika przed kontynuowaniem podróży użytkownika. W profilu technicznym weryfikacji na stronie z potwierdzeniem zostanie wyświetlony komunikat o błędzie.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **name** elementu **Protocol** musi mieć wartość `Proprietary`. Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu programu obsługi protokołu, który jest używany przez Azure AD B2C:

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Poniższy przykład przedstawia profil techniczny usługi Azure MFA:

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>Wyślij wiadomość SMS

Pierwszy tryb tego profilu technicznego polega na wygenerowaniu kodu i wysłaniu go. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do usługi Azure MFA. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w profilu technicznym usługi MFA.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- |
| userPrincipalName | Tak | Identyfikator użytkownika, który jest właścicielem numeru telefonu. |
| phoneNumber | Tak | Numer telefonu, na który ma zostać wysłany kod SMS. |
| companyName | Nie |Nazwa firmy w wiadomości SMS. Jeśli nie zostanie podany, zostanie użyta nazwa aplikacji. |
| locale | Nie | Ustawienia regionalne wiadomości SMS. Jeśli nie zostanie podany, używane są ustawienia regionalne użytkownika w przeglądarce. |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wysłaniem do usługi Azure MFA.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure MFA nie zwraca żadnych **OutputClaims**, więc nie ma potrzeby określania oświadczeń wyjściowych. Można jednak uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości usługi Azure MFA, tak długo, jak ustawisz atrybut `DefaultValue`.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

### <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **OneWaySMS**.  |
| UserMessageIfInvalidFormat | Nie | Niestandardowy komunikat o błędzie, jeśli podany numer telefonu nie jest prawidłowym numerem telefonu |
| UserMessageIfCouldntSendSms | Nie | Niestandardowy komunikat o błędzie, jeśli podany numer telefonu nie akceptuje wiadomości SMS |
| UserMessageIfServerError | Nie | Niestandardowy komunikat o błędzie, jeśli serwer napotkał błąd wewnętrzny |

### <a name="return-an-error-message"></a>Zwróć komunikat o błędzie

Zgodnie z opisem w temacie [Metadata](#metadata)można dostosować komunikat o błędzie wyświetlany użytkownikowi dla różnych przypadków błędów. Te komunikaty można później zlokalizować, tworząc prefiks ustawień regionalnych. Przykład:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>Przykład: Wyślij wiadomość SMS

Poniższy przykład przedstawia profil techniczny usługi Azure MFA, który jest używany do wysyłania kodu za pośrednictwem wiadomości SMS.

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

## <a name="verify-code"></a>Weryfikuj kod

Drugi tryb tego profilu technicznego polega na sprawdzeniu kodu. Dla tego trybu można skonfigurować następujące opcje.

### <a name="input-claims"></a>Oświadczenia wejściowe

Element **InputClaims** zawiera listę oświadczeń do wysłania do usługi Azure MFA. Możesz również zmapować nazwę swojego zgłoszenia na nazwę zdefiniowaną w profilu technicznym usługi MFA.

| ClaimReferenceId | Wymagane | Opis |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| Tak | Ten sam numer telefonu, który został wcześniej użyty do wysłania kodu. Służy również do lokalizowania sesji weryfikacyjnej telefonu. |
| verificationCode  | Tak | Kod weryfikacyjny dostarczony przez użytkownika do zweryfikowania |

Element **InputClaimsTransformations** może zawierać kolekcję elementów **InputClaimsTransformation** , które są używane do modyfikowania oświadczeń wejściowych lub generować nowe przed wywołaniem usługi Azure MFA.

### <a name="output-claims"></a>Oświadczenia wyjściowe

Dostawca protokołu usługi Azure MFA nie zwraca żadnych **OutputClaims**, więc nie ma potrzeby określania oświadczeń wyjściowych. Można jednak uwzględnić oświadczenia, które nie są zwracane przez dostawcę tożsamości usługi Azure MFA, tak długo, jak ustawisz atrybut `DefaultValue`.

Element **OutputClaimsTransformations** może zawierać kolekcję elementów **OutputClaimsTransformation** , które są używane do modyfikowania oświadczeń wyjściowych lub generowania nowych.

## <a name="metadata"></a>Metadane

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Operacja | Tak | Musi być **zweryfikowana** |
| UserMessageIfInvalidFormat | Nie | Niestandardowy komunikat o błędzie, jeśli podany numer telefonu nie jest prawidłowym numerem telefonu |
| UserMessageIfWrongCodeEntered | Nie | Niestandardowy komunikat o błędzie, jeśli kod wprowadzony do weryfikacji jest błędny |
| UserMessageIfMaxAllowedCodeRetryReached | Nie | Niestandardowy komunikat o błędzie, jeśli użytkownik próbuje wykonać kod weryfikacyjny zbyt wiele razy |
| UserMessageIfThrottled | Nie | Niestandardowy komunikat o błędzie, jeśli użytkownik jest ograniczany |
| UserMessageIfServerError | Nie | Niestandardowy komunikat o błędzie, jeśli serwer napotkał błąd wewnętrzny |

### <a name="return-an-error-message"></a>Zwróć komunikat o błędzie

Zgodnie z opisem w temacie [Metadata](#metadata)można dostosować komunikat o błędzie wyświetlany użytkownikowi dla różnych przypadków błędów. Te komunikaty można później zlokalizować, tworząc prefiks ustawień regionalnych. Przykład:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>Przykład: Weryfikowanie kodu

W poniższym przykładzie przedstawiono profil techniczny usługi Azure MFA służący do sprawdzania kodu.

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
