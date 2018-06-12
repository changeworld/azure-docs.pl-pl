---
title: Monitor i get wgląd w aplikację logiki jest wykonywane przy użyciu Log Analytics — usługi Azure Logic Apps | Dokumentacja firmy Microsoft
description: Monitorowanie sieci jest uruchamiany aplikacji logiki z analizy dzienników, aby uzyskać szczegółowe informacje i szczegóły debugowania bardziej zaawansowane funkcje do rozwiązywania problemów i Diagnostyka
author: divyaswarnkar
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/9/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 493d82f59600d83a083bb13cdd0a21b872d77a43
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299947"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitorowanie i uzyskiwanie szczegółowych informacji o uruchomieniu aplikacji logiki z analizy dzienników

Do monitorowania i bardziej rozbudowane informacje debugowania można włączyć analizy dzienników w tym samym czasie, podczas tworzenia aplikacji logiki. Analiza dzienników zapewnia diagnostyki rejestrowania i monitorowania aplikacji logiki uruchamia się za pośrednictwem portalu Azure. Po dodaniu rozwiązania do zarządzania aplikacji logiki, otrzymasz zagregowany stan logiki aplikacji działa i konkretne szczegółowe informacje, takie jak stan, czas wykonywania, stan ponownego wysyłania i identyfikatorów korelacji.

