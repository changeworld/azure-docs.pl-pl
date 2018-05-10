---
title: Formanty strony Zarządzanie interfejsami API Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat dostępnych do użycia w szablonach portalu deweloperów w usłudze Azure API Management formantów strony.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: da68c9b7ebbb1880e35bd60b12db9f920f51e13c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="azure-api-management-page-controls"></a>Formanty strony Zarządzanie interfejsami API Azure
Azure API Management zapewnia następujące formanty do użycia w dewelopera szablony portalu.  
  
Aby użyć formantu, należy umieścić w dowolnym miejscu w szablonie portalu deweloperów. Niektóre formanty, takie jak [akcje aplikacji](#app-actions) kontrolować, mieć parametrów, jak pokazano w poniższym przykładzie:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Wartości parametrów są przekazywane w jako część modelu danych dla szablonu. W większości przypadków można po prostu wkleić w podanym przykładzie dla każdego formantu, aby działać poprawnie. Aby uzyskać więcej informacji o wartościach parametrów widać sekcji modelu danych do każdego szablonu, w którym można użyć formantu.  
  
 Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [dostosowywaniu portalu dla deweloperów interfejsu API zarządzania za pomocą szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Formantów strony szablonu portalu dla deweloperów  
  
-   [Akcje aplikacji](#app-actions)  
-   [Rejestrowanie Basic](#basic-signin)  
-   [Formant stronicowania](#paging-control)  
-   [dostawców](#providers)  
-   [formant wyszukiwania](#search-control)  
-   [rejestracji](#sign-up)  
-   [przycisk subskrypcji](#subscribe-button)  
-   [Anuluj subskrypcję](#subscription-cancel)  
  
##  <a name="app-actions"></a> Akcje aplikacji  
 `app-actions` Kontroli udostępnia interfejs użytkownika do interakcji z aplikacjami na stronie profilu użytkownika w portalu dla deweloperów.  
  
 ![Aplikacja&#45;kontroli akcje](./media/api-management-page-controls/APIM-app-actions-control.png "APIM akcje aplikacji kontroli")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Opis|  
|---------------|-----------------|  
|appId|Identyfikator aplikacji.|  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `app-actions` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Aplikacje](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> Rejestrowanie Basic  
 `basic-signin` Kontroli udostępnia kontrolkę do pobierania użytkownika logowania na stronie logowania w portalu dla deweloperów.  
  
 ![podstawowe&#45;kontroli signin](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic Rejestrowanie formantu")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `basic-signin` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Rejestrowanie](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> Formant stronicowania  
 `paging-control` Zawiera funkcje stronicowania po developer portal stron, które wyświetlania listy elementów.  
  
 ![stronicowanie kontroli](./media/api-management-page-controls/APIM-paging-control.png "APIM formant stronicowania")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `paging-control` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Lista interfejsu API](api-management-api-templates.md#APIList)  
  
-   [Lista problemów](api-management-issue-templates.md#IssueList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> dostawców  
 `providers` Kontroli udostępnia kontrolkę wyboru dostawców uwierzytelniania na stronie logowania w portalu dla deweloperów.  
  
 ![Formant dostawców](./media/api-management-page-controls/APIM-providers-control.png "APIM dostawców kontroli")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `providers` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Rejestrowanie](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> formant wyszukiwania  
 `search-control` Udostępnia funkcje wyszukiwania na deweloperów na stronach portalu zawierające listę elementów.  
  
 ![Wyszukaj kontroli](./media/api-management-page-controls/APIM-search-control.png "APIM formant wyszukiwania")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `search-control` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Lista interfejsu API](api-management-api-templates.md#APIList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> rejestracji  
 `sign-up` Kontroli udostępnia kontrolkę do zbierania informacji o profilu użytkownika na stronie tworzenia konta w portalu dla deweloperów.  
  
 ![znak&#45;w górę kontrolki](./media/api-management-page-controls/APIM-sign-up-control.png "APIM zapisywania kontrolki")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `sign-up` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Zarejestruj się](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> przycisk subskrypcji  
 `subscribe-button` Udostępnia kontrolkę do subskrypcji użytkownika produktu.  
  
 ![Subskrypcja&#45;przycisku kontrolki](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM formantu przycisku subskrypcji")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `subscribe-button` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Produktu](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> Anuluj subskrypcję  
 `subscription-cancel` Kontroli udostępnia kontrolkę do anulowanie subskrypcji produktu na stronie profilu użytkownika w portalu dla deweloperów.  
  
 ![Subskrypcja&#45;Anuluj kontroli](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM kontroli Anuluj subskrypcję")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Opis|  
|---------------|-----------------|  
|subscriptionId|Identyfikator subskrypcji można anulować.|  
|cancelUrl|Subskrypcja anuluje adresu URL.|  
  
### <a name="developer-portal-templates"></a>Szablony portalu dla deweloperów  
 `subscription-cancel` Formant może być używany w następujących szablonów portalu developer:  
  
-   [Produktu](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [dostosowywaniu portalu dla deweloperów interfejsu API zarządzania za pomocą szablonów](api-management-developer-portal-templates.md).