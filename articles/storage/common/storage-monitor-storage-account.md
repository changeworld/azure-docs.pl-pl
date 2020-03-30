---
title: Jak monitorować konto usługi Azure Storage w witrynie Azure portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak monitorować konto magazynu na platformie Azure przy użyciu witryny Azure portal.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 43955950b2fc0544b149c67e3f8df7ca905fafed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268290"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorowanie konta magazynu w witrynie Azure Portal

[Usługa Azure Storage Analytics](storage-analytics.md) udostępnia metryki dla wszystkich usług magazynu i dzienniki obiektów blob, kolejek i tabel. Za pomocą [witryny Azure Portal](https://portal.azure.com) można skonfigurować, które metryki i dzienniki są rejestrowane dla twojego konta i skonfigurować wykresy, które zapewniają wizualne reprezentacje danych metryk. 

Zalecamy przejrzenie [usługi Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (wersja zapoznawcza). Jest to funkcja usługi Azure Monitor, która oferuje kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony widok wydajności, pojemności i dostępności usług Azure Storage. Nie wymaga włączania ani konfigurowania czegokolwiek i można natychmiast wyświetlić te metryki ze wstępnie zdefiniowanych interaktywnych wykresów i innych uwzględnionych wizualizacji.

> [!NOTE]
> Istnieją koszty związane z badaniem danych monitorowania w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Analiza pamięci masowej](storage-analytics.md).
>
> Usługa Azure Files obsługuje obecnie metryki usługi Storage Analytics, ale nie obsługuje jeszcze rejestrowania.
>
> Konta magazynu magazynu bloków obiektów blob w wersji Premium nie obsługują metryk analitycznych magazynu, ale obsługują rejestrowanie. Rejestrowanie można włączyć programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Jeśli chcesz wyświetlić metryki z kontami magazynu obiektów blob o wysokiej wydajności, rozważ użycie [metryk usługi Azure Storage w usłudze Azure Monitor.](storage-metrics-in-azure-monitor.md)
>
> Aby uzyskać szczegółowy przewodnik dotyczący korzystania z analizy magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurowanie monitorowania konta magazynu

1. W [witrynie Azure portal](https://portal.azure.com)wybierz **pozycję Konta usługi Storage**, a następnie nazwę konta magazynu, aby otworzyć pulpit nawigacyjny konta.
1. Wybierz **opcję Diagnostyka** w sekcji **MONITOROWANIE** bloku menu.

    ![MonitoringOpcje](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Wybierz **typ** danych metryk dla każdej **usługi,** którą chcesz monitorować, oraz **zasady przechowywania** danych. Monitorowanie można również wyłączyć, ustawiając **stan** **na Wyłączone**.

    ![MonitoringOpcje](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Aby ustawić zasady przechowywania danych, przesuń suwak **Przechowywanie (dni)** lub wprowadź liczbę dni przechowywania danych z 1 do 365. Wartość domyślna dla nowych kont magazynu to siedem dni. Jeśli nie chcesz ustawiać zasad przechowywania, wprowadź zero. Jeśli nie ma żadnych zasad przechowywania, należy do Ciebie, aby usunąć dane monitorowania.

   > [!WARNING]
   > Opłaty są naliczane podczas ręcznego usuwania danych metryk. Nieaktualne dane analityczne (dane starsze niż zasady przechowywania) są usuwane przez system bez żadnych kosztów. Zalecamy ustawienie zasad przechowywania na podstawie tego, jak długo chcesz przechowywać dane analizy magazynu dla swojego konta. Aby uzyskać więcej informacji, zobacz [Rozliczenia dotyczące metryk magazynu.](storage-analytics-metrics.md#billing-on-storage-metrics)
   >

1. Po zakończeniu konfiguracji monitorowania wybierz pozycję **Zapisz**.

Domyślny zestaw metryk jest wyświetlany na wykresach w bloku konta magazynu, a także w poszczególnych blokach usługi (obiekt blob, kolejka, tabela i plik). Po włączeniu metryki dla usługi, może upłynąć do godziny dla danych, aby pojawić się na wykresach. Możesz wybrać **edytuj** na dowolnym wykresie metrykowym, aby skonfigurować, które dane są wyświetlane na wykresie.

Można wyłączyć zbieranie i rejestrowanie metryk, ustawiając **stan** **na Wyłączone**.

> [!NOTE]
> Usługa Azure Storage używa [magazynu tabel](storage-introduction.md#table-storage) do przechowywania metryk dla konta magazynu i przechowuje metryki w tabelach na koncie. Aby uzyskać więcej informacji, zobacz [Sposób przechowywania danych](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Dostosowywanie wykresów metryk

Poniższa procedura służy do wybierania metryk magazynu do wyświetlenia na wykresie metryk.

1. Zacznij od wyświetlenia wykresu metryki magazynu w witrynie Azure portal. Wykresy można znaleźć w **bloku konta magazynu** i w bloku **Metryki** dla pojedynczej usługi (obiekt blob, kolejka, tabela, plik).

   W tym przykładzie używa następującego wykresu, który pojawia się na **bloku konta magazynu:**

   ![Wybór wykresu w witrynie Azure portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Kliknij dowolne miejsce na wykresie, aby edytować wykres.

1. Następnie wybierz **zakres czasu** metryk do wyświetlenia na wykresie oraz **usługę** (obiekt blob, kolejka, tabela, plik), których metryki mają być wyświetlane. W tym miejscu metryki z ostatniego tygodnia są wybierane do wyświetlania dla usługi obiektów blob:

   ![Zakres czasu i wybór usługi w bloku Edytuj wykres](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Wybierz poszczególne **dane,** które chcesz wyświetlić na wykresie, a następnie kliknij przycisk **OK**.

   ![Wybór poszczególnych metryk w bloku Edytuj wykres](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Ustawienia wykresu nie mają wpływu na zbieranie, agregację ani przechowywanie danych monitorowania na koncie magazynu.

### <a name="metrics-availability-in-charts"></a>Dostępność wskaźników na wykresach

Lista dostępnych danych zmienia się na podstawie wybranej usługi w rozwijanej i typu jednostki edytowanego wykresu. Na przykład można wybrać metryki procentowe, takie jak *PercentNetworkError* i *PercentThrottlingError* tylko wtedy, gdy edytujesz wykres, który wyświetla jednostki w procentach:

![Żądanie wykresu procentowego błędu w witrynie Azure portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Rozdzielczość metryki

Metryki wybrane w **przeglądarce Diagnostyka** określają rozdzielczość metryk dostępnych dla Twojego konta:

* **Monitorowanie agregacji** zapewnia metryki, takie jak przychodzące/wychodzące, dostępność, opóźnienie i procenty sukcesu. Te metryki są agregowane z usług obiektu blob, tabeli, plików i kolejek.
* **Na interfejs API** zapewnia lepszą rozdzielczość, z metryki dostępne dla poszczególnych operacji magazynu, oprócz agregacji poziomu usługi.

## <a name="configure-metrics-alerts"></a>Konfigurowanie alertów dotyczących metryk

Można utworzyć alerty, aby powiadamiać o osiągnięciu progów dla metryk zasobów magazynu.

1. Aby otworzyć **blok Reguły alertów,** przewiń w dół do sekcji **MONITOROWANIE** **bloku Menu** i wybierz pozycję **Alerty (klasyczne).**
2. Wybierz **dodaj alert metryki (klasyczny),** aby otworzyć blok **Dodaj regułę alertu**
3. Wprowadź **nazwę** i **opis** nowej reguły alertów.
4. Wybierz **metrykę,** dla której chcesz dodać alert, **warunek**alertu i **próg**. Typ jednostki progowej zmienia się w zależności od wybranej metryki. Na przykład "count" jest typem jednostki *containercount*, podczas gdy jednostka dla *metryki PercentNetworkError* jest procentem.
5. Wybierz **okres**. Metryki, które osiągają lub przekraczają próg w okresie wyzwalają alert.
6. (Opcjonalnie) Konfigurowanie powiadomień **poczty e-mail** i **elementu webhook.** Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Konfigurowanie elementu webhook w alertie metryki platformy Azure.](../../azure-monitor/platform/alerts-webhooks.md) Jeśli nie skonfigurujesz powiadomień e-mail lub webhook, alerty będą wyświetlane tylko w witrynie Azure portal.

![Bloku "Dodaj regułę alertu" w witrynie Azure portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Dodawanie wykresów metryk do pulpitu nawigacyjnego portalu

Do pulpitu nawigacyjnego portalu można dodać wykresy metryk usługi Azure Storage dla dowolnego konta magazynu.

1. Wybierz pozycję **Edytuj pulpit nawigacyjny** podczas wyświetlania pulpitu nawigacyjnego w [witrynie Azure portal](https://portal.azure.com).
1. W **Galerii kafelków**wybierz pozycję **Znajdź kafelki według** > **typu**.
1. Wybierz **pozycję Wpisz** > **konta magazynu**.
1. W **obszarze Zasoby**wybierz konto magazynu, którego metryki chcesz dodać do pulpitu nawigacyjnego.
1. Wybierz opcję**Monitorowanie** **kategorii** > .
1. Przeciągnij i upuść kafelek wykresu na pulpit nawigacyjny, aby wyświetlić metrykę, którą chcesz wyświetlić. Powtórz tę czynność dla wszystkich danych, które chcesz wyświetlić na pulpicie nawigacyjnym. Na poniższej ilustracji wykres "Obiekty blob — całkowita liczba żądań" jest wyróżniony jako przykład, ale wszystkie wykresy są dostępne do umieszczenia na pulpicie nawigacyjnym.

   ![Galeria kafelków w witrynie Azure portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Po zakończeniu dodawania wykresów wybierz **opcję Gotowe dostosowywanie** w górnej części pulpitu nawigacyjnego.

Po dodaniu wykresów do pulpitu nawigacyjnego można je dostosować zgodnie z opisem w obszarze Dostosowywanie wykresów metryk.

## <a name="configure-logging"></a>Konfigurowanie rejestrowania

Można poinstruować usługę Azure Storage, aby zapisywała dzienniki diagnostyczne dla żądań odczytu, zapisu i usuwania dla usług obiektów blob, tabeli i kolejek. Zasady przechowywania danych, które można ustawić stosuje się również do tych dzienników.

> [!NOTE]
> Usługa Azure Files obsługuje obecnie metryki usługi Storage Analytics, ale nie obsługuje jeszcze rejestrowania.
>

1. W [witrynie Azure portal](https://portal.azure.com)wybierz **konta magazynu**, a następnie nazwę konta magazynu, aby otworzyć blok konta magazynu.
1. Wybierz **ustawienia diagnostyki (klasyczne)** w sekcji **Monitorowanie (klasyczne)** bloku menu.

    ![Element menu diagnostyki w obszarze MONITOROWANIE w witrynie Azure portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Upewnij się, że **stan** jest ustawiony **na Włączone**i wybierz **usługi,** dla których chcesz włączyć rejestrowanie.

    ![Konfigurowanie rejestrowania w witrynie Azure portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Kliknij przycisk **Zapisz**.

Dzienniki diagnostyczne są zapisywane w kontenerze obiektów blob o nazwie *$logs* na koncie magazynu. Dane dziennika można wyświetlać przy użyciu eksploratora magazynu, takiego jak [Eksplorator magazynu firmy Microsoft,](https://storageexplorer.com)lub programowo przy użyciu biblioteki klienta magazynu lub programu PowerShell.

Aby uzyskać informacje dotyczące uzyskiwania dostępu do kontenera $logs, zobacz [Rejestrowanie analizy magazynu](storage-analytics-logging.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [danych, rejestrowaniu i rozliczeniach](storage-analytics.md) w usłudze Storage Analytics.
