---
title: Skalowanie funkcji usługi Machine Learning w usłudze Azure Stream Analytics
description: W tym artykule opisano sposób skalowania zadań usługi Stream Analytics korzystających z funkcji uczenia maszynowego, konfigurując jednostki partycjonowania i strumienia.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067005"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>Skalowanie zadania usługi Stream Analytics za pomocą funkcji usługi Azure Machine Learning Studio (klasycznych)

> [!TIP]
> Zdecydowanie zaleca się używanie [plików UDF usługi Azure Machine Learning](machine-learning-udf.md) zamiast usługi Azure Machine Learning Studio (klasyczny) UDF w celu zwiększenia wydajności i niezawodności.

W tym artykule omówiono sposób efektywnego skalowania zadań usługi Azure Stream Analytics korzystających z funkcji usługi Azure Machine Learning. Aby uzyskać informacje na temat skalowania zadań usługi Stream Analytics w ogóle, zobacz artykuł [Skalowanie zadań](stream-analytics-scale-jobs.md).

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>Co to jest funkcja usługi Azure Machine Learning w usłudze Stream Analytics?

Funkcja uczenia maszynowego w usłudze Stream Analytics może być używana jako zwykłe wywołanie funkcji w języku zapytań usługi Stream Analytics. W tle jednak te wywołania funkcji są faktycznie żądania usługi Azure Machine Learning Web Service.

Można zwiększyć przepływność żądań usługi sieci web uczenia maszynowego przez "przetwarzanie wsadowe" wiele wierszy razem w tym samym wywołaniu interfejsu API usługi sieci web. To grupowanie jest nazywane mini-partią. Aby uzyskać więcej informacji, zobacz [Usługi sieci Web usługi Azure Machine Learning Studio (klasyczne).](../machine-learning/studio/consume-web-services.md) Obsługa usługi Azure Machine Learning Studio (klasyczna) w usłudze Stream Analytics jest w wersji zapoznawczej.

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>Konfigurowanie zadania usługi Stream Analytics za pomocą funkcji uczenia maszynowego

Istnieją dwa parametry do skonfigurowania funkcji uczenia maszynowego używanej przez zadanie usługi Stream Analytics:

* Rozmiar partii wywołania funkcji uczenia maszynowego.
* Liczba jednostek przesyłania strumieniowego (SU) aprowizowanych dla zadania usługi Stream Analytics.

Aby określić odpowiednie wartości dla sus, zdecydować, czy chcesz zoptymalizować opóźnienie zadania usługi Stream Analytics lub przepływności każdej SU. SUs zawsze mogą być dodawane do zadania, aby zwiększyć przepływność kwerendy dobrze podzielonych na partycje usługi Stream Analytics. Dodatkowe SUs zwiększyć koszt uruchomienia zadania.

Określ *tolerancję* opóźnień dla zadania usługi Stream Analytics. Zwiększenie rozmiaru partii zwiększy opóźnienie żądań usługi Azure Machine Learning i opóźnienie zadania usługi Stream Analytics.

Zwiększenie rozmiaru partii umożliwia zadanie usługi Stream Analytics do przetwarzania **większej liczby zdarzeń** z taką **samą liczbą** żądań usługi sieci web uczenia maszynowego. Wzrost opóźnienia usługi sieci web usługi uczenia maszynowego jest zwykle podliniowy do zwiększenia rozmiaru partii. 

Należy wziąć pod uwagę najbardziej opłacalny rozmiar partii dla usługi sieci web uczenia maszynowego w danej sytuacji. Domyślny rozmiar partii dla żądań usługi sieci web wynosi 1000. Ten domyślny rozmiar można zmienić za pomocą [interfejsu API REST usługi Stream Analytics](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "Interfejs API REST usługi Stream Analytics") lub klienta programu [PowerShell dla usługi Stream Analytics](stream-analytics-monitor-and-manage-jobs-use-powershell.md).

Po podjęciu decyzji o rozmiarze partii można ustawić liczbę jednostek przesyłania strumieniowego (SUs), na podstawie liczby zdarzeń, które funkcja musi przetwarzać na sekundę. Aby uzyskać więcej informacji na temat jednostek przesyłania strumieniowego, zobacz [Zadania skalowania usługi Stream Analytics](stream-analytics-scale-jobs.md).

Co 6 SUs uzyskać 20 równoczesnych połączeń z usługą sieci web uczenia maszynowego. Jednak 1 zadanie SU i 3 zadania SU otrzymują 20 równoczesnych połączeń.  

Jeśli aplikacja generuje 200 000 zdarzeń na sekundę, a rozmiar partii wynosi 1000, wynikowe opóźnienie usługi sieci web wynosi 200 ms. Ta szybkość oznacza, że każde połączenie może złożyć pięć żądań do usługi sieci web uczenia maszynowego co sekundę. Dzięki 20 połączeniom zadanie usługi Stream Analytics może przetwarzać 20 000 zdarzeń w 200 ms i 100 000 zdarzeń w ciągu sekundy.

