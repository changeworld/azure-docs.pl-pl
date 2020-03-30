---
title: Zasady uwierzytelniania usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach uwierzytelniania dostępnych do użycia w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473561"
---
# <a name="api-management-authentication-policies"></a>Zasady uwierzytelniania w usłudze API Management
Ten temat zawiera odwołanie do następujących zasad zarządzania interfejsami API. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [Zasady w zarządzania interfejsami API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Zasady uwierzytelniania

-   [Uwierzytelnij się za pomocą podstawowego](api-management-authentication-policies.md#Basic) — uwierzytelnij się za pomocą usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego.

-   [Uwierzytelnij się przy użyciu certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnij się za pomocą usługi wewnętrznej bazy danych przy użyciu certyfikatów klienta.

-   [Uwierzytelnij się przy użyciu tożsamości zarządzanej](api-management-authentication-policies.md#ManagedIdentity) — uwierzytelnij się przy użyciu [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla usługi api Management.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>Uwierzytelnij się za pomocą języka basic
 Użyj `authentication-basic` zasad do uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego. Ta zasada skutecznie ustawia nagłówek autoryzacji HTTP na wartość odpowiadającą poświadczeń podanych w zasadach.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Przykład

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagany|
|----------|-----------------|--------------|
|uwierzytelnianie podstawowe|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagany|Domyślne|
|----------|-----------------|--------------|-------------|
|nazwa użytkownika|Określa nazwę użytkownika poświadczeń podstawowych.|Tak|Nie dotyczy|
|hasło|Określa hasło poświadczenia podstawowego.|Tak|Nie dotyczy|

### <a name="usage"></a>Sposób użycia
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>Uwierzytelnij się przy użyciu certyfikatu klienta
 Zasady `authentication-certificate` służy do uwierzytelniania przy użyciu usługi wewnętrznej bazy danych przy użyciu certyfikatu klienta. Certyfikat musi być [zainstalowany w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=511599) pierwszy i jest identyfikowany przez jego odcisk palca.

### <a name="policy-statement"></a>Oświadczenie dotyczące zasad

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Przykłady

W tym przykładzie certyfikat klienta jest identyfikowany przez jego odcisk palca.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
W tym przykładzie certyfikat klienta jest identyfikowany przez nazwę zasobu.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagany|  
|----------|-----------------|--------------|  
|certyfikat uwierzytelniania|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagany|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Odcisk palca|Odcisk palca certyfikatu klienta.|Albo `thumbprint` musi `certificate-id` być obecny.|Nie dotyczy|  
|identyfikator certyfikatu|Nazwa zasobu certyfikatu.|Albo `thumbprint` musi `certificate-id` być obecny.|Nie dotyczy|  
  
### <a name="usage"></a>Sposób użycia  
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .  
  
-   **Sekcje zasad:** przychodzące  
  
-   **Zakresy zasad:** wszystkie zakresy  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Uwierzytelnij się przy użyciu tożsamości zarządzanej  
 Zasady `authentication-managed-identity` można używać do uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu tożsamości zarządzanej usługi api Management. Ta zasada zasadniczo używa tożsamości zarządzanej, aby uzyskać token dostępu z usługi Azure Active Directory do uzyskiwania dostępu do określonego zasobu. Po pomyślnym uzyskaniu tokenu zasady ustawi wartość tokenu w nagłówku `Authorization` przy użyciu schematu. `Bearer`
  
### <a name="policy-statement"></a>Oświadczenie dotyczące zasad  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Przykład  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Uwierzytelnianie za pomocą tożsamości zarządzanej za pomocą usługi wewnętrznej bazy danych
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Używanie tożsamości zarządzanej w zasadach żądania wysyłania
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagany|  
|----------|-----------------|--------------|  
|uwierzytelnianie-zarządzana tożsamość |Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagany|Domyślne|  
|----------|-----------------|--------------|-------------|  
|zasób|Ciąg. Identyfikator aplikacji docelowego interfejsu API sieci web (zasobu zabezpieczonego) w usłudze Azure Active Directory.|Tak|Nie dotyczy|  
|nazwa zmiennej tokenu wyjściowego|Ciąg. Nazwa zmiennej kontekstowej, która otrzyma wartość `string`tokenu jako typ obiektu . |Nie|Nie dotyczy|  
|ignoruj-błąd|Boolean. Jeśli ustawiona na `true`, potok zasad będzie nadal wykonywać, nawet jeśli token dostępu nie zostanie uzyskany.|Nie|false|  
  
### <a name="usage"></a>Sposób użycia  
 Ta zasada może być używana w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad .  
  
-   **Sekcje zasad:** przychodzące  
  
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w zarządzaniu interfejsami API](api-management-howto-policies.md)
+ [Przekształcanie interfejsów API](transform-api.md)
+ [Odwołanie do zasad](api-management-policy-reference.md) dla pełnej listy oświadczeń zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
