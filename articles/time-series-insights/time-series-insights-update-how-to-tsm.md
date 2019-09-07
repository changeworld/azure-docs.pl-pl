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
ms.openlocfilehash: 245a69f5e5834e68bbbd17a96859a93bc16eacbe
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744257"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelowanie danych w wersji zapoznawczej Azure Time Series Insights

W tym dokumencie opisano, jak korzystać z modeli szeregów czasowych, po Azure Time Series Insights wersji zapoznawczej. Zawiera szczegółowe informacje o kilku typowych scenariuszach dotyczących danych.

Aby dowiedzieć się więcej na temat sposobu korzystania z aktualizacji, przeczytaj artykuł [Azure Time Series Insights Explorer w wersji zapoznawczej](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Tworzenie pojedynczego typu

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **typy** z menu. Zwiń panel, aby skoncentrować się na typach modeli szeregów czasowych.

    [![Tworzenie pojedynczego typu](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.
1. Wprowadź wszystkie szczegóły, które odnoszą się do typów, a następnie wybierz pozycję **Utwórz**. Ta akcja tworzy typy w środowisku.

    [![Dodawanie typu](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Przekaż zbiorczo jeden lub więcej typów

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek typu.
1. Wybierz pozycję **Przekaż**.

    [![Przekaż kod JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Edytuj pojedynczy typ

1. Wybierz typ i wybierz pozycję **Edytuj**. 
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytuj typ](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Usuń typ

1. Wybierz typ i wybierz pozycję **Usuń**.
1. Jeśli żadne wystąpienia nie są skojarzone z typami, zostaną usunięte.

    [![Usuń typ](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Utwórz pojedynczą hierarchię

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz **hierarchie** z menu. Zwiń panel, aby skoncentrować się na hierarchiach modeli szeregów czasowych.

    [![Wybierz hierarchie](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Wybierz pozycję **+ Dodaj**.

    [![Dodawanie hierarchii](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Wybierz pozycję **+ Dodaj poziom** w okienku po prawej stronie.

    [![Dodawanie poziomu](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Wprowadź szczegóły hierarchii i wybierz pozycję **Utwórz**.

    [![Tworzenie poziomu](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Przekaż zbiorczo co najmniej jedną hierarchię

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek hierarchii.
1. Wybierz pozycję **Przekaż**.

    [![Zbiorcze przekazywanie hierarchii](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Edytowanie pojedynczej hierarchii

1. Wybierz hierarchię i wybierz pozycję **Edytuj**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytowanie pojedynczej hierarchii](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Usuwanie hierarchii

1. Wybierz hierarchię i wybierz pozycję **Usuń**. 
1. Jeśli żadne wystąpienia nie są skojarzone z hierarchią, jest usuwana.

    [![Usuwanie hierarchii](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Wystąpienia

### <a name="create-a-single-instance"></a>Tworzenie pojedynczego wystąpienia

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **wystąpienia** z menu. Zwiń panel, aby skoncentrować się na wystąpieniach modeli szeregów czasowych.

    [![Tworzenie pojedynczego wystąpienia](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Wybierz pozycję **Dodaj**.

    [![Dodaj wystąpienie](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Wprowadź szczegóły wystąpienia, wybierz pozycję typ i skojarzenie hierarchii, a następnie wybierz pozycję **Utwórz**.

### <a name="bulk-upload-one-or-more-instances"></a>Przekaż zbiorczo co najmniej jedno wystąpienie

1. Wybierz pozycję **Przekaż plik JSON**.
1. Wybierz plik, który zawiera ładunek wystąpień.

    [![Przekaż zbiorczo co najmniej jedno wystąpienie](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Wybierz pozycję **Przekaż**.

### <a name="edit-a-single-instance"></a>Edytuj pojedyncze wystąpienie

1. Wybierz wystąpienie i wybierz pozycję **Edytuj**. 
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytuj pojedyncze wystąpienie](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat modeli szeregów czasowych, przeczytaj artykuł [Modelowanie danych](./time-series-insights-update-tsm.md).

- Aby dowiedzieć się więcej na temat wersji zapoznawczej, przeczytaj artykuł [Wizualizacja danych w eksploratorze Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

- Aby dowiedzieć się więcej o obsługiwanych kształtach JSON, Odczytaj [obsługiwane kształty JSON](./time-series-insights-send-events.md#json).
