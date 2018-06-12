---
title: Przenoszenie na platformę Azure portalu OMS | Dokumentacja firmy Microsoft
description: W portalu OMS są sunsetted ze wszystkich funkcji przechodzenia do portalu Azure. Ten artykuł zawiera szczegółowe informacje na ten proces przejścia.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: bwren
ms.openlocfilehash: 5719dc3719739fb561626e307ee295729752c1fa
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297757"
---
# <a name="oms-portal-moving-to-azure"></a>Przenoszenie na platformę Azure portalu OMS
Dziękujemy za korzystanie z portalu OMS. Firma Microsoft zaleca się przez działem pomocy technicznej i przejdź do silnie inwestycji w naszych usług zarządzania i monitorowania. Jedna część opinię heard wielokrotnie klientów jest konieczność obsługi pojedynczego użytkownika monitorować i zarządzać nimi, zarówno lokalnie, jak i obciążeń Azure. Prawdopodobnie wiesz, portalu Azure Centrum dla wszystkich usług platformy Azure, oferuje rozbudowane Zarządzanie doświadczenia z możliwości, takie jak pulpity nawigacyjne dla funkcji przypinania zasobów, inteligentnego wyszukiwanie zasobów znajdowania i znakowanie dla zarządzania zasobami. Konsolidacja i usprawnić przepływ pracy zarządzania i monitorowania, możemy rozpoczęto Dodawanie możliwości portalu OMS do portalu Azure. Mamy przyjemność poinformować, że większość funkcji portalu OMS są teraz częścią portalu Azure. W rzeczywistości niektóre z nowych funkcji, takich jak usługi Traffic Manager są dostępne tylko w portalu Azure. Istnieje tylko kilka luk pozostałą, najbardziej impactful jest pięć rozwiązań, które są nadal w procesie do przeniesienia do portalu Azure. Jeśli nie używasz tych funkcji, można wykonywać wszystkie obiekty, które wcześniej czynności w portalu OMS z portalu Azure i inne. Jeśli jeszcze tego nie zrobiono, zaleca się uruchamiania przy użyciu portalu Azure już dziś! 

Oczekujemy zamknięcia w pozostałych luki pomiędzy dwoma portalami przez 2018 sierpnia. Na podstawie opinii klientów, firma Microsoft będzie komunikować się osi czasu sunsetting portalu OMS. Możemy radością przejść do portalu Azure i oczekują przejścia, aby były łatwe. Ale Rozumiemy zmiany są trudne i mogą stanowić zakłócenie. Wyślij jakichkolwiek pytań, opinię lub wątpliwości do LAUpgradeFeedback@microsoft.com. Dalszej części tego artykułu przechodzi przez kluczowych scenariuszy, bieżący przerw i Przewodnik ten proces przejścia. 


## <a name="what-will-change"></a>Zmiany? 
Następujące zmiany są anonsowanych z amortyzacja portalu OMS. Każdy z tych zmian jest opisany bardziej szczegółowo w poniższych sekcjach.

- Nowe środowisko zarządzania alertami spowoduje zastąpienie rozwiązania do zarządzania alertu.
- Zarządzanie dostępem użytkowników będzie realizowane w portalu Azure przy użyciu kontroli dostępu opartej na rolach na platformie Azure.
- Łącznik usługi Insights aplikacji nie są już wymagane, ponieważ te same funkcje można włączyć za pomocą obszaru roboczego między zapytania.
- Aplikacja mobilna OMS zostaną wycofane. 
- Rozwiązanie grupa NSG jest zastępowany z rozszerzoną funkcjonalność dostępne za pośrednictwem rozwiązania analizy ruchu.



