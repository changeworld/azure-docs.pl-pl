---
title: Migrowanie aplikacji i interfejsów API w celu b2clogin.com
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o używaniu b2clogin.com w adresach URL przekierowania dla usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 64b440054795670b99a22e37dec7188f3e1cd74c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189994"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ustawianie adresów URL przekierowania do b2clogin.com dla usługi Azure Active Directory B2C

Podczas konfigurowania dostawcy tożsamości do rejestracji i logowania się w aplikacji B2C usługi Azure Active Directory (Azure AD B2C) należy określić adres URL przekierowania. Nie należy już odwoływać się *do login.microsoftonline.com* w aplikacjach i interfejsach API. Zamiast tego należy użyć *b2clogin.com* dla wszystkich nowych aplikacji i migrować istniejące aplikacje z *login.microsoftonline.com* do *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Wycofanie login.microsoftonline.com

4 grudnia 2019 r. ogłosiliśmy planową wycofanie pomocy login.microsoftonline.com na platformie Azure AD B2C w dniu **04 grudnia 2020 r.:**

[Usługa Azure Active Directory B2C przestarzałe login.microsoftonline.com](https://azure.microsoft.com/updates/b2c-deprecate-msol/)

Wycofanie login.microsoftonline.com wchodzi w życie dla wszystkich dzierżaw usługi Azure AD B2C w dniu 04 grudnia 2020 r., zapewniając istniejącym dzierżawcom jeden (1) rok migracji do b2clogin.com. Nowi najemcy powołani po 04 grudnia 2019 r. nie będą przyjmować wniosków od login.microsoftonline.com. Wszystkie funkcje pozostają takie same w b2clogin.com punktu końcowego.

Wycofanie login.microsoftonline.com nie ma wpływu na dzierżawy usługi Azure Active Directory. Ta zmiana dotyczy tylko dzierżaw usługi Azure Active Directory B2C.

## <a name="benefits-of-b2clogincom"></a>Korzyści z b2clogin.com

Gdy używasz *b2clogin.com* jako adresu URL przekierowania:

* Ilość miejsca zużywanego w nagłówku plików cookie przez usługi firmy Microsoft jest ograniczona.
* Adresy URL przekierowania nie muszą już zawierać odwołania do firmy Microsoft.
* Kod po stronie klienta JavaScript jest obsługiwany (obecnie w [wersji zapoznawczej)](user-flow-javascript-overview.md)na niestandardowych stronach. Ze względu na ograniczenia bezpieczeństwa elementy kodu JavaScript i formularza HTML są usuwane ze stron niestandardowych, jeśli używasz *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Przegląd wymaganych zmian

Istnieje kilka modyfikacji, które mogą być konieczne do migracji aplikacji do *b2clogin.com:*

* Zmień adres URL przekierowania w aplikacjach dostawcy tożsamości, aby odwoływać się do *b2clogin.com*.
* Zaktualizuj aplikacje usługi Azure AD B2C, aby *używały b2clogin.com* w ich odwołaniach do przepływu użytkownika i tokenu końcowego.
* Zaktualizuj wszystkie **dozwolone źródła,** które zostały zdefiniowane w ustawieniach CORS do [dostosowywania interfejsu użytkownika](custom-policy-ui-customization.md).

## <a name="change-identity-provider-redirect-urls"></a>Zmienianie adresów URL przekierowania adresów URL dostawcy tożsamości

W witrynie sieci Web każdego dostawcy tożsamości, w której utworzono `your-tenant-name.b2clogin.com` aplikację, zmień wszystkie zaufane adresy URL, aby przekierować do zamiast *login.microsoftonline.com*.

Istnieją dwa formaty, których można użyć w b2clogin.com przekierowania adresów URL. Pierwszy zapewnia korzyści z braku "Microsoft" pojawiają się w dowolnym miejscu w adresie URL przy użyciu identyfikatora dzierżawy (identyfikator GUID) zamiast nazwy domeny dzierżawy:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Druga opcja używa nazwy domeny dzierżawy `your-tenant-name.onmicrosoft.com`w postaci . Przykład:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Dla obu formatów:

* Zamień `{your-tenant-name}` na nazwę dzierżawy usługi Azure AD B2C.
* Usuń, `/te` jeśli istnieje w adresie URL.

## <a name="update-your-applications-and-apis"></a>Aktualizowanie aplikacji i interfejsów API

Kod w aplikacjach i interfejsach API obsługujących usługę `login.microsoftonline.com` Azure AD B2C może odnosić się w kilku miejscach. Na przykład kod może mieć odwołania do przepływów użytkownika i punktów końcowych tokenu. Zaktualizuj następujące `your-tenant-name.b2clogin.com`odwołanie, aby zamiast tego odwołać:

* Punkt końcowy autoryzacji
* Punkt końcowy tokenu
* Wystawca tokenów

Na przykład punkt końcowy urzędu dla zasad rejestracji/logowania firmy Contoso będzie teraz:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Aby uzyskać informacje dotyczące migracji aplikacji sieci Web opartych na owiwanie do b2clogin.com, zobacz [Migrowanie internetowego interfejsu API opartego na usłudze OWIN w celu b2clogin.com](multiple-token-endpoints.md).

Aby przeprowadzić migrację interfejsów API usługi Azure API Management chronionych przez usługę Azure AD B2C, zobacz [sekcję Migruj do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) [interfejsu API secure interfejsu API usługi Azure za pomocą usługi Azure AD B2C.](secure-api-management.md)

## <a name="microsoft-authentication-library-msal"></a>Biblioteka uwierzytelniania firmy Microsoft (MSAL)

### <a name="validateauthority-property"></a>Właściwość ValidateAuthority

Jeśli używasz [MSAL.NET][msal-dotnet] wersji 2 lub wcześniejszej, ustaw właściwość `false` **ValidateAuthority** na wystąpienie klienta, aby umożliwić przekierowania do *b2clogin.com*. To ustawienie nie jest wymagane dla MSAL.NET v3 i powyżej.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Jeśli używasz [MSAL dla JavaScript:][msal-js]

```JavaScript
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false
  }
);
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje dotyczące migracji aplikacji sieci Web opartych na owiwanie do b2clogin.com, zobacz [Migrowanie internetowego interfejsu API opartego na usłudze OWIN w celu b2clogin.com](multiple-token-endpoints.md).

Aby przeprowadzić migrację interfejsów API usługi Azure API Management chronionych przez usługę Azure AD B2C, zobacz [sekcję Migruj do b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) [interfejsu API secure interfejsu API usługi Azure za pomocą usługi Azure AD B2C.](secure-api-management.md)

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
