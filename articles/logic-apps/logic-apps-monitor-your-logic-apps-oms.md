---
title: Monitorowanie aplikacji logiki jest uruchamiany z usługą Log Analytics — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Uzyskiwanie szczegółowych informacji i debugowanie dane dotyczące uruchomienia aplikacji logiki z usługą Log Analytics do diagnostyki i rozwiązywania problemów
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457145"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitoruj i uzyskuj wgląd w uruchomienia aplikacji logiki z usługą Log Analytics

Do monitorowania oraz dokładniejsze informacje debugowania można włączyć usługi Log Analytics, w tym samym czasie, podczas tworzenia aplikacji logiki. Usługa log Analytics oferuje uruchomienia diagnostyki rejestrowanie i monitorowanie aplikacji logiki w witrynie Azure portal. Po dodaniu rozwiązania do zarządzania aplikacji logiki, otrzymasz zagregowany stan uruchomienia aplikacji logiki i szczegółowe informacje, takie jak stan, czas wykonywania, ponowne przesłanie stanu i identyfikatorów korelacji.

W tym artykule pokazano, jak włączyć usługi Log Analytics, dzięki czemu można wyświetlać zdarzenia środowiska uruchomieniowego i uruchom danych dla aplikacji logiki.

 > [!TIP]
 > Aby monitorować istniejące aplikacje logiki, wykonaj następujące kroki, aby [włączyć rejestrowanie diagnostyczne i wysyłać dane środowiska uruchomieniowego aplikacji logiki do usługi Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Wymagania

Przed rozpoczęciem wykonywania tej procedury, musisz mieć obszar roboczy usługi Log Analytics. Dowiedz się, [jak utworzyć obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Włączanie rejestrowania diagnostycznego, podczas tworzenia aplikacji logiki

1. W [witryny Azure portal](https://portal.azure.com), tworzenie aplikacji logiki. Wybierz **Utwórz zasób** > **integracji** > **aplikacji logiki**.

   ![Tworzenie aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. W obszarze **tworzenie aplikacji logiki**, wykonywać te zadania, jak pokazano:

   1. Podaj nazwę aplikacji logiki, a następnie wybierz swoją subskrypcję platformy Azure. 

   1. Utwórz lub wybierz grupę zasobów platformy Azure.

   1. Ustaw **Log Analytics** do **na**. 

   1. Z poziomu listy listy obszaru roboczego usługi Log Analytics wybierz obszar roboczy, w której chcesz wysyłać dane dla aplikacji logiki jest uruchamiany. 

      ![Tworzenie aplikacji logiki](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Po zakończeniu tego kroku, platforma Azure tworzy aplikację logiki, która jest teraz skojarzone z obszarem roboczym usługi Log Analytics. 
      Ponadto ten krok również automatycznie zainstaluje rozwiązanie Logic Apps Management w obszarze roboczym.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Aby wyświetlić aplikację logiki jest uruchamiany, [wykonaj następujące kroki](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>Zainstalować rozwiązanie do zarządzania aplikacjami logiki

Jeśli pominiesz ten krok jest już włączona usługa Log Analytics, gdy utworzono aplikację logiki. Masz już zainstalowano rozwiązania do zarządzania aplikacji logiki.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wprowadź "log analytics" jako filtr, a następnie wybierz pozycję **usługi Log Analytics**.

   ![Wybierz pozycję "Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. W obszarze **usługi Log Analytics**, Znajdź i wybierz swój obszar roboczy usługi Log Analytics. 

   ![Wybierz swój obszar roboczy usługi Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. W obszarze **konfigurowanie rozwiązania do monitorowania**, wybierz **Wyświetl rozwiązania**.

   ![Wybierz "Wyświetl rozwiązania"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Na stronie Przegląd wybierz **Dodaj**, co spowoduje otwarcie **rozwiązań do zarządzania** listy. Z tej listy, wybierz **Logic Apps Management**. 

   ![Wybierz pozycję "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Jeśli nie możesz znaleźć rozwiązania, w dolnej części listy, wybierz opcję **Załaduj więcej** , aż pojawi się w rozwiązaniu.

1. Wybierz **Utwórz**, który instaluje rozwiązanie.

   ![Wybieranie pozycji "Dodaj" "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>Uruchomienia aplikacji logiki widoku w obszarze roboczym usługi Log Analytics

1. Aby wyświetlić liczbę i stan uruchomienia aplikacji logiki, przejdź do obszaru roboczego usługi Log Analytics, a następnie otwórz stronę przeglądu. 

   Szczegóły dotyczące uruchomienia aplikacji logiki są wyświetlane na **Logic Apps Management** kafelka. Aby wyświetlić podsumowanie z dodatkowymi szczegółami dotyczącymi uruchomienia aplikacji logiki, wybierz **Logic Apps Management** kafelka. 

   ![Kafelek przeglądu przedstawiający stan i liczba przebiegu aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   W tym miejscu uruchomienia aplikacji logiki są grupowane według nazwy lub stanu wykonywania. 
   Ta strona zawiera także szczegółowe informacje o błędach w akcji ani wyzwalaczy do uruchomienia aplikacji logiki.

   ![Podsumowanie stanu dla aplikacji logiki uruchamia](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Aby wyświetlić wszystkie uruchomienia dla stanu lub logikę specyficzną dla aplikacji, wybierz wiersz dla aplikacji logiki lub stanu.

   Oto przykład, który pokazuje wszystkie przebiegi logikę specyficzną dla aplikacji:

   ![Wyświetl uruchomienia aplikacji logiki lub stan](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Ta strona zawiera następujące opcje zaawansowane:

   * **Śledzone właściwości:**

     Ta kolumna zawiera śledzone właściwości, które są pogrupowane według akcji aplikacji logiki. Aby wyświetlić właściwości śledzone, wybierz **widoku**. 
     Aby wyszukać śledzone właściwości, użyj filtru kolumny.
   
     ![Wyświetl śledzonych właściwości dla aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Wszystkie nowo dodane śledzone właściwości może potrwać 10 – 15 minut, zanim pojawią się po raz pierwszy. Dowiedz się, [sposób dodawania śledzonych do aplikacji logiki](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Prześlij ponownie:** można ponownie przesłać co najmniej jeden uruchomienia aplikacji logiki, które się nie powiodły, zakończyło się pomyślnie, lub nadal są uruchomione. Zaznacz pola wyboru dla przebiegów, które chcesz ponownie przesłać, a następnie wybierz **ponownie przesłać**. 

     ![Prześlij ponownie uruchomienia aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Aby filtrować wyniki, można wykonać filtrowania zarówno po stronie klienta, jak i po stronie serwera.

   * **Filtr po stronie klienta**: dla każdej kolumny, należy wybrać filtry, które chcesz, na przykład:

     ![Przykład filtrami kolumn.](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtr po stronie serwera**: wybranie określonego okna czasowego lub ograniczyć liczbę tych, które są wyświetlane, korzystanie z kontroli zakresu w górnej części strony. Domyślnie tylko 1000 rekordów są wyświetlane w danym momencie.
   
     ![Zmiany w danym przedziale czasu](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Aby wyświetlić wszystkie akcje i ich szczegóły dotyczące określonego uruchomienia, wybierz wiersz dla przebiegu aplikacji logiki.

   Oto przykład, który pokazuje wszystkie akcje dla przebiegu aplikacji logiki określonych:

   ![Wyświetlanie akcji dla przebiegu aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. Na każdej stronie wyników, aby wyświetlić zapytanie w odniesieniu do wyników lub aby zobaczyć wszystkie wyniki, wybierz opcję **Zobacz wszystkie**, który spowoduje otwarcie strony wyszukiwanie w dzienniku.
   
   ![Zobacz wszystko na stronach wyników](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   Na stronie przeszukiwania dzienników

   * Aby wyświetlić wyniki zapytania w tabeli, wybierz **tabeli**.

   * Aby zmienić kwerendę, można edytować ciągu zapytania w pasku wyszukiwania. 
   Aby uzyskać lepsze efekty, wybierz **Advanced Analytics**.

     ![Wyświetlanie akcji oraz szczegóły przebiegu aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Na stronie usługi Azure Log Analytics można zaktualizować zapytania i wyświetlić wyniki z tabeli. To zapytanie używa [język zapytania Kusto](https://aka.ms/LogAnalyticsLanguageReference), który można edytować, jeśli chcesz wyświetlić różne wyniki. 

     ![Usługa Azure Log Analytics — widok zapytania](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Kolejne kroki

* [Monitorowanie komunikatów B2B](../logic-apps/logic-apps-monitor-b2b-message.md)