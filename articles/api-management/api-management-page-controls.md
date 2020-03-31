---
title: Formanty strony usługi Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się więcej o formanty strony dostępne do użycia w szablonach portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244019"
---
# <a name="azure-api-management-page-controls"></a>Formanty strony usługi Azure API Management
Usługa Azure API Management udostępnia następujące kontrolki do użycia w szablonach portalu dla deweloperów.  
  
Aby użyć formantu, umieść go w żądanej lokalizacji w szablonie portalu dewelopera. Niektóre formanty, takie jak formant [akcji aplikacji,](#app-actions) mają parametry, jak pokazano w poniższym przykładzie:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Wartości parametrów są przekazywane jako część modelu danych dla szablonu. W większości przypadków można po prostu wkleić w podanym przykładzie dla każdego formantu, aby działał poprawnie. Aby uzyskać więcej informacji na temat wartości parametrów, można zobaczyć sekcję modelu danych dla każdego szablonu, w którym formant może być używany.  

Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Kontrolki strony szablonu portalu deweloperów  
  
-   [działania aplikacji](#app-actions)  
-   [podstawowe-signin](#basic-signin)  
-   [sterowanie stronicowania](#paging-control)  
-   [Dostawców](#providers)  
-   [kontrola wyszukiwania](#search-control)  
-   [Rejestracji](#sign-up)  
-   [przycisk subskrybuj](#subscribe-button)  
-   [subskrypcja-anulowanie](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a>działania aplikacji  
 Formant `app-actions` zapewnia interfejs użytkownika do interakcji z aplikacjami na stronie profilu użytkownika w portalu dla deweloperów.  
  
 ![kontrola&#45;działaniami aplikacji](./media/api-management-page-controls/APIM-app-actions-control.png "Kontrola akcji aplikacji APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Opis|  
|---------------|-----------------|  
|appId|Identyfikator aplikacji.|  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `app-actions` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Aplikacje](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a>podstawowe-signin  
 Formant `basic-signin` zapewnia formant do zbierania informacji logowania użytkownika na stronie logowania w portalu dewelopera.  
  
 ![podstawowa kontrola&#45;signin](./media/api-management-page-controls/APIM-basic-signin-control.png "Kontrola podstawowego logowania APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `basic-signin` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Zaloguj się](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a>sterowanie stronicowania  
 Zapewnia `paging-control` funkcje stronicowania na stronach portalu dewelopera, które wyświetlają listę elementów.  
  
 ![sterowanie stronicowającymi](./media/api-management-page-controls/APIM-paging-control.png "Kontrola stronicowania interfejsu APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `paging-control` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Lista interfejsów API](api-management-api-templates.md#APIList)  
  
-   [Lista problemów](api-management-issue-templates.md#IssueList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>Dostawców  
 Formant `providers` zapewnia kontrolę wyboru dostawców uwierzytelniania na stronie logowania w portalu dewelopera.  
  
 ![dostawcy kontrolują](./media/api-management-page-controls/APIM-providers-control.png "Kontrola dostawców usług APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `providers` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Zaloguj się](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a>kontrola wyszukiwania  
 Zapewnia `search-control` funkcję wyszukiwania na stronach portalu dewelopera, które wyświetlają listę elementów.  
  
 ![kontrola wyszukiwania](./media/api-management-page-controls/APIM-search-control.png "Kontrola wyszukiwania interfejsu APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `search-control` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Lista interfejsów API](api-management-api-templates.md#APIList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>Rejestracji  
 Formant `sign-up` zapewnia formant do zbierania informacji o profilu użytkownika na stronie rejestracji w portalu dla deweloperów.  
  
 ![podpisywanie&#45;kontroli](./media/api-management-page-controls/APIM-sign-up-control.png "Kontrola rejestracji w usłudze APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `sign-up` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Zarejestruj się](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a>przycisk subskrybuj  
 Zapewnia `subscribe-button` formant do subskrybowania użytkownika do produktu.  
  
 ![subskrybowanie&#45;przycisku](./media/api-management-page-controls/APIM-subscribe-button-control.png "Kontrola przycisku subskrypcji APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `subscribe-button` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Product (Produkt)](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a>subskrypcja-anulowanie  
 Formant `subscription-cancel` zapewnia formant anulowania subskrypcji produktu na stronie profilu użytkownika w portalu dla deweloperów.  
  
 ![&#45;anulowania subskrypcji](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Kontrola subskrypcji apim-anuluj")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Opis|  
|---------------|-----------------|  
|subscriptionId|Identyfikator subskrypcji do anulowania.|  
|cancelUrl|Subskrypcja anuluje adres URL.|  
  
### <a name="developer-portal-templates"></a>Szablony portali deweloperów  
 Formant `subscription-cancel` może być używany w następujących szablonach portalu dla deweloperów:  
  
-   [Product (Produkt)](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](api-management-developer-portal-templates.md).
