---
title: Skalowanie funkcji usługi Machine Learning w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób skalować zadania usługi Stream Analytics, korzystających z funkcji usługi Machine Learning, konfigurując jednostki partycjonowania i strumienia.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3d478c2421066c8347622f9064c479bb8255b112
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621748"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-functions"></a>Skalować zadania usługi Stream Analytics przy użyciu funkcji Azure Machine Learning Studio

W tym artykule omówiono sposób wydajne skalowanie zadań usługi Azure Stream Analytics, korzystających z funkcji usługi Azure Machine Learning. Aby uzyskać informacje dotyczące skalowanie zadań usługi Stream Analytics ogólnie rzecz biorąc, zobacz artykuł [skalowanie zadań](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co to jest funkcja usługi Azure Machine Learning w usłudze Stream Analytics?

Funkcji usługi Machine Learning, w usłudze Stream Analytics mogą być używane jak regularnym wywołaniu funkcji w języku zapytań usługi Stream Analytics. W tle jednak te wywołania funkcji są faktycznie żądania usługi sieci Web Azure Machine Learning.

Przez "przetwarzanie wsadowe" wiele wierszy ze sobą w tej samej wywołania interfejsu API usługi sieci web, można zwiększyć przepływność żądania usługi sieci web Machine Learning. Ta metoda grupowania jest nazywany mini usługi batch. Aby uzyskać więcej informacji, zobacz [usług sieci Web programu Azure Machine Learning Studio](../machine-learning/studio/consume-web-services.md). Obsługa usługi Azure Machine Learning Studio w usłudze Stream Analytics jest dostępna w wersji zapoznawczej.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurowanie zadania usługi Stream Analytics przy użyciu funkcji usługi Machine Learning

Istnieją dwa parametry do konfigurowania funkcji usługi Machine Learning, używane przez zadanie usługi Stream Analytics:

* Rozmiar partii wywołań funkcji usługi Machine Learning.
* Liczba jednostek przesyłania strumieniowego (SUs) aprowizowaną dla zadania usługi Stream Analytics.

Aby określić odpowiednie wartości dla SUs, zdecyduj, czy chcesz zoptymalizować opóźnienie zadania usługi Stream Analytics lub przepływność każdy jednostka SU. Usługi SUs zawsze mogą być dodawane do zadania w celu zwiększenia przepływności dobrze podzielonym na partycje zapytań usługi Stream Analytics. Dodatkowe SUs zwiększyć koszt uruchamiania zadania.

Określić opóźnienie *tolerancji* dla zadania usługi Stream Analytics. Zwiększenie rozmiaru partii spowoduje zwiększenie opóźnienia żądania obsługi usługi Azure Machine Learning i czas oczekiwania na zadanie usługi Stream Analytics.

Zwiększenie rozmiaru partii umożliwia zadanie usługi Stream Analytics, aby przetworzyć **więcej wydarzeń** z **ten sam numer** usługi Machine Learning web żądania obsługi. Zwiększenie opóźnienia usługi sieci web Machine Learning jest zwykle sublinear na zwiększenie rozmiaru partii. 

Należy wziąć pod uwagę najbardziej efektywny kosztowo sposób rozmiar partii usługi sieci web Machine Learning w dowolnej sytuacji. Domyślny rozmiar partii dla żądania usługi sieci web to 1000. Możesz zmienić ten domyślny rozmiar przy użyciu [interfejsu API REST usługi Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "interfejsu API REST usługi Stream Analytics") lub [klienta programu PowerShell dla usługi Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Po podjęciu na rozmiar partii, można ustawić liczbę jednostek przesyłania strumieniowego (SUs) na podstawie liczby zdarzeń, które funkcja wymaga do procesu na sekundę. Aby uzyskać więcej informacji na temat jednostek przesyłania strumieniowego, zobacz [usługi Stream Analytics skalować zadania](stream-analytics-scale-jobs.md).

Co 6 SUs uzyskać 20 równoczesnych połączeń z usługą sieci web Machine Learning. Jednak 1 zadanie SU i 3 zadania SU uzyskać 20 równoczesnych połączeń.  

Jeśli aplikacja generuje 200 000 zdarzeń na sekundę, a rozmiar partii to 1000, wynikłe opóźnienie usługi sieci web jest równy 200 ms. Ten kurs oznacza, że każde połączenie można dokonać pięciu żądań do usługi sieci web Machine Learning co sekundę. Przy użyciu 20 połączeń zadania usługi Stream Analytics może przetwarzać 20 000 zdarzeń w 200 ms i 100 000 zdarzeń na sekundę.

Aby przetwarzać 200 000 zdarzeń na sekundę, zadanie usługi Stream Analytics musi 40 współbieżnych połączeń, które są oferowane do 12 SUs. Na poniższym diagramie przedstawiono żądania z zadania usługi Stream Analytics z punktem końcowym usługi sieci web Machine Learning — co 6 SUs ma 20 równoczesnych połączeń do usługi sieci web Machine Learning w max.

![Skalowanie usługi Stream Analytics przy użyciu przykładu dwa zadania Machine Learning funkcje](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "skalowania Stream Analytics za pomocą przykład dwa zadania Machine Learning funkcji")

Ogólnie rzecz biorąc ***B*** rozmiaru partii ***L*** opóźnienia usługi sieci web na rozmiar partii B (w milisekundach), przepływności usługi Stream Analytics, zadania za pomocą ***N*** SUs jest:

![Stream Analytics z usługą Machine Learning funkcje formułę skalowania](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Stream Analytics z usługą Machine Learning funkcje formułę skalowania")

Można również skonfigurować "max współbieżnych wywołań" w usłudze sieci web Machine Learning. Zaleca się Ustaw ten parametr na wartość maksymalną (obecnie 200).

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

Przeanalizujmy konfiguracji niezbędne do utworzenia zadania usługi Stream Analytics, w których analiza tonacji ma tweetów w wysokości 10 000 tweety na sekundę.

Za pomocą polecenia SU 1, można tego zadania usługi Stream Analytics obsługuje ruch? Zadania mogą kontaktować się z danymi wejściowymi, korzystając z domyślnego rozmiaru partii 1000. Opóźnienie domyślne analizę tonacji usługę internetową Machine Learning (przy użyciu usługi batch domyślny rozmiar wynosi 1000) tworzy nie więcej niż 1 sekunda opóźnienia.

Zadanie usługi Stream Analytics **ogólną** lub opóźnienia end-to-end będzie zazwyczaj kilka sekund. Zapoznaj się z bardziej szczegółowe do tego zadania usługi Stream Analytics, *szczególnie* wywołania funkcji usługi Machine Learning. Przepływność 10 000 zdarzeń partii o rozmiarze 1000, trwa około 10 żądań usługi sieci web. Nawet w przypadku jednego polecenia SU Brak wystarczającej liczby jednoczesnych połączeń, aby uwzględnić ten ruch danych wejściowych.

Jeśli współczynnik zdarzeń wejściowych zwiększa się o 100 razy, zadanie usługi Stream Analytics musi przetwarzać 1 000 000 tweety na sekundę. Dostępne są dwie opcje w celu zwiększenia skali:

1. Zwiększ rozmiar partii.
2. Partycja strumień wejściowy na potrzeby przetwarzania zdarzeń w sposób równoległy.

Pierwsza opcja zadania **opóźnienie** zwiększa się.

Z drugiej opcji należy aprowizować więcej SUs mieć większą liczbę jednoczesnych żądań usług sieci web Machine Learning. Większa liczba SUs, zwiększa to zadanie **koszt**.

Spójrzmy na skalowania przy użyciu następujące pomiary opóźnienia dla każdego rozmiaru partii:

| Opóźnienie | Rozmiar partii |
| --- | --- |
| 200 ms | partie 1000 zdarzeń lub niższy |
| 250 ms | 5000 zdarzeń partii |
| 300 ms | 10 000 zdarzeń partii |
| 500 ms | 25 000 zdarzeń partii |

1. Przy użyciu pierwszej opcji (**nie** aprowizacji więcej SUs). Można zwiększyć rozmiar partii **25 000**. Zwiększenie rozmiaru partii w ten sposób umożliwi zadania przetwarzania 1 000 000 zdarzeń za pomocą 20 równoczesnych połączeń z usługą sieci web Machine Learning (z opóźnieniem równym 500 ms za wywołania). Dlatego dodatkowe opóźnienie zadania usługi Stream Analytics z powodu żądania funkcji wskaźniki nastrojów klientów dla żądania usługi sieci web Machine Learning zwiększyłoby się ze **200 ms** do **500 ms**. Jednakże, wielkość partii **nie** można zwiększyć, nieskończenie usługi Machine Learning w sieci web wymaga rozmiar ładunku żądania 4 MB lub mniejsze i limit czasu żądania usługi w sieci web po 100 sekund działania.
1. Druga opcja, rozmiar partii pozostanie ustawiony na 1000, przy opóźnieniu usługi sieci web 200 ms, co 20 równoczesnych połączeń z usługą sieci web będą mogli przesyłać zdarzenia procesu 1000 * 20 * 5 = 100 000 na sekundę. Dlatego na potrzeby przetwarzania 1 000 000 zdarzeń na sekundę, zadanie należałoby 60 SUs. W porównaniu do pierwszej opcji, zadanie usługi Stream Analytics czyniłyby więcej żądania usługi sieci web usługi batch, z kolei generowania zwiększenia kosztu.

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

Zwykle rozmiar partii, które możemy ustawić dla funkcji usługi Machine Learning nie będzie dokładnie podzielna przez liczbę zdarzeń, zwracany przez każde zadanie usługi Stream Analytics "pull". W takiej sytuacji usługa sieci web Machine Learning jest wywoływana z partii "częściowej". Za pomocą częściowej partie pozwala uniknąć ponoszenia dodatkowych zadań opóźnienie w zdarzeniach łączącego ściągnięcia ściągnięcia.

## <a name="new-function-related-monitoring-metrics"></a>Nowe metryki monitorowania dotyczące — funkcja
W obszarze monitorowania zadań usługi Stream Analytics zostały dodane trzy dodatkowe metryki dotyczące funkcji. Są one **ŻĄDANIA funkcji**, **zdarzenia funkcji** i **ŻĄDANIA funkcji zakończone NIEPOWODZENIEM**, jak pokazano na poniższym rysunku.

![Skalowanie Stream Analytics z usługą Machine Learning funkcji metryki](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "skalowanie Stream Analytics z usługą Machine Learning funkcji metryki")

Są zdefiniowane w następujący sposób:

**FUNCTION REQUESTS**: Liczba żądań funkcji.

**ZDARZENIA FUNKCJI**: Liczbę zdarzeń w żądaniach funkcji.

**ŻĄDANIA FUNKCJI ZAKOŃCZONE NIEPOWODZENIEM**: Liczba żądań zakończonych niepowodzeniem funkcji.

## <a name="key-takeaways"></a>Najważniejsze wnioski

Aby skalować zadania usługi Stream Analytics, za pomocą funkcji usługi Machine Learning, należy wziąć pod uwagę następujące czynniki:

1. Częstotliwość zdarzeń wejściowych.
2. Tolerowana opóźnienie uruchomionego zadania usługi Stream Analytics (i w związku z tym rozmiar partii żądań usług sieci web Machine Learning).
3. Elastycznie Stream Analytics SUs i liczbę żądań usług sieci web Machine Learning (dodatkowych funkcji koszty związane z).

W pełni podzielonym na partycje zapytań usługi Stream Analytics została użyta jako przykład. W razie potrzeby bardziej złożonego zapytania [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) jest doskonałym zasobem w celu uzyskania dodatkowej pomocy od zespołu usługi Stream Analytics.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat usługi Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