Aby przetworzyć 200 000 zdarzeń na sekundę, zadanie usługi Stream Analytics wymaga 40 równoczesnych połączeń, które wychodzą na 12 sus. Na poniższym diagramie przedstawiono żądania z zadania usługi Stream Analytics do punktu końcowego usługi sieci web uczenia maszynowego — co 6 SU ma 20 równoczesnych połączeń z usługą sieci web uczenia maszynowego na maks.

![Skalowanie analizy strumienia za pomocą funkcji uczenia maszynowego dwa przykłady zadań](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "Skalowanie analizy strumienia za pomocą funkcji uczenia maszynowego dwa przykłady zadań")

Ogólnie rzecz biorąc ***B*** dla rozmiaru partii, ***L*** dla opóźnienia usługi sieci web w rozmiarze partii B w milisekundach, przepływność zadania usługi Stream Analytics z ***N*** SUs jest:

![Skalowanie analizy strumienia za pomocą formuły funkcji uczenia maszynowego](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "Skalowanie analizy strumienia za pomocą formuły funkcji uczenia maszynowego")

Można również skonfigurować "maksymalną liczbę równoczesnych wywołań" w usłudze sieci web uczenia maszynowego. Zaleca się ustawienie tego parametru na wartość maksymalną (obecnie 200).

Aby uzyskać więcej informacji na temat tego ustawienia, zapoznaj się z [artykułem Skalowanie dla usług sieci Web uczenia maszynowego](../machine-learning/studio/scaling-webservice.md).

## <a name="example--sentiment-analysis"></a>Przykład — analiza tonacji
Poniższy przykład zawiera zadanie usługi Stream Analytics z funkcją analizy tonacji Machine Learning, zgodnie z opisem w [samouczku integracji usługi Stream Analytics Machine Learning.](stream-analytics-machine-learning-integration-tutorial.md)

Kwerenda jest prostą kwerendą w pełni podzieloną na partycje, po której następuje funkcja **tonacji,** jak pokazano w poniższym przykładzie:

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

Przeanalizujmy konfigurację niezbędną do utworzenia zadania usługi Stream Analytics, które wykonuje analizę tonacji tweetów z szybkością 10 000 tweetów na sekundę.

Za pomocą 1 SU, to zadanie usługi Stream Analytics obsługiwać ruch? Zadanie może nadążyć za dane wejściowe przy użyciu domyślnego rozmiaru partii 1000. Domyślne opóźnienie usługi sieci web uczenia maszynowego analizy tonacji (o domyślnym rozmiarze partii 1000) powoduje utworzenie nie więcej niż sekundy opóźnienia.

Ogólne **lub** końcowe opóźnienie zadania usługi Stream Analytics to zwykle kilka sekund. Zapoznaj się z tym zadaniem usługi Stream Analytics, *zwłaszcza* wywołaniem funkcji uczenia maszynowego. Przy rozmiarze partii 1000 przepływność 10 000 zdarzeń zajmuje około 10 żądań do usługi sieci web. Nawet w jednym SU, istnieje wystarczająco dużo równoczesnych połączeń, aby pomieścić ten ruch wejściowy.

Jeśli szybkość zdarzenia wejściowego wzrasta o 100x, zadanie usługi Stream Analytics musi przetwarzać 1 000 000 tweetów na sekundę. Istnieją dwie opcje, aby osiągnąć zwiększoną skalę:

1. Zwiększ rozmiar partii.
2. Partycja strumienia wejściowego do przetwarzania zdarzeń równolegle.

Przy pierwszej opcji zwiększa **się opóźnienie** zadania.

Za pomocą drugiej opcji trzeba będzie aprowizować więcej SUs, aby mieć więcej równoczesnych żądań usługi sieci web uczenia maszynowego. Ta większa liczba SUs, zwiększa **koszt**zadania .

Przyjrzyjmy się skalowaniu przy użyciu następujących pomiarów opóźnienia dla każdego rozmiaru partii:

| Opóźnienie | Rozmiar partii |
| --- | --- |
| 200 ms | 1000 partii zdarzeń lub poniżej |
| 250 ms | 5000 partii zdarzeń |
| 300 ms | 10 000 partii zdarzeń |
| 500 ms | 25 000 partii zdarzeń |

