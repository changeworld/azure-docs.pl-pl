---
title: Omówienie pakietu Operations Management Suite (OMS) | Microsoft Docs
description: Pakiet Microsoft Operations Management Suite (OMS) to oparte na chmurze rozwiązanie firmy Microsoft do zarządzania systemami IT, które ułatwia zarządzanie infrastrukturą lokalną i chmurową oraz jej ochronę.  W tym artykule przedstawiono zalety pakietu OMS, opisano różne usługi i oferty w ramach pakietu OMS oraz umieszczono linki do szczegółowych treści na ich temat.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.openlocfilehash: 94dedebe48060441cd3167fea87f6b721eb14517
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2017
ms.locfileid: "26684018"
---
# <a name="what-is-operations-management-suite-oms"></a>Co to jest pakiet Operations Management Suite (OMS)?
Ten artykuł zawiera wprowadzenie do pakietu Operations Management Suite (OMS), łącznie z krótkim omówieniem korzyści biznesowych związanych z korzystaniem z tego pakietu, zawartych w nim usług i rozwiązań do zarządzania oraz ofert obejmujących różne usługi i rozwiązania.  Dołączono linki do szczegółowej dokumentacji dotyczące wdrażania i używania poszczególnych usług oraz rozwiązań.

## <a name="from-on-premises-to-the-cloud"></a>Ze środowiska lokalnego do chmury
Firma Microsoft od dawna oferuje produkty do zarządzania środowiskami w przedsiębiorstwiach.  W 2007 r. wiele produktów dołączono do produktów do zarządzania wchodzących w skład pakietu System Center.  Dotyczyło to też programów Configuration Manager (oferuje on takie funkcje jak dystrybucja i spis oprogramowania), Operations Manager (umożliwia proaktywne monitorowanie systemów i aplikacji), Orchestrator (udostępnia elementy runbook w celu zautomatyzowania procesów ręcznych) oraz Data Protection Manager, który umożliwia wykonywanie kopii zapasowych i odzyskiwanie kluczowych danych.

Wraz z coraz większą liczbą zasobów obliczeniowych przenoszonych do chmury produkty programu System Center oferowały coraz więcej funkcji związanych z chmurą, takich jak zasoby zarządzania programów Operations Manager i Orchestrator na platformie Azure.  Jednak nadal zasadniczo były to rozwiązania lokalne i wymagały znaczących inwestycji związanych z wdrożeniem i utrzymaniem lokalnego środowiska zarządzania.  Konieczne było wprowadzenie nowych rozwiązań, które w pełni wykorzystają możliwości chmury i zapewnią obsługę aplikacji tworzonych w przyszłości.

## <a name="introducing-operations-management-suite"></a>Wprowadzenie do pakietu Operations Management Suite
Pakiet Operations Management Suite (OMS) to zbiór usług zarządzania zaprojektowanych od podstaw w chmurze.  Składniki pakietu OMS są w pełni hostowane na platformie Azure, dzięki czemu nie trzeba wdrażać zasobów lokalnych ani zarządzać nimi.  Konfiguracja jest ograniczona do minimalnego zakresu, a pracę można rozpocząć dosłownie w ciągu kilku minut.  

