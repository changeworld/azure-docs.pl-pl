---
title: Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się, jak wstępnie wypełnić nazwę logowania lub przekierować bezpośrednio do dostawcy tożsamości społecznościowej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9a02ad3ea43ae9d91489417bc314e3c23d54a958
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188770"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C

Podczas konfigurowania logowania dla aplikacji przy użyciu usługi Azure Active Directory (AD) B2C, można wstępnie wypełnić nazwę logowania lub bezpośrednie logowanie do określonego dostawcy tożsamości społecznej, takich jak Facebook, LinkedIn lub konto Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Wstępne wypełnianie nazwy logowania

Podczas podróży użytkownika logowania aplikacja jednostki uzależniającej może kierować się na określonego użytkownika lub nazwę domeny. Podczas kierowania na użytkownika aplikacja może określić w `login_hint` żądaniu autoryzacji parametr kwerendy o nazwie logowania użytkownika. Usługa Azure AD B2C automatycznie wypełnia nazwę logowania, podczas gdy użytkownik musi tylko podać hasło.

![Zarejestruj się strona logowania za pomocą login_hint param zapytania wyróżniony w adresie URL](./media/direct-signin/login-hint.png)

Użytkownik może zmienić wartość w polu tekstowym logowania.

Jeśli używasz zasad niestandardowych, należy `SelfAsserted-LocalAccountSignin-Email` zastąpić profil techniczny. W `<InputClaims>` sekcji ustaw defaultvalue oświadczenia signInName `{OIDC:LoginHint}`do . Zmienna `{OIDC:LoginHint}` zawiera wartość `login_hint` parametru. Usługa Azure AD B2C odczytuje wartość oświadczenia signInName i wstępnie wypełnia pole tekstowe signInName.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="redirect-sign-in-to-a-social-provider"></a>Przekierowywanie logowania do dostawcy usług społecznościowych

Jeśli skonfigurowano podróż logowania do aplikacji tak, aby obejmowała konta społecznościowe, takie jak Facebook, `domain_hint` LinkedIn lub Google, możesz określić ten parametr. Ten parametr kwerendy zawiera wskazówkę dla usługi Azure AD B2C o dostawcy tożsamości społecznościowej, który powinien być używany do logowania. Jeśli na przykład aplikacja `domain_hint=facebook.com`określi , logowanie przechodzi bezpośrednio na stronę logowania na Facebooku.

![Zarejestruj się strona logowania z domain_hint param zapytania wyróżniona w adresie URL](./media/direct-signin/domain-hint.png)

Jeśli używasz zasad niestandardowych, możesz skonfigurować nazwę `<Domain>domain name</Domain>` domeny przy `<ClaimsProvider>`użyciu elementu XML dowolnego .

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