## <a name="current-known-gaps"></a>Bieżący znanych luk 
Obecnie istnieją luki niektórych funkcji, wymagających nadal korzystać z portalu OMS. Te luki są zamknięte, a ten dokument zostanie odpowiednio aktualizowany. Należy również zapoznać się [aktualizacje Azure](https://azure.microsoft.com/updates/?product=log-analytics) dla bieżącego anonsów o rozszerzenia i zmiany.

- Następujące rozwiązania nie są jeszcze funkcjonalnej w portalu Azure. Można nadal używać tych rozwiązań w klasycznym portalu, dopóki te są aktualizowane.

    - Rozwiązań analitycznych systemu Windows ([gotowości do uaktualnienia](https://technet.microsoft.com/itpro/windows/deploy/upgrade-readiness-get-started), [kondycji urządzenia](https://docs.microsoft.com/windows/deployment/update/device-health-monitor), i [zgodności aktualizacji](https://technet.microsoft.com/itpro/windows/manage/update-compliance-get-started))
    - [Analiza DNS](log-analytics-dns.md) 
    - [Surface Hub](log-analytics-surface-hubs.md)

-  Aby uzyskać dostęp do zasobów analizy dzienników na platformie Azure, użytkownik musi otrzymać dostęp za pośrednictwem [dostępu opartej na rolach na platformie Azure](#user-access-and-role-migration).
- Harmonogramy aktualizacji, które zostały utworzone przy użyciu portalu OMS nie zostaną odzwierciedlone w przypadku wdrożeń zaplanowanych aktualizacji lub zaktualizować historii zadań pulpitu nawigacyjnego zarządzania aktualizacji w portalu Azure. Ta przerwa powinien być kierowane do końca czerwca 2018.
- Funkcja w wersji zapoznawczej dzienników niestandardowych można włączyć tylko za pośrednictwem portalu OMS. Na koniec czerwca 2018 to zostaną automatycznie włączone dla we wszystkich obszarach roboczych.
 
## <a name="what-should-i-do-now"></a>Co należy zrobić teraz?  
Należy zapoznać się [często zadawane pytania przejścia z portalu OMS do portalu Azure Log Analytics użytkowników](../log-analytics/log-analytics-oms-portal-faq.md) informacji o sposobie przejścia do portalu Azure. Jeśli [luki opisane powyżej](#current-known-gaps) nie można stosować do środowiska, a następnie należy rozważyć uruchamianie za pomocą portalu Azure jako podstawowego środowiska. Wyślij jakichkolwiek opinii, pytań lub wątpliwości do LAUpgradeFeedback@microsoft.com.

## <a name="changes-to-alerts"></a>Zmiany do alertów 

### <a name="alert-extension"></a>Rozszerzenia alertu  
Alerty są właśnie trwa [rozszerzony do portalu Azure](../monitoring-and-diagnostics/monitoring-alerts-extend.md). Po zakończeniu tej operacji, akcje zarządzania alerty tylko będą dostępne w portalu Azure. Istniejące alerty będą nadal wyświetlane w portalu OMS. Programowy dostęp alerty za pomocą interfejsu API REST alertu dziennika analizy lub szablon zasobu alertu analizy dziennika należy używać grup akcji zamiast akcje w wywołania interfejsu API, szablonów usługi Azure Resource Manager i poleceń programu PowerShell.

### <a name="alert-management-solution"></a>Rozwiązanie do zarządzania alertu
Zamiast [alertów rozwiązania do zarządzania](log-analytics-solution-alert-management.md), można użyć [alertów interfejsu unified Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-unified-alerts.md) wizualizacji i Zarządzanie alertami. Nowe środowisko agreguje alerty z wielu źródeł w Azure tym alertów dziennika z analizy dzienników. Można Zobacz dystrybucje alertów, korzystać z automatycznych grupowanie powiązanych alertów za pośrednictwem grup inteligentne i wyświetlanie alertów w subskrypcjach wielu podczas stosowania filtrów zaawansowanych. Wszystkie te funkcje są dostępne w wersji zapoznawczej uruchamianie 4 czerwca 2018. Rozwiązanie do zarządzania alertu nie będą dostępne w portalu Azure. 

Dane zebrane przez rozwiązanie do zarządzania alertami (rekordy typu alertu) w dalszym ciągu mieć tak długo, jak rozwiązanie jest zainstalowane dla obszaru roboczego analizy dzienników. Począwszy od sierpnia 2018, przesyłanie strumieniowe alertów z ujednoliconego alertów w obszary robocze zostaną włączone, zastępując tej możliwości. Niektóre zmiany schematu są oczekiwane i będą ogłaszane w późniejszym terminie.

## <a name="user-access-and-role-migration"></a>Migracja dostępu i roli użytkownika
Zarządzanie dostępu do portalu Azure jest bardziej zaawansowane funkcje i bardziej efektywne niż zarządzania dostępem w portalu OMS, ale wymagają niektórych konwersji. Zobacz [zarządzanie obszarami roboczymi](log-analytics-manage-access.md#manage-accounts-and-users) szczegółowe zarządzanie dostępem w analizy dzienników.

Uruchamianie 25 czerwca i kontynuowanie przy użyciu lipca, automatycznej konwersji dostępu kontroli uprawnień z portalu OMS do portalu Azure, rozpocznie się uprawnienia. Po ukończeniu konwersji sekcji portalu OMS użytkownika administracyjnego będzie przekierowywać użytkowników do kontroli dostępu (IAM) na platformie Azure. 

Podczas konwersji system Sprawdź każdemu użytkownikowi lub grupie zabezpieczeń, które ma uprawnienia w portalu OMS i sprawdzić, czy jest on tego samego poziomu lub uprawnienia na platformie Azure. Jeśli brakuje uprawnień przypisze następujące role dla odpowiednich obszarów roboczych i rozwiązania.

| Uprawnienia w portalu OMS | Rola platformy Azure |
|:---|:---|
| Tylko do odczytu | Czytelnik usługi Log Analytics |
| Współautor | Współautor usługi Log Analytics |
| Administrator | Właściciel |

Aby upewnić się, że żadne nadmiernego uprawnienia są przypisane do użytkowników, system nie będzie automatycznie przypisywani te uprawnienia na poziomie grupy zasobów. W związku z tym administratorzy obszaru roboczego ręcznie przypisać się _właściciela_ lub _współautora_ ról na poziomie grupy lub subskrypcji zasobów można wykonywać następujące czynności.

- Dodawanie lub usuwanie rozwiązania
- Definiowanie nowych widoków niestandardowych
- Zarządzanie alertami 

W niektórych przypadkach automatyczna konwersja nie można zastosować uprawnienia i wyświetli monit dla administratora, aby ręcznie przypisać uprawnienia.

## <a name="oms-mobile-app"></a>OMS aplikacji mobilnej
Aplikacja mobilna OMS będzie sunsetted wraz z portalu OMS. Zamiast OMS aplikacji mobilnej dostęp do informacji o infrastruktury IT, pulpity nawigacyjne i zapisane kwerendy dostępne portalu Azure bezpośrednio w przeglądarce urządzenia przenośnego. Aby uzyskać alerty, należy skonfigurować [grupy akcji Azure](../monitoring-and-diagnostics/monitoring-action-groups.md) otrzymywania powiadomień w postaci SMS lub połączeń głosowych

## <a name="application-insights-connector-and-solution"></a>Application Insights łącznika i rozwiązania
[Application Insights łącznik](log-analytics-app-insights-connector.md) umożliwia przenoszenie danych do usługi Application Insights do obszaru roboczego analizy dzienników. Ta deduplikacji danych jest wymagane w celu włączenia wgląd w dane infrastruktury i aplikacji.

Z obsługą programu [zapytania zasobów między](log-analytics-cross-workspace-search.md), nie będzie już trzeba zduplikowane dane. W efekcie zostaną wycofane istniejącego rozwiązania usługi Application Insights. Uruchamianie lipca, nie będzie mógł połączyć nowych zasobów usługi Application Insights do analizy dzienników obszarów roboczych. Istniejące linki i pulpity nawigacyjne będą nadal działać do czasu 2018 listopad.


## <a name="azure-network-security-group-analytics"></a>Analiza sieciowej grupy zabezpieczeń platformy Azure
[Rozwiązania analizy grupy zabezpieczeń sieci Azure](log-analytics-azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-log-analytics) zostaną zastąpione z ostatnio uruchomionego [analizy ruchu](../network-watcher/traffic-analytics.md) zapewniające wgląd w działania użytkowników i aplikacji w sieciach w chmurze. Analiza ruchu pomaga inspekcji aktywność sieci, zabezpieczenia aplikacji i danych organizacji, zoptymalizować wydajność przetwarzania obciążenia i pozostać zgodne. 

To rozwiązanie analizuje przepływu NSG dzienniki i zapewnia wgląd w informacje poniżej.

- Ruch w sieci platformy Azure i Internet, regionów chmury publicznej, sieci wirtualnych i podsieci.
- Protokołów w sieci, bez konieczności ataki penetratorów lub dedykowanych przepływu kolekcji urządzeń i aplikacji.
- Górny talkers chatty aplikacji konwersacje maszyny Wirtualnej w chmurze, ruch punkty aktywne.
- Źródła i miejsc docelowych ruchu między sieciami wirtualnymi, między relacje między usługi o krytycznym znaczeniu dla firmy i aplikacji.
- Zabezpieczenia, w tym złośliwego ruchu, internetowych, aplikacji lub maszyn wirtualnych próby dostępu do Internetu Otwórz porty.
- Wykorzystanie pojemności, co pomaga wyeliminować problemy dotyczące inicjowania obsługi administracyjnej lub niedostateczne wykorzystanie zasobów.

Można nadal zależą od ustawień diagnostycznych, aby wysłać dzienniki grupy NSG do analizy dzienników, istniejące zapisane wyszukiwania, alerty, pulpity nawigacyjne będą nadal działać. Klienci, którzy zainstalowali rozwiązania można nadal używać go deklarujące. Uruchamianie czerwca 20 rozwiązania NSG zostaną usunięte z portalu marketplace i dostępne za pośrednictwem Wspólnoty jako [szablonie Szybki Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="next-steps"></a>Kolejne kroki
- Zobacz [często zadawane pytania przejścia z portalu OMS do portalu Azure Log Analytics użytkowników](log-analytics-oms-portal-faq.md) wskazówki dotyczące przenoszenia z portalu OMS do portalu Azure.