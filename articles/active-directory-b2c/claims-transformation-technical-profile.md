---
title: Definiowanie profilu technicznego przekształcania oświadczeń w zasadach niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Definiowanie profilu technicznego przekształcania oświadczeń w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0a2904bec34978a33d25534c9e9b32552191ad88
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705324"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definiowanie profilu technicznego przekształcania oświadczeń w zasadach niestandardowych usługi Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny przekształcania oświadczeń umożliwia wywoływanie oświadczeń danych wyjściowych przekształceń do manipulowania wartością oświadczenia, weryfikowania oświadczeń lub ustawić wartości domyślne dla zestawu oświadczeń wyjściowych.

## <a name="protocol"></a>Protokół

**Nazwa** atrybutu **protokołu** element musi być równa `Proprietary`. **Obsługi** atrybutu musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Profil techniczny przekształcania oświadczeń można znaleźć w poniższym przykładzie:

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Oświadczeń danych wyjściowych

**OutputClaims** element jest obowiązkowy. Należy podać, że co najmniej jeden wyjściowy oświadczenia zwrócone przez profil techniczny. Poniższy przykład pokazuje, jak ustawić wartości domyślne na oświadczeń danych wyjściowych:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Dane wyjściowe oświadczenia przekształcenia

**OutputClaimsTransformations** element może zawierać zbiór **OutputClaimsTransformation** elementy, które są używane do modyfikowania oświadczeń lub wygenerować nowe. Następujące wywołania profilu technicznego **RemoveAlternativeSecurityIdByIdentityProvider** przekształcania oświadczeń. To oświadczeń usuwa przekształcania społeczności rozpoznać na podstawie kolekcji **AlternativeSecurityIds**. Oświadczeń danych wyjściowych tego profilu technicznego **identityProvider2**, która jest równa `facebook.com`, i **AlternativeSecurityIds**, który zawiera listę tożsamości społecznościowych skojarzony z tym Użytkownik, po usunięciu facebook.com tożsamości.

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

Profil techniczny przekształcania oświadczeń umożliwia wykonywanie transformacji roszczenia kroku aranżacji podróży dowolnego użytkownika. W poniższym przykładzie kroku aranżacji wywołania jeden z profilów techniczne odłączania, takich jak **odłączania-Facebook-OAUTH**. Ten profil techniczny wywołuje oświadczenia profilu technicznego przekształcania **RemoveAlternativeSecurityIdByIdentityProvider**, która generuje nowy **AlternativeSecurityIds2** oświadczenia, który zawiera Lista tożsamości użytkownika w sieciach społecznościowych, podczas usuwania tożsamość usługi Facebook z kolekcji.

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

## <a name="use-a-validation-technical-profile"></a>Użyj profilu technicznego sprawdzania poprawności

Profil techniczny przekształcania oświadczeń może służyć do sprawdzania poprawności informacji. W poniższym przykładzie [samodzielnie określonych profilu technicznego](self-asserted-technical-profile.md) o nazwie **LocalAccountSignUpWithLogonEmail** prosi użytkownika o wprowadzenie wiadomości e-mail, dwa razy, a następnie wywołuje [weryfikacji technicznej profil](validation-technical-profile.md) o nazwie **weryfikacji E-mail** do weryfikacji wiadomości e-mail. **Weryfikacji E-mail** profilu technicznego wywołuje przekształcania oświadczeń **AssertEmailAreEqual** porównać dwa oświadczenia **e-mail** i **emailRepeat** i zgłosić wyjątek, jeśli nie są równe, zgodnie z określonym porównania.

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

Profil techniczny wywołania przekształcania oświadczeń **AssertEmailAreEqual** oświadczeń transformacji, który potwierdza, że wiadomości e-mail przez użytkownika są takie same.

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

A własnym potwierdzone profilu technicznego można wywołać profilu technicznego sprawdzania poprawności i Pokaż komunikat o błędzie, jak to określono w **UserMessageIfClaimsTransformationStringsAreNotEqual** metadanych.

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
