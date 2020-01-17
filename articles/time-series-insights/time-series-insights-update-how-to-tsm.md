---
title: Modelowanie danych w środowiskach w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się więcej na temat modelowania danych w wersji zapoznawczej Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/20/2019
ms.custom: seodec18
ms.openlocfilehash: 591233154ab79b9493d77030727462bae68ea7d5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152361"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelowanie danych w wersji zapoznawczej Azure Time Series Insights

W tym artykule opisano sposób pracy z modelem szeregów czasowych w wersji zapoznawczej Azure Time Series Insights. Zawiera szczegółowe informacje o kilku typowych scenariuszach dotyczących danych.

> [!TIP]
> * Przeczytaj o [modelu szeregów czasowych](time-series-insights-update-tsm.md)w wersji zapoznawczej.
> * Dowiedz się więcej na temat nawigowania po interfejsie użytkownika w wersji zapoznawczej w [eksploratorze Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Wystąpienia

Eksplorator Azure Time Series Insights obsługuje operacje **tworzenia**, **odczytywania**, **aktualizowania**i **usuwania** wystąpień w przeglądarce. 

Aby rozpocząć, wybierz widok **modelu** w widoku **analizy** Time Series Insights Explorer.

### <a name="create-a-single-instance"></a>Tworzenie pojedynczego wystąpienia

1. Przejdź do panelu selektora modelu szeregów czasowych, a następnie wybierz pozycję **wystąpienia** z menu. Zostaną wyświetlone wszystkie wystąpienia skojarzone z wybranym środowiskiem Time Series Insights.

    [![utworzyć pojedyncze wystąpienie, zaznaczając najpierw wystąpienia.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![dodać wystąpienie, wybierając przycisk + Dodaj.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Wprowadź szczegóły wystąpienia, wybierz pozycję typ i skojarzenie hierarchii, a następnie wybierz pozycję **Utwórz**.

### <a name="bulk-upload-one-or-more-instances"></a>Przekaż zbiorczo co najmniej jedno wystąpienie

> [!TIP]
> Twoje wystąpienia można zapisać na pulpicie w formacie JSON. Pobrany plik JSON można następnie przekazać, wykonując poniższe kroki.

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek wystąpień.

    [![zbiorczego przekazywania wystąpień za poorednictwem JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Wybierz pozycję **Przekaż**.

### <a name="edit-a-single-instance"></a>Edytuj pojedyncze wystąpienie

1. Zaznacz wystąpienie i wybierz ikonę **Edytuj** lub **ołówka**. 
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![edytować pojedyncze wystąpienie.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Usuwanie wystąpienia

1. Wybierz wystąpienie, a następnie wybierz ikonę **Usuń** lub **odlotowy pojemnik**.

   [![usunąć wystąpienie, wybierając pozycję Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

> [!NOTE]
> Wystąpienie musi pomyślnie przekazać sprawdzenie poprawności pola, które ma zostać usunięte.

## <a name="hierarchies"></a>Hierarchie

Eksplorator Azure Time Series Insights obsługuje operacje **tworzenia**, **odczytywania**, **aktualizowania**i **usuwania** hierarchii w przeglądarce. 

Aby rozpocząć, wybierz widok **modelu** w widoku **analizy** Time Series Insights Explorer.

### <a name="create-a-single-hierarchy"></a>Utwórz pojedynczą hierarchię

1. Przejdź do panelu selektora modelu szeregów czasowych, a następnie wybierz **hierarchie** z menu. Zostaną wyświetlone wszystkie hierarchie skojarzone z wybranym środowiskiem Time Series Insights.

    [![utworzyć hierarchię za pomocą okienka.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![hierarchii + Dodaj przycisk.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Wybierz pozycję **+ Dodaj poziom** w okienku po prawej stronie.

    [![dodać poziom do hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Wprowadź szczegóły hierarchii i wybierz pozycję **Zapisz**.

    [![określić szczegóły hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Przekaż zbiorczo co najmniej jedną hierarchię

> [!TIP]
> Hierarchie można zapisać na pulpicie w formacie JSON. Pobrany plik JSON można następnie przekazać, wykonując poniższe kroki.

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek hierarchii.
1. Wybierz pozycję **Przekaż**.

    [![wybrane do zbiorczego przekazywania hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Edytowanie pojedynczej hierarchii

1. Wybierz hierarchię i wybierz ikonę **Edytuj** lub **ołówka**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![wybranych opcji edytowania pojedynczej hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Usuwanie hierarchii

1. Wybierz hierarchię, a następnie wybierz ikonę **Usuń** lub **odpady z pojemników**. 

    [![usunąć hierarchii, wybierając przycisk Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

## <a name="types"></a>Typy

Eksplorator Azure Time Series Insights obsługuje operacje **tworzenia**, **odczytu**, **aktualizacji**i **usuwania** w przeglądarce. 

Aby rozpocząć, wybierz widok **modelu** w widoku **analizy** Time Series Insights Explorer.

### <a name="create-a-single-type"></a>Tworzenie pojedynczego typu

1. Przejdź do panelu selektora modelu szeregów czasowych, a następnie wybierz pozycję **typy** z menu. Zostaną wyświetlone wszystkie typy skojarzone z wybranym środowiskiem Time Series Insights.

    [![okienku typy modeli szeregów czasowych.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj** , aby wyświetlić modalne **Dodawanie nowego typu** .
1. Wprowadź właściwości i zmienne dla typu. Po wprowadzeniu wybierz pozycję **Zapisz**. 

    [![ustawienia konfiguracji, aby dodać typ.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Przekaż zbiorczo jeden lub więcej typów

> [!TIP]
> Możesz zapisać swoje typy na pulpicie w formacie JSON. Pobrany plik JSON można następnie przekazać, wykonując poniższe kroki.

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek typu.
1. Wybierz pozycję **Przekaż**.

    [![typów zbiorczych przekazywania opcji.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Edytuj pojedynczy typ

1. Wybierz typ i wybierz ikonę **Edytuj** lub **ołówka**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![edytować typ w okienku.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Usuń typ

1. Wybierz typ, a następnie wybierz ikonę **usuwanie** lub **odpady z pojemników**. .

   [![usunąć typ, wybierając pozycję Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat modelu szeregów czasowych, przeczytaj artykuł [Modelowanie danych](./time-series-insights-update-tsm.md).

- Aby dowiedzieć się więcej na temat wersji zapoznawczej, przeczytaj artykuł [Wizualizacja danych w eksploratorze Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

- Aby dowiedzieć się więcej o obsługiwanych kształtach JSON, Odczytaj [obsługiwane kształty JSON](./time-series-insights-send-events.md#supported-json-shapes).
