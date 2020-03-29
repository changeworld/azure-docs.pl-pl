---
title: Dostosowywanie portalu deweloperów zarządzania interfejsami API przy użyciu szablonów
titleSuffix: Azure API Management
description: Dowiedz się, jak dostosować portal deweloperów usługi Azure API Management przy użyciu szablonów.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a8c348340be143f7059ce7e64a1c66b66074a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430787"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Jak dostosować portal deweloperski usługi Azure API Management przy użyciu szablonów

Istnieją trzy podstawowe sposoby dostosowywania portalu deweloperów w usłudze Azure API Management:

* [Edycja zawartości stron statycznych i elementów układu strony][modify-content-layout]
* [Aktualizacja stylów używanych dla elementów strony w portalu deweloperów][customize-styles]
* [Modyfikowanie szablonów używanych dla stron generowanych przez portal][portal-templates] (wyjaśniono w tym przewodniku)

Szablony służą do dostosowywania zawartości stron portalu deweloperów generowanych przez system (na przykład dokumentów interfejsu API, produktów, uwierzytelniania użytkowników itp.). Za pomocą [dotLiquid](http://dotliquidmarkup.org/) składni i pod warunkiem, zestaw zlokalizowanych zasobów ciągów, ikony i formanty strony, masz dużą elastyczność, aby skonfigurować zawartość stron, jak chcesz.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Omówienie szablonów szablonów portalu dla deweloperów

Edytowanie szablonów odbywa się z **portalu dewelopera,** gdy jest zalogowany jako administrator. Aby się tam dostać, najpierw otwórz portal platformy Azure i kliknij portal **deweloperów** z paska narzędzi usługi wystąpienia usługi API Management.

Aby uzyskać dostęp do szablonów portali deweloperów, kliknij ikonę dostosuj po lewej stronie, aby wyświetlić menu dostosowywania, a następnie kliknij pozycję **Szablony**.

![Szablony portali deweloperów][api-management-customize-menu]

Na liście szablonów jest wyświetlanych kilka kategorii szablonów obejmujących różne strony w portalu dla deweloperów. Każdy szablon jest inny, ale kroki, aby je edytować i opublikować zmiany są takie same. Aby edytować szablon, kliknij jego nazwę.

![Szablony portali deweloperów][api-management-templates-menu]

Kliknięcie szablonu prowadzi do strony portalu dewelopera, która można dostosować za pomocą tego szablonu. W tym przykładzie zostanie wyświetlony szablon **listy produktów.** **Szablon listy produktów** steruje obszarem ekranu wskazywanym przez czerwony prostokąt.

![Szablon listy produktów][api-management-developer-portal-templates-overview]

Niektóre szablony, takie jak szablony **profilu użytkownika,** dostosowują różne części tej samej strony.

![Szablony profilów użytkowników][api-management-user-profile-templates]

Edytor dla każdego szablonu portalu dla deweloperów ma dwie sekcje wyświetlane u dołu strony. Po lewej stronie jest wyświetlane okienko edycji szablonu, a po prawej stronie model danych szablonu.

Okienko edycji szablonu zawiera znaczniki, które sterują wyglądem i zachowaniem odpowiedniej strony w portalu dla deweloperów. Znaczników w szablonie używa [dotLiquid](http://dotliquidmarkup.org/) składni. Jeden popularny edytor dla DotLiquid jest [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Wszelkie zmiany wprowadzone w szablonie podczas edycji są wyświetlane w czasie rzeczywistym w przeglądarce, ale nie są widoczne dla klientów, dopóki nie [zapiszesz](#to-save-a-template) i nie [opublikujesz](#to-publish-a-template) szablonu.

![Znaczniki szablonu][api-management-template]

Okienko **danych Szablon** zawiera przewodnik po modelu danych dla jednostek, które są dostępne do użycia w określonym szablonie. Zawiera ten przewodnik, wyświetlając dane na żywo, które są obecnie wyświetlane w portalu dla deweloperów. Okienka szablonów można rozwinąć, klikając prostokąt w prawym górnym rogu okienka **danych Szablon.**

![Model danych szablonu][api-management-template-data]

W poprzednim przykładzie istnieją dwa produkty wyświetlane w portalu dla deweloperów, które zostały pobrane z danych wyświetlanych w okienku **danych szablonu,** jak pokazano w poniższym przykładzie:

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
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
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

Znaczników w **szablonie listy produktów** przetwarza dane, aby zapewnić żądane dane wyjściowe przez iteracji za pośrednictwem kolekcji produktów do wyświetlania informacji i łącze do każdego produktu. Zanotuj `<search-control>` elementy i `<page-control>` w znacznikach. Sterują one wyświetlaniem formantów wyszukiwania i stronicowania na stronie. `ProductsStrings|PageTitleProducts`jest zlokalizowanym odwołaniem do `h2` ciągu, które zawiera tekst nagłówka strony. Aby uzyskać listę zasobów ciągów, formantów stron i ikon dostępnych do użycia w szablonach portali deweloperów, zobacz [Odwołanie do szablonów portali dla deweloperów usług API Management](api-management-developer-portal-templates-reference.md).

```html
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

## <a name="to-save-a-template"></a>Aby zapisać szablon
Aby zapisać szablon, kliknij przycisk Zapisz w edytorze szablonów.

![Zapisywanie szablonu][api-management-save-template]

Zapisane zmiany nie są aktywne w portalu dla deweloperów, dopóki nie zostaną opublikowane.

## <a name="to-publish-a-template"></a>Aby opublikować szablon
Zapisane szablony mogą być publikowane pojedynczo lub wszystkie razem. Aby opublikować pojedynczy szablon, kliknij pozycję Publikuj w edytorze szablonów.

![Publikowanie szablonu][api-management-publish-template]

Kliknij **przycisk Tak,** aby potwierdzić i udostępnić szablon w portalu dla deweloperów.

![Potwierdź publikację][api-management-publish-template-confirm]

Aby opublikować wszystkie aktualnie nieopublikowane wersje szablonów, kliknij pozycję **Publikuj** na liście szablonów. Nieopublikowane szablony są oznaczone gwiazdką po nazwie szablonu. W tym przykładzie **lista produktów** i **szablony produktów** są publikowane.

![Publikowanie szablonów][api-management-publish-templates]

Kliknij **pozycję Opublikuj dostosowania,** aby potwierdzić.

![Potwierdź publikację][api-management-publish-customizations]

Nowo opublikowane szablony wchodzą w życie natychmiast w portalu dla deweloperów.

## <a name="to-revert-a-template-to-the-previous-version"></a>Aby przywrócić szablon do poprzedniej wersji
Aby przywrócić szablon do poprzedniej opublikowanej wersji, kliknij przycisk Przywróć w edytorze szablonów.

![Przywróć szablon][api-management-revert-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Confirm][api-management-revert-template-confirm]

Poprzednio opublikowana wersja szablonu jest dostępna w portalu dewelopera po zakończeniu operacji powrotu.

## <a name="to-restore-a-template-to-the-default-version"></a>Aby przywrócić szablon do wersji domyślnej
Przywracanie szablonów do ich domyślnej wersji jest procesem dwuetapowym. Najpierw szablony muszą zostać przywrócone, a następnie przywrócone wersje muszą zostać opublikowane.

Aby przywrócić pojedynczy szablon do wersji domyślnej, kliknij przycisk przywróć w edytorze szablonów.

![Przywróć szablon][api-management-reset-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Confirm][api-management-reset-template-confirm]

Aby przywrócić wszystkie szablony do ich wersji domyślnych, kliknij pozycję **Przywróć szablony domyślne** na liście szablonów.

![Przywracanie szablonów][api-management-restore-templates]

Przywrócone szablony muszą być następnie publikowane indywidualnie lub wszystkie naraz, wykonując kroki opisane w [aby opublikować szablon](#to-publish-a-template).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać informacje referencyjne dotyczące szablonów portali deweloperów, zasobów ciągów, ikon i formantów stron, zobacz [Odwołanie do szablonów szablonów portali deweloperów usługi API Management](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
