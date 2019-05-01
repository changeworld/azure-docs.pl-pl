---
title: Ciąg przykłady przekształcania oświadczeń tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Ciąg oświadczeń przykłady przekształcania tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6c6b90cb2b24f6a87504a3a0dbd47f998b67ebc1
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707465"
---
# <a name="string-claims-transformations"></a>Ciąg oświadczeń przekształcenia

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera przykłady dotyczące używania przekształcenia oświadczeń ciągu schematu, struktura środowiska tożsamości w usłudze Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

Porównaj dwa oświadczenia i zgłosić wyjątek, jeśli nie są równe, zgodnie z określonego porównania inputClaim1, inputClaim2 i stringComparison.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputClaim1 | string | Typ pierwszego oświadczenia, który ma być porównane. |
| Oświadczenie InputClaim | inputClaim2 | string | Drugi typ oświadczenia, który ma być porównane. |
| InputParameter | stringComparison | string | porównania ciągów, jedna z wartości: Ordinal, OrdinalIgnoreCase. |

**AssertStringClaimsAreEqual** przekształcania oświadczeń jest zawsze wykonywana z [profilu technicznego weryfikacji](validation-technical-profile.md) który jest wywoływany [własnym potwierdzone profilu technicznego](self-asserted-technical-profile.md). **UserMessageIfClaimsTransformationStringsAreNotEqual** samodzielnie profilu technicznego kontroluje komunikat o błędzie, które są prezentowane użytkownikowi.

![AssertStringClaimsAreEqual execution](./media/string-transformations/assert-execution.png)

Można użyć oświadczeń to przekształcenie, aby upewnić się, dwa ClaimTypes mają taką samą wartość. W przeciwnym razie generowany jest komunikat o błędzie. Poniższy przykład sprawdza, czy **strongAuthenticationEmailAddress** oświadczenia jest równa **e-mail** typu oświadczenia. W przeciwnym razie generowany jest komunikat o błędzie. 

```XML
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


**Logowania nieinterakcyjnego** wywołania profilu technicznego weryfikacji **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** przekształcania oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Samodzielnie profilu technicznego wywołuje weryfikacji **logowania nieinterakcyjnego** profilu technicznego.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
    - Parametry wejściowe:
  - **stringComparison**: ordinalIgnoreCase
- Wynik: Zgłoszony błąd

## <a name="changecase"></a>ChangeCase 

Zmienia wielkość liter w podanej oświadczenia do obniżenia lub wielkie litery, w zależności od operatora.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputClaim1 | string | Typ oświadczenia, które można zmienić. |
| InputParameter | toCase | string | Jedną z następujących wartości: `LOWER` lub `UPPER`. |
| oświadczenie outputClaim | oświadczenie outputClaim | string | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane. |

Użyj tego przekształcania oświadczeń, aby zmienić dowolnego ciągu typu oświadczenia, aby obniżyć lub wielkie litery.  

```XML
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **adres e-mail**: SomeOne@contoso.com
- Parametry wejściowe:
    - **toCase**: LOWER
- Oświadczeń danych wyjściowych:
  - **adres e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim 

Tworzy oświadczenie ciągu z podanego parametru wejściowego w zasadach.

| Element | TransformationClaimType | Typ danych | Uwagi |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | string | Ciąg do ustawienia |
| oświadczenie outputClaim | createdClaim | string | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane, z wartością określoną w parametrze wejściowym. |

Służy to oświadczeń przekształcenia, aby ustawić ciąg wartości oświadczenia.

