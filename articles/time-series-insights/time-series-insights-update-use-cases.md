---
title: Przypadki użycia usługi Azure Insights serii czasu (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, przypadki użycia Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.custom: seodec18
ms.openlocfilehash: 677d0458acb9fa29f91a7fb849da56cb3cd67678
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707086"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Przypadki użycia w usłudze Azure czas Series Insights w wersji zapoznawczej

Ten artykuł zawiera omówienie kilku typowe przypadki użycia dla usługi Azure czas Series Insights w wersji zapoznawczej. Zalecenia przedstawione w tym artykule służyć jako punkt początkowy, podczas opracowywania aplikacji i rozwiązań za pomocą usługi Time Series Insights.

Po przeczytaniu tego artykułu, należy odpowiedzieć na następujące pytania:

* Jakie są typowe przypadki użycia dla usługi Time Series Insights?
* Jakie są korzyści z używania usługi Time Series Insights do eksploracji danych i wykrywania anomalii visual?
* Jakie są korzyści z używania usługi Time Series Insights do analizy operacyjnej i efektywność procesu?
* Jakie są korzyści z używania usługi Time Series Insights na potrzeby zaawansowanej analizy?

Ten dokument zawiera omówienie przypadki użycia, które Azure czas serii Insights (wersja zapoznawcza) jest przeznaczona dla.

## <a name="introduction"></a>Wprowadzenie

Usługa Time Series Insights to oferta, platform-as-a-service end-to-end. Służy do pozyskiwania, przetwarzania i magazynowania wysoce skontekstualizowanych, zoptymalizowanych pod kątem szeregów czasowych danych środowiska IoT i wykonywania dotyczących ich zapytań. Usługa Time Series Insights jest idealny dla eksploracji danych ad hoc i analizę operacyjną. Usługa Time Series Insights to usługa jednoznacznie rozszerzalne, dostosowany, oferty, że spełnia szerokie musi przemysłowych wdrożenia IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Eksplorowanie danych i wizualne wykrywanie anomalii

Błyskawicznie eksploruj i analizuj miliardy zdarzeń, aby wykrywać anomalie i znajdować ukryte trendy w swoich danych. Usługa Time Series Insights przetwarza obciążenia analityczne usług IoT i DevOps niemalże w czasie rzeczywistym.

![Eksplorator danych][1]

Większość klientów zgadza się, czy czas insight jest między najsilniejszych zasobów usługi Time Series Insights. Usługa Time Series Insights nie wymaga ponoszonych z góry przygotowania danych. Działa ona szybki w celu nawiązania połączenia z miliardami zdarzeń w usłudze Azure IoT Hub lub Azure Event Hubs w ciągu kilku minut. Po nawiązaniu połączenia możesz wizualizować i analizować miliardy zdarzeń można zauważyć anomalie i wykryć ukryte trendy w danych. 

Usługa Time Series Insights jest intuicyjna i łatwa w użyciu. Za interakcję z danymi bez napisania choćby jednego wiersza kodu. Istnieje również nie uczyć się nowego języka. Time Series Insights udostępnia szczegółową oparte na tekście zapytania dla zaawansowanych użytkowników, którzy są zaznajomieni z językiem SQL. Umożliwia także eksploracji wybierz i kliknij dla początkujących użytkowników.

Klienci z zalet prędkości szybkie diagnozowanie problemów związanych z zasobów. Jakie wykonują metodyki DevOps, aby uzyskać dostęp do głównej przyczyny błędów w rozwiązaniach IoT. Mogą również zidentyfikować obszary, które wymaga zbadania pod inicjatyw do nauki o danych.  

Istnieją trzy podstawowe sposoby interakcji z danymi przechowywanymi w usłudze Time Series Insights:

- Jest to pierwszy i najłatwiejszy sposób na rozpoczęcie pracy za pomocą Eksploratora usługi Czas Series Insights w wersji zapoznawczej. Służy on do szybko wizualizować wszystkie dane IoT w jednym miejscu. Zapewnia narzędzia, takie jak mapy cieplnej, aby pomóc w wykrywaniu anomalii w danych. Umożliwia także widoku perspektywy. Umożliwia porównywanie maksymalnie cztery widoki z jednego lub kilku środowisk usługi Time Series Insights na jednym pulpicie nawigacyjnym. Pulpit nawigacyjny zapewnia wgląd w dane szeregów czasowych we wszystkich lokalizacjach. Dowiedz się więcej o [czasu Series Insights w wersji zapoznawczej Eksplorator](./time-series-insights-update-explorer.md). Aby zaplanować środowisko usługi Time Series Insights, przeczytaj [planowania usługi Time Series Insights](./time-series-insights-update-plan.md).

- Drugi sposób na rozpoczęcie jest szybkie osadzić zaawansowane wykresy i diagramy w aplikacji sieci web przy użyciu zestawu SDK języka JavaScript. Przy użyciu zaledwie kilku wierszy kodu umożliwia tworzenie zaawansowanych zapytań. Użyj ich do wypełnienia wykresów liniowych, wykresów kołowych, wykresy słupkowe, map cieplnych, siatek danych i więcej. Wszystkie te elementy istnieje poza pole za pomocą zestawu SDK. Zestaw SDK również przenosi interfejsami API zapytań usługi Time Series Insights. Ich umożliwia tworzenie predykatów podobnego do SQL do wykonywania zapytań o dane, które mają być wyświetlane na pulpicie nawigacyjnym. W przypadku rozwiązania hybrydowe przez warstwę prezentacji usługi Time Series Insights oferuje sparametryzowanych adresów URL. Aby uzyskać szczegółowe informacje o dane zapewniają bezproblemowe łączenie punktów za pomocą Eksploratora usługi Czas Series Insights w wersji zapoznawczej. Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, przeczytaj [biblioteki klienta Javascript Insights serii czasu](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) i [klienta usługi Time Series Insights](https://github.com/Microsoft/tsiclient) dokumentacji. Aby uzyskać więcej informacji o sparametryzowanych adresów URL, przeczytaj [sparametryzowanych adresów URL](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).

- Jest trzeci sposobem na rozpoczęcie korzystania z zaawansowanych interfejsów API wykonywać zapytania względem danych przechowywanych w usłudze Time Series Insights. Usługa Time Series Insights ma operatory czasowe tak, aby imię i nazwisko. Ma ona agregacje i transformacje, takie jak średnia, wartość minimalna, maksymalna, dzielenie przez order by i DateHistogram. Ma także filtrowanie operatory takich jak ma, w programie i, lub większe niż i wyrażenie regularne. Te operatory umożliwiają aplikacjom podrzędne szybko znaleźć interesujące trendów i wzorców w danych. Ich używać, aby wypełnić wizualizacje rozwijane w domu, można zauważyć anomalie.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analiza operacyjna i podnoszenie efektywności procesów

Usługa Time Series Insights umożliwia monitorowanie kondycji, użycia i wydajności sprzętu na dużą skalę. Usługa Time Series Insights zapewnia prosty sposób, aby zmierzyć wydajność operacyjną. Usługa Time Series Insights pomaga zarządzać zróżnicowanymi i nieprzewidywalnymi obciążeniami IoT bez pogarszania wydajności procesów pozyskiwania danych i wykonywania zapytań.

![Omówienie][2]

Przesyłanie strumieniowe i ciągłe przetwarzanie danych pochodzących z procesów operacyjnych pomyślnie przekształcić każdej firmy, jeśli jest połączona z odpowiednią technologię lub rozwiązania. Często rozwiązania te stanowią połączenie wielu systemów. Umożliwiają one eksploracji i analizy danych, ciągle się zmienia się szczególnie w przypadku obszaru IoT, która udostępnia wspólny wzorzec.

Te wzorce często rozpoczyna się z platformami włączone IoT, które pozyskiwania miliardy zdarzeń z urządzeń i czujników, obejmujących wiele różnych ustawień regionalnych. Te systemy przetwarzać i analizować dane przesyłane strumieniowo do uzyskania szczegółowych informacji w czasie rzeczywistym i akcje. Zazwyczaj archiwizowania danych ogrzewanie i zimnego magazynu dla niemal w czasie rzeczywistym i wsadowych analizy. 

Dane gromadzone przechodzi przez szereg przetwarzania do czyszczenia i nakreślić kontekst dla podrzędnego scenariuszach zapytań i analizy. Platforma Azure oferuje bogatej oferty usług, które mogą być stosowane do scenariuszy IoT, takich jak konserwacji zasobów i produkcji. Usługi te obejmują usługi Time Series Insights, usługi IoT Hub, usługa Event Hubs, Azure Stream Analytics, usługi Azure Functions, Azure Logic Apps, usługi Azure Databricks, usługi Azure Machine Learning i usługi Power BI.

Architektura rozwiązania można osiągnąć w następujący sposób:

- Pozyskiwanie danych za pomocą usługi IoT Hub lub centrów zdarzeń zabezpieczeń najlepsze w swojej klasie, przepływności i opóźnień. 
- Wykonaj przetwarzanie danych i obliczeń. Lejkowy pozyskiwanych danych za pośrednictwem usług, takich jak Stream Analytics, Logic Apps i Azure Functions. Usługi, którego używasz, zależy od specyficznych potrzeb przetwarzania danych. 
- Obliczona sygnałów z potoku przetwarzania są przekazywane do usługi Time Series Insights do przechowywania i analizy. 

Usługi Time Series Insights oferuje niemal Eksplorowanie danych w czasie rzeczywistym i na podstawie zasobów szczegółowych informacji na podstawie danych historycznych. W zależności od potrzeb biznesowych zadania MapReduce i Hive można uruchomić na danych przechowywanych w usłudze Time Series Insights, łącząc usługi Time Series Insights do usługi Azure HDInsight. Dane przechowywane w usłudze Time Series Insights jest dostępna dla usługi Power BI i innych aplikacji klienta za pośrednictwem usługi Time Series Insights publicznych powierzchni interfejsami API zapytań. Te dane mogą służyć do biznesowa i scenariusze analizy operacyjnej.

## <a name="advanced-analytics"></a>Analiza zaawansowana

Integracja z zaawansowaną analizę usług, takich jak Machine Learning i Azure Databricks. Time Series Insights ingresses nieprzetworzonych danych pochodzących z milionów urządzeń. Dodaje dane kontekstowe, które mogą być używane w bezproblemowo przez mechanizm usługi analiz platformy Azure.

![Analiza][3]

Zaawansowane funkcje analityczne i uczenia maszynowego korzystać i przetwarzanie dużych ilości danych. Te dane są używane do podejmowania decyzji na podstawie danych i wykonywać analizy predykcyjnej. W przypadki użycia IoT zaawansowane funkcje analityczne algorytmy ucz się od danych zebranych z milionów urządzeń. Tych urządzeń przesyłających dane wielokrotnie w ciągu sekundy. Danych zbieranych z urządzeń IoT jest raw. Mu Informacje kontekstowe, takie jak lokalizacja urządzenia i jednostka odczytu czujnika. W rezultacie danych pierwotnych jest trudny do korzystać bezpośrednio na potrzeby zaawansowanej analizy.

Usługi Time Series Insights Most między danymi IoT i zaawansowane funkcje analityczne na dwa sposoby proste i ekonomiczne: 

- Po pierwsze usługi Time Series Insights zbiera nieprzetworzonych danych telemetrycznych pochodzących z milionów urządzeń przy użyciu usługi IoT Hub. On uzupełnia danych przy użyciu informacji kontekstowych i przekształca dane w formacie parquet. Ten format można łatwo zintegrować z inne zaawansowane funkcje analityczne usług, takich jak Machine Learning, usługi Azure Databricks i aplikacji innych firm. 

    Usługa Time Series Insights może służyć jako źródło prawdziwych danych, dla wszystkich danych w całej organizacji. Tworzy centralne repozytorium podrzędnym analytics obciążeń korzystać. Ponieważ usługa Time Series Insights jest niemal w czasie rzeczywistym magazynu usługi, zaawansowane funkcje analityczne, które modele mogą ucz się stale przychodzących danych telemetrycznych IoT. W rezultacie modele wprowadzać dokładniejszych prognoz.

- Po drugie usługi Time Series Insights może być zasilane dane wyjściowe modele uczenia i przewidywania maszynowego wizualizacji i zapisywanie ich wyników. Ta procedura umożliwia organizacjom do optymalizacji i dostosowanie ich modeli. Usługa Time Series Insights pozwala w prosty sposób wizualizować dane telemetryczne dotyczące tej samej płaszczyźnie przesyłania strumieniowego, zgodnie z danych wyjściowych uczonego modelu. W ten sposób ułatwia zespołów do nauki o danych, wykrywaniu anomalii i zidentyfikować wzorce.  

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [czasu Series Insights w wersji zapoznawczej Eksplorator](./time-series-insights-update-explorer.md).

- Aby zaplanować środowisko, przeczytaj [planowania czasu Series Insights w wersji zapoznawczej](./time-series-insights-update-plan.md).

- Odczyt [klienta usługi Time Series Insights](https://github.com/Microsoft/tsiclient) dokumentacji.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
