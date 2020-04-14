---
title: 'Omówienie: Co to jest usługa Azure Time Series Insights Preview? — usługa Azure Time Series Insights | Dokumenty firmy Microsoft'
description: Dowiedz się więcej o zmianach, ulepszeniach i funkcjach w usłudze Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 73244a635bbf14efcf33f1b978db14e9e2589581
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271067"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Co to jest wersja zapoznawcza usługi Azure Time Series Insights?

Usługa Azure Time Series Insights Preview to kompleksowa oferta platformy jako usługi (PaaS). Można go używać do zbierania, przetwarzania, przechowywania, analizowania i wyszukiwania danych w skali Internetu rzeczy (IoT) — danych, które są bardzo kontekstowe i zoptymalizowane pod kątem szeregów czasowych. 

Usługa Time Series Insights jest przeznaczona do doraźnej eksploracji danych i analizy operacyjnej. Jest to rozszerzalna i dostosowana oferta usług, która spełnia szerokie potrzeby przemysłowych wdrożeń IoT.

## <a name="video"></a>Film wideo

Dowiedz się więcej o usłudze Azure Time Series Insights Preview.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definicja danych IoT

Przemysłowe dane IoT w organizacjach intensywnie korzystających z zasobów często nie mają spójności strukturalnej ze względu na zróżnicowany charakter urządzeń i czujników w środowisku przemysłowym. Dane z tych strumieni charakteryzują się znacznymi przerwami, a czasami uszkodzonymi wiadomościami i fałszywymi odczytami. Dane IoT często mają znaczenie w kontekście dodatkowych danych wejściowych pochodzących ze źródeł zewnętrznych lub zewnętrznych, takich jak CRM lub ERP, które dodają kontekst dla kompleksowych przepływów pracy. Dane wejściowe ze źródeł danych innych firm, takich jak dane pogodowe, mogą pomóc w powiększenie strumieni telemetrycznych w danej instalacji. 

Wszystko to oznacza, że tylko ułamek danych jest używany do celów operacyjnych i biznesowych, a analiza wymaga kontekstualizacji. Dane przemysłowe są często historycznie analizowane w dłuższych przedziałach czasowych w celu zrozumienia i skorelowania trendów. Przekształcanie zbieranych danych IoT na szczegółowe informacje umożliwiające podejmowanie działań wymaga następujących elementów: 

* przetwarzanie danych w celu oczyszczenia, przefiltrowania, interpolacji, przekształcenia i przygotowania danych do analizy;
* Struktura do nawigacji i zrozumienia danych, czyli do normalizacji i kontekstualizowania danych.
* Ekonomiczne przechowywanie długich lub nieskończonych przechowywania przetworzonych (lub pochodnych) danych i nieprzetworzonych danych.

Takie dane dostarczają spójnych, kompleksowych, aktualnych i prawidłowych informacji do analizy biznesowej i raportowania.

Na poniższej ilustracji przedstawiono typowy przepływ danych IoT.

