---
title: Szablony produktów w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować zawartość stron produktu w portalu dla deweloperów usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 14090e21fb7c6ca07fe63220ffd1d44d483ac869
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61088192"
---
# <a name="product-templates-in-azure-api-management"></a>Szablony produktów w usłudze Azure API Management

Usługa Azure API Management zapewnia możliwość dostosować zawartość portalu dla deweloperów przy użyciu zestawu szablonów, które ich zawartość. Przy użyciu [DotLiquid](http://dotliquidmarkup.org/) składni i Edytor wybranych przez użytkownika, takich jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i zlokalizowanych podany zbiór [zasoby ciągu](api-management-template-resources.md#strings), [symbol zasoby](api-management-template-resources.md#glyphs), i [stronie kontrolki](api-management-page-controls.md), masz dużą elastyczność konfigurowania zawartości stron, zgodnie z potrzebami przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartości stron produktu w portalu dla deweloperów.  
  
-   [Lista produktów](#ProductList)  
  
-   [Produkt](#Product)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są uwzględnione w poniższej dokumentacji, ale mogą ulec zmianie ze względu na stałe udoskonalenia. Szablony domyślne na żywo można wyświetlić w portalu dla deweloperów, przechodząc do żądanego szablony osobno. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> Lista produktów  
 **Lista produktów** szablon umożliwia dostosowanie treść strony listy produktów w portalu dla deweloperów.  
  
 ![Lista produktów](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<search-control></search-control>  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if products.size > 0 %}  
    <ul class="list-unstyled">  
    {% for product in products %}  
        <li>  
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>  
            {{product.description}}  
        </li>     
    {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>Kontrolki  
 `Product list` Szablonu może korzystać z następujących [stronie kontrolki](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
-   [Kontrolka wyszukiwania](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Paging|[Stronicowanie](api-management-template-data-model-reference.md#Paging) jednostki.|Informacje o stronicowania dla kolekcji produktów.|  
|Filtering|[Filtrowanie](api-management-template-data-model-reference.md#Filtering) jednostki.|Filtrowanie informacji dla strony listy produktów.|  
|Products|Kolekcja [produktu](api-management-template-data-model-reference.md#Product) jednostek.|Produkty widoczne dla bieżącego użytkownika.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 2,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Filtering": {  
        "Pattern": null,  
        "Placeholder": "Search products"  
    },  
    "Products": [  
        {  
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
            "Title": "Unlimited",  
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",  
            "Terms": null,  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        }  
    ]  
}  
```  
  
##  <a name="Product"></a> Produkt  
 **Produktu** szablon umożliwia dostosowanie treści na stronie produktu w portalu dla deweloperów.  
  
 ![Strona produktu w portalu dla deweloperów](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Kontrolki  
 `Product list` Szablonu może korzystać z następujących [stronie kontrolki](api-management-page-controls.md).  
  
-   [przycisk subskrypcji](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|Product (Produkt)|[Produkt](api-management-template-data-model-reference.md#Product)|Określony produkt.|  
|IsDeveloperSubscribed|wartość logiczna|Czy bieżący użytkownik jest subskrybentem tego produktu.|  
|SubscriptionState|numer|Stan subskrypcji. Możliwe stany to:<br /><br /> -   `0 - suspended` — subskrypcji jest zablokowane i subskrybenta nie można wywołać dowolnych interfejsów API produktu.<br />-   `1 - active` — Subskrypcja jest aktywna.<br />-   `2 - expired` — Subskrypcja osiągnęła daty wygaśnięcia i zostało zdezaktywowane.<br />-   `3 - submitted` — Żądanie subskrypcji przez deweloperów, ale ma nie została jeszcze zatwierdzeniu lub odrzuceniu.<br />-   `4 - rejected` — Żądanie subskrypcji zostało odrzucone przez administratora.<br />-   `5 - cancelled` — Subskrypcja została anulowana przez dewelopera lub administratora.|  
|Limits|tablica|Ta właściwość jest przestarzała i nie powinna być używana.|  
|DelegatedSubscriptionEnabled|wartość logiczna|Czy [delegowania](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) jest włączona dla tej subskrypcji.|  
|DelegatedSubscriptionUrl|ciąg|Jeśli delegowanie jest włączone, adres URL delegowanego subskrypcji.|  
|IsAgreed|wartość logiczna|Jeśli produkt zawiera warunki, czy bieżący użytkownik zgodził się warunki.|  
|Subscriptions|Kolekcja [Podsumowanie subskrypcji](api-management-template-data-model-reference.md#SubscriptionSummary) jednostek.|Subskrypcje do produktu.|  
|Apis|Kolekcja [API](api-management-template-data-model-reference.md#API) jednostek.|Interfejsy API, w tym produkcie.|  
|CannotAddBecauseSubscriptionNumberLimitReached|wartość logiczna|Czy bieżący użytkownik jest uprawniona do subskrybowania tego produktu w odniesieniu do limit subskrypcji.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|wartość logiczna|Czy bieżący użytkownik jest uprawniona do subskrybowania tego produktu w odniesieniu do wielu subskrypcji, które są dozwolone lub nie.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).