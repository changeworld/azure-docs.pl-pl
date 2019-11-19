---
title: Monitoruj wydajność, kondycję i użycie usługi Azure Eksplorator danych przy użyciu metryk
description: Dowiedz się, jak używać metryk Eksplorator danych platformy Azure do monitorowania wydajności, kondycji i użycia klastra.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173785"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Monitoruj wydajność, kondycję i użycie usługi Azure Eksplorator danych przy użyciu metryk

Usługa Azure Data Explorer to szybka, w pełni zarządzana usługa do analizy danych, która pozwala w czasie rzeczywistym analizować duże woluminy danych przesyłanych strumieniowo z aplikacji, witryn internetowych, urządzeń IoT i nie tylko. Aby używać usługi Azure Data Explorer, najpierw utwórz klaster, a następnie utwórz w tym klastrze co najmniej jedną bazę danych. Następnie pozyskaj (załaduj) dane do bazy danych, aby uruchamiać w niej zapytania. Metryki usługi Azure Eksplorator danych zapewniają kluczowe wskaźniki dotyczące kondycji i wydajności zasobów klastra. Aby monitorować kondycję i wydajność klastra platformy Azure Eksplorator danych w konkretnym scenariuszu jako metryki autonomiczne, należy użyć metryk, które zostały szczegółowo opisane w tym artykule. Możesz również użyć metryk jako podstawy dla operacyjnych [pulpitów nawigacyjnych platformy Azure](/azure/azure-portal/azure-portal-dashboards) i [alertów platformy Azure](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* Utwórz [klaster i bazę danych](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="using-metrics"></a>Korzystanie z metryk

W klastrze usługi Azure Eksplorator danych wybierz pozycję **metryki** , aby otworzyć okienko metryki i rozpocząć analizowanie w klastrze.

![Wybierz metryki](media/using-metrics/select-metrics.png)

W okienku metryki:

![Okienko metryk](media/using-metrics/metrics-pane.png)

1. Aby utworzyć wykres metryki, wybierz pozycję Nazwa **metryki** i odpowiednią **agregację** na metrykę, jak pokazano poniżej. Selektory **przestrzeni nazw** **zasobów** i metryk są wstępnie wybrane dla klastra Eksplorator danych platformy Azure.

    **Metryka** | **Unit** | **Agregacja** | **Opis metryki**
    |---|---|---|---|
    | Użycie pamięci podręcznej | Procent | Średnia, maks., minimum | Procent przyznanych zasobów pamięci podręcznej, które są obecnie używane przez klaster. Pamięć podręczna odnosi się do rozmiaru dysku SSD przydzieloną dla aktywności użytkownika zgodnie ze zdefiniowanymi zasadami pamięci podręcznej. Średnie użycie pamięci podręcznej wynoszącej 80% lub mniej jest trwałym stanem klastra. Jeśli średnie wykorzystanie pamięci podręcznej jest powyżej 80%, klaster należy [skalować w górę](manage-cluster-vertical-scaling.md) do warstwy cenowej zoptymalizowanej pod kątem magazynu lub [skalować](manage-cluster-horizontal-scaling.md) do większej liczby wystąpień. Alternatywnie możesz dostosować zasady pamięci podręcznej (mniej dni w pamięci podręcznej). Jeśli użycie pamięci podręcznej wynosi ponad 100%, rozmiar danych, które mają być buforowane, zgodnie z zasadami buforowania, jest większy niż całkowity rozmiar pamięci podręcznej w klastrze. |
    | Procesor CPU | Procent | Średnia, maks., minimum | Procent przyznanych zasobów obliczeniowych, które są obecnie używane przez maszyny w klastrze. Średni procesor CPU wynoszący 80% lub mniej jest trwały dla klastra. Maksymalna wartość procesora CPU to 100%, co oznacza, że nie ma dodatkowych zasobów obliczeniowych do przetwarzania danych. Gdy klaster nie działa prawidłowo, sprawdź maksymalną wartość procesora CPU, aby określić, czy istnieją konkretne procesora CPU, które są blokowane. |
    | Przetworzone zdarzenia (dla Event Hubs) | Licznik | Max, min, sum | Łączna liczba zdarzeń odczytanych z centrów zdarzeń i przetworzonych przez klaster. Zdarzenia są podzielone na zdarzenia odrzucone i zdarzenia akceptowane przez aparat klastra. |
    | Opóźnienie pozyskiwania | Sekundy | Średnia, maks., minimum | Opóźnienie pozyskiwania danych od momentu odebrania danych w klastrze do momentu, aż będzie gotowe do zapytania. Okres opóźnienia pozyskiwania zależy od scenariusza pozyskiwania. |
    | Wynik pozyskiwania | Licznik | Licznik | Łączna liczba operacji pozyskiwania zakończonych niepowodzeniem i zakończonych pomyślnie. Użyj **dzielenia** , aby utworzyć zasobniki sukcesów i niepowodzeń oraz analizować Wymiary (**wartość** > **status**).|
    | Wykorzystanie pozyskiwania | Procent | Średnia, maks., minimum | Procent rzeczywistych zasobów używanych do pozyskiwania danych z łącznej liczby przyznanych zasobów w ramach zasad pojemności, aby przeprowadzić pozyskiwanie. Domyślne zasady pojemności nie przekraczają 512 współbieżnych operacji pozyskiwania lub 75% zasobów klastra zainwestowanych w pozyskiwanie. Średnie wykorzystanie pozyskiwania 80% lub mniej jest trwałym stanem klastra. Maksymalna wartość wykorzystania pozyskiwania to 100%, co oznacza, że wszystkie możliwości pozyskiwania klastra są używane, a kolejka pozyskiwania może wynikać z kolejki pozyskiwania. |
    | Wolumin pozyskiwania (w MB) | Licznik | Max, min, sum | Łączny rozmiar danych pozyskanych w klastrze (w MB) przed kompresją. |
    | Utrzymywanie aktywności | Licznik | Śr | Śledzi czas odpowiedzi klastra. W pełni reagujący klaster zwraca wartość 1, a zablokowany lub odłączony klaster zwraca 0. |
    | Czas trwania zapytania | Sekundy | Liczba, średnia, minimum, maksimum, suma | Łączny czas do odebrania wyników zapytania (nie obejmuje opóźnienia sieci). |
    | | | |

    Dodatkowe informacje na temat [obsługiwanych metryk klastrów Eksplorator danych platformy Azure](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Wybierz przycisk **Dodaj metrykę** , aby zobaczyć wiele metryk wykreślonych na tym samym wykresie.
3. Wybierz przycisk **+ Nowy wykres** , aby wyświetlić wiele wykresów w jednym widoku.
4. Użyj selektora czas, aby zmienić zakres czasu (domyślnie: ostatnie 24 godziny).
5. Użyj [ **Dodaj filtr** i **Zastosuj podział** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) dla metryk, które mają wymiary.
6. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby dodać konfigurację wykresu do pulpitów nawigacyjnych, aby można było wyświetlić ją ponownie.
7. Ustaw **nową regułę alertu** , aby wizualizować metryki przy użyciu ustawionych kryteriów. Nowa reguła alertów będzie obejmować zasób docelowy, metrykę, podział i wymiary filtru z wykresu. Zmodyfikuj te ustawienia w [okienku tworzenia reguły alertów](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

Dodatkowe informacje na temat korzystania z [Eksplorator metryk](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>Następne kroki

* [Samouczek: pozyskiwanie i wykonywanie zapytań dotyczących danych monitorowania na platformie Azure Eksplorator danych](/azure/data-explorer/ingest-data-no-code)
* [Monitorowanie operacji pozyskiwania Eksplorator danych platformy Azure przy użyciu dzienników diagnostycznych](/azure/data-explorer/using-diagnostic-logs)
* [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)
