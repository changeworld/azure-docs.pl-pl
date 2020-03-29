---
title: Definiowanie profilu technicznego dla wystawcy SAML w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny wystawcy tokenu saml (SamL) tokenu asercji zabezpieczeń w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967272"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego dla wystawcy tokenu SAML w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) emituje kilka typów tokenów zabezpieczających podczas przetwarzania każdego przepływu uwierzytelniania. Profil techniczny wystawcy tokenu SAML emituje token SAML, który jest zwracany z powrotem do aplikacji jednostki uzależniającej (dostawcy usług). Zazwyczaj ten profil techniczny jest ostatnim krokiem aranżacji w podróży użytkownika.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `None`na . Ustaw element **OutputTokenFormat** na `SAML2`.

W poniższym przykładzie `Saml2AssertionIssuer`przedstawiono profil techniczny dla:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Oświadczenia dotyczące danych wejściowych, wyjściowych i utrzymywać

**InputClaims**, **OutputClaims**i **PersistClaims** elementy są puste lub nieobecne. **InutputClaimsTransformations** i **OutputClaimsTransformations** elementy są również nieobecne.

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| IssuerUri | Nie | Nazwa wystawcy, która pojawia się w odpowiedzi SAML. Wartość powinna mieć taką samą nazwę, jak skonfigurowana w aplikacji jednostki uzależniona. |

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

CryptographicKey element zawiera następujące atrybuty:

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| Podpisywanie metadanych | Tak | Certyfikat X509 (zestaw kluczy RSA) używany do podpisywania metadanych SAML. Usługa Azure AD B2C używa tego klucza do podpisywania metadanych. |
| SamlMessageSigning (Podpis samlMessage)| Tak| Określ certyfikat X509 (zestaw kluczy RSA), który ma być używany do podpisywania komunikatów SAML. Usługa Azure AD B2C używa tego `<samlp:Response>` klucza do podpisywania odpowiedzi wysyłanej do jednostki uzależniającej.|

## <a name="session-management"></a>Zarządzanie sesjami

Aby skonfigurować sesje SAML usługi Azure AD B2C między aplikacją `UseTechnicalProfileForSessionManagement` jednostki uzależnianej, atrybut elementu, odwołanie do [samlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) sesji SWO.

## <a name="next-steps"></a>Następne kroki

Zobacz następujący artykuł, na przykład przy użyciu profilu technicznego emitenta SAML:

- [Rejestrowanie aplikacji SAML w usłudze Azure AD B2C](connect-with-saml-service-providers.md)












