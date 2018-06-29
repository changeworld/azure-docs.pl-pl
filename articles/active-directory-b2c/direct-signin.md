---
title: Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wstępnie nazwą logowania lub Przekieruj bezpośrednio do dostawcy tożsamości społecznościowych.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/18/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: cc3baa8fe4139acec94a722bf8c2bccb708a9470
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102530"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C

Podczas konfigurowania logowania dla aplikacji za pomocą usługi Azure Active Directory (AD) B2C, można wstępnie wypełnić nazwą logowania lub bezpośredniego logowania do dostawcy określonej tożsamości społecznościowych, takich jak Facebook, LinkedIn lub konta Microsoft. 

## <a name="prepopulate-the-sign-in-name"></a>Wstępnie nazwy logowania

Podczas podróży logowania użytkownika jednostki uzależnionej aplikacji firm mogą odnosić się do określonej nazwy użytkownika lub domeny. Gdy użytkownik, aplikacja może określić, w żądaniu autoryzacji `login_hint` parametr z nazwą logowania użytkownika zapytania. Azure AD B2C automatycznie wypełni nazwy logowania, gdy użytkownik musi tylko podać hasło.

![przy użyciu wskazówki logowania](./media/direct-signin/login-hint.png) 

Użytkownik będzie mógł Zmień wartość w polu tekstowym logowania.

Jeśli używane są zasady niestandardowe, należy zastąpić `SelfAsserted-LocalAccountSignin-Email` techniczne profilu. W `<InputClaims>` sekcji, ustaw właściwość DefaultValue oświadczenia signInName na `{OIDC:LoginHint}`. `{OIDC:LoginHint}` Zmienna uwzględnia wartość `login_hint` parametru. Usługa Azure AD B2C odczytuje wartości oświadczenia signInName i wstępnie wypełnia pole tekstowe signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Logowanie przekierowania do dostawcy społecznościowych

Jeśli skonfigurowano przebieg logowania dla aplikacji do uwzględnienia kont społecznościowych, takich jak Facebook, LinkedIn lub Google, można określić `domain_hint` parametru. Ten parametr zapytania zawiera wskazówkę do usługi Azure AD B2C o dostawcy tożsamości społecznościowych, które mają być używane do logowania. Na przykład, jeśli aplikacja Określa `domain_hint=facebook.com`, logowania trafia bezpośrednio do strony logowania usługi Facebook.

![przy użyciu wskazówki domeny](./media/direct-signin/domain-hint.png) 

Jeśli używasz zasad niestandardowych, można skonfigurować przy użyciu nazwy domeny `<Domain>domain name</Domain>` — element XML dowolnego `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


