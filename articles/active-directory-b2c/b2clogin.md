---
title: Zestaw przekierowania adresów URL z usługi b2clogin.com dla usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z usługi b2clogin.com w Twojego przekierowania adresów URL dla usługi Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 8e06cf1a443d4fd158e29ef4b53206a83800dfe9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803056"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Zestaw przekierowania adresów URL z usługi b2clogin.com dla usługi Azure Active Directory B2C

Po skonfigurowaniu dostawcy tożsamości dla rejestracji i logowania w aplikacji Azure Active Directory (Azure AD) B2C, musisz określić adres URL przekierowania. W przeszłości login.microsoftonline.com został użyty po stronie b2clogin.com powinien być używany.

Korzystanie z usługi b2clogin.com zapewnia dodatkowe korzyści, takich jak:

- Pliki cookie nie są już są współużytkowane z innymi usługami firmy Microsoft.
- Adresami URL nie będzie zawierać odniesienie do firmy Microsoft. Na przykład `https://your-tenant-name.b2clogin.com/tfp/your-tenant-ID/policyname/v2.0/.well-known/openid-configuration`.

Aby korzystać z usługi b2clogin.com, ustaw przekierowania adresów URL w aplikacji dostawcy tożsamości z usługi b2clogin.com. Można również ustawić aplikacji usługi Azure AD B2C na korzystanie z usługi b2clogin.com dla odwołania do zasad i tokenu punktów końcowych. Jeśli używasz biblioteki MSAL, musisz ustawić **ValidateAuthority** właściwość `false`.

## <a name="change-redirect-urls"></a>Zmiana przekierowania adresów URL

Aby korzystać z usługi b2clogin.com, w ustawieniach aplikacji dostawcy tożsamości, Znajdź i zmienić listę zaufanych adresów URL do przekierowania usługi Azure AD B2C.  Obecnie możesz prawdopodobnie jest skonfigurowany do Przekieruj z powrotem do lokacji niektóre login.microsoftonline.com. 

Musisz zmienić adres URL przekierowania tak, aby `your-tenant-name.b2clogin.com` jest autoryzowany. Upewnij się zastąpić `your-tenant-name` o nazwie usługi Azure AD B2C dzierżawy i Usuń `/te` istnienia w adresie URL. Ma niewielkie zmiany do tego adresu URL, dla każdego dostawcy tożsamości, więc zajrzyj odpowiadającą mu stronę, aby uzyskać dokładny adres URL.

Informacje o konfiguracji dla dostawcy tożsamości można znaleźć w następujących artykułach:

- [Konto Microsoft](active-directory-b2c-setup-msa-app.md)
- [Facebook](active-directory-b2c-setup-fb-app.md)
- [Google](active-directory-b2c-setup-goog-app.md)
- [Amazon](active-directory-b2c-setup-amzn-app.md)
- [LinkedIn](active-directory-b2c-setup-li-app.md)
- [Twitter](active-directory-b2c-setup-twitter-app.md)
- [GitHub](active-directory-b2c-setup-github-app.md)
- [Weibo](active-directory-b2c-setup-weibo-app.md)
- [QQ](active-directory-b2c-setup-qq-app.md)
- [WeChat](active-directory-b2c-setup-wechat-app.md)
- [Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)
- [Niestandardowe OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Aktualizowanie aplikacji

Aplikacja usługi Azure AD B2C, ale prawdopodobnie dotyczy `login.microsoftonline.com` w kilku miejscach, takie jak odwołania do zasad i tokenu punktów końcowych.  Upewnij się, że Twoje punkt końcowy autoryzacji punktu końcowego tokenu i Wystawca zostały zaktualizowane na potrzeby `your-tenant-name.b2clogin.com`.  

## <a name="set-the-validateauthority-property"></a>Ustaw właściwość ValidateAuthority

Jeśli używasz biblioteki MSAL **ValidateAuthority** do `false`. Poniższy przykład pokazuje, jak można ustawić właściwości:

```
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

 Aby uzyskać więcej informacji, zobacz [klasy ClientApplicationBase ](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).