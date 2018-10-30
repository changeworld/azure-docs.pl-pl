---
title: Monitorowanie aplikacji logic apps z usługą Log Analytics — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Uzyskaj szczegółowe informacje i dane do uruchomienia aplikacji logiki z usługą Azure Log Analytics do diagnozowania i rozwiązywania problemów debugowania
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: c65cc24f9b0083e9c873465008490bf00ea83852
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232380"
---
# <a name="monitor-logic-apps-with-azure-log-analytics"></a>Monitorowanie aplikacji logic apps z usługą Azure Log Analytics

Aby monitorować i uzyskać bardziej rozbudowane debugowania szczegółowe informacje o aplikacji usługi logic apps, włączyć [usługi Azure Log Analytics](../log-analytics/log-analytics-overview.md) podczas tworzenia aplikacji logiki. Usługa log Analytics udostępnia diagnostyki, rejestrowania i monitorowania dla aplikacji logiki po zainstalowaniu rozwiązania do zarządzania aplikacji logiki w witrynie Azure portal. To rozwiązanie zawiera także zagregowane informacje dotyczące aplikacji logiki uruchamia z konkretne szczegółowe informacje, takie jak stan, czas wykonywania, ponowne przesłanie stanu i identyfikatorów korelacji. W tym artykule pokazano, jak włączyć usługi Log Analytics, dzięki czemu można wyświetlać zdarzenia środowiska uruchomieniowego i uruchamia dane dla aplikacji logiki.

Aby włączyć usługi Azure Log Analytics dla istniejących aplikacji logiki, wykonaj następujące kroki, aby [włączyć rejestrowanie diagnostyczne i wysyłać dane środowiska uruchomieniowego aplikacji logiki do usługi Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Ta strona opisane wcześniej kroki dotyczące sposobu wykonywania tych zadań za pomocą programu Microsoft Operations Management Suite (OMS), który jest [wycofywania w styczniu 2019](../log-analytics/log-analytics-oms-portal-transition.md), zamiast tego zastępuje te kroki z usługą Azure Log Analytics. 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy obszar roboczy usługi Log Analytics. Dowiedz się, [jak utworzyć obszar roboczy usługi Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). 

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

## <a name="install-logic-apps-management-solution"></a>Zainstalować rozwiązanie do zarządzania aplikacjami logiki

Jeśli pominiesz ten krok jest już włączona usługa Log Analytics, gdy utworzono aplikację logiki. Masz już zainstalowano rozwiązania do zarządzania aplikacji logiki.

1. W witrynie [Azure Portal](https://portal.azure.com) wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania Znajdź "log analytics", a następnie wybierz **usługi Log Analytics**.

   ![Wybierz pozycję "Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. W obszarze **usługi Log Analytics**, Znajdź i wybierz swój obszar roboczy usługi Log Analytics. 

   ![Wybierz swój obszar roboczy usługi Log Analytics](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. W obszarze **Rozpoczynanie pracy z usługą Log Analytics** > **konfigurowanie rozwiązania do monitorowania**, wybierz **Wyświetl rozwiązania**.

   ![Wybierz "Wyświetl rozwiązania"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Na stronie Przegląd wybierz **Dodaj**, co spowoduje otwarcie **rozwiązań do zarządzania** listy. Z tej listy, wybierz **Logic Apps Management**. 

   ![Wybierz pozycję "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Jeśli nie możesz znaleźć rozwiązania, w dolnej części listy, wybierz opcję **Załaduj więcej** , aż pojawi się w rozwiązaniu.

1. Wybierz **Utwórz**, upewnij się, którym chcesz zainstalować rozwiązanie, a następnie wybierz obszar roboczy usługi Log Analytics **Utwórz** ponownie.   

   ![Wybierz pozycję "Utwórz" na "Zarządzanie usługą Logic Apps"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Jeśli nie chcesz używać istniejącego obszaru roboczego, można również utworzyć nowy obszar roboczy, w tym momencie.

   Gdy skończysz, rozwiązanie Logic Apps Management pojawia się na stronie Przegląd. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Wyświetl informacje o przebieg aplikacji logiki

Po uruchomieniu aplikacji logiki na można wyświetlić stan i liczba te przebiegi **Logic Apps Management** kafelka. 

1. Przejdź do obszaru roboczego usługi Log Analytics, a następnie otwórz stronę przeglądu. Wybierz **Logic Apps Management**. 

   ![Przebieg stan i liczba aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

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