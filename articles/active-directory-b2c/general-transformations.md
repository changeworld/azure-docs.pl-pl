---
title: Przykłady przekształcania oświadczeń ogólne tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Przykłady przekształcania oświadczeń ogólne tożsamości środowisko Framework schematu z usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 6a9a819e75e487999a2b50ae758b8d9c6c716a4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397162"
---
# <a name="general-claims-transformations"></a>Przekształcenia oświadczeń ogólne

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ten artykuł zawiera przykłady dotyczące używania przekształcenia oświadczeń ogólne schematu, struktura środowiska tożsamości w usłudze Azure Active Directory (Azure AD) B2C. Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Sprawdza, czy **oświadczenie inputClaim** istnieje lub nie, a następnie ustawia **oświadczenie outputClaim** na wartość true lub false odpowiednio.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | Oświadczenie InputClaim |Dowolne | Oświadczeń wejściowych, których istnienie muszą zostać zweryfikowane. |
| oświadczenie outputClaim | oświadczenie outputClaim | wartość logiczna | Typ oświadczenia, które są generowane po wywołaniu tego ClaimsTransformation. |

Użyj tego przekształcania do Sprawdź, czy oświadczenie istnieje lub zawiera wszystkie wartości oświadczeń. Wartość zwracana jest wartość logiczna wskazująca, czy istnieje oświadczenia. Poniższy przykład sprawdza, czy istnieje adres e-mail.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
  - **oświadczenie inputClaim**: someone@contoso.com
- Oświadczeń danych wyjściowych: 
    - **oświadczenie outputClaim**: true

## <a name="hash"></a>Skrót

Skrótu podany tekst zwykły przy użyciu ziarna i klucz tajny.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie InputClaim | zwykły tekst | string | Oświadczeń wejściowych do zaszyfrowania |
| Oświadczenie InputClaim | ziarna | string | Parametr ziarna. Możesz utworzyć losową wartość, przy użyciu `CreateRandomString` przekształcania oświadczeń. |
| InputParameter | randomizerSecret | string | Wskazuje na istniejące usługi Azure AD B2C **klucze zasad**. Aby utworzyć nowe konto: W ramach dzierżawy usługi Azure AD B2C wybierz **ustawieniami B2C > Struktura środowiska tożsamości**. Wybierz **klucze zasad** do wyświetlania kluczy, które są dostępne w Twojej dzierżawie. Wybierz pozycję **Dodaj**. Aby uzyskać **opcje**, wybierz opcję **ręczne**. Podaj nazwę (prefiks B2C_1A_ mogą zostać dodane automatycznie.). W polu klucza tajnego wprowadź wszystkie hasła, które chcesz użyć, takie jak 1234567890. Wybierz użycie klucza **klucz tajny**. Wybierz pozycję **Utwórz**. |
| oświadczenie outputClaim | Skrót | string | Typ oświadczenia, które są generowane po tym przekształcania oświadczeń zostało wywołane. Oświadczenie skonfigurowane w `plaintext` oświadczenie inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczeń wejściowych:
    - **PlainText**: MyPass@word1
    - **ziarna**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Oświadczeń danych wyjściowych: 
    - **oświadczenie outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



