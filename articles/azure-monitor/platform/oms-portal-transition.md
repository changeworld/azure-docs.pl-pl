---
title: Portal OMS przenoszący się na platformę Azure | Dokumenty firmy Microsoft
description: Portal OMS jest zachód słońca z wszystkich funkcji przenoszenia do witryny Azure portal. Ten artykuł zawiera szczegółowe informacje na temat tego przejścia.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: 680cd9b44cc447f9bdea38cb9d04fc661fba9c79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659258"
---
# <a name="oms-portal-moving-to-azure"></a>Portal OMS przenoszący się na platformę Azure

> [!NOTE]
> Ten artykuł dotyczy zarówno chmury publicznej platformy Azure, jak i chmury rządowej, z wyjątkiem sytuacji, gdy zaznaczono inaczej.

**Portal OMS dla chmury publicznej platformy Azure został oficjalnie wycofany. Portal OMS dla chmury azure us government został oficjalnie wycofany 15 maja 2019 r.** Cieszymy się, że możemy przejść do witryny Azure portal i oczekujemy, że przejście będzie łatwe. Rozumiemy jednak, że zmiany są trudne i mogą być destrukcyjne. Poniżej dalsza część artykułu Dotyczy kluczowych scenariuszy i planu przejścia.

Portal Azure jest centrum dla wszystkich usług platformy Azure i oferuje bogate środowisko zarządzania z możliwości, takich jak pulpity nawigacyjne do przypinania zasobów, inteligentne wyszukiwanie zasobów i tagowanie do zarządzania zasobami. Aby skonsolidować i usprawnić przepływ pracy monitorowania i zarządzania, rozpoczęliśmy dodawanie funkcji portalu OMS do witryny Azure portal. Wszystkie funkcje portalu OMS są teraz częścią witryny Azure portal. W rzeczywistości niektóre z nowych funkcji, takich jak usługa Traffic Analytics, są dostępne tylko w witrynie Azure portal. Będziesz mógł osiągnąć wszystko, co robisz w portalu OMS za pomocą witryny Azure portal i nie tylko. Jeśli jeszcze tego nie zrobiono, należy rozpocząć korzystanie z witryny Azure portal już dziś!

## <a name="what-is-changing"></a>Co się zmienia? 
Następujące zmiany są ogłaszane wraz z wycofaniem portalu OMS. Każda z tych zmian jest opisana bardziej szczegółowo w poniższych sekcjach.

