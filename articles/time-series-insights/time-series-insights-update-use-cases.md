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
ms.date: 11/27/2018
ms.openlocfilehash: 9d9fab9f0a515cacdf2a1425c4da06c9e3d4c364
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856559"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Przypadki użycia usługi Azure Time Series Insights (wersja zapoznawcza)

Ten artykuł zawiera omówienie kilku typowe przypadki użycia dla usługi Azure Time Series Insights (TSI). Zalecenia przedstawione w tym artykule służyć jako punkt początkowy, podczas opracowywania aplikacji i rozwiązań w usłudze TSI.

Po przeczytaniu tego artykułu, będziesz mieć możliwość odpowiedzieć na następujące pytania:

* Jakie są typowe przypadki użycia, usługi Azure TSI?
* Jakie są korzyści z używania usługi Azure TSI eksploracji danych i wykrywania anomalii visual?
* Jakie są korzyści z używania usługi Azure TSI analizy operacyjnej i efektywność procesu?
* Jakie są korzyści z używania usługi Azure TSI zaawansowaną analizę?

Ten dokument zawiera omówienie przypadki użycia, które Azure czas serii Insights prywatnej wersji zapoznawczej jest przeznaczona dla.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Eksplorowanie danych i wizualne wykrywanie anomalii

Błyskawicznie eksploruj i analizuj miliardy zdarzeń, aby wykrywać anomalie i znajdować ukryte trendy w swoich danych. Usługa Azure TSI zapewnia niemal w czasie rzeczywistym wydajności dla obciążeń analizy IoT i metodyki DevOps.

![Eksplorator danych][1]

Wszystkie zalety usługi Azure TSI większość klientów zgadza się, to czas na do wglądu między najwyższy. Usługa TSI Przesyła wymaga nie wcześniejszego przygotowania danych i działa z dużą szybkością, zapewniając łączność miliardów zdarzeń w usłudze Azure IoT Hub lub Centrum zdarzeń w ciągu kilku minut.  Po nawiązaniu połączenia możesz wizualizować i analizować miliardy zdarzeń można zauważyć anomalie i wykryć ukryte trendy w danych.  Ponieważ usługa TSI Przesyła jest intuicyjna i prosta w obsłudze, możesz uruchomić, interakcje z danymi bez napisania choćby jednego wiersza kodu. Nie musisz również uczyć się nowego języka. Usługa Time Series Insights zapewnia szczegółowe zapytania oparte na tekście dla zaawansowanych użytkowników zaznajomionych z językiem SQL, a także możliwość eksploracji opartej na wyborach i kliknięciach dla początkujących.

Widzimy, korzystając z zalet szybkość klientom szybko, diagnozowanie problemów związanych z zasobów w wykonywaniu metodyki DevOps, aby przejść do głównej przyczyny błędu w rozwiązaniu IoT i identyfikować obszary wymaga zbadania pod inicjatyw do nauki o danych.  

Istnieją trzy podstawowe sposoby interakcji z danymi przechowywanymi w usłudze TSI:

1. Pierwszy i najprostszy rozpocząć pracę z to nasza wizualizacja Explorer, w którym można szybko wizualizować wszystkie dane IoT w jednym miejscu. Zapewnia ona narzędzia, takie jak mapy cieplnej, które wizualnie wykrywając anomalie w danych proste, a także perspektywy widoku, który umożliwia porównanie maksymalnie cztery widoki z jednego lub kilku środowisk TSI w pojedynczym pulpicie nawigacyjnym, dzięki czemu umożliwia wgląd w dane szeregów czasowych w wszystkie lokalizacje. Dowiedz się więcej o [Eksplorator usługi TSI](./time-series-insights-update-explorer.md). Aby zaplanować środowisko aktualizacji TSI, przeczytaj [TSI Aktualizacja planowania](./time-series-insights-update-plan.md).

