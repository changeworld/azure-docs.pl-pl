---
title: Jednostki przesyłania strumieniowego w usłudze Azure Stream Analytics
description: W tym artykule opisano ustawienie Jednostki przesyłania strumieniowego i inne czynniki wpływające na wydajność usługi Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 397e455c8b6a1097e2a32473036e1acd2bbdf2eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267354"
---
# <a name="understand-and-adjust-streaming-units"></a>Opis i dostosowywanie jednostek przesyłania strumieniowego

Jednostki przesyłania strumieniowego (SU) reprezentuje zasoby obliczeniowe, które są przydzielane do wykonania zadania usługi Stream Analytics. Im większa liczba jednostek przesyłania strumieniowego, tym większa ilość zasobów procesora i pamięci jest przydzielana do zadania. Ta pojemność pozwala skupić się na logice zapytań i abstrakcji potrzebę zarządzania sprzętem, aby uruchomić zadanie usługi Stream Analytics w odpowiednim czasie.

W celu uzyskania małych opóźnień przetwarzania strumieni całe przetwarzanie dla zadań usługi Azure Stream Analytics jest wykonywane w pamięci. Gdy zabraknie pamięci, zadanie przesyłania strumieniowego kończy się niepowodzeniem. W rezultacie dla zadania produkcyjnego ważne jest monitorowanie użycia zasobów zadania przesyłania strumieniowego i upewnij się, że jest wystarczająco dużo zasobów, aby utrzymać zadania uruchomione 24/7.

Metryka wykorzystania SU %, która waha się od 0% do 100%, opisuje zużycie pamięci obciążenia. W przypadku zadania przesyłania strumieniowego przy minimalnym rozmiarze wskaźnik ten wynosi zwykle od 10% do 20%. Jeśli wykorzystanie SU% jest niska i zdarzenia wejściowe są zaległe, obciążenie prawdopodobnie wymaga więcej zasobów obliczeniowych, co wymaga zwiększenia liczby jednostek SU. Najlepiej jest zachować wskaźnik SU poniżej 80%, aby uwzględnić sporadyczne skoki. Firma Microsoft zaleca ustawienie alertu na metrykę wykorzystania SU 80%, aby zapobiec wyczerpaniu zasobów. Aby uzyskać więcej informacji, zobacz [Samouczek: Konfigurowanie alertów dla zadań usługi Azure Stream Analytics](stream-analytics-set-up-alerts.md).

## <a name="configure-stream-analytics-streaming-units-sus"></a>Konfigurowanie jednostek przesyłania strumieniowego usługi Stream Analytics (SU)
1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/)

2. Na liście zasobów znajdź zadanie usługi Stream Analytics, które chcesz skalować, a następnie otwórz je. 

3. Na stronie zadania w nagłówku **Konfiguruj** wybierz pozycję **Skaluj**. 

    ![Konfiguracja zadania usługi Azure portal Stream Analytics][img.stream.analytics.preview.portal.settings.scale]
    
4. Użyj suwaka, aby ustawić SUs dla zadania. Należy zauważyć, że są ograniczone do określonych ustawień SU. 
5. Można zmienić liczbę SU przypisanych do zadania, nawet gdy jest uruchomiona. Nie jest to możliwe, jeśli zadanie używa [danych wyjściowych nie podzielonych na partycje](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) lub ma [kwerendę wieloetapową z różnymi wartościami PARTITION BY](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values). Być może ograniczone do wyboru z zestawu wartości SU, gdy zadanie jest uruchomione. 

## <a name="monitor-job-performance"></a>Monitorowanie wydajności zadania
Za pomocą witryny Azure portal, można śledzić przepływność zadania:

![Zadania monitorowania usługi Azure Stream Analytics][img.stream.analytics.monitor.job]

Oblicz oczekiwaną przepływność obciążenia. Jeśli przepływność jest mniejsza niż oczekiwano, dostroić partycję wejściową, dostroić kwerendę i dodać SUs do zadania.

