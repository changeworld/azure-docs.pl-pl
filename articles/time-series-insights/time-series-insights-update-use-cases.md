---
title: Podgląd przypadków użycia — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się więcej o przypadkach użycia usługi Azure Time Series Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac2a728750c6b01dfc57fa7e20df25c856395a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087390"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Przypadki użycia usługi Azure Time Series Insights w wersji zapoznawczej

W tym artykule podsumowano kilka typowych przypadków użycia usługi Azure Time Series Insights Preview. Zalecenia zawarte w tym artykule służą jako punkt wyjścia do tworzenia aplikacji i rozwiązań za pomocą usługi Time Series Insights.

W szczególności w tym artykule odpowiedzi na następujące pytania:

* Jakie są typowe przypadki użycia usługi Time Series Insights?
* Jakie są korzyści z używania usługi Time Series Insights do [eksploracji danych i wykrywania anomalii wizualnych?](#data-exploration-and-visual-anomaly-detection)
* Jakie są korzyści z używania usługi Time Series Insights do [analizy operacyjnej i wydajności procesu?](#operational-analysis-and-driving-process-efficiency)
* Jakie są korzyści z korzystania z usługi Time Series Insights do [zaawansowanej analizy?](#advanced-analytics)

Omówienie tych scenariuszy użycia opisano w poniższych sekcjach.

## <a name="introduction"></a>Wprowadzenie

Usługa Azure Time Series Insights to kompleksowa oferta platformy jako usługi. Służy do zbierania, przetwarzania, przechowywania, analizowania i wykonywania zapytań o wysoce kontekstowe dane w skali IoT zoptymalizowane pod kątem szeregów czasowych. Usługa Time Series Insights idealnie nadaje się do eksplorowania danych ad hoc oraz do analizy operacyjnej. Usługa Time Series Insights to wyjątkowo rozszerzalna, dostosowana oferta usług, która spełnia szerokie potrzeby przemysłowych wdrożeń IoT.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Eksploracja danych i wykrywanie anomalii wizualnych

Błyskawicznie eksploruj i analizuj miliardy zdarzeń, aby wykrywać anomalie i odkrywać ukryte trendy w danych. Aplikacja Time Series Insights zapewnia niemal wydajność w czasie rzeczywistym dla obciążeń analitycznych IoT i DevOps.

[![Eksplorator danych](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

Większość klientów zgadza się, że minimalna ilość czasu potrzebna do uzyskania wglądu jest jedną z wyróżniających się funkcji usługi Time Series Insights:

* Usługa Time Series Insights nie wymaga przygotowania danych z góry. 
* Szybko można połączyć się z miliardami zdarzeń w wystąpieniach usługi Azure IoT Hub lub Usługi Azure Event Hubs w ciągu kilku minut. 
* Po połączeniu można wizualizować i analizować miliardy zdarzeń, aby wykrywać anomalie i odkrywać ukryte trendy w danych.

Aplikacja Time Series Insights jest intuicyjna i prosta w obsłudze. Można wchodzić w interakcje z danymi bez pisania jednego wiersza kodu. Nie ma również nowego języka, który jest wymagany do nauki, chociaż usługa Time Series Insights zapewnia szczegółowy język zapytań tekstowych dla zaawansowanych użytkowników, którzy są zaznajomieni z sql. Zapewnia również wybierz i kliknij eksploracji dla początkujących.

Klienci mogą skorzystać z szybkości, aby szybko zdiagnozować problemy związane z zasobami. Mogą wykonywać analizę DevOps, aby uzyskać główną przyczynę błędu w rozwiązaniu IoT. Mogą również identyfikować obszary, które należy oznaczyć do dalszego badania w ramach swoich inicjatyw w zakresie nauki o danych. 

Istnieją trzy podstawowe sposoby interakcji z danymi przechowywanymi w usłudze Time Series Insights:

* Pierwszym i najprostszym sposobem rozpoczęcia pracy jest eksplorator usługi Time Series Insights Preview. Można go użyć do szybkiego wizualizacji wszystkich danych IoT w jednym miejscu. Zapewnia narzędzia, takie jak mapa ciepła, które pomagają wykryć anomalie w danych. Zapewnia również widok perspektywy. Służy do porównywania maksymalnie czterech widoków z jednego lub więcej środowisk usługi Time Series Insights w jednym pulpicie nawigacyjnym. Pulpit nawigacyjny umożliwia wyświetlanie danych szeregów czasowych we wszystkich lokalizacjach. Dowiedz się więcej o [Eksploratorze wglądu w statystyki szeregów czasowych](./time-series-insights-update-explorer.md). Aby zaplanować środowisko usługi Time Series Insights, przeczytaj [artykuł Planowanie usługi Time Series Insights](./time-series-insights-update-plan.md).

* Drugim sposobem rozpoczęcia jest użycie zestawie JavaScript SDK do szybkiego osadzania zaawansowanych wykresów i wykresów w aplikacji internetowej. Wystarczy kilka wierszy kodu, aby tworzyć zaawansowane zapytania. Za ich pomocą można wypełniać wykresy liniowe, wykresy kołowe, wykresy słupkowe, mapy skupień, siatki danych i inne. Wszystkie te elementy istnieją out-of-the-box przy użyciu SDK. SDK również abstrakcje time series insights kwerendy interfejsów API. Można ich używać do tworzenia predykatów podobnych do SQL, aby zbadać dane, które mają być wyświetlane na pulpicie nawigacyjnym. W przypadku hybrydowych rozwiązań warstwy prezentacji aplikacja Time Series Insights oferuje sparametryzowane adresy URL. Zapewniają one bezproblemowe punkty połączenia z Eksploratorem podglądu usługi Time Series Insights dla głębokich nurkowań w danych.

  * Przeczytaj o [bibliotece klienta JS usługi Time Series Insights](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) i dokumentacji klienta usługi Time Series [Insights,](https://github.com/Microsoft/tsiclient) aby dowiedzieć się więcej o sdk JavaScript.

  * Dowiedz się więcej o udostępnianiu adresów URL i nowego interfejsu użytkownika, przeglądając [dane wizualizowania w Eksploratorze usługi Azure Time Series Insights Preview](time-series-insights-update-explorer.md).

* Trzecim sposobem uruchomienia jest użycie zaawansowanych interfejsów API do wykonywania zapytań o dane przechowywane w usłudze Time Series Insights. Aplikacja Time Series Insights ma `from` `to`operatory czasowe, takie jak , `first`, i `last`. Ma agregacje i `average`przekształcenia, `order by`takie `DateHistogram`jak , `min`, `max`, `split by`, i . Posiada również operatory filtrowania, `or`takie `greater than`jak `REGEX` `has`, `in` `and`, , , i . Wszystkie te operatory umożliwiają aplikacjom niższego szczebla szybkie znajdowanie interesujących trendów i wzorców w danych. Użyj ich do wypełniania wizualizacji homegrown do wykrywania anomalii.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Analiza operacyjna i efektywność procesu

Aplikacja Time Series Insights umożliwia monitorowanie kondycji, użycia i wydajności sprzętu na dużą skalę. Usługa Time Series Insights zapewnia łatwy sposób pomiaru wydajności operacyjnej. Aplikacja Time Series Insights pomaga zarządzać różnorodnymi i nieprzewidywalnymi obciążeniami IoT bez poświęcania wydajności pozyskiwania lub zapytań.

[![Omówienie](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Przesyłanie strumieniowe i ciągłe przetwarzanie danych pochodzących z procesów operacyjnych może z powodzeniem przekształcić każdą firmę, jeśli jest połączona z odpowiednią technologią lub rozwiązaniem. Często te rozwiązania są kombinacją wielu systemów. Umożliwiają one eksplorację i analizę danych, które stale się zmieniają, szczególnie w sferze IoT, i współużytkują wspólny wzorzec.

Te wzorce często zaczynają się od platform obsługujących IoT, które pochłoną miliardy zdarzeń z urządzeń i czujników obejmujących różne ustawienia regionalne. Systemy te przetwarzają i analizują dane przesyłania strumieniowego w celu uzyskania szczegółowych informacji i działań w czasie rzeczywistym. Dane są zazwyczaj archiwizowane w ciepłym i chłodni w celu analizy w czasie zbliżonym do rzeczywistego i analizy wsadowej.

Dane, które są zbierane przechodzi przez serię przetwarzania, aby oczyścić i contextualize go do dalszych zapytań i scenariuszy analizy. Platforma Azure oferuje zaawansowane usługi, które można zastosować do scenariuszy IoT, takich jak konserwacja zasobów i produkcja. Usługi te obejmują usługi Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning i Power BI.

Architekturę rozwiązania można osiągnąć w następujący sposób:

* Pociucieszanie danych za pośrednictwem usługi IoT Hub lub Centrum zdarzeń dla najlepszych w swojej klasie zabezpieczeń, przepływności i opóźnień.
* Wykonywanie przetwarzania danych i obliczeń. Lejek pozyskiwania danych za pośrednictwem usług, takich jak usługa Stream Analytics, aplikacje logiki i usługi Azure Functions. Usługa, z której korzystasz, zależy od konkretnych potrzeb przetwarzania danych.
* Obliczone sygnały z potoku przetwarzania są wypychane do usługi Time Series Insights w celu przechowywania i analizy.

Usługa Time Series Insights oferuje niemal eksplorację danych w czasie rzeczywistym i wgląd w dane historyczne w czasie rzeczywistym. W zależności od potrzeb biznesowych zadania MapReduce i Hive można uruchamiać na danych przechowywanych w usłudze Time Series Insights, łącząc usługę Time Series Insights z usługą Azure HDInsight. Dane przechowywane w usłudze Time Series Insights są dostępne dla usługi Power BI i innych aplikacji klientów za pośrednictwem interfejsów API zapytań publicznych usługi Time Series Insights. Te dane mogą być używane do głębokich scenariuszy analizy biznesowej i operacyjnej.

## <a name="advanced-analytics"></a>Analiza zaawansowana

Integracja z zaawansowanymi usługami analitycznymi, takimi jak uczenie maszynowe i usługa Azure Databricks. Usługa Time Series Insights przesyła nieprzetworzone dane z milionów urządzeń. Dodaje dane kontekstowe, które mogą być bezproblemowo używane przez pakiet usług analitycznych platformy Azure.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Zaawansowana analityka i uczenie maszynowe zużywają i przetwarzają duże ilości danych. Dane te są używane do podejmowania decyzji opartych na danych i przeprowadzania analiz predykcyjnych. W przypadkach użycia IoT zaawansowane algorytmy analityczne uczą się na podstawie danych zebranych z milionów urządzeń. Urządzenia te przesyłają dane wiele razy co sekundę. Dane zebrane z urządzeń IoT są surowe. Brakuje informacji kontekstowych, takich jak lokalizacja urządzenia i jednostka odczytu czujnika. W rezultacie nieprzetworzone dane są trudne do użycia bezpośrednio do zaawansowanej analizy.

Usługa Time Series Insights wypełnia lukę między danymi IoT a zaawansowaną analizą na dwa proste i ekonomiczne sposoby:

* Po pierwsze usługa Time Series Insights zbiera nieprzetworzone dane telemetryczne z milionów urządzeń przy użyciu usługi IoT Hub. Wzbogaca dane o informacje kontekstowe i przekształca dane w format parkietu. Ten format można łatwo zintegrować z innymi zaawansowanymi usługami analitycznymi, takimi jak uczenie maszynowe, usługa Azure Databricks i aplikacje innych firm.

    Usługa Time Series Insights może służyć jako źródło prawdy dla wszystkich danych w organizacji. Tworzy centralne repozytorium dla obciążeń analizy niższego rzędu do wykorzystania. Ponieważ usługa Time Series Insights jest usługą magazynu w czasie zbliżonym do rzeczywistego, zaawansowane modele analizy mogą uczyć się w sposób ciągły na podstawie przychodzących danych telemetrycznych IoT. W rezultacie modele można dokonać bardziej dokładne prognoz.

* Po drugie dane wyjściowe modeli uczenia maszynowego i przewidywania mogą być wprowadzane do usługi Time Series Insights w celu wizualizacji i przechowywania ich wyników. Ta procedura pomaga organizacjom zoptymalizować i dostosować swoje modele. Usługa Time Series Insights ułatwia wizualizowanie danych telemetrycznych przesyłania strumieniowego na tej samej płaszczyźnie, co dane wyjściowe uczonego modelu. W ten sposób pomaga zespołom do nauki o danych wykrywać anomalie i identyfikować wzorce.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Eksploratorze wglądu w statystyki szeregów czasowych](./time-series-insights-update-explorer.md).
* Przeczytaj [planowanie podglądu usługi Time Series Insights Preview](./time-series-insights-update-plan.md) w celu zaplanowania środowiska.
* Przeczytaj dokumentację [klienta usługi Time Series Insights.](https://github.com/Microsoft/tsiclient)