1. Druga metoda jest szybko osadzić zaawansowane wykresy i diagramy w aplikacji sieci web przy użyciu naszego zestawu SDK języka JavaScript. Przy użyciu zaledwie kilku wierszy kodu umożliwia tworzenie zaawansowanych zapytań w celu wypełnienia wykresów liniowych, wykresów kołowych, wykresy słupkowe, map cieplnych, siatek danych i więcej. Wszystkie te elementy istnieje out-of--box przy użyciu zestawu SDK. Zestaw SDK również przenosi usługa TSI Przesyła zapytania interfejsów API, dzięki któremu można tworzyć predykatów podobnego do SQL do wykonywania zapytań o dane, które mają być wyświetlane na pulpicie nawigacyjnym. W przypadku rozwiązania hybrydowe przez warstwę prezentacji TSI oferuje sparametryzowanych adresów URL, które zapewniają bezproblemowe łączenie punktów za pomocą Eksploratora usługi dla dokładnego omówieniach na dane. Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, przeczytaj [biblioteki klienta usługi TSI JS](https://docs.microsoft.com/azure/time-series-insights/tutorial-explore-js-client-lib) i [klienta TSI](https://github.com/Microsoft/tsiclient) dokumentacji. Aby uzyskać więcej informacji o sparametryzowanych adresów URL, przeczytaj nasze artykuł na [sparametryzowanych adresów URL](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-parameterized-urls).  

1. Na koniec usługa TSI Przesyła udostępnia zaawansowane interfejsy API do wykonywania zapytań na danych przechowywanych w usłudze TSI. TSI zawiera operatory czasowe, takie jak, pierwsze i ostatnie, agregacji i transformacji, takie jak średnia, min, max, Podziel wg, kolejność według i DateHistogram i operatorów, takich jak filtrowanie ma w programie i, lub większy niż wyrażeń regularnych, itp. Te operatory umożliwiają aplikacjom podrzędne szybko znaleźć interesujące trendów i wzorców w danych i może służyć do wypełniania wizualizacje rozwijane w domu, można zauważyć anomalie.  

Aby uzyskać więcej informacji na temat ofert na platformie Azure dla IoT, zobacz [Stwórz Internet swoich rzeczy —](https://www.microsoft.com/internet-of-things).

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analiza operacyjna i podnoszenie efektywności procesów

Aby włączyć monitorowanie kondycji, użycia i wydajności sprzętu na dużą skalę, zapewniając w ten sposób prosty sposób, aby zmierzyć wydajność operacyjną. Usługa Time Series Insights pomaga zarządzać zróżnicowanymi i nieprzewidywalnymi obciążeniami IoT bez pogarszania wydajności procesów pozyskiwania danych i wykonywania zapytań.

![overview][2]

Przesyłanie strumieniowe i ciągłe przetwarzanie danych pochodzących z procesów operacyjnych pomyślnie przekształcić każdej firmie na odpowiednie technologie/rozwiązanie. Często te rozwiązania są kombinacją wiele systemów, które umożliwiają eksploracji i analizy danych, które zmieniają się nieustannie specjalnie w obszarze IoT. Te systemy światła razem w górę scenariusze, które mają wspólny wzorzec, jeśli chodzi o pozyskiwania procesu, przechowywanie, analizowanie i wizualizowanie danych IoT.

Część rozwiązaniu systemów konieczności pozyskiwania miliardy zdarzeń z urządzeń i czujników, obejmujące różnych ustawień regionalnych. Następnie te systemy przetwarzać i analizować dane przesyłane strumieniowo w czasie rzeczywistym analiz. Następnie archiwizowania danych do ciepłych i zimnych magazynu dla niemal w czasie rzeczywistym i analizy wsadowej.

Następnie te systemy muszą procesów, z którymi dane są zbierane, aby umożliwić czyszczenia i contextualization, podczas której będą przechowywane dane podrzędne scenariusze tworzenia zapytań i analizy. Platforma Microsoft Azure oferuje bogatej oferty usług, które mogą być stosowane dla tych scenariuszy IoT, w tym Azure TSI, usługi Azure IoT Hub, Azure Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, usługi Azure Databricks, usługi Azure Machine Learning i Microsoft Power BI.

Z powyższymi ustawieniami rozwiązania dane można pozyskać za pomocą usługi Azure IoT lub Event Hubs jako oferuje wysoką przepływność pozyskiwanie danych z małymi opóźnieniami. Dane pozyskane, który ma zostać przetworzony, aby uzyskać wgląd w czasie rzeczywistym można funneled do usługi Azure Stream Analytics, Azure Logic Apps i Azure Functions. Wynik następnie może być zasilany do usługi Power BI dla dashboarding w czasie rzeczywistym, a także może być załadowany do usługi Azure Time Series Insights do monitorowania, w porównaniu z historycznych rozmieszczania i generowania alertów. Dane pozyskane, których potrzebuje eksplorację danych w czasie zbliżonym do rzeczywistego lub ad hoc, wykonanie zapytania dotyczącego historycznej analizy trendów można załadować bezpośrednio do usługi Azure Time Series Insights. Danych załadowanych jest gotowy do można wykonywać zapytania wraz z nieograniczone dane historyczne dotyczące analizy operacyjnej i analiz, optymalizowanie procesów, aby uzyskać maksymalną wydajność. Wszystkie dane lub po prostu zmiany danych w tym załadować ostatnio może służyć jako dane referencyjne w ramach analizy w czasie rzeczywistym. Ponadto dane dalsze można Elegancja i przetwarzane przez nawiązanie połączenia danych usługi Azure Time Series Insights HDInsight dla Map/Reduce, Hive, itp. zadania. Na koniec udostępniania tych danych w usłudze Power BI i w dowolnej aplikacji klienta za pośrednictwem naszego publicznego zapytania powierzchni interfejsów API.

## <a name="advanced-analytics"></a>Analiza zaawansowana

Integracja z zaawansowaną analizę usług, takich jak Azure Machine Learning i Azure Databricks. Usługa TSI Przesyła dane pierwotne ingresses pochodzących z milionów urządzeń i dodaje dane kontekstowe, które mogą być używane w bezproblemowo przez zestaw usług analizy usługi Azure.

![analiza][3]

Zaawansowane funkcje analityczne i uczenia maszynowego korzystać i przetwarzanie dużych ilości danych, aby podejmować decyzje oparte na danych i wykonywać analizy predykcyjnej. W przypadki użycia IoT zaawansowane funkcje analityczne algorytmy ucz się od danych zebranych z milionów urządzeń, które mogą przesyłać dane wielokrotnie w ciągu sekundy. Danych zbieranych z urządzeń IoT jest jednak nieprzetworzone i nie ma informacji kontekstowych, takie jak lokalizacja urządzenia, jednostka odczytu czujników itp. Te dane nie mogą być używane bezpośrednio na potrzeby zaawansowanej analizy.

Azure TSI Most między danych IoT i zaawansowanych analiz w prosty i ekonomiczny sposób. Usługa TSI Przesyła zbiera dane nieprzetworzone dane telemetryczne z milionów urządzeń, wzbogaca danych przy użyciu informacji kontekstowych i przekształca dane w "format parquet", który można łatwo zintegrować z liczbą Azure Advanced Analytics usług, takich jak Azure Machine Learning, Azure Usługi DataBricks, a także aplikacji innych firm. Zaawansowane modele analizy znajdują się stale informacje z przychodzących danych telemetrycznych IoT w celu bardziej precyzyjne prognozowania.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [Eksplorator usługi TSI](./time-series-insights-update-explorer.md).

* Aby zaplanować środowisko, przeczytaj [planowania (wersja zapoznawcza) w usłudze TSI](./time-series-insights-update-plan.md).

* Odczyt [klienta TSI](https://github.com/Microsoft/tsiclient) dokumentacji.

<!-- Images -->
[1]: media/v2-update-use-cases/data-explorer.png
[2]: media/v2-update-use-cases/overview.png
[3]: media/v2-update-use-cases/advanced-analytics.png
