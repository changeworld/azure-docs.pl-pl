---
title: Przykłady transformacji kont społecznościowych dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przykłady transformacji kont społecznościowych dla schematu struktury ief (Identity Experience Framework) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb713651aca266ab2546ff26c3cd0175a4cbc289
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183758"
---
# <a name="social-accounts-claims-transformations"></a>Przekształcenia kont społecznościowych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W usłudze Azure Active Directory B2C (Azure AD B2C) tożsamości kont społecznościowych są przechowywane w `userIdentities` atrybucie typu oświadczenia **alternativeSecurityIdCollection.** Każdy element w **alternativeSecurityIdCollection** określa wystawcę (nazwa dostawcy tożsamości, takich `issuerUserId`jak facebook.com) i , który jest unikatowy identyfikator użytkownika dla wystawcy.

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

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń konta społecznościowego schematu struktury środowiska tożsamości w usłudze Azure AD B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Tworzy reprezentację JSON alternatywnej Właściwość użytkownikaSecurityId, która może być używana w wywołaniach usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [schemat AlternativeSecurityId.](https://docs.microsoft.com/graph/api/resources/alternativesecurityid)

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | key | ciąg | ClaimType, który określa unikatowy identyfikator użytkownika używany przez dostawcę tożsamości społecznościowej. |
| InputClaim (własnach wejściowych) | identityProvider | ciąg | ClaimType, który określa nazwę dostawcy tożsamości konta społecznościowego, takich jak facebook.com. |
| WynikClaim | alternativeSecurityId | ciąg | ClaimType, który jest produkowany po ClaimsTransformation został wywołany. Zawiera informacje o tożsamości użytkownika konta społecznościowego. **Emitent** jest wartością `identityProvider` roszczenia. Identyfikator **issuerUserId** jest wartością `key` oświadczenia w formacie base64. |

Ta transformacja oświadczeń `alternativeSecurityId` służy do generowania typu oświadczeń. Jest on używany przez wszystkie profile techniczne dostawcy `Facebook-OAUTH`tożsamości społecznej, takie jak . Następujące przekształcenia oświadczeń odbiera identyfikator konta społecznościowego użytkownika i nazwę dostawcy tożsamości. Dane wyjściowe tego profilu technicznego to format ciągu JSON, który może być używany w usługach katalogowych usługi Azure AD.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **klucz**: 12334
    - **identityProvider**: Facebook.com
- Oświadczenia wyjściowe:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection AddItemToAlternativeSecurityIdCollection AddItemToAlternativeSecurityIdCollection AddIt

Dodaje `AlternativeSecurityId` do `alternativeSecurityIdCollection` oświadczenia.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | element | ciąg | ClaimType, które mają zostać dodane do oświadczenia wyjściowego. |
| InputClaim (własnach wejściowych) |  — kolekcja | alternatywnaSecurityIdCollection | ClaimTypes, które są używane przez transformację oświadczeń, jeśli są dostępne w zasadach. Jeśli pod warunkiem, transformacja oświadczeń dodaje `item` na końcu kolekcji. |
| WynikClaim |  — kolekcja | alternatywnaSecurityIdCollection | ClaimTypes, które są produkowane po tym ClaimsTransformation został wywołany. Nowa kolekcja, która zawiera zarówno `collection` `item`elementy z danych wejściowych i . |

Poniższy przykład łączy nową tożsamość społecznościową z istniejącym kontem. Aby połączyć nową tożsamość społeczną:
1. W profilach technicznych **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingObjectId** należy wyprowadzić oświadczenie **alternatywnesecurityids** użytkownika.
1. Poproś użytkownika o zalogowanie się u jednego z dostawców tożsamości, którzy nie są skojarzoni z tym użytkownikiem.
1. Za pomocą **CreateAlternativeSecurityId** roszczeń transformacji, utworzyć nowy **alternativeSecurityId** typu oświadczenia o nazwie`AlternativeSecurityId2`
1. Wywołanie **AddItemToAlternativeSecurityIdCollection** roszczeń transformacji, aby dodać **AlternativeSecurityId2** roszczenia do istniejącego **AlternativeSecurityIds** oświadczenia.
1. Utrwalić **alternativeSecurityIds** oświadczenia do konta użytkownika

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

- Oświadczenia wejściowe:
    - **pozycja**: { "wystawca": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **kolekcja**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Oświadczenia wyjściowe:
    - **kolekcja**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "emitent": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformacja

Zwraca listę wystawców z **alternatywnego OświadczeniaSecurityIdCollection** do nowego **oświadczenia stringCollection.**

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | alternatywnaSecurityIdCollection | alternatywnaSecurityIdCollection | ClaimType, który ma być używany do uzyskania listy dostawców tożsamości (wystawcy). |
| WynikClaim | tożsamośćProvidersCollection | Stringcollection | ClaimTypes, które są produkowane po tym ClaimsTransformation został wywołany. Lista dostawców tożsamości skojarzyć z alternatywnegoSecurityIdCollection oświadczenia wejściowego |

Następujące przekształcenia oświadczeń odczytuje użytkownika **alternativeSecurityIds** oświadczenia i wyodrębnia listę nazw dostawców tożsamości skojarzonych z tym kontem. Użyj danych wyjściowych **identityProvidersCollection,** aby wyświetlić użytkownikowi listę dostawców tożsamości skojarzonych z kontem. Lub na stronie wyboru dostawcy tożsamości filtruj listę dostawców tożsamości na podstawie **oświadczenia tożsamości wyjściowejProvidersCollection.** Użytkownik może więc wybrać połączenie nowej tożsamości społecznościowej, która nie jest jeszcze skojarzona z kontem.

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

- Oświadczenia wejściowe:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Oświadczenia wyjściowe:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>UsuńAlternativeSecurityIdByIdentityProvider

Usuwa **AlternativeSecurityId** z **alternativeSecurityIdCollection** oświadczenia.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | identityProvider | ciąg | ClaimType, który zawiera nazwę dostawcy tożsamości do usunięcia z kolekcji. |
| InputClaim (własnach wejściowych) |  — kolekcja | alternatywnaSecurityIdCollection | ClaimTypes, które są używane przez transformację oświadczeń. Transformacja oświadczeń usuwa identityProvider z kolekcji. |
| WynikClaim |  — kolekcja | alternatywnaSecurityIdCollection | ClaimTypes, które są produkowane po tym ClaimsTransformation został wywołany. Nowa kolekcja, po identityProvider usunięte z kolekcji. |

Poniższy przykład odłącza jedną z tożsamości społecznościowych z istniejącym kontem. Aby odłączyć tożsamość społecznościową:
1. W profilach technicznych **AAD-UserReadUsingAlternativeSecurityId** i **AAD-UserReadUsingObjectId** należy wyprowadzić oświadczenie **alternatywnesecurityids** użytkownika.
2. Poproś użytkownika, aby wybrał konto społecznościowe, które ma usunąć z listy dostawców tożsamości skojarzonych z tym użytkownikiem.
3. Wywołanie profilu technicznego transformacji oświadczeń, który wywołuje **RemoveAlternativeSecurityIdByIdentityProvider** roszczeń transformacji, który usunął wybranej tożsamości społecznej, przy użyciu nazwy dostawcy tożsamości.
4. Utrwalić **alternativeSecurityIds** oświadczenia do konta użytkownika.

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

- Oświadczenia wejściowe:
    - **identityProvider**: facebook.com
    - **kolekcja**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "emitent": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Oświadczenia wyjściowe:
    - **kolekcja**: [ { "emitent": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
