---
title: Często zadawane pytania dotyczące przejścia z portalu pakietu OMS do witryny Azure portal dla użytkowników usługi Log Analytics | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące usługi Log Analytics użytkownikom przechodzenie z poziomu portalu pakietu OMS do witryny Azure portal.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.openlocfilehash: 8947d235894ffc021243a64244b4904c5ca7f128
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453184"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Często zadawane pytania dotyczące przejścia z portalu pakietu OMS do witryny Azure portal dla użytkowników usługi Log Analytics
Usługi log Analytics swój własny portal o nazwie portalu pakietu OMS początkowo użyty do zarządzania jej konfiguracji i analizowania zebranych danych.  Wszystkie funkcje z tego portalu został przeniesiony do witryny Azure portal gdzie będą opracowywane.

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące użytkowników, dzięki czemu ten proces przejścia.  Jeśli używasz usługi Log Analytics w portalu pakietu OMS, można znaleźć tutaj odpowiedzi na działanie tego samego zadania w witrynie Azure portal.

## <a name="do-i-need-to-migrate-anything"></a>Należy przeprowadzić migrację nic?
Nie. Brak zmian wprowadzanych do usługi Log Analytics, więc nie ma nic, które muszą zostać zmigrowane. Jedyną czynnością, zmiana jest interfejsem, który umożliwia dostęp do niego. W rzeczywistości można teraz witrynie Azure portal umożliwia dostęp do obszarów roboczych w tym samym, rozwiązania, widoki i dziennikach, których używasz w portalu pakietu OMS już dziś.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Gdzie znaleźć usługi Log Analytics na platformie Azure?
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).  Kliknij przycisk **wszystkich usług**, a następnie na liście zasobów wpisz **usługi Log Analytics**. Wybierz **usługi Log Analytics** , a następnie wybierz obszar roboczy. Zostanie wyświetlona strona podsumowania dla obszaru roboczego.

