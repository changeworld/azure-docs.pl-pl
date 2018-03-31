---
title: Zadanie skalowanie przy użyciu funkcji usługi Azure Stream Analytics & uczenie maszynowe Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak poprawnie skalowanie zadania usługi analiza strumienia (partycjonowania, ilość SU i inne) przy użyciu funkcji usługi Azure Machine Learning.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 47ce7c5e-1de1-41ca-9a26-b5ecce814743
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: dd6effab3ba0b411131414bd757ffe8cc54e49d2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-functions"></a>Skalowanie zadania usługi analiza strumienia z funkcjami usługi Azure Machine Learning
Często jest to łatwa do ustawiania zadania usługi analiza strumienia i uruchamiania przykładowych danych przy jego użyciu. Co możemy zrobić, gdy będzie trzeba przeprowadzić to samo zadanie z większą ilość danych? Wymaga to nam zrozumieć, jak skonfigurować zadanie usługi Stream Analytics, aby go skaluje. W tym dokumencie możemy skupić się na specjalne aspektów skalowanie zadania usługi analiza strumienia z funkcjami usługi Machine Learning. Aby uzyskać informacje dotyczące skalowania zadania usługi analiza strumienia ogólnie rzecz biorąc, zobacz artykuł [skalowanie zadania](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co to jest funkcja uczenie maszynowe Azure w Stream Analytics?
Funkcja uczenia maszynowego w Stream Analytics może służyć jak wywołanie funkcji regularne w języku zapytań usługi Stream Analytics. Za sceny, wywołania funkcji są faktycznie Usługa sieci Web systemu Azure Machine Learning żądania. Usługi sieci web usługi Machine Learning obsługuje "przetwarzanie wsadowe" wiele wierszy, nazywanego w tej samej sieci web usługi wywołania interfejsu API, aby zwiększyć ogólną przepustowość minimalna partii. Zobacz następujące artykuły, aby uzyskać więcej informacji; [Funkcji uczenia maszynowego azure Stream Analytics](https://blogs.technet.microsoft.com/machinelearning/2015/12/10/azure-ml-now-available-as-a-function-in-azure-stream-analytics/) i [usługi sieci Web systemu Azure Machine Learning](../machine-learning/studio/consume-web-services.md).

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Skonfiguruj zadania usługi analiza strumienia z funkcjami uczenia maszynowego
Podczas konfigurowania funkcji Machine Learning zadania usługi analiza strumienia, istnieją dwa parametry wziąć pod uwagę, wielkość partii wywołania funkcji uczenia maszynowego i jednostki przesyłania strumieniowego (SUs) udostępniane zadania usługi analiza strumienia. Aby określić odpowiednie wartości dla nich, najpierw należy podjąć decyzję między opóźnienia i przepływności, oznacza to, opóźnienie zadanie usługi Stream Analytics, a przepustowość każdej SU. SUs zawsze można dodać do zadania w celu zwiększenia przepływności również podzielone na partycje zapytań usługi Stream Analytics, mimo że dodatkowe SUs wzrost kosztów wykonywania zadania.

Dlatego jest ważne jest określenie *tolerancji* opóźnienia w uruchomienia zadania usługi analiza strumienia. Rozmiar partii, co oznacza czas oczekiwania w zadaniu Stream Analytics naturalnie zwiększy się opóźnienie dodatkowych z żądaniami obsługi usługi Azure Machine Learning uruchomiony. Z drugiej strony, zwiększyć rozmiar partii umożliwia zadania usługi analiza strumienia do przetworzenia * więcej zdarzeń o *tego samego numeru* z usługi Machine Learning web żądania obsługi. Często wzrost opóźnienia usługi sieci web uczenie maszynowe jest sublinear do zwiększenia rozmiaru partii, dlatego należy wziąć pod uwagę najbardziej ekonomiczne rozmiar wsadu dla usługi sieci web uczenie maszynowe w danej sytuacji. Domyślny rozmiar wsadu dla usługi sieci web żądań wynosi 1000, a można modyfikować przy użyciu [interfejsu API REST usługi analiza strumienia](https://msdn.microsoft.com/library/mt653706.aspx "interfejsu API REST usługi analiza strumienia") lub [klienta programu PowerShell dla strumienia Analiza](stream-analytics-monitor-and-manage-jobs-use-powershell.md "klienta programu PowerShell dla usługi Stream Analytics").

Po określeniu rozmiar partii liczbę przesyłania strumieniowego jednostki (SUs) jest to możliwe, na podstawie liczby zdarzeń, które funkcja musi procesów na sekundę. Aby uzyskać więcej informacji na temat jednostek przesyłania strumieniowego, zobacz [Stream Analytics skalowanie zadania](stream-analytics-scale-jobs.md).

Ogólnie rzecz biorąc są 20 równoczesnych połączeń z usługą sieci web uczenie maszynowe dla każdego 6 SUs z tą różnicą, że zadania SU 1 i 3 zadania SU pobrać 20 równoczesnych połączeń również.  Jeśli szybkość danych wejściowych jest 200 000 zdarzeń na sekundę i rozmiar partii pozostawiono domyślne 1000 wynikowy opóźnienia usługi sieci web z 1000 zdarzeń mini partii jest 200 ms. Oznacza to, że każdy połączenia mogą wysyłać żądania pięć z usługą sieci web Machine Learning na sekundę. W przypadku połączeń 20 zadania usługi analiza strumienia może przetwarzać 20 000 zdarzeń w 200 ms i w związku z tym 100 000 zdarzeń na sekundę. Tak aby przetwarzać 200 000 zdarzeń na sekundę, zadanie usługi analiza strumienia musi 40 równoczesnych połączeń, które do 12 SUs. Na poniższym diagramie przedstawiono żądania zadania usługi analiza strumienia z punktem końcowym usługi sieci web uczenie maszynowe — co 6 SUs ma 20 równoczesnych połączeń usługi sieci web uczenie maszynowe max.

![Skalowanie usługi Stream Analytics przykładu dwa zadania Machine Learning funkcji](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "skali Stream Analytics, Machine Learning funkcje dwa zadania przykładu")

Ogólnie rzecz biorąc ***B*** dla rozmiaru partii ***L*** dla usługi sieci web opóźnienia na rozmiar partii B (w milisekundach), przepływności analiza strumienia zadania z ***N*** SUs jest:

![Skalowanie usługi Stream Analytics formułą funkcji Machine Learning](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "skalowania usługi Stream Analytics formułą funkcji Machine Learning")

Dodatkowego rozważenia może być "max równoczesnych wywołań" po stronie usługi sieci web uczenie maszynowe, zaleca się Ustaw tę opcję na wartość maksymalna (obecnie 200).

Aby uzyskać więcej informacji na temat tego ustawienia Przejrzyj [artykułu skalowanie dla usługi sieci Web usługi Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Przykład — analizy wskaźniki nastrojów klientów
Poniższy przykład zawiera zadania usługi analiza strumienia z analizą wskaźniki nastrojów klientów funkcji Machine Learning, zgodnie z opisem w [samouczek integracji usługi Stream Analytics Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Zapytanie jest prostą pełni na partycje query następuje **wskaźniki nastrojów klientów** funkcji, jak pokazano poniżej:

    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery

Rozważmy poniższy scenariusz; o przepustowości 10 000 tweetów na sekundę zadanie usługi Stream Analytics należy utworzyć w celu przeprowadzania analizy wskaźniki nastrojów klientów tweetów (zdarzeń). Przy użyciu funkcji 1 SU, może to zadanie usługi Stream Analytics możliwe do obsługi ruchu? Przy użyciu domyślny rozmiar partii 1000 zadanie powinno być możliwe przeglądanie danych wejściowych. Dalsze funkcja dodana uczenia maszynowego powinna generować ma więcej niż 1 sekunda opóźnienia, czyli ogólne opóźnienie domyślne analizy wskaźniki nastrojów klientów usługi sieci web uczenie maszynowe (z domyślny rozmiar partii 1000). Zadanie Stream Analytics **ogólną** lub czas oczekiwania na trasie będzie zazwyczaj kilka sekund. Zapoznaj się z bardziej szczegółowe do tego zadania usługi analiza strumienia *szczególnie* wywołania funkcji Machine Learning. O rozmiar partii jako 1000, przepustowości 10 000 zdarzeń może potrwać około 10 żądań do usługi sieci web. Nawet w przypadku 1 SU Brak wystarczającej liczby równoczesnych połączeń obsłużyć ten ruch wejściowego.

Ale co zrobić, jeśli występowania zdarzeń wejściowych zwiększa 100 x i zadania usługi analiza strumienia musi teraz przetworzyć 1 000 000 tweetów na sekundę? Dostępne są dwie opcje:

1. Zwiększ rozmiar partii lub
2. Strumień wejściowy do przetwarzania zdarzeń równolegle partycji

Pierwsza opcja zadanie **opóźnienia** zwiększa.

Z drugiej opcji więcej SUs musi do obsługi administracyjnej i w związku z tym generowania więcej jednoczesnych żądań usług sieci web Machine Learning. Oznacza to, że zadanie **koszt** zwiększa.

Załóżmy, że opóźnienie analizy wskaźniki nastrojów klientów usługi sieci web uczenie maszynowe jest 200 ms dla partii zdarzeń 1000 lub poniżej 250 ms dla partii 5000 zdarzeń, 300 ms dla partii 10 000 zdarzeń lub 500 ms na partie 25 000 zdarzeń.

1. Przy użyciu opcji pierwszy (**nie** inicjowania obsługi administracyjnej SUs więcej), można zwiększyć rozmiar partii do **25 000**. To z kolei umożliwia zadania przetwarzania 1 000 000 zdarzeń o 20 równoczesnych połączeń z usługą sieci web uczenie maszynowe (z opóźnieniem 500 ms na wywołanie). Dlatego dodatkowe opóźnienia zadania usługi analiza strumienia z powodu żądania funkcji wskaźniki nastrojów klientów do żądania usługi sieci web uczenie maszynowe zostanie zwiększona z **200 ms** do **500 ms**. Jednakże rozmiar partii **nie** można zwiększyć nieograniczonej Machine Learning usług sieci web wymaga rozmiar ładunku żądania 4 MB lub mniejsze usługi sieci web żądania limitu czasu po 100 sekund operacji.
2. Druga opcja, wielkość partii pozostawiono 1000, z opóźnieniem usługi sieci web 200 ms, co 20 równoczesnych połączeń z usługą sieci web będzie mogła zdarzeń procesu 1000 * 20 * 5 = 100 000 na sekundę. Tak aby przetwarzać 1 000 000 zdarzeń na sekundę, zadanie potrzebny 60 SUs. W porównaniu do pierwszej opcji zadanie usługi Stream Analytics spowodowałoby więcej żądania usługi sieci web usługi partia zadań, z kolei generowania zwiększenie kosztów.

Poniżej znajduje się tabela dla zadania Stream Analytics przepustowość dla różnych SUs i rozmiary partii (w liczbie zdarzeń na sekundę).

| rozmiar partii (ML opóźnienia) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUs** |5000 |10 000 |40,000 |60,000 |100,000 |
| **18 SUs** |7500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10 000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300,000 |500,000 |

W razie ma już dobrą znajomość jak działają funkcje uczenia maszynowego w Stream Analytics. Prawdopodobnie również rozumiesz, że zadania usługi analiza strumienia "ściągania" danych ze źródeł danych i każdego "wypychania" zwraca partii zdarzenia dla zadania usługi analiza strumienia do przetworzenia. Jak wpływ model ściągania ten Machine Learning web żądania obsługi?

Zwykle ustawione dla funkcji Machine Learning rozmiar partii nie będzie można dokładnie podzielna przez liczba zdarzeń zwróconych przez każde zadanie usługi analiza strumienia "ściągania". Gdy to występuje usługę sieci web uczenie maszynowe jest wywoływana z partii "częściowej". W tym celu nie nakładu dodatkowych zadań opóźnienia w zdarzeniach łączącego ściągania ściągania.

## <a name="new-function-related-monitoring-metrics"></a>Nowe związanych z funkcji monitorowania metryki
W obszarze monitora w zadaniu Stream Analytics zostały dodane trzy dodatkowe metryki dotyczące funkcji. Są one ŻĄDANIA funkcji, zdarzenia funkcji i ŻĄDANIA funkcji nie powiodło się, jak pokazano na poniższym rysunku.

![Skalowanie analizy strumienia w usłudze Machine Learning funkcje metryki](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "analizy strumienia w usłudze Machine Learning funkcje metryki skalowania")

Są zdefiniowane w następujący sposób:

**Funkcja ŻĄDANIA**: liczba żądań funkcji.

**ZDARZENIA funkcji**: liczba zdarzeń w żądaniach funkcji.

**Liczba NIEUDANYCH ŻĄDAŃ funkcja**: liczba żądań funkcji zakończonych niepowodzeniem.

## <a name="key-takeaways"></a>Takeaways klucza
Podsumowując głównych punktów, aby skalować zadanie usługi Stream Analytics, Machine Learning funkcji, należy rozważyć następujące elementy:

1. Częstotliwość zdarzenia wejściowego
2. Opóźnienie tolerowaną uruchomione zadanie usługi Stream Analytics (i w związku z tym rozmiar partii żądania usługi sieci web uczenie maszynowe)
3. Udostępnione SUs analiza strumienia i liczba żądań usługi sieci web uczenie maszynowe (dodatkowych funkcji koszty związane z)

Pełni partycjonowanej zapytań usługi Stream Analytics została użyta jako przykład. Jeśli potrzebna jest bardziej złożonego zapytania [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics) jest doskonałą pomocą podczas pobierania uzyskać dodatkową pomoc od zespołu usługi Stream Analytics.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat usługi Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
