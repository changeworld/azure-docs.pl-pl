---
title: Szablony aplikacji w usłudze Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak dostosować zawartość stron aplikacji w portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: d635950c8b34986cd5824660166017317948cbd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73176914"
---
# <a name="application-templates-in-azure-api-management"></a>Szablony aplikacji w usłudze Azure API Management
Usługa Azure API Management umożliwia dostosowanie zawartości stron portalu deweloperów przy użyciu zestawu szablonów, które konfigurują ich zawartość. Za pomocą [dotLiquid](http://dotliquidmarkup.org/) składni i edytora do wyboru, takich jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i pod warunkiem, zestaw zlokalizowanych [zasobów ciąg,](api-management-template-resources.md#strings) [zasoby glifów](api-management-template-resources.md#glyphs)i [page formantów,](api-management-page-controls.md)masz dużą elastyczność, aby skonfigurować zawartość stron, jak można uznać za stosowne przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartości stron aplikacji w portalu dla deweloperów.  
  
-   [Lista aplikacji](#ProductList)  
  
-   [Aplikacja](#Application)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są zawarte w poniższej dokumentacji, ale mogą ulec zmianie z powodu ciągłych ulepszeń. Domyślne szablony na żywo można wyświetlić w portalu dla deweloperów, przechodząc do żądanych szablonów indywidualnych. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="application-list"></a><a name="ProductList"></a>Lista aplikacji  
 **Szablon listy aplikacji** umożliwia dostosowanie treści strony listy aplikacji w portalu dla deweloperów.  
  
 ![Szablony portalu dla deweloperów listy aplikacji](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "Szablony portalu dla deweloperów listy aplikacji APIM")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
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
  
### <a name="controls"></a>Formanty  
 Szablon `Product list` może używać [kontrolek](api-management-page-controls.md)strony poniżej .  
  
-   [sterowanie stronicowania](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Paging`|[Jednostka stronicowania.](api-management-template-data-model-reference.md#Paging)|Informacje stronicowania dla kolekcji aplikacji.|  
|`Applications`|Kolekcja jednostek [aplikacji.](api-management-template-data-model-reference.md#Application)|Aplikacje widoczne dla bieżącego użytkownika.|  
|`CategoryName`|ciąg|Kategoria zastosowania.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="application"></a><a name="Application"></a>Aplikacji  
 Szablon **aplikacji** umożliwia dostosowanie treści strony aplikacji w portalu dewelopera.  
  
 ![Szablony portalu deweloperów strony aplikacji](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "Szablony portalu dla deweloperów strony aplikacji APIM")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>Formanty  
 Szablon `Application` nie zezwala na używanie żadnych [formantów strony](api-management-page-controls.md).  
  
### <a name="data-model"></a>Model danych  
 [Jednostki aplikacji.](api-management-template-data-model-reference.md#Application)  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Jak dostosować portal dewelopera zarządzania interfejsami API przy użyciu szablonów](api-management-developer-portal-templates.md).
