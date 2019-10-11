---
title: Modelowanie danych w wersji zapoznawczej Azure Time Series Insights | Microsoft Docs
description: Zrozumienie modelowania danych w wersji zapoznawczej Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 64f892f344780d4af58c70935b9b7b68bad9550d
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273741"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelowanie danych w wersji zapoznawczej Azure Time Series Insights

W tym dokumencie opisano, jak korzystać z modeli szeregów czasowych, po Azure Time Series Insights wersji zapoznawczej. Zawiera szczegółowe informacje o kilku typowych scenariuszach dotyczących danych.

Aby dowiedzieć się więcej na temat sposobu korzystania z aktualizacji, przeczytaj artykuł [Azure Time Series Insights Explorer w wersji zapoznawczej](./time-series-insights-update-explorer.md).

## <a name="types"></a>Types

### <a name="create-a-single-type"></a>Tworzenie pojedynczego typu

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **typy** z menu. Zwiń panel, aby skoncentrować się na typach modeli szeregów czasowych.

    [@no__t — 1Create pojedynczy typ](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.
1. Wprowadź wszystkie szczegóły, które odnoszą się do typów, a następnie wybierz pozycję **Utwórz**. Ta akcja tworzy typy w środowisku.

    [@no__t — 1Add typu](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Przekaż zbiorczo jeden lub więcej typów

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek typu.
1. Wybierz pozycję **Przekaż**.

    [@no__t — 1Upload JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Edytuj pojedynczy typ

1. Wybierz typ i wybierz pozycję **Edytuj**. 
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [@no__t — 1Edit typu](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Usuń typ

1. Wybierz typ i wybierz pozycję **Usuń**.
1. Jeśli żadne wystąpienia nie są skojarzone z typami, zostaną usunięte.

    [@no__t — 1Delete typu](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Zlokalizowan

### <a name="create-a-single-hierarchy"></a>Utwórz pojedynczą hierarchię

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz **hierarchie** z menu. Zwiń panel, aby skoncentrować się na hierarchiach modeli szeregów czasowych.

    [Hierarchie @no__t 1Select](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![Add hierarchii](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Wybierz pozycję **+ Dodaj poziom** w okienku po prawej stronie.

    [@no__t — 1Add poziom](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Wprowadź szczegóły hierarchii i wybierz pozycję **Utwórz**.

    [@no__t — 1Create poziom](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Przekaż zbiorczo co najmniej jedną hierarchię

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek hierarchii.
1. Wybierz pozycję **Przekaż**.

    [1Bulk — przekazanie hierarchii @no__t](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Edytowanie pojedynczej hierarchii

1. Wybierz hierarchię i wybierz pozycję **Edytuj**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [@no__t — 1Edit pojedynczej hierarchii](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Usuwanie hierarchii

1. Wybierz hierarchię i wybierz pozycję **Usuń**. 
1. Jeśli żadne wystąpienia nie są skojarzone z hierarchią, jest usuwana.

    [![Delete hierarchii](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Wystąpienia

### <a name="create-a-single-instance"></a>Tworzenie pojedynczego wystąpienia

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **wystąpienia** z menu. Zwiń panel, aby skoncentrować się na wystąpieniach modeli szeregów czasowych.

    [@no__t — 1Create pojedyncze wystąpienie](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Wybierz pozycję **Dodaj**.

    [@no__t — 1Add wystąpienie](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Wprowadź szczegóły wystąpienia, wybierz pozycję typ i skojarzenie hierarchii, a następnie wybierz pozycję **Utwórz**.

### <a name="bulk-upload-one-or-more-instances"></a>Przekaż zbiorczo co najmniej jedno wystąpienie

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek wystąpień.

    [![Bulk przekazać co najmniej jedno wystąpienie](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Wybierz pozycję **Przekaż**.

### <a name="edit-a-single-instance"></a>Edytuj pojedyncze wystąpienie

1. Wybierz wystąpienie i wybierz pozycję **Edytuj**. 
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [@no__t — 1Edit pojedyncze wystąpienie](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat modeli szeregów czasowych, przeczytaj artykuł [Modelowanie danych](./time-series-insights-update-tsm.md).

- Aby dowiedzieć się więcej na temat wersji zapoznawczej, przeczytaj artykuł [Wizualizacja danych w eksploratorze Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

- Aby dowiedzieć się więcej o obsługiwanych kształtach JSON, Odczytaj [obsługiwane kształty JSON](./time-series-insights-send-events.md#supported-json-shapes).
