---
title: Jak monitorować konta usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak monitorować konto magazynu na platformie Azure za pomocą portalu Azure.
services: storage
documentationcenter: ''
author: tamram
manager: timlt
editor: tysonn
ms.assetid: b83cba7b-4627-4ba7-b5d0-f1039fe30e78
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: tamram
ms.openlocfilehash: ffc7d46bbfa4db47a47e416c395efdfc451cadc1
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
ms.locfileid: "30322917"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitor konta magazynu w portalu Azure

[Analizy usługi Azure Storage](../storage-analytics.md) dostarcza metryki dla wszystkich usług magazynu i dzienniki dla obiektów blob, kolejek i tabel. Można użyć [portalu Azure](https://portal.azure.com) do skonfiguruj dzienniki i metryk, które są rejestrowane dla Twojego konta oraz wykresy, które zapewniają wizualnej reprezentacji danych metryki.

> [!NOTE]
> Brak kosztów związanych z badanie danych monitorowania w portalu Azure. Aby uzyskać więcej informacji, zobacz [analizy magazynu i rozliczeń](/rest/api/storageservices/Storage-Analytics-and-Billing).
>
> Usługa pliki Azure obecnie obsługuje metryki analityka magazynu, ale jeszcze nie obsługuje rejestrowania.
> 
> Aby uzyskać szczegółowy przewodnik przy użyciu analizy magazynu i innych narzędzi do identyfikacji, diagnozowanie i rozwiązywanie problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Magazyn Microsoft Azure](../storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurowanie monitorowania dla konta magazynu

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **kont magazynu**, następnie nazwa konta magazynu, aby otworzyć pulpit nawigacyjny konta.
1. Wybierz **diagnostyki** w **monitorowanie** bloku menu.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)

