---
title: Dostosowywanie portalu deweloperów usługi API Management przy użyciu szablonów — Azure | Dokumentacja firmy Microsoft
description: Informacje o sposobie dostosowywania portalu deweloperów usługi Azure API Management przy użyciu szablonów.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 00d5e3df78e85d19a519786dad1a1b176ad7fa08
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837235"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Dostosowywanie portalu dla deweloperów usługi Azure API Management przy użyciu szablonów

Istnieją trzy podstawowe sposoby dostosowywania portalu deweloperów w usłudze Azure API Management:

* [Edycja zawartości stron statycznych i elementów układu strony][modify-content-layout]
* [Aktualizacja stylów używanych dla elementów strony w portalu deweloperów][customize-styles]
* [Modyfikowanie szablonów używanych dla stron wygenerowanych przez portal] [ portal-templates] (opisana w tym przewodniku)

Szablony są używane, aby dostosować zawartość generowanych przez system portalu dla deweloperów (na przykład dokumentacji interfejsu API, produktów, uwierzytelniania użytkowników itp.). Za pomocą [DotLiquid](http://dotliquidmarkup.org/) składni i podany zbiór zasobów zlokalizowanych ciągów, ikon i formantów strony masz dużą elastyczność konfigurowania zawartości stron, zgodnie z potrzebami.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Omówienie szablonów portalu dla deweloperów

Edytowanie szablonów odbywa się z **portalu dla deweloperów** przy użyciu konta administratora. Do niego przejść, otwórz Azure Portal, a następnie kliknij przycisk **portalu dla deweloperów** na pasku narzędzi wystąpienia usługi API Management.

Aby uzyskać dostęp do szablonów portalu dla deweloperów, kliknij ikonę Dostosuj po lewej stronie, aby wyświetlić menu dostosowywania, a następnie kliknij przycisk **szablony**.

![Szablonów portalu dla deweloperów][api-management-customize-menu]

Na liście szablony zostaną wyświetlone różne kategorie szablonów obejmujących różne strony w portalu dla deweloperów. Każdy szablon jest inna, ale kroki, aby je edytować, a następnie opublikować zmiany są takie same. Aby edytować szablon, kliknij nazwę szablonu.

![Szablonów portalu dla deweloperów][api-management-templates-menu]

Kliknij szablon spowoduje przejście do strony portalu dla deweloperów, który można dostosować za pomocą tego szablonu. W tym przykładzie **lista produktów** szablon jest wyświetlany. **Lista produktów** szablon określa obszar ekranu wskazywanym przez czerwony prostokąt.

![Szablon listy produktów][api-management-developer-portal-templates-overview]

Niektóre szablony, takie jak **profilu użytkownika** szablonów, dostosować różnych części tej samej stronie.

![Szablony profilu użytkownika][api-management-user-profile-templates]

Edytor dla każdego szablonu portalu dla deweloperów zawiera dwie sekcje wyświetlane u dołu strony. Lewa strona wyświetla okienka edycji szablonu, a po prawej stronie zawiera model danych dla szablonu.

Szablon do edycji okienko zawiera kod znaczników, który kontroluje wygląd i zachowanie odpowiadającej mu stronie w portalu dla deweloperów. Używa znaczników w szablonie [DotLiquid](http://dotliquidmarkup.org/) składni. Jeden z popularnych edytorem DotLiquid jest [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Wszelkie zmiany wprowadzone podczas edycji szablonu są wyświetlane w czasie rzeczywistym w przeglądarce, ale nie są widoczne dla klientów do momentu [Zapisz](#to-save-a-template) i [publikowania](#to-publish-a-template) szablonu.

![Oznaczenia szablonu][api-management-template]

**Danych szablonu** okienko zawiera przewodnik do modelu danych jednostek, które są dostępne do użycia w określonym szablonie. Ten przewodnik zapewnia za pomocą wyświetlania danych na żywo, które są aktualnie wyświetlane w portalu dla deweloperów. Można rozwinąć okienka szablon, klikając pozycję prostokąt w prawym górnym rogu **danych szablonu** okienka.

![Szablon modelu danych][api-management-template-data]

W poprzednim przykładzie, istnieją dwa produkty wyświetlane w portalu dla deweloperów, które zostały pobrane z danym wyświetlanym w **danych szablonu** okienko, jak pokazano w poniższym przykładzie:

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

Kod znaczników w **lista produktów** szablonu przetwarza dane w celu dostarczania żądanego wyniku, iteracji w kolekcji produktów, aby wyświetlić informacje i łącza do każdego produktu. Uwaga `<search-control>` i `<page-control>` elementy w znaczniku. Te kontrolują wyświetlanie wyszukiwanie i stronicowanie formantów na stronie. `ProductsStrings|PageTitleProducts` jest to odwołanie zlokalizowany ciąg, który zawiera `h2` tekst nagłówka dla strony. Aby uzyskać listę zasobów ciągów, formantów strony i ikon, dostępny do użytku w szablonów portalu dla deweloperów, zobacz [dokumentacja szablonów portalu dla deweloperów usługi API Management](api-management-developer-portal-templates-reference.md).

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
Aby zapisać szablon, kliknij przycisk Zapisz w edytorze szablonu.

![Zapisywanie szablonu][api-management-save-template]

Zapisano zmiany nie są na żywo w portalu dla deweloperów, dopóki nie zostaną opublikowane.

## <a name="to-publish-a-template"></a>Aby opublikować szablon
Zapisane szablony można publikować indywidualnie lub wszystko ze sobą. Aby opublikować szablon osobno, kliknij przycisk Publikuj w edytorze szablonu.

![Publikowanie szablonu][api-management-publish-template]

Kliknij przycisk **tak** aby potwierdzić, a następnie udostępnić szablon na żywo w portalu dla deweloperów.

![Upewnij się, Publikuj][api-management-publish-template-confirm]

Aby opublikować wszystkie wersje obecnie nieopublikowane szablonu, kliknij przycisk **Publikuj** z listy szablonów. Cofnięto publikowanie szablonów są oznaczane gwiazdką po nazwie szablonu. W tym przykładzie **lista produktów** i **produktu** szablony są publikowane.

![Publikowanie szablonów][api-management-publish-templates]

Kliknij przycisk **Publikuj dostosowania** o potwierdzenie.

![Upewnij się, Publikuj][api-management-publish-customizations]

Nowo opublikowana szablonów zaczynają obowiązywać natychmiast w portalu dla deweloperów.

## <a name="to-revert-a-template-to-the-previous-version"></a>Aby szablon z poprzednią wersją
Aby szablon do poprzedniej wersji opublikowanej, kliknij przycisk Przywróć w edytorze szablonu.

![Przywróć szablonu][api-management-revert-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Potwierdź][api-management-revert-template-confirm]

Poprzednio opublikowanej wersji szablonu jest aktywna w portalu dla deweloperów, po zakończeniu operacji przywracania.

## <a name="to-restore-a-template-to-the-default-version"></a>Aby przywrócić domyślną wersję szablonu
Przywracanie szablonów do ich wersja domyślna jest procesem dwuetapowym. Najpierw musi zostać przywrócona szablonów i przywrócony wersji musi być następnie publikowane.

Aby przywrócić jednego szablonu do domyślnej wersji, kliknij przycisk przywracania w edytorze szablonu.

![Przywróć szablonu][api-management-reset-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Potwierdź][api-management-reset-template-confirm]

Aby przywrócić wszystkie szablony do ich wersji domyślnej, kliknij przycisk **Przywracanie domyślnych szablonów** na liście szablonów.

![Przywróć szablonów][api-management-restore-templates]

Szablony przywróconej musi być następnie publikowane indywidualnie lub w całości wykonując kroki opisane w [publikowania szablonu](#to-publish-a-template).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje referencyjne dotyczące szablonów portalu dla deweloperów, zasoby w postaci ciągów, ikon i formantów strony, zobacz [dokumentacja szablonów portalu dla deweloperów usługi API Management](api-management-developer-portal-templates-reference.md).

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
