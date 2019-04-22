---
title: Monitorowanie wydajności, kondycji i użycia za pomocą metryk Eksplorator danych platformy Azure
description: Informacje o sposobie monitorowania wydajności, kondycji i użycia klastra przy użyciu Eksploratora danych usługi Azure metryk.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050620"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitorowanie wydajności, kondycji i użycia za pomocą metryk Eksplorator danych platformy Azure

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. Metryki w Eksploratorze danych platformy Azure zapewniają kluczowe wskaźniki dotyczące kondycji i wydajności zasobów klastra. Użyj metryk, które są szczegółowo opisane w tym artykule do monitorowania kondycji klastra Eksploratora danych usługi Azure i wydajności w danym scenariuszu określone jako autonomiczny metryki. Można również użyć metryki jako podstawy dla operacyjnej [pulpitów nawigacyjnych platformy Azure](/azure/azure-portal/azure-portal-dashboards) i [Azure Alerts](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Tworzenie [klastra i bazy danych](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="using-metrics"></a>Przy użyciu metryk

W klastrze Azure Eksploratora danych, wybierz **metryki** aby otworzyć okienko metryki i rozpocząć analizę w klastrze.

![Wybierz metryki](media/using-metrics/select-metrics.png)

W okienku metryki:

![W okienku metryki](media/using-metrics/metrics-pane.png)

1. Aby utworzyć wykresu metryki, wybierz **metryki** nazwy, a odpowiednie **agregacji** na metrykę zgodnie z opisem poniżej. **Zasobów** i **Namespace metryki** selektora są wstępnie wybrane do klastra usługi Azure Eksploratora danych.

    **Metryka** | **Unit** | **Agregacja** | **Opis metryki**
    |---|---|---|---|
    | Wykorzystanie pamięci podręcznej | Procent | Avg, Max, Min | Wartość procentową zasobów przydzielonych pamięci podręcznej, obecnie w użyciu przez klaster. Pamięć podręczna odnosi się do rozmiaru dysków SSD przydzielone dla działań użytkownika, zgodnie z zasadami pamięci podręcznej zdefiniowane. Wykorzystanie pamięci podręcznej, 80% lub mniej jest trwały stan dla klastra. Jeśli wykorzystanie pamięci podręcznej przekracza 80%, klaster powinien być [skalowany w górę](manage-cluster-scale-up.md) do magazynu zoptymalizowane pod kątem warstwy cenowej lub [skalowana w poziomie](manage-cluster-scale-out.md) do większej liczby wystąpień. Alternatywnie można dostosować zasady pamięci podręcznej (mniejszą liczbę dni w pamięci podręcznej). Jeśli wykorzystanie pamięci podręcznej wynosi ponad 100%, rozmiar danych w pamięci podręcznej, zgodnie z zasadami pamięci podręcznej jest większy, całkowity rozmiar pamięci podręcznej w klastrze. |
    | Procesor CPU | Procent | Avg, Max, Min | Procent zasoby obliczeniowe przydzielone aktualnie w użyciu przez komputery w klastrze. Średnie użycie procesora CPU 80% lub mniej jest trwały dla klastra. Wartość maksymalna procesora CPU wynosi 100%, co oznacza, że nie ma żadnych dodatkowe zasoby obliczeniowe do przetwarzania danych. Gdy klaster nie działa prawidłowo, sprawdź wartość maksymalna procesora CPU, aby ustalić, czy określonych procesorów CPU, które są blokowane. |
    | Zdarzenia przetwarzane (dla usługi Event Hubs) | Licznik | Suma Max, Min, | Łączna liczba zdarzeń odczytu z usługi event hubs i przetwarzane przez klaster. Zdarzenia są dzielone na zdarzenia odrzucone i zaakceptowane przez aparat klastra. |
    | Opóźnienia w pozyskiwaniu danych | Sekundy | Avg, Max, Min | Opóźnienie danych pozyskanych od chwili, gdy otrzymano dane w klastrze, dopóki nie jest gotowa do zapytania. Pozyskiwanie czasu oczekiwania zależy od scenariusza wprowadzania. |
    | Wynik pozyskiwania | Licznik | Licznik | Łączna liczba operacji pozyskiwania, które nie powiodło się i zakończyło się pomyślnie. Użyj **zastosować podział** tworzenie zasobników sukces i niepowodzenie wyniki oraz analizowanie wymiary (**wartość** > **stan**).|
    | Wykorzystanie pozyskiwania | Procent | Avg, Max, Min | Wartość procentowa rzeczywiste używanych pozyskiwać dane z łącznych zasobów przydzielonych w zasadach pojemności, przeprowadzić pozyskiwanie zasobów. Domyślne zasady pojemności jest nie więcej niż 512 operacje współbieżne pozyskiwania lub 75% zainwestowały w pozyskiwanie zasoby klastra. Pozyskiwanie średnie wykorzystanie 80% lub mniej jest trwały stan dla klastra. Maksymalna wartość wykorzystania pozyskiwania wynosi 100%, co oznacza, że wszystkie możliwości pozyskiwania klastra jest używany i może spowodować kolejki pozyskiwania. |
    | Pozyskiwanie woluminu (w MB) | Licznik | Suma Max, Min, | Całkowity rozmiar danych pozyskanych do klastra (w MB) przed kompresją. |
    | Podtrzymanie | Licznik | Średnio | Śledzi czas reakcji klastra. Klaster w pełni interaktywne, zwraca wartość 1, i zwraca wartość 0, zablokowanych lub odłączonych klastra. |
    | Czas trwania zapytania | Sekundy | Liczba, Avg, Min, Max, Sum | Łączny czas otrzymanie wyników zapytania (nie obejmuje opóźnienia sieci). |
    | | | |

    Dodatkowe informacje dotyczące [obsługiwane metryki klastra Eksplorator danych platformy Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Wybierz **Dodaj metrykę** przycisk, aby wyświetlić wiele metryk, w tym samym wykresie.
3. Wybierz **+ nowy wykres** przycisk, aby wyświetlić wiele wykresów w jednym widoku.
4. Użyj selektora czasu można zmienić zakres czasu (domyślne: ostatnie 24 godziny).
5. Użyj [ **Dodaj filtr** i **zastosować podział** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) dla metryk, który ma wymiarów.
6. Wybierz **Przypnij do pulpitu nawigacyjnego** , można go ponownie wyświetlić, dodać konfigurację wykresu do pulpitów nawigacyjnych.
7. Ustaw **Nowa reguła alertu** w celu wizualizacji metryk przy użyciu zestawu kryteriów. Nowa reguła alertu będzie zawierać Twojego zasobu docelowego, metryki, dzielenie i wymiary filtru z wykresu. Modyfikowanie tych ustawień w [panelu Tworzenie reguły alertu](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Dodatkowe informacje na temat korzystania z [Eksploratora metryk](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
