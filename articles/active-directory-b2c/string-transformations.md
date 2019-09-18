---
title: Przykłady transformacji oświadczeń ciągów dla schematu struktury środowiska tożsamości Azure Active Directory B2C | Microsoft Docs
description: Przykłady transformacji oświadczeń ciągów dla schematu struktury środowiska tożsamości Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 83379cc194f23ebff977babc7124a7bc90f4bc60
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063456"
---
# <a name="string-claims-transformations"></a>Przekształcenia oświadczeń ciągów

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń w ramach programu Identity Experience Framework w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Porównaj dwa oświadczenia i Zgłoś wyjątek, jeśli nie są one równe zgodnie z określonymi porównaniami inputClaim1, inputClaim2 i stringComparison.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Typ pierwszego wystąpienia, który ma zostać porównany. |
| Oświadczenie inputclaim | inputClaim2 | ciąg | Typ drugiego zgłoszenia, który ma zostać porównany. |
| InputParameter | stringComparison | ciąg | porównanie ciągów, jedna z wartości: Liczba porządkowa, OrdinalIgnoreCase. |

Przekształcenie oświadczeń **AssertStringClaimsAreEqual** jest zawsze wykonywane z poziomu [weryfikacji profilu technicznego](validation-technical-profile.md) , który jest wywoływany przez [własny profil techniczny](self-asserted-technical-profile.md). Metadane profilu technicznego **UserMessageIfClaimsTransformationStringsAreNotEqual** z własnym potwierdzeń są kontrolowane przez komunikat o błędzie wyświetlany użytkownikowi.

![AssertStringClaimsAreEqual wykonywanie](./media/string-transformations/assert-execution.png)

Możesz użyć tej transformacji oświadczeń, aby upewnić się, że dwa oświadczenia muszą mieć tę samą wartość. Jeśli nie, zostanie zgłoszony komunikat o błędzie. Poniższy przykład sprawdza, czy element ClaimType **strongAuthenticationEmailAddress** jest równy elementowi ClaimType **poczty e-mail** . W przeciwnym razie zostanie zgłoszony komunikat o błędzie.

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


Profil techniczny **nieinteraktywnego sprawdzania poprawności nazwy logowania** jest wywoływany przez **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** transformację oświadczeń.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Profil techniczny z własnym potwierdzeniem wywołuje profil techniczny **logowania weryfikacji — nieinteraktywny** .

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

- Oświadczenia wejściowe:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
    - Parametry wejściowe:
  - **stringComparison**: ordinalIgnoreCase
- Wynika Zgłoszono błąd

## <a name="changecase"></a>ChangeCase

Zmienia wielkość liter podanego żądania na niższą lub wielką literę w zależności od operatora.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Wartość oświadczenia, którą można zmienić. |
| InputParameter | toCase | ciąg | Jedna z następujących wartości: `LOWER` lub. `UPPER` |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczenia umożliwia zmianę dowolnego typu oświadczenia ciągu na niski lub wielką literę.

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

- Oświadczenia wejściowe:
  - **adres e-mail**:SomeOne@contoso.com
- Parametry wejściowe:
    - **toCase**: LOWER
- Oświadczenia wyjściowe:
  - **adres e-mail**:someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Tworzy w zasadach ciąg z podanego parametru wejściowego.

| Element | TransformationClaimType | Typ danych | Uwagi |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | ciąg | Ciąg, który ma zostać ustawiony. |
| Oświadczenie outputclaim | createdClaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń, z wartością określoną w parametrze wejściowym. |

Użyj tej transformacji oświadczeń, aby ustawić wartość typu oświadczenia ciągu.

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
    - **wartość**: Warunki użytkowania usługi firmy Contoso...
- Oświadczenia wyjściowe:
    - **createdClaim**: Oświadczenia TOS zawiera "warunki korzystania z usługi firmy Contoso..." wartościami.

## <a name="compareclaims"></a>CompareClaims

