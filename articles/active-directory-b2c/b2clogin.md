---
title: Ustawianie adresów URL przekierowania na b2clogin.com — Azure Active Directory B2C
description: Dowiedz się więcej na temat używania b2clogin.com w adresach URL przekierowania dla Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: dbc366daac89f44d4b084081590124f81ff9cc9c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533748"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Ustaw adresy URL przekierowania na b2clogin.com dla Azure Active Directory B2C

Po skonfigurowaniu dostawcy tożsamości na potrzeby rejestracji i logowania w aplikacji Azure Active Directory B2C (Azure AD B2C) musisz określić adres URL przekierowania. Nie należy już odwoływać się do *login.microsoftonline.com* w aplikacjach i interfejsach API. Zamiast tego należy użyć *b2clogin.com* dla wszystkich nowych aplikacji i zmigrować istniejące aplikacje z *login.microsoftonline.com* do *b2clogin.com*.

## <a name="benefits-of-b2clogincom"></a>Zalety b2clogin.com

Jeśli używasz *b2clogin.com* jako adresu URL przekierowania:

* Zajęte miejsce w nagłówku pliku cookie przez usługi firmy Microsoft jest ograniczone.
* Adresy URL przekierowań nie muszą już zawierać odwołania do firmy Microsoft.
* Kod po stronie klienta JavaScript jest obsługiwany (obecnie w [wersji](user-flow-javascript-overview.md)zapoznawczej) na niestandardowych stronach. Ze względu na ograniczenia zabezpieczeń, kod JavaScript i elementy formularza HTML są usuwane z niestandardowych stron, jeśli używasz *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Przegląd wymaganych zmian

Istnieje kilka modyfikacji, które trzeba wykonać, aby przeprowadzić migrację aplikacji do *b2clogin.com*:

* Zmień adres URL przekierowania w aplikacjach dostawcy tożsamości, aby odwoływała się do *b2clogin.com*.
* Zaktualizuj aplikacje Azure AD B2C, aby używać *b2clogin.com* w ich przepływie użytkownika i odwołaniach do punktów końcowych tokenu.
* Zaktualizuj wszystkie **dozwolone źródła** zdefiniowane w ustawieniach mechanizmu CORS do [dostosowywania interfejsu użytkownika](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="change-identity-provider-redirect-urls"></a>Zmienianie adresów URL przekierowań dostawcy tożsamości

W każdej witrynie sieci Web dostawcy tożsamości, w której została utworzona aplikacja, Zmień wszystkie Zaufane adresy URL, aby `your-tenant-name.b2clogin.com` przekierować do *login.microsoftonline.com*.

Istnieją dwa formaty, których można użyć w odniesieniu do adresów URL przekierowania b2clogin.com. Pierwsza z nich to korzyść, która nie ma "firmy Microsoft" pojawia się w dowolnym miejscu w adresie URL przy użyciu identyfikatora dzierżawy (GUID) zamiast nazwy domeny dzierżawcy:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Druga opcja używa nazwy domeny dzierżawy w postaci `your-tenant-name.onmicrosoft.com`. Na przykład:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Dla obu formatów:

* Zastąp `{your-tenant-name}` nazwą dzierżawy usługi Azure AD B2C.
* Usuń `/te` , jeśli istnieje w adresie URL.

## <a name="update-your-applications-and-apis"></a>Aktualizowanie aplikacji i interfejsów API

Kod w aplikacjach i interfejsach API z obsługą Azure AD B2C mogą odwoływać się do `login.microsoftonline.com` programu w kilku miejscach. Na przykład kod może zawierać odwołania do przepływów użytkowników i punktów końcowych tokenu. W zamian należy zaktualizować następujące informacje `your-tenant-name.b2clogin.com`:

* Punkt końcowy autoryzacji
* Punkt końcowy tokenu
* Wystawca tokenu

Na przykład punkt końcowy urzędu certyfikacji dla zasad rejestracji/logowania w firmie Contoso będzie teraz:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

## <a name="microsoft-authentication-library-msal"></a>Biblioteka uwierzytelniania firmy Microsoft (MSAL)

### <a name="validateauthority-property"></a>Właściwość ValidateAuthority

Jeśli używasz programu [MSAL.NET][msal-dotnet] v2 lub starszego, ustaw właściwość `false` ValidateAuthority na tworzenie wystąpienia klienta, aby umożliwić przekierowywanie do *b2clogin.com*. To ustawienie nie jest wymagane w przypadku wersji MSAL.NET v3 i nowszych.

```CSharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

Jeśli używasz [MSAL na potrzeby języka JavaScript][msal-js]:

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

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md