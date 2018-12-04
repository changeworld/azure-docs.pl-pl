---
title: Modelowanie danych w usłudze Azure Time Series Insights | Dokumentacja firmy Microsoft
description: Omówienie modelowania danych w usłudze Azure Time Series Insights
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 926d1a35cb10d50ddeacbed5476e2dcf14d0999d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856632"
---
# <a name="data-modeling-in-azure-time-series-insights"></a>Modelowanie danych w usłudze Azure Time Series Insights

W tym dokumencie opisano sposób pracy z **modeli szeregów czasowych** zgodnie z usługi Azure Time Series Insights (wersja zapoznawcza). Szczegółowe informacje dotyczące kilka typowych scenariuszy danych.

Odczyt [Azure TSI Explorer (wersja zapoznawcza)](./time-series-insights-update-explorer.md) artykułu, aby dowiedzieć się więcej o przechodząc prywatnej wersji zapoznawczej.

## <a name="types"></a>Typy

### <a name="how-to-create-a-single-type"></a>Jak utworzyć jeden typ

1. Początek nagłówka do panelu selektor TSM modelu, a następnie wybierz typy z menu. Następnie zwijanie panelu, aby skoncentrować się na typach TSM:

    ![portal_one][1]

1. Kliknij pozycję **Dodaj**.
1. Wszystkie szczegółowe informacje dotyczące typów wejściowych, a następnie kliknij przycisk **Utwórz**. Ten sposób należy utworzyć typy w środowisku:

    ![portal_two][2]

### <a name="how-to-bulk-upload-one-or-more-types"></a>Jak przeprowadzić zbiorczą przekazać jeden lub więcej typów

1. Kliknij pozycję **przekazywanie JSON**.
1. Wybierz ten plik, który zawiera ładunek typu.
1. Kliknij pozycję **przekazywania**

    ![portal_three][3]

### <a name="how-to-edit-a-single-type"></a>Jak edytować pojedynczego typu

* Wybierz typ, a następnie kliknij pozycję **Edytuj** przycisku. Wprowadź wymagane zmiany, a następnie kliknij przycisk **Zapisz**:

    ![portal_four][4]

### <a name="how-to-delete-a-type"></a>Jak usunąć typ

* Wybierz typ, a następnie kliknij pozycję **Usuń** przycisku. Jeśli żadne wystąpienia są skojarzone z typami, zostaną usunięte:

    ![portal_five][5]

## <a name="hierarchies"></a>Hierarchie

### <a name="how-to-create-a-single-hierarchy"></a>Jak utworzyć pojedynczą hierarchię

1. Rozpocznij od pozycji do panelu selektor TSM modelu, a następnie wybierz hierarchie z menu. Następnie zwijanie panelu, aby skoncentrować się na typach TSM:

    ![portal_six][6]

1. Kliknij pozycję **Dodaj**

    ![portal_seven][7]

1. Kliknij pozycję **Dodaj poziom** w okienku po prawej stronie:

    ![portal_eight][8]

1. Wprowadź szczegóły hierarchii, a następnie kliknij przycisk **Utwórz**:

    ![portal_nine][9]

### <a name="how-to-bulk-upload-one-or-more-hierarchies"></a>Jak przeprowadzić zbiorczą przekazania jednej lub wielu hierarchii

1. Kliknij pozycję **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek hierarchii.
1. Kliknij pozycję **przekazywanie**:

    ![portal_ten][10]

### <a name="how-to-edit-a-single-hierarchy"></a>Jak edytować jednej hierarchii

* Wybierz hierarchię i kliknij pozycję **Edytuj** przycisku. Wprowadź wymagane zmiany, a następnie kliknij przycisk **Zapisz**:

    ![portal_eleven][11]

### <a name="how-to-delete-a-hierarchy"></a>Usuwanie hierarchii

* Wybierz hierarchię i kliknij pozycję **Usuń** przycisku. Brak wystąpień są skojarzone z hierarchii, zostaną usunięte.

    ![portal_twelve][12]

## <a name="instances"></a>Wystąpienia

### <a name="how-to-create-a-single-instance"></a>Jak utworzyć pojedyncze wystąpienie

1. Rozpocznij od pozycji do panelu selektor TSM modelu, a następnie wybierz wystąpienia z menu. Następnie zwijanie panelu, aby skoncentrować się na typach TSM:

    ![portal_thirteen][13]

1. Kliknij pozycję **Dodaj**:

    ![portal_fourteen][14]

1. Wprowadź szczegóły wystąpienia, wybierz typ i hierarchii i kliknij **Utwórz**.

### <a name="how-to-bulk-upload-one-or-more-instances"></a>Jak przeprowadzić zbiorczą przekazać co najmniej jedno wystąpienie

1. Kliknij pozycję **przekazywanie JSON**.
1. Wybierz plik, który zawiera ładunek wystąpień:

    ![portal_fifteen][15]

1. Kliknij pozycję **przekazywanie**.

### <a name="how-to-edit-a-single-instance"></a>Jak edytować pojedynczego wystąpienia

* Wybierz wystąpienie, a następnie kliknij pozycję **Edytuj** przycisku. Wprowadź wymagane zmiany, a następnie kliknij przycisk **Zapisz**:

    ![portal_sixteen][16]

### <a name="how-to-delete-an-instance"></a>Jak usunąć wystąpienia

* Wybierz wystąpienie, a następnie kliknij pozycję **Usuń** przycisku. Jeśli żadne zdarzenia nie są skojarzone z wystąpienia, zostaną usunięte.

## <a name="next-steps"></a>Kolejne kroki

Odczyt [modeli szeregów czasowych](./time-series-insights-update-tsm.md) Aby uzyskać więcej informacji na temat **modeli szeregów czasowych**.

Wyświetl Azure TSI Explorer (wersja zapoznawcza) [artykułu](./time-series-insights-update-explorer.md) Aby dowiedzieć się więcej na temat korzystania z wersji zapoznawczej.

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