1. Wybierz **typu** danych metryki dla każdego **usługi** chcesz monitorować i **zasady przechowywania** danych. Można również wyłączyć monitorowanie przez ustawienie **stan** do **poza**.

    ![MonitoringOptions](./media/storage-monitor-storage-account/stg-enable-metrics-01.png)

   Istnieją dwa typy metryk, które można włączyć dla poszczególnych usług, które są domyślnie włączone dla nowego konta magazynu:

   * **Łączny**: umożliwia zbieranie metryk, takich jak wejście/wyjście, dostępności, opóźnienia i Powodzenie wartości procentowe. Te metryki są agregowane dla obiektów blob, kolejek, tabel i usług plików.
   * **Dla interfejsu API**: oprócz agregacji miar, zbiera ten sam zestaw metryki dla każdej operacji magazynu w interfejsie API usługi Azure Storage.

   Aby skonfigurować zasady przechowywania danych, przenieść **przechowywania (dni)** suwak lub wprowadź liczbę dni przechowywania od 1 do 365 danych. Wartość domyślna dla nowych kont magazynu wynosi siedem dni. Jeśli nie chcesz ustawić zasady przechowywania, wprowadź wartość zero. Jeśli nie ma żadnych zasad przechowywania, jest można usunąć dane monitorowania.

   > [!WARNING]
   > Są naliczane, gdy należy ręcznie usunąć dane metryk. System bez ponoszenia kosztów usunięcie danych starych analytics (dane starsze niż zasady przechowywania). Firma Microsoft zaleca ustawienie zasady przechowywania oparte na jak długo mają być przechowywane dane analityczne magazynu dla Twojego konta. Zobacz [co opłat, czy ponosisz, po włączeniu metryk storage?](../common/storage-enable-and-view-metrics.md#what-charges-do-you-incur-when-you-enable-storage-metrics) Aby uzyskać więcej informacji.
   >

1. Po zakończeniu konfiguracji monitorowania, wybierz **zapisać**.

Domyślny zestaw miar jest wyświetlany na wykresach w bloku konto magazynu, a także bloków pojedynczej usługi (obiektu blob, kolejki, tabel i plików). Po aktywowaniu metryki dla usługi może potrwać do godziny danych pojawią się w jego wykresy. Możesz wybrać **Edytuj** na wszystkie metryki wykres [skonfiguruj metrykę, które](#how-to-customize-metrics-charts) są wyświetlane na wykresie.

Zbieranie metryk i rejestrowania można wyłączyć, ustawiając **stan** do **poza**.

> [!NOTE]
> Usługa Azure Storage korzysta [tabeli magazynu](../common/storage-introduction.md#table-storage) do przechowywania metryki dla konta magazynu i magazyny metryki tabel na koncie. Aby uzyskać więcej informacji zobacz. [Jak są przechowywane metryki](../common/storage-analytics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Dostosowanie metryk wykresów

Użyj poniższej procedury, aby wybrać które metryki magazynu, aby wyświetlić na wykresie metryki. 

1. Rozpocznij od wyświetlania wykresu metryki magazynu w portalu Azure. Wykresy można znaleźć na **bloku konto magazynu** i **metryki** bloku dla poszczególnych usług (obiektu blob, kolejki, tabeli, plik).

   W tym przykładzie współpracujemy z poniższej tabeli, który znajduje się w **bloku konto magazynu**:

   ![Wybór wykresu w portalu Azure](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Następnie kliknij w dowolnym miejscu w obrębie wykresu, aby otworzyć **Metryka** bloku. Wybierz **Edytuj wykres** otworzyć **Edytuj wykres** bloku.

   ![Edytuj przycisk Wykres w bloku wykresu](./media/storage-monitor-storage-account/stg-customize-chart-01.png)

1. Na **Edytuj wykres** bloku, wybierz opcję **zakres czasu** miar do wyświetlenia na wykresie i **usługi** (obiektu blob, kolejek, tabeli, pliku) metryki, którego chcesz wyświetlić. Wybraliśmy tutaj wyświetlane w poprzednim tygodniu metryki dla usługi blob:

   ![Wybór zakresu i Usługa czasu w bloku Edytuj wykres](./media/storage-monitor-storage-account/stg-customize-chart-02.png)

1. Wybierz poszczególne **metryki** tak samo, jak gdyby wyświetlane na wykresie, kliknij przycisk **OK**. Na przykład, w tym miejscu możemy zostały wybrane do wyświetlenia *ContainerCount* i *ObjectCount* metryki:

   ![Poszczególne metryki zaznaczenia w bloku Edytuj wykres](./media/storage-monitor-storage-account/stg-customize-chart-03.png)

Ustawienia wykresu nie wpływają na kolekcji, agregacja lub magazynu danych na koncie magazynu, tylko wyświetlanie danych metryk monitorowania.

### <a name="metrics-availability-in-charts"></a>Metryki dostępności na wykresach

Lista zmian dostępne metryki oparte na usługi, która została wybrana w listy rozwijanej, a typ jednostki wykresu jest edytowany. Na przykład można wybrać procent metryk, takich jak *PercentNetworkError* i *PercentThrottlingError* tylko wtedy, gdy edycji wykres wyświetlający jednostki w procentach:

![Wykres procent Błąd żądania w portalu Azure](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Rozdzielczość metryk

Metryki, które wybrano w diagnostyce Określa rozdzielczość metryk, które są dostępne dla Twojego konta:

* **Łączny** monitorowania udostępnia metryk, takich jak wejście/wyjście, dostępności, opóźnienia i Powodzenie wartości procentowe. Te metryki są agregowane z obiektu blob, tabeli, plików i usługi kolejki.
* **Dla interfejsu API** zapewnia bardziej precyzyjną rozpoznawania metryk dostępnych dla operacji magazynu, oprócz wartości zagregowane poziomu usług.

## <a name="configure-metrics-alerts"></a>Konfigurowanie alertów metryk

Można tworzyć alerty informujące o osiągnięto progi dla metryki zasobów magazynu.

1. Aby otworzyć **bloku reguły alertów**, przewiń w dół do **monitorowanie** sekcji **bloku Menu** i wybierz **reguły alertów**.
1. Wybierz **Dodaj alert** otworzyć **dodać regułę alertu** bloku
1. Wybierz **zasobów** (obiektu blob, plików, kolejka, tabela) z listy rozwijanej, a następnie wprowadź **nazwa** i **opis** nowej reguły alertów.
1. Wybierz **Metryka** dla której chcesz dodać alert, alert **warunku**, a **próg**. Próg jednostki typu różnić w zależności od wybrana metryka. Na przykład "count" jest typem jednostki *ContainerCount*, podczas gdy jednostki na potrzeby *PercentNetworkError* Metryka to wartość procentową.
1. Wybierz **okresu**. Metryki, które osiągną lub przekroczą próg w okresie wyzwolenia alertu.
1. (Opcjonalnie) Skonfiguruj **E-mail** i **Webhook** powiadomienia. Aby uzyskać więcej informacji dotyczących elementów webhook, zobacz [skonfigurować elementu webhook na alert metryki Azure](../../monitoring-and-diagnostics/insights-webhooks-alerts.md). Jeśli nie zostanie skonfigurowane powiadomienia e-mail lub elementu webhook, alerty będą wyświetlane tylko w portalu Azure.

!["Dodaj regułę alertu" bloku w portalu Azure](./media/storage-monitor-storage-account/stg-alert-rules-01.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Dodaj wykresy metryk do pulpitu nawigacyjnego portalu

Wykresy metryk usługi Azure Storage dla każdego z kont magazynu można dodać do pulpitu nawigacyjnego portalu.

1. Kliknij przycisk Wybierz **edycji pulpitu nawigacyjnego** podczas wyświetlania pulpitu nawigacyjnego w [portalu Azure](https://portal.azure.com).
1. W **galerii kafelka**, wybierz pozycję **Znajdź Kafelki przez** > **typu**.
1. Wybierz **typu** > **kont magazynu**.
1. W **zasobów**, wybierz konto magazynu metryki, którego chcesz dodać do pulpitu nawigacyjnego.
1. Wybierz **kategorii** > **monitorowania**.
1. Przeciągnij i upuść wykresu kafelka na pulpit nawigacyjny metryki, które mają wyświetlane. Powtórz dla wszystkich metryki którego chciałbyś wyświetlane na pulpicie nawigacyjnym. Na poniższej ilustracji "Obiekty BLOB — łączna liczba żądań" wykresu zostanie wyróżniona, na przykład, ale wszystkich schematów są dostępne do umieszczenia na pulpicie nawigacyjnym.

   ![Galeria kafelka w portalu Azure](./media/storage-monitor-storage-account/stg-customize-dashboard-01.png)
1. Wybierz **gotowe dostosowywanie** górnej części pulpitu nawigacyjnego po zakończeniu dodawania wykresy.

Po dodaniu wykresy do pulpitu nawigacyjnego, można dostosować je zgodnie z opisem w [dostosowanie metryk wykresy](#how-to-customize-metrics-charts).

## <a name="configure-logging"></a>Konfigurowanie rejestrowania

Można zmodyfikować magazynu Azure do zapisywania dzienników diagnostycznych do odczytu, zapisu i usuwania żądań dla obiekt blob, tabel i kolejek usługi. Zasady przechowywania danych, które można ustawić ma również zastosowanie do tych dzienników.

> [!NOTE]
> Usługa pliki Azure obecnie obsługuje metryki analityka magazynu, ale jeszcze nie obsługuje rejestrowania.
>

1. W [portalu Azure](https://portal.azure.com), wybierz pozycję **kont magazynu**, następnie nazwę konta magazynu, aby otworzyć blok konta magazynu.
1. Wybierz **diagnostyki** w **monitorowanie** bloku menu.

    ![Diagnostyka element menu, w obszarze monitorowania w portalu Azure.](./media/storage-monitor-storage-account/stg-enable-metrics-00.png)
    
1. Upewnij się, **stan** ustawiono **na**i wybierz **usług** dla której chcesz włączyć rejestrowanie.

    ![Konfiguruj rejestrowanie w portalu Azure.](./media/storage-monitor-storage-account/stg-enable-logging-01.png)
1. Kliknij pozycję **Zapisz**.

Dzienniki diagnostyczne są zapisywane w kontenerze obiektu blob o nazwie $logs na koncie magazynu. Można wyświetlić dane dziennika za pomocą Eksploratora magazynu, takich jak [Eksploratora magazynu](http://storageexplorer.com), albo programowo z użyciem biblioteki klienta usługi storage lub programu PowerShell.

Informacje o uzyskiwaniu dostępu do kontenera $logs, zobacz [Włączanie rejestrowania magazynu i uzyskiwanie dostępu do danych dziennika](/rest/api/storageservices/enabling-storage-logging-and-accessing-log-data).

## <a name="next-steps"></a>Następne kroki

* Uzyskać więcej informacji na temat [metryki, rejestrowania i rozliczeń](../storage-analytics.md) analizy magazynu.
* [Włącz dane metryk usługi Azure Storage metryki i widoku](../storage-enable-and-view-metrics.md) przy użyciu programu PowerShell i programowo w języku C#.
