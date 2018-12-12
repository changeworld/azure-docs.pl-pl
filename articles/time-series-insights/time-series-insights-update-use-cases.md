---
title: Przypadki użycia usługi Azure Time Series Insights (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Zastosowań Understanding Azure Time Series Insights (wersja zapoznawcza)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 67be21ae7f0cb997563f17130b9d5ecb7d359b31
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873873"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Przypadki użycia usługi Azure Time Series Insights (wersja zapoznawcza)

Ten artykuł zawiera omówienie kilku typowe przypadki użycia dla usługi Azure Time Series Insights (TSI). Zalecenia przedstawione w tym artykule służyć jako punkt początkowy, podczas opracowywania aplikacji i rozwiązań w usłudze TSI.

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

* Jakie są typowe przypadki użycia, usługi Azure TSI?
* Jakie są korzyści z używania usługi Azure TSI eksploracji danych i wykrywania anomalii visual?
* Jakie są korzyści z używania usługi Azure TSI analizy operacyjnej i efektywność procesu?
* Jakie są korzyści z używania usługi Azure TSI zaawansowaną analizę?

Ten dokument zawiera omówienie przypadki użycia, które Azure TSI prywatne w wersji zapoznawczej jest przeznaczona dla.

## <a name="introduction"></a>Wprowadzenie

Usługa Azure TSI jest end-to-end Platform-As-A-Service do odbierania, przetwarzania, przechowywania i wysyłania zapytań dotyczących danych skali IoT wysoce contextualized, zoptymalizowanego serii czasu. W efekcie Azure TSI jest idealny dla eksplorację danych ad hoc, a także analizy operacyjnej. Usługa TSI Przesyła to usługa jednoznacznie rozszerzalne, dostosowany, oferty, że spełnia szerokie musi przemysłowych wdrożeń IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Eksplorowanie danych i wizualne wykrywanie anomalii

Błyskawicznie eksploruj i analizuj miliardy zdarzeń, aby wykrywać anomalie i znajdować ukryte trendy w swoich danych. Usługa Azure TSI zapewnia niemal w czasie rzeczywistym wydajności dla obciążeń analizy IoT i metodyki DevOps.

![Eksplorator danych][1]

Wszystkie zalety usługi Azure TSI większość klientów zgadza się, to czas na do wglądu między najwyższy. Usługa TSI Przesyła wymaga nie wcześniejszego przygotowania danych i działa z dużą szybkością, zapewniając łączność miliardów zdarzeń w usłudze Azure IoT Hub lub Centrum zdarzeń w ciągu kilku minut.  Po nawiązaniu połączenia możesz wizualizować i analizować miliardy zdarzeń można zauważyć anomalie i wykryć ukryte trendy w danych.  Ponieważ usługa TSI Przesyła jest intuicyjna i prosta w obsłudze, możesz uruchomić, interakcje z danymi bez napisania choćby jednego wiersza kodu. Nie musisz również uczyć się nowego języka. Usługa Time Series Insights zapewnia szczegółowe zapytania oparte na tekście dla zaawansowanych użytkowników zaznajomionych z językiem SQL, a także możliwość eksploracji opartej na wyborach i kliknięciach dla początkujących.

Widzimy, korzystając z zalet szybkość klientom szybko, diagnozowanie problemów związanych z zasobów w wykonywaniu metodyki DevOps, aby przejść do głównej przyczyny błędu w rozwiązaniu IoT i identyfikować obszary wymaga zbadania pod inicjatyw do nauki o danych.  

Istnieją trzy podstawowe sposoby interakcji z danymi przechowywanymi w usłudze TSI:

1. Pierwszy i najprostszy rozpocząć pracę z to nasza wizualizacja explorer, w którym można szybko wizualizować wszystkie dane IoT w jednym miejscu. Zapewnia narzędzia, takie jak mapy cieplnej, które powodują, że wizualnie wykrywając anomalie w danych proste. Umożliwia także widoku perspektywy, która umożliwia porównywanie maksymalnie cztery widoki z jednego lub kilku środowisk TSI w pojedynczym pulpicie nawigacyjnym, dzięki czemu umożliwia wgląd w dane szeregów czasowych we wszystkich lokalizacjach. Dowiedz się więcej o [Eksplorator usługi TSI](./time-series-insights-update-explorer.md). Aby zaplanować środowisko aktualizacji TSI, przeczytaj [TSI Aktualizacja planowania](./time-series-insights-update-plan.md).

1. Druga metoda jest szybko osadzić zaawansowane wykresy i diagramy w aplikacji sieci web przy użyciu naszego zestawu SDK języka JavaScript. Przy użyciu zaledwie kilku wierszy kodu umożliwia tworzenie zaawansowanych zapytań w celu wypełnienia wykresów liniowych, wykresów kołowych, wykresy słupkowe, map cieplnych, siatek danych i więcej. Wszystkie te elementy istnieje out-of--box przy użyciu zestawu SDK. Zestaw SDK również przenosi usługa TSI Przesyła zapytania interfejsów API, dzięki któremu można tworzyć predykatów podobnego do SQL do wykonywania zapytań o dane, które mają być wyświetlane na pulpicie nawigacyjnym. W przypadku rozwiązania hybrydowe przez warstwę prezentacji TSI oferuje sparametryzowanych adresów URL, które zapewniają bezproblemowe łączenie punktów za pomocą Eksploratora usługi dla dokładnego omówieniach na dane. Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, przeczytaj [biblioteki klienta usługi TSI JS](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) i [klienta TSI](https://github.com/Microsoft/tsiclient) dokumentacji. Aby uzyskać więcej informacji o sparametryzowanych adresów URL, przeczytaj nasze artykuł na [sparametryzowanych adresów URL](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Na koniec usługa TSI Przesyła udostępnia zaawansowane interfejsy API do wykonywania zapytań na danych przechowywanych w usłudze TSI. TSI zawiera operatory czasowe, takie jak, pierwsze i ostatnie, agregacji i transformacji, takie jak średnia, min, max, Podziel wg, kolejność według i DateHistogram i operatorów, takich jak filtrowanie ma w programie i, lub większy niż wyrażeń regularnych, itp. Te operatory umożliwiają aplikacjom podrzędne szybko znaleźć interesujące trendów i wzorców w danych i może służyć do wypełniania wizualizacje rozwijane w domu, można zauważyć anomalie.  

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analiza operacyjna i podnoszenie efektywności procesów

Aby włączyć monitorowanie kondycji, użycia i wydajności sprzętu na dużą skalę, zapewniając w ten sposób prosty sposób, aby zmierzyć wydajność operacyjną. Usługa Time Series Insights pomaga zarządzać zróżnicowanymi i nieprzewidywalnymi obciążeniami IoT bez pogarszania wydajności procesów pozyskiwania danych i wykonywania zapytań.

![overview][2]

Przesyłanie strumieniowe i ciągłe przetwarzanie danych pochodzących z procesów operacyjnych pomyślnie przekształcić każdej firmie na odpowiednie technologie/rozwiązanie. Często te rozwiązania są kombinacją wiele systemów, które umożliwiają eksploracji i analizy danych, które zmienia się stale specjalnie w obszarze IoT i udostępniać wspólny wzorzec.

Te wzorce często rozpoczyna się przy użyciu platformy IoT, włączone, w których pozyskiwania miliardy zdarzeń z urządzeń i czujników, obejmujące różnych ustawień regionalnych. Te systemy przetwarzania i analizowania danych przesyłanych strumieniowo do uzyskania szczegółowych informacji w czasie rzeczywistym i akcje, zwykle archiwizowania danych do magazynu ciepłe i zimne dla niemal w czasie rzeczywistym i analizy wsadowej. Dane gromadzone przechodzi przez serię przetwarzania do czyszczenia i nakreślić kontekst dla podrzędnego scenariuszach zapytań i analizy. Platforma Microsoft Azure oferuje bogatej oferty usług, które można zastosować do tych scenariuszy IoT (konserwacji zasobów, produkcji, itp.). Obejmują one Azure TSI, usługi Azure IoT Hub, Azure Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, usługi Azure Databricks, usługi Azure Machine Learning i Microsoft Power BI.

Ta architektura rozwiązania można osiągnąć następujące — pozyskiwania danych za pomocą usługi Azure IoT Hub lub Azure Event Hub zabezpieczeń najlepsze w swojej klasie, przepływności i opóźnień. Wykonywania obliczeń i przetwarzania danych przez funneling pozyskiwanych danych za pośrednictwem usług, takich jak Azure Stream Analytics, Azure Logic Apps, Azure Functions, w zależności od potrzeb określonego przetwarzania danych. Obliczona sygnałów z potoku przetwarzania są przekazywane do usługi Azure TSI do przechowywania i analizy. Azure Time Series Insights oferuje niemal Eksplorowanie danych w czasie rzeczywistym i na podstawie zasobów szczegółowych informacji na podstawie danych historycznych. W zależności od potrzeb biznesowych można uruchomić zadania MapReduce i Hive danych przechowywanych w usłudze Time Series Insights łącząc usługi Time Series Insights, usługą HDInsight. Dane przechowywane w usłudze Time Series Insights może udostępnione do usługi Power BI i innych aplikacji klienta, za pośrednictwem usługi Time Series Insights publiczny powierzchni interfejsami API zapytań dla scenariuszy analizy operacyjnej i biznesowa.

## <a name="advanced-analytics"></a>Analiza zaawansowana

Integracja z zaawansowaną analizę usług, takich jak Azure Machine Learning i Azure Databricks. Usługa Time Series Insights pozyskuje nieprzetworzone dane z milionów urządzeń i dodaje dane kontekstowe, które mogą być bezproblemowo przetwarzane przez pakiet usług analitycznych platformy Azure.

![analiza][3]

Zaawansowane funkcje analityczne i uczenia maszynowego korzystać i przetwarzanie dużych ilości danych, aby podejmować decyzje oparte na danych i wykonywać analizy predykcyjnej. W przypadki użycia IoT zaawansowane funkcje analityczne algorytmy ucz się od danych zebranych z milionów urządzeń, które mogą przesyłać dane wielokrotnie w ciągu sekundy. Danych zbieranych z urządzeń IoT jest jednak nieprzetworzone i nie ma informacji kontekstowych, takie jak lokalizacja urządzenia, jednostka odczytu czujników itp., co utrudniało to dane, które mają być używane bezpośrednio na potrzeby zaawansowanej analizy.

Azure Time Series Insights Most między danymi IoT i zaawansowanej analizy na dwa sposoby proste i ekonomiczne. Najpierw aktualizacji usługi Time Series Insights zbiera dane nieprzetworzone dane telemetryczne z milionów urządzeń przy użyciu usługi IoT hub, wzbogaca danych przy użyciu informacji kontekstowych i przekształca dane w "format parquet", który można łatwo zintegrować z wielu usług zaawansowane funkcje analityczne takie jak usługa Azure Machine Learning, usługi Azure Databricks i innych aplikacji innych firm.  Usługa Time Series Insights może służyć jako źródła z prawdę dla wszystkich danych całej organizacji, tworząc centralne repozytorium podrzędnym analytics obciążeń z.  Ponieważ usługa Time Series Insights jest niemal w czasie rzeczywistym magazynu usługi, zaawansowane funkcje analityczne, które modele mogą ucz się stale przychodzących danych telemetrycznych IoT w celu bardziej precyzyjne prognozowania.

Po drugie usługi Time Series Insights może być zasilane dane wyjściowe modele uczenia i przewidywania maszynowego wizualizacji i zapisywanie ich wyników tym samym pomagając organizacjom do optymalizacji i dostosowanie ich modeli.  Co więcej usługi Time Series Insights pozwala w prosty sposób wizualizacji, przesyłania strumieniowego dane telemetryczne dotyczące tej samej płaszczyźnie, jak pomagają zespołom do nauki o danych, wykrywaniu anomalii i zidentyfikowania wzorców, dane wyjściowe kontrolek uczonego modelu.  

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [Eksplorator usługi TSI](./time-series-insights-update-explorer.md).

Aby zaplanować środowisko, przeczytaj [planowania (wersja zapoznawcza) w usłudze TSI](./time-series-insights-update-plan.md).

Odczyt [klienta TSI](https://github.com/Microsoft/tsiclient) dokumentacji.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.svg
[2]: media/v2-update-use-cases/overview.svg
[3]: media/v2-update-use-cases/advanced-analytics.svg
