---
title: Konta społecznościowego przykłady przekształcania oświadczeń tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konta społecznościowego oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f6da199beb292f193d97eee309ca40dd74f81f8e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64697720"
---
# <a name="social-accounts-claims-transformations"></a>Przekształcenia oświadczeń kont społecznościowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W usłudze Azure Active Directory (Azure AD) B2C tożsamości konta w sieci społecznościowej są przechowywane w `userIdentities` atrybutu **alternativeSecurityIdCollection** typ oświadczenia. Każdy element na **alternativeSecurityIdCollection** określa wystawcy (tożsamość Nazwa dostawcy, takie jak facebook.com) i `issuerUserId`, czyli identyfikator unikatowy użytkownika dla wystawcy.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Ten artykuł zawiera przykłady dotyczące korzystania z konta społecznościowego przekształcenia oświadczeń schematu, struktura środowiska tożsamości w usłudze Azure AD B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Tworzy reprezentację JSON właściwość alternativeSecurityId użytkownika, który może służyć w wywołaniach do usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [schematu firmy AlternativeSecurityId](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#AlternativeSecurityIdType).

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | key | string | Typ oświadczenia, który określa identyfikator unikatowy użytkownika używanej przez dostawcę tożsamości dla sieci społecznościowej. |
| Oświadczenie InputClaim | identityProvider | string | Typ oświadczenia, który określa nazwę dostawcy tożsamości konta w sieci społecznościowej, takich jak facebook.com. |
| oświadczenie outputClaim | alternativeSecurityId | string | Typ oświadczenia, które są generowane po wywołaniu ClaimsTransformation. Zawiera informacje o tożsamości użytkownika konta w sieci społecznościowej. **Wystawcy** jest wartością `identityProvider` oświadczenia. **IssuerUserId** jest wartością `key` oświadczenia w formacie base64. |

Korzystanie z oświadczeń to przekształcenie, aby wygenerować `alternativeSecurityId` typu oświadczenia. Jest używany przez wszystkie tożsamości dla sieci społecznościowej dostawcy profile techniczne, takie jak `Facebook-OAUTH`. Następujące przekształcania oświadczeń odbiera identyfikator użytkownika konta w sieci społecznościowej i nazwę dostawcy tożsamości. Dane wyjściowe tego profilu technicznego jest format ciągu JSON, który może służyć w usłudze Azure AD directory services.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="socialIdpUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **klucz**: 12334
    - **identityProvider**: Facebook.com
- Oświadczeń danych wyjściowych:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Dodaje `AlternativeSecurityId` do `alternativeSecurityIdCollection` oświadczenia.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Element | string | Oświadczenia, które mają zostać dodane do oświadczeń wychodzących. |
| Oświadczenie InputClaim | kolekcja | alternativeSecurityIdCollection | ClaimTypes, używanych przez przekształcania oświadczeń, jeśli są dostępne w zasadach. Jeśli nie dostarczono, przekształcania oświadczeń dodaje `item` na końcu kolekcji. |
| oświadczenie outputClaim | kolekcja | alternativeSecurityIdCollection | ClaimTypes, które są generowane po wywołaniu tego ClaimsTransformation. Nowa kolekcja, który zawiera elementy z danych wejściowych `collection` i `item`. |

Poniższy przykład łączy nowej tożsamości społecznościowych, przy użyciu istniejącego konta. Aby utworzyć link nowej tożsamości społecznościowych:
1. W **AAD UserReadUsingAlternativeSecurityId** i **AAD UserReadUsingObjectId** profile techniczne, dane wyjściowe użytkownika **alternativeSecurityIds** oświadczenia.
1. Poproś użytkownika o zalogowanie się jednego z dostawców tożsamości, które nie są skojarzone z tym użytkownikiem.
1. Za pomocą **CreateAlternativeSecurityId** oświadczeń przekształcenia, Utwórz nową **alternativeSecurityId** oświadczenia typu, o nazwie `AlternativeSecurityId2`
1. Wywołaj **AddItemToAlternativeSecurityIdCollection** przekształcania, aby dodać oświadczeń **AlternativeSecurityId2** oświadczenia do istniejących **AlternativeSecurityIds** oświadczenie.
1. Utrwalanie **alternativeSecurityIds** oświadczenie do konta użytkownika

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **element**: {"Wystawca": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **Kolekcja**: [{"Wystawca": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
- Oświadczeń danych wyjściowych:
    - **Kolekcja**: [{"Wystawca": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Wystawca": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Zwraca listę wystawców z **alternativeSecurityIdCollection** oświadczenia do nowego **stringCollection** oświadczenia.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Element ClaimType używany do pobrania listy dostawców tożsamości (Wystawca). |
| oświadczenie outputClaim | identityProvidersCollection | właściwości stringCollection | ClaimTypes, które są generowane po wywołaniu tego ClaimsTransformation. Lista dostawców tożsamości skojarzenia z oświadczeń przychodzących alternativeSecurityIdCollection |

Następujące przekształcania oświadczeń odczytuje użytkownika **alternativeSecurityIds** oświadczeń i wyodrębnia listę nazw dostawcy tożsamości skojarzone z tym kontem. Użyj wyjściowego **identityProvidersCollection** pokazanie listy dostawców tożsamości skojarzonego z kontem użytkownika. Ewentualnie na stronie Wybieranie dostawcy tożsamości, filtrowanie listy dostawców tożsamości, w oparciu o dane wyjściowe **identityProvidersCollection** oświadczenia. Tak użytkownik może wybrać do połączenia nowej tożsamości społecznościowych, który nie jest już skojarzony z kontem.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczeń wejściowych:
    - **alternativeSecurityIdCollection**: [{"Wystawca": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Wystawca": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Oświadczeń danych wyjściowych:
    - **identityProvidersCollection**: ["facebook.com", "google.com"]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Usuwa **AlternativeSecurityId** z **alternativeSecurityIdCollection** oświadczenia.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | identityProvider | string | Typ oświadczenia, który zawiera nazwę dostawcy tożsamości, aby były usuwane z kolekcji. |
| Oświadczenie InputClaim | kolekcja | alternativeSecurityIdCollection | ClaimTypes, używanych przez przekształcania oświadczeń. Przekształcanie oświadczeń usuwa dostawca tożsamości z kolekcji. |
| oświadczenie outputClaim | kolekcja | alternativeSecurityIdCollection | ClaimTypes, które są generowane po wywołaniu tego ClaimsTransformation. Nowa kolekcja, po dostawca tożsamości został usunięty z kolekcji. |

Poniższy przykład rozłączysz jednej tożsamości społecznościowych, przy użyciu istniejącego konta. Aby odłączyć tożsamości społecznościowych:
1. W **AAD UserReadUsingAlternativeSecurityId** i **AAD UserReadUsingObjectId** profile techniczne, dane wyjściowe użytkownika **alternativeSecurityIds** oświadczenia.
2. Poproś użytkownika o wybranie konta społecznościowe, którego należy usunąć z listy dostawców tożsamości, które są skojarzone z tym użytkownikiem.
3. Wywołaj profil techniczne przekształcania oświadczeń, który wywołuje **RemoveAlternativeSecurityIdByIdentityProvider** przekształcania, które usunięte wybranej tożsamości społecznościowych, przy użyciu nazwy dostawcy tożsamości oświadczeń.
4. Utrwalanie **alternativeSecurityIds** oświadczenie do konta użytkownika.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **Dostawca identityProvider**: facebook.com
    - **Kolekcja**: [{"Wystawca": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}, {"Wystawca": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Oświadczeń danych wyjściowych:
    - **Kolekcja**: [{"Wystawca": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}]