[![Przepływ danych IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Usługa Azure Time Series Insights dla przemysłowego Internetu rzeczy

Krajobraz IoT jest zróżnicowany wśród klientów obejmujących różne segmenty przemysłu, w tym produkcję, motoryzację, energię, usługi użyteczności publicznej, inteligentne budynki i doradztwo. W całej tej szerokiej gamie przemysłowego rynku IoT wciąż ewoluują rozwiązania natywne dla chmury, które zapewniają kompleksową analizę ukierunkowaną na dane IoT na dużą skalę. 

Usługa Azure Time Series Insights zaspokaja to potrzeby rynku, udostępniając gotowe, kompleksowe rozwiązanie analityczne IoT z bogatym modelowania semantycznym do kontekstualizacji danych szeregów czasowych, szczegółowych informacji opartych na zasobach i najlepszego w swojej klasie środowiska użytkownika do odnajdowania, trendów, wykrywania anomalii i analizy operacyjnej. 

Dzięki bogatej platformie analizy operacyjnej w połączeniu z naszymi interaktywnymi funkcjami eksploracji danych można użyć usługi Time Series Insights, aby uzyskać większą wartość danych zebranych z zasobów IoT. Oferta wersji zapoznawczej zapewnia następujące składniki: 

* Wielowarstwowe rozwiązanie pamięci masowej z obsługą ciepłej i zimnej analizy zapewniając klientom możliwość kierowania danych między ciepłymi i zimnymi do interaktywnej analizy na ciepłych danych, a także analizy operacyjnej na przestrzeni dziesięcioleci danych historycznych. 

    *    Wysoce interaktywne rozwiązanie do analizy ciepłej do wykonywania częstych i dużej liczby zapytań w krótszym czasie pracy danych 
    *    Skalowalne, wydajne i zoptymalizowane pod względem kosztów usługi usługi Time Series data lake na podstawie usługi Azure Storage, umożliwiające klientom wyznaczanie wartości danych szeregów czasowych w ciągu kilku sekund. 

* Obsługa modelu semantycznego, która opisuje domenę i metadane skojarzone z pochodnymi i nieprzetworzonymi sygnałami z zasobów i urządzeń.

* Elastyczna platforma analityczna do przechowywania historycznych danych szeregów czasowych na koncie usługi Azure Storage należącym do klienta, umożliwiając klientom posiadanie ich danych IoT. Dane są przechowywane w formacie Apache Parquet typu open source, który umożliwia łączność i współdziałać w różnych scenariuszach danych, w tym analizie predykcyjnej, uczeniu maszynowym i innych obliczeniach niestandardowych wykonywanych przy użyciu znanych technologii, takich jak Spark, Databricks i Jupyter.

* Bogata analiza z ulepszonymi interfejsami API zapytań i środowiskami użytkownika, która łączy szczegółowe informacje o danych opartych na zasobach z bogatą, doraźną analizą danych z obsługą funkcji interpolacji, skalarnych i zbiorczych, zmiennych kategorycznych, wykresów punktowych i sygnałów szeregów czasowych przesunięciem czasu do dogłębnej analizy.

*    Platforma klasy korporacyjnej do obsługi potrzeb w zakresie skali, wydajności, bezpieczeństwa i niezawodności naszych klientów IoT w przedsiębiorstwie.

* Obsługa rozszerzalności i integracji w zakresie kompleksowej analizy. Usługa Time Series Insights zapewnia rozszerzalną platformę analityczną dla różnych scenariuszy danych. Łącznik usługi Power BI usługi Time Series Insights umożliwia klientom wykonywanie zapytań, które wykonują w usłudze Time Series Insights bezpośrednio do usługi Power BI, aby uzyskać ujednolicony widok analizy bi i szeregów czasowych w jednym okienku szkła.

Na poniższym diagramie przedstawiono przepływ danych wysokiego poziomu.

  [![Najważniejsze możliwości](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Usługa Azure Time Series Insights udostępnia skalowalny model cenowy płatności zgodnie z rzeczywistym użyciem do przetwarzania danych, magazynu (danych i metadanych) i zapytań, umożliwiając klientom dostosowanie ich użycia do ich potrzeb biznesowych. 
 
Wraz z wprowadzeniem tych kluczowych przemysłowych funkcji IoT usługa Time Series Insights zapewnia również następujące kluczowe korzyści.  

| | |
| ---| ---|
| Wielowarstwowa pamięć masowa dla danych serii czasowych skali IoT | Dzięki potokowi przetwarzania danych udostępnionych do pozyskiwania danych można pozyskiwania danych zarówno w ciepłych, jak i chłodniach. Użyj ciepłego magazynu dla zapytań interaktywnych i chłodni do przechowywania dużych ilości danych. Aby dowiedzieć się więcej o tym, jak korzystać z zaawansowanych zapytań opartych na zasobach, zobacz [kwerendy](./time-series-insights-update-tsq.md). |
| Model szeregu czasowego na potrzeby określania kontekstu nieprzetworzonych danych telemetrycznych i uzyskiwania szczegółowych danych opartych na zasobach | Model szeregów czasowych służy do tworzenia wystąpień, hierarchii, typów i zmiennych dla danych szeregów czasowych. Aby dowiedzieć się więcej o modelu szeregów czasowych, zobacz [Model szeregów czasowych](./time-series-insights-update-tsm.md).  |
| Bezproblemowa i ciągła integracja z innymi rozwiązaniami do obsługi danych | Dane w chłodni Time Series Insights są [przechowywane](./time-series-insights-update-storage-ingress.md) w plikach parquet Apache open source. Umożliwia to integrację danych z innymi rozwiązaniami do danych, jedną lub trzecią firmą, w przypadku scenariuszy obejmujących analizę biznesową, zaawansowane uczenie maszynowe i analizę predykcyjną. |
| Eksploracja danych w czasie niemal rzeczywistym | Środowisko użytkownika [eksploratora usługi Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md) udostępnia wizualizacje dla wszystkich danych przesyłanych strumieniowo za pośrednictwem potoku pozyskiwania. Po podłączeniu źródła zdarzeń można wyświetlać, eksplorować i badać dane zdarzeń. W ten sposób można sprawdzić, czy urządzenie emituje dane zgodnie z oczekiwaniami. Można również monitorować kondycję, wydajność i ogólną efektywność zasobu IoT. | 
| Rozszerzalność i integracja | Integracja programu Azure Time Series Insights z programem Power BI Connector jest dostępna bezpośrednio w eksploratorze szeregów czasowych za pośrednictwem opcji **Eksportuj,** umożliwiając klientom eksportowanie zapytań szeregów czasowych, które tworzą w naszym interfejsie użytkownika bezpośrednio do pulpitu usługi Power BI, i wyświetlanie ich wykresów szeregów czasowych wraz z innymi analizami analizy analizy biznesowej. Otwiera to drzwi do nowej klasy scenariuszy dla przemysłowych przedsiębiorstw IoT, które zainwestowały w usłudze Power BI, udostępniając pojedynczą taflę szkła za pomocą analizy z różnych źródeł danych, w tym szeregów czasowych IoT. | 
| Aplikacje niestandardowe oparte na platformie Time Series Insights | Usługa Time Series Insights obsługuje [zestaw SDK języka JavaScript](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Ten zestaw SDK zapewnia zaawansowane kontrolki i uproszczony dostęp do zapytań. Użyj SDK do tworzenia niestandardowych aplikacji IoT na podstawie usługi Time Series Insights zgodnie z potrzebami biznesowymi. Można również bezpośrednio korzystać z [interfejsów API zapytań](./time-series-insights-update-tsq.md) usługi Time Series Insights, aby kierować dane do niestandardowych aplikacji IoT. |

## <a name="next-steps"></a>Następne kroki

Wprowadzenie do usługi Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Przewodnik Szybki start](./time-series-insights-update-quickstart.md)

Dowiedz się więcej na temat przypadków użycia:

> [!div class="nextstepaction"]
> [Przypadki użycia usługi Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-use-cases.md)