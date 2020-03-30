---
title: Definiowanie profilu technicznego usługi Application Insights w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Zdefiniuj profil techniczny usługi Application Insights w zasadach niestandardowych w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f50373b0841b7626bc405f121015c15ae1587a97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80108577"
---
# <a name="define-an-application-insights-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Definiowanie profilu technicznego usługi Application Insights w zasadach niestandardowych usługi Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Usługa Azure Active Directory B2C (Azure AD B2C) obsługuje wysyłanie danych zdarzeń bezpośrednio do [usługi Application Insights](../azure-monitor/app/app-insights-overview.md) przy użyciu klucza instrumentacji dostarczonego do usługi Azure AD B2C.  Dzięki profilowi technicznemu usługi Application Insights można uzyskać szczegółowe i dostosowane dzienniki zdarzeń dla podróży użytkownika do:

* Uzyskaj szczegółowe informacje na temat zachowania użytkownika.
* Rozwiązywanie problemów z własnymi zasadami w programach programistów lub w produkcji.
* Zmierz wydajność.
* Tworzenie powiadomień z usługi Application Insights.


## <a name="protocol"></a>Protocol (Protokół)

Atrybut **Nazwa** elementu **Protokołu** musi być ustawiony `Proprietary`na . Atrybut **programu obsługi** musi zawierać w pełni kwalifikowaną nazwę zestawu obsługi protokołu, który jest używany przez usługę Azure AD B2C dla usługi Application Insights:`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

W poniższym przykładzie przedstawiono wspólny profil techniczny usługi Application Insights. Inne profile techniczne usługi Application Insights obejmują AzureInsights-Common, aby wykorzystać jego konfigurację.  

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Oświadczenia wejściowe

**InputClaims** element zawiera listę oświadczeń do wysłania do usługi Application Insights. Nazwę oświadczenia można również mapować na nazwę, która ma być wyświetlana w usłudze Application Insights. W poniższym przykładzie pokazano, jak wysłać telemetrie do usługi Application Insights. Właściwości zdarzenia są dodawane za pośrednictwem `{property:NAME}`składni , gdzie NAZWA jest właściwością dodawany do zdarzenia. Wartość domyślna może być wartością statyczną lub wartością rozpoznawaną przez jeden z obsługiwanych [programów rozpoznawania oświadczeń.](claim-resolver-overview.md)

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

**InputClaimsTransformations** element może zawierać kolekcję **InputClaimsTransformation** elementów, które są używane do modyfikowania oświadczeń wejściowych lub generowania nowych przed wysłaniem do usługi Application Insights.

## <a name="persist-claims"></a>Utrwalanie oświadczeń

Element PersistedClaims nie jest używany.

## <a name="output-claims"></a>Oświadczenia wyjściowe

OutputClaims i OutputClaimsTransformations elementy nie są używane.

## <a name="cryptographic-keys"></a>Klucze kryptograficzne

CryptographicKey element nie jest używany.


## <a name="metadata"></a>Metadane

| Atrybut | Wymagany | Opis |
| --------- | -------- | ----------- |
| InstrumentationKey (Narzędzie instrumentacyjne)| Tak | [Klucz instrumentacji](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)usługi Application Insights , który będzie używany do rejestrowania zdarzeń. | 
| Tryb deweloperski| Nie | Wartość logiczna wskazująca, czy tryb dewelopera jest włączony. Możliwe `true` wartości: `false` lub (domyślnie). Te metadane kontroluje sposób buforowania zdarzeń. W środowisku deweloperskim z minimalnym woluminem zdarzeń włączenie trybu dewelopera powoduje natychmiastowe wysłanie zdarzeń do usługi Application Insights.|  
|Wyłącztelemetrię |Nie |Wartość logiczna, która wskazuje, czy dane telemetryczne powinny być włączone, czy nie. Możliwe `true` wartości: `false` lub (domyślnie).| 


## <a name="next-steps"></a>Następne kroki

- [Tworzenie zasobu usługi Application Insights](../azure-monitor/app/create-new-resource.md)
- Dowiedz się, jak [śledzić zachowanie użytkowników w usłudze Azure Active Directory B2C przy użyciu usługi Application Insights](analytics-with-application-insights.md)
