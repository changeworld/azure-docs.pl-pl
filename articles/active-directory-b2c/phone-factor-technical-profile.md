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
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e2b30e8f6bcbe7c0e739455f4942712f68ff8404
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437450"
---
# <a name="define-a-phone-factor-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego współczynnika telefonu w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) zapewnia obsługę rejestrowania i weryfikowania numerów telefonów. Ten profil techniczny:

- Udostępnia interfejs użytkownika do interakcji z użytkownikiem, aby zweryfikować lub zarejestrować numer telefonu.
- Obsługuje połączenia telefoniczne i wiadomości tekstowe w celu zweryfikowannia numeru telefonu.
- Obsługuje wiele numerów telefonów. Użytkownik może wybrać jeden z numerów telefonów do zweryfikowania.  
- Zwraca oświadczenie wskazujące, czy użytkownik podał nowy numer telefonu. Można użyć tego oświadczenia, aby zdecydować, czy numer telefonu powinny być zachowywane do profilu użytkownika usługi Azure AD B2C.  
- Używa [definicji zawartości](contentdefinitions.md) do kontrolowania wyglądu i działania.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C dla czynnika telefonu:`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

W poniższym przykładzie przedstawiono profil techniczny współczynnika telefonu do rejestracji i sprawdzania poprawności:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims-transformations"></a>Przekształcenia oświadczeń wejściowych

InputClaimsTransformations element może zawierać kolekcję przekształceń oświadczeń wejściowych, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych. Następujące wejściowe oświadczenia transformacji `UserId` generuje oświadczenie, które jest używane później w kolekcji oświadczeń wejściowych.

```xml
<InputClaimsTransformations>
  <InputClaimsTransformation ReferenceId="CreateUserIdForMFA" />
</InputClaimsTransformations>
```

## <a name="input-claims"></a>Oświadczenia wejściowe

InputClaims element musi zawierać następujące oświadczenia. Nazwę oświadczenia można również mapować na nazwę zdefiniowaną w profilu technicznym współczynnika telefonu. 

|  Typ danych| Wymagany | Opis |
| --------- | -------- | ----------- | 
| ciąg| Tak | Unikatowy identyfikator użytkownika. Nazwa oświadczenia lub PartnerClaimType musi `UserId`być ustawiona na . Twierdzenie to nie powinno zawierać danych osobowych.|
| ciąg| Tak | Lista typów oświadczeń. Każde oświadczenie zawiera jeden numer telefonu. Jeśli którekolwiek z oświadczeń wejściowych nie zawiera numeru telefonu, użytkownik zostanie poproszony o zarejestrowanie i zweryfikowanie nowego numeru telefonu. Zweryfikowany numer telefonu jest zwracany jako oświadczenie wyjściowe. Jeśli jedno z oświadczeń wejściowych zawiera numer telefonu, użytkownik jest proszony o jego zweryfikowanie. Jeśli wiele oświadczeń wejściowych zawiera numer telefonu, użytkownik jest proszony o wybranie i zweryfikowanie jednego z numerów telefonów. |

Poniższy przykład pokazuje przy użyciu wielu numerów telefonów. Aby uzyskać więcej informacji, zobacz [przykładowe zasady](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

OutputClaims element zawiera listę oświadczeń zwróconych przez profil techniczny czynnika telefonu.

|  Typ danych| Wymagany | Opis |
|  -------- | ----------- |----------- |
| wartość logiczna | Tak | Wskazuje, czy nowy numer telefonu został wprowadzony przez użytkownika. Nazwę oświadczenia lub PartnerClaimType należy ustawić na`newPhoneNumberEntered`|
| ciąg| Tak | Zweryfikowany numer telefonu. Nazwa oświadczenia lub PartnerClaimType musi `Verified.OfficePhone`być ustawiona na .|

OutputClaimsTransformations element może zawierać kolekcję OutputClaimsTransformation elementów, które są używane do modyfikowania oświadczeń danych wyjściowych lub generowania nowych.

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

**CryptographicKey element** nie jest używany.


## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId (ida) | Tak | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym profilem technicznym. |
| Ręczna numer NumerEntry Dozwolone| Nie | Określ, czy użytkownik może ręcznie wprowadzić numer telefonu. Możliwe wartości: `true` `false` , lub (domyślnie).|
| setting.authenticationMode | Nie | Metoda sprawdzania poprawności numeru telefonu. Możliwe `sms`wartości: `phone`, `mixed` , lub (domyślnie).|
| ustawienie.autodial| Nie| Określ, czy profil techniczny powinien automatycznie wybierać lub automatycznie wysyłać SMS-a. Możliwe wartości: `true` `false` , lub (domyślnie). Automatyczne wybieranie `setting.authenticationMode` numeru wymaga `sms`ustawionego `phone`metadanych na , lub . Kolekcja oświadczeń wejściowych musi mieć jeden numer telefonu. |

### <a name="ui-elements"></a>Elementy interfejsu użytkownika

Elementy interfejsu użytkownika strony uwierzytelniania szesnaskowego telefonu mogą być [zlokalizowane.](localization-string-ids.md#azure-mfa-error-messages)

## <a name="next-steps"></a>Następne kroki

- Sprawdź konta społecznościowe i lokalne za pomocą pakietu startowego [usługi MFA.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa)
