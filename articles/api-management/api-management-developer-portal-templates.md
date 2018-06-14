---
title: Dostosowywanie portalu dla deweloperów interfejsu API zarządzania za pomocą szablonów-Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostosować portalu dla deweloperów usługi Azure API Management za pomocą szablonów.
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
ms.openlocfilehash: 2bdb8c30ffa630f85d666f3a16ce2b9dcdab6492
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29118228"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Dostosowywanie portalu dla deweloperów usługi Azure API Management za pomocą szablonów

Istnieją trzy podstawowe sposoby dostosowywania portalu deweloperów w usłudze Azure API Management:

* [Edycja zawartości stron statycznych i elementów układu strony][modify-content-layout]
* [Aktualizacja stylów używanych dla elementów strony w portalu deweloperów][customize-styles]
* [Modyfikowanie szablonów używany dla stron wygenerowanych przez portal] [ portal-templates] (co omówiono w tym przewodniku)

Szablony umożliwiają dostosować zawartość strony portalu deweloperów generowanych przez system (na przykład dokumentacja interfejsu API, produktów, uwierzytelnianie użytkowników, itp.). Przy użyciu [DotLiquid](http://dotliquidmarkup.org/) składni i podany zestaw zasobów zlokalizowanego ciągu, ikony i formantów strony ma dużą elastyczność konfigurowania zawartości stron, zgodnie z własnymi potrzebami.

## <a name="developer-portal-templates-overview"></a>Przegląd szablonów portalu deweloperów
Edytowanie szablonów jest wykonywane od **portalu dla deweloperów** podczas jest zalogowany jako administrator. Do uzyskania najpierw otwórz Azure Portal, a następnie kliknij przycisk **portalu dla deweloperów** na pasku narzędzi usługi Zarządzanie interfejsami API wystąpienia.

Aby uzyskać dostęp do szablonów portalu deweloperów, kliknij ikonę Dostosuj lewej strony, aby wyświetlić menu dostosowania, a następnie kliknij przycisk **szablony**.

![Szablony portalu dla deweloperów][api-management-customize-menu]

Na liście szablony zostaną wyświetlone różne kategorie szablonów obejmujących różne strony w portalu dla deweloperów. Każdy szablon jest inny, ale kroki, aby edytować je oraz opublikować zmiany są takie same. Aby edytować szablony, kliknij nazwę szablonu.

![Szablony portalu dla deweloperów][api-management-templates-menu]

Kliknięcie przycisku szablon umożliwia przejście do strony portalu dewelopera, który można dostosować za pomocą tego szablonu. W tym przykładzie **listę produktów** szablonu jest wyświetlany. **Listę produktów** szablon określa obszar ekranu wskazywanym przez czerwonym prostokątem. 

![Szablon listy produktów][api-management-developer-portal-templates-overview]

Niektóre szablony, takie jak **profilu użytkownika** szablony, dostosować różnych części strony. 

![Szablony profilów użytkownika][api-management-user-profile-templates]

Edytor dla każdego szablonu portalu dewelopera ma dwie sekcje wyświetlane u dołu strony. Lewa strona wyświetla okienku edycji szablonu oraz po prawej stronie modelu danych dla szablonu. 

Szablon edycji okienko zawiera kod znaczników, który określa wygląd i zachowanie odpowiedniej strony w portalu dla deweloperów. Używa znacznika w szablonie [DotLiquid](http://dotliquidmarkup.org/) składni. Jeden popularnych edytorem DotLiquid jest [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Wszelkie zmiany wprowadzone podczas edycji szablonu są wyświetlane w czasie rzeczywistym w przeglądarce, ale nie są widoczne dla klientów dopóki [zapisać](#to-save-a-template) i [publikowania](#to-publish-a-template) szablonu.

![Oznaczenia szablonu][api-management-template]

**Dane szablonu** okienko zapewnia przewodnik do modelu danych jednostki, które są dostępne do użycia w określonym szablonie. Ten przewodnik zapewnia wyświetlając dane na żywo, które są aktualnie wyświetlane w portalu dla deweloperów. Rozwiń węzeł okienka szablonu, klikając w prawym górnym rogu prostokąta **dane szablonu** okienka.

![Model danych szablonu][api-management-template-data]

W poprzednim przykładzie są wyświetlane w portalu dla deweloperów dwóch produktów, które zostały pobrane z danych wyświetlanych w **dane szablonu** okienka, jak pokazano w poniższym przykładzie:

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

Znaczniki w **listę produktów** szablonu przetwarza dane zapewnienie żądanego wyniku iteracji w kolekcji produktów, aby wyświetlić informacje i łącza do każdego indywidualnych produktów. Uwaga `<search-control>` i `<page-control>` elementy w znaczniku. Te sterowania wyświetlaniem wyszukiwania i stronicowania kontrolki na stronie. `ProductsStrings|PageTitleProducts`to odwołanie do zlokalizowanego ciągu, które zawiera `h2` tekst nagłówka strony. Lista zasobów ciągu, formantów strony i ikony dostępne do użycia w szablonach portalu dla deweloperów, [szablony portalu dewelopera zarządzanie interfejsami API](api-management-developer-portal-templates-reference.md).

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
Zapisane szablony mogą być publikowane pojedynczo, lub wszystkich elementów. Aby opublikować szablon osobno, kliknij przycisk Publikuj w edytorze szablonu.

![Publikowanie szablonu][api-management-publish-template]

Kliknij przycisk **tak** potwierdzenie i wybrać ustawienie szablonu powodujące na żywo w portalu dla deweloperów.

![Potwierdź publikowania][api-management-publish-template-confirm]

Aby opublikować wszystkie wersje obecnie nieopublikowane szablonu, kliknij polecenie **publikowania** na liście szablonów. Nieopublikowane szablony są oznaczane gwiazdką po nazwie szablonu. W tym przykładzie **listę produktów** i **produktu** szablony zostały opublikowane.

![Publikowanie szablonów][api-management-publish-templates]

Kliknij przycisk **opublikować dostosowania** o potwierdzenie.

![Potwierdź publikowania][api-management-publish-customizations]

Nowo opublikowana szablonów zaczynają obowiązywać natychmiast w portalu dla deweloperów.

## <a name="to-revert-a-template-to-the-previous-version"></a>Aby przywrócić poprzednią wersję szablonu
Aby przywrócić szablonu do poprzednich wersji opublikowanej, kliknij przycisk Przywróć w edytorze szablonu.

![Przywróć szablonu][api-management-revert-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Potwierdź][api-management-revert-template-confirm]

Poprzednio opublikowanej wersji szablonu jest na żywo w portalu dla deweloperów, po zakończeniu operacji przywracania.

## <a name="to-restore-a-template-to-the-default-version"></a>Aby przywrócić szablon to wersja domyślna
Przywracanie szablonów do ich domyślna wersja jest procesem dwuetapowym. Najpierw musi zostać przywrócona szablonów, a następnie przywrócone wersji musi zostać opublikowany.

Aby przywrócić jednego szablonu do domyślnej wersji, kliknij przycisk Przywróć w edytorze szablonu.

![Przywróć szablonu][api-management-reset-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Potwierdź][api-management-reset-template-confirm]

Aby przywrócić wszystkie szablony ich wersje domyślny, kliknij przycisk **Przywróć domyślne szablony** na liście szablonów.

![Przywracanie szablonów][api-management-restore-templates]

Szablonach przywróconą następnie należy opublikować pojedynczo lub w całości, wykonując kroki opisane w [publikowania szablonu](#to-publish-a-template).

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje referencyjne dotyczące szablonów portalu dla deweloperów, zasoby ciągów, ikony i formantów strony, zobacz [szablony portalu dewelopera zarządzanie interfejsami API](api-management-developer-portal-templates-reference.md).

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