![Obszar roboczy usługi Log Analytics](media/oms-portal-faq/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Jak zarządzać uprawnieniami?
Jeśli nie masz dostępu do obszaru roboczego usługi Log Analytics w witrynie Azure portal należy skonfigurować uprawnienia za pomocą [dostępu opartej na rolach na platformie Azure](../../role-based-access-control/role-assignments-portal.md). Aby uzyskać więcej informacji na temat zarządzania uprawnień obszaru roboczego, zobacz [możesz zarządzać obszarami roboczymi](manage-access.md#manage-accounts-and-users). Aby uzyskać informacji o zarządzaniu uprawnienia do alertów, zobacz [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń za pomocą usługi Azure Monitor](../../azure-monitor/platform/roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Jak utworzyć nowy obszar roboczy? 
Na liście obszarów roboczych w witrynie Azure portal kliknij **Dodaj** na liście obszarów roboczych.  Aby uzyskać szczegółowe informacje, zobacz [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](../learn/quick-create-workspace.md).

![Strona przeglądu](media/oms-portal-faq/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Gdzie jest Moja strona przeglądu?
Ekranu głównego, w portalu pakietu OMS Wyświetla kafelków dla wszystkich rozwiązań zarządzania zainstalowany w obszarze roboczym i niestandardowe widoki, które zostały utworzone. Tego samego widoku jest dostępna w witrynie Azure portal. W obszarze roboczym wybierz **podsumowanie obszaru roboczego**.

![Strona przeglądu](media/oms-portal-faq/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Jak otworzyć wyszukiwanie w dziennikach i Projektant widoków
Zarówno **wyszukiwanie w dzienniku** i **Projektant widoków** są dostępne na stronie głównej i w menu po lewej stronie obszaru roboczego w witrynie Azure portal w prawo obok do **Przegląd**.

## <a name="where-do-i-find-settings"></a>Gdzie znaleźć ustawienia?
Wiele ustawień w **ustawienia** sekcji w portalu pakietu OMS są dostępne w **Zaawansowane ustawienia** menu w witrynie Azure portal w obszarze roboczym.

![Ustawienia zaawansowane](media/oms-portal-faq/advanced-settings.png)

Poniższe sekcje zawierają pełną listę, jak można pobrać ustawień, które były wcześniej dostępne w **ustawienia** sekcji w portalu pakietu OMS.

### <a name="accounts"></a>Konta 
Ustawienia kont odbywa się w różnych miejscach w witrynie Azure portal, zgodnie z opisem w poniższej tabeli.

| Ustawienie w portalu pakietu OMS | Odpowiednik w witrynie Azure portal |
|:---|:---|
| Konto usługi Automation | **Konto usługi Automation** menu dla obszaru roboczego. |
| Subskrypcja platformy Azure i Plan taryfowy | **Warstwa cenowa** menu dla obszaru roboczego. |
| Zarządzanie użytkownikami | Korzystanie z platformy Azure dostępu opartej na rolach do [Zarządzaj uprawnieniami dla obszaru roboczego](#how-do-i-manage-permissions). |
| Informacje o obszarze roboczym | Informacje dostępne w **obszaru roboczego pakietu OMS** menu dla obszaru roboczego. |

### <a name="alerts"></a>Alerty
Reguły alertów na podstawie zapytań usługi Log Analytics zarządza się teraz w [ujednolicone środowisko alertów](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Grupy komputerów
Zarządzanie grupami komputerów w **Zaawansowane ustawienia** menu dla obszaru roboczego. 

### <a name="connected-sources"></a>Połączone źródła
Zarządzanie większość ustawień podłączone źródło w **Zaawansowane ustawienia** menu dla obszaru roboczego. Poniższa tabela zawiera szczegółowe informacje dla każdej sekcji tego menu.

| Ustawienie w portalu pakietu OMS | Odpowiednik w witrynie Azure portal |
|:---|:---|
| Serwery z systemem Windows   | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Linux   Servers   | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Azure Storage     | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| System Center     | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Office 365        | Zobacz [dokumentację dotyczącą rozwiązania do zarządzania usługi Office 365](../insights/solution-office-365.md) szczegółowe informacje dotyczące konfiguracji. |
| Telemetria systemu Windows | Menu Ustawienia dla rozwiązania. Zobacz [Windows Analytics w witrynie Azure portal](/windows/deployment/update/windows-analytics-azure-portal) szczegółowe informacje dotyczące konfiguracji. |
| Łącznik ITSM    | Zobacz [połączenia narzędzia ITSM produktów/usług za pomocą łącznika zarządzania usługami IT](itsmc-connections.md) instrukcje dotyczące łączenia usługi ITSM za pomocą usługi Log Analytics. |

### <a name="data"></a>Dane
Większość ustawień danych zarządzania w **Zaawansowane ustawienia** menu dla obszaru roboczego. Poniższa tabela zawiera szczegółowe informacje dla każdej sekcji tego menu.

| Ustawienie w portalu pakietu OMS | Odpowiednik w witrynie Azure portal |
|:---|:---|
| Dzienniki zdarzeń systemu Windows           | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Liczniki wydajności Windows | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Liczniki wydajności systemu Linux   | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Dzienniki usług IIS                     | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Pola niestandardowe                | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Dzienniki niestandardowe                  | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Dziennik systemu                       | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Application Insights         | To rozwiązanie jest przestarzała, skoro usługa Log Analytics i usługi Application Insights udostępnianie tego samego silnika danych.  |
| Śledzenie plików systemu Windows        | **Śledzenie zmian** menu w usłudze Azure Automation. Zobacz [śledzenia zmian w środowisku przy użyciu rozwiązania Change Tracking](../../automation/automation-change-tracking.md) Aby uzyskać szczegółowe informacje. |
| Śledzenie rejestru systemu Windows        | **Śledzenie zmian** menu w usłudze Azure Automation. Zobacz [śledzenia zmian w środowisku przy użyciu rozwiązania Change Tracking](../../automation/automation-change-tracking.md) Aby uzyskać szczegółowe informacje. |
| Śledzenie plików w systemie Linux          | **Śledzenie zmian** menu w usłudze Azure Automation. Zobacz [śledzenia zmian w środowisku przy użyciu rozwiązania Change Tracking](../../automation/automation-change-tracking.md) Aby uzyskać szczegółowe informacje. |

### <a name="solutions"></a>Rozwiązania
Zarządzanie rozwiązaniami w **rozwiązania** menu dla obszaru roboczego. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Jak zainstalować i usuwanie rozwiązań do zarządzania?
W portalu pakietu OMS zainstalowaniu rozwiązania do zarządzania z galerii rozwiązań i usunąć je z **ustawienia**. W witrynie Azure portal [instalacji rozwiązania do zarządzania](../insights/solutions.md#install-a-monitoring-solution) w portalu Azure Marketplace. [Usuwanie rozwiązania](../insights/solutions.md#remove-a-monitoring-solution) na liście zainstalowane rozwiązania.

## <a name="how-do-i-create-and-manage-alerts"></a>Jak tworzyć i zarządzać alertami
Reguły alertów na podstawie zapytań usługi Log Analytics zarządza się teraz w [ujednolicone środowisko alertów](../../azure-monitor/platform/alerts-metric.md). Zobacz [sposób rozszerzyć alerty z usługą Log Analytics do usługi Azure Alerts](../../azure-monitor/platform/alerts-extend-tool.md) Aby uzyskać szczegółowe informacje dotyczące konfigurowania i używania alertów w witrynie Azure portal.

## <a name="how-do-i-access-my-dashboards"></a>Jak uzyskać dostęp do moich pulpitów nawigacyjnych?
**Mój pulpit nawigacyjny** funkcji w usłudze Log Analytics jest przestarzała. Ta funkcja pozwala na zapewnienie prywatnej kolekcji części projektanta widoków i zostało zastąpione przez wbudowane funkcje pulpitu nawigacyjnego platformy Azure. Wizualizuj dane przy użyciu usługi Log Analytics można kontynuować [Projektant widoków](view-designer.md) udostępnionego widoków. Możesz również przypiąć wizualizacje z tych widoków lub [pojedynczych zapytań](../learn/tutorial-logs-dashboards.md) do pulpitów nawigacyjnych platformy Azure.

## <a name="how-do-i-check-my-usage"></a>Jak sprawdzić użycia?
Można teraz łatwo wyświetlać i zarządzać użycia i kosztów usługi Log Analytics, wybierając **użycie i szacunkowe koszty** w obszarze roboczym.

![Użycie i szacunkowe koszty](media/oms-portal-faq/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Można nadal korzystać z klasycznego portalu?
Przez ograniczony czas możesz uzyskiwać dostęp portalu za pośrednictwem tego adresu URL na własną nazwę obszaru roboczego: https://\<nazwy swojego obszaru roboczego\>. portal.mms.microsoft.com. Firma Microsoft zaleca się jednak przy użyciu witryny Azure portal i przekazywania nam opinii na LAUpgradeFeedback@microsoft.com na wszelkie problemy z blokowaniem.

## <a name="next-steps"></a>Kolejne kroki

- [Znalezienie i zainstalowanie rozwiązania do zarządzania](../insights/solutions.md) przy użyciu witryny Azure portal.
- Dowiedz się więcej o [przeszukiwania dzienników w witrynie Azure portal](../log-query/portals.md).
