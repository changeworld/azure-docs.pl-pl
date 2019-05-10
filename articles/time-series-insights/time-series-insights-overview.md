---
title: 'Omówienie: Co to jest usługa Azure Time Series Insights? | Microsoft Docs'
description: Wprowadzenie do usługi Azure Time Series Insights — nowej usługi do analizy danych szeregów czasowych i obsługi rozwiązań IoT.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: overview
ms.date: 04/26/2019
ms.custom: seodec18
ms.openlocfilehash: 8c7a24c4c4e071271d16de036ef6896e9d172913
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406329"
---
# <a name="what-is-azure-time-series-insights"></a>Co to jest usługa Azure Time Series Insights?

Usługa Azure Time Series Insights (TSI) zaprojektowano pod kątem przechowywania, wizualizacji i wykonywania zapytań dużych ilości danych szeregów czasowych, takich jak wygenerowany przez urządzenia IoT.  Jeśli chcesz przechowywać, odpytywać i wizualizować dane szeregów czasowych w chmurze oraz zarządzać nimi, usługa Time Series Insights będzie prawdopodobnie odpowiednia dla Ciebie.  

![Schemat blokowy usługi Time Series Insights](media/overview/time-series-insights-flowchart.png)

Usługa Time Series Insights ma cztery kluczowe zadania:

1. Jest w pełni zintegrowany z bramy chmury, takich jak usługi Azure IoT Hub i Azure Event Hubs. Łatwo łączy się z takimi źródłami zdarzeń i analizuje kod JSON z komunikatów i struktur z danymi w postaci czytelnych wierszy i kolumn. Łączy metadane z telemetrią i indeksuje dane w magazynie kolumnowym.
1. Usługa TSI Przesyła zarządza magazynu danych. Aby mieć pewność, że dane są zawsze łatwo dostępne, przechowuje je w pamięci i na dyskach SSD przez nawet 400 dni. W ciągu kilku sekund można interaktywnie wykonać zapytanie o miliardy zdarzeń — na żądanie.
1. Usługa TSI Przesyła zapewnia wizualizacji out-of--box za pomocą Eksploratora usługi TSI.  
1. Usługa TSI Przesyła udostępnia usługi zapytań w Eksploratorze TSI i za pomocą interfejsów API, które ułatwiają integrowanie w celu osadzania danych szeregów czasowych w aplikacjach niestandardowych.  

Jeśli tworzysz aplikację do użytku wewnętrznego lub zewnętrznego klienci będą mogli korzystać, TSI może służyć jako serwer zaplecza do indeksowania, przechowywania i agregowanie danych szeregów czasowych. Można tworzyć niestandardowych wizualizacji i środowisko użytkownika na temat korzystania z najważniejszych [zestawu SDK klienta](tutorial-explore-js-client-lib.md). Usługa TSI Przesyła jest również wyposażony w kilka [interfejsów API zapytań](how-to-shape-query-json.md) Aby włączyć te dostosowaną scenariuszy.  

Dane szeregów czasowych przedstawiają, jak zasób lub proces zmienia się wraz z upływem czasu. Dane szeregów czasowych w związku z tym jest indeksowane według sygnatur czasowych, a czas jest największe znaczenie osi, na której takie dane są organizowane. Dane szeregów czasowych zwykle umieszczane są w kolejności sekwencyjnej i zwykle jest traktowany jako wstawiania, a nie aktualizacji do bazy danych na w wyniku.

W przypadku dużych woluminów przechowywanie, indeksowanie, odpytywanie, analizowanie i wizualizowanie danych szeregów czasowych może być trudne.
Jednak usługa Azure Time Series Insights powoduje przechwycenie i każdego nowego zdarzenia są przechowywane jako wiersz i zmiany efektywnie mierzy się wraz z upływem czasu, dzięki czemu można sprawdzić wstecz prowadzenie analiz z przeszłości i przewidzieć przyszłe zmiany.

## <a name="video"></a>Wideo

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Dowiedz się więcej na temat usługi Azure Time Series Insights, platforma analiz IoT opartych na chmurze.</br>