## <a name="how-many-sus-are-required-for-a-job"></a>How many SUs are required for a job? (Ile jednostek przesyłania strumieniowego jest wymaganych dla zadania?)

Wybór liczby wymaganych sus dla określonego zadania zależy od konfiguracji partycji dla danych wejściowych i kwerendy, która jest zdefiniowana w zadaniu. Strona **Skala** umożliwia ustawienie odpowiedniej liczby sus. Jest najlepszym rozwiązaniem, aby przydzielić więcej SUs niż jest to potrzebne. Aparat przetwarzania usługi Stream Analytics optymalizuje opóźnienie i przepływność kosztem przydzielania dodatkowej pamięci.

Ogólnie rzecz biorąc, najlepszym rozwiązaniem jest rozpoczęcie od 6 SUs dla kwerend, które nie używają **PARTITION BY**. Następnie określ sweet spot za pomocą metody prób i błędów, w którym można zmodyfikować liczbę SUs po przejściu reprezentatywnych ilości danych i zbadać metrykę wykorzystania SU%. Maksymalna liczba jednostek przesyłania strumieniowego, które mogą być używane przez zadanie usługi Stream Analytics zależy od liczby kroków w kwerendzie zdefiniowanej dla zadania i liczby partycji w każdym kroku. Możesz dowiedzieć się więcej o ograniczeniach [tutaj](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Aby uzyskać więcej informacji na temat wybierania odpowiedniej liczby su, zobacz tę stronę: [Skalowanie zadań usługi Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

> [!Note]
> Wybór liczby jednostek SU są wymagane dla określonego zadania zależy od konfiguracji partycji dla danych wejściowych i kwerendy zdefiniowane dla zadania. Możesz wybrać maksymalnie przydział w SU dla zadania. Domyślnie każda subskrypcja platformy Azure ma przydział do 500 SUs dla wszystkich zadań analizy w określonym regionie. Aby zwiększyć sus dla subskrypcji poza ten przydział, skontaktuj się z [pomocą techniczną firmy Microsoft](https://support.microsoft.com). Prawidłowe wartości dla SUs na zadanie są 1, 3, 6 i do góry w przyrostach 6.

## <a name="factors-that-increase-su-utilization"></a>Factors that increase SU% utilization (Czynniki zwiększające procentowe wykorzystanie jednostek przesyłania strumieniowego) 

Elementy zapytania czasowego (czasochłowego) są podstawowym zestawem stanowych operatorów dostarczanych przez analizę stream analytics. Usługa Stream Analytics zarządza stanem tych operacji wewnętrznie w imieniu użytkownika, zarządzając zużyciem pamięci, punktami kontrolnymi pod kątem odporności i odzyskiwaniem stanu podczas uaktualniania usługi. Mimo że usługa Stream Analytics w pełni zarządza stanami, istnieje wiele zaleceń dotyczących najlepszych rozwiązań, które użytkownicy powinni wziąć pod uwagę.

Należy zauważyć, że zadanie ze złożoną logiką kwerendy może mieć wysokie wykorzystanie SU% nawet wtedy, gdy nie jest stale odbieranie zdarzeń wejściowych. Może się to zdarzyć po nagłym skokiem zdarzeń wejściowych i wyjściowych. Zadanie może nadal obsługiwać stan w pamięci, jeśli kwerenda jest złożona.

Wykorzystanie SU% może nagle spaść do 0 na krótki okres przed powrotem do oczekiwanych poziomów. Dzieje się tak z powodu błędów przejściowych lub uaktualnień inicjowanych przez system. Zwiększenie liczby jednostek przesyłania strumieniowego dla zadania może nie zmniejszyć wykorzystania SU%, jeśli zapytanie nie jest [w pełni równoległe.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)

Porównując wykorzystanie w określonym czasie, użyj [danych szybkości zdarzeń](stream-analytics-monitoring.md). Dane InputEvents i OutputEvents pokazują, ile zdarzeń zostało odczytanych i przetworzonych. Istnieją metryki, które wskazują liczbę zdarzeń błędów, jak również, takich jak błędy deserializacji. Gdy liczba zdarzeń na jednostkę czasu wzrasta, SU% zwiększa się w większości przypadków.

## <a name="stateful-query-logicin-temporal-elements"></a>Logika kwerendy stanowej w elementach czasowych
Jedną z unikatowych możliwości zadania usługi Azure Stream Analytics jest wykonywanie przetwarzania stanowego, takiego jak agregaty okienne, sprzężenia czasowe i funkcje analityczne czasowe. Każdy z tych operatorów przechowuje informacje o stanie.Maksymalny rozmiar okna dla tych elementów kwerendy wynosi siedem dni. 

Koncepcja okna czasowego pojawia się w kilku elementach zapytania usługi Stream Analytics:
1. Agregaty w oknach: GRUPA PRZEZ okna tumbling, hopping i przesuwne

2. Sprzężenia czasowe: DOŁĄCZ z funkcją DATEDIFF

3. Czasowe funkcje analityczne: ISFIRST, LAST i LAG z LIMITEM CZASU TRWANIA

Następujące czynniki wpływają na pamięć używaną (część metryki jednostek przesyłania strumieniowego) przez zadania usługi Stream Analytics:

## <a name="windowed-aggregates"></a>Agregaty okienkowe
Zużyta pamięć (rozmiar stanu) dla agregacji w oknie nie zawsze jest wprost proporcjonalna do rozmiaru okna. Zamiast tego zużyta pamięć jest proporcjonalna do kardynalności danych lub liczby grup w każdym oknie czasu.


Na przykład w poniższej kwerendzie numer `clusterid` skojarzony z jest kardynalność kwerendy. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Aby ograniczyć wszelkie problemy spowodowane przez wysoką kardynalność w poprzedniej kwerendzie, można wysyłać zdarzenia do Centrum zdarzeń podzielonych na partycje przez `clusterid`program , i skalować w poziomie kwerendy, zezwalając systemowi na przetwarzanie każdej partycji wejściowej oddzielnie przy użyciu **partycji BY,** jak pokazano w poniższym przykładzie:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W rezultacie liczba `clusterid` wartości wchodzących do każdego węzła jest zmniejszona, zmniejszając w ten sposób kardynalność grupy przez operatora. 

Partycje Centrum zdarzeń powinny być podzielone na partycje przez klucz grupowania, aby uniknąć konieczności zmniejszenia kroku. Aby uzyskać więcej informacji, zobacz [Omówienie centrów zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md). 

## <a name="temporal-joins"></a>Dokładowe sprzężenia
Zużyta pamięć (rozmiar stanu) sprzężenia czasowego jest proporcjonalna do liczby zdarzeń w pokoju czasowego wiggle sprzężenia, czyli szybkości wprowadzania zdarzeń pomnożonej przez rozmiar pokoju. Innymi słowy pamięci używane przez sprzężenia jest proporcjonalna do zakresu czasu DateDiff pomnożone przez średnią szybkość zdarzenia.

Liczba niedopasowanych zdarzeń w sprzężeniu wpływa na wykorzystanie pamięci dla kwerendy. Następujące zapytanie służy do znajdowania wyświetleń reklam, które generują kliknięcia:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

W tym przykładzie możliwe jest, że wiele reklam jest wyświetlanych i niewiele osób klika na nią i jest wymagane, aby zachować wszystkie zdarzenia w oknie czasu. Używana pamięć jest proporcjonalna do wielkości okna i szybkości zdarzeń. 

Aby skorygować tę decyzję, wyślij zdarzenia do Centrum zdarzeń podzielonych na partycje przez klucze sprzężenia (identyfikator w tym przypadku) i skaluj w poziomie kwerendy, zezwalając systemowi na przetwarzanie każdej partycji wejściowej oddzielnie przy użyciu **partycji PRZEZ,** jak pokazano na rysunku:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W rezultacie liczba zdarzeń wchodzących do każdego węzła jest zmniejszona, zmniejszając w ten sposób rozmiar stanu przechowywane w oknie sprzężenia. 

## <a name="temporal-analytic-functions"></a>Funkcje analityczne doczesne
Pamięć zużyta (rozmiar stanu) czasowej funkcji analitycznej jest proporcjonalna do szybkości zdarzenia mnożą się przez czas trwania.Pamięć zużywana przez funkcje analityczne nie jest proporcjonalna do rozmiaru okna, ale raczej liczba partycji w każdym oknie czasu.

Korygowanie jest podobne do sprzężenia czasowego. Kwerendę można skalować w poziomie za pomocą **programu PARTITION BY**. 

## <a name="out-of-order-buffer"></a>Bufor poza kolejnością 
Użytkownik może skonfigurować rozmiar buforu poza kolejnością w okienku konfiguracji kolejność zdarzeń. Bufor jest używany do przechowywania danych wejściowych przez cały czas trwania okna i zmieniania ich kolejności. Rozmiar buforu jest proporcjonalny do szybkości wprowadzania zdarzeń mnożą przez rozmiar okna poza kolejnością. Domyślny rozmiar okna to 0. 

Aby skorygować przepełnienie buforu poza kolejnością, skaluj kwerendę w poziomie przy użyciu **programu PARTITION BY**. Po podzieleniu na partycje zapytanie jest rozmieszczane w wielu węzłach. W rezultacie liczba zdarzeń wchodzących do każdego węzła jest zmniejszona, zmniejszając w ten sposób liczbę zdarzeń w każdym buforze ponownego rzędu. 

## <a name="input-partition-count"></a>Liczba partycji wejściowych 
Każda partycja wejściowa zadania ma bufor. Im większa liczba partycji wejściowych, tym więcej zasobów zużywa zadanie. Dla każdej jednostki przesyłania strumieniowego usługa Azure Stream Analytics może przetwarzać około 1 MB/s danych wejściowych. W związku z tym można zoptymalizować, dopasowując liczbę jednostek strumieniowego usługi Stream Analytics z liczbą partycji w Centrum zdarzeń. 

Zazwyczaj zadanie skonfigurowane z jedną jednostką przesyłania strumieniowego jest wystarczające dla Centrum zdarzeń z dwiema partycjami (co jest minimum dla Centrum zdarzeń). Jeśli Centrum zdarzeń ma więcej partycji, zadanie usługi Stream Analytics zużywa więcej zasobów, ale niekoniecznie używa dodatkowej przepływności zapewnianej przez Centrum zdarzeń. 

W przypadku zadania z 6 jednostkami przesyłania strumieniowego może być potrzebnych 4 lub 8 partycji z Centrum zdarzeń. Jednak uniknąć zbyt wiele zbędnych partycji, ponieważ powoduje to nadmierne użycie zasobów. Na przykład Centrum zdarzeń z 16 partycji lub większe w zadaniu usługi Stream Analytics, który ma 1 jednostki przesyłania strumieniowego. 

## <a name="reference-data"></a>Dane referencyjne 
Dane referencyjne w asa są ładowane do pamięci dla szybkiego wyszukiwania. Przy bieżącej implementacji każda operacja sprzężenia z danymi referencyjnymi przechowuje kopię danych referencyjnych w pamięci, nawet jeśli łączysz się z tymi samymi danymi referencyjnymi wiele razy. W przypadku kwerend z **partition by,** każda partycja ma kopię danych referencyjnych, więc partycje są całkowicie oddzielone. Dzięki efektowi mnożnikowe użycie pamięci może szybko uzyskać bardzo wysoki poziom, jeśli łączysz się z danymi referencyjnymi wiele razy za pomocą wielu partycji.  

### <a name="use-of-udf-functions"></a>Korzystanie z funkcji UDF
Po dodaniu funkcji UDF usługa Azure Stream Analytics ładuje środowisko uruchomieniowe JavaScript do pamięci. Będzie to miało wpływ na SU%.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie równoległych zapytań w usłudze Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skalowanie zadań usługi Azure Stream Analytics w celu zwiększenia przepływności](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