```XML
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Parametr wejściowy:
    - **Wartość**: Contoso warunki użytkowania usługi...
- Oświadczeń danych wyjściowych:
    - **createdClaim**: Element ClaimType TOS zawiera wartość "Contoso warunki korzystania z usługi...".

## <a name="compareclaims"></a>CompareClaims

Ustal, czy jedno oświadczenie ciągu jest równy innemu. Wynik jest nowy logiczna typu oświadczenia o wartości `true` lub `false`.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputClaim1 | string | Najpierw oświadczenia typu, który ma być porównane. |
| Oświadczenie InputClaim | inputClaim2 | string | Po drugie oświadczenia typu, który ma być porównane. |
| InputParameter | operator | string | Możliwe wartości: `EQUAL` lub `NOT EQUAL`. |
| InputParameter | ignoreCase | wartość logiczna | Określa, czy to porównanie powinien ignorować wielkość liter porównywane ciągi. |
| oświadczenie outputClaim | oświadczenie outputClaim | wartość logiczna | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane. |

Użycie oświadczeń to przekształcenie, aby sprawdzić, czy roszczenie jest taki sam, do innego roszczenia. Na przykład, następujące oświadczeń kontroli przekształcenia, jeśli wartość **e-mail** oświadczeń jest równa **Verified.Email** oświadczenia.

```XML
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Parametry wejściowe:
    - **Operator**:  NIE RÓWNA SIĘ
    - **ignoreCase**: true
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Określa, czy wartość oświadczenia jest równa wartości parametru wejściowego.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputClaim1 | string | Typ oświadczenia, który ma być porównane. |
| InputParameter | operator | string | Możliwe wartości: `EQUAL` lub `NOT EQUAL`. |
| InputParameter | Element compareTo | string | porównania ciągów, jedna z wartości: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | wartość logiczna | Określa, czy to porównanie powinien ignorować wielkość liter porównywane ciągi. |
| oświadczenie outputClaim | oświadczenie outputClaim | wartość logiczna | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane. |

Możesz użyć tego oświadczenia przekształcenia, aby sprawdzić, czy roszczenie jest równa określonej wartości. Na przykład, następujące oświadczeń kontroli przekształcenia, jeśli wartość **termsOfUseConsentVersion** oświadczeń jest równa `v1`.

```XML
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład
- Oświadczeń wejściowych:
    - **inputClaim1**: 1
- Parametry wejściowe:
    - **Element compareTo**: Wersja 1
    - **Operator**: RÓWNA SIĘ 
    - **ignoreCase**: true
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Tworzy losowy ciąg przy użyciu generator liczb losowych. Jeśli generator liczb losowych jest typu `integer`, opcjonalnie można podać parametr inicjatora i maksymalną liczbę. Parametr opcjonalny ciąg formatu zezwala sformatowane przy użyciu jego danych wyjściowych, a parametr opcjonalny base64 Określa, czy dane wyjściowe oświadczenie outputClaim randomGeneratorType [identyfikator guid, liczba całkowita] zakodowane w formacie base64 (ciąg).

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | string | Określa wartość losowe wygenerowanie, `GUID` (unikatowy identyfikator globalny) lub `INTEGER` (liczba). |
| InputParameter | stringFormat | string | [Opcjonalnie] Sformatuj wartości losowej. |
| InputParameter | base64 | wartość logiczna | [Opcjonalnie] Konwertuj wartość losową base64. Jeśli zostanie zastosowany format ciągu, wartość po ciągu formatu jest zakodowany w formacie Base64. |
| InputParameter | maximumNumber | int | [Opcjonalnie] Aby uzyskać `INTEGER` randomGeneratorType tylko. Określ maksymalną liczbę. |
| InputParameter | Inicjator  | int | [Opcjonalnie] Aby uzyskać `INTEGER` randomGeneratorType tylko. Należy określić inicjatora dla wartości losowej. Uwaga: ten sam inicjatora daje tę samą sekwencję liczb losowych. |
| oświadczenie outputClaim | oświadczenie outputClaim | string | Zostało wywołane ClaimTypes, które zostaną wygenerowane po tym przekształcania oświadczeń. Wartości losowej. |

Poniższy przykład generuje unikatowy identyfikator globalny. Oświadczeń to, że przekształcenie służy do tworzenia losowej nazwy UPN (główna nazwa użytkownika).

```XML
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Przykład

- Parametry wejściowe:
    - **randomGeneratorType**: Identyfikator GUID
- Oświadczeń danych wyjściowych: 
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Poniższy przykład generuje losową wartość całkowitą od 0 do 1000. Wartość jest formatowane OTP_ {losową wartość}.

```XML
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Parametry wejściowe:
    - **randomGeneratorType**: INTEGER
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **Base64**: false
- Oświadczeń danych wyjściowych: 
    - **oświadczenie outputClaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Format oświadczenia zgodnie z podany ciąg formatu. Ta transformacja używa języka C# `String.Format` metody.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim |string |Typ oświadczenia, który działa jako format ciągu {0} parametru. |
| InputParameter | stringFormat | string | Format ciągu, w tym {0} parametru. |
| oświadczenie outputClaim | oświadczenie outputClaim | string | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane. |

Korzystanie z oświadczeń to przekształcenie do formatu dowolny ciąg znaków z jednego parametru {0}. Poniższy przykład tworzy **userPrincipalName**. Wszystkie tożsamości dla sieci społecznościowej dostawcy profile techniczne, takie jak `Facebook-OAUTH` wywołania **CreateUserPrincipalName** do generowania **userPrincipalName**.   

```XML
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **inputClaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parametry wejściowe:
    - **stringFormat**: cpim_{0}@{RelyingPartyTenantId}
