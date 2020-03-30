---
title: Definiowanie profilu technicznego transformacji oświadczeń
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny transformacji oświadczeń w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84c1cf798e88e4067da8a495c1591143d2ee1bd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189790"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego transformacji oświadczeń w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny transformacji oświadczeń umożliwia wywołanie przekształceń oświadczeń wyjściowych w celu manipulowania wartościami oświadczeń, sprawdzania poprawności oświadczeń lub ustawiania wartości domyślnych dla zestawu oświadczeń danych wyjściowych.

## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`używanego przez usługę Azure AD B2C: .

W poniższym przykładzie pokazano profil techniczny transformacji oświadczeń:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Oświadczenia wyjściowe

**OutputClaims** element jest obowiązkowe. Należy podać co najmniej jedno oświadczenie wyjściowe zwrócone przez profil techniczny. W poniższym przykładzie pokazano, jak ustawić wartości domyślne w oświadczeń danych wyjściowych:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Przekształcenia oświadczeń wyjściowych

**OutputClaimsTransformations** element może zawierać kolekcję **OutputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń lub generowania nowych. Poniższy profil techniczny wywołuje **RemoveAlternativeSecurityIdByIdentityProvider** roszczeń transformacji. Ta transformacja roszczeń usuwa identyfikacji społecznej z kolekcji **AlternativeSecurityIds**. Oświadczenia wyjściowe tego profilu technicznego są **identityProvider2**, który jest ustawiony na `facebook.com`, i **AlternativeSecurityIds**, który zawiera listę tożsamości społecznych skojarzonych z tym użytkownikiem po usunięciu facebook.com tożsamości.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Profil techniczny transformacji oświadczeń umożliwia wykonanie transformacji oświadczeń z kroku aranżacji dowolnej podróży użytkownika. W poniższym przykładzie krok aranżacji wywołuje jeden z odłączonych profilów technicznych, takich jak **UnLink-Facebook-OAUTH**. Ten profil techniczny wywołuje profil techniczny transformacji oświadczeń **RemoveAlternativeSecurityIdByIdentityProvider**, który generuje nowe **oświadczenie AlternativeSecurityIds2,** które zawiera listę tożsamości społecznościowych użytkowników, jednocześnie usuwając tożsamość Facebooka z kolekcji.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | Nie | W przypadku oświadczeń wejściowych i wyjściowych określa, czy [rozpoznawanie oświadczeń](claim-resolver-overview.md) jest uwzględnione w profilu technicznym. Możliwe wartości: `true` `false`  , lub (domyślnie). Jeśli chcesz użyć programu rozpoznawania oświadczeń w profilu technicznym, ustaw to na `true`. |

## <a name="use-a-validation-technical-profile"></a>Korzystanie z profilu technicznego sprawdzania poprawności

Profil techniczny transformacji oświadczeń może służyć do sprawdzania poprawności informacji. W poniższym przykładzie [samodzielnie potwierdzony profil techniczny](self-asserted-technical-profile.md) o nazwie **LocalAccountSignUpWithLogonEmail** prosi użytkownika o dwukrotne wprowadzenie wiadomości e-mail, a następnie wywołuje [profil techniczny sprawdzania poprawności](validation-technical-profile.md) o nazwie **Validate-Email,** aby sprawdzić poprawność wiadomości e-mail. Profil techniczny **validate-email** wywołuje transformację oświadczeń **AssertEmailAreEqual,** aby porównać dwie **wiadomości e-mail** i **emailRepeat**oświadczeń i zgłosić wyjątek, jeśli nie są równe zgodnie z określonym porównaniem.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Profil techniczny transformacji oświadczeń wywołuje **AssertEmailAreEqual** transformacja oświadczeń, który twierdzi, że wiadomości e-mail dostarczone przez użytkownika są takie same.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Samodzielnie potwierdzony profil techniczny może wywołać profil techniczny sprawdzania poprawności i wyświetlić komunikat o błędzie określony w **metadanych UserMessageIfClaimsTransformationStringsAreNotEqual.**

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
