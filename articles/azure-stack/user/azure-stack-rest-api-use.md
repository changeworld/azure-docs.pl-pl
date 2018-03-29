---
title: Użyj interfejsu API stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać uwierzytelniania z platformą Azure, aby wysyłać żądania interfejsu API Azure stosu.
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 3523f86791a3bf437cbd21ba4df5afc0cd1955fd
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
<!--  cblackuk and charliejllewellyn -->

# <a name="use-the-azure-stack-api"></a>Użyj interfejsu API Azure stosu

Można użyć interfejsu API stosu Azure do automatyzacji operacji, takich jak syndicating elementów marketplace.

Proces wymaga, aby Twoje uwierzytelnienia klienta względem punktu końcowego logowania Microsoft Azure. Punkt końcowy zwraca token. Podaj token w nagłówku każde żądanie wysłane do interfejsu API Azure stosu. Platforma Azure korzysta Oauth2.0.

Ten artykuł zawiera proste przykłady określonych stos Azure używane narzędzie curl. Ten temat przeprowadzi Cię przez proces pobierania tokenu na potrzeby dostępu interfejsu API stosu Azure. Większość języków udostępnia bibliotekach Oauth 2.0, które ma niezawodne funkcje zarządzania tokenu i obsługi zadań takich token odświeżania.

Spojrzenie na cały proces przy użyciu interfejsu API REST stosu Azure z ogólnym klienta REST takie jak curl mogą ułatwić zrozumienie podstawowej żądania, a czego można oczekiwać w ładunku odpowiedzi.

W tym artykule nie Poznaj także wszystkie opcje dostępne dla pobierania tokenów, takie jak logowania interakcyjnego lub tworzenia dedykowanych identyfikatorów aplikacji. Aby uzyskać więcej informacji, zobacz [dokumentacja interfejsu API REST Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Pobierz token z platformy Azure

Utwórz żądanie *treści* sformatowany przy użyciu typu zawartości x--www-form-urlencoded można uzyskać tokenu dostępu. Po żądania do punktu końcowego uwierzytelniania REST Azure i logowania.
```
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**Identyfikator dzierżawy** jest:

- Domena dzierżawy, takie jak fabrikam.onmicrosoft.com
- Twój identyfikator dzierżawy, takie jak 8eaed023-2b34-4da1-9baa-8bc8c9d6a491
- Wartość domyślna dla kluczy dzierżawy niezależny: wspólnej

### <a name="post-body"></a>Treść wpisu
```
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Dla każdej wartości:

  **grant_type**
  
  Typ schematu uwierzytelniania będzie przy użyciu. W tym przykładzie wartość to:
  ```
  password
  ```

  **resource**

  Zasób uzyskuje dostęp do tokenu. Badając punktu końcowego metadanych zarządzania stosu Azure można znaleźć zasobu. Przyjrzyj się **odbiorców** sekcji

  Punkt końcowy usługi Azure Management stosu:
  ```
  https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
  ```
 > [!NOTE]  
 > Jeśli jesteś administratorem próby uzyskania dostępu do interfejsu API dzierżawcy, należy upewnić się użyć punktu końcowego dzierżawcy, na przykład "https://adminmanagement.{region}.{Azure domeny stosu} punkty końcowe metadanych /? api-version = 2015-01-011
  
  Na przykład z usługi Azure stosu Development Kit:
  ```
  curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
  ```
 
  Odpowiedź:
  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>Przykład
  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Ta wartość jest zapisane na stałe wartość domyślna:
  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternatywne opcje są dostępne w określonych scenariuszach:
  
  | Aplikacja | Identyfikator aplikacji |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **Nazwa użytkownika**
  
  Azure stosu AAD konto, na przykład:
  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **Hasło**

  Hasło administratora usługi Azure stosu AAD.
  
### <a name="example"></a>Przykład

Żądanie:
```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Odpowiedź:
```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Zapytania interfejsu API

Po uzyskaniu tokenu dostępu, należy dodać ją jako nagłówka do wszystkich żądań interfejsu API. Aby to zrobić, należy utworzyć nagłówek **autoryzacji** z wartością: `Bearer <access token>`. Na przykład:

Żądanie:
```
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Odpowiedź:
```
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Adres URL struktury i składni zapytań

Identyfikator URI żądania ogólnego składa się z: {schemat identyfikatora URI} :// {host identyfikatora URI} / {ścieżka zasobu}? {Ciąg zapytania}

- **Schemat identyfikatora URI**:  
Identyfikator URI Określa protokół używany do wysyłania żądania. Na przykład `http` lub `https`.
- **Host identyfikatora URI**:  
Host Określa nazwę domeny lub adres IP serwera, na którym hostowana jest punktem końcowym usługi REST, takich jak `graph.microsoft.com` lub `adminmanagement.local.azurestack.external`.
- **Ścieżka zasobu**:  
Ścieżka Określa zasobu lub kolekcji zasobów, co może obejmować wiele segmentów używane przez usługę w określeniu zaznaczenie tych zasobów. Na przykład: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` może służyć do badania właścicieli określonej aplikacji w kolekcji aplikacji z listy.
- **Długość ciągu zapytania**:  
Ciąg zawiera dodatkowych parametrów proste, na przykład kryteria wyboru wersji lub zasobu interfejsu API.

## <a name="azure-stack-request-uri-construct"></a>Konstrukcja identyfikatora URI żądania w usłudze Azure stosu
```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version} 
```

### <a name="uri-syntax"></a>Składnia identyfikatora URI
```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version} 
```

### <a name="query-uri-example"></a>Przykład identyfikatora URI zapytania
```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji o używaniu Azure RESTful punktów końcowych, zobacz [dokumentacja interfejsu API REST Azure](https://docs.microsoft.com/rest/api/).
