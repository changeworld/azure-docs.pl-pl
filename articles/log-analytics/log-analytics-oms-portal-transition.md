---
title: Portal pakietu OMS na platformę Azure | Dokumentacja firmy Microsoft
description: Portal pakietu OMS jest sunsetted z wszystkimi funkcjami, przejście do witryny Azure portal. Ten artykuł zawiera szczegółowe informacje dotyczące tego przejścia.
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
ms.date: 08/27/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 2e14112220eeea1df094ff0c5844429fa3891a9b
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612809"
---
# <a name="oms-portal-moving-to-azure"></a>Portal pakietu OMS na platformę Azure

> [!NOTE]
> Ten artykuł ma zastosowanie do chmury publicznej platformy Azure i w chmurze dla instytucji rządowych, chyba że zaznaczono inaczej.

Witryna Azure portal jest Centrum dla wszystkich usług platformy Azure i oferuje zaawansowane zarządzanie doświadczenie z możliwości, takich jak pulpity nawigacyjne dla funkcji przypinania zasobów, inteligentne wyszukiwanie, znajdowanie zasobów, tagowania i zarządzanie zasobami. Konsolidacja i usprawnić przepływ pracy zarządzania i monitorowania, czasu zaczęliśmy dodawać możliwości portalu pakietu OMS w witrynie Azure portal. Wszystkie funkcje portalu pakietu OMS są teraz częścią witryny Azure portal. W rzeczywistości niektóre z nowych funkcji, takich jak analiza ruchu są dostępne tylko w witrynie Azure portal. Będzie można wykonać wszystko, co robisz w portalu pakietu OMS przy użyciu witryny Azure portal i nie tylko. Jeśli jeszcze tego nie zrobiono, należy rozpocząć w witrynie Azure portal już dziś!

**Portal pakietu OMS zostanie oficjalnie wycofana 15 stycznia 2019 r.** Cieszymy się przenieść do witryny Azure portal i oczekują przejścia, które można łatwo. Ale wiemy, zmiany są trudne i może być szkodliwe. Wyślij wszelkie pytania opinii i zagadnień związanych z **LAUpgradeFeedback@microsoft.com**. W pozostałej części tego artykułu przechodzi przez kluczowych scenariuszy i harmonogram działania dla tego przejścia.

## <a name="what-is-changing"></a>Co ulega zmianie? 
Następujące zmiany są anonsowanych z amortyzacja portalu pakietu OMS. Każdy z tych zmian jest opisany bardziej szczegółowo w poniższych sekcjach.

