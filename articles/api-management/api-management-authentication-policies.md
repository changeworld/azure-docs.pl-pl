---
title: Zasady uwierzytelniania w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasad uwierzytelniania można używać w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 9ee4a9fb5c63061eed32389b5672652aad01208a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994952"
---
# <a name="api-management-authentication-policies"></a>Zasady uwierzytelniania usługi API Management
Ten temat zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Zasady uwierzytelniania  
  
-   [Uwierzytelnianie Basic](api-management-authentication-policies.md#Basic) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu uwierzytelniania podstawowego.  
  
-   [Uwierzytelnianie za pomocą certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu certyfikatów klienta.  

-   [Uwierzytelnianie za pomocą tożsamości zarządzanej](api-management-authentication-policies.md#ManagedIdentity) — uwierzytelnianie za pomocą [tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md) usługi API Management.  
  
##  <a name="Basic"></a> Uwierzytelnianie Basic  
 Użyj `authentication-basic` zasad uwierzytelniania za pomocą usługi zaplecza przy użyciu uwierzytelniania podstawowego. Ta zasada efektywnie ustawia nagłówek autoryzacji HTTP na wartość odpowiadającą poświadczenia podane w zasadach.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Przykład  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Elementy  
  
|Name (Nazwa)|Opis|Wymagany|  
|----------|-----------------|--------------|  
|Uwierzytelnianie podstawowe|Element główny.|Yes|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagany|Domyślne|  
|----------|-----------------|--------------|-------------|  
|nazwa użytkownika|Określa atrybut username podstawowych poświadczeń.|Yes|ND|  
|password|Określa hasło podstawowych poświadczeń.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** Interfejs API  
  
##  <a name="ClientCertificate"></a> Uwierzytelnianie za pomocą certyfikatu klienta  
 Użyj `authentication-certificate` zasad uwierzytelniania za pomocą usługi zaplecza przy użyciu certyfikatu klienta. Ten certyfikat musi być [zainstalowane do usługi API Management](https://go.microsoft.com/fwlink/?LinkID=511599) pierwszy i jest identyfikowany przez jego odcisk palca.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Przykład  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elementy  
  
|Name (Nazwa)|Opis|Wymagany|  
|----------|-----------------|--------------|  
|authentication-certificate|Element główny.|Yes|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagany|Domyślne|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Odcisk palca certyfikatu klienta.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** Interfejs API  

##  <a name="ManagedIdentity"></a> Uwierzytelnianie za pomocą tożsamości zarządzanych  
 Użyj `authentication-managed-identity` zasad uwierzytelniania za pomocą usługi zaplecza, używając zarządzanej tożsamości usługi API Management. Te zasady skutecznie korzysta z tożsamości zarządzanej można uzyskać tokenu dostępu z usługi Azure Active Directory do uzyskiwania dostępu do określonego zasobu. 
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Przykład  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Elementy  
  
|Name (Nazwa)|Opis|Wymagany|  
|----------|-----------------|--------------|  
|Uwierzytelnianie zarządzane identity |Element główny.|Yes|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagany|Domyślne|  
|----------|-----------------|--------------|-------------|  
|zasób|ciąg. Identyfikator URI Identyfikatora aplikacji docelowej sieci Web interfejsu API (zabezpieczono zasób) w usłudze Azure Active Directory.|Yes|ND|  
|dane wyjściowe — token-— nazwa zmiennej|ciąg. Nazwa zmiennej kontekstu, który otrzyma wartość tokenu jako typ obiektu `string`.|Nie|ND|  
|ignore-error|Wartość logiczna. Jeśli ustawiono `true`, potok zasady będą w dalszym ciągu wykonują nawet wtedy, gdy token dostępu nie zostanie uzyskana.|Nie|false|  
  
### <a name="usage"></a>Sposób użycia  
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** globalny, produktu, interfejsu API, operacji  

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

+ [Zasady usługi API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
