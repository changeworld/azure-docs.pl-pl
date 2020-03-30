---
title: Modelowanie danych w środowiskach w wersji zapoznawczej — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o modelowaniu danych w usłudze Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470755"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelowanie danych w usłudze Azure Time Series Insights Preview

W tym artykule opisano sposób pracy z modelem szeregów czasowych w usłudze Azure Time Series Insights Preview. Wyszcze wyszczełmnie kilka typowych scenariuszy danych.

> [!TIP]
> * Przeczytaj o modelu [serii czasowych](time-series-insights-update-tsm.md)podglądu .
> * Dowiedz się więcej o nawigowaniu po interfejsie użytkownika w wersji zapoznawczej w [Eksploratorze usługi Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Wystąpienia

Eksplorator usługi Azure Time Series Insights obsługuje operacje **tworzenia**wystąpienia , **odczytu,** **aktualizacji**i **usuwania** w przeglądarce. 

Aby rozpocząć, wybierz widok **Modelu** z widoku **Analizowania analizowania** szeregów czasowych.

### <a name="create-a-single-instance"></a>Tworzenie pojedynczego wystąpienia

1. Przejdź do panelu selektora modelu **szeregów czasowych** i wybierz z menu wystąpienia. Zostaną wyświetlone wszystkie wystąpienia skojarzone z wybranym środowiskiem usługi Time Series Insights.

    [![Utwórz pojedyncze wystąpienie, najpierw wybierając wystąpienia.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![Dodaj wystąpienie, zaznaczając przycisk + Dodaj.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Wprowadź szczegóły wystąpienia, wybierz skojarzenie typu i hierarchii, a następnie wybierz pozycję **Utwórz**.

### <a name="bulk-upload-one-or-more-instances"></a>Przesyłanie zbiorcze co najmniej jedno wystąpienie

> [!TIP]
> Wystąpienia można zapisać na pulpicie w aplikacji JSON. Pobrany plik JSON można następnie przekazać za pomocą następujących kroków.

1. Wybierz **pozycję Przekaż JSON**.
1. Wybierz plik zawierający ładunek wystąpień.

    [![Zbiorcze przekazywanie wystąpień za pośrednictwem JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Wybierz **pozycję Przekaż**.

### <a name="edit-a-single-instance"></a>Edytowanie pojedynczego wystąpienia

1. Zaznacz instancję i wybierz ikonę **edycji** lub **ołówka**. 
1. Wykonuj wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytowanie pojedynczego wystąpienia.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Usuwanie wystąpienia

1. Zaznacz wystąpienie i wybierz ikonę **usuwania** lub **pojemnika na odpady**.

   [![Usuń wystąpienie, wybierając pozycję Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

> [!NOTE]
> Wystąpienie musi pomyślnie przejść sprawdzanie poprawności pola, aby zostało usunięte.

## <a name="hierarchies"></a>Hierarchie

Eksplorator usługi Azure Time Series Insights obsługuje operacje hierarchy **CREATE**, **READ**, **UPDATE**i **DELETE** w przeglądarce. 

Aby rozpocząć, wybierz widok **Modelu** z widoku **Analizowania analizowania** szeregów czasowych.

### <a name="create-a-single-hierarchy"></a>Tworzenie pojedynczej hierarchii

1. Przejdź do panelu selektora modelu szeregów czasowych i wybierz z menu **pozycję Hierarchie.** Zostaną wyświetlone wszystkie hierarchie skojarzone z wybranym środowiskiem usługi Time Series Insights.

    [![Tworzenie hierarchii za pomocą okienka.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![Hierarchia + Przycisk Dodaj.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Wybierz **+ Dodaj poziom** w prawym okienku.

    [![Dodaj poziom do hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Wprowadź szczegóły hierarchii i wybierz pozycję **Zapisz**.

    [![Określ szczegóły hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Przesyłanie zbiorcze co najmniej jednej hierarchii

> [!TIP]
> Hierarchie można zapisać na pulpicie w json. Pobrany plik JSON można następnie przekazać za pomocą następujących kroków.

1. Wybierz **pozycję Przekaż JSON**.
1. Wybierz plik zawierający ładunek hierarchii.
1. Wybierz **pozycję Przekaż**.

    [![Wybory do zbiorczego przekazywania hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Edytowanie pojedynczej hierarchii

1. Wybierz hierarchię i wybierz ikonę **edycji** lub **ołówka**.
1. Wykonuj wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Wybór do edycji pojedynczej hierarchii.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Usuwanie hierarchii

1. Zaznacz hierarchię i wybierz ikonę **usuwania** lub **pojemnika na odpady**. 

    [![Usuń hierarchię, wybierając przycisk Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

## <a name="types"></a>Types

Eksplorator usługi Azure Time Series Insights obsługuje operacje Type **CREATE**, **READ**, **UPDATE**i **DELETE** w przeglądarce. 

Aby rozpocząć, wybierz widok **Modelu** z widoku **Analizowania analizowania** szeregów czasowych.

### <a name="create-a-single-type"></a>Tworzenie pojedynczego typu

1. Przejdź do panelu wyboru modelu szeregów czasowych i wybierz z menu **pozycję Typy.** Zostaną wyświetlone wszystkie typy skojarzone z wybranym środowiskiem usługi Time Series Insights.

    [![Okienko typy modeli szeregów czasowych.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Wybierz **+ Dodaj,** aby wyświetlić **modal wyskakujące okienko Dodaj nowy typ.**
1. Wprowadź właściwości i zmienne dla twojego typu. Po wprowadzeniu wybierz pozycję **Zapisz**. 

    [![Ustawienia konfiguracji, aby dodać typ.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Przesyłanie zbiorcze co najmniej jeden typ

> [!TIP]
> Typy można zapisać na pulpicie w json. Pobrany plik JSON można następnie przekazać za pomocą następujących kroków.

1. Wybierz **pozycję Przekaż JSON**.
1. Wybierz plik zawierający ładunek typu.
1. Wybierz **pozycję Przekaż**.

    [![Opcje przesyłania zbiorczego typu zbiorczego.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Edytowanie pojedynczego typu

1. Zaznacz typ i wybierz ikonę **edycji** lub **ołówka**.
1. Wykonuj wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytowanie tekstu w okienku.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Usuwanie typu

1. Zaznacz typ i wybierz ikonę **usuwania** lub **pojemnika na odpady**. .

   [![Usuń typ, wybierając pozycję Usuń.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Potwierdź usunięcie, wybierając pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat modelu szeregów czasowych, zobacz [Modelowanie danych](./time-series-insights-update-tsm.md).

- Aby dowiedzieć się więcej o wersji zapoznawczej, przeczytaj artykuł [Wizualizacja danych w Eksploratorze usługi Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

- Aby dowiedzieć się więcej o obsługiwanych kształtach JSON, zobacz [Obsługiwane kształty JSON](./time-series-insights-send-events.md#supported-json-shapes).
