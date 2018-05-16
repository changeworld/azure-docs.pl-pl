---
title: Samodzielnie realizowany pokaz rozwiązania usługi Service Map na platformie Azure | Microsoft Docs
description: Usługa Service Map jest rozwiązaniem platformy Azure, które automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Jest to pokaz realizowany samodzielnie, który demonstruje użycie usługi Service Map do zidentyfikowania i zdiagnozowania symulowanego problemu w aplikacji internetowej.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: monitoring
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: b406cb2a9e96e3ba3514ed08c20483df57de9c71
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="self-paced-demo---service-map"></a>Pokaz realizowany samodzielnie — Service Map
Jest to pokaz realizowany samodzielnie, który demonstruje użycie [rozwiązania Service Map](monitoring-service-map.md) na platformie Azure do zidentyfikowania i zdiagnozowania symulowanego problemu w aplikacji internetowej. Mapa usługi automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Konsoliduje ona również dane zebrane przez inne usługi i rozwiązania, aby pomóc w analizowaniu wydajności oraz identyfikowaniu problemów. Korzystasz także z funkcji [przeszukiwania dzienników w usłudze Log Analytics](../log-analytics/log-analytics-log-searches.md), aby przejść do szczegółów zebranych danych w celu zidentyfikowania głównego problemu.


## <a name="scenario-description"></a>Opis scenariusza
Właśnie odebrano powiadomienie, że aplikacja ACME Customer Portal ma problemy z wydajnością. Jedyną dostępną informacją jest godzina, o której problemy się zaczęły, tj. 4:00 czasu PST. Nie masz całkowitej pewności co do wszystkich składników, od których portal jest zależny, z wyjątkiem zestawu serwerów sieci Web. 

## <a name="components-and-features-used"></a>Używane składniki i funkcje
- [Rozwiązanie mapy usługi](monitoring-service-map.md)
- [Przeszukiwanie dzienników w usłudze Log Analytics](../log-analytics/log-analytics-log-searches.md)


## <a name="walkthrough"></a>Przewodnik

