---
title: Modelowanie danych w wersji zapoznawczej Azure czas serii szczegółowych informacji | Dokumentacja firmy Microsoft
description: Dowiedz się, modelowania danych w wersji zapoznawczej Azure czas serii szczegółowych informacji.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: df94290c5e62b898b6490c78ef0ae1ee79437240
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64716954"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelowanie danych w wersji zapoznawczej Azure czas serii szczegółowych informacji

W tym dokumencie opisano sposób pracy z modeli szeregów czasowych, zgodnie z wersji zapoznawczej Azure czas serii szczegółowych informacji. Szczegółowe informacje dotyczące kilka typowych scenariuszy danych.

Aby dowiedzieć się więcej o sposobie używania aktualizacji, przeczytaj [Azure czas Series Insights w wersji zapoznawczej Eksplorator](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Tworzenie jednego typu

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **typy** z menu. Zwiń panel skoncentrować się na typy modeli szeregów czasowych.

    ![Portal_one][1]

1. Wybierz pozycję **Dodaj**.
1. Wprowadź wszystkie szczegółowe informacje, które odnoszą się do typów, a następnie wybierz **Utwórz**. Ta akcja tworzy typy w środowisku.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Masowe przekazywanie jeden lub więcej typów

1. Wybierz **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek typu.
1. Wybierz pozycję **Przekaż**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Edytowanie pojedynczego typu

Wybierz typ, a następnie wybierz pozycję **Edytuj**. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

![Portal_four][4]

### <a name="delete-a-type"></a>Usuwanie typu

Wybierz typ, a następnie wybierz pozycję **Usuń**. Jeśli żadne wystąpienia są skojarzone z typami, jest usuwany.

![Portal_five][5]

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Utwórz pojedynczą hierarchię

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **hierarchie** z menu. Zwiń panel skoncentrować się na hierarchie modeli szeregów czasowych.

    ![Portal_six][6]

1. Wybierz pozycję **Dodaj**.

    ![Portal_seven][7]

1. Wybierz **Dodaj poziom** w okienku po prawej stronie.

    ![Portal_eight][8]

1. Wprowadź szczegóły hierarchii, a następnie wybierz pozycję **Utwórz**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Masowe przekazywanie jednej lub wielu hierarchii

1. Wybierz **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek hierarchii.
1. Wybierz pozycję **Przekaż**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Edytuj jednej hierarchii

Wybierz hierarchię, a następnie wybierz pozycję **Edytuj**. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Usuwanie hierarchii

Wybierz hierarchię, a następnie wybierz pozycję **Usuń**. Jeśli żadne wystąpienia są skojarzone z hierarchii, zostanie usunięta.

![Portal_twelve][12]

## <a name="instances"></a>Wystąpienia

### <a name="create-a-single-instance"></a>Tworzenie pojedynczego wystąpienia

1. Przejdź do panelu selektor modeli szeregów czasowych, a następnie wybierz pozycję **wystąpień** z menu. Zwiń panel, aby skoncentrować się na wystąpieniach modeli szeregów czasowych.

    ![Portal_thirteen][13]

1. Wybierz pozycję **Dodaj**.

    ![Portal_fourteen][14]

1. Wprowadź szczegóły wystąpienia, wybierz skojarzenia typu i hierarchii i wybierz **Utwórz**.

### <a name="bulk-upload-one-or-more-instances"></a>Masowe przekazywanie co najmniej jedno wystąpienie

1. Wybierz **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek wystąpień.

    ![Portal_fifteen][15]

1. Wybierz pozycję **Przekaż**.

### <a name="edit-a-single-instance"></a>Edytowanie pojedynczego wystąpienia

Wybierz wystąpienie, a następnie wybierz pozycję **Edytuj**. Wprowadź wymagane zmiany, a następnie wybierz pozycję **Zapisz**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Usunięcia wystąpienia

Wybierz wystąpienie, a następnie wybierz pozycję **Usuń**. Jeśli żadne zdarzenia nie są skojarzone z wystąpieniami, zostanie usunięta.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać więcej informacji na temat modeli szeregów czasowych, przeczytaj [modelowania danych](./time-series-insights-update-tsm.md).

- Aby dowiedzieć się więcej na temat korzystania z wersji zapoznawczej, przeczytaj [wizualizować dane w Eksploratorze usługi Azure czas Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).

- Aby dowiedzieć się o obsługiwane kształty JSON, przeczytaj [obsługiwane kształty JSON](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png