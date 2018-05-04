---
title: Informacje o i dostosować jednostek przesyłania strumieniowego w programie Azure Stream Analytics
description: W tym artykule opisano ustawienia jednostek przesyłania strumieniowego i innych czynników, które mają wpływ na wydajność w Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/12/2018
ms.openlocfilehash: c96e9825cddd0b60e67cd4752fab8f440ceaae76
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="understand-and-adjust-streaming-units"></a>Informacje o i dostosować jednostek przesyłania strumieniowego

Usługa Azure Stream Analytics agreguje wydajności "waga" uruchamiania zadania do jednostek przesyłania strumieniowego (SUs). Usługi SUs reprezentuje zasoby obliczeniowe, które są używane do wykonania zadania. Jednostki przesyłania strumieniowego to sposób opisywania względnych możliwości obliczeniowych dotyczących przetwarzania na podstawie mieszanego pomiaru wydajności procesora CPU, pamięci i operacji odczytu oraz zapisu. Pojemność pozwala skupić się na logice zapytania i streszczenia konieczność zarządzania sprzętowych dotyczących uruchamiania Twojej usługi analiza strumienia zadania w odpowiednim czasie.

Aby osiągnąć małe opóźnienia przesyłania strumieniowego przetwarzania, zadania usługi analiza strumienia Azure wykonywać przetwarzania w pamięci. Gdy używany jest za mało pamięci, zadanie przesyłania strumieniowego nie powiedzie się. W związku z tym dla zadania produkcji, należy do monitorowania użycia zasobów zadanie przesyłania strumieniowego i upewnij się, że jest za mało zasobów przydzielonych w celu zachowania zadania uruchomione 24/7.

