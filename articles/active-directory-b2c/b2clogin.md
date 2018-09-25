---
title: Korzystanie z usługi b2clogin.com | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat korzystania z usługi b2clogin.com zamiast login.microsoftonline.com.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/29/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c24582fce44006d9a3972d73078aa8cb0d212c11
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47053848"
---
# <a name="using-b2clogincom"></a>Korzystanie z usługi B2Clogin.com

Idąc dalej, firma Microsoft jest zachęcanie do wszystkich klientów, aby użyć `<YourDirectoryName>.b2clogin.com` i wycofamy `login.microsoftonline.com`. Z usługi B2Clogin.com zapewnia dodatkowe korzyści takie jak:
* Nie udostępniasz ten sam plik cookie w innych usługach firmy Microsoft.
* Możesz usunąć wszystkie odwołania do firmy Microsoft w adresie URL (można zastąpić `<YourDirectoryName>.onmicrosoft.com` za pomocą Identyfikatora katalogu). Na przykład: `https://<YourDirectoryName>.b2clogin.com/tfp/<YourDirectoryID>/<policyname>/v2.0/.well-known/openid-configuration`.

Oto co należy zrobić, aby migracji na stronie b2clogin.com

* Zmienić identyfikatory URI przekierowania dla aplikacji dostawcy tożsamości dla sieci społecznościowej
* Edytowanie aplikacji na korzystanie z usługi B2Clogin.com zamiast `login.microsoftonline.com` dla odwołania do zasad i tokenu punktów końcowych.
* Jeśli używasz biblioteki MSAL, musisz ustawić `ValidateAuthority=false`.  

##<a name="redirect-uris-for-social-identity-providers"></a>Identyfikatory URI przekierowania dla dostawców tożsamości dla sieci społecznościowej

Jeśli masz konta społecznościowego dostawcy tożsamości w katalogu należy wprowadzać modyfikacje w swoich aplikacjach.  Brak parametru stosowania dla każdego dostawcy społecznościowych, który zawiera listę zaufanych adresów URL do przekierowania usługi Azure AD B2C.  Obecnie, prawdopodobnie jest skonfigurowany do Przekieruj z powrotem do niektórych `login.microsoftonline.com` witryny, należy zmienić ten adres URL, aby `YourDirectoryName.b2clogin.com` będzie autoryzowany identyfikator URI przekierowania.  Upewnij się usunąć `/te` także.  Ma niewielkie zmiany do tego adresu URL, dla każdego dostawcy tożsamości, więc zajrzyj odpowiadającą mu stronę, aby uzyskać dokładny adres URL.  

| Dostawca tożsamości |
|-------------------|
|[Konto Microsoft](active-directory-b2c-setup-msa-app.md)|
|[Facebook](active-directory-b2c-setup-fb-app.md)|
|[Google](active-directory-b2c-setup-goog-app.md)|
|[Amazon](active-directory-b2c-setup-amzn-app.md)|
|[LinkedIn](active-directory-b2c-setup-li-app.md)|
|[Twitter](active-directory-b2c-setup-twitter-app.md)|
|[GitHub](active-directory-b2c-setup-github-app.md)|
|[Weibo](active-directory-b2c-setup-weibo-app.md)|
|[QQ](active-directory-b2c-setup-qq-app.md)|
|[WeChat](active-directory-b2c-setup-wechat-app.md)|
|[Azure AD](active-directory-b2c-setup-oidc-azure-active-directory.md)|
|[Niestandardowe OIDC](active-directory-b2c-setup-oidc-idp.md)|

##<a name="update-your-application-references"></a>Aktualizuj odwołania do swojej aplikacji

Aplikacja prawdopodobnie odwołuje się do `login.microsoftonline.com` w kilku miejscach, takie jak odwołania do zasad i tokenu punktów końcowych.  Upewnij się, że usługi punktu końcowego autoryzacji punktu końcowego tokenu i Wystawca zostały zaktualizowane.  

##<a name="set-validateauthorityfalse-in-msal"></a>Ustaw `ValidateAuthority=false` w biblioteki MSAL

Jeśli używasz biblioteki MSAL, musisz ustawić `ValidateAuthority=false`.  Aby uzyskać więcej informacji, zobacz [tej dokumentacji](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase?view=azure-dotnet).