Ustal, czy jedno z nich jest równe innemu. Wynik jest nowym obiektem logicznym ClaimType z wartością `true` lub `false`.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Typ pierwszego wystąpienia, który ma zostać porównany. |
| Oświadczenie inputclaim | inputClaim2 | ciąg | Drugi typ zgłoszenia, który ma zostać porównany. |
| InputParameter | Zakład | ciąg | Możliwe wartości: `EQUAL` lub `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Określa, czy to porównanie ma ignorować wielkość liter podczas porównywania ciągów. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczeń służy do sprawdzania, czy oświadczenie jest równe innemu oświadczenia. Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **adresu e-mail** jest równa **Zweryfikowanemu oświadczenia adresu e-mail** .

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

- Oświadczenia wejściowe:
  - **inputClaim1**:someone@contoso.com
  - **inputClaim2**:someone@outlook.com
- Parametry wejściowe:
    - **operator**:  NIE RÓWNA SIĘ
    - **IgnoreCase**: true
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Określa, czy wartość żądania jest równa wartości parametru wejściowego.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputClaim1 | ciąg | Typ zgłoszenia, który ma zostać porównany. |
| InputParameter | Zakład | ciąg | Możliwe wartości: `EQUAL` lub `NOT EQUAL`. |
| InputParameter | compareTo | ciąg | porównanie ciągów, jedna z wartości: Liczba porządkowa, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Określa, czy to porównanie ma ignorować wielkość liter podczas porównywania ciągów. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | boolean | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Możesz użyć tej transformacji oświadczeń, aby sprawdzić, czy oświadczenie jest równe określonej wartości. Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **termsOfUseConsentVersion** jest równa `v1`.

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
- Oświadczenia wejściowe:
    - **inputClaim1**: V1
- Parametry wejściowe:
    - **CompareTo**: Wersja 1
    - **operator**: WIĘKSZY
    - **IgnoreCase**: true
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Tworzy losowy ciąg przy użyciu generatora liczb losowych. Jeśli generator liczb losowych jest typu `integer`, opcjonalnie można podać parametr inicjatora i maksymalną liczbę. Opcjonalny parametr formatu ciągu umożliwia formatowanie danych wyjściowych przy użyciu go, a opcjonalny parametr Base64 określa, czy dane wyjściowe są zakodowane w formacie base64 randomGeneratorType [GUID, Integer] oświadczenie outputclaim (ciąg).

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | ciąg | Określa wartość losową do wygenerowania `GUID` (unikatowy identyfikator globalny) lub `INTEGER` (liczba). |
| InputParameter | StringFormat — | ciąg | Obowiązkowe Sformatuj wartość losową. |
| InputParameter | base64 | boolean | Obowiązkowe Konwertuj wartość losową na format Base64. Jeśli format ciągu jest stosowany, wartość po formacie ciągu jest zaszyfrowana do Base64. |
| InputParameter | maximumNumber | int | Obowiązkowe Tylko `INTEGER` dla randomGeneratorType. Określ maksymalną liczbę. |
| InputParameter | sadzenia  | int | Obowiązkowe Tylko `INTEGER` dla randomGeneratorType. Określ inicjator dla losowej wartości. Uwaga: ten sam inicjator zwraca tę samą sekwencję liczb losowych. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Oświadczenia są tworzone po wywołaniu tej transformacji oświadczeń. Wartość losowa. |

Poniższy przykład generuje globalnie unikatowy identyfikator. Ta transformacja oświadczeń służy do tworzenia losowej nazwy UPN (główna nazwa użytkownika).

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
    - **randomGeneratorType**: GUID
- Oświadczenia wyjściowe:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Poniższy przykład generuje losową wartość całkowitą z zakresu od 0 do 1000. Wartość jest formatowana do OTP_ {Value Random}.

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
    - **StringFormat —** : OTP_{0}
    - **Base64**: false
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Sformatuj wierzytelność zgodnie z podanym ciągiem formatu. Ta transformacja używa C# `String.Format` metody.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim |ciąg |Wartość oświadczenia, która działa jako parametr {0} formatu ciągu. |
| InputParameter | StringFormat — | ciąg | Format ciągu, łącznie z {0} parametrem. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczeń służy do formatowania dowolnego ciągu z jednym parametrem {0}. Poniższy przykład tworzy element **userPrincipalName**. Wszystkie profile techniczne dostawcy tożsamości społecznościowej, takie `Facebook-OAUTH` jak wywołanie elementu.

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

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Parametry wejściowe:
    - **StringFormat —** : cpim_{0}@ {RelyingPartyTenantId}
- Oświadczenia wyjściowe:
  - **oświadczenie outputclaim**:cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Sformatuj dwa oświadczenia zgodnie z podanym ciągiem formatu. Ta transformacja używa C# metody **String. format** .

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim |ciąg | Wartość oświadczenia, która działa jako parametr {0} formatu ciągu. |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Wartość oświadczenia, która działa jako parametr {1} formatu ciągu. |
| InputParameter | StringFormat — | ciąg | Format ciągu, łącznie z {0} parametrami i. {1} |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. |

Ta transformacja oświadczeń służy do formatowania dowolnego ciągu z dwoma parametrami {0} i {1}. Poniższy przykład tworzy **DisplayName** o określonym formacie:

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

- Oświadczenia wejściowe:
    - **inputClaim1**: Janusz
    - **inputClaim2**: Fernando
- Parametry wejściowe:
    - **StringFormat —** : {0}{1}
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: Jan Fernando

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Wyszukiwanie elementu z kolekcji **ograniczeń** dotyczących roszczeń.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | mapFromClaim | ciąg | Oświadczenie zawierające tekst do wyszukania w oświadczeniach **restrictionValueClaim** z kolekcją **ograniczeń** .  |
| Oświadczenie outputclaim | restrictionValueClaim | ciąg | Zastrzeżenie, które zawiera kolekcję **ograniczeń** . Po wywołaniu przekształcenia oświadczeń wartość tego oświadczenia zawiera wartość wybranego elementu. |

Poniższy przykład wyszukuje opis komunikatu o błędzie na podstawie klucza błędu. **ResponseMsg** zawiera kolekcję komunikatów o błędach, które mają być obecne dla użytkownika końcowego lub wysyłane do jednostki uzależnionej.

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
Transformacja oświadczeń wyszukuje tekst elementu i zwraca jego wartość. Jeśli ograniczenie jest zlokalizowane przy użyciu `<LocalizedCollection>`, transformacja oświadczeń zwraca zlokalizowaną wartość.

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

- Oświadczenia wejściowe:
    - **mapFromClaim**: B2C_V1_90001
- Oświadczenia wyjściowe:
    - **restrictionValueClaim**: Nie możesz się zalogować, ponieważ jesteś małym.

## <a name="lookupvalue"></a>LookupValue

Wyszukaj wartość roszczeń z listy wartości na podstawie wartości innego żądania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | inputParameterId | ciąg | Zgłoszenie zawierające wartość wyszukiwania |
| InputParameter | |ciąg | Kolekcja inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Kontrolowanie, czy błąd jest zwracany w przypadku braku zgodnego wyszukiwania. |
| Oświadczenie outputclaim | inputParameterId | ciąg | Oświadczenia są tworzone po wywołaniu tej transformacji oświadczeń. Wartość zgodnego identyfikatora. |

Poniższy przykład wyszukuje nazwę domeny w jednej z kolekcji inputParameters. Transformacja oświadczeń wyszukuje nazwę domeny w identyfikatorze i zwraca jej wartość (Identyfikator aplikacji).

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

- Oświadczenia wejściowe:
    - **inputParameterId**: test.com
- Parametry wejściowe:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: FAŁSZ
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: c7026f88-4299-4CDB-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Wyczyść wartość danego żądania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie outputclaim | claim_to_null | ciąg | Wartość tego żądania jest równa NULL. |

Ta transformacja oświadczenia służy do usuwania zbędnych danych z zbioru właściwości oświadczeń. W związku z tym plik cookie sesji będzie mniejszy. Poniższy przykład usuwa wartość `TermsOfService` typu "typ".

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Oświadczenia wejściowe:
    - **oświadczenie outputclaim**: Witamy w aplikacji contoso. Jeśli będziesz kontynuować przeglądanie tej witryny sieci Web i korzystanie z niej, wyrażasz zgodę na przestrzeganie i powiąże się z następującymi warunkami i postanowieniami...
- Oświadczenia wyjściowe:
    - **oświadczenie outputclaim**: NULL

## <a name="parsedomain"></a>ParseDomain

Pobiera część domeny adresu e-mail.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | emailAddress | ciąg | Wartość oświadczenia, która zawiera adres e-mail. |
| Oświadczenie outputclaim | domena | ciąg | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń — domena. |

Użyj tej transformacji oświadczeń, aby przeanalizować nazwę domeny po znaku @ symbolu użytkownika. Może to być przydatne w przypadku usuwania informacji osobowych z danych inspekcji. Następująca transformacja oświadczeń pokazuje, jak przeanalizować nazwę domeny z oświadczenia **adresu e-mail** .

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

- Oświadczenia wejściowe:
  - **EmailAddress**:joe@outlook.com
- Oświadczenia wyjściowe:
    - **domena**: Outlook.com

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Sprawdza, czy oświadczenie ciągu i `matchTo` parametr wejściowy są równe, a następnie ustawia oświadczenia danych wyjściowych z wartością `stringMatchMsg` obecną w parametrach i parametrów wejściowych oraz `stringMatchMsgCode` z oświadczeniem wyjściowym porównania wynik, `true` który ma zostać ustawiony jako lub `false` na podstawie wyniku porównania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim | ciąg | Typ zgłoszenia, który ma zostać porównany. |
| InputParameter | matchTo | ciąg | Ciąg, który ma zostać porównany z `inputClaim`. |
| InputParameter | stringComparison | ciąg | Możliwe wartości: `Ordinal` lub `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | ciąg | Pierwsza wartość, która ma być ustawiona, jeśli ciągi są równe. |
| InputParameter | stringMatchMsgCode | ciąg | Druga wartość, która ma zostać ustawiona, jeśli ciągi są równe. |
| Oświadczenie outputclaim | outputClaim1 | ciąg | Jeśli ciągi są równe, to zgłoszenie wyjściowe zawiera wartość `stringMatchMsg` parametru wejściowego. |
| Oświadczenie outputclaim | outputClaim2 | ciąg | Jeśli ciągi są równe, to zgłoszenie wyjściowe zawiera wartość `stringMatchMsgCode` parametru wejściowego. |
| Oświadczenie outputclaim | stringCompareResultClaim | boolean | Typ zgłoszenia wynikowego porównania, który ma zostać ustawiony jako `true` lub `false` w oparciu o wynik porównania. |