- **Minimalne koszt i złożoność wdrożenia.**  Wszystkie składniki i dane pakietu OMS są przechowywane na platformie Azure, dzięki czemu można szybko rozpocząć pracę bez konieczności stosowania złożonych składników lokalnych i inwestowania w nie.
- **Skalowanie do poziomu chmury.**  Nie musisz martwić się o płatności za zasoby obliczeniowe, których nie potrzebujesz, ani o zbyt małą ilość przestrzeni dyskowej, ponieważ dzięki chmurze możesz płacić tylko za rzeczywiste użycie i dowolnie skalować obciążenie.  Spróbuj zacząć od zarządzania kilkoma zasobami, a następnie skaluj je w górę w całym środowisku.
- **Korzystaj z najnowszych funkcji.**  Do pakietu OMS są stale dodawane nowe funkcje, a jego istniejące funkcje są nieustannie aktualizowane.  Masz stały dostęp do najnowszych funkcji bez konieczności wdrażania aktualizacji.
- **Zintegrowane usługi.**  Każda usługa pakietu OMS z osobna oferuje duże korzyści, a wspólnie umożliwiają realizowanie złożonych scenariuszy z zakresu zarządzania.  Na przykład element runbook w usłudze Azure Automation może przeprowadzić proces przechodzenia w tryb failover w usłudze Azure Site Recovery, a następnie zarejestrować informacje w usłudze Log Analytics i wygenerować alert.
- **Globalna wiedza.**  Rozwiązania do zarządzania w ramach pakietu OMS mają stały dostęp do najnowszych informacji.  Na przykład rozwiązanie Zabezpieczenia i inspekcja może przeprowadzić analizę zagrożeń, korzystając z najnowszych informacji o zagrożeniach wykrytych na całym świecie.
- **Dostęp z dowolnego miejsca.**  Możesz uzyskiwać dostęp do środowiska zarządzania z dowolnego miejsca za pomocą przeglądarki.  Zainstaluj aplikację OMS na smartfonie, aby uzyskać dostęp do danych monitorowania.

### <a name="is-it-just-for-the-cloud"></a>Czy jest to rozwiązanie tylko dla chmury?
Usługi pakietu OMS działają w chmurze, ale to nie oznacza, że nie mogą efektywnie zarządzać środowiskiem lokalnym.  Jeśli umieścisz agenta na dowolnym komputerze z systemem Windows lub Linux w centrum danych, będzie on wysyłać dane do usługi Log Analytics w celu ich przeanalizowania wraz ze wszystkimi pozostałymi danymi zebranymi z chmury lub usług lokalnych.  Usługi Azure Backup i Azure Site Recovery pozwalają używać chmury na potrzeby kopii zapasowych i wysokiej dostępności zasobów lokalnych.  
Elementy runbook w chmurze zwykle nie mają dostępu do zasobów lokalnych, ale możesz też zainstalować na komputerach agenta, który będzie hostować elementy runbook w centrum danych.  Po uruchomieniu elementu runbook wystarczy określić, czy ma on działać w chmurze czy w lokalnym procesie roboczym.

## <a name="hybrid-management-with-system-center"></a>Zarządzanie hybrydowe za pomocą programu System Center
Jeśli masz istniejącą instalację programu System Center, możesz zintegrować te składniki z usługami pakietu OMS, wykorzystując zalety poszczególnych produktów w celu utworzenia rozwiązania hybrydowego dla środowiska lokalnego i środowiska w chmurze.  Połącz grupę zarządzania programu Operations Manager z usługą Log Analytics, aby móc analizować zarządzanych agentów w chmurze.  Możesz w niezmieniony sposób wykonywać kopie zapasowe danych w chmurze za pomocą programu Data Protection Manager.  


## <a name="oms-services"></a>Usługi pakietu OMS
Podstawowe funkcje pakietu OMS są oferowane jako zbiór usług działających na platformie Azure.  Każda usługa udostępnia określoną funkcję zarządzania. Możesz łączyć usługi, aby realizować różne scenariusze zarządzania.

|| Usługa | Opis |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Monitorowanie i analizowanie dostępności oraz wydajności różnych zasobów, łącznie z maszynami fizycznymi i wirtualnymi. |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automatyzacja | Automatyzowanie procesów ręcznych oraz wymuszanie konfiguracji maszyn fizycznych i wirtualnych. |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Tworzenie kopii zapasowych | Wykonywanie kopii zapasowych i przywracanie kluczowych danych. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Zapewnianie wysokiej dostępności kluczowych aplikacji. |

### <a name="log-analytics"></a>Log Analytics
Usługa [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) umożliwia monitorowanie pakietu OMS przez zbieranie danych z zarządzanych zasobów w centralnym repozytorium.  Te dane mogą obejmować zdarzenia, dane wydajności i niestandardowe dane dostarczane przez interfejs API. Zebrane dane są dostępne na potrzeby alertów, analizy i eksportu.  Ta metoda umożliwia konsolidowanie danych z różnych źródeł, dzięki czemu można połączyć dane z usług platformy Azure z istniejącym środowiskiem lokalnym.  Ponadto wprowadza wyraźny podział między zbieraniem danych a akcjami wykonanymi na tych danych, tak aby wszystkie akcje były dostępne dla wszystkich typów danych.  

