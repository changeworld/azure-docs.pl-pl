---
title: JSON przykłady przekształcania oświadczeń tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: JSON oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 88d447f86dd54fc9479a6d7d2006b9a8639ad09e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60396852"
---
# <a name="json-claims-transformations"></a>JSON oświadczeń przekształcenia

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera przykłady dotyczące używania przekształcenia oświadczeń JSON schematu, struktura środowiska tożsamości w usłudze Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Pobierz określony element z danymi w formacie JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputJson | string | ClaimTypes, używane przez Przekształcanie oświadczeń można pobrać elementu. |
| InputParameter | claimToExtract | string | Nazwa elementu JSON, który ma zostać wyodrębniony. |
| oświadczenie outputClaim | extractedClaim | string | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane, wartość elementu określona w _claimToExtract_ parametr wejściowy. |

W poniższym przykładzie wyodrębnione przekształcania oświadczeń `emailAddress` element z danych JSON: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Parametr wejściowy:
    - **claimToExtract**: emailAddress
- Oświadczeń danych wyjściowych: 
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Pobierz listę określonych elementów z danymi w formacie Json.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | jsonSourceClaim | string | ClaimTypes, używane przez Przekształcanie oświadczeń do uzyskania oświadczeń. |
| InputParameter | errorOnMissingClaims | wartość logiczna | Określa, czy należy zgłosić błąd, jeśli jedno z oświadczeń brakuje. |
| InputParameter | includeEmptyClaims | string | Określ, czy zawierają puste oświadczenia. |
| InputParameter | jsonSourceKeyName | string | Nazwa klucza elementu |
| InputParameter | jsonSourceValueName | string | Nazwa wartości elementu |
| oświadczenie outputClaim | Collection | ciąg, int, atrybut typu wartość logiczna i daty/godziny |Lista oświadczeń do wyodrębnienia. Nazwy oświadczenia powinna być równa podanej w _jsonSourceClaim_ oświadczeń przychodzących. |

W poniższym przykładzie przekształcania oświadczeń wyodrębnia poniższe oświadczenia: wiadomości e-mail (ciąg), displayName (ciąg), membershipNum (int), aktywne (wartość logiczna) i Data urodzenia (Data/godzina) z danych JSON.

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Oświadczeń wejściowych:
  - **jsonSourceClaim**: [{"key": "email", "value": "someone@example.com"}, {"key": "displayName", "value": "Ktoś"}, {"key": "membershipNum", "value": 6353399}, {"key": "aktywny", "value": true}, {"key": "Data urodzenia", "value": "1980-09-23T00:0 0:00Z"}]
- Parametry wejściowe:
    - **errorOnMissingClaims**: false
    - **includeEmptyClaims**: false
    - **jsonSourceKeyName**: klucz
    - **jsonSourceValueName**: wartość
- Oświadczeń danych wyjściowych:
  - **adres e-mail**: "someone@example.com"
  - **displayName**: "Ktoś"
  - **membershipNum**: 6353399
  - **aktywne**: true
  - **Data urodzenia**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Pobiera określony element liczbowych (long) z danymi w formacie JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputJson | string | ClaimTypes, używane przez Przekształcanie oświadczeń można pobrać oświadczenia. |
| InputParameter | claimToExtract | string | Nazwa elementu JSON do wyodrębnienia. |
| oświadczenie outputClaim | extractedClaim | długi | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation, wartość elementu określona w _claimToExtract_ parametrów wejściowych. |

W poniższym przykładzie wyodrębnia przekształcania oświadczeń `id` element z danych JSON.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Ktoś", "id": 6353399}
- Parametry wejściowe
    - **claimToExtract**: identyfikator
- Oświadczeń danych wyjściowych: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Pobiera pierwszy element z tablicy danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputJsonClaim | string | ClaimTypes, które są używane przez Przekształcanie oświadczeń do pobrania elementu z tablicy JSON. |
| oświadczenie outputClaim | extractedClaim | string | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation, pierwszy element w tablicy JSON. |

W poniższym przykładzie przekształcania oświadczeń wybiera pierwszy element (adres e-mail) od tablicę JSON `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Oświadczeń danych wyjściowych: 
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konwertuje dane XML do formatu JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | xml | string | ClaimTypes, używane przez Przekształcanie oświadczeń przeprowadzenie konwersji danych z pliku XML do formatu JSON. |
| oświadczenie outputClaim | json | string | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation, dane w formacie JSON. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

W poniższym przykładzie przekształcania oświadczeń konwertuje następujące dane XML do formatu JSON.

#### <a name="example"></a>Przykład
Oświadczeń wejściowych:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Oświadczeń wychodzących:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

