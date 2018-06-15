---
title: Zasady uwierzytelniania usługi Azure API Management | Dokumentacja firmy Microsoft
description: Więcej informacji na temat zasad uwierzytelniania można używać usługi Azure API Management.
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
ms.openlocfilehash: 4d13d9dbea9da9db5bfe9a9af85fdbf9eab1ae84
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2017
ms.locfileid: "26127763"
---
# <a name="api-management-authentication-policies"></a>Zarządzanie interfejsami API zasady uwierzytelniania
W tym temacie znajdują się informacje na następujące zasady usługi API Management. Aby uzyskać informacje dotyczące dodawania i konfigurowania zasad, zobacz [zasad w usłudze API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a>Zasady uwierzytelniania  
  
-   [Uwierzytelnianie za pomocą Basic](api-management-authentication-policies.md#Basic) -uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego.  
  
-   [Uwierzytelniania za pomocą certyfikatu klienta](api-management-authentication-policies.md#ClientCertificate) -uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu certyfikatów klienta.  
  
##  <a name="Basic"></a>Uwierzytelniania Basic  
 Użyj `authentication-basic` zasad uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu uwierzytelniania podstawowego. Ta zasada efektywnie ustawia dla nagłówka HTTP autoryzacji wartość odpowiadającą poświadczenia podane w zasadach.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
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
|Uwierzytelnianie — podstawowy|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|nazwa użytkownika|Określa nazwę użytkownika, podstawowe poświadczenia.|Tak|Nie dotyczy|  
|hasło|Określa hasło podstawowych poświadczeń.|Tak|Nie dotyczy|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** interfejsu API  
  
##  <a name="ClientCertificate"></a>Uwierzytelniania za pomocą certyfikatu klienta  
 Użyj `authentication-certificate` zasad uwierzytelniania za pomocą usługi wewnętrznej bazy danych przy użyciu certyfikatu klienta. Ten certyfikat musi być [zainstalowane do interfejsu API zarządzania](http://go.microsoft.com/fwlink/?LinkID=511599) pierwszy i jest identyfikowany przez jego odcisk palca.  
  
### <a name="policy-statement"></a>Deklaracja zasad  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Przykład  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Elementy  
  
|Nazwa|Opis|Wymagane|  
|----------|-----------------|--------------|  
|certyfikat uwierzytelniania|Element główny.|Tak|  
  
### <a name="attributes"></a>Atrybuty  
  
|Nazwa|Opis|Wymagane|Domyślne|  
|----------|-----------------|--------------|-------------|  
|Odcisk palca|Odcisk palca certyfikatu klienta.|Tak|Nie dotyczy|  
  
### <a name="usage"></a>Sposób użycia  
 Te zasady służą następujące zasady [sekcje](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) i [zakresy](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Sekcje zasad:** dla ruchu przychodzącego  
  
-   **Zakresy zasad:** interfejsu API  
  

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, Praca z zasad Zobacz:

+ [Zasady w usłudze API Management](api-management-howto-policies.md)
+ [Przekształć interfejsów API](transform-api.md)
+ [Informacje o zasadach](api-management-policy-reference.md) pełną listę deklaracji zasad i ich ustawienia
+ [Przykłady zasad](policy-samples.md)   
