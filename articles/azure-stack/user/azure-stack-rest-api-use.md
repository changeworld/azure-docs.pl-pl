---
title: Użyj usługi Azure Stack interfejsu API | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać uwierzytelniania z platformy Azure w celu wysyłania żądań interfejsu API do usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: fe516d1d34496d190ae45e00893deb646fc08408
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306560"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Użyj usługi Azure Stack interfejsu API

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Interfejs programowania aplikacji (API) umożliwia automatyzowanie operacji, takich jak dodawanie maszyny Wirtualnej z Twoją chmurą usługi Azure Stack.

Interfejs API wymaga klienta do uwierzytelniania punktu końcowego logowania Microsoft Azure. Punkt końcowy zwraca token do użycia w nagłówku każdego żądania wysyłane do interfejsu API usługi Azure Stack. Microsoft Azure korzysta z protokołu Oauth 2.0.

Ten artykuł zawiera przykłady z zastosowaniem **cURL** narzędzia do tworzenia żądań usługi Azure Stack. Aplikacja programu cURL, jest narzędziem wiersza polecenia za pomocą biblioteki do przesyłania danych. Te przykłady prowadzą użytkownika przez proces pobierania tokenu dostępu do interfejsu API usługi Azure Stack. Większość języków programowania udostępniają biblioteki protokołu Oauth 2.0, które mają niezawodne tokenu zadania zarządzania i obsługują takie odświeżanie tokenu.

Przejrzyj cały proces przy użyciu interfejsu API REST usługi Azure Stack przy użyciu ogólnego klienta REST, takich jak **cURL**, aby ułatwić zrozumienie podstawowych żądania i pokazuje, czego mogą oczekiwać otrzymać w ładunku odpowiedzi.

W tym artykule nie Poznaj wszystkie opcje dostępne do pobierania tokenów, takich jak interakcyjnego logowania lub tworzenia dedykowanych identyfikatory aplikacji. Aby uzyskać informacje dotyczące tych tematów, zobacz [dokumentacja interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Pobierz token z platformy Azure

Tworzenie treści żądania, sformatowany przy użyciu typu zawartości x--www-form-urlencoded do uzyskania tokenu dostępu. PUBLIKUJ Twoje żądanie do punktu końcowego Azure REST uwierzytelniania i logowania.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**Identyfikator dzierżawy** jest:

 - Twoja domena dzierżawy, takich jak `fabrikam.onmicrosoft.com`
 - Identyfikator dzierżawy, takich jak `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Wartość domyślna dla kluczy niezależnie od dzierżawcy: `common`

### <a name="post-body"></a>Treść wpisu

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Dla każdej wartości:

 - **grant_type**  
    Typ schematu uwierzytelniania należy za pomocą. W tym przykładzie wartość `password`

 - **resource**  
    Zasób uzyskuje dostęp do tokenu. Można znaleźć zasobu, badając punkt końcowy metadanych zarządzania usługi Azure Stack. Przyjrzyj się **odbiorców** sekcji

 - **Punkt końcowy zarządzania usługi Azure Stack**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > Jeśli jesteś administratorem, który próbuje uzyskać dostęp interfejs API dzierżawcy musi upewnij się, że punkt końcowy dzierżawy, należy użyć na przykład: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Na przykład za pomocą usługi Azure Stack Development Kit jako punkt końcowy:

    ```bash
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

  Ta wartość jest zakodowana w domyślną wartość:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Alternatywne opcje są dostępne dla konkretnych scenariuszy:

  
  | Aplikacja | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **Nazwa użytkownika**

  Na przykład konto usługi Azure Stack AAD:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **Hasło**

  Hasło administratora usługi Azure Stack w usłudze AAD.

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

## <a name="api-queries"></a>Interfejs API zapytań

Po pobraniu tokenu dostępu, należy dodać go jako nagłówek do poszczególnych żądań interfejsu API. Aby to zrobić, należy utworzyć nagłówek **autoryzacji** z wartością: `Bearer <access token>`. Na przykład:

Żądanie:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Odpowiedź:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Adres URL struktury i składni zapytań

Rodzajowe żądanie identyfikatora URI, który składa się z: {identyfikator URI scheme} :// {host identyfikatora URI} / {ścieżka zasobu}? {Ciąg zapytania}

- **Schemat identyfikatora URI**:  
Identyfikator URI Określa protokół używany do wysyłania żądania. Na przykład `http` lub `https`.
- **Host identyfikatora URI**:  
Host Określa nazwę domeny lub adres IP serwera, na którym hostowana jest punkt końcowy usługi REST, takich jak `graph.microsoft.com` lub `adminmanagement.local.azurestack.external`.
- **Ścieżka zasobu**:  
Ścieżka Określa zasób lub kolekcję zasobów, które mogą obejmować wiele segmentów używanych przez usługę podczas wybierania tych zasobów. Na przykład: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` może służyć do wykonywania zapytań na liście właścicieli określonej aplikacji w obrębie kolekcji aplikacji.
- **Ciąg zapytania**:  
Ciąg udostępnia dodatkowe proste parametry, takie jak API wersji lub kryteria wybierania zasobów.

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

Aby uzyskać więcej informacji o korzystaniu z punktów końcowych RESTful na platformie Azure, zobacz [dokumentacja interfejsu API REST usługi Azure](https://docs.microsoft.com/rest/api/).
