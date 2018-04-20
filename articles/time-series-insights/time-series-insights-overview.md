---
title: Co to jest Azure czas serii Insights? | Microsoft Docs
description: Wprowadzenie do usługi Azure czas serii Insights, nową usługę dla analizy danych serii czasu i rozwiązania IoT.
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan, jasonh
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 01/26/2018
ms.openlocfilehash: e31cebfd027e93096e233f2963445e4fc50a7e9d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="what-is-azure-time-series-insights"></a>Co to jest Azure czas serii Insights?

Czas serii Insights zaprojektowano pod kątem przechowywania, wizualizowanie i badania dużych ilości danych serii czasu, takich jak generowany przez urządzenia IoT.  Jeśli chcesz przechowywać, zarządzanie, zapytania lub wizualizacji danych w serii. czas w chmurze, czas serii Insights prawdopodobnie dla odpowiedni.  

! [Flowchart Insights serii czasu] (media/overview/time-series-insights-flowchart.png)

Czas serii Insights ma cztery zadania klucza:

- Najpierw należy go jest w pełni zintegrowana z bramy chmury, takich jak Centrum IoT Azure i usługi Azure Event Hubs. Możesz łatwo łączy do tych źródeł zdarzeń i analizuje JSON z wiadomości i struktury danych w czystej wierszy i kolumn. Sprzężenia metadanych z telemetrii, a dane w magazynie kolumnowy indeksy.
- Po drugie czas serii Insights zarządza magazynu danych. Aby upewnić się, że zawsze są łatwo dostępne dane, przechowuje dane w pamięci i SSD na maksymalnie 400 dni. Miliardów zdarzeń może interakcyjnie zapytania w sekundach — na żądanie.
- Trzecie czas serii Insights udostępnia poza okno wizualizacji za pomocą Eksploratora TSI.  
- Czwarty czas serii Insights udostępnia usługę zapytania, zarówno w Eksploratorze TSI, jak i za pomocą interfejsów API, które są łatwo ją zintegrować dla osadzanie danych serii czasu w aplikacje niestandardowe.  

Jeśli tworzysz aplikację na użytek wewnętrzny lub zewnętrzny klientów do użycia, czas serii Insights może służyć jako zaplecza do indeksowania, przechowywania i agregowanie danych serii czasu. Można tworzyć niestandardowe wizualizacji i środowisko użytkownika w górnej części.  Czas serii Insights udostępnia interfejsy API zapytania do realizacji tego scenariusza.  

Jeśli nie wiesz, jeśli dane są szeregów czasowych, Oto co należy wiedzieć.  Czas serii danych reprezentuje jak zasobów lub proces zmienia się wraz z upływem czasu.  Jest on unikatowy, w tym ma sygnaturę czasową, a czas jest największe znaczenie jako oś.  Czas serii danych zwykle dociera w kolejności czasu i zwykle jest traktowana jako instrukcji insert, a nie aktualizacji do bazy danych.  Ponieważ Insights serii czasu powoduje przechwycenie i przechowywanie każdej nowe zdarzenie jako wiersz, zmiany jest mierzony w czasie, umożliwiając do wyszukiwania do tyłu i do przewidywania przyszłych zmian.  W dużych woluminów przechowywania, indeksowania, zapytań, analizy i Środek wywołujący danych serii czasu może być trudne.  

## <a name="primary-scenarios"></a>Podstawowe scenariusze

- Przechowywanie danych w serii. czas w sposób skalowalny.  
  - Zasadniczo Insights serii czasu ma bazę danych z danych w serii. czas na uwadze.  Ponieważ jest ona skalowalna i w pełni zarządzana, czas serii Insights obsługuje pracy przechowywania i zarządzania nimi zdarzeń.

- Niemal Eksploracja danych w czasie rzeczywistym.  
  - Czas serii Insights udostępnia explorer wizualizuje wszystkich danych przesyłania strumieniowego w środowisku.  Wkrótce po nawiązaniu połączenia źródła zdarzenia można wyświetlić dane zdarzenia, eksplorowanych i kwerendy w czasie serii Insights.  Dane są przydatne do sprawdzania, czy urządzenie jest emitowanie danych zgodnie z oczekiwaniami i monitorowania zasobów IoT kondycji, wydajność i skuteczność ogólnej.  