![Omówienie usługi Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Zbieranie danych
Istnieje wiele sposobów przekazywania danych do repozytorium na potrzeby analizy w usłudze Log Analytics.

- **Maszyny wirtualne i komputery z systemem Windows lub Linux.**  Zainstaluj program Microsoft Monitoring Agent na maszynach wirtualnych lub komputerach z systemami [Windows](../log-analytics/log-analytics-windows-agent.md) i [Linux](../log-analytics/log-analytics-linux-agents.md), z których mają być zbierane dane.  Agent automatycznie pobierze dane z konfiguracji usługi Log Analytics, w której określono zdarzenia i dane wydajności do zebrania.  Możesz łatwo zainstalować agenta na maszynach wirtualnych działających na platformie Azure za pomocą witryny Azure Portal.  Jeśli masz istniejące środowisko programu Operations Manager, możesz połączyć grupę zarządzania z usługą Log Analytics i automatycznie zacząć zbierać dane ze wszystkich istniejących agentów.
- **Usługi platformy Azure.**  Usługa Log Analytics zbiera dane telemetryczne z [usług diagnostyki i monitorowania platformy Azure](../log-analytics/log-analytics-azure-storage.md), a następnie zapisuje je w repozytorium, aby umożliwić monitorowanie zasobów platformy Azure.
- **Interfejs API modułu zbierającego dane.**  Usługa Log Analytics zawiera [interfejs API REST umożliwiający wypełnianie danych z dowolnego klienta](../log-analytics/log-analytics-data-collector-api.md).  Pozwala to zbierać dane z aplikacji innych firm lub Implementować niestandardowe scenariusze zarządzania.  Częstym rozwiązaniem jest zbieranie danych za pomocą elementu runbook w usłudze Azure Automation, a następnie zapisywanie danych w repozytorium za pomocą interfejsu API modułu zbierającego dane.

#### <a name="reporting-and-analyzing-data"></a>Raportowanie i analizowanie danych
Usługa Log Analytics obejmuje zaawansowany język zapytań umożliwiający wyodrębnianie danych przechowywanych w repozytorium.  Dane ze wszystkich źródeł są przechowywane jako rekordy, dlatego można przeanalizować dane z wielu źródeł w ramach jednego zapytania.
  
Oprócz analizy ad hoc usługa Log Analytics oferuje wiele metod raportowania i analizowania danych z zapytań.

- **Widoki i pulpity nawigacyjne.**  [Widoki](../log-analytics/log-analytics-view-designer.md) i [pulpity nawigacyjne](../log-analytics/log-analytics-dashboards.md) pozwalają wizualnie przedstawić wyniki zapytania w portalu.  Rozwiązania do zarządzania zwykle zawierają widoki, które umożliwiają analizowanie własnych danych.  Możesz też tworzyć własne widoki niestandardowe na potrzeby analizowania danych i udostępniania ich w portalu niestandardowym.
- **Eksportowanie.**  Możesz wyeksportować wyniki dowolnego zapytania, aby przeanalizować je poza usługą Log Analytics.  Możesz nawet zaplanować regularne eksportowanie do usługi [Power BI](../log-analytics/log-analytics-powerbi.md), która oferuje zaawansowane funkcje wizualizacji i analizy.
- **Interfejs API funkcji przeszukiwania dzienników.**  Usługa Log Analytics zawiera [interfejs API REST umożliwiający zbieranie danych z dowolnego klienta](../log-analytics/log-analytics-log-search-api.md).  Pozwala to programowo pracować z danymi zbieranymi w repozytorium i uzyskiwać do nich dostęp z innego narzędzia do monitorowania.

#### <a name="alerting"></a>Generowanie alertów
Usługa Log Analytics może [proaktywnie generować alerty](../log-analytics/log-analytics-alerts.md) lub podejmować działania naprawcze po wykryciu problemu.  Tak jak w przypadku wszystkich pozostałych funkcji analizy w usłudze Log Analytics jest używane przeszukiwanie dzienników.  To wyszukiwanie jest przeprowadzane zgodnie z ustalonym harmonogramem, a w przypadku zgodności wyników z określonymi kryteriami jest tworzony alert.

![Alerty usługi Log Analytics](media/operations-management-suite-overview/overview-alerts.png)

Poniżej przedstawiono inne możliwe działania alertów oprócz utworzenia rekordu alertu w repozytorium usługi Log Analytics.

- **E-mail.**  Wysyłanie wiadomości e-mail w celu proaktywnego powiadamiania o wykrytych problemach.
- **Runbook.**  Alert w usłudze Log Analytics może uruchomić element runbook w usłudze Azure Automation.  Zwykle ma to na celu próbę rozwiązania wykrytego problemu.  Element runbook może zostać uruchomiony w chmurze w przypadku problemu na platformie Azure lub w innej chmurze albo może zostać uruchomiony na agencie lokalnym w przypadku problemu na maszynie fizycznej lub wirtualnej.
- **Webhook.**  Alert może uruchomić element webhook i przekazać do niego dane z wyników przeszukiwania dzienników.  Umożliwia to integrację z usługami zewnętrznymi, takimi jak alternatywny systemu alertów, oraz pozwala próbować wykonywać działania naprawcze w przypadku zewnętrznej witryny sieci Web.

### <a name="azure-automation"></a>Azure Automation
Usługa [Azure Automation](http://azure.microsoft.com/documentation/services/automation) umożliwia zautomatyzowanie procesów i zarządzanie konfiguracjami w pakiecie OMS.  Automatyzuje ona procesy ręczne oraz ułatwia wymuszanie konfiguracji komputerów fizycznych i wirtualnych.  

#### <a name="process-automation"></a>Automatyzacja procesów
Usługa Azure Automation umożliwia automatyzowanie procesów ręcznych przy użyciu elementów [runbook](../automation/automation-runbook-types.md) opartych na skrypcie programu PowerShell lub przepływie pracy programu PowerShell.  Zawiera także zasoby pomocnicze dla elementów runbook, takie jak zmienne, które mogą być współużytkowane przez wiele elementów runbook, oraz poświadczenia i połączenia umożliwiające przechowywanie zaszyfrowanych informacji, które mogą być wymagane do uwierzytelnienia elementu runbook.
Elementy runbook umożliwiają automatyzację procesów w innych usługach pakietu.  Dostęp do każdej z pozostałych usług jest możliwy za pomocą programu PowerShell lub interfejsu API REST, dlatego można tworzyć elementy runbook wykonujące takie działania jak zbieranie danych zarządzania w usłudze Log Analytics lub inicjowanie kopii zapasowej za pomocą usługi Azure Backup.

##### <a name="accessing-resources"></a>Uzyskiwanie dostępu do zasobów
Elementy runbook są oparte na programie PowerShell, dlatego mogą zarządzać każdym zasobem, do którego można uzyskiwać dostęp za pomocą poleceń cmdlet programu PowerShell.  [Załadowanie modułu](../automation/automation-integration-modules.md) na koncie usługi Automation powoduje udostępnienie go dla wszystkich elementów runbook na tym koncie. 
 
Gdy elementy runbook działają w chmurze, mogą uzyskiwać dostęp do wszystkich zasobów w chmurze.  Mogą to być zasoby w ramach Twojej subskrypcji platformy Azure, w innej chmurze, takiej jak usługi Amazon Web Services (AWS), lub w usłudze dostępnej za pośrednictwem interfejsu API REST.  Do działania elementów runbook w chmurze nie są wymagane żadne poświadczenia, ale mogą one używać elementów zawartości usługi Automation, takich jak poświadczenia, połączenia i certyfikaty, na potrzeby uwierzytelniania w zasobach, do których uzyskują dostęp.

Najprawdopodobniej zasoby w centrum danych nie są dostępne z poziomu elementu runbook działającego w chmurze.  Możesz zainstalować [hybrydowe procesy robocze elementu runbook](../automation/automation-hybrid-runbook-worker.md) w centrum danych, aby uruchamiać elementy runbook wymagające dostępu do zasobów lokalnych.  Po uruchomieniu elementu runbook możesz określić, czy ma on działać w chmurze czy w określonym procesie roboczym.

![Elementy runbook usługi Azure Automation](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Uruchamianie elementu runbook
Elementy runbook mogą być [uruchamiane na wiele sposobów](../automation/automation-starting-a-runbook.md), dzięki czemu mogą być dołączane do różnych scenariuszy zarządzania.  

- **Azure Portal.**  Podobnie jak w przypadku innych usług platformy Azure usługą Azure Automation można zarządzać za pomocą witryny Azure Portal.  Oprócz uruchamiania elementów runbook można je importować i tworzyć własne elementy.
- **Planowanie.**  Możesz zaplanować uruchamianie elementów runbook w regularnych odstępach czasu.  Pozwala to automatycznie regularnie powtarzać proces zarządzania lub zbierać dane do usługi Log Analytics.
- **Program PowerShell i interfejs API.**  Możesz uruchamiać elementy runbook i przekazywać do nich wymagane informacje o parametrach z polecenia cmdlet programu PowerShell lub interfejsu API REST usługi Azure Automation.  
- **Webhook.**  Element webhook można utworzyć dla każdego elementu runbook, który umożliwia uruchamianie z zewnętrznych aplikacji lub witryn sieci Web.
- **Alert usługi Log Analytics.**  Alert w usłudze Log Analytics może automatycznie uruchomić element runbook w celu podjęcia próby rozwiązania problemu wskazanego przez alert.

#### <a name="configuration-management"></a>Zarządzanie konfiguracją
[Konfiguracja żądanego stanu (DSC) programu PowerShell](../automation/automation-dsc-overview.md) to platforma do zarządzania w programie Windows PowerShell, która umożliwia wdrażanie oraz wymuszanie konfiguracji maszyn fizycznych i wirtualnych.  Usługa Azure Automation zarządza konfiguracjami DSC i udostępnia serwer ściągania w chmurze, z którego agenci mogą pobierać wymagane konfiguracje.

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup i Azure Site Recovery
Usługi Azure Backup i Azure Site Recovery ułatwiają zachowanie ciągłości działania oraz odzyskiwanie po awarii.  Każda z nich oferuje funkcje, które ułatwiają zapewnienie dostępności aplikacji w przypadku przestojów i powrót do normalnego działania po przywróceniu systemów do trybu online.  Obie te usługi ułatwiają osiąganie celów punktów odzyskiwania i celów czasu odzyskiwania zdefiniowanych dla organizacji. Cel punktu odzyskiwania definiuje limit, w ramach którego dane są niedostępne podczas przestoju, natomiast cel czasu odzyskiwania ogranicza czas, w którym usługa lub aplikacja jest niedostępna podczas przestoju.

#### <a name="azure-backup"></a>Azure Backup
Usługa [Azure Backup](http://azure.microsoft.com/documentation/services/backup) umożliwia wykonywanie kopii zapasowych i przywracanie danych w pakiecie OMS.  Chroni ona dane aplikacji i przechowuje je przez wiele lat bez konieczności ponoszenia jakichkolwiek inwestycji kapitałowych i przy minimalnych kosztach operacyjnych.  Poza tworzeniem kopii zapasowych z obciążeń aplikacji, takich jak SQL Server i SharePoint, może także tworzyć kopie zapasowe danych z fizycznych i wirtualnych serwerów z systemem Windows.  Może również być wykorzystywana przez program System Center Data Protection Manager (DPM) do replikacji chronionych danych na platformę Azure w celu uzyskania nadmiarowości i długoterminowego przechowywania.

Chronione dane w usłudze Azure Backup są przechowywane w magazynie kopii zapasowych, znajdującym się w określonym regionie geograficznym. Dane są replikowane w obrębie tego samego regionu, a następnie, w zależności od typu magazynu, mogą być również replikowane do innego regionu w celu osiągnięcia większej odporności.

Usługa Azure Backup ma trzy podstawowe scenariusze.

- **Maszyna z systemem Windows z agentem usługi Azure Backup.** Możesz wykonywać kopie zapasowe plików i folderów z dowolnego serwera lub klienta systemu Windows bezpośrednio w magazynie kopii zapasowych Azure.<br><br>![Maszyna z systemem Windows z agentem usługi Azure Backup](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) lub Microsoft Azure Backup Server.** Program DPM i usługa Microsoft Azure Backup Server umożliwiają wykonywanie kopii zapasowych plików i folderów, a także obciążeń aplikacji, takich jak SQL i SharePoint, w magazynie lokalnym, a następnie replikowanie do magazynu kopii zapasowych Azure. Obsługuje maszyny wirtualne z systemem Windows lub Linux korzystające z funkcji Hyper-V lub oprogramowania VMware.<br><br>![System Center Data Protection Manager (DPM) lub Microsoft Azure Backup Server](media/operations-management-suite-overview/overview-backup-02.png)
- **Rozszerzenia maszyny wirtualnej platformy Azure.** Możesz wykonywać kopie zapasowe maszyn wirtualnych z systemem Windows lub Linux działających na platformie Azure w magazynie kopii zapasowych Azure.<br><br>![Rozszerzenia maszyny wirtualnej platformy Azure](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
Usługa [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) umożliwia zachowanie ciągłości działania przez organizowanie replikacji lokalnych maszyn wirtualnych i fizycznych do platformy Azure lub lokacji dodatkowej. Jeśli lokacja podstawowa jest niedostępna, możesz przejść w tryb failover do lokalizacji dodatkowej, aby użytkownicy mogli kontynuować pracę, i wrócić po awarii, gdy systemy wznowią działanie. 

Usługa Azure Site Recovery zapewnia wysoką dostępność serwerów i aplikacji.  Przyczynia się ona do realizacji strategii ciągłości biznesowej i odzyskiwania po awarii (BCDR, business continuity and disaster recovery) przez organizowanie replikacji, pracy w trybie failover i odzyskiwania danych w odniesieniu do lokalnych maszyn wirtualnych funkcji Hyper-V, maszyn wirtualnych oprogramowania VMware oraz serwerów fizycznych z systemem Windows lub Linux. Można replikować maszyny do dodatkowego centrum danych lub rozszerzyć centrum danych przez replikowanie ich na platformę Azure. Usługa Site Recovery udostępnia także prosty tryb failover oraz odzyskiwanie dla obciążeń. Integruje się z mechanizmami odzyskiwania po awarii, takimi jak SQL Server AlwaysOn, i udostępnia plany odzyskiwania w celu zapewnienia łatwego trybu failover dla obciążeń, które są rozmieszczone warstwowo na różnych maszynach.

Usługa Azure Site Recovery ma trzy podstawowe scenariusze replikacji.

- **Replikacja maszyn wirtualnych funkcji Hyper-V.**  Jeśli maszyny wirtualne funkcji Hyper-V są zarządzane w chmurach programu VMM, można replikować do dodatkowego centrum danych lub do magazynu Azure. Replikacja do platformy Azure odbywa się za pośrednictwem bezpiecznego połączenia internetowego. Replikacja do dodatkowego centrum danych odbywa się za pośrednictwem sieci LAN.  Jeśli maszyny wirtualne funkcji Hyper-V nie są zarządzane przez program VMM, można replikować tylko do magazynu Azure. Replikacja do platformy Azure odbywa się za pośrednictwem bezpiecznego połączenia internetowego.<br><br>![Replikacja maszyn wirtualnych funkcji Hyper-V](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Replikacja maszyn wirtualnych oprogramowania VMware.**  Maszyny wirtualne VMware można replikować do dodatkowego centrum danych z uruchomionym programem VMware lub do magazynu Azure. Replikacja do platformy Azure może odbywać się przy użyciu sieci VPN typu „lokacja do lokacji”, usługi Azure ExpressRoute lub bezpiecznego połączenia internetowego. Replikacja do dodatkowego centrum danych odbywa się za pośrednictwem kanału danych InMage Scout.<br><br>![Replikacja maszyn wirtualnych VMware](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Replikacja serwerów fizycznych z systemami Windows i Linux.**  Serwery fizyczne można replikować do dodatkowego centrum danych lub magazynu Azure. Replikacja do platformy Azure może odbywać się przy użyciu sieci VPN typu „lokacja do lokacji”, usługi Azure ExpressRoute lub bezpiecznego połączenia internetowego. Replikacja do dodatkowego centrum danych odbywa się za pośrednictwem kanału danych InMage Scout. Usługa Azure Site Recovery obejmuje rozwiązanie pakietu OMS, które wyświetla niektóre statystyki, ale dla wszystkich operacji wymagane jest użycie witryny Azure Portal.<br><br>![Replikacja serwerów fizycznych z systemami Windows i Linux](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Usługa Site Recovery przechowuje metadane w magazynach znajdujących się w określonym regionie geograficznym świadczenia usługi Azure. Żadne replikowane dane nie są przechowywane w usłudze Site Recovery.

## <a name="management-solutions"></a>Rozwiązania do zarządzania
[Rozwiązania do zarządzania](operations-management-suite-solutions.md) to wstępnie spakowane zestawy logiki implementujące określony scenariusz zarządzania z użyciem co najmniej jednej usługi pakietu OMS.  Dostępne są różne rozwiązania oferowane przez firmę Microsoft i partnerów, które można łatwo dodać do subskrypcji platformy Azure w celu zwiększenia wartości inwestycji w pakiet OMS.  Partnerzy mogą tworzyć własne rozwiązania do obsługi danych aplikacji i usług oraz udostępniać je użytkownikom za pośrednictwem witryny Azure Marketplace lub szablonów z pakietu Quickstart.

Dobrym przykładem rozwiązania korzystającego z wielu usług w celu zapewnienia dodatkowych funkcji jest [rozwiązanie do zarządzania aktualizacjami](oms-solution-update-management.md).  To rozwiązanie zbiera informacje o wymaganych aktualizacjach na każdym agencie przy użyciu agenta usługi Log Analytics dla systemów Windows i Linux.  Te dane są zapisywane w repozytorium usługi Log Analytics, w którym można je przeanalizować za pomocą dołączonego pulpitu nawigacyjnego.  W przypadku tworzenia wdrożenia elementy runbook w usłudze Azure Automation umożliwiają instalowanie wymaganych aktualizacji.  Możesz zarządzać całym procesem w portalu, dzięki czemu nie musisz przejmować się szczegółami.

![Rozwiązanie](media/operations-management-suite-overview/overview-solution.png)

Większość rozwiązań może wykonywać co najmniej jedno z poniższych działań.

- Zbieranie dodatkowych informacji.  Usługa Log Analytics zbiera różne dane z klientów i usług, w tym zdarzenia i dane wydajności.  Rozwiązanie do zarządzania może zbierać dodatkowe informacje, które nie są dostępne z innych źródeł danych — często przy użyciu elementów runbook usługi Azure Automation.
- Dodatkowa analiza zebranych informacji.  Rozwiązania do zarządzania zawierają pulpity nawigacyjne i widoki, które umożliwiają analizowanie i wizualizowanie danych.  Są one połączone ze wstępnie zdefiniowanymi operacjami przeszukiwania dzienników, dzięki którym można przejść do szczegółowych danych.  Umożliwiają one też analizowanie danych, które zostały już zebrane w repozytorium, na przykład przeszukiwanie zdarzeń zabezpieczeń pod kątem wzorców wskazujących zagrożenie.
- Dodawanie funkcji.  Niektóre rozwiązania firmy Microsoft mogą korzystać z możliwości podstawowych usług w celu udostępnienia dodatkowych funkcji.  Na przykład mapa usługi zawiera własną konsolę do odnajdywania oraz mapowania zależności serwerów i procesów w czasie rzeczywistym.
Rozwiązania są regularnie dodawane do pakietu OMS przez firmę Microsoft i partnerów, dzięki czemu możesz stale zwiększać wartość inwestycji.  Wykaz rozwiązań w portalu pakietu OMS umożliwia przeglądanie i instalowanie rozwiązań firmy Microsoft, natomiast witryna Azure Marketplace w witrynie Azure Portal umożliwia przeglądanie oraz instalowanie rozwiązań firmy Microsoft i partnerów.  

![Galeria rozwiązań](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o usłudze [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Dowiedz się więcej o usłudze [Azure Automation](../automation/automation-intro.md).
* Dowiedz się więcej o usłudze [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Dowiedz się więcej o usłudze [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).
* Sprawdź [rozwiązania dostępne](../log-analytics/log-analytics-add-solutions.md) w ramach różnych ofert pakietu OMS. 