W tym temacie pokazano, jak włączyć funkcję analizy dzienników, dzięki czemu można wyświetlać zdarzenia środowiska uruchomieniowego i uruchom danych aplikacji logiki.

 > [!TIP]
 > Aby monitorować istniejących aplikacji logiki, wykonaj następujące kroki, aby [włączyć rejestrowania diagnostycznego i wysyłać dane środowiska uruchomieniowego aplikacji logiki do analizy dzienników](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Wymagania

Przed rozpoczęciem, musisz mieć obszaru roboczego analizy dzienników. Dowiedz się [Tworzenie obszaru roboczego analizy dzienników](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Włącz rejestrowanie danych diagnostycznych podczas tworzenia aplikacji logiki

1. W [portalu Azure](https://portal.azure.com), tworzenie aplikacji logiki. Wybierz **Utwórz zasób** > **integracji przedsiębiorstwa** > **aplikacji logiki**.

   ![Tworzenie aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. W **tworzenie aplikacji logiki** wykonaj te zadania, jak pokazano:

   1. Podaj nazwę aplikacji logiki, a następnie wybierz subskrypcję platformy Azure. 
   2. Utwórz lub wybierz grupę zasobów platformy Azure.
   3. Ustaw **dziennika analizy** do **na**. 
   Wybierz obszar roboczy analizy dzienników, które chcesz wysyłać dane dotyczące aplikacji logiki uruchamia. 
   4. Gdy wszystko jest gotowe, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

      ![Tworzenie aplikacji logiki](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Po zakończeniu tego kroku, platforma Azure tworzy aplikację logiki, który jest obecnie skojarzony z obszaru roboczego analizy dzienników. 
      Ponadto ten krok również automatycznie instaluje rozwiązanie do zarządzania aplikacji logiki w obszarze roboczym.

3. Aby wyświetlić aplikację logiki uruchomieniu [wykonaj te czynności](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Instalowanie rozwiązania do zarządzania aplikacji logiki

Jeśli już włączone analizy dzienników, podczas tworzenia aplikacji logiki, Pomiń ten krok. Masz już zainstalowany rozwiązania do zarządzania aplikacji logiki.

1. W [portalu Azure](https://portal.azure.com), wybierz **więcej usług**. Wyszukaj "analizy dzienników" jako filtr, a następnie wybierz pozycję **analizy dzienników** pokazany:

   ![Wybierz pozycję "Analizy dzienników"](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. W obszarze **analizy dzienników**, Znajdź i zaznacz pozycję obszaru roboczego analizy dzienników. 

   ![Wybierz obszar roboczy analizy dzienników](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. W obszarze **zarządzania**, wybierz **portalu OMS**.

   ![Wybierz pozycję "Portalu OMS"](media/logic-apps-monitor-your-logic-apps-oms/oms-portal-page.png)

4. W obszarze **wszystkie rozwiązania**, Znajdź i wybierz Kafelek **zarządzania aplikacje logiki** rozwiązania.

   ![Wybierz pozycję "Zarządzanie aplikacje logiki"](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-management-tile2.png)

5. Aby zainstalować rozwiązania, w obszarze roboczym analizy dzienników, wybierz **Dodaj**.

   ![Wybierz opcję "Dodaj", "Zarządzania aplikacjami logiki"](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-log-analytics-workspace"></a>Widok, który uruchamia aplikację logiki w obszarze roboczym analizy dzienników

1. Aby wyświetlić liczbę i stan sekwencji aplikacji logiki, przejdź do strony Przegląd obszaru roboczego analizy dzienników. Przejrzyj szczegóły na **zarządzania aplikacje logiki** kafelka.

   ![Wyświetlanie stanu i liczba Uruchom aplikację logiki kafelka przeglądu](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

2. Aby wyświetlić podsumowanie z bardziej szczegółowymi informacjami na temat sekwencji aplikacji logiki, wybierz **zarządzania aplikacje logiki** kafelka.

   W tym miejscu że aplikacja działa logiki są pogrupowane według nazwy lub stan wykonania. Można również wyświetlić szczegółowe informacje o niepowodzeniach w akcji lub wyzwalaczy dla przebiegów aplikacji logiki.

   ![Uruchamia aplikację logiki podsumowanie stanu](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Aby wyświetlić wszystkie elementy dla konkretnej logiki aplikacji lub stanu, wybierz wiersz dla aplikacji logiki lub stanu.

   Oto przykład pokazujący wszystkie elementy aplikacji logika charakterystyczna dla:

   ![Widok jest uruchamiana dla aplikacji logiki lub stanu](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Dostępne są dwie opcje zaawansowane na tej stronie:
   * **Śledzone właściwości:** ta kolumna zawiera śledzonych właściwości, które są pogrupowane według akcje dotyczące aplikacji logiki. Aby wyświetlić właściwości śledzonych, wybierz **widoku**. Można przeszukiwać właściwości śledzonych przy użyciu filtru kolumny.
   
     ![Wyświetl właściwości śledzonych aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Wszystkie nowo dodanych właściwości śledzonych może potrwać 10 – 15 minut, zanim pojawią się po raz pierwszy. Dowiedz się [sposobu dodawania właściwości śledzonych do aplikacji logiki](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Prześlij ponownie:** można ponownie prześlij co najmniej jeden przebieg aplikacji logiki, których nie powiodła się, zakończyło się pomyślnie, lub nadal są uruchomione. Zaznacz pola wyboru dla przebiegów, które chcesz przesłać ponownie, a następnie wybierz pozycję **ponownie prześlij**. 

     ![Prześlij ponownie uruchamia aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

4. Aby filtrować wyniki, można wykonać filtrowania zarówno po stronie klienta i po stronie serwera.

   * Filtr po stronie klienta: dla każdej kolumny wybierz filtry, które mają. 
   Oto kilka przykładów:

     ![Przykładowe filtry kolumn](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Filtr po stronie serwera: okna, określony czas lub Ogranicz liczbę uruchomień, które są wyświetlane, należy użyć kontrola zakresu, w górnej części strony. 
   Domyślnie są wyświetlane tylko 1000 rekordów jednocześnie. 
   
     ![Zmień przedział czasu](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Aby wyświetlić wszystkie akcje i ich szczegóły dla określonego przebiegu, wybierz wiersz dla uruchamiania aplikacji logiki.

   Oto przykład pokazujący wszystkie akcje dla aplikacji logiki określonej Uruchom:

   ![Wyświetlanie akcji dla uruchamiania aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
6. Na każdej stronie wyników, aby wyświetlić kwerendy w trakcie wyników lub aby wyświetlić wszystkie wyniki, wybierz **Zobacz wszystkie**, który otwiera stronę wyszukiwania dziennika.
   
   ![Zobacz wszystkie na stronach wyników](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na stronie dziennik wyszukiwania
   * Aby wyświetlić wyniki zapytania w tabeli, wybierz **tabeli**.
   * Aby zmienić kwerendy, można edytować ciągu zapytania w pasku wyszukiwania. 
   Aby lepiej wykorzystać możliwości wybierz **zaawansowane analizy**.

     ![Wyświetlanie akcji i szczegóły dotyczące uruchamiania aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     W tym miejscu na stronie usługi Analiza dzienników Azure można aktualizować zapytania i wyświetlić wyniki z tabeli. 
     To zapytanie używa [język zapytań Kusto](https://docs.loganalytics.io/docs/Language-Reference), który można edytować, jeśli chcesz wyświetlić różne wyniki. 

     ![Analiza dzienników Azure - widok zapytania](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Kolejne kroki

* [Monitorowanie komunikatów B2B](../logic-apps/logic-apps-monitor-b2b-message.md)

