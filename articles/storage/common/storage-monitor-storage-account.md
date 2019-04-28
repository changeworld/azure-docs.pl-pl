---
title: Jak monitorować konto usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować konta magazynu na platformie Azure przy użyciu witryny Azure portal.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/31/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 64cfac0d689df88c4d432e772bcd0a0cc7ab4ade
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478197"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorowanie konta magazynu w witrynie Azure portal

[Analizy usługi Azure Storage](storage-analytics.md) generuje dane pomiarowe dla wszystkich usług magazynu i dzienniki obiektów blob, kolejek i tabel. Możesz użyć [witryny Azure portal](https://portal.azure.com) do skonfigurowania, dzienniki i metryki, które są rejestrowane dla konta usługi i konfigurowanie wykresów, które zapewniają wizualne reprezentacje danych metryk.

> [!NOTE]
> Brak koszty związane z badanie danych monitorowania w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Storage Analytics](storage-analytics.md).
>
> Usługa Azure Files aktualnie obsługuje metryk usługi Storage Analytics, ale nie obsługuje jeszcze rejestrowania.
>
> Aby uzyskać szczegółowy przewodnik na temat korzystania z usługi Storage Analytics i inne narzędzia do identyfikowania, diagnozowanie i rozwiązywanie problemów związanych z usługi Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurowanie monitorowania dla konta magazynu

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **kont magazynu**, następnie nazwę konta magazynu można otworzyć pulpitu nawigacyjnego konta.
1. Wybierz **diagnostyki** w **monitorowanie** części bloku menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Wybierz **typu** danych metryk dla każdego **usługi** chcesz monitorować i **zasady przechowywania** danych. Można również wyłączyć monitorowanie, ustawiając **stan** do **poza**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Aby ustawić zasady przechowywania danych, Przenieś **przechowywania (dni)** suwak lub wprowadź liczbę dni przechowywania od 1 do 365 danych. Wartość domyślna dla nowych kont usługi storage wynosi siedem dni. Jeśli chcesz ustawić zasady przechowywania, wprowadź wartość zero. Jeśli nie ma żadnych zasad przechowywania, to do Ciebie, aby usunąć dane monitorowania.

   > [!WARNING]
   > Opłaty są naliczane, gdy ręcznie usunąć dane metryk. Analizy stare dane (dane starsze niż zasady przechowywania) są usuwane przez system, bez ponoszenia kosztów. Zalecamy ustawienie zasady przechowywania, oparte na jak długo chcesz zachować dane analityczne magazynu dla konta usługi. Zobacz [rozliczenia na metryki magazynu](storage-analytics-metrics.md#billing-on-storage-metrics) Aby uzyskać więcej informacji.
   >

1. Po zakończeniu konfiguracji monitorowania, wybierz **Zapisz**.

Domyślny zestaw metryk jest wyświetlany na wykresach w bloku konto magazynu, a także bloków poszczególnych usług (obiektu blob, kolejki, tabela i plik). Po włączeniu metryki dla usługi może potrwać do godziny dane zostaną wyświetlone w jego przypadku wykresów. Możesz wybrać **Edytuj** na wykresów metryki, aby skonfigurować metryk, które są wyświetlane na wykresie.

Można wyłączyć zbieranie metryk i rejestrowania, ustawiając **stan** do **poza**.

> [!NOTE]
> Usługa Azure Storage korzysta [magazyn tabel](storage-introduction.md#table-storage) do przechowywania metryki dla konta magazynu i magazynami metryki w tabelach w ramach Twojego konta. Aby uzyskać więcej informacji zobacz. [Jak są przechowywane metryki](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Dostosowywanie wykresów metryk

Poniższa procedura umożliwia wybieranie metryk usługi storage, które można wyświetlić na wykresie metryk.

1. Rozpocznij od wyświetlania wykresu metryki magazynu w witrynie Azure portal. Wykresy można znaleźć na **blok konto magazynu** i **metryki** blok dla poszczególnych usług (obiektu blob, kolejka, tabela, plik).

   W tym przykładzie korzysta z poniższej tabeli, który pojawia się na **blok konto magazynu**:

   ![Wybór wykresu w witrynie Azure portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Kliknij w dowolnym miejscu w obrębie wykresu do edytowania wykresu.

1. Następnie wybierz pozycję **zakres czasu** metryki do wyświetlenia na wykresie i **usługi** (obiektu blob, kolejki, tabel, plików) metryki, którego chcesz wyświetlić. W tym miejscu metryki w ostatnim tygodniu są wybrane do wyświetlenia dla usługi blob:

   ![Wybór zakresu i Usługa czasu w bloku Edytuj wykres](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Wybierz poszczególne **metryki** jak były wyświetlane na wykresie, kliknij przycisk **OK**.

   ![Poszczególne wybór metryki w bloku Edytuj wykres](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Ustawienia wykresu nie wpływają na kolekcji, agregacja lub magazynu danych na koncie magazynu z monitorowania.

### <a name="metrics-availability-in-charts"></a>Metryki dostępności, w przypadku wykresów

Lista dostępnych metryk zmienia się zależnie od usługę, w której została wybrana z listy rozwijanej i typ jednostki wykresu edycji. Na przykład, można wybrać wartość procentową metryki, takie jak *wartości PercentNetworkError* i *wartości PercentThrottlingError* tylko wtedy, gdy edytujesz wykres, który wyświetla jednostki w postaci wartości procentowej:

![Żądanie błąd wartość procentową wykresu w witrynie Azure portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Rozpoznawanie metryki

Metryki wybranym **diagnostyki** Określa rozdzielczość metryki, które są dostępne dla Twojego konta:

* **Łączny** monitorowania udostępnia metryki, takie jak wartości procentowych wejście/wyjście, dostępności, opóźnienia i Powodzenie. Te metryki są agregowane z obiektu blob, tabel, plików i kolejki usługi.
* **Na interfejs API** zapewnia bardziej precyzyjną rozwiązania za pomocą metryk dostępnych dla operacji magazynu poza agregacje poziomu usług.

## <a name="configure-metrics-alerts"></a>Konfigurowanie alertów metryk

Możesz utworzyć alerty, aby otrzymywać powiadomienia, gdy zostały osiągnięte progi dla metryk zasobów usługi storage.

1. Aby otworzyć **bloku reguł alertów**, przewiń w dół do **monitorowanie** części **bloku Menu** i wybierz **alerty (klasyczne)**.
2. Wybierz **Dodaj alert dotyczący metryki (wersja klasyczna)** otworzyć **Dodawanie reguły alertów** bloku
3. Wprowadź **nazwa** i **opis** nowej reguły alertu.
4. Wybierz **metryki** dla której chcesz dodać alert, alert **warunek**, a **próg**. Jednostka próg wprowadź zmiany w zależności od wybranej metryki. Na przykład "licznik" jest typem jednostki *ContainerCount*, podczas gdy jednostki dla *wartości PercentNetworkError* metryka jest wartością procentową.
5. Wybierz **okres**. Metryki, które osiągną lub przekroczą próg przed upływem wyzwolenia alertu.
6. (Opcjonalnie) Konfigurowanie **E-mail** i **elementu Webhook** powiadomienia. Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Konfigurowanie elementu webhook dla alertu dotyczącego metryki Azure](../../azure-monitor/platform/alerts-webhooks.md). Jeśli nie zostanie skonfigurowane powiadomienia e-mail lub elementu webhook, alerty będą wyświetlane tylko w witrynie Azure portal.

![Blok "Dodawanie reguły alertów" w witrynie Azure portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Dodawanie wykresów metryk do pulpitu nawigacyjnego w portalu

Możesz dodać wykresy metryk usługi Azure Storage dla każdego konta magazynu do pulpitu nawigacyjnego portalu.

1. Kliknij Wybierz **Edytuj pulpit nawigacyjny** podczas wyświetlania pulpitu nawigacyjnego [witryny Azure portal](https://portal.azure.com).
1. W **Galeria kafelków**, wybierz opcję **Znajdź Kafelki przez** > **typu**.
1. Wybierz **typu** > **kont magazynu**.
1. W **zasobów**, wybierz konto magazynu, którego metryk, które chcesz dodać do pulpitu nawigacyjnego.
1. Wybierz **kategorie** > **monitorowania**.
1. Przeciągnij i upuść wykresu kafelka do pulpitu nawigacyjnego metryki, które mają być wyświetlane. Powtórz dla wszystkich metryk które mają być wyświetlane na pulpicie nawigacyjnym. Na poniższej ilustracji wykres "Obiektami blob — łączna liczba żądań" jest wyróżniony jako przykładu, ale wszystkich wykresach są dostępne do umieszczenia na pulpicie nawigacyjnym.

   ![Galeria kafelków w witrynie Azure portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Wybierz **Zakończono dostosowywanie** w górnej części pulpitu nawigacyjnego, po zakończeniu dodawania wykresów.

Po dodaniu wykresy do pulpitu nawigacyjnego, można dostosować je zgodnie z opisem w Dostosowywanie wykresów metryk.

## <a name="configure-logging"></a>Konfigurowanie rejestrowania

Można nakazać usłudze Azure Storage, aby zapisać dzienniki diagnostyczne do odczytu, zapisu i usuwania żądań dla obiektów blob, tabel i kolejek usługi. Zasady przechowywania danych, gdy ustawiasz dotyczy również tych dzienników.

> [!NOTE]
> Usługa Azure Files aktualnie obsługuje metryk usługi Storage Analytics, ale nie obsługuje jeszcze rejestrowania.
>

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **kont magazynu**, następnie nazwę konta magazynu, aby otworzyć blok konto magazynu.
1. Wybierz **diagnostyki** w **monitorowanie** części bloku menu.

    ![Element menu Diagnostyka w obszarze monitorowanie w witrynie Azure portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Upewnij się, **stan** ustawiono **na**i wybierz **usług** dla której chcesz włączyć rejestrowanie.

    ![Konfigurowanie rejestrowania w witrynie Azure portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Kliknij pozycję **Zapisz**.

Dzienniki diagnostyczne są zapisywane w kontenerze obiektów blob o nazwie *$logs* na koncie magazynu. Można wyświetlić za pomocą Eksploratora magazynu, takich jak dane dziennika [Microsoft Storage Explorer](https://storageexplorer.com), lub programowo przy użyciu biblioteki klienta usługi storage lub programu PowerShell.

Aby uzyskać informacje na temat uzyskiwania dostępu do kontenera $logs, zobacz [rejestrowania danych analizy magazynu](storage-analytics-logging.md).

## <a name="next-steps"></a>Kolejne kroki

* Poznaj więcej szczegółów dotyczących [metryki, rejestrowania i fakturowania](storage-analytics.md) dla analityka magazynu.