1. Przy użyciu pierwszej opcji (**nie** inicjowania obsługi administracyjnej więcej SUs). Wielkość partii może zostać zwiększona do **25 000**. Zwiększenie rozmiaru partii w ten sposób pozwoli zadanie do przetwarzania 1,000,000 zdarzeń z 20 równoczesnych połączeń z usługą sieci web uczenia maszynowego (z opóźnieniem 500 ms na wywołanie). Dlatego dodatkowe opóźnienie zadania usługi Stream Analytics z powodu żądań funkcji tonacji w żądaniach usługi sieci web usługi uczenia maszynowego zostanie zwiększone z **200 ms** do **500 ms**. Jednak rozmiar partii **nie można** zwiększyć nieskończenie, ponieważ usługi sieci web uczenia maszynowego wymaga rozmiaru ładunku żądania być 4 MB lub mniejsze, a żądanie usługi sieci web limit czasu po 100 sekundach pracy.
1. Przy użyciu drugiej opcji, rozmiar partii pozostaje na 1000, z opóźnieniem usługi sieci web 200 ms, co 20 równoczesnych połączeń z usługą sieci web będzie w stanie przetworzyć 1000 * 20 * 5 zdarzeń = 100 000 na sekundę. Aby przetworzyć 1 000 000 zdarzeń na sekundę, zadanie wymagałoby 60 sus. W porównaniu z pierwszą opcją zadanie usługi Stream Analytics spowoduje zwiększenie liczby żądań wsadowych usługi sieci web, co z kolei spowoduje zwiększenie kosztów.

Poniżej znajduje się tabela przepływności zadania usługi Stream Analytics dla różnych sus i rozmiary partii (w liczbie zdarzeń na sekundę).

| rozmiar partii (opóźnienie ML) | 500 (200 ms) | 1000 (200 ms) | 5000 (250 ms) | 10 000 (300 ms) | 25 000 (500 ms) |
| --- | --- | --- | --- | --- | --- |
| **1 su** |2500 |5000 |20 000 |30,000 |50 000 |
| **3 SUs** |2500 |5000 |20 000 |30,000 |50 000 |
| **6 sus** |2500 |5000 |20 000 |30,000 |50 000 |
| **12 sus** |5000 |10 000 |40 000 |60 000 |100 000 |
| **18 sus** |7500 |15 000 |60 000 |90 000 |150 000 |
| **24 sus** |10 000 |20 000 |80 000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 sus** |25 000 |50 000 |200,000 |300,000 |500 000 |

Do tej pory powinieneś już dobrze zrozumieć, jak działa uczenie maszynowe w usłudze Stream Analytics. Prawdopodobnie rozumiesz również, że zadania usługi Stream Analytics "ściągają" dane ze źródeł danych, a każde "ściąganie" zwraca partię zdarzeń dla zadania usługi Stream Analytics do przetworzenia. Jak ten model ściągania wpływa na żądania usługi sieci web usługi uczenia maszynowego?

Zwykle rozmiar partii ustawiony dla funkcji uczenia maszynowego nie będzie dokładnie podzielny przez liczbę zdarzeń zwróconych przez każde zadanie usługi Stream Analytics "pull". W takim przypadku usługa sieci web uczenia maszynowego jest wywoływana z "częściowymi" partiami. Za pomocą częściowych partii pozwala uniknąć ponoszenia dodatkowych zadań opóźnienia obciążenie w scalanie zdarzeń z ciągnąć do ciągnięcia.

## <a name="new-function-related-monitoring-metrics"></a>Nowe metryki monitorowania związane z funkcjami
W obszarze Monitor zadania usługi Stream Analytics dodano trzy dodatkowe metryki związane z funkcjami. Są to **żądania funkcji,** **zdarzenia funkcji** i **nieudane żądania funkcji,** jak pokazano na poniższej grafice.

![Skalowanie analizy strumienia za pomocą metryk funkcji uczenia maszynowego](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "Skalowanie analizy strumienia za pomocą metryk funkcji uczenia maszynowego")

Są zdefiniowane w następujący sposób:

**ŻĄDANIA FUNKCJI:** Liczba żądań funkcji.

**ZDARZENIA FUNKCJI**: Liczba zdarzeń w żądaniach funkcji.

**NIEUDANE ŻĄDANIA FUNKCJI:** Liczba żądań funkcji, które nie powiodły się.

## <a name="key-takeaways"></a>Kluczowe dania na wynos

Aby skalować zadanie usługi Stream Analytics za pomocą funkcji uczenia maszynowego, należy wziąć pod uwagę następujące czynniki:

1. Szybkość zdarzenia wejściowego.
2. Tolerowane opóźnienie dla uruchomionego zadania usługi Stream Analytics (a tym samym rozmiar partii żądań usługi sieci web usługi uczenia maszynowego).
3. Aprowizowana usługa SU usługi Stream Analytics i liczba żądań usługi sieci web usługi uczenia maszynowego (dodatkowe koszty związane z funkcjami).

Jako przykład użyto w pełni podzielonej na partycje kwerendy usługi Stream Analytics. Jeśli potrzebne jest bardziej złożone zapytanie, [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) jest doskonałym źródłem informacji na temat uzyskiwania dodatkowej pomocy od zespołu usługi Stream Analytics.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o usłudze Stream Analytics, zobacz:

* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