Możesz użyć tej transformacji oświadczeń, aby sprawdzić, czy oświadczenie jest równe określonej wartości. Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **termsOfUseConsentVersion** jest równa `v1`. Jeśli tak, Zmień wartość na `v2`.

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

- Oświadczenia wejściowe:
    - **oświadczenie inputclaim**: V1
- Parametry wejściowe:
    - **matchTo**: Wersja 1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**:  B2C_V1_90005
    - **stringMatchMsgCode**:  OT jest uaktualniany do wersji 2
- Oświadczenia wyjściowe:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: OT jest uaktualniany do wersji 2
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Sprawdza, czy oświadczenie ciągu i `matchTo` parametr wejściowy są równe, a następnie ustawia oświadczenia danych wyjściowych z wartością `outputClaimIfMatched` obecną w parametrze wejściowym oraz z oświadczeniem wyjściowym porównania wynik, `true` które `false` ma zostać ustawione jako lub oparte na wynik porównania.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | claimToMatch | ciąg | Typ zgłoszenia, który ma zostać porównany. |
| InputParameter | matchTo | ciąg | Ciąg, który będzie porównywany z oświadczenie inputclaim. |
| InputParameter | stringComparison | ciąg | Możliwe wartości: `Ordinal` lub `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | ciąg | Wartość, która ma zostać ustawiona, jeśli ciągi są równe. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | ciąg | Jeśli ciągi są równe, to zgłoszenie wyjściowe zawiera wartość `outputClaimIfMatched` parametru wejściowego. Lub wartość null, jeśli ciągi nie są zgodne. |
| Oświadczenie outputclaim | stringCompareResultClaim | boolean | Typ zgłoszenia wynikowego porównania, który ma zostać ustawiony jako `true` lub `false` w oparciu o wynik porównania. |

Na przykład następujące przekształcenia oświadczeń sprawdzają, czy wartość oświadczenia **grupy wiekowej** jest równa `Minor`. Jeśli tak, zwróć wartość do `B2C_V1_90001`.

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

- Oświadczenia wejściowe:
    - **claimToMatch**: Pomocnicza
- Parametry wejściowe:
    - **matchTo**: Pomocnicza
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**:  B2C_V1_90001
- Oświadczenia wyjściowe:
    - **isMinorResponseCode**: B2C_V1_90001
    - **ismoll**: prawda