- Wykrywanie analizy i anomalii głównej przyczyny problemu.
  - Czas serii Insights ma narzędzi, takich jak wzorców i widoki perspektywy prowadzenie i zapisania analizy wieloetapowych przyczyn.  Ponadto czas serii Insights działa w połączeniu z alertów usług, takich jak usługi Azure Stream Analytics, alerty i wykrytych anomalii, które mogą być wyświetlane w pobliżu w czasie rzeczywistym w Eksploratorze Insights serii czasu.  

- Globalny widok danych serii czasu przesyłania strumieniowego z różnych lokalizacji dla porównania kilku asset/lokacji.
  - Wiele źródeł zdarzeń może nawiązać środowisku Insights serii czasu.  Oznacza to, że przesyłanie strumieniowe w wielu różnych lokalizacjach danych mogą być wyświetlane razem w niemal w czasie rzeczywistym.  Użytkownicy mogą wykorzystać to wgląd w udostępniać dane biznesowe liderów i umożliwiają lepsze współpracę z ekspertami domeny, które można zastosować ich wiedzy na rozwiązywanie problemów, zastosowanie najlepszych rozwiązań i udostępniać learnings.

- Tworzenie aplikacji klienta na podstawie czasu Insights serii. 
  - Czas serii Insights udostępnia interfejsy API zapytania REST, umożliwiające tworzenie aplikacji korzystających z czasu serii danych.

## <a name="capabilities"></a>Możliwości

- **Szybkie rozpoczęcie pracy:** Azure czas serii Insights wymaga nie przygotowania początkowych danych. W ciągu minut nawiązać miliony zdarzeń w Centrum IoT Azure lub Centrum zdarzeń. Po nawiązaniu połączenia wizualizacji i interakcję z danych czujnika można szybko sprawdzić poprawności Twojego rozwiązania IoT. Możesz użyć danych bez pisania kodu.
Brak nie nowego języka Aby dowiedzieć się więcej; Czas serii Insights udostępnia surface szczegółowego, niezależnych kwerendy dla użytkowników zaawansowanych i i kliknij opcję eksploracji.
- **Wgląd w czasie rzeczywistym w pobliżu:** Insights serii czasu może obsługiwać miliony czujnik zdarzeń na dzień, z opóźnieniem jednej minuty. Czas serii Insights pomaga uzyskać wgląd w dane czujników pomagając tendencje i anomalie, należy przeprowadzić analizy głównej przyczyny problemu i uniknąć kosztownych przestojów. Po włączeniu korelacji między informacjami uzyskiwanymi w czasie rzeczywistym i danymi historycznymi usługa Time Series Insights pomaga uwidocznić ukryte trendy w danych.
- **Tworzyć niestandardowe rozwiązania:** osadzić Azure czas serii Insights danych do istniejących aplikacji, lub Utwórz nowe niestandardowe rozwiązania o czasie interfejsów API REST szczegółowych informacji dla serii. Utwórz widoki spersonalizowane, można udostępniać innym użytkownikom poznać szczegółowe dane.
- **Skalowalność:** Insights serii czasu jest przeznaczony do obsługi IoT na dużą skalę. Pozwala transfer danych przychodzących z zakresu od 1 milionów do 100 milionów zdarzeń na dzień z przechowywaniem domyślny zakres 31 dni. Możesz wizualizować i analizować dane na żywo strumieni w pobliżu w czasie rzeczywistym, obok danych historycznych. Przenoszenie do przodu, ruch przychodzący i przechowywania zwiększy aby zmieścił się w skali przedsiębiorstwa.

## <a name="getting-started"></a>Wprowadzenie
Wprowadzenie ma mniej niż 5 minut. 

1.  Aby rozpocząć korzystanie udostępniania środowisku czasu serii wgląd w portalu Azure. 
2.  Połącz źródła zdarzenia, takie jak Azure Centrum IoT i Centrum zdarzeń.  
3.  Przekazywanie danych referencyjnych (nie jest to dodatkowe usługi).
4.  Zobacz danych w ciągu minut przy Eksplorator Insights serii czasu.

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights
Ten diagram przedstawia przykład szeregów czasowych danych usługi insights wyświetlać za pomocą Eksploratora:! [Eksplorator Insights serii czasu] (media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Kolejne kroki
 - [Eksplorowania przy użyciu Eksploratora czasu serii wgląd w środowisku pokaz](./time-series-quickstart.md)
 - [Planowanie środowiska Insights serii czasu](time-series-insights-environment-planning.md)