[![FILM WIDEO](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Podstawowe scenariusze

- Przechowywanie danych szeregów czasowych w sposób skalowalny.  
  - Zasadniczo usługa Time Series Insights ma bazę danych zaprojektowaną z myślą o danych szeregów czasowych.  Ponieważ jest ona skalowalna i w pełni zarządzana, usługa Time Series Insights bierze na siebie pracę związaną z przechowywaniem zdarzeń i zarządzaniem nimi.

- Eksploracja danych w czasie niemal rzeczywistym.  
  - Usługa Time Series Insights udostępnia eksploratora wizualizującego wszystkie dane napływające do środowiska.  Wkrótce po nawiązaniu połączenia ze źródłem zdarzeń można wyświetlić dane zdarzeń, eksplorować je i wykonywać zapytania o nie w usłudze Time Series Insights.  Dane są przydatne do sprawdzania, czy urządzenie generuje dane zgodnie z oczekiwaniami, i monitorowania zasobu IoT pod kątem kondycji, wydajności i ogólnej efektywności.  

- Analiza głównych przyczyn problemów i wykrywanie anomalii.
  - Usługa Time Series Insights jest wyposażona w narzędzia, takie jak wzorce i widoki perspektyw, które pozwalają przeprowadzać i zapisywać wieloetapowe analizy głównych przyczyn.  Ponadto usługa Time Series Insights działa w połączeniu z usługami alertów, takimi jak usługa Azure Stream Analytics, dzięki czemu alerty i wykryte anomalie można oglądać w czasie niemal rzeczywistym w eksploratorze usługi Time Series Insights.  

- Globalny widok danych szeregów czasowych napływających z różnych lokalizacji na potrzeby porównania wielu zasobów/lokacji.
  - Ze środowiskiem usługi Time Series Insights można połączyć wiele źródeł zdarzeń.  Oznacza to, że dane przesyłane strumieniowo z wielu różnych lokalizacji można wyświetlać razem w czasie niemal rzeczywistym.  Użytkownicy mogą wykorzystać tę cechę do udostępniania danych liderom biznesowym i zapewniania lepszej współpracy z ekspertami w danej dziedzinie, którzy mogą swoją specjalistyczną wiedzą wspomóc rozwiązywanie problemów, stosowanie najlepszych rozwiązań i udostępnianie informacji.

- Tworzenie aplikacji klienta na bazie usługi Time Series Insights. 
  - Usługa Time Series Insights uwidacznia interfejsy API REST zapytań pozwalające na tworzenie aplikacji korzystających z danych szeregów czasowych.

## <a name="capabilities"></a>Możliwości

- **Szybkie rozpoczęcie pracy:** usługa Azure Time Series Insights nie wymaga wcześniejszego przygotowania danych. W ciągu kilku minut możliwe jest połączenie się z milionami zdarzeń w usłudze Azure IoT Hub lub centrum zdarzeń. Po połączeniu możesz wchodzić w interakcję z danymi czujników i wizualizować je, aby szybko zweryfikować swoje rozwiązania IoT. Ze swoimi danymi możesz wchodzić w interakcję bez pisania kodu.
Nie trzeba uczyć się nowego języka — usługa Time Series Insights udostępnia szczegółową warstwę zapytań obejmujących dowolny tekst przeznaczoną dla zaawansowanych użytkowników oraz eksplorowanie za pomocą wskazywania i kliknięć.

- **Szczegółowe informacje niemal w czasie rzeczywistym:** usługa Time Series Insights może pozyskiwać miliony zdarzeń z czujników dziennie z minutowym opóźnieniem. Usługa Time Series Insights umożliwia uzyskiwanie szczegółowych informacji dotyczących danych z czujników dzięki ułatwieniu wykrywania trendów i anomalii, przeprowadzaniu analiz głównych przyczyn problemów i unikaniu kosztownych przestojów. Po włączeniu korelacji między informacjami uzyskiwanymi w czasie rzeczywistym i danymi historycznymi usługa Time Series Insights pomaga uwidocznić ukryte trendy w danych.

- **Tworzenie niestandardowych rozwiązań:** dane usługi Azure Time Series Insights można osadzić w istniejących aplikacjach. Przy użyciu interfejsów API REST usługi Time Series Insights można również tworzyć nowe, niestandardowe rozwiązania. Utwórz widoki spersonalizowane, które można udostępniać innym, aby umożliwić im eksplorowanie Twoich szczegółowych danych.

- **Skalowalność:** usługa Time Series Insights obsługuje urządzenia IoT na dużą skalę. Umożliwia pozyskiwanie od 1 miliona do 100 milionów zdarzeń dziennie, które domyślnie są przechowywane przez 31 dni. Możliwe jest wizualizowanie i analizowanie strumieni bieżących danych niemal w czasie rzeczywistym na tle danych historycznych. Skala przyszłych możliwości w zakresie pozyskiwania i przechowywania danych jeszcze się zwiększy w celu dostosowania ich do potrzeb przedsiębiorstw.

## <a name="getting-started"></a>Wprowadzenie

Rozpoczęcie pracy zajmuje mniej niż 5 minut.

1. W tym celu aprowizuj środowisko usługi Time Series Insights w witrynie Azure Portal.
1. Połącz źródło zdarzeń, takie jak usługa Azure IoT Hub lub centrum zdarzeń.  
1. Przekaż dane referencyjne (nie jest to usługa dodatkowa).
1. Po kilku minutach zobaczysz swoje dane w eksploratorze usługi Time Series Insights.

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Ten diagram przedstawia przykład szczegółowych informacji o danych szeregów czasowych wyświetlanych za pomocą eksploratora: ![Eksplorator usługi Time Series Insights](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się z ogólną dostępność usługi Azure Time Series Insights [bezpłatnym środowisku pokazowym](./time-series-quickstart.md).

- Dowiedz się więcej o [planowania usługi Time Series Insights](time-series-insights-environment-planning.md) środowiska.
