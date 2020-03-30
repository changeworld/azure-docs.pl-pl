---
title: Szablony portalu deweloperów usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak dostosować zawartość stron portalu deweloperów przy użyciu zestawu szablonów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 5189f3d8-2a4c-4dc8-ab19-11c7df0114d4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 9bbb7ef46fbde881c8968128870d24a03b046c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176791"
---
# <a name="developer-portal-templates"></a>Szablony portali deweloperów

Usługa Azure API Management umożliwia dostosowanie zawartości stron portalu deweloperów przy użyciu zestawu szablonów, które konfigurują ich zawartość. Za pomocą [dotLiquid](http://dotliquidmarkup.org/) składni i edytora do wyboru, takich jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i pod warunkiem, zestaw zlokalizowanych [zasobów ciąg,](api-management-template-resources.md#strings) [zasoby glifów](api-management-template-resources.md#glyphs)i [page formantów,](api-management-page-controls.md)masz dużą elastyczność, aby skonfigurować zawartość stron, jak można uznać za stosowne przy użyciu tych szablonów.  
  
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

##  <a name="developer-portal-templates"></a><a name="DeveloperPortalTemplates"></a>Szablony portali deweloperów  
  
-   [Interfejsy API](api-management-api-templates.md)  
    -   [Lista interfejsów API](api-management-api-templates.md#APIList)  
    -   [Operacji](api-management-api-templates.md#Product)  
    -   [Przykłady kodu](api-management-api-templates.md#CodeSamples)  
        -   [Curl](api-management-api-templates.md#Curl)  
        -   [C #](api-management-api-templates.md#CSharp)  
        -   [Java](api-management-api-templates.md#Stub)  
        -   [Javascript](api-management-api-templates.md#JavaScript)  
        -   [Cel C](api-management-api-templates.md#ObjectiveC)  
        -   [PHP](api-management-api-templates.md#PHP)  
        -   [Python](api-management-api-templates.md#Python)  
        -   [Ruby](api-management-api-templates.md#Ruby)  
-   [Produkty](api-management-product-templates.md)  
    -   [Lista produktów](api-management-product-templates.md#ProductList)  
    -   [Product (Produkt)](api-management-product-templates.md#Product)  
-   [Aplikacje](api-management-application-templates.md)  
    -   [Lista aplikacji](api-management-application-templates.md#ProductList)  
    -   [Aplikacja](api-management-application-templates.md#Application)  
-   [Problemy](api-management-issue-templates.md)  
    -   [Lista problemów](api-management-issue-templates.md#IssueList)  
-   [Profil użytkownika](api-management-user-profile-templates.md)  
    -   [Profil](api-management-user-profile-templates.md#Profile)  
    -   [Subskrypcje](api-management-user-profile-templates.md#Subscriptions)  
    -   [Aplikacje](api-management-user-profile-templates.md#Applications)  
    -   [Aktualizowanie informacji o koncie](api-management-user-profile-templates.md#UpdateAccountInfo)  
-   [Strony](api-management-page-templates.md)  
    -   [Zaloguj się](api-management-page-templates.md#SignIn)  
    -   [Zarejestruj się](api-management-page-templates.md#SignUp)  
    -   [Nie znaleziono strony](api-management-page-templates.md#PageNotFound)

## <a name="next-steps"></a>Następne kroki  

+ [Dokumentacja dotycząca szablonów](api-management-developer-portal-templates-reference.md)  
+ [Dokumentacja dotycząca modeli obiektów](api-management-template-data-model-reference.md)  
+ [Kontrolki strony](api-management-page-controls.md)  
+ [Zasoby szablonu](api-management-template-resources.md)