### <a name="1-connect-to-the-oms-experience-center"></a>1. Nawiąż połączenie z centrum OMS Experience Center
Ten przewodnik korzysta z centrum [Operations Management Suite Experience Center](https://experience.mms.microsoft.com/), które zapewnia kompletne środowisko usługi Log Analytics z przykładowymi danymi. Rozpocznij, klikając ten link, podaj informacje, a następnie wybierz scenariusz **Insight and Analytics**.


### <a name="2-start-service-map"></a>2. Uruchamianie mapy usługi
Uruchom rozwiązanie mapy usługi, klikając kafelek **Mapa usługi**.

![Kafelek Mapa usługi](media/monitoring-walkthrough-servicemap/tile.png)

Zostanie wyświetlona konsola mapy usługi. W okienku po lewej stronie znajduje się lista komputerów w Twoim środowisku z zainstalowanym agentem mapy usługi. Z tej listy wybierasz komputer do wyświetlenia.

![Lista komputerów](media/monitoring-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3. Wyświetl komputer
Wiesz, że serwery internetowe mają nazwy AcmeWFE001 i AcmeWFE002, więc to uzasadnione miejsce do rozpoczęcia. Kliknij serwer **AcmeWFE001**. Spowoduje to wyświetlenie mapy dla serwera AcmeWFE001 i wszystkich jego zależności. Widoczne są procesy uruchomione na wybranym komputerze i usługi zewnętrzne, z którymi się komunikują.

![Serwer sieci Web](media/monitoring-walkthrough-servicemap/web-server.png)

Interesuje Cię wydajność aplikacji internetowej, więc kliknij proces **AcmeAppPool (Pula aplikacji IIS)**. Spowoduje to wyświetlenie szczegółów tego procesu i wyróżnienie jego zależności. 

![Pula aplikacji](media/monitoring-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4. Zmień przedział czasu

Wiesz, że problem rozpoczął się o godz. 4:00, więc zobaczmy, co się działo w tym czasie. Kliknij pozycję **Zakres czasu** i zmień godzinę na 4:00 czasu PST (zachowaj bieżącą datę i dostosuj godzinę dla lokalnej strefy czasowej), a czas trwania na 20 minut.

![Selektor czasu](./media/monitoring-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5. Wyświetl alert

Teraz widzisz, że dla zależności **acmetomcat** jest wyświetlany alert, więc to jest potencjalny problem. Kliknij ikonę alertu serwera **acmetomcat**, aby wyświetlić jego szczegóły. Widzisz, że masz krytyczne wykorzystanie procesora CPU, i możesz rozwinąć informacje, aby uzyskać więcej szczegółów. Prawdopodobnie to jest właśnie przyczyna niskiej wydajności. 

![Alerty](./media/monitoring-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6. Wyświetl wydajność

Przyjrzyjmy się bliżej serwerowi **acmetomcat**. Kliknij w prawym górnym rogu serwera **acmetomcat** i wybierz pozycję **Załaduj mapę serwera**, aby wyświetlić szczegóły i zależności dla tej maszyny. Możesz przyjrzeć się bliżej licznikom wydajności, aby zweryfikować swoje podejrzenia. Wybierz kartę **Wydajność**, aby wyświetlić [liczniki wydajności zebrane przez usługę Log Analytics](../log-analytics/log-analytics-data-sources-performance-counters.md) w całym zakresie czasu. Widoczne jest okresowe wysokie wykorzystanie procesora i pamięci.

![Wydajność](./media/monitoring-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7. Wyświetl dane śledzenia zmian
Spróbujmy dowiedzieć się, co jest przyczyną tego wysokiego wykorzystania. Kliknij kartę **Podsumowanie**. Zawiera ona informacje zebrane przez usługę Log Analytics z komputera, np. informacje o połączeniach zakończonych niepowodzeniem, alertach krytycznych i zmianach oprogramowania. Sekcje z odpowiednimi aktualnymi informacjami powinny być już rozwinięte. Możesz też rozwinąć inne sekcje w celu inspekcji zawartych w nich informacji.


Jeśli sekcja **Śledzenie zmian** nie jest jeszcze otwarta, rozwiń ją. Pokazuje ona informacje zebrane przez [rozwiązanie do śledzenia zmian](../log-analytics/log-analytics-change-tracking.md). Wygląda na to, że w tym przedziale czasu nastąpiła zmiana oprogramowania. Kliknij kartę **Oprogramowanie** w celu uzyskania szczegółowych informacji. Proces tworzenia kopii zapasowej został dodany do maszyny zaraz po godzinie 4:00, więc prawdopodobnie to jest przyczyna nadmiernego korzystania z zasobów.

![Śledzenie zmian](./media/monitoring-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8. Wyświetl szczegóły w funkcji przeszukiwania dzienników
Możesz to potwierdzić, przeglądając szczegółowe informacje dotyczące wydajności zebrane w obszarze roboczym usługi Log Analytics. Ponownie kliknij kartę **Alerty**, a następnie kliknij jeden z alertów **Wysokie użycie procesora CPU**. Kliknij pozycję **Pokaż podczas wyszukiwania dziennika**. Spowoduje to otwarcie okna Przeszukiwanie dzienników, w którym można [przeszukiwać dzienniki](../log-analytics/log-analytics-log-searches.md) pod kątem danych przechowywanych w obszarze roboczym. Usługa Service Map już wypełniła zapytanie w celu pobrania alertu, który nas interesuje. 

![Wyszukiwanie w dzienniku](./media/monitoring-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9. Otwórz zapisane wyszukiwanie
Zobaczmy, czy uda nam się uzyskać więcej szczegółów na temat kolekcji danych wydajności, która wygenerowała ten alert, i potwierdzić podejrzenie, że problemy powoduje proces tworzenia kopii zapasowej. Zmień zakres czasu na **6 godzin**. Następnie kliknij kartę **Ulubione** i przewiń w dół do zapisanych wyszukiwań w sekcji **Mapa usługi**. Te zapytania zostały utworzone specjalnie na potrzeby tej analizy. Kliknij zapytanie **5 procesów najbardziej obciążających procesor CPU serwera acmetomcat**.

![Zapisane wyszukiwanie](./media/monitoring-walkthrough-servicemap/saved-search.png)


To zapytanie zwraca listę 5 procesów najbardziej obciążających procesor serwera **acmetomcat**. Możesz sprawdzić zapytanie w celu wstępnego zapoznania się z językiem zapytań używanym do przeszukiwania dzienników. Jeśli interesują Cię procesy na innych komputerach, możesz zmodyfikować zapytanie, aby pobrać te informacje.

W tym przypadku widać, że proces tworzenia kopii zapasowej stale używa około 60% procesora CPU serwera aplikacji. Jest oczywiste, że ten nowy proces jest odpowiedzialny za problem z wydajnością. Twoim rozwiązaniem byłoby usunięcie tego nowego oprogramowania do tworzenia kopii zapasowych z serwera aplikacji. Możesz użyć funkcji Desired State Configuration (DSC) zarządzanej przez usługę Azure Automation w celu zdefiniowania zasad, które zapewnią, że ten proces nigdy nie będzie uruchamiany w systemach krytycznych.


## <a name="summary-points"></a>Punkty podsumowujące
- [Mapa usługi](monitoring-service-map.md) udostępnia widok całej aplikacji nawet wtedy, gdy nie znasz wszystkich jej serwerów i zależności.
- Usługa Service Map prezentuje dane zbierane przez inne rozwiązania do zarządzania, aby pomóc w identyfikowaniu problemów dotyczących aplikacji i jej podstawowej infrastruktury.
- [Przeszukiwanie dzienników](../log-analytics/log-analytics-log-searches.md) pozwala na przechodzenie do szczegółów określonych danych zebranych w obszarze usługi Log Analytics.  

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [mapie usługi](monitoring-service-map.md).
- [Wdróż i skonfiguruj](monitoring-service-map-configure.md) mapę usługi.
- Dowiedz się więcej o usłudze [Log Analytics](../log-analytics/log-analytics-overview.md), która jest wymagana, aby korzystać z usługi Service Map, i zawiera dane operacyjne przechowywane przez agentów.