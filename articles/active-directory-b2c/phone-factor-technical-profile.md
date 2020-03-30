---
title: Definiowanie profilu technicznego czynnika telefonu w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny czynnika telefonu w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3a0511a19477f3d76baf9c453316c5348cc31397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332655"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego współczynnika telefonu w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę rejestrowania i weryfikowania numerów telefonów. Ten profil techniczny:

- Zapewnia interfejs użytkownika do interakcji z użytkownikiem.
- Używa definicji zawartości do kontrolowania wyglądu i działania.
- Obsługuje zarówno połączenia telefoniczne, jak i wiadomości tekstowe w celu zweryfikowannia numeru telefonu.
- Obsługuje wiele numerów telefonów. Użytkownik może wybrać jeden z numerów telefonów do zweryfikowania.  
- Jeśli podany jest numer telefonu, interfejs użytkownika czynnika telefonu prosi użytkownika o zweryfikowanie numeru telefonu. Jeśli nie podano, prosi użytkownika o zarejestrowanie nowego numeru telefonu.
- Zwraca oświadczenie wskazujące, czy użytkownik podał nowy numer telefonu. Można użyć tego oświadczenia, aby zdecydować, czy numer telefonu powinny być zachowywane do profilu użytkownika usługi Azure AD.  

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C dla czynnika telefonu:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

W poniższym przykładzie przedstawiono profil techniczny współczynnika telefonu do rejestracji i sprawdzania poprawności:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Oświadczenia wejściowe

InputClaims element musi zawierać następujące oświadczenia. Nazwę oświadczenia można również mapować na nazwę zdefiniowaną w profilu technicznym współczynnika telefonu. 

```XML
<InputClaims>
  <!--A unique identifier of the user. The partner claim type must be set to `UserId`. -->
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <!--A claim that contains the phone number. If the claim is empty, Azure AD B2C asks the user to enroll a new phone number. Otherwise, it asks the user to verify the phone number. -->
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
</InputClaims>
```

Poniższy przykład pokazuje przy użyciu wielu numerów telefonów. Aby uzyskać więcej informacji, zobacz [przykładowe zasady](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

InputClaimsTransformations element może zawierać kolekcję InputClaimsTransformation elementów, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed przedstawieniem ich do strony współczynnika telefonu.

## <a name="output-claims"></a>Oświadczenia wyjściowe

OutputClaims element zawiera listę oświadczeń zwróconych przez profil techniczny czynnika telefonu.

```xml
<OutputClaims>
  <!-- The verified phone number. The partner claim type must be set to `Verified.OfficePhone`. -->
  <OutputClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="Verified.OfficePhone" />
  <!-- Indicates whether the new phone number has been entered by the user. The partner claim type must be set to `newPhoneNumberEntered`. -->
  <OutputClaim ClaimTypeReferenceId="newPhoneNumberEntered" PartnerClaimType="newPhoneNumberEntered" />
</OutputClaims>
```

OutputClaimsTransformations element może zawierać kolekcję OutputClaimsTransformation elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKey element** nie jest używany.


## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId (ida) | Tak | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym profilem technicznym. |
| Ręczna numer NumerEntry Dozwolone| Nie | Określ, czy użytkownik może ręcznie wprowadzić numer telefonu. Możliwe `true` wartości: `false` lub (domyślnie).|

### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Elementy interfejsu użytkownika strony uwierzytelniania szesnaskowego telefonu mogą być [zlokalizowane.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Następne kroki

- Sprawdź konta społecznościowe i lokalne za pomocą pakietu startowego [usługi MFA.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)

