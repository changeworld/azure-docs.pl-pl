---
title: Użyj interfejsu API stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pobrać uwierzytelniania z platformą Azure, aby wysyłać żądania interfejsu API Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e8a9489a3f487a45303bac45f805381b41427b4b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Użyj interfejsu API Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Azure stosu interfejsu programowania aplikacji (API) służy do automatyzacji operacji, takich jak syndicating elementów marketplace.

Interfejs API wymaga klienta do uwierzytelniania względem punktu końcowego logowania Microsoft Azure. Punkt końcowy zwraca token do użycia w nagłówku każde żądanie wysłane do interfejsu API Azure stosu. Microsoft Azure korzysta z protokołu Oauth 2.0.

W tym artykule przedstawiono przykłady, które używają **cURL** narzędzie do tworzenia żądań stosu Azure. Aplikacja, zwinięcie, jest narzędziem wiersza polecenia z biblioteki do przesyłania danych. Poniższe przykłady przeprowadzenie proces pobierania tokenu na potrzeby dostępu interfejsu API stosu Azure. Większość języków programowania udostępnia bibliotekach Oauth 2.0, które są niezawodne tokenu zadania zarządzania i obsługi takich token odświeżania.

Przejrzyj cały proces przy użyciu interfejsu API REST stosu Azure z ogólnym klienta REST, takich jak **cURL**, aby ułatwić zrozumienie podstawowych żądania i pokazuje, czego można oczekiwać w ładunku odpowiedzi.

W tym artykule nie Eksploruj wszystkie opcje dostępne dla pobierania tokenów, takie jak logowania interakcyjnego lub tworzenia dedykowanych identyfikatorów aplikacji. Aby uzyskać informacje dotyczące tych tematów, zobacz [dokumentacja interfejsu API REST Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Pobierz token z platformy Azure

Utwórz treści żądania, sformatowany przy użyciu typu zawartości x--www-form-urlencoded można uzyskać tokenu dostępu. Po żądania do punktu końcowego uwierzytelniania REST Azure i logowania.

### <a name="uri"></a>Identyfikator URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**Identyfikator dzierżawy** jest:

 - Domenę dzierżawy, takich jak `fabrikam.onmicrosoft.com`
 - Identyfikator dzierżawy, takich jak `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Wartość domyślna dla kluczy niezależny od dzierżawy: `common`

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
    Typ schematu uwierzytelniania będzie przy użyciu. W tym przykładzie wartość to `password`

 - **resource**  
    Zasób uzyskuje dostęp do tokenu. Badając punktu końcowego metadanych zarządzania stosu Azure można znaleźć zasobu. Przyjrzyj się **odbiorców** sekcji

 - **Punkt końcowy zarządzania Azure stosu**  
    ```
    https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
    ```

  > [!NOTE]  
  > Jeśli jesteś administratorem próby uzyskania dostępu do interfejsu API dzierżawcy musi upewnij się, że należy użyć punktu końcowego dzierżawcy, na przykład: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Na przykład z zestawem Azure stosu programowanie jako punktu końcowego:

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

  Na przykład konto Azure AAD stosu:

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

Po pobraniu tokenu dostępu, należy dodać go jako nagłówka do wszystkich żądań interfejsu API. Aby to zrobić, należy utworzyć nagłówek **autoryzacji** z wartością: `Bearer <access token>`. Na przykład:

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
