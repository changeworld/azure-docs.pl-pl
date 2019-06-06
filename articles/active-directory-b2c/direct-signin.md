---
title: Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wstępnie wypełnić nazwą logowania lub przekierowywanie sterowania bezpośrednio do dostawcy tożsamości społecznościowych.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 869097ac3b91e55d5dbf948680450f31efafd359
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511108"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Konfigurowanie bezpośredniego logowania przy użyciu usługi Azure Active Directory B2C

Podczas konfigurowania logowania dla aplikacji za pomocą usługi Azure Active Directory (AD) B2C, możesz wstępnie wypełnić nazwą logowania lub bezpośrednie logowanie do określonych społecznościowego dostawcy tożsamości, takich jak Facebook, LinkedIn lub konta Microsoft. 

## <a name="prepopulate-the-sign-in-name"></a>Wstępne wprowadzanie nazwy logowania

Podczas podróży logowania użytkownika aplikację jednostki uzależnionej mogą odnosić się do określonej nazwy użytkownika lub domenę. Gdy użytkownik, aplikacja może określić, w żądaniu autoryzacji `login_hint` parametr z nazwą logowania użytkownika zapytania. Usługa Azure AD B2C spowoduje automatyczne wypełnienie nazwy logowania podczas, gdy użytkownik musi tylko podać hasło.

![przy użyciu wskazówki logowania](./media/direct-signin/login-hint.png) 

Użytkownik będzie mógł zmienić wartość w polu tekstowym logowania.

Jeśli używane są zasady niestandardowe, należy zastąpić `SelfAsserted-LocalAccountSignin-Email` profilu technicznego. W `<InputClaims>` sekcji, ustaw właściwość DefaultValue oświadczenia signInName do `{OIDC:LoginHint}`. `{OIDC:LoginHint}` Zmienna zawiera wartość `login_hint` parametru. Usługa Azure AD B2C odczytuje wartość oświadczenia signInName i wstępnie wypełnia pole tekstowe signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Przekieruj zalogować się do mediów społecznościowych

Jeśli skonfigurowano podróż logowania dla swojej aplikacji uwzględnić kont społecznościowych, takich jak Facebook, LinkedIn i Google, możesz określić `domain_hint` parametru. Ten parametr zapytania stanowi wskazówkę do usługi Azure AD B2C o dostawcy tożsamości społecznościowych, które mają być używane do logowania. Na przykład, jeśli aplikacja Określa `domain_hint=facebook.com`, zaloguj się przechodzi bezpośrednio do strony logowania serwisu Facebook.

![za pomocą wskazówkę dotyczącą domeny](./media/direct-signin/domain-hint.png) 

Jeśli używasz zasad niestandardowych, można skonfigurować przy użyciu nazwy domeny `<Domain>domain name</Domain>` — element XML dowolnego `<ClaimsProvider>`. 

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


