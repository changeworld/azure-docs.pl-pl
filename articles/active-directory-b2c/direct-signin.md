---
title: Skonfiguruj bezpośrednie Logowanie przy użyciu Azure Active Directory B2C | Microsoft Docs
description: Informacje na temat wstępnego wypełniania nazwy logowania lub przekierowania bezpośrednio do dostawcy tożsamości społecznościowej.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188770"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Skonfiguruj bezpośrednie Logowanie przy użyciu Azure Active Directory B2C

Podczas konfigurowania logowania do aplikacji przy użyciu Azure Active Directory (AD) B2C można wstępnie wypełnić nazwę logowania lub bezpośrednio zalogować się do określonego dostawcy tożsamości społecznościowej, takiego jak Facebook, LinkedIn lub konto Microsoft.

## <a name="prepopulate-the-sign-in-name"></a>Wstępne wypełnianie nazwy logowania

Podczas podróży użytkowników w ramach logowania aplikacja jednostki uzależnionej może kierować do określonego użytkownika lub nazwy domeny. Podczas określania wartości docelowej użytkownik może określić, w żądaniu autoryzacji, `login_hint` parametr zapytania z nazwą logowania użytkownika. Azure AD B2C automatycznie wypełnia nazwę logowania, a użytkownik musi podać hasło.

![Zarejestruj się na stronie logowania przy użyciu parametru zapytania login_hint wyróżnionego w adresie URL](./media/direct-signin/login-hint.png)

Użytkownik może zmienić wartość w polu tekstowym logowania.

Jeśli używasz zasad niestandardowych, Zastąp profil techniczny `SelfAsserted-LocalAccountSignin-Email`. W sekcji `<InputClaims>` ustaw wartość DefaultValue dla żądania signInName na `{OIDC:LoginHint}`. Zmienna `{OIDC:LoginHint}` zawiera wartość parametru `login_hint`. Azure AD B2C odczytuje wartość żądania signInName i wstępnie wypełnia pole TextBox signInName.

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

## <a name="redirect-sign-in-to-a-social-provider"></a>Przekieruj logowanie do dostawcy społeczności

Jeśli skonfigurowano podróż do logowania dla aplikacji w celu uwzględnienia kont społecznościowych, takich jak Facebook, LinkedIn lub Google, można określić parametr `domain_hint`. Ten parametr zapytania zawiera wskazówkę dotyczącą Azure AD B2C dostawcy tożsamości społecznościowej, który ma być używany do logowania. Na przykład jeśli aplikacja określi `domain_hint=facebook.com`, logowanie prowadzi bezpośrednio do strony logowania w serwisie Facebook.

![Zarejestruj się na stronie logowania przy użyciu parametru zapytania domain_hint wyróżnionego w adresie URL](./media/direct-signin/domain-hint.png)

Jeśli używasz zasad niestandardowych, możesz skonfigurować nazwę domeny za pomocą `<Domain>domain name</Domain>` elementu XML dowolnego `<ClaimsProvider>`.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```