- Oświadczeń danych wyjściowych:
  - **oświadczenie outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formatuj dwa oświadczenia zgodnie z podany ciąg formatu. Ta transformacja używa języka C# **String.Format** metody.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim |string | Typ oświadczenia, który działa jako format ciągu {0} parametru. |
| Oświadczenie InputClaim | Oświadczenie InputClaim | string | Typ oświadczenia, który działa jako format ciągu {1} parametru. |
| InputParameter | stringFormat | string | Format ciągu, w tym {0} i {1} parametrów. |
| oświadczenie outputClaim | oświadczenie outputClaim | string | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane. |

Korzystanie z oświadczeń to przekształcenie do formatu dowolny ciąg, z dwoma parametrami {0} i {1}. Poniższy przykład tworzy **displayName** przy użyciu określonego formatu:

```XML
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Parametry wejściowe:
    - **stringFormat**: {0} {1}
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: Joe Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Trwa wyszukiwanie elementu z oświadczenia **ograniczeń** kolekcji.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | mapFromClaim | string | Oświadczenie, które zawiera tekst, który ma być wyszukiwana w **restrictionValueClaim** oświadczeń z **ograniczeń** kolekcji.  |
| oświadczenie outputClaim | restrictionValueClaim | string | Oświadczenie, które zawiera **ograniczeń** kolekcji. Po wywołaniu przekształcania oświadczeń wartość tego oświadczenia zawiera wartość wybranego elementu. |

Poniższy przykład pobiera opis komunikatu o błędzie na podstawie klucza błąd. **ResponseMsg** oświadczeń zawiera zbiór komunikaty o błędach, aby przedstawić użytkownikowi końcowemu lub do wysłania do jednostki uzależnionej.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Przekształcanie oświadczeń wyszukuje tekst elementu i zwraca jego wartość. Jeżeli ograniczenia są lokalizowane za pomocą `<LocalizedCollection>`, przekształcania oświadczeń zwraca zlokalizowaną wartość.

```XML
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />         
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **mapFromClaim**: B2C_V1_90001
- Oświadczeń danych wyjściowych:
    - **restrictionValueClaim**: Możesz niedomiaru zalogować, ponieważ jesteś pomocnicze.

## <a name="lookupvalue"></a>LookupValue

Wyszukaj wartość oświadczenia z listy na podstawie wartości oświadczenia innej wartości.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | inputParameterId | string | Oświadczenie, które zawiera wartość wyszukiwania |
| InputParameter | |string | Kolekcja inputParameters. |
| InputParameter | errorOnFailedLookup | wartość logiczna | Kontrolowanie, czy błąd jest zwracany, gdy nie pasującego wyszukiwania. |
| oświadczenie outputClaim | inputParameterId | string | Zostało wywołane ClaimTypes, które zostaną wygenerowane po tym przekształcania oświadczeń. Wartość identyfikatora dopasowania. |

Poniższy przykład pobiera nazwę domeny w jednej z kolekcji inputParameters. Przekształcanie oświadczeń wyszukuje nazwy domeny w identyfikatorze i zwraca jego wartość (identyfikator aplikacji).

```XML
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation> 
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **inputParameterId**: test.com
- Parametry wejściowe:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Oświadczeń danych wyjściowych:
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Wyczyść wartość danego oświadczenia.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| oświadczenie outputClaim | claim_to_null | string | Oświadczenie jej wartość na wartość NULL. |

Usuń zbędne dane z zbiór właściwości oświadczenia za pomocą tego przekształcania oświadczeń. Tak pliku cookie sesji będzie mniejsza. Poniższy przykład usuwa wartość `TermsOfService` typ oświadczenia.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczeń wejściowych:
    - **oświadczenie outputClaim**: Zapraszamy do aplikacji firmy Contoso. Jeśli nadal Przeglądaj i korzystaj z tej witryny sieci Web, zgadzasz się do wykonania i być ograniczone przez następujące warunki i postanowienia...
- Oświadczeń danych wyjściowych:
    - **oświadczenie outputClaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Pobiera część domeny adresu e-mail.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | emailAddress | string | Typ oświadczenia, który zawiera adres e-mail. |
| oświadczenie outputClaim | domena | string | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane — domeny. |

Użyj oświadczeń to przekształcenie, które można przeanalizować nazwy domeny po symbolu użytkownika @. Może to być przydatne podczas usuwania identyfikowalne dane osobowe (PII) z danych inspekcji. Następujące przekształcania oświadczeń pokazuje, jak można przeanalizować nazwy domeny z **e-mail** oświadczenia.

```XML
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **emailAddress**: joe@outlook.com
- Oświadczeń danych wyjściowych:
    - **domeny**: outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Sprawdza, czy ciąg oświadczeń i `matchTo` parametr wejściowy są równe, i zestawy danych wyjściowych oświadczenia o wartości, które są obecne w `stringMatchMsg` i `stringMatchMsgCode` parametrów wraz z oświadczeń wychodzących wynik porównania, która ma być ustawiona jako wejściowych `true` lub `false` na podstawie wyniku porównania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim | string | Typ oświadczenia należy porównać. |
