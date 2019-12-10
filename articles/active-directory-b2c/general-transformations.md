---
title: Ogólne przykłady transformacji oświadczeń dla zasad niestandardowych
titleSuffix: Azure AD B2C
description: Ogólne przykłady transformacji oświadczeń dla schematu programu Identity Experience Framework (IEF) Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/27/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 639277177bf63e659e5b0ea804eca5e20f956831
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948894"
---
# <a name="general-claims-transformations"></a>Ogólne przekształcenia oświadczeń

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W tym artykule przedstawiono przykłady użycia ogólnych przekształceń oświadczeń schematu programu Identity Experience Framework w Azure Active Directory B2C (Azure AD B2C). Aby uzyskać więcej informacji, zobacz [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Sprawdza, czy **oświadczenie inputclaim** istnieje, czy nie, i odpowiednio ustawia **oświadczenie outputclaim** na wartość true lub false.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | Oświadczenie inputclaim |Dowolne | Dane wejściowe, których istnienie musi być zweryfikowane. |
| Oświadczenie outputclaim | Oświadczenie outputclaim | wartość logiczna | Wartość oświadczenia jest generowana po wywołaniu tego ClaimsTransformation. |

Ta transformacja oświadczeń służy do sprawdzania, czy oświadczenie istnieje lub zawiera dowolną wartość. Wartość zwracana jest wartością logiczną, która wskazuje, czy istnieje. Poniższy przykład sprawdza, czy adres e-mail istnieje.

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
  - **oświadczenie inputclaim**: someone@contoso.com
- Oświadczenia wyjściowe:
  - **oświadczenie outputclaim**: true

## <a name="hash"></a>Skrót

Mieszaj podany zwykły tekst przy użyciu soli i wpisu tajnego. Algorytmem wyznaczania wartości skrótu jest SHA-256.

| Element | TransformationClaimType | Typ danych | Uwagi |
| ---- | ----------------------- | --------- | ----- |
| Oświadczenie inputclaim | formacie | string | Dane wejściowe do zaszyfrowania |
| Oświadczenie inputclaim | Solo | string | Parametr soli. Można utworzyć wartość losową przy użyciu transformacji oświadczeń `CreateRandomString`. |
| InputParameter | randomizerSecret | string | Wskazuje istniejący **klucz zasad**Azure AD B2C. Aby utworzyć nowy klucz zasad: w dzierżawie Azure AD B2C w obszarze **Zarządzaj**wybierz pozycję **platforma obsługi tożsamości**. Wybierz pozycję **klucze zasad** , aby wyświetlić klucze, które są dostępne w dzierżawie. Wybierz pozycję **Dodaj**. W obszarze **Opcje**wybierz pozycję **Ręczne**. Podaj nazwę (prefiks *B2C_1A_* może zostać dodany automatycznie). W polu tekstowym **wpis tajny** wprowadź dowolne tajne, na przykład 1234567890. W obszarze **użycie klucza**wybierz pozycję **podpis**. Wybierz pozycję **Utwórz**. |
| Oświadczenie outputclaim | hash | string | Wartość oświadczenia, która jest generowana po wywołaniu tej transformacji oświadczeń. W `plaintext` oświadczenie inputclaim. |

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
  - **zwykły tekst**: MyPass@word1
  - **sól**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Oświadczenia wyjściowe:
  - **oświadczenie outputclaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U =
