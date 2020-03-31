---
title: Przewodnik po migracji języka ADAL języka Python do MSAL | Azure
description: Dowiedz się, jak przeprowadzić migrację aplikacji Python z biblioteki uwierzytelniania usługi Azure Active Directory (ADAL) do biblioteki uwierzytelniania Microsoft (MSAL) dla języka Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696563"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Przewodnik po migracji ADAL do MSAL dla języka Python

W tym artykule przedstawiono zmiany, które należy wprowadzić w celu migracji aplikacji korzystającej z biblioteki uwierzytelniania usługi Azure Active Directory (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft (MSAL).

## <a name="difference-highlights"></a>Wyróżnienia różnic

Usługa ADAL współpracuje z punktem końcowym usługi Azure Active Directory (Azure AD) w wersji 1.0. Biblioteka uwierzytelniania firmy Microsoft (MSAL) współpracuje z platformą tożsamości firmy Microsoft — wcześniej znaną jako punkt końcowy usługi Azure Active Directory w wersji 2.0. Platforma tożsamości firmy Microsoft różni się od usługi Azure AD 1.0 tym, że:

Obsługuje:
  - Konta służbowe i szkolne (konta aprowizacji usługi Azure AD)
  - Konta osobiste (takie jak Outlook.com lub Hotmail.com)
  - Twoi klienci, którzy przynoszą własną pocztę e-mail lub tożsamość społecznościową (na przykład LinkedIn, Facebook, Google) za pośrednictwem oferty Azure AD B2C

- Czy normy są zgodne z:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Zobacz [Co się różni w punkcie końcowym platformy tożsamości firmy Microsoft (wersja 2.0) aby](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) uzyskać więcej informacji.

### <a name="scopes-not-resources"></a>Zakresy nie zasoby

ADAL Python nabywa tokeny dla zasobów, ale MSAL Python nabywa tokeny dla zakresów. Powierzchnia interfejsu API w języku MSAL Python nie ma już parametru zasobu. Należy podać zakresy jako listę ciągów, które deklarują żądane uprawnienia i zasoby, które są wymagane. Aby zobaczyć przykład zakresów, zobacz [zakresy programu Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Obsługa błędów

Biblioteka uwierzytelniania usługi Azure Active Directory (ADAL) dla języka Python używa wyjątku, `AdalError` aby wskazać, że wystąpił problem. MSAL dla języka Python zazwyczaj używa kodów błędów, zamiast tego. Aby uzyskać więcej informacji, zobacz [MSAL dla obsługi błędów języka Python](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Zmiany api

Poniższa tabela zawiera listę interfejsu API w języku ADAL dla języka Python i interfejsu, który ma być używany w jego miejscu w języku MSAL dla języka Python:

| Interfejs API usługi ADAL dla języka Python  | Interfejs API msal dla języka Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext (Tekst uwierzytelniania)](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication lub ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Nie dotyczy  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Nie dotyczy |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) i [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) i [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Nie dotyczy | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Nie dotyczy | Pamięć podręczna z trwałością dostępna w [rozszerzeniach MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrowanie istniejących tokenów odświeżania dla usługi MSAL Python

Biblioteka uwierzytelniania firmy Microsoft (MSAL) streszczy koncepcję tokenów odświeżania. MSAL Python domyślnie udostępnia pamięć podręczną tokenów w pamięci, dzięki czemu nie trzeba przechowywać, odnoślić ani aktualizować tokenów odświeżania. Użytkownicy zobaczą również mniej monitów logowania, ponieważ tokeny odświeżania można zwykle aktualizować bez interwencji użytkownika. Aby uzyskać więcej informacji na temat pamięci podręcznej tokenów, zobacz [Serializacja niestandardowej pamięci podręcznej tokenów w biuletynie MSAL dla języka Python.](msal-python-token-cache-serialization.md)

Poniższy kod pomoże Ci przeprowadzić migrację tokenów odświeżania zarządzanych przez inną bibliotekę OAuth2 (w tym między innymi ADAL Python), które mają być zarządzane przez program MSAL dla języka Python. Jednym z powodów migracji tych tokenów odświeżania jest uniemożliwienie istniejącym użytkownikom ponownego zalogowania się podczas migracji aplikacji do usługi MSAL dla języka Python.

Metoda migracji tokenu odświeżania jest użycie MSAL dla języka Python, aby uzyskać nowy token dostępu przy użyciu poprzedniego tokenu odświeżania. Po powrocie nowego tokenu odświeżania usługa MSAL dla języka Python będzie przechowywać go w pamięci podręcznej. Oto przykład, jak to zrobić:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z [porównaniem wersji 1.0 i v2.0](active-directory-v2-compare.md).
