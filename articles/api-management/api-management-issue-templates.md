---
title: Wystawiać szablonów w usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować zawartość strony problem w portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c70e88a572bc261ad9d25f9a742b1987d0c19b44
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656717"
---
# <a name="issue-templates-in-azure-api-management"></a>Szablony problem w usłudze Azure API Management
Usługa Azure API Management zapewnia możliwość dostosować zawartość portalu dla deweloperów przy użyciu zestawu szablonów, które ich zawartość. Przy użyciu [DotLiquid](http://dotliquidmarkup.org/) składni i Edytor wybranych przez użytkownika, takich jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i zlokalizowanych podany zbiór [zasoby ciągu](api-management-template-resources.md#strings), [symbol zasoby](api-management-template-resources.md#glyphs), i [stronie kontrolki](api-management-page-controls.md), masz dużą elastyczność konfigurowania zawartości stron, zgodnie z potrzebami przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartości stron problem w portalu dla deweloperów.  
  
-   [Lista problemów](#IssueList)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są uwzględnione w poniższej dokumentacji, ale mogą ulec zmianie ze względu na stałe udoskonalenia. Szablony domyślne na żywo można wyświetlić w portalu dla deweloperów, przechodząc do żądanego szablony osobno. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a> Lista problemów  
 **Lista problemów** szablon umożliwia dostosowanie treści na stronie listy problem w portalu dla deweloperów.  
  
 ![Portalu dla deweloperów listy wystawiania](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "portalu dla deweloperów systemu usługi APIM, oznacza problem z listy")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Kontrolki  
 `Issue list` Szablonu może korzystać z następujących [stronie kontrolki](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Typ|Opis|  
|--------------|----------|-----------------|  
|`Issues`|Kolekcja [problem](api-management-template-data-model-reference.md#Issue) jednostek.|Problemy widoczne dla bieżącego użytkownika.|  
|`Paging`|[Stronicowanie](api-management-template-data-model-reference.md#Paging) jednostki.|Informacje o stronicowania dla kolekcji.|  
|`IsAuthenticated`|wartość logiczna|Czy bieżący użytkownik jest zalogowany do portalu dla deweloperów.|  
|`CanReportIssues`|wartość logiczna|Czy bieżący użytkownik ma uprawnienia do pliku wystąpił problem.|  
|`Search`|string|Ta właściwość jest przestarzała i nie powinna być używana.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Dostosowywanie portalu dla deweloperów usługi API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
