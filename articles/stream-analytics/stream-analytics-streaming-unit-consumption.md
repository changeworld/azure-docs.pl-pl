---
title: 'Usługa Azure Stream Analytics: Zrozumienie i dostosować jednostek przesyłania strumieniowego | Dokumentacja firmy Microsoft'
description: Dowiedz się, jakie czynniki wpłynąć na wydajność w Azure Stream Analytics.
keywords: Jednostka, wydajność zapytań
services: stream-analytics
documentationcenter: ''
author: JSeb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 4312b5f5a64048ad697ae398697da1c59a75e75c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="understand-and-adjust-streaming-units"></a>Informacje o i dostosować jednostek przesyłania strumieniowego

Usługa Azure Stream Analytics agreguje wydajności "waga" uruchamiania zadania do jednostek przesyłania strumieniowego (SUs). Usługi SUs reprezentuje zasoby obliczeniowe, które są używane do wykonania zadania. Jednostki przesyłania strumieniowego to sposób opisywania względnych możliwości obliczeniowych dotyczących przetwarzania na podstawie mieszanego pomiaru wydajności procesora CPU, pamięci i operacji odczytu oraz zapisu. Pozwala to pojemności logiki kwerendy i usuwa z znajomości magazynu warstwy zagadnienia dotyczące wydajności, ręcznie przydzielić pamięci dla zadania i przybliżona core-liczba Procesora wymaganego do uruchomienia zadania w odpowiednim czasie.

Aby osiągnąć małe opóźnienia przesyłania strumieniowego przetwarzania, zadania usługi analiza strumienia Azure wykonywać przetwarzania w pamięci. Gdy używany jest za mało pamięci, zadanie przesyłania strumieniowego nie powiedzie się. W związku z tym dla zadania produkcji, należy do monitorowania użycia zasobów zadanie przesyłania strumieniowego i upewnij się, że jest za mało zasobów przydzielonych w celu zachowania zadania uruchomione 24/7.

Metryka jest liczbą wartość procentową z zakresu od 0 do 100%. Zadanie przesyłania strumieniowego z minimalnego rozmiaru SU % wykorzystania metryka jest zwykle od 10 do 20%. Najlepiej przechowywać Metryka poniżej 80% do konta okazjonalne maksymalnej.  Alert można ustawiać metryka (zobacz [tutaj, aby skonfigurować alerty metryki](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)).



## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurowanie usługi Stream Analytics (SUs) jednostki przesyłania strumieniowego
1. Zaloguj się do [portalu Azure](http://portal.azure.com/)
2. Na liście zasobów można znaleźć zadania usługi analiza strumienia, który chcesz skalować, a następnie otwórz go. 
3. Na stronie zadania w obszarze Konfiguracja, kliknij przycisk **skali**. 

    ![Konfiguracja zadania usługi analiza strumienia portalu Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Aby ustawić SUs zadania za pomocą suwaka. Zwróć uwagę, że jest ograniczona do określonych ustawień SU. 


## <a name="monitor-job-performance"></a>Monitor wydajności zadania
Przy użyciu portalu Azure, można śledzić przepływności zadania:

![Azure Stream Analytics monitorowanie zadań][img.stream.analytics.monitor.job]

Oblicz przepływność oczekiwanego obciążenia. Jeśli przepustowość jest mniejsza niż oczekiwano, dostroić wejściowych partycji, ulepszenia zapytania i dodać SUs do zadania.


## <a name="how-many-sus-are-required-for-a-job"></a>Ile SUs są wymagane w przypadku zadania?

Wybór liczby SUs wymagane dla określonego zadania zależy od konfiguracji partycji dla danych wejściowych i zapytania, która jest zdefiniowana w zadaniu. **Skali** strony umożliwia określenie prawidłowej liczby SUs. Jest najlepszym rozwiązaniem przydzielić SUs więcej niż jest to potrzebne. Aparat przetwarzania Stream Analytics jest zoptymalizowana opóźnienia i przepływności kosztem przydzielania dodatkowych pamięci.

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest rozpoczynać się od 6 SUs zapytań, które nie używają **PARTITION BY**. Następnie określ miejsce słodka przy użyciu metody prób i błędów, w którym można zmodyfikować liczbę SUs po przekazać reprezentatywny ilości danych i sprawdź, czy metryka wykorzystania % SU.

Aby uzyskać więcej informacji na temat wybierania prawidłowej liczby SUs strona ta zostanie wyświetlona: [zadania usługi analiza strumienia Azure skali w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

> [!Note]
> Wybranie liczby SUs są wymagane dla określonego zadania zależy od konfiguracji partycji dla danych wejściowych oraz zapytania zdefiniowanych dla tego zadania. Można wybrać do limitu przydziału w SUs dla zadania. Domyślnie każdej subskrypcji platformy Azure ma limit przydziału wynoszący maksymalnie 200 SUs dla wszystkich zadań analityka w określonym regionie. Aby zwiększyć SUs dla Twojej subskrypcji po przekroczeniu tego przydziału, skontaktuj się z [Microsoft Support](http://support.microsoft.com). Prawidłowe wartości SUs na zadanie to 1, 3, 6 oraz w przyrostach 6.



## <a name="factors-increasing-su-utilization"></a>Czynniki zwiększenie SU % wykorzystania 
### <a name="stateful-query-logic"></a>Logika stanowe zapytania 
Jednym z unikatowych możliwości zadania usługi analiza strumienia Azure jest przetwarzania stanowe, takie jak agregacjami, sprzężenia danych czasowych i funkcje analityczne danych czasowych. Każdy z tych operatorów zachowuje niektóre stanów. 
#### <a name="windowed-aggregates"></a>Agregacjami
Rozmiar stanu okna agregacji jest proporcjonalny do liczby grup (Kardynalność) w grupie przez operatora. Na przykład w następującym zapytaniu numer skojarzony z clusterid jest pod uwagę Kardynalność elementu zapytania. 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


Aby można było rzecz poprawy funkcjonowania problemy spowodowane dużej kardynalności w poprzednie zapytanie, może wysyłać zdarzenia do Centrum zdarzeń partycjonowanego '' clusterid'' i skalowania w poziomie zapytania przez system do przetworzenia każdej partycji wejściowych oddzielnie za pomocą **PARTITION BY**  jak pokazano w poniższym przykładzie:


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W związku z tym clusterid wchodzących w każdym węźle jest zmniejszyć liczbę zawężając Kardynalność grupy przez operatora. 

Powinien być dzielony na partycje partycji Centrum zdarzeń za pomocą klucza grupowania, aby uniknąć konieczności krok Zmniejsz. Dodatkowe szczegółowe informacje znajdują się [tutaj](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview). 
#### <a name="temporal-join"></a>Sprzężenia danych czasowych
Rozmiar stanu sprzężenia danych czasowych jest proporcjonalna do liczby zdarzeń w pomieszczeniu danych czasowych poruszaj sprzężenia, czyli wejściowych występowania zdarzeń wielokrotnie rozmiarem poruszaj miejsca. 

Liczba zdarzeń niedopasowane sprzężenia mają wpływ na wykorzystanie pamięci dla zapytania. Następujące zapytanie służy do znajdowania wyświetleń reklam, które generują kliknięcia:

    SELECT clicks.id
    FROM clicks 
    INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

W tym przykładzie jest możliwe, że są wyświetlane partii reklam i kliknij kilka osób i jest wymagany do zachowania wszystkich zdarzeń w przedziale czasu. Używana pamięć jest proporcjonalna do wielkości okna i szybkości zdarzeń. 

Aby to skorygować, wysyłania zdarzeń do Centrum zdarzeń partycjonowanego klucze sprzężenia (identyfikator: w tym przypadku) i skalowania w poziomie zapytania przez system do przetworzenia każdej partycji wejściowych oddzielnie za pomocą **PARTITION BY** pokazany:

    SELECT clicks.id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W związku z tym liczbę zdarzeń wchodzących w każdym węźle, zostanie zmniejszona co zmniejsza rozmiar danych stanu znajdującej się w oknie sprzężenia. 
#### <a name="temporal-analytic-function"></a>Zawierająca dane czasowe funkcji analitycznych
Rozmiar stanu funkcji analitycznych danych czasowych jest proporcjonalny do występowania zdarzeń, mnożenie przez czas trwania. Korygowanie jest podobny do sprzężenia danych czasowych. Możesz skalować w poziomie zapytania za pomocą **PARTITION BY**. 

### <a name="out-of-order-buffer"></a>Bufor poza kolejnością 
Użytkownik może skonfigurować rozmiar buforu poza kolejnością, w przypadku porządkowania okienko konfiguracji. Bufor jest używany do przechowywania danych wejściowych na czas trwania okna i ponownie określić ich kolejność. Rozmiar buforu jest proporcjonalny do zdarzeń szybkości wprowadzania mnożenie przez rozmiar okna poza kolejnością. Domyślny rozmiar okna jest 0. 

Aby to skorygować, skalowanie w poziomie zapytania za pomocą **PARTITION BY**. Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. Jako wynik jest zmniejszyć liczbę zdarzeń w każdym węźle, co zmniejsza się liczba zdarzeń w buforze każdej zmiany kolejności. 

### <a name="input-partition-count"></a>Liczba partycji wejściowych 
Każda partycja wprowadzania danych wejściowych zadania ma buforu. Większa liczba partycji wejściowych, więcej zasobów zużywa zadania. Dla każdego SU Azure Stream Analytics może przetworzyć około 1 MB/s danych wejściowych, dlatego może być odpowiadać ASA SU liczbie o liczbie partycji Centrum zdarzeń. Zwykle zadania 1SU jest wystarczająca dla Centrum zdarzeń z partycjami 2 (czyli minimalnym do Centrum zdarzeń). Jeśli Centrum zdarzeń ma więcej partycji, zadanie ASA wykorzystuje więcej zasobów, ale niekoniecznie używa dodatkowe przepływność udostępniane przez Centrum zdarzeń. W przypadku zadania 6SU może być konieczne 4 lub 8 partycji w Centrum zdarzeń. Przy użyciu Centrum zdarzeń z 16 partycji lub większą 1SU zadania często przyczynia się do użycia zasobów nadmiernego i należy unikać. 

### <a name="reference-data"></a>Dane referencyjne 
Dane referencyjne w ASA są ładowane do pamięci w celu szybkiego wyszukiwania. Z bieżąca implementacja każdej operacji tworzenia sprzężenia w przypadku danych referencyjnych przechowuje kopie danych referencyjnych w pamięci, nawet jeśli przyłączysz z tymi samymi danymi odwołania wiele razy. Dla zapytania z **PARTITION BY**, każda partycja zawiera kopię danych referencyjnych, dlatego partycje są całkowicie rozdzielonymi. Z mocą mnożnik użycie pamięci może szybko uzyskać bardzo wysoka, jeśli przyłączysz z danych referencyjnych wiele razy z wieloma partycjami.  

#### <a name="use-of-udf-functions"></a>Użycie funkcji UDF
Po dodaniu funkcji UDF Azure Stream Analytics ładuje środowiska wykonawczego języka JavaScript do pamięci. Będzie to miało wpływ na SU %.


## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie zapytań działania równoległego w Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skalowanie zadania usługi analiza strumienia Azure w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
