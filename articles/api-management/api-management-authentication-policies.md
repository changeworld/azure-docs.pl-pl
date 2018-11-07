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
ms.openlocfilehash: 4c4c03fffa5786bf3a50f4d2c03511f0a2de0f48
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250955"
---
# <a name="api-management-authentication-policies"></a>Zasady uwierzytelniania usługi API Management
Ten temat zawiera odwołania do następujących zasad usługi API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Zasady uwierzytelniania  
  
-   [Uwierzytelnianie Basic](api-management-authentication-policies.md#Basic) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu uwierzytelniania podstawowego.  
  
-   [Uwierzytelnianie za pomocą certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) — uwierzytelnianie za pomocą usługi zaplecza przy użyciu certyfikatów klienta.  
  
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
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|Uwierzytelnianie podstawowe|Element główny.|Yes|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|nazwa użytkownika|Określa atrybut username podstawowych poświadczeń.|Yes|ND|  
|hasło|Określa hasło podstawowych poświadczeń.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** interfejsu API  
  
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
  
|Name (Nazwa)|Opis|Wymagane|  
|----------|-----------------|--------------|  
|certyfikat uwierzytelniania|Element główny.|Yes|  
  
### <a name="attributes"></a>Atrybuty  
  
|Name (Nazwa)|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Odcisk palca|Odcisk palca certyfikatu klienta.|Yes|ND|  
  
### <a name="usage"></a>Sposób użycia  
 Ta zasada może służyć w następujących zasadach [sekcje](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** interfejsu API  
  

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji, w pracy z tymi zasadami zobacz:

+ [Zasady usługi API Management](api-management-howto-policies.md)
+ [Przekształć interfejsy API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
