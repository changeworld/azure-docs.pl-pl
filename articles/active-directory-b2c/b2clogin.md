---
title: Ustawianie adresów URL przekierowania na b2clogin.com-Azure Active Directory B2C | Microsoft Docs
description: Dowiedz się więcej na temat używania b2clogin.com w adresach URL przekierowania dla Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 080c1933f88d9e824969a42212de2eacd0f62e14
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927280"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ustaw adresy URL przekierowania na b2clogin.com dla Azure Active Directory B2C

Po skonfigurowaniu dostawcy tożsamości na potrzeby rejestracji i logowania Azure Active Directory w aplikacji B2C (Azure AD), należy określić adres URL przekierowania. W przeszłości użyto login.microsoftonline.com, teraz należy używać b2clogin.com.

> [!NOTE]
> Możesz użyć kodu po stronie klienta JavaScript (obecnie w wersji zapoznawczej) w b2clogin.com. Kod JavaScript zostanie usunięty ze strony niestandardowej, jeśli używasz login.microsoftonline.com. Dodatkowe ograniczenia zabezpieczeń są również stosowane do login.microsoftonline.com, takich jak usuwanie elementów formularza HTML ze strony niestandardowej. 

Korzystanie z usługi b2clogin.com daje dodatkowe korzyści, takie jak:

- Zajęte miejsce w nagłówku pliku cookie przez usługi firmy Microsoft jest ograniczone.
- Adresy URL nie zawierają już odwołania do firmy Microsoft. Na przykład `https://your-tenant-name.b2clogin.com/tenant-id/oauth2/authresp`.

> [!NOTE]
> Można użyć zarówno nazwy dzierżawy, jak i identyfikatora GUID dzierżawy w następujący sposób:
> * `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`(nadal odwołuje się `onmicrosoft.com`do)
> * `https://your-tenant-name.b2clogin.com/your-tenant-guid`(w tym przypadku w ogóle nie ma odwołań do firmy Microsoft)
>
> Nie można jednak używać _domeny niestandardowej_ dla dzierżawy Azure Active Directory B2C, na przykład `https://your-tenant-name.b2clogin.com/your-custom-domain-name` _nie będzie ona_ zadziałała.

Należy wziąć pod uwagę te ustawienia, które mogą wymagać zmiany w przypadku korzystania z b2clogin.com:

- Ustaw adresy URL przekierowania w aplikacjach dostawcy tożsamości, aby używać b2clogin.com. 
- Ustaw dla aplikacji Azure AD B2C użycie b2clogin.com do odwoływania się do przepływu użytkownika i punktów końcowych tokenów. 
- Jeśli używasz MSAL, musisz ustawić właściwość **ValidateAuthority** na `false`.
- Upewnij się, że zmienisz dowolne **dozwolone źródła** zdefiniowane w ustawieniach mechanizmu CORS do [dostosowywania interfejsu użytkownika](active-directory-b2c-ui-customization-custom-dynamic.md).  

## <a name="change-redirect-urls"></a>Zmienianie adresów URL przekierowania

Aby użyć b2clogin.com, w ustawieniach aplikacji dostawcy tożsamości Wyszukaj i zmień listę zaufanych adresów URL w celu przekierowania z powrotem do Azure AD B2C.  Obecnie jest to prawdopodobnie skonfigurowane w celu przekierowania z powrotem do pewnej lokacji login.microsoftonline.com. 

Należy zmienić adres URL przekierowania tak, `your-tenant-name.b2clogin.com` aby był autoryzowany. Pamiętaj o zamianie `your-tenant-name` na nazwę dzierżawy Azure AD B2C i Usuń `/te` ją, jeśli istnieje w adresie URL. W przypadku każdego dostawcy tożsamości istnieją nieznaczne zmiany w tym adresie URL, dlatego należy sprawdzić odpowiednią stronę, aby uzyskać dokładny adres URL.

Informacje dotyczące konfigurowania dostawców tożsamości można znaleźć w następujących artykułach:

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
- [Niestandardowy OIDC](active-directory-b2c-setup-oidc-idp.md)

## <a name="update-your-application"></a>Aktualizowanie aplikacji

Aplikacja Azure AD B2C prawdopodobnie odwołuje się `login.microsoftonline.com` do programu w kilku miejscach, takich jak odwołania do przepływu użytkownika i punkty końcowe tokenu.  Upewnij się, że punkt końcowy autoryzacji, punkt końcowy tokenu i wystawca zostały zaktualizowane do `your-tenant-name.b2clogin.com`użycia.  

## <a name="set-the-validateauthority-property"></a>Ustaw właściwość ValidateAuthority

Jeśli używasz MSAL, ustaw właściwość **ValidateAuthority** na `false`. Gdy **ValidateAuthority** jest ustawiona na `false`, przekierowania są dozwolone do b2clogin.com. 

Poniższy przykład pokazuje, jak można ustawić właściwość:

W programie [MSAL for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet):

```CSharp
 ConfidentialClientApplication client = new ConfidentialClientApplication(...); // can also be PublicClientApplication
 client.ValidateAuthority = false;
```

I w [MSAL dla języka JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js):

```Javascript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```
