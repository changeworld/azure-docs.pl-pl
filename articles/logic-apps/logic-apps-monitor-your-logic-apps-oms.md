---
title: Monitorowanie aplikacji logiki przy użyciu dzienników usługi Azure Monitor — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Uzyskaj szczegółowe informacje i dane do uruchomienia aplikacji logiki z usługą Azure Log Analytics do diagnozowania i rozwiązywania problemów debugowania
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106214"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>Monitorowanie aplikacji logiki przy użyciu dzienników usługi Azure Monitor

Aby monitorować i uzyskać bardziej rozbudowane debugowania szczegółowe informacje o aplikacji usługi logic apps, włączyć [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md) podczas tworzenia aplikacji logiki. Dzienniki platformy Azure Monitor udostępnia diagnostyki, rejestrowania i monitorowania dla aplikacji logiki po zainstalowaniu rozwiązania do zarządzania aplikacji logiki w witrynie Azure portal. To rozwiązanie zawiera także zagregowane informacje dotyczące aplikacji logiki uruchamia z konkretne szczegółowe informacje, takie jak stan, czas wykonywania, ponowne przesłanie stanu i identyfikatorów korelacji. W tym artykule pokazano, jak włączyć dzienniki usługi Azure Monitor, dzięki czemu można wyświetlać zdarzenia środowiska uruchomieniowego i uruchamia dane dla aplikacji logiki.

Aby włączyć dzienniki usługi Azure Monitor w przypadku istniejących aplikacji logiki, wykonaj następujące kroki, aby [włączyć rejestrowanie diagnostyczne i wysyłać dane środowiska uruchomieniowego aplikacji logiki do dzienników usługi Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Ta strona opisane wcześniej kroki dotyczące sposobu wykonywania tych zadań za pomocą programu Microsoft Operations Management Suite (OMS), który jest [wycofywania w styczniu 2019](../azure-monitor/platform/oms-portal-transition.md), zamiast tego zastępuje te kroki z usługą Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy obszar roboczy usługi Log Analytics. Dowiedz się, [jak utworzyć obszar roboczy usługi Log Analytics](../azure-monitor/learn/quick-create-workspace.md). 

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

Jeśli pominiesz ten krok jest już włączona dzienniki usługi Azure Monitor, gdy utworzono aplikację logiki. Masz już zainstalowano rozwiązania do zarządzania aplikacji logiki.

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

   * **Prześlij ponownie:** Można ponownie przesłać co najmniej jeden przebieg aplikacji logiki, których nie powiodła się, zakończyło się pomyślnie, lub nadal są uruchomione. Zaznacz pola wyboru dla przebiegów, które chcesz ponownie przesłać, a następnie wybierz **ponownie przesłać**. 

     ![Prześlij ponownie uruchomienia aplikacji logiki](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Aby filtrować wyniki, można wykonać filtrowania zarówno po stronie klienta, jak i po stronie serwera.

   * **Filtr po stronie klienta**: Dla każdej kolumny należy wybrać filtry, które chcesz, na przykład:

     ![Przykład filtrami kolumn.](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Filtr po stronie serwera**: Aby wybrać określonego okna czasowego lub ograniczyć liczbę tych, które są wyświetlane, formant zakresu w górnej części strony. Domyślnie tylko 1000 rekordów są wyświetlane w danym momencie.
   
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
     
     Na stronie log analytics można zaktualizować zapytania i wyświetlić wyniki z tabeli. To zapytanie używa [język zapytania Kusto](https://aka.ms/LogAnalyticsLanguageReference), który można edytować, jeśli chcesz wyświetlić różne wyniki. 

     ![log analytics — widok zapytania](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>Kolejne kroki

* [Monitorowanie komunikatów B2B](../logic-apps/logic-apps-monitor-b2b-message.md)