| InputParameter | matchTo | string | Ciąg, który ma zostać porównane z `inputClaim`. |
| InputParameter | stringComparison | string | Możliwe wartości: `Ordinal` lub `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | string | Pierwsza wartość do ustawienia, jeśli ciągi są równe. |
| InputParameter | stringMatchMsgCode | string | Druga wartość do ustawienia, jeśli ciągi są równe. |
| oświadczenie outputClaim | outputClaim1 | string | Jeśli ciągi są equals, to oświadczenie w danych wyjściowych zawiera wartość `stringMatchMsg` parametr wejściowy. |
| oświadczenie outputClaim | outputClaim2 | string | Jeśli ciągi są equals, to oświadczenie w danych wyjściowych zawiera wartość `stringMatchMsgCode` parametr wejściowy. |
| oświadczenie outputClaim | stringCompareResultClaim | wartość logiczna | Typ, który ma być ustawiony jako oświadczeń wychodzących wynik porównania `true` lub `false` na podstawie wyniku porównania. |

Możesz użyć tego oświadczenia przekształcenia, aby sprawdzić, czy roszczenie jest równa określonej wartości. Na przykład, następujące oświadczeń kontroli przekształcenia, jeśli wartość **termsOfUseConsentVersion** oświadczeń jest równa `v1`. Jeśli tak, zmień wartość na `v2`. 

```XML
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **oświadczenie inputClaim**: 1
- Parametry wejściowe:
    - **matchTo**: Wersja 1
    - **stringComparison**: ordinalIgnoreCase 
    - **stringMatchMsg**:  B2C_V1_90005
    - **stringMatchMsgCode**:  OT została uaktualniona do wersji 2
- Oświadczeń danych wyjściowych:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: OT została uaktualniona do wersji 2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Sprawdza, czy ciąg oświadczeń i `matchTo` parametr wejściowy są równe, i zestawy danych wyjściowych oświadczenia o wartości, które są obecne w `outputClaimIfMatched` parametr wejściowy, wraz z oświadczeń wychodzących wynik porównania, która ma być ustawiona jako `true` lub `false` na podstawie wynik porównania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | claimToMatch | string | Typ oświadczenia należy porównać. |
| InputParameter | matchTo | string | Ciąg, który ma zostać porównane z oświadczenie inputClaim. |
| InputParameter | stringComparison | string | Możliwe wartości: `Ordinal` lub `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | string | Wartość można ustawić, jeśli ciągi są równe. |
| oświadczenie outputClaim | oświadczenie outputClaim | string | Jeśli ciągi są equals, to oświadczenie w danych wyjściowych zawiera wartość `outputClaimIfMatched` parametr wejściowy. Lub wartość null, jeśli ciągi są dopasowania. |
| oświadczenie outputClaim | stringCompareResultClaim | wartość logiczna | Typ, który ma być ustawiony jako oświadczeń wychodzących wynik porównania `true` lub `false` na podstawie wyniku porównania. |

Na przykład, następujące oświadczeń kontroli przekształcenia, jeśli wartość **grupy wiekowej** oświadczeń jest równa `Minor`. Jeśli tak, zwróć wartość `B2C_V1_90001`. 

```XML
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **claimToMatch**: Podrzędna
- Parametry wejściowe:
    - **matchTo**: Podrzędna
    - **stringComparison**: ordinalIgnoreCase 
    - **outputClaimIfMatched**:  B2C_V1_90001
- Oświadczeń danych wyjściowych:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: true