Metryka jest liczbą wartość procentową z zakresu od 0 do 100%. Zadanie przesyłania strumieniowego z minimalnego rozmiaru SU % wykorzystania metryka jest zwykle od 10 do 20%. Najlepiej przechowywać Metryka poniżej 80% do konta okazjonalne maksymalnej. Firma Microsoft zaleca ustawienie alert na 80% wykorzystania SU Metryka zapobiegające wyczerpanie zasobów. Aby uzyskać więcej informacji, zobacz [samouczek: Ustawianie alertów dla zadania usługi analiza strumienia Azure](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurowanie usługi Stream Analytics (SUs) jednostki przesyłania strumieniowego
1. Zaloguj się do [portalu Azure](http://portal.azure.com/)

2. Na liście zasobów można znaleźć zadania usługi analiza strumienia, który chcesz skalować, a następnie otwórz go. 

3. Na stronie zadania w obszarze **Konfiguruj** nagłówek, wybierz **skali**. 

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

## <a name="factors-that-increase-su-utilization"></a>Czynniki, które zwiększają SU % wykorzystania 

Elementy danych czasowych zapytań (zorientowane na czas) są podstawowy zestaw operatorów stanowe udostępniane przez usługi Stream Analytics. Analiza strumienia zarządza stan tych operacji wewnętrznie w imieniu użytkownika, zarządzając zużycie pamięci, tworzenie punktów kontrolnych dla ochrony i odzyskiwania stanu podczas uaktualnienia usługi. Chociaż Stream Analytics w pełni zarządza stany, są liczbę zalecenia należy wziąć pod uwagę użytkowników.

## <a name="stateful-query-logic-in-temporal-elements"></a>Logika stanowe zapytania w elementach danych czasowych
Jednym z unikatowych możliwości zadania usługi analiza strumienia Azure jest przetwarzania stanowe, takie jak agregacjami, sprzężenia danych czasowych i funkcje analityczne danych czasowych. Każdy z tych operatorów przechowuje informacje o stanie. Maksymalny rozmiar okna dla tych elementów zapytania wynosi siedem dni. 

Pojęcie okno danych czasowych pojawia się w kilku elementach zapytań usługi Stream Analytics:
1. Agregacjami: grupy przez z wirowania, skaczące i przedłużanie systemu windows

2. Sprzężenia danych czasowych: dołączanie za pomocą funkcji DATEDIFF

3. Funkcje analityczne danych czasowych: ISFIRST, LAST i opóźnienie z LIMIT czasu trwania

Pamięć używana wpływ następujące czynniki (część przesyłania strumieniowego jednostki Metryka) przez zadania usługi analiza strumienia:

## <a name="windowed-aggregates"></a>Agregacjami
Pamięć używane (rozmiar danych stanu) okna agregacji nie zawsze jest wprost proporcjonalny do rozmiaru okna. Zamiast tego pamięci używane jest proporcjonalny do pod uwagę Kardynalność elementu danych lub liczby grup w każdym przedziale czasu.


Na przykład w następującym zapytaniu numer skojarzony z `clusterid` jest pod uwagę Kardynalność elementu zapytania. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Aby rzecz poprawy funkcjonowania problemy spowodowane dużej kardynalności w poprzednie zapytanie, może wysyłać zdarzenia do Centrum zdarzeń partycjonowanego `clusterid`i skalowania w poziomie zapytania przez system do przetworzenia każdej partycji wejściowych oddzielnie za pomocą **PARTYCJI PRZEZ** jak pokazano w poniższym przykładzie:

   ```sql
   SELECT count(*) 
   FROM PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W rezultacie, liczba `clusterid` wartości wchodzących w każdym węźle, zostanie zmniejszona zawężając Kardynalność grupy operatora. 

Powinien być dzielony na partycje partycji Centrum zdarzeń za pomocą klucza grupowania, aby uniknąć konieczności krok Zmniejsz. Aby uzyskać więcej informacji, zobacz [Przegląd usługi Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Sprzężenia danych czasowych
Ilość pamięci używane (rozmiar danych stanu) sprzężenia danych czasowych jest proporcjonalna do liczby zdarzeń w pomieszczeniu danych czasowych poruszaj sprzężenia, czyli wejściowych występowania zdarzeń wielokrotnie rozmiarem poruszaj miejsca. Innymi słowy pamięci używane przez sprzężenia jest proporcjonalny do zakresu czasu DateDiff pomnożona przez średni czas występowania zdarzeń.

Liczba zdarzeń niedopasowane sprzężenia mają wpływ na wykorzystanie pamięci dla zapytania. Następujące zapytanie służy do znajdowania wyświetleń reklam, które generują kliknięcia:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

W tym przykładzie jest możliwe, że są wyświetlane partii reklam i kliknij kilka osób i jest wymagany do zachowania wszystkich zdarzeń w przedziale czasu. Używana pamięć jest proporcjonalna do wielkości okna i szybkości zdarzeń. 

Aby to skorygować, wysyłania zdarzeń do Centrum zdarzeń partycjonowanego klucze sprzężenia (identyfikator: w tym przypadku) i skalowania w poziomie zapytania przez system do przetworzenia każdej partycji wejściowych oddzielnie za pomocą **PARTITION BY** pokazany:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W związku z tym liczbę zdarzeń wchodzących w każdym węźle, zostanie zmniejszona co zmniejsza rozmiar danych stanu znajdującej się w oknie sprzężenia. 

## <a name="temporal-analytic-functions"></a>Funkcje analityczne danych czasowych
Pamięć używane (rozmiar danych stanu) funkcji analitycznych danych czasowych jest proporcjonalny do występowania zdarzeń mnożenie przez czas trwania. Nie jest proporcjonalny do rozmiaru okna pamięci używane przez funkcje analityczne, ale raczej partycji: liczba w każdym przedziale czasu.

Korygowanie jest podobny do sprzężenia danych czasowych. Możesz skalować w poziomie zapytania za pomocą **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Bufor poza kolejnością 
Użytkownik może skonfigurować rozmiar buforu poza kolejnością, w przypadku porządkowania okienko konfiguracji. Bufor jest używany do przechowywania danych wejściowych na czas trwania okna i ponownie określić ich kolejność. Rozmiar buforu jest proporcjonalny do zdarzeń szybkości wprowadzania mnożenie przez rozmiar okna poza kolejnością. Domyślny rozmiar okna jest 0. 

Korygowanie przepełnienie buforu poza kolejnością, skalowanie w poziomie zapytania za pomocą **PARTITION BY**. Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W związku z tym liczba zdarzeń wchodzących w każdym węźle zostanie zmniejszona, co zmniejsza się liczba zdarzeń w buforze każdej zmiany kolejności. 

## <a name="input-partition-count"></a>Liczba partycji wejściowych 
Każda partycja wprowadzania danych wejściowych zadania ma buforu. Większa liczba partycji wejściowych, więcej zasobów zużywa zadania. Dla każdej jednostki przesyłania strumieniowego usługi Azure Stream Analytics może przetworzyć około 1 MB/s danych wejściowych. W związku z tym można zoptymalizować przez dopasowanie liczba Stream Analytics jednostki o liczbie partycji w Centrum zdarzeń przesyłania strumieniowego. 

Zwykle zadania skonfigurowana z jedną jednostkę przesyłania strumieniowego jest wystarczająca dla Centrum zdarzeń z dwóch partycji (czyli minimalnym do Centrum zdarzeń). Centrum zdarzeń ma więcej partycji, zadanie usługi Stream Analytics wykorzystuje więcej zasobów, ale niekoniecznie używa dodatkowe przepływność udostępniane przez Centrum zdarzeń. 

Dla zadania o 6 jednostki przesyłania strumieniowego może być konieczne 4 lub 8 partycji w Centrum zdarzeń. Jednak unikać zbyt wiele partycji zbędne, ponieważ obciążenie nadmiernego zasobów, która powoduje. Na przykład Centrum zdarzeń z 16 partycji lub większe w zadaniu Stream Analytics, zawierający 1 jednostkę przesyłania strumieniowego. 

## <a name="reference-data"></a>Dane referencyjne 
Dane referencyjne w ASA są ładowane do pamięci w celu szybkiego wyszukiwania. Z bieżąca implementacja każdej operacji tworzenia sprzężenia w przypadku danych referencyjnych przechowuje kopie danych referencyjnych w pamięci, nawet jeśli przyłączysz z tymi samymi danymi odwołania wiele razy. Dla zapytania z **PARTITION BY**, każda partycja zawiera kopię danych referencyjnych, dlatego partycje są całkowicie rozdzielonymi. Z mocą mnożnik użycie pamięci może szybko uzyskać bardzo wysoka, jeśli przyłączysz z danych referencyjnych wiele razy z wieloma partycjami.  

### <a name="use-of-udf-functions"></a>Użycie funkcji UDF
Po dodaniu funkcji UDF Azure Stream Analytics ładuje środowiska wykonawczego języka JavaScript do pamięci. Będzie to miało wpływ na SU %.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie zapytań działania równoległego w Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skalowanie zadania usługi analiza strumienia Azure w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
