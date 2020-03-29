---
title: Przykłady przekształcania oświadczeń ogólnych dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Ogólne przykłady transformacji oświadczeń dla schematu struktury środowiska tożsamości (IEF) usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: afdf2f531ede30d868123d89cac94fcfae070384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188549"
---
# <a name="general-claims-transformations"></a>Przekształcenia roszczeń ogólnych

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia przekształceń oświadczeń ogólnych schematu struktury środowiska tożsamości w usłudze Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="copyclaim"></a>Kopiuj Roszczenia

Skopiuj wartość oświadczenia do innej. Obie reklamy muszą być tego samego typu.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim | ciąg, int | Typ oświadczenia, który ma zostać skopiowany. |
| WynikClaim | outputClaim | ciąg, int | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |

Ta transformacja oświadczeń służy do kopiowania wartości z ciągu lub oświadczenia liczbowego do innego oświadczenia. Poniższy przykład kopiuje wartość oświadczenia zewnętrznego Email do oświadczenia e-mail.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Przykład

- Oświadczenia wejściowe:
    - **inputClaim**:bob@contoso.com
- Oświadczenia wyjściowe:
    - **outputClaim**:bob@contoso.com

## <a name="doesclaimexist"></a>CzyClaimExist

Sprawdza, czy **inputClaim** istnieje lub nie i ustawia **outputClaim** true lub false odpowiednio.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | inputClaim |Dowolne | Oświadczenie wejściowe, którego istnienie musi zostać zweryfikowane. |
| WynikClaim | outputClaim | wartość logiczna | ClaimType, który jest produkowany po tym ClaimsTransformation został wywołany. |

Ta transformacja oświadczeń służy do sprawdzania, czy oświadczenie istnieje lub zawiera dowolną wartość. Zwracana wartość jest wartością logiczną, która wskazuje, czy oświadczenie istnieje. Poniższy przykład sprawdza, czy adres e-mail istnieje.

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

- Oświadczenia wejściowe:
  - **inputClaim**:someone@contoso.com
- Oświadczenia wyjściowe:
  - **outputClaim**: prawda

## <a name="hash"></a>Skrót

Hash pod warunkiem zwykły tekst przy użyciu soli i tajemnicy. Algorytm mieszania używany jest SHA-256.

| Element | TransformClaimType (Typ transformacji) | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| InputClaim (własnach wejściowych) | Postaci zwykłego tekstu | ciąg | Oświadczenie wejściowe, które ma być zaszyfrowane |
| InputClaim (własnach wejściowych) | Soli | ciąg | Parametr soli. Można utworzyć wartość losową, używając `CreateRandomString` transformacji oświadczeń. |
| Inputparameter | randomizerSecret | ciąg | Wskazuje istniejący **klucz zasad**usługi Azure AD B2C . Aby utworzyć nowy klucz zasad: w dzierżawie usługi Azure AD B2C w obszarze **Zarządzanie**wybierz pozycję **Identity Experience Framework**. Wybierz **klawisze zasad,** aby wyświetlić klucze, które są dostępne w dzierżawie. Wybierz pozycję **Dodaj**. W obszarze **Opcje**wybierz **pozycję Ręczny**. Podaj nazwę (prefiks *B2C_1A_* może zostać dodany automatycznie.). W polu **tekstowym Tajne** wprowadź dowolny klucz tajny, którego chcesz użyć, na przykład 1234567890. Aby uzyskać **opcję Użycie klucza,** wybierz **pozycję Podpis**. Wybierz **pozycję Utwórz**. |
| WynikClaim | hash | ciąg | ClaimType, który jest produkowany po tej transformacji oświadczeń został wywołany. Oświadczenie skonfigurowane w `plaintext` inputClaim. |

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

- Oświadczenia wejściowe:
  - **zwykły tekst**:MyPass@word1
  - **sól**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Oświadczenia wyjściowe:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
