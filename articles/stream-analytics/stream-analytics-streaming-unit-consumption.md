---
title: Opis i dostosowywanie jednostek przesyłania strumieniowego w usłudze Azure Stream Analytics
description: W tym artykule opisano ustawienia jednostki przesyłania strumieniowego i inne czynniki, które mają wpływ na wydajność w usłudze Azure Stream Analytics.
services: stream-analytics
author: JSeb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/17/2019
ms.openlocfilehash: acafd6d8f37edd3e16561a4e588556bb771619f8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206711"
---
# <a name="understand-and-adjust-streaming-units"></a>Opis i dostosowywanie jednostek przesyłania strumieniowego

Przesyłanie strumieniowe jednostki (SUs) reprezentuje zasoby obliczeniowe, które są przydzielane do wykonania zadania. Im większa liczba jednostek przesyłania strumieniowego, tym większa ilość zasobów procesora i pamięci jest przydzielana do zadania. Pojemność pozwala skupić się na logice zapytania i streszczenia konieczność zarządzania sprzętowe do uruchomienia usługi Stream Analytics, zadania w odpowiednim czasie.

W celu uzyskania małych opóźnień przetwarzania strumieni całe przetwarzanie dla zadań usługi Azure Stream Analytics jest wykonywane w pamięci. Gdy używany jest za mało pamięci, zadanie przesyłania strumieniowego nie powiedzie się. W wyniku dla zadania produkcji, ważne jest monitorowanie użycia zasobów zadania przesyłania strumieniowego i upewnij się, Brak wystarczających zasobów przydzielonych do zachowania zadania uruchamiania 24/7.

Metryki wykorzystania SU %, której zakres od 0% do 100%, w tym artykule opisano obciążenia zużycie pamięci. Zadanie przesyłania strumieniowego przy użyciu minimalnego rozmiaru ta metryka jest zwykle od 10 do 20%. Jeśli brakuje % wykorzystania SU i Uzyskaj zaległe zdarzenia wejściowe, prawdopodobnie obciążenie wymaga większej ilości zasobów obliczeniowych, co wymaga zwiększenia liczby usług SUs. Najlepiej przechowywać metryki SU poniżej 80%, aby uwzględnić okazjonalne skoki. Firma Microsoft zaleca ustawienie alertu na 80% wykorzystania SU metrykę, aby zapobiec wyczerpanie zasobów. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie alertów dotyczących zadań usługi Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurowanie usługi Stream Analytics (SUs) jednostki przesyłania strumieniowego
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/)

2. Na liście zasobów Znajdź zadanie usługi Stream Analytics, które chcesz skalować, a następnie otwórz go. 

