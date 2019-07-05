---
title: Modelowanie danych w wersji zapoznawczej Azure czas serii szczegółowych informacji | Dokumentacja firmy Microsoft
description: Dowiedz się, modelowania danych w wersji zapoznawczej Azure czas serii szczegółowych informacji.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 05faf77d22f77da87e7c22d47473e6debf0f77c8
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461087"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelowanie danych w wersji zapoznawczej Azure czas serii szczegółowych informacji

W tym dokumencie opisano sposób pracy z modeli szeregów czasowych, zgodnie z wersji zapoznawczej Azure czas serii szczegółowych informacji. Szczegółowe informacje dotyczące kilka typowych scenariuszy danych.

Aby dowiedzieć się więcej o sposobie używania aktualizacji, przeczytaj [Azure czas Series Insights w wersji zapoznawczej Eksplorator](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Tworzenie jednego typu

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **typy** z menu. Zwiń panel skoncentrować się na typy modeli szeregów czasowych.

    [![Tworzenie jednego typu](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Wybierz pozycję **Dodaj**.
1. Wprowadź wszystkie szczegółowe informacje, które odnoszą się do typów, a następnie wybierz **Utwórz**. Ta akcja tworzy typy w środowisku.

    [![Dodaj typ](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Masowe przekazywanie jeden lub więcej typów

1. Wybierz **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek typu.
1. Wybierz pozycję **Przekaż**.

    [![Przekaż JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Edytowanie pojedynczego typu

1. Wybierz typ, a następnie wybierz pozycję **Edytuj**. 
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytowanie typu](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Usuwanie typu

1. Wybierz typ, a następnie wybierz pozycję **Usuń**.
1. Jeśli żadne wystąpienia są skojarzone z typami, jest usuwany.

    [![Usuwanie typu](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Utwórz pojedynczą hierarchię

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **hierarchie** z menu. Zwiń panel skoncentrować się na hierarchie modeli szeregów czasowych.

    [![Wybierz opcję Hierarchie](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Wybierz pozycję **Dodaj**.

    [![Dodaj hierarchię](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Wybierz **Dodaj poziom** w okienku po prawej stronie.

    [![Dodaj poziom](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Wprowadź szczegóły hierarchii, a następnie wybierz pozycję **Utwórz**.

    [![Utworzyć poziom](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Masowe przekazywanie jednej lub wielu hierarchii

1. Wybierz **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek hierarchii.
1. Wybierz pozycję **Przekaż**.

    [![Zbiorcze przekazywanie hierarchii](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Edytuj jednej hierarchii

1. Wybierz hierarchię, a następnie wybierz pozycję **Edytuj**.
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytuj jednej hierarchii](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Usuwanie hierarchii

1. Wybierz hierarchię, a następnie wybierz pozycję **Usuń**. 
1. Jeśli żadne wystąpienia są skojarzone z hierarchii, zostanie usunięta.

    [![Usuwanie hierarchii](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Wystąpienia

### <a name="create-a-single-instance"></a>Tworzenie pojedynczego wystąpienia

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **wystąpień** z menu. Zwiń panel, aby skoncentrować się na wystąpieniach modeli szeregów czasowych.

    [![Tworzenie pojedynczego wystąpienia](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Wybierz pozycję **Dodaj**.

    [![Dodaje wystąpienie](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Wprowadź szczegóły wystąpienia, wybierz skojarzenia typu i hierarchii i wybierz **Utwórz**.

### <a name="bulk-upload-one-or-more-instances"></a>Masowe przekazywanie co najmniej jedno wystąpienie

1. Wybierz **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek wystąpień.

    [![Masowe przekazywanie co najmniej jedno wystąpienie](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Wybierz pozycję **Przekaż**.

### <a name="edit-a-single-instance"></a>Edytowanie pojedynczego wystąpienia

1. Wybierz wystąpienie, a następnie wybierz pozycję **Edytuj**. 
1. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

    [![Edytowanie pojedynczego wystąpienia](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat modeli szeregów czasowych, przeczytaj [modelowania danych](./time-series-insights-update-tsm.md).

- Aby dowiedzieć się więcej na temat korzystania z wersji zapoznawczej, przeczytaj [wizualizować dane w Eksploratorze usługi Azure czas Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).

- Aby dowiedzieć się o obsługiwane kształty JSON, przeczytaj [obsługiwane kształty JSON](./time-series-insights-send-events.md#json).