- Nowe obszary [robocze](#new-workspaces) można tworzyć tylko w witrynie Azure portal.
- Nowe środowisko zarządzania [alertami zastępuje rozwiązanie do zarządzania alertami](#changes-to-alerts).
- [Zarządzanie dostępem użytkowników](#user-access-and-role-migration) jest teraz wykonywane w witrynie Azure portal przy użyciu kontroli dostępu opartej na rolach platformy Azure.
- [Łącznik usługi Application Insights nie jest już wymagany,](#application-insights-connector-and-solution) ponieważ ta sama funkcja jest włączona za pośrednictwem zapytań między obszarami roboczymi.
- [Aplikacja mobilna OMS](#oms-mobile-app) jest przestarzała. 
- [Rozwiązanie nsg jest zastępowane](#azure-network-security-group-analytics) rozszerzonymi funkcjami dostępnymi za pośrednictwem rozwiązania Traffic Analytics.
- Nowe połączenia z programu System Center Operations Manager do usługi Log Analytics wymagają [zaktualizowanych pakietów administracyjnych](#system-center-operations-manager).
- Aby uzyskać szczegółowe informacje na temat zmian w [zarządzania](../../automation/automation-update-management.md) [aktualizacjami, zobacz Migrowanie wdrożeń aktualizacji pakietu OMS na platformę Azure.](../../automation/migrate-oms-update-deployments.md)


## <a name="what-should-i-do-now"></a>Co powinienem teraz zrobić?
Podczas gdy większość funkcji będzie nadal działać bez przeprowadzania migracji, należy wykonać następujące zadania:

- Należy [przeprowadzić migrację uprawnień użytkownika](#user-access-and-role-migration) do witryny Azure portal.
- Zobacz [Migrowanie wdrożeń aktualizacji pakietu OMS na platformę Azure, aby](../../automation/migrate-oms-update-deployments.md) uzyskać szczegółowe informacje na temat przenoszenia rozwiązania do zarządzania aktualizacjami.

Zapoznaj się [z typowymi pytaniami dotyczącymi przejścia z portalu OMS do portalu Azure dla użytkowników usługi Log Analytics,](oms-portal-faq.md) aby uzyskać informacje na temat przejścia do witryny Azure portal. 

## <a name="user-access-and-role-migration"></a>Dostęp użytkowników i migracja ról
Zarządzanie dostępem do portalu Azure jest bogatsze i bardziej zaawansowane niż zarządzanie dostępem w portalu OMS. Aby uzyskać szczegółowe informacje na temat zarządzania dostępem w usłudze Log Analytics, zobacz [Projektowanie obszaru roboczego Dzienniki monitora platformy Azure.](design-logs-deployment.md)

> [!NOTE]
> Poprzednie wersje tego artykułu stwierdził, że uprawnienia zostaną automatycznie przekonwertowane z portalu OMS do witryny Azure portal. Ta konwersja automatyczna nie jest już planowana i musisz wykonać konwersję samodzielnie.

Możesz mieć już odpowiedni dostęp w witrynie Azure portal, w którym to przypadku nie trzeba wprowadzać żadnych zmian. Istnieje kilka przypadków, w których użytkownik może nie mieć odpowiedniego dostępu, w którym to przypadku administrator musi przypisać uprawnienia.

- Masz uprawnienia ReadOnly User w portalu OMS, ale nie ma uprawnień w witrynie Azure portal. 
- Masz uprawnienia współautora w portalu OMS, ale tylko dostęp czytelnika w witrynie Azure portal.
 
W obu tych przypadkach administrator musi ręcznie przypisać odpowiednią rolę z poniższej tabeli. Zaleca się przypisanie tej roli na poziomie grupy zasobów lub subskrypcji.  Wkrótce zostaną przedstawione bardziej nakazowe wskazówki dla obu tych przypadków.

| Uprawnienie portalu OMS | Rola platformy Azure |
|:---|:---|
| ReadOnly | Czytelnik usługi Log Analytics |
| Współautor | Współautor usługi Log Analytics |
| Administrator | Właściciel | 
 

## <a name="new-workspaces"></a>Nowe obszary robocze
Za pomocą portalu OMS nie można już tworzyć nowych obszarów roboczych. Postępuj zgodnie ze wskazówkami w [tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure portal,](../learn/quick-create-workspace.md) aby utworzyć nowy obszar roboczy w witrynie Azure portal.

## <a name="changes-to-alerts"></a>Zmiany w alertach

### <a name="alert-extension"></a>Rozszerzenie alertu  

Alerty zostały [rozszerzone do witryny Azure portal](alerts-extend.md) Istniejące alerty będą nadal wyświetlane w portalu OMS, ale można nimi zarządzać tylko w witrynie Azure portal. Jeśli dostęp do alertów programowo przy użyciu interfejsu API REST alert usługi Log Analytics lub szablonu zasobu alertu usługi Log Analytics należy użyć grup akcji zamiast akcji w wywołaniach interfejsu API, szablonów usługi Azure Resource Manager i poleceń programu PowerShell.

### <a name="alert-management-solution"></a>Rozwiązanie do zarządzania alertami
Jako zmiana w stosunku do poprzedniego ogłoszenia rozwiązanie do [zarządzania alertami](alert-management-solution.md) będzie nadal dostępne i w pełni obsługiwane w witrynie Azure portal. Możesz kontynuować instalowanie rozwiązania z portalu Azure Marketplace.

Gdy rozwiązanie do zarządzania alertami jest nadal dostępne, zachęcamy do korzystania z [ujednoliconego interfejsu alertów usługi Azure Monitor](alerts-overview.md) do wizualizacji wszystkich alertów na platformie Azure i zarządzania nimi. To nowe środowisko natywnie agreguje alerty z wielu źródeł na platformie Azure, w tym alerty dziennika z usługi Log Analytics. Jeśli używasz ujednoliconego interfejsu alertów usługi Azure Monitor, rozwiązanie do zarządzania alertami jest wymagane tylko do włączenia integracji alertów z programu System Center Operation Manager do platformy Azure. W ujednoliconym interfejsie alertów usługi Azure Monitor możesz wyświetlać dystrybucje alertów, korzystać z automatycznego grupowania powiązanych alertów za pośrednictwem grup inteligentnych i wyświetlać alerty w wielu subskrypcjach podczas stosowania filtrów rozszerzonych. Przyszłe postępy w zarządzaniu alertami będą dostępne przede wszystkim dzięki temu nowemu doświadczeniu. 

Dane zbierane przez rozwiązanie do zarządzania alertami (rekordy z typem alertu) nadal są w usłudze Log Analytics tak długo, jak długo jest zainstalowane rozwiązanie dla obszaru roboczego. 

## <a name="oms-mobile-app"></a>Aplikacja mobilna OMS
Aplikacja mobilna OMS zostanie zasysona wraz z portalem OMS. Zamiast aplikacji mobilnej OMS, aby uzyskać dostęp do informacji o infrastrukturze IT, pulpitach nawigacyjnych i zapisanych zapytaniach, możesz uzyskać dostęp do witryny Azure portal bezpośrednio z przeglądarki na urządzeniu przenośnym. Aby otrzymywać alerty, należy skonfigurować [grupy akcji platformy Azure](action-groups.md) do odbierania powiadomień w formie wiadomości SMS lub połączenia głosowego

## <a name="application-insights-connector-and-solution"></a>Łącznik i rozwiązanie usługi Application Insights
[Łącznik usługi Application Insights zapewnia](app-insights-connector.md) możliwość uwzględnienia danych usługi Application Insights w obszarze roboczym usługi Log Analytics. To duplikowanie danych było wymagane, aby umożliwić wgląd w infrastrukturę i dane aplikacji. Dzięki rozszerzonej obsłudze przechowywania danych usługi Application Insights w marcu 2019 r. i możliwości wykonywania [zapytań między zasobami](../log-query/cross-workspace-query.md) oprócz możliwości [wyświetlania wielu zasobów usługi Azure Monitor Application Insights](../log-query/unify-app-resource-data.md)nie ma potrzeby duplikowania danych z zasobów usługi Application Insights i wysyłania ich do usługi Log Analytics. Ponadto łącznik wysyła podzbiór właściwości aplikacji do usługi Log Analytics, podczas gdy kwerendy między zasobami zapewnia większą elastyczność.  

W związku z tym łącznik usługi Application Insights został przestarzały i usunięty z portalu Azure Marketplace wraz z wycofaniem portalu OMS w dniu 30 marca 2019 r. Istniejące połączenia będą działać do 30 czerwca 2019 r. Po usunięciu portalu usługi OMS nie można skonfigurować i usunąć istniejących połączeń z portalu. Będzie to obsługiwane przy użyciu interfejsu API REST, który zostanie udostępniony w styczniu 2019 r., a powiadomienie zostanie opublikowane w [aktualizacjach platformy Azure.](https://azure.microsoft.com/updates/) 

## <a name="azure-network-security-group-analytics"></a>Analiza grupy zabezpieczeń sieci platformy Azure
[Rozwiązanie usługi Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) zostanie zastąpione niedawno uruchomiona usługa Traffic [Analytics,](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) która zapewnia wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Usługa Traffic Analytics pomaga kontrolować aktywność sieciową organizacji, zabezpieczać aplikacje i dane, optymalizować wydajność obciążenia i zachować zgodność. 

To rozwiązanie analizuje dzienniki przepływu nsg i zapewnia wgląd w następujące.

- Ruch przepływa przez sieci między platformą Azure i Internetem, regionami chmury publicznej, sieciami wirtualnymi i podsieciami.
- Aplikacje i protokoły w sieci, bez konieczności wąchania lub dedykowanych urządzeń do zbierania przepływu.
- Top talkers, chatty aplikacji, rozmowy maszyn wirtualnych w chmurze, hotspotów ruchu.
- Źródła i miejsca docelowe ruchu w sieciach wirtualnych, wzajemnych relacjach między krytycznymi usługami biznesowymi a aplikacjami.
- Zabezpieczenia, w tym złośliwy ruch, porty otwarte dla Internetu, aplikacje lub maszyny wirtualne próbujące uzyskać dostęp do Internetu.
- Wykorzystanie pojemności, co pomaga wyeliminować problemy nadmiernej inicjowania obsługi administracyjnej lub niepełnego wykorzystania.

Możesz nadal polegać na ustawieniach diagnostyki, aby wysyłać dzienniki sieciowej sieciowej sieciowej do usługi Log Analytics, aby istniejące zapisane wyszukiwania, alerty, pulpity nawigacyjne nadal działały. Klienci, którzy już zainstalowali rozwiązanie, mogą nadal z niego korzystać do odwołania. Od 5 września rozwiązanie Network Security Group Analytics zostanie usunięte z rynku i udostępnione za pośrednictwem społeczności jako [szablon Szybki start platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights)

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Jeśli grupa [zarządzania programu Operations Manager została połączona z usługą Log Analytics,](om-agents.md)będzie ona nadal działać bez żadnych zmian. W przypadku nowych połączeń należy jednak postępować zgodnie ze wskazówkami zawartymi w [pakiecie administracyjnym programu Microsoft System Center Operations Manager, aby skonfigurować pakiet Operations Management Suite](https://blogs.technet.microsoft.com/momteam/2018/07/25/microsoft-system-center-operations-manager-management-pack-to-configure-operations-management-suite/).

## <a name="next-steps"></a>Następne kroki
- Zobacz [typowe pytania dotyczące przejścia z portalu OMS do witryny Azure portal dla użytkowników usługi Log Analytics, aby](oms-portal-faq.md) uzyskać wskazówki dotyczące przechodzenia z portalu OMS do witryny Azure portal.
