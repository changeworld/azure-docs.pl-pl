---
title: Formantów strony w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Więcej informacji na temat formantów strony dostępny do użytku w szablonów portalu dla deweloperów w usłudze Azure API Management.
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
ms.openlocfilehash: d87293d89e4009512494bf47f9742ea5901f909a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656986"
---
# <a name="azure-api-management-page-controls"></a>Formantów strony w usłudze Azure API Management
Usługa Azure API Management zawiera następujące kontrolki do użycia w Deweloper szablonów portalu.  
  
Aby użyć kontrolki, umieść go w dowolnym miejscu w szablonie portalu dla deweloperów. Niektóre kontrolki, takie jak [akcje aplikacji](#app-actions) sterowania, mają parametry, jak pokazano w poniższym przykładzie:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Wartości parametrów są przekazywane w jako część modelu danych dla szablonu. W większości przypadków można po prostu wkleić w podanym przykładzie dla każdego formantu, aby mogła działać prawidłowo. Aby uzyskać więcej informacji na temat wartości parametrów zobacz temat sekcji modelu danych do każdego szablonu, w którym formant może być używany.  
  
 Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Formanty strony szablonu portalu dla deweloperów  
  
-   [Akcje aplikacji](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [dostawcy](#providers)  
-   [Kontrolka wyszukiwania](#search-control)  
-   [Zarejestruj się](#sign-up)  
-   [przycisk subskrypcji](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
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
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `app-actions` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Aplikacje](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> Basic — logowanie  
 `basic-signin` Kontroli udostępnia kontrolkę do pobierania użytkownika logowania na stronie logowania w portalu dla deweloperów.  
  
 ![podstawowe&#45;logowanie za pomocą kontrolki](./media/api-management-page-controls/APIM-basic-signin-control.png "kontroli basic — logowanie za pomocą usługi APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `basic-signin` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Rejestrowanie](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> Formant stronicowania  
 `paging-control` Zapewnia funkcje stronicowania na dewelopera na stronach portalu wyświetlające listy elementów.  
  
 ![stronicowanie kontroli](./media/api-management-page-controls/APIM-paging-control.png "formant stronicowania APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `paging-control` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Interfejs API — lista](api-management-api-templates.md#APIList)  
  
-   [Lista problemów](api-management-issue-templates.md#IssueList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> dostawcy  
 `providers` Kontroli udostępnia kontrolkę do wyboru dostawcy uwierzytelniania, na stronie logowania w portalu dla deweloperów.  
  
 ![Kontrolka Providers](./media/api-management-page-controls/APIM-providers-control.png "kontrolka providers APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `providers` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Rejestrowanie](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> Kontrolka wyszukiwania  
 `search-control` Zawiera funkcję wyszukiwania, po deweloperów stron portalu, wyświetlające listy elementów.  
  
 ![Wyszukaj kontrolki](./media/api-management-page-controls/APIM-search-control.png "kontrolka wyszukiwania usługi APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `search-control` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Interfejs API — lista](api-management-api-templates.md#APIList)  
  
-   [Lista produktów](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> Zarejestruj się  
 `sign-up` Kontroli udostępnia kontrolkę do zbierania informacji o profilu użytkownika na stronie rejestracji w portalu dla deweloperów.  
  
 ![znak&#45;Konfigurowanie kontrolki](./media/api-management-page-controls/APIM-sign-up-control.png "kontroli rejestracji usługi APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `sign-up` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Tworzenie konta](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> przycisk subskrypcji  
 `subscribe-button` Udostępnia kontrolkę do subskrypcji użytkownikowi produktu.  
  
 ![Subskrybuj&#45;kontrolka przycisku](./media/api-management-page-controls/APIM-subscribe-button-control.png "formant przycisku subskrybowania usługi APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametry  
 Brak.  
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `subscribe-button` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Produkt](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> Anuluj subskrypcję  
 `subscription-cancel` Kontroli udostępnia kontrolkę do anulowania subskrypcji do produktu na stronie profilu użytkownika w portalu dla deweloperów.  
  
 ![Subskrypcja&#45;Anuluj kontroli](./media/api-management-page-controls/APIM-subscription-cancel-control.png "kontroli Anuluj subskrypcję usługi APIM")  
  
### <a name="usage"></a>Sposób użycia  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametry  
  
|Parametr|Opis|  
|---------------|-----------------|  
|subscriptionId|Identyfikator subskrypcji, aby anulować.|  
|cancelUrl|Subskrypcja anuluje adresu URL.|  
  
### <a name="developer-portal-templates"></a>Szablonów portalu dla deweloperów  
 `subscription-cancel` Kontrolki mogą być używane w następujących szablonów portalu dla deweloperów:  
  
-   [Produkt](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).