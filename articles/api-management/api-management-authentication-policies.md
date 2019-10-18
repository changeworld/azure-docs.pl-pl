---
title: Zasady uwierzytelniania API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat zasad uwierzytelniania dostępnych do użycia w usłudze Azure API Management.
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
ms.openlocfilehash: 572d8c4b59622156e8b3aca4565bbc206367f6d4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514855"
---
# <a name="api-management-authentication-policies"></a>Zasady uwierzytelniania API Management
Ten temat zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a>Zasady uwierzytelniania

-   [Uwierzytelnianie z](api-management-authentication-policies.md#Basic) uwierzytelnianiem Basic z użyciem usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego.

-   [Uwierzytelnianie przy użyciu certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu certyfikatów klienta.

-   [Uwierzytelnianie przy użyciu tożsamości zarządzanej](api-management-authentication-policies.md#ManagedIdentity) — uwierzytelnianie za pomocą [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) dla usługi API Management.

##  <a name="Basic"></a>Uwierzytelnianie za pomocą języka Basic
 Użyj zasad `authentication-basic`, aby uwierzytelniać się w usłudze zaplecza przy użyciu uwierzytelniania podstawowego. Te zasady skutecznie ustawiają nagłówek autoryzacji HTTP na wartość odpowiadającą podanym w zasadzie poświadczeniami.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Przykład

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementy

|Nazwa|Opis|Wymagane|
|----------|-----------------|--------------|
|Uwierzytelnianie — podstawowe|Element główny.|Tak|

### <a name="attributes"></a>Atrybuty

|Nazwa|Opis|Wymagane|Domyślne|
|----------|-----------------|--------------|-------------|
|nazwa użytkownika|Określa nazwę użytkownika poświadczeń podstawowych.|Tak|ND|
|hasło|Określa hasło poświadczeń podstawowych.|Tak|ND|

### <a name="usage"></a>Użycie
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

##  <a name="ClientCertificate"></a>Uwierzytelnianie przy użyciu certyfikatu klienta
 Użyj zasad `authentication-certificate`, aby uwierzytelniać się w usłudze zaplecza przy użyciu certyfikatu klienta. Certyfikat musi zostać [zainstalowany do API Management](https://go.microsoft.com/fwlink/?LinkID=511599) jako pierwszy i jest identyfikowany przez jego odcisk palca.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Przykłady

Ten przykładowy certyfikat klienta jest identyfikowany za pomocą odcisku palca.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Ten przykładowy certyfikat klienta jest identyfikowany według nazwy zasobu.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|Uwierzytelnianie — certyfikat|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|odcisk palca|Odcisk palca certyfikatu klienta.|Musi być obecna wartość `thumbprint` lub `certificate-id`.|ND|  
|Identyfikator certyfikatu|Nazwa zasobu certyfikatu.|Musi być obecna wartość `thumbprint` lub `certificate-id`.|ND|  
  
### <a name="usage"></a>Użycie  
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.  
  
-   **Sekcje zasad:** przychodzące  
  
-   **Zakresy zasad:** wszystkie zakresy  

##  <a name="ManagedIdentity"></a>Uwierzytelnianie przy użyciu tożsamości zarządzanej  
 Użyj zasad `authentication-managed-identity`, aby uwierzytelniać się w usłudze zaplecza przy użyciu tożsamości zarządzanej usługi API Management. Te zasady zasadniczo wykorzystują zarządzaną tożsamość do uzyskiwania tokenu dostępu z Azure Active Directory na potrzeby uzyskiwania dostępu do określonego zasobu. Po pomyślnym uzyskaniu tokenu zasady ustawili wartość tokenu w nagłówku `Authorization` przy użyciu schematu `Bearer`.
  
### <a name="policy-statement"></a>Instrukcja zasad  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Przykład  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Uwierzytelnianie za pomocą usługi wewnętrznej bazy danych przy użyciu tożsamości zarządzanej
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
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
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Używanie tożsamości zarządzanej w zasadach wysyłania żądania
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|Uwierzytelnianie — tożsamość zarządzana |Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Zasoby|parametry. Identyfikator URI aplikacji docelowego internetowego interfejsu API (zabezpieczony zasób) w Azure Active Directory.|Tak|ND|  
|Output-token-Variable-Name|parametry. Nazwa zmiennej kontekstowej, która będzie otrzymywać wartość tokenu jako typ obiektu `string`. |Nie|ND|  
|Ignoruj-błąd|Typu. Jeśli zostanie ustawiona na `true`, potok zasad będzie nadal wykonywany nawet wtedy, gdy nie zostanie uzyskany token dostępu.|Nie|false|  
  
### <a name="usage"></a>Użycie  
 Tych zasad można używać w następujących [sekcjach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresach](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)zasad.  
  
-   **Sekcje zasad:** przychodzące  
  
-   **Zakresy zasad:** wszystkie zakresy

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

+ [Zasady w API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Dokumentacja zasad](api-management-policy-reference.md) pełna lista instrukcji zasad i ich ustawień
+ [Przykłady zasad](policy-samples.md)
