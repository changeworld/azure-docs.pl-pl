---
title: Przewodnik migracji biblioteki ADAL do MSAL dla języka Python | Azure
description: Dowiedz się, jak przeprowadzić migrację aplikacji w języku Python biblioteki Azure Active Directory Authentication Library (ADAL) do biblioteki uwierzytelniania firmy Microsoft (MSAL) dla języka Python.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72a4af690a1433f6a185ab17f06748d67927edea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917237"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Przewodnik migracji biblioteki ADAL do MSAL dla języka Python

W tym artykule przedstawiono zmiany, które należy wykonać w celu przeprowadzenia migracji aplikacji, która Azure Active Directory używa biblioteki MSAL Authentication Library (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft.

## <a name="difference-highlights"></a>Różnice między wyróżnieniami

Biblioteka ADAL współpracuje z punktem końcowym Azure Active Directory (Azure AD) v 1.0. Biblioteka Microsoft Authentication Library (MSAL) współpracuje z platformą tożsamości firmy Microsoft — znana wcześniej jako punkt końcowy Azure Active Directory v 2.0. Platforma tożsamości firmy Microsoft różni się od usługi Azure AD v 1.0:

Uguje
  - Konta służbowe (konta aprowizacji usługi Azure AD)
  - Konta osobiste (takie jak Outlook.com lub Hotmail.com)
  - Klienci, którzy korzystają z własnej poczty e-mail lub tożsamości społecznościowej (na przykład LinkedIn, Facebook, Google) za pośrednictwem oferty Azure AD B2C

- Są zgodne ze standardami:
  - OAuth 2.0
  - OpenID Connect Connect (OIDC)

Zobacz [, co różni się w punkcie końcowym platformy Identity platform (v 2.0) firmy Microsoft,](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) Aby uzyskać więcej informacji.

### <a name="scopes-not-resources"></a>Zakresy nie są zasobami

Biblioteka ADAL Python uzyskuje tokeny dla zasobów, ale MSAL Python uzyskuje tokeny dla zakresów. Powierzchnia interfejsu API w MSAL Python nie ma już parametru zasobu. Należy podać zakresy jako listę ciągów, które deklarują żądane uprawnienia i żądane zasoby. Aby zobaczyć przykład zakresów, zobacz [zakresy Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Obsługa błędów

Biblioteka Azure Active Directory Authentication Library (ADAL) dla języka Python używa `AdalError` wyjątków do wskazywania, że wystąpił problem. MSAL for Python zwykle używa kodów błędów zamiast. Aby uzyskać więcej informacji, zobacz [MSAL for Python — obsługa błędów](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Zmiany interfejsu API

Poniższa tabela zawiera listę interfejsów API w bibliotece ADAL dla języka Python oraz te, które mają być używane w środowisku MSAL dla języka Python:

| Biblioteka ADAL dla interfejsu API języka Python  | MSAL dla interfejsu API języka Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication lub ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| ND  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | ND |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) i [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) i [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| ND | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| ND | Pamięć podręczna z trwałość, dostępna z poziomu [rozszerzeń MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrowanie istniejących tokenów odświeżania dla języka MSAL Python

Biblioteka Microsoft Authentication Library (MSAL) stanowi streszczenie koncepcji tokenów odświeżania. MSAL Python domyślnie udostępnia pamięć podręczną tokenów w pamięci, dzięki czemu nie trzeba przechowywać, wyszukiwać ani aktualizować tokenów odświeżania. Użytkownicy będą również widzieć mniejszą liczbę monitów logowania, ponieważ tokeny odświeżania mogą być zazwyczaj aktualizowane bez interwencji użytkownika. Aby uzyskać więcej informacji na temat pamięci podręcznej tokenów, zobacz [niestandardowe serializacji pamięci podręcznej w MSAL dla języka Python](msal-python-token-cache-serialization.md).

Poniższy kod pomoże Ci migrować tokeny odświeżania zarządzane przez inną bibliotekę OAuth2 (w tym, ale nie ograniczone do biblioteki ADAL Python), aby były zarządzane przez MSAL dla języka Python. Jedną z przyczyn migrowania tych tokenów odświeżania jest uniemożliwienie zalogowania się istniejących użytkowników podczas migrowania aplikacji do MSAL dla języka Python.

Metoda migrowania tokenu odświeżania polega na użyciu MSAL dla języka Python w celu uzyskania nowego tokenu dostępu przy użyciu poprzedniego tokenu odświeżania. Po zwróceniu nowego tokenu odświeżania MSAL dla języka Python zapisze go w pamięci podręcznej. Oto przykład, jak to zrobić:

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

Aby uzyskać więcej informacji, zobacz [porównanie v 1.0 i 2.0](active-directory-v2-compare.md).
