---
title: Skalowanie funkcji usługi Machine Learning w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób skalować zadania usługi Stream Analytics, korzystających z funkcji usługi Machine Learning, konfigurując jednostki partycjonowania i strumienia.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: db14f8240dea95eb073a0a653c2798f02fbb7c35
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "67162585"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Skalować zadania usługi Stream Analytics przy użyciu funkcji Azure Machine Learning Studio
Jest bardzo proste zadanie usługi Stream Analytics i wykonaj przykładowe dane. Co możemy zrobić, jeśli zaistnieje taka potrzeba uruchomić to samo zadanie z większą ilość danych? Wymaga to nam dowiedzieć się, jak skonfigurować zadanie usługi Stream Analytics, dzięki czemu zostanie przeprowadzone skalowanie. W tym dokumencie skupimy się na specjalne aspektów skalowanie zadań usługi Stream Analytics przy użyciu funkcji usługi Machine Learning. Aby uzyskać informacje dotyczące skalowanie zadań usługi Stream Analytics ogólnie rzecz biorąc, zobacz artykuł [skalowanie zadań](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co to jest funkcja usługi Azure Machine Learning w usłudze Stream Analytics?
Funkcji usługi Machine Learning, w usłudze Stream Analytics mogą być używane jak regularnym wywołaniu funkcji w języku zapytań usługi Stream Analytics. Za sceną, wywołania funkcji są faktycznie żądania usługi sieci Web Azure Machine Learning Studio. Usługi sieci web Machine Learning obsługuje "przetwarzanie wsadowe" wiele wierszy, o nazwie mini partii, w tej samej sieci web usługi wywołania interfejsu API, aby zwiększyć ogólną przepływność. Aby uzyskać więcej informacji, zobacz [usług sieci Web programu Azure Machine Learning Studio](../machine-learning/studio/consume-web-services.md). Obsługa usługi Azure Machine Learning Studio w usłudze Stream Analytics jest dostępna w wersji zapoznawczej.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurowanie zadania usługi Stream Analytics przy użyciu funkcji usługi Machine Learning
Podczas konfigurowania funkcji usługi Machine Learning, zadania usługi Stream Analytics, istnieją dwa parametry należy wziąć pod uwagę, rozmiaru partii wywołania funkcji usługi Machine Learning i jednostek przesyłania strumieniowego (SUs) aprowizowaną dla zadania usługi Stream Analytics. Aby określić odpowiednie wartości dla SUs, najpierw należy podjąć decyzję między opóźnienia i przepływności, oznacza to, czas oczekiwania na zadanie usługi Stream Analytics i przepływności każdy jednostka SU. SUs zawsze mogą być dodawane do zadania w celu zwiększenia przepływności również podzielone na partycje zapytań usługi Stream Analytics, mimo że dodatkowe SUs zwiększyć koszt uruchamiania zadania.

W związku z tym jest ważne określić *tolerancji* opóźnienia w uruchamianiu zadania usługi Stream Analytics. Naturalnie zwiększy się dodatkowe opóźnienie z uruchomionych żądań usługi Azure Machine Learning o rozmiarze usługi batch, co oznacza czas oczekiwania na zadanie usługi Stream Analytics. Z drugiej strony, zadanie usługi Stream Analytics, aby przetworzyć umożliwia zwiększenie rozmiaru partii * więcej zdarzeń za pomocą *ten sam numer* usługi Machine Learning web żądania obsługi. Zwiększenie opóźnienia usługi sieci web Machine Learning jest często sublinear wzrost o rozmiar partii, więc warto wziąć pod uwagę najbardziej efektywny kosztowo sposób rozmiar partii usługi sieci web Machine Learning w dowolnej sytuacji. Domyślny rozmiar wsadu dla usługi sieci web żądań to 1000, a następnie można zmodyfikować za pomocą [interfejsu API REST usługi Stream Analytics](https://msdn.microsoft.com/library/mt653706.aspx "interfejsu API REST usługi Stream Analytics") lub [klienta programu PowerShell dla Stream Analiza](stream-analytics-monitor-and-manage-jobs-use-powershell.md "klienta programu PowerShell dla usługi Stream Analytics").

Po określeniu rozmiar partii liczbę przesyłania strumieniowego jednostki (SUs) jest to możliwe, na podstawie liczby zdarzeń, które funkcja musi procesu na sekundę. Aby uzyskać więcej informacji na temat jednostek przesyłania strumieniowego, zobacz [usługi Stream Analytics skalować zadania](stream-analytics-scale-jobs.md).

Ogólnie rzecz biorąc są 20 równoczesnych połączeń z usługą sieci web Machine Learning dla każdego 6 SUs z tą różnicą, że jednego zadania SU i 3 zadania SU uzyskać 20 równoczesnych połączeń również.  Na przykład jeśli współczynnik danych wejściowych wynosi 200 000 zdarzeń na sekundę, a rozmiar partii pozostanie domyślnie 1000 wynikłe opóźnienie usługi sieci web z usługą batch mini zdarzenia 1000 jest 200 ms. Oznacza to, że każde połączenie ułatwia pięciu żądań do usługi sieci web Machine Learning na sekundę. Przy użyciu 20 połączeń zadania usługi Stream Analytics może przetwarzać 20 000 zdarzeń w 200 ms i w związku z tym 100 000 zdarzeń na sekundę. Dlatego na potrzeby przetwarzania 200 000 zdarzeń na sekundę, zadanie usługi Stream Analytics musi 40 współbieżnych połączeń, które są oferowane do 12 SUs. Na poniższym diagramie przedstawiono żądania z zadania usługi Stream Analytics z punktem końcowym usługi sieci web Machine Learning — co 6 SUs ma 20 równoczesnych połączeń do usługi sieci web Machine Learning w max.

![Skalowanie usługi Stream Analytics przy użyciu przykładu dwa zadania Machine Learning funkcje](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "skalowania Stream Analytics za pomocą przykład dwa zadania Machine Learning funkcji")

Ogólnie rzecz biorąc ***B*** rozmiaru partii ***L*** opóźnienia usługi sieci web na rozmiar partii B (w milisekundach), przepływności usługi Stream Analytics, zadania za pomocą ***N*** SUs jest:

![Stream Analytics z usługą Machine Learning funkcje formułę skalowania](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics z usługą Machine Learning funkcje formułę skalowania")

Dodatkowe kwestia może być "max współbieżnych wywołań" po stronie usługi sieci web Machine Learning, zaleca się Ustaw tę opcję na wartość maksymalna (obecnie 200).

Aby uzyskać więcej informacji na temat tego ustawienia, przejrzyj [artykułu skalowanie dla usługi sieci Web Machine Learning](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Przykład — analiza tonacji
Poniższy przykład zawiera zadania usługi Stream Analytics, za pomocą analizy opinii funkcji usługi Machine Learning, zgodnie z opisem w [samouczkiem integracji usługi Stream Analytics Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).

Zapytanie jest prosty w pełni na partycje zapytanie, a następnie **tonacji** funkcji, jak pokazano w poniższym przykładzie:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```
Rozważmy następujący scenariusz; przy przepływności 10 000 tweety na sekundę zadania usługi Stream Analytics należy utworzyć w celu przeprowadzania analizy tonacji tweetów (zdarzenia). Za pomocą polecenia SU 1, tego zadania usługi Stream Analytics, będzie mogła obsługiwać ruch? Korzystając z domyślnego rozmiaru partii 1000 zadanie powinno być możliwe na bieżąco z danymi wejściowymi. Dodatkowo dodano funkcji usługi Machine Learning będzie generował nie więcej niż 1 sekunda opóźnienia, czyli ogólnego domyślne opóźnienie analizy tonacji usługę internetową Machine Learning (domyślny rozmiar partii 1000). Zadanie usługi Stream Analytics **ogólną** lub opóźnienia end-to-end będzie zazwyczaj kilka sekund. Zapoznaj się z bardziej szczegółowe do tego zadania usługi Stream Analytics, *szczególnie* wywołania funkcji usługi Machine Learning. Rozmiar partii o 1000, przepływności 10 000 zdarzeń może zająć około 10 żądań do usługi sieci web. Nawet w przypadku jednego polecenia SU Brak wystarczającej liczby jednoczesnych połączeń, aby uwzględnić ten ruch danych wejściowych.

Jeśli współczynnik zdarzeń wejściowych zwiększa się o 100 razy, zadanie usługi Stream Analytics musi przetwarzać 1 000 000 tweety na sekundę. Dostępne są dwie opcje w celu zwiększenia skali:

1. Zwiększ rozmiar partii lub
2. Strumień wejściowy na potrzeby przetwarzania zdarzeń w sposób równoległy partycji

Pierwsza opcja zadania **opóźnienie** zwiększa się.

Z drugiej opcji więcej SUs należałoby do zainicjowania obsługi administracyjnej i w związku z tym generowania większą liczbę jednoczesnych żądań usług sieci web Machine Learning. Oznacza to, że zadanie **koszt** zwiększa się.

Załóżmy, że opóźnienie analizę tonacji usługę internetową Machine Learning jest 200 ms dla partii zdarzeń 1000 lub poniżej 250 ms dla partii 5000 zdarzeń, 300 ms dla partii zdarzeń 10 000 lub 500 ms dla partii 25 000 zdarzeń.

1. Przy użyciu pierwszej opcji (**nie** aprowizacji więcej SUs). Można zwiększyć rozmiar partii **25 000**. Z kolei pozwoliłoby to zadanie do przetworzenia 1 000 000 zdarzeń za pomocą 20 równoczesnych połączeń z usługą sieci web Machine Learning (z opóźnieniem równym 500 ms za wywołania). Dlatego dodatkowe opóźnienie zadania usługi Stream Analytics z powodu żądania funkcji wskaźniki nastrojów klientów dla żądania usługi sieci web Machine Learning zwiększyłoby się ze **200 ms** do **500 ms**. Jednak wielkość partii **nie** można zwiększyć, nieskończenie usługi Machine Learning w sieci web wymaga rozmiar ładunku żądania 4 MB lub mniejsze limit czasu żądania obsługi sieci web na 100 sekund działania.
2. Druga opcja, rozmiar partii pozostanie ustawiony na 1000, przy opóźnieniu usługi sieci web 200 ms, co 20 równoczesnych połączeń z usługą sieci web będą mogli przesyłać zdarzenia procesu 1000 * 20 * 5 = 100 000 na sekundę. Dlatego na potrzeby przetwarzania 1 000 000 zdarzeń na sekundę, zadanie należałoby 60 SUs. W porównaniu do pierwszej opcji, zadanie usługi Stream Analytics czyniłyby więcej żądania usługi sieci web usługi batch, z kolei generowania zwiększenia kosztu.

Poniżej znajduje się tabela przepływności zadania usługi Stream Analytics, dla różnych usług SUs i rozmiary usługi batch (w liczbie zdarzeń na sekundę).

| rozmiar partii (ML opóźnienie) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SUs** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SUs** |5,000 |10 000 |40,000 |60,000 |100,000 |
| **18 SUs** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SUs** |10 000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SUs** |25,000 |50,000 |200,000 |300,000 |500,000 |

W razie powinno mieć już dobrej znajomości sposobu działania funkcji usługi Machine Learning w usłudze Stream Analytics. Prawdopodobnie dodatkowo użytkownik rozumie, że zadania usługi Stream Analytics "pull" dane ze źródeł danych i każda "pull" zwraca partii zdarzeń dla zadania usługi Stream Analytics do przetworzenia. Jak wpływ modelu ściągania usługi Machine Learning internetowego żądań obsługi?

Zwykle rozmiar partii, które możemy ustawić dla funkcji usługi Machine Learning nie będzie dokładnie podzielna przez liczbę zdarzeń, zwracany przez każde zadanie usługi Stream Analytics "pull". W takiej sytuacji usługa sieci web Machine Learning jest wywoływana z partii "częściowej". W ten sposób nie pociągnąć za sobą dodatkowych zadań opóźnienie w zdarzeniach łączącego ściągnięcia ściągnięcia.

## <a name="new-function-related-monitoring-metrics"></a>Nowe metryki monitorowania dotyczące — funkcja
W obszarze monitorowania zadań usługi Stream Analytics zostały dodane trzy dodatkowe metryki dotyczące funkcji. Są one ŻĄDANIA funkcji, zdarzenia funkcji i ŻĄDANIA funkcji nie powiodło się, jak pokazano na poniższym rysunku.

![Skalowanie Stream Analytics z usługą Machine Learning funkcji metryki](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "skalowanie Stream Analytics z usługą Machine Learning funkcji metryki")

Są zdefiniowane w następujący sposób:

**FUNCTION REQUESTS**: Liczba żądań funkcji.

**ZDARZENIA FUNKCJI**: Liczbę zdarzeń w żądaniach funkcji.

**ŻĄDANIA FUNKCJI ZAKOŃCZONE NIEPOWODZENIEM**: Liczba żądań zakończonych niepowodzeniem funkcji.

## <a name="key-takeaways"></a>Najważniejsze wnioski
Aby podsumować główne punkty, aby można było skalować zadania usługi Stream Analytics, za pomocą funkcji usługi Machine Learning, należy rozważyć następujące elementy:

1. Częstotliwość zdarzenia wejściowe
2. Tolerowana opóźnienia dla uruchomionego zadania usługi Stream Analytics (i w związku z tym rozmiar partii żądań usług sieci web Machine Learning)
3. Elastycznie Stream Analytics SUs i liczbę żądań usług sieci web Machine Learning (dodatkowych funkcji koszty związane z)

W pełni podzielonym na partycje zapytań usługi Stream Analytics została użyta jako przykład. W razie potrzeby bardziej złożonego zapytania [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) jest doskonałym zasobem w celu uzyskania dodatkowej pomocy od zespołu usługi Stream Analytics.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat usługi Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