- Można tworzyć nowe [tylko obszary robocze](#new-workspaces) w witrynie Azure portal.
- Nowe środowisko zarządzania alertami [zastępuje rozwiązania do zarządzania Alert](#changes-to-alerts).
- [Zarządzanie dostępem użytkowników](#user-access-and-role-migration) odbywa się teraz w witrynie Azure portal, za pomocą kontroli dostępu opartej na rolach na platformie Azure.
- [Łącznik usługi Application Insights nie jest już wymagany](#application-insights-connector-and-solution) ponieważ taką samą funkcjonalność jest włączana za pomocą obszaru roboczego dla wielu kwerendach.
- [Aplikacji OMS Mobile](#oms-mobile-app) jest on przestarzały. 
- [Rozwiązania sieciowa grupa zabezpieczeń jest zastępowany](#azure-network-security-group-analytics) z rozszerzoną funkcjonalność dostępna za pośrednictwem rozwiązania do analizy ruchu.
- Nowe połączenia z programu System Center Operations Manager do usługi Log Analytics wymaga [zaktualizowane pakiety administracyjne](#system-center-operations-manager).
- Zobacz [Zmigruj swoje wdrożenia aktualizacji pakietu OMS na platformę Azure](../automation/migrate-oms-update-deployments.md) Aby uzyskać szczegółowe informacje na temat zmian [rozwiązania Update Management](../automation/automation-update-management.md).


## <a name="what-should-i-do-now"></a>Co należy zrobić teraz?
Chociaż większość funkcji będą nadal działać bez żadnych migracji, należy wykonać następujące zadania:

- Musisz [migrować swoje uprawnienia użytkownika](#user-access-and-role-migration) do witryny Azure portal.
- Zobacz [Zmigruj swoje wdrożenia aktualizacji pakietu OMS na platformę Azure](../automation/migrate-oms-update-deployments.md) szczegółowe informacje na temat przejścia na rozwiązanie do zarządzania aktualizacjami.

Zapoznaj się [często zadawane pytania dotyczące przejścia z portalu pakietu OMS do witryny Azure portal dla użytkowników usługi Log Analytics](../log-analytics/log-analytics-oms-portal-faq.md) informacje na temat, do którego nastąpi przejście do witryny Azure portal. Wyślij wszelkie opinii i pytań ani do zagadnień związanych z **LAUpgradeFeedback@microsoft.com**.

## <a name="user-access-and-role-migration"></a>Migracja dostępu i roli użytkownika
Zarządzanie dostępem portalu platformy Azure jest bogatsze i bardziej efektywne niż zarządzanie dostępem w portalu pakietu OMS. Zobacz [możesz zarządzać obszarami roboczymi](log-analytics-manage-access.md#manage-accounts-and-users) informacje na temat zarządzania dostępem w usłudze Log Analytics.

> [!NOTE]
> Poprzednie wersje w tym artykule stwierdził, że uprawnienia automatycznie skonwertowana z portalu pakietu OMS do witryny Azure portal. Ta automatyczna konwersja nie jest już jest planowane i należy dokonać konwersji samodzielnie.

Już masz odpowiedni dostęp w witrynie Azure portal w tym przypadku nie trzeba wprowadzać żadnych zmian. Istnieje kilka przypadków, gdy nie masz odpowiedni dostęp w takim przypadku administrator musi przydzielić uprawnienia.

- Masz uprawnienia tylko do odczytu użytkownika w portalu pakietu OMS, ale żadne uprawnienia w witrynie Azure portal. 
- Masz uprawnienia współautora w portalu pakietu OMS, ale tylko czytnika dostępu w witrynie Azure portal.
 
W obu przypadkach administrator musi ręcznie przypisać możesz odpowiednią rolę z poniższej tabeli. Firma Microsoft zaleca, aby przypisać tę rolę na poziomie zasobów grupy lub subskrypcji.  Bardziej szczegółowe wskazówki, zapewnia się wkrótce dla obu tych przypadkach.

| Uprawnienia w portalu pakietu OMS | Rolę na platformie Azure |
|:---|:---|
| Tylko do odczytu | Czytelnik usługi Log Analytics |
| Współautor | Współautor usługi Log Analytics |
| Administrator | Właściciel | 
 

## <a name="new-workspaces"></a>Nowych obszarów roboczych
Są już nie można utworzyć nowych obszarów roboczych przy użyciu portalu pakietu OMS. Postępuj zgodnie ze wskazówkami w [Utwórz obszar roboczy usługi Log Analytics w witrynie Azure portal](log-analytics-quick-create-workspace.md) Aby utworzyć nowy obszar roboczy w witrynie Azure portal.

## <a name="changes-to-alerts"></a>Zmiany do alertów

### <a name="alert-extension"></a>Rozszerzenie alertu  

> [!NOTE]
> Alerty mają została w pełni rozszerzona do witryny Azure portal w przypadku chmury publicznej. Istniejących reguł alertów mogą być wyświetlane w portalu pakietu OMS, ale mogą być zarządzane tylko w witrynie Azure portal. Rozszerzenie alertów do witryny Azure portal rozpocznie się w chmurze Azure dla instytucji rządowych w października 2018 r.

Alerty zostały [rozszerzone do postaci witryny Azure portal](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Po zakończeniu tej operacji akcje z zakresu zarządzania alerty tylko będą dostępne w witrynie Azure portal. Istniejące alerty będą nadal wyświetlane w portalu pakietu OMS. Jeśli uzyskujesz dostęp do alertów programowo przy użyciu interfejsu API REST alertów Log Analytics lub szablon Log Analytics alertu zasobu, należy korzystanie z grup akcji zamiast akcji w wywołania interfejsu API, szablony usługi Azure Resource Manager i poleceń programu PowerShell.

### <a name="alert-management-solution"></a>Rozwiązanie do zarządzania alertami
Zamiast [rozwiązanie usługi alert management](log-analytics-solution-alert-management.md), możesz użyć [usługi Azure Monitor ujednoliconego interfejsu alertów](../monitoring-and-diagnostics/monitoring-overview-alerts.md) wizualizacji i zarządzać alertami. Nowe środowisko agreguje alertów z wielu źródeł w ramach alertów platformy Azure łącznie dziennika z usługą Log Analytics. Możesz Zobacz dystrybucje alertów, skorzystaj z zalet automatyczne grupowanie powiązanych alertów za pośrednictwem inteligentnego grup i wyświetlić alerty w ramach wielu subskrypcji podczas stosowania filtrów zaawansowanych. Wszystkie te funkcje są dostępne w wersji zapoznawczej, począwszy od 4 czerwca 2018 r. Rozwiązanie do zarządzania alertów nie będą dostępne w witrynie Azure portal. 

Dane zebrane przez rozwiązanie do zarządzania alertami (rekordy z typem alertu) w dalszym ciągu można w usłudze Log Analytics, tak długo, jak rozwiązanie jest zainstalowane dla obszaru roboczego. Od sierpnia 2018 r. przesyłania strumieniowego alertów z ujednoliconego generowania alertów w obszary robocze zostaną włączone, zastępując tę możliwość. Niektóre zmiany schematu są oczekiwane i zostanie ogłoszona w późniejszym terminie.

## <a name="oms-mobile-app"></a>Aplikację OMS Mobile
Aplikację OMS mobile będzie sunsetted wraz z portalu pakietu OMS. Zamiast aplikację OMS mobile dostęp do informacji o infrastrukturę IT, pulpity nawigacyjne i zapisane zapytania dostępne witryny Azure portal bezpośrednio z przeglądarki w urządzeniu przenośnym. Aby otrzymywać alerty, należy skonfigurować [grup akcji platformy Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) do odbierania powiadomień w postaci wiadomości SMS lub połączenie głosowe

## <a name="application-insights-connector-and-solution"></a>Łącznik usługi Application Insights i rozwiązania
[Łącznik usługi Application Insights](log-analytics-app-insights-connector.md) umożliwia przenoszenie danych usługi Application Insights do obszaru roboczego usługi Log Analytics. Ta duplikacja danych był wymagany do włączenia widoczności danych infrastruktury i aplikacji.

Dzięki obsłudze [zapytania obejmujące wiele zasobów](log-analytics-cross-workspace-search.md), nie ma już konieczności zduplikowane dane. W efekcie istniejące rozwiązanie usługi Application Insights staną się przestarzałe. Począwszy od października, nie można połączyć nowe zasoby usługi Application Insights do obszarów roboczych usługi Log Analytics. Istniejące linki i pulpity nawigacyjne będzie działać do momentu 15 stycznia 2019 r.


## <a name="azure-network-security-group-analytics"></a>Analiza sieciowej grupy zabezpieczeń platformy Azure
[Rozwiązania do analizy grupy zabezpieczeń sieci platformy Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) zostaną zastąpione przy użyciu niedawno wprowadzona na rynek [analizy ruchu](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) która umożliwia wgląd w aktywność użytkownika i aplikacji w sieciach w chmurze. Analiza ruchu pomaga inspekcji aktywności w sieci organizacji, bezpiecznych aplikacji i danych oraz zoptymalizować wydajność obciążenia pozostają zgodne. 

To rozwiązanie analizuje dzienniki przepływu sieciowej grupy zabezpieczeń i zapewnia wgląd w to następujące czynności.

- Ruch odbywa się za pośrednictwem sieci między platformą Azure i Internet, regionach chmury publicznej, sieci wirtualne i podsieci.
- Protokołów w sieci, bez konieczności ataki penetratorów lub dedykowany przepływ kolekcji urządzeń i aplikacji.
- Najważniejsze nadajniki aplikacje intensywnych konwersacji maszyny Wirtualnej w chmurze, ruchu obszarów nadmiernej aktywności.
- Źródeł i miejsc docelowych ruchu między sieciami wirtualnymi, sieciami relacje między usług biznesowych i aplikacji.
- Security, w tym złośliwym ruchem, porty otwarte dla Internetu, aplikacji lub maszyny wirtualne, próby uzyskania dostępu do Internetu.
- Wykorzystanie pojemności, która pomaga wyeliminować problemy dotyczące aprowizacji lub zapobiec niedostatecznemu wykorzystaniu.

Można nadal zależą od ustawień diagnostyki, aby wysyłać dzienniki sieciowych grup zabezpieczeń do usługi Log Analytics, dlatego istniejące zapisane wyszukiwania, alerty i pulpity nawigacyjne będą nadal działać. Klienci, którzy już zainstalowano rozwiązania mogą w dalszym ciągu używać go do odwołania. Począwszy od września 5, rozwiązanie analizy grupy zabezpieczeń sieci zostaną usunięte z portalu marketplace i udostępnione przez społeczność jako [szablon szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Jeśli masz [podłączone grupy zarządzania programu Operations Manager do usługi Log Analytics](log-analytics-om-agents.md), a następnie go będą nadal działać bez konieczności wprowadzania zmian. Dla nowych połączeń, należy wykonać wskazówki zawarte w [programu Microsoft Operations Manager pakiet administracyjny System Center do konfigurowania pakietu Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [często zadawane pytania dotyczące przejścia z portalu pakietu OMS do witryny Azure portal dla użytkowników usługi Log Analytics](log-analytics-oms-portal-faq.md) wskazówki dotyczące przechodzenia z portalu pakietu OMS do witryny Azure portal.
