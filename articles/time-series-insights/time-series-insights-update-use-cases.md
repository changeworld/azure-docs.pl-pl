---
title: Azure Time Series Insights wersji zapoznawczej — przypadki użycia | Microsoft Docs
description: Informacje na temat Azure Time Series Insights przypadków użycia wersji zapoznawczej.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: f4eb1b69a1464cacaf666e49f4aff7422d63611d
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264514"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Azure Time Series Insights Podgląd przypadków użycia

W tym artykule opisano kilka typowych przypadków użycia w wersji zapoznawczej Azure Time Series Insights. Zalecenia przedstawione w tym artykule stanowią punkt wyjścia do opracowywania aplikacji i rozwiązań za pomocą Time Series Insights.

W tym artykule znajdują się odpowiedzi na następujące pytania:

* Jakie są typowe przypadki użycia dla Time Series Insights?
* Jakie korzyści wynikają z używania Time Series Insights do [eksploracji danych i wykrywania anomalii w zakresie wizualizacji](#data-exploration-and-visual-anomaly-detection)?
* Jakie korzyści wynikają z używania Time Series Insights do [analizy operacyjnej i wydajności procesów](#operational-analysis-and-driving-process-efficiency)?
* Jakie korzyści wynikają z używania Time Series Insights do [zaawansowanej analizy](#advanced-analytics)?

Przegląd tych scenariuszy użycia opisano w poniższych sekcjach.

## <a name="introduction"></a>Wprowadzenie

Azure Time Series Insights to kompleksowa oferta typu "platforma jako usługa". Służy do zbierania, przetwarzania, przechowywania, analizowania i wykonywania zapytań o wysoce zastrzeżonych, zoptymalizowanych od szeregów czasowych danych na skalę IoT. Time Series Insights doskonale nadaje się do eksploracji danych ad hoc i analizy operacyjnej. Time Series Insights to unikatowa rozszerzalna, dostosowana oferta usługi, która spełnia szeroki zakres potrzeb przemysłowych wdrożeń IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Eksplorowanie danych i wizualne wykrywanie anomalii

Błyskawicznie eksploruj i analizuj miliardy zdarzeń, aby wykrywać anomalie i znajdować ukryte trendy w swoich danych. Usługa Time Series Insights przetwarza obciążenia analityczne usług IoT i DevOps niemalże w czasie rzeczywistym.

[![Eksplorator danych](media/v2-update-use-cases/data-explorer.svg)](media/v2-update-use-cases/data-explorer.svg#lightbox)

Większość klientów wyraża zgodę na to, że minimalny czas wymagany do uzyskania wglądu to jedna z funkcji wyróżniającą Time Series Insights:

* Time Series Insights nie wymaga przygotowania danych z góry. 
* Umożliwia ona szybkie łączenie się z miliardami zdarzeń w usłudze Azure IoT Hub lub Event Hubs platformy Azure w ciągu kilku minut. 
* Po nawiązaniu połączenia można wizualizować i analizować miliardy zdarzeń, aby wypróbować anomalie i odkrywać ukryte trendy w danych.

Time Series Insights jest intuicyjny i prosty do użycia. Możesz korzystać z danych bez konieczności pisania jednego wiersza kodu. Nie istnieje również nowy język, który jest wymagany do nauczenia się, ale Time Series Insights udostępnia szczegółowy język zapytań oparty na tekście dla zaawansowanych użytkowników, którzy znają program SQL. Udostępnia również eksplorację z wybieraniem i kliknięciem dla początkujących.

Klienci mogą korzystać z szybkości, aby szybko zdiagnozować problemy związane z zasobami. Mogą przeprowadzić analizę DevOps, aby uzyskać dostęp do głównej przyczyny błędu w rozwiązaniu IoT. Mogą również identyfikować obszary, które mają być poddane kontroli w celu dalszej analizy w ramach swoich inicjatyw dotyczących analizy danych.  

Istnieją trzy podstawowe sposoby współpracy z danymi przechowywanymi w Time Series Insights:

- Pierwszym i najprostszym sposobem rozpoczęcia pracy jest wprowadzenie do Eksploratora Time Series Insights w wersji zapoznawczej. Za jego pomocą można szybko wizualizować wszystkie dane IoT w jednym miejscu. Udostępnia narzędzia takie jak mapę cieplną, które ułatwiają wykrywanie anomalii w danych. Zawiera również widok perspektywy. Służy do porównywania maksymalnie czterech widoków z jednego lub kilku środowisk Time Series Insights na jednym pulpicie nawigacyjnym. Pulpit nawigacyjny umożliwia wyświetlenie danych szeregów czasowych we wszystkich lokalizacjach. Dowiedz się więcej o [programie Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md). Aby zaplanować środowisko Time Series Insights, Przeczytaj [Time Series Insights planowanie](./time-series-insights-update-plan.md).

- Drugim sposobem na rozpoczęcie pracy jest użycie zestawu SDK języka JavaScript w celu szybkiego osadzenia zaawansowanych wykresów i grafów w aplikacji sieci Web. Wystarczy kilka wierszy kodu, które umożliwiają tworzenie zaawansowanych zapytań. Służą one do wypełniania wykresów liniowych, wykresów kołowych, wykresów słupkowych, map cieplnychów, siatek danych i innych. Wszystkie te elementy istnieją jako wbudowane przy użyciu zestawu SDK. Zestaw SDK jest również abstrakcyjny Time Series Insights interfejsów API zapytań. Za ich pomocą można tworzyć predykaty podobne do języka SQL, aby wykonywać zapytania dotyczące danych, które mają być wyświetlane na pulpicie nawigacyjnym. W przypadku hybrydowych rozwiązań warstwy prezentacji Time Series Insights oferuje sparametryzowane adresy URL. Zapewniają one bezproblemowe punkty połączenia z Eksploratorem Time Series Insights w wersji zapoznawczej, aby uzyskać szczegółowe omówieniach do danych.

    * Zapoznaj się z [biblioteką klienta Time Series INSIGHTS js](tutorial-explore-js-client-lib.md) i dokumentacją [klienta Time Series Insights](https://github.com/Microsoft/tsiclient) , aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript.

    * Dowiedz się więcej o udostępnianiu adresów URL i nowym interfejsie użytkownika, przeglądając temat [Wizualizacja danych w eksploratorze Azure Time Series Insights w wersji zapoznawczej](time-series-insights-update-explorer.md).

- Trzecim sposobem na rozpoczęcie pracy jest użycie zaawansowanych interfejsów API do wykonywania zapytań dotyczących danych przechowywanych w Time Series Insights. Time Series Insights ma operatory `from`czasowe `first`, takie `to`jak,, `last`, i. Ma agregacje i przekształcenia `average` `max`, takie jak, `min`,, `split by` `order by`, i `DateHistogram`. Zawiera również operatory filtrowania, takie jak `has`, `in`, `and`, `or` `greater than`, i `REGEX`. Wszystkie te operatory umożliwiają aplikacjom podrzędnym szybkie znajdowanie interesujących trendów i wzorców w danych. Korzystaj z nich, aby wypełnić wizualizacje w domu w celu wykrycia anomalii.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analiza operacyjna i podnoszenie efektywności procesów

Użyj Time Series Insights do monitorowania kondycji, użycia i wydajności sprzętu w odpowiedniej skali. Time Series Insights zapewnia łatwy sposób mierzenia wydajności operacyjnej. Usługa Time Series Insights pomaga zarządzać zróżnicowanymi i nieprzewidywalnymi obciążeniami IoT bez pogarszania wydajności procesów pozyskiwania danych i wykonywania zapytań.

[![Przegląd](media/v2-update-use-cases/overview.svg)](media/v2-update-use-cases/overview.svg#lightbox)

Przesyłanie strumieniowe i ciągłe przetwarzanie danych pochodzących z procesów operacyjnych może pomyślnie przekształcić każdą firmę, jeśli jest ona połączona z odpowiednią technologią lub rozwiązaniem. Często te rozwiązania są kombinacją wielu systemów. Umożliwiają one eksplorowanie i analizowanie danych, które są stale zmieniane, szczególnie w obszarze IoT i udostępniają wspólny wzorzec.

Te wzorce często zaczynają się od platformy z obsługą IoT, która pozyskuje miliardy zdarzeń z urządzeń i czujników obejmujących różne ustawienia regionalne. Te systemy przetwarzają i analizują dane przesyłane strumieniowo, aby uzyskiwać szczegółowe informacje i akcje w czasie rzeczywistym. Dane są zwykle archiwizowane w pamięci masowej i w chłodnym magazynie na potrzeby analiz w czasie rzeczywistym i w usłudze Batch.

Gromadzone dane przechodzą przez serię przetwarzania, aby czyścić i contextualize je na potrzeby scenariuszy zapytań podrzędnych i analizy. Platforma Azure oferuje bogate usługi, które mogą być stosowane do scenariuszy IoT, takich jak konserwacja zasobów i produkcja. Do tych usług należą Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning i Power BI.

Architekturę rozwiązania można osiągnąć w następujący sposób:

- Pozyskiwanie danych za pośrednictwem IoT Hub lub Event Hubs w celu uzyskania najlepszych w swojej klasie zabezpieczeń, przepływności i opóźnień.
- Wykonywanie przetwarzania i obliczeń danych. Pozyskaj dane przy użyciu usług takich jak Stream Analytics, Logic Apps i Azure Functions. Używana usługa zależy od konkretnych potrzeb związanych z przetwarzaniem danych.
- Obliczane sygnały z potoku przetwarzania są przekazywane do Time Series Insights do przechowywania i analizowania.

Time Series Insights oferuje około eksploracji danych w czasie rzeczywistym i szczegółowe informacje oparte na zasobach za pośrednictwem danych historycznych. W zależności od potrzeb firmy zadania MapReduce i Hive mogą być uruchamiane na danych przechowywanych w Time Series Insights przez połączenie Time Series Insights z usługą Azure HDInsight. Dane przechowywane w Time Series Insights są dostępne dla Power BI i innych aplikacji klienta za pośrednictwem Time Series Insights interfejsów API zapytania o powierzchnię publiczną. Te dane mogą być używane w scenariuszach analizy biznesowej i funkcjonalnej.

## <a name="advanced-analytics"></a>Analiza zaawansowana

Integruj z zaawansowanymi usługami analitycznymi, takimi jak Machine Learning i Azure Databricks. Time Series Insights ingresses danych pierwotnych z milionów urządzeń. Dodaje kontekstowe dane, które mogą być bezproblemowo wykorzystywane przez pakiet usług analitycznych platformy Azure.

[![Wersję](media/v2-update-use-cases/advanced-analytics.svg)](media/v2-update-use-cases/advanced-analytics.svg#lightbox)

Zaawansowana analiza i uczenie maszynowe zużywają i przetwarzają duże ilości danych. Te dane służą do podejmowania decyzji opartych na danych i wykonywania analizy predykcyjnej. W przypadku przypadków użycia IoT zaawansowane algorytmy analizy uczyją się od danych zebranych z milionów urządzeń. Te urządzenia przesyłają dane wiele razy na sekundę. Dane zbierane z urządzeń IoT są surowe. Brak informacji kontekstowych, takich jak lokalizacja urządzenia i jednostka odczytywania czujnika. W związku z tym dane pierwotne są trudne do użycia bezpośrednio dla zaawansowanej analizy.

Time Series Insights mostkuje przerwy między danymi IoT i zaawansowaną analizą na dwa proste i ekonomiczne metody:

- Najpierw Time Series Insights zbiera dane danych telemetrycznych z milionów urządzeń przy użyciu IoT Hub. Wzbogaca dane z informacjami kontekstowymi i przekształca dane w format Parquet. Ten format można łatwo zintegrować z innymi zaawansowanymi usługami analitycznymi, takimi jak Machine Learning, Azure Databricks i aplikacje innych firm.

    Time Series Insights może stanowić źródło prawdy dla wszystkich danych w organizacji. Tworzy centralne repozytorium dla obciążeń analizy podrzędnej do użycia. Ponieważ Time Series Insights jest usługą magazynu niemal w czasie rzeczywistym, zaawansowane modele analityczne mogą się ciągle uczyć od przychodzących danych telemetrycznych IoT. W efekcie modele mogą wprowadzać dokładniejsze przewidywania.

- W drugim Time Series Insights można uzyskać dane wyjściowe modeli uczenia maszynowego i przewidywania, aby wizualizować i przechowywać wyniki. Ta procedura ułatwia organizacjom optymalizowanie i dostosowywanie modeli. Time Series Insights ułatwia wizualizację danych telemetrycznych przesyłanych strumieniowo na tej samej płaszczyźnie co w przypadku wyników przeszkolonych modeli. W ten sposób ułatwiają zespołom analizy danych wykrywanie anomalii i identyfikowanie wzorców.  

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [programie Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).
- Przeczytaj [Time Series Insights planowanie w wersji zapoznawczej](./time-series-insights-update-plan.md) w celu zaplanowania środowiska.
- Przeczytaj dokumentację [klienta Time Series Insights](https://github.com/Microsoft/tsiclient) .
