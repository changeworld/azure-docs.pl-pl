---
title: Mapa aplikacji w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Monitorowanie aplikacji złożonych topologii z mapy aplikacji
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3bf37fe9-70d7-4229-98d6-4f624d256c36
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/14/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 9b39eef5accec4764f61ab31dd894d368242ee3d
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47094654"
---
# <a name="application-map-triage-distributed-applications"></a>Mapa aplikacji: Klasyfikowanie aplikacji rozproszonych

Mapa aplikacji pomaga wąskich gardeł wydajności dodatkowych lub niepowodzenie obszarów nadmiernej aktywności dotyczące wszystkich składników aplikacji rozproszonej. Każdy węzeł na mapie reprezentuje składnik aplikacji lub jej zależności ma kondycji kluczowy wskaźnik wydajności i alerty stanu. Kliknij z dowolnego składnika do bardziej szczegółowe dane diagnostyczne, takie jak zdarzenia usługi Application Insights. Jeśli aplikacja korzysta z usług platformy Azure, możesz również kliknąć za pośrednictwem do diagnostyki platformy Azure, takie jak zalecenia usługi Advisor z bazy danych SQL.

## <a name="what-is-a-component"></a>Co to jest składnikiem?

Składniki są niezależne wdrażanym poszczególnych części aplikacji rozproszonych mikrousług. Zespoły deweloperów i operacyjne mają widoczność na poziomie kodu lub dostęp do telemetrii wygenerowanej przez te składniki aplikacji. 

* Składniki różnią się od "obserwowanych" zależności zewnętrzne, takie jak SQL, itp. Centrum zdarzeń, które zespół/organizacji nie mogą mieć dostęp do (kod lub telemetrii).
* Składniki są uruchomione na dowolną liczbę wystąpień kontenera server/roli.
* Składniki mogą być oddzielnych kluczy Instrumentacji usługi Application Insights (nawet jeśli różnią się subskrypcje) lub różne role, raportowanie do jednego klucza Instrumentacji usługi Application Insights. Środowisko podglądu tak mapy przedstawiono składniki, niezależnie od tego, jak ustawić.

## <a name="composite-application-map"></a>Mapa aplikacji złożonej

Możesz zobaczyć topologia pełnej aplikacji na wielu poziomach składników powiązane aplikacji. Składniki mogą być, różne zasoby usługi Application Insights lub różne role w pojedynczym zasobie. Mapa aplikacji umożliwia znalezienie składników przez następujące HTTP zależności wywołań między serwerami za pomocą zainstalować zestaw Application Insights SDK. 

To środowisko rozpoczyna się od progresywnego odnajdywania składników. Podczas pierwszego ładowania mapy aplikacji, zestawu zapytań są wyzwalane odnajdywania składników powiązane z tego składnika. Przycisk w lewym górnym rogu zostanie zaktualizowana o liczbę składników w aplikacji, zgodnie z ich odnalezieniu. 

Po kliknięciu przycisku "Aktualizuj składniki mapy", mapy zostaną odświeżone przy użyciu wszystkich składników wykryte do momentu na tym etapie.

Jeśli wszystkie składniki są ról w ramach pojedynczego zasobu usługi Application Insights, ten krok odnajdowania nie jest wymagane. Ładowania początkowego dla takich aplikacji będzie miał wszystkie jego składniki.

![Zrzut ekranu z mapy aplikacji](media/app-insights-app-map/001.png)

Jednym z celów klucza z tego środowiska jest możliwe do wizualizacji złożonych topologii z setkami składników.

Kliknij dowolny składnik, aby zobaczyć powiązane szczegółowe informacje, a następnie przejdź do wydajności i dokładniejszej klasyfikacji błędów danego składnika.

![Okno wysuwane](media/app-insights-app-map/application-map-001.png)

### <a name="investigate-failures"></a>Zbadaj błędy

Wybierz **zbadaj błędy** można uruchomić z okienka błędów.

![Zrzut ekranu przedstawiający zbadaj błędy przycisku](media/app-insights-app-map/investigate-failures.png)

![Zrzut ekranu przedstawiający obsługi błędów](media/app-insights-app-map/failures.png)

### <a name="investigate-performance"></a>Zbadaj wydajność

Rozwiązywania problemów z wydajnością problemów wybierz **badanie wydajności**

![Zrzut ekranu przedstawiający badanie wydajności przycisku](media/app-insights-app-map/investigate-performance.png)

![Zrzut ekranu przedstawiający wydajność](media/app-insights-app-map/performance.png)

### <a name="go-to-details"></a>Przejdź do szczegółów

Wybierz **przejdź do szczegółów** Eksplorowanie środowiska transakcji end-to-end, które może zaoferować widoków gotowe, aby poziom stosu wywołań.

![Zrzut ekranu przedstawiający przycisk Przejdź do szczegółów](media/app-insights-app-map/go-to-details.png)

![Zrzut ekranu przedstawiający szczegóły transakcji end-to-end](media/app-insights-app-map/end-to-end-transaction.png)

### <a name="view-in-analytics"></a>Wyświetl w usłudze Analytics

Do tworzenia zapytań i zbadaj kliknięcia dalsze dane aplikacji **Wyświetl w obszarze analiza**.

![Zrzut ekranu przedstawiający widok przycisk Analiza](media/app-insights-app-map/view-in-analytics.png)

![Zrzut ekranu przedstawiający środowisko analizy](media/app-insights-app-map/analytics.png)

### <a name="alerts"></a>Alerty

Zaznacz, aby wyświetlić aktywne alerty i podstawowych reguł, które alerty można tiggered **alerty**.

![Zrzut ekranu przedstawiający przycisk alertów](media/app-insights-app-map/alerts.png)

![Zrzut ekranu przedstawiający środowisko analizy](media/app-insights-app-map/alerts-view.png)

## <a name="video"></a>Połączenia wideo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="feedback"></a>Opinia
Przekaż opinię za pośrednictwem portalu opinie.

![Obraz MapLink-1](./media/app-insights-app-map/13.png)

## <a name="next-steps"></a>Kolejne kroki

* [Azure Portal](https://portal.azure.com)
