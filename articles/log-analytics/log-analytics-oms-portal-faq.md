---
title: Często zadawane pytania przejścia z portalu OMS do portalu Azure Log Analytics użytkowników | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dla użytkowników analizy dzienników przejścia z portalu OMS do portalu Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 6479db44681071932ff92ee17ea0aec6518dc74e
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128643"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Często zadawane pytania przejścia z portalu OMS do portalu Azure Log Analytics użytkowników
Analizy dzienników początkowo umożliwia własną portalu o nazwie portalu OMS zarządzanie jej konfiguracją i analizowanie zebranych danych.  Wszystkie funkcje z tego portalu został przeniesiony do portalu Azure której będzie można rozwijać.

Ten artykuł zawiera odpowiedzi na często zadawane pytania dla użytkowników, co ten proces przejścia.  Jeśli używasz Log Analytics w portalu OMS, można znaleźć tutaj odpowiedzi dla sposób wykonania tych samych czynności w portalu Azure.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Gdzie znaleźć analizy dzienników na platformie Azure?
Zaloguj się do witryny Azure Portal na stronie [https://portal.azure.com](https://portal.azure.com).  Kliknij przycisk **wszystkie usługi**, a na liście zasobów wpisz **analizy dzienników**. Wybierz **analizy dzienników** , a następnie wybierz obszar roboczy. Wyświetlana jest strona podsumowania dla obszaru roboczego.

![Obszar roboczy usługi Log Analytics](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Jak zarządzać uprawnienia?
Jeśli nie masz dostępu do swojego obszaru roboczego analizy dzienników w portalu Azure, należy skonfigurować uprawnienia za pomocą [dostępu opartej na rolach na platformie Azure](../active-directory/role-based-access-control-configure.md). Aby uzyskać więcej informacji na temat zarządzania uprawnień obszaru roboczego, zobacz [zarządzanie obszarami roboczymi](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Informacje na temat zarządzania uprawnienia do alertów, zobacz [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń z monitorem Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Jak utworzyć nowy obszar roboczy? 
Na liście obszarów roboczych w portalu Azure kliknij **Dodaj** na liście obszarów roboczych.  Aby uzyskać szczegółowe informacje, zobacz [Tworzenie obszaru roboczego analizy dzienników w portalu Azure](../log-analytics/log-analytics-quick-create-workspace.md).

![Strona przeglądu](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Gdzie jest strony Przegląd?
Ekranie głównym w portalu OMS Wyświetla kafelków dla wszystkich rozwiązań zarządzania zainstalowany w obszarze roboczym i niestandardowe widoki, które zostały utworzone. W tym samym widoku jest dostępna w portalu Azure. Z obszaru roboczego wybierz **podsumowanie obszaru roboczego**.

![Strona przeglądu](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Jak otworzyć dziennika wyszukiwania i Widok projektanta?
Zarówno **wyszukiwania dziennika** i **Widok projektanta** są dostępne na stronie głównej i w menu po lewej stronie obszaru roboczego w portalu Azure po prawej obok **omówienie**.

## <a name="where-do-i-find-settings"></a>Gdzie znaleźć ustawienia?
Wiele ustawień w **ustawienia** części portalu OMS są dostępne w **Zaawansowane ustawienia** menu w portalu Azure w obszarze roboczym.

![Ustawienia zaawansowane](media/log-analytics-new-portal/advanced-settings.png)

Poniższe sekcje zawierają pełną listę, jak uzyskać dostęp do ustawień, które były wcześniej dostępne w **ustawienia** części portalu OMS.

### <a name="accounts"></a>Konta 
Ustawienia konta są zarządzane w różnych miejscach w portalu Azure, zgodnie z opisem w poniższej tabeli.

| Ustawienie w portalu OMS | Odpowiednik w portalu Azure |
|:---|:---|
| Konto usługi Automation | **Konto automatyzacji** menu dla obszaru roboczego. |
| Subskrypcja platformy Azure i Plan danych | **Warstwa cenowa** menu dla obszaru roboczego. |
| Zarządzanie użytkownikami | Korzystanie z platformy Azure opartej na rolach dostępu do [zarządzać uprawnieniami do obszaru roboczego](#how-do-i-manage-permissions). |
| Informacje o obszarze roboczym | Informacje są dostępne na **obszarem roboczym pakietu OMS** menu dla obszaru roboczego. |

### <a name="alerts"></a>Alerty
Reguły alertów na podstawie kwerend analizy dzienników są teraz zarządzane w [unified alerty środowisko](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Grupy komputerów
Zarządzanie grupami komputerów w **Zaawansowane ustawienia** menu dla obszaru roboczego. 

### <a name="connected-sources"></a>Połączone źródła
Zarządzanie ustawieniami połączenia źródła większości w **Zaawansowane ustawienia** menu dla obszaru roboczego. Poniższa tabela zawiera szczegóły dotyczące każdej sekcji tego menu.

| Ustawienie w portalu OMS | Odpowiednik w portalu Azure |
|:---|:---|
| Serwery z systemem Windows   | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Serwery systemu Linux   | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Azure Storage     | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| System Center     | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Office 365        | Zobacz [dokumentacji rozwiązania do zarządzania usługi Office 365](../operations-management-suite/oms-solution-office-365.md) szczegółowe informacje dotyczące konfiguracji. |
| Telemetria systemu Windows | Jeszcze niedostępne w portalu Azure. |
| Zarządzanie usługami IT — łącznika    | Zobacz [Zarządzanie usługami IT — Połącz produktów/usług IT usługi zarządzania łącznik](../log-analytics/log-analytics-itsmc-connections.md) instrukcje dotyczące nawiązywania połączenia z usługą Zarządzanie usługami IT — z analizy dzienników. |

### <a name="data"></a>Dane
Większość ustawień danych zarządzania w **Zaawansowane ustawienia** menu dla obszaru roboczego. Poniższa tabela zawiera szczegóły dotyczące każdej sekcji tego menu.

| Ustawienie w portalu OMS | Odpowiednik w portalu Azure |
|:---|:---|
| Dzienniki zdarzeń systemu Windows           | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Liczniki wydajności systemu Windows | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Liczniki wydajności systemu Linux   | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Dzienniki usług IIS                     | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Pola niestandardowe                | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Dzienniki niestandardowe                  | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Dziennik systemu                       | **Zaawansowane ustawienia** menu dla obszaru roboczego. |
| Application Insights         | To rozwiązanie jest przestarzała teraz, analizy dzienników i usługi Application Insights udostępnianie tego samego silnika danych.  |
| Śledzenie plików systemu Windows        | **Śledzenie zmian** menu w automatyzacji Azure. Zobacz [śledzenia zmian w środowisku z rozwiązaniem do śledzenia zmian](../automation/automation-change-tracking.md) szczegółowe informacje. |
| Śledzenie rejestru systemu Windows        | **Śledzenie zmian** menu w automatyzacji Azure. Zobacz [śledzenia zmian w środowisku z rozwiązaniem do śledzenia zmian](../automation/automation-change-tracking.md) szczegółowe informacje. |
| Śledzenie plików w systemie Linux          | **Śledzenie zmian** menu w automatyzacji Azure. Zobacz [śledzenia zmian w środowisku z rozwiązaniem do śledzenia zmian](../automation/automation-change-tracking.md) szczegółowe informacje. |

### <a name="solutions"></a>Rozwiązania
Zarządzaj rozwiązaniami w **rozwiązań** menu dla obszaru roboczego. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Jak zainstalować i usunąć rozwiązania do zarządzania?
W portalu OMS zainstalować rozwiązania do zarządzania z galerii rozwiązań i usunąć je z **ustawienia**. W portalu Azure [zainstalować rozwiązania do zarządzania](../monitoring/monitoring-solutions.md#install-a-management-solution) z portalu Azure Marketplace. [Usuń rozwiązań](../monitoring/monitoring-solutions.md#remove-a-management-solution) z listy zainstalowanych rozwiązania.

## <a name="how-do-i-create-and-manage-alerts"></a>Jak utworzyć i alertami można zarządzać?
Reguły alertów na podstawie kwerend analizy dzienników są teraz zarządzane w [unified alerty środowisko](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). Zobacz [jak rozszerzyć alerty z analizy dzienników do alertów Azure](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) szczegółowe informacje dotyczące konfigurowania i używania alertów w portalu Azure.

## <a name="how-do-i-access-my-dashboards"></a>Jak uzyskać dostępu do mojego pulpity nawigacyjne
[Pulpity nawigacyjne](../log-analytics/log-analytics-dashboards.md) w Log Analytics została wycofana.  Można wizualizuj dane przy użyciu analizy dzienników [Widok projektanta](../log-analytics/log-analytics-view-designer.md) mającego dodatkowe funkcje i numer pin zapytań i widoków do platformy Azure pulpitów nawigacyjnych.

## <a name="how-do-i-check-my-usage"></a>Jak sprawdzić użycia?
Można teraz łatwo wyświetlać i zarządzać użycia i kosztów analizy dzienników, wybierając **użycia i szacowane koszty** w obszarze roboczym.

![Użycie i szacunkowe koszty](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Można nadal korzystać z portalu klasycznego?
Przez ograniczony czas, możesz nadal dostępu do portalu za pośrednictwem tego adresu URL, z nazwą obszaru roboczego: https://\<nazwa obszaru roboczego\>. portal.mms.microsoft.com. Zaleca się jednak przy użyciu portalu Azure i zapewnić nam opinię w LAUpgradeFeedback@microsoft.com na wszystkie problemy z blokowaniem.

## <a name="next-steps"></a>Kolejne kroki

- [Znajdź i zainstaluj rozwiązania do zarządzania](../monitoring/monitoring-solutions.md) przy użyciu portalu Azure.
- Dowiedz się więcej o [wyszukiwania dziennika w portalu Azure](log-analytics-log-search-portals.md).