3. Na stronie zadania w obszarze **Konfiguruj** nagłówka, wybierz **skalowania**. 

    ![Konfiguracja zadania usługi Stream Analytics portalu Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Za pomocą suwaka, aby ustawić SUs dla zadania. Należy zauważyć, że są ograniczone do określonych ustawień SU. 

## <a name="monitor-job-performance"></a>Monitorowanie wydajności zadania
W witrynie Azure portal, można śledzić przepływność zadania:

![Usługa Azure Stream Analytics, monitorowanie zadań][img.stream.analytics.monitor.job]

Obliczanie przepływności oczekiwanego obciążenia. Przepływność jest mniejsze niż oczekiwane, Dostosuj partycję danych wejściowych, ulepszenia zapytania i Dodaj SUs do zadania.

## <a name="how-many-sus-are-required-for-a-job"></a>Ilości jednostek przesyłania strumieniowego są wymagane dla zadania?

Wybór liczby SUs wymagane dla określonego zadania zależy od konfiguracji partycji dla danych wejściowych i zapytania, który jest zdefiniowany w ramach zadania. **Skalowania** strona pozwala na ustawienie odpowiedniej liczby usług SUs. Jest najlepszym rozwiązaniem, aby przydzielić SUs więcej niż jest to potrzebne. Aparat przetwarzania usługi Stream Analytics jest zoptymalizowana opóźnień i przepływności kosztem alokowanie dodatkowej pamięci.

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest rozpoczęcie od 6 SUs dla zapytań, które nie używają **PARTITION BY**. Następnie określ miejsce sweet przy użyciu metody prób i błędów, w którym możesz zmodyfikować liczbę SUs po przekazać reprezentatywnej ilości danych i sprawdzić metryki użycia % SU. Maksymalna liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie usługi Stream Analytics zależy od liczby kroków w zapytaniu, określony dla zadania i liczby partycji w każdym kroku. Dowiedz się więcej na temat limitów [tutaj](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Aby uzyskać więcej informacji o wybieraniu odpowiedniej liczby usług SUs zobacz tę stronę: [Skalowanie zadań usługi Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

> [!Note]
> Wybieranie ilości jednostek przesyłania strumieniowego są wymagane dla określonego zadania zależy od konfiguracji partycji dla danych wejściowych i zapytania zdefiniowanego dla tego zadania. Możesz wybrać do limitu przydziału w SUs do danego zadania. Domyślnie każda subskrypcja platformy Azure ma limit przydziału programu SUs do 500 dla wszystkich zadań analizy w określonym regionie. Aby zwiększyć SUs dla Twojej subskrypcji po przekroczeniu tego limitu przydziału, skontaktuj się z pomocą [Microsoft Support](https://support.microsoft.com). Prawidłowe wartości dla SUs poszczególne zadania są 1, 3, 6 i w przyrostach co 6.

## <a name="factors-that-increase-su-utilization"></a>Czynniki, które zwiększają % wykorzystania SU 

Elementy danych czasowych zapytania (zorientowane na czas) to podstawowy zestaw operatorów stanowych dostarczane przez usługę Stream Analytics. Stream Analytics zarządza stanem tych operacji wewnętrznie w imieniu użytkownika, zarządzając zużycie pamięci, punkty kontrolne dla odporności i odzyskiwania stanu podczas uaktualnienia usługi. Mimo że usługi Stream Analytics w pełni zarządza każdym stany, istnieje kilka zalecenia dotyczące najlepszych rozwiązań, które należy wziąć pod uwagę użytkowników.

Należy zauważyć, że zadanie z logiką złożonego zapytania można wysokiej % wykorzystania SU nawet wtedy, gdy nie jest stale odbierania zdarzeń wejściowych. Może to nastąpić po nagłym skokiem w zdarzeniach wejściowych i wyjściowych. Zadanie może w dalszym ciągu zachować stan pamięci, jeśli zapytanie jest złożone.

% Wykorzystania SU nagle może porzucić 0 na krótko przed powracające do oczekiwanego poziomu. Dzieje się tak z powodu przejściowych błędów lub uaktualnień zainicjowane przez system. Zwiększenie liczby jednostek przesyłania strumieniowego dla zadania nie może zmniejszyć SU % wykorzystania, jeśli zapytanie nie jest [pełni równoległe](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization).

## <a name="stateful-query-logicin-temporal-elements"></a>Logiki zapytania stanowych w elementach danych czasowych
Jednym z unikatowych możliwości zadania usługi Azure Stream Analytics jest wykonanie stanowych przetwarzania, takich jak okresowymi danych czasowych sprzężeń i funkcji analitycznych danych czasowych. Każda z tych operatorów przechowuje informacje o stanie. Maksymalny rozmiar okna dla tych elementów zapytania wynosi siedem dni. 

Koncepcja okno danych czasowych pojawia się w kilku elementów zapytań usługi Stream Analytics:
1. Agregacje okna: GRUPY według z wirowania, przeskokiem i przesuwanie systemu windows

2. Sprzężenia danych czasowych: Dołącz do przy użyciu funkcji DATEDIFF

3. Funkcje analityczne danych czasowych: ISFIRST, LAST i OPÓŹNIENIEM przy użyciu limitu czasu trwania

Następujące czynniki mają wpływ na pamięć używana (część przesyłania strumieniowego metryki jednostek) przez zadania usługi Stream Analytics:

## <a name="windowed-aggregates"></a>Okresowymi
Używana pamięć (rozmiar danych stanu) dla okna agregacji nie zawsze jest wprost proporcjonalny do rozmiaru okna. Zamiast tego używana pamięć jest proporcjonalna do Kardynalność danych lub liczby grup w każdym przedziale czasowym.


Na przykład wysłanie następującego zapytania, liczba skojarzonych `clusterid` to Kardynalność zapytania. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Aby powodowane problemy spowodowane przez wysoką Kardynalność w poprzednim zapytaniu, możesz wysłać zdarzenia do Centrum zdarzeń, które podzielone między `clusterid`i skalowanie zapytań, umożliwiając systemowi przetwarzanie każdej partycji danych wejściowych osobno przy użyciu **PARTYCJI PRZEZ** jak pokazano w poniższym przykładzie:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W wyniku liczbę `clusterid` wartości przychodzących do każdego węzła, zostanie zmniejszona zmniejszając w ten sposób Kardynalność grupy operator. 

Za pomocą klucza grupowania, aby uniknąć konieczności korzystania z krokiem redukcji, powinny być dzielone partycji Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Przegląd usługi Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Sprzężenia danych czasowych
Używana pamięć (rozmiar danych stanu) danych czasowych sprzężenia jest proporcjonalna do liczby zdarzeń w pokoju danych czasowych poruszaj sprzężenia, czyli szybkość danych wejściowych zdarzeń mnożenie przez rozmiar pokoju poruszaj. Oznacza to używana przez sprzężenia pamięć jest proporcjonalna do zakresu czasu DateDiff pomnożona przez średni czas występowania zdarzeń.

Liczba niedopasowanych zdarzeń w sprzężeniu wpływa na użycie pamięci przez program dla zapytania. Następujące zapytanie służy do znajdowania wyświetleń reklam, które generują kliknięcia:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

W tym przykładzie jest możliwe, że wiele reklam wyświetlanych i kliknij kilka osób i jest to wymagane, aby zachować wszystkie zdarzenia w przedziale czasu. Używana pamięć jest proporcjonalna do wielkości okna i szybkości zdarzeń. 

Aby rozwiązać ten problem, wysyłanie zdarzeń do Centrum zdarzeń dzielone według kluczy sprzężenia (identyfikator: w tym przypadku) i skalowania w poziomie zapytania, umożliwiając systemowi przetwarzania poszczególnych partycji danych wejściowych osobno przy użyciu **PARTITION BY** jak pokazano:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W wyniku liczba zdarzeń przychodzących do każdego węzła jest obniżona, zmniejszając w ten sposób rozmiar stanu przechowywane w oknie sprzężenia. 

## <a name="temporal-analytic-functions"></a>Funkcje analityczne danych czasowych
Używana pamięć (rozmiar danych stanu) danych czasowych funkcji analitycznej jest proporcjonalna do występowania zdarzeń wielokrotnie DURATION. Pamięci używane przez funkcje analityczne nie jest proporcjonalny do rozmiaru okna, ale raczej partycji liczba w każdym przedziale czasowym.

Korygowanie jest podobne do łączenia danych czasowych. Można skalować w poziomie zapytania za pomocą **PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Bufor poza kolejnością 
Użytkownik może skonfigurować rozmiar buforu poza kolejnością, w przypadku porządkowania okienko konfiguracji. Rozmiar buforu jest używany do przechowywania danych wejściowych na czas trwania okna, zmienić ich kolejność. Rozmiar buforu jest proporcjonalna do zdarzeń wejściowych szybkość, z mnożenie przez rozmiar okna poza kolejnością. Domyślny rozmiar okna wynosi 0. 

Korygowanie przepełnienia bufora poza kolejnością, skalowanie w poziomie zapytania za pomocą **PARTITION BY**. Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W rezultacie liczba zdarzeń przychodzących do każdego węzła jest mniejsze, zmniejszając liczbę zdarzeń w każdy bufor zmiany kolejności. 

## <a name="input-partition-count"></a>Liczba partycji danych wejściowych 
Każda partycja wprowadzania danych wejściowych zadania ma buforu. Większa liczba partycji danych wejściowych, więcej zasobów zużywa zadania. Dla każdej jednostki przesyłania strumieniowego usługi Azure Stream Analytics może przetwarzać około 1 MB/s danych wejściowych. W związku z tym można zoptymalizować, przez dopasowanie liczby Stream Analytics — przesyłanie strumieniowe jednostki z liczbą partycji w Centrum zdarzeń. 

Zwykle zadania skonfigurowano jedną jednostkę przesyłania strumieniowego jest wystarczająca dla Centrum zdarzeń za pomocą dwóch partycji (jest to minimum potrzebne do Centrum zdarzeń). Jeśli Centrum zdarzeń ma więcej partycji, zadanie usługi Stream Analytics wykorzystuje więcej zasobów, ale niekoniecznie używa dodatkowej przepływności, dostarczone przez Centrum zdarzeń. 

Dla zadania o 6 jednostek przesyłania strumieniowego może być konieczne 4 lub 8 partycje z Centrum zdarzeń. Jednakże Unikaj zbyt dużej liczby partycji niepotrzebne, ponieważ, która powoduje nadmierne użycie zasobów. Na przykład Centrum zdarzeń z 16 partycji lub większe w zadaniu Stream Analytics, które ma 1 jednostka przesyłania strumieniowego. 

## <a name="reference-data"></a>Dane referencyjne 
Dane referencyjne w ASA są ładowane do pamięci w celu szybkiego wyszukiwania. Przy użyciu bieżącej implementacji każda operacja łączenia z danymi referencyjnymi przechowuje kopie danych referencyjnych w pamięci, nawet jeśli dołączysz do tych samych danych odwołania wiele razy. Dla zapytań z **PARTITION BY**, każda partycja zawiera kopię danych referencyjnych, dzięki czemu partycje są całkowicie odłączony. Ze skutkiem mnożnik użycia pamięci może szybko uzyskać bardzo duże, Jeśli dołączysz do programu z danymi referencyjnymi wiele razy przy użyciu wielu partycji.  

### <a name="use-of-udf-functions"></a>Korzystanie z funkcji UDF
Po dodaniu funkcji UDF, Azure Stream Analytics ładuje środowisko uruchomieniowe JavaScript do pamięci. Ma to wpływu na SU %.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie zapytań równoległego w usłudze Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skalowanie zadań usługi Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
