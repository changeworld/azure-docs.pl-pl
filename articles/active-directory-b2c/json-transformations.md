---
title: Przykłady transformacji oświadczeń JSON dla schematu programu Identity Experience Framework Azure Active Directory B2C | Microsoft Docs
description: Przykłady transformacji oświadczeń JSON dla schematu struktury obsługi tożsamości Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ff70b2f54304c83f70ff578e1947d752aafb34a7
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064159"
---
# <a name="json-claims-transformations"></a>Przekształcenia oświadczeń JSON

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń JSON schematu platformy Identity Experience w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Pobierz określony element z danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputJson | ciąg | Elementy Claims, które są używane przez transformację oświadczeń do pobrania elementu. |
| InputParameter | claimToExtract | ciąg | Nazwa elementu JSON, który ma zostać wyodrębniony. |
| Oświadczenie outputclaim | extractedClaim | ciąg | Element ClaimType, który jest generowany po wywołaniu tej transformacji oświadczeń, jest wartością elementu określoną w parametrze wejściowym _claimToExtract_ . |

W poniższym przykładzie transformacja oświadczeń wyekstrahowano `emailAddress` element z danych JSON:`{"emailAddress": "someone@example.com", "displayName": "Someone"}`

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

- Oświadczenia wejściowe:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Ktoś"}
- Parametr wejściowy:
    - **claimToExtract**: EmailAddress
- Oświadczenia wyjściowe:
  - **extractedClaim**:someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Pobierz listę określonych elementów z danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | jsonSourceClaim | ciąg | Oświadczenia są używane przez transformację oświadczeń do pobierania oświadczeń. |
| InputParameter | errorOnMissingClaims | boolean | Określa, czy zgłosić błąd, jeśli brakuje jednego z oświadczeń. |
| InputParameter | includeEmptyClaims | ciąg | Określ, czy mają być dołączane puste oświadczenia. |
| InputParameter | jsonSourceKeyName | ciąg | Nazwa klucza elementu |
| InputParameter | jsonSourceValueName | ciąg | Nazwa wartości elementu |
| Oświadczenie outputclaim | Collection | String, int, Boolean i DateTime |Lista oświadczeń do wyodrębnienia. Nazwa żądania powinna być taka sama jak określona w _jsonSourceClaim_ wejściowym. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia następujące oświadczenia: adres e-mail (ciąg), displayName (String), membershipNum (int), Active (Boolean) i DataUrodzenia (DateTime) z danych JSON.

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

- Oświadczenia wejściowe:
  - **jsonSourceClaim**: [{"Key": "email", "value": "someone@example.com"}, {"Key": "DisplayName", "value": "ktoś"}, {"Key": "membershipNum", "value": 6353399}, {"Key": "Active", "value": true}, {"Key": "DataUrodzenia", "value": "1980-09-23T00:00:00Z "}]
- Parametry wejściowe:
    - **errorOnMissingClaims**: FAŁSZ
    - **includeEmptyClaims**: FAŁSZ
    - **jsonSourceKeyName**: klucz
    - **jsonSourceValueName**: wartość
- Oświadczenia wyjściowe:
  - **poczta e-mail**:someone@example.com""
  - **displayName**: Otwarciu
  - **membershipNum**: 6353399
  - **aktywne**: prawda
  - **Data urodzenia**: 1980 – 09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Pobiera określony liczbowy (Long) element z danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputJson | ciąg | Oświadczenia są używane przez transformację oświadczeń do uzyskania oświadczenia. |
| InputParameter | claimToExtract | ciąg | Nazwa elementu JSON do wyodrębnienia. |
| Oświadczenie outputclaim | extractedClaim | long | Element ClaimType wygenerowany po tym ClaimsTransformation został wywołany, wartość elementu określona w parametrach wejściowych _claimToExtract_ . |

W poniższym przykładzie transformacja oświadczeń wyodrębnia `id` element z danych JSON.

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

- Oświadczenia wejściowe:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Ktoś", "ID": 6353399}
- Parametry wejściowe
    - **claimToExtract**: Identyfikator
- Oświadczenia wyjściowe:
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Pobiera pierwszy element z tablicy danych JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputJsonClaim | ciąg | Elementy Claims, które są używane przez transformację oświadczeń do pobierania elementu z tablicy JSON. |
| Oświadczenie outputclaim | extractedClaim | ciąg | Element ClaimType, który jest generowany po wywołaniu tego ClaimsTransformation, jest pierwszym elementem w tablicy JSON. |

W poniższym przykładzie transformacja oświadczeń wyodrębnia pierwszy element (adres e-mail) z tablicy `["someone@example.com", "Someone", 6353399]`json.

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

- Oświadczenia wejściowe:
  - **inputJsonClaim**: ["someone@example.com", "ktoś", 6353399]
- Oświadczenia wyjściowe:
  - **extractedClaim**:someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konwertuje dane XML do formatu JSON.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | xml | ciąg | Oświadczenia są używane przez transformację oświadczeń do konwersji danych z formatu XML na format JSON. |
| Oświadczenie outputclaim | json | ciąg | W formacie JSON jest wywoływana wartość oświadczenia, która jest generowana po wywołaniu tej ClaimsTransformation. |

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

W poniższym przykładzie transformacja oświadczeń konwertuje następujące dane XML do formatu JSON.

#### <a name="example"></a>Przykład
Dane wejściowe:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Zgłoszenie wyjściowe:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

