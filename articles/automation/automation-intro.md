---
title: Omówienie usługi Azure Automation
description: Dowiedz się, jak używać usługi Azure Automation do automatyzacji cyklu życia infrastruktury i aplikacji.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, state configuration, update management, change tracking, DSC, inventory, runbooks, python, graphical
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010243"
---
# <a name="an-introduction-to-azure-automation"></a>Wprowadzenie do usługi Azure Automation

Ten artykuł zawiera krótkie omówienie usługi Azure Automation i odpowiedzi na niektóre często zadawane pytania. Aby uzyskać więcej informacji o różnych możliwościach, odwiedź linki zawarte w tym omówieniu.

## <a name="about-azure-automation"></a>Informacje o usłudze Azure Automation

Usługa Azure Automation zapewnia usługę automatyzacji i konfiguracji opartą na chmurze, która obsługuje spójne zarządzanie w środowiskach platformy Azure i innych niż Azure. Obejmuje automatyzację procesów, zarządzanie konfiguracją, zarządzanie aktualizacjami, funkcje współużytkowane i funkcje heterogeniczne. Automatyzacja zapewnia pełną kontrolę podczas wdrażania, operacji i likwidacji obciążeń i zasobów.

![Możliwości automatyzacji](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Automatyzacja procesów

Automatyzacja procesów w usłudze Azure Automation umożliwia automatyzację częstych, czasochłonnych i podatnych na błędy zadań zarządzania chmurą. Ta usługa pomaga skupić się na pracy, która zwiększa wartość biznesową. Poprzez zmniejszenie liczby błędów i zwiększenie wydajności pozwala ona również zmniejszyć koszty operacyjne. Środowisko operacyjne automatyzacji procesów jest szczegółowo opisane w [twórze uruchamiania w usłudze Azure Automation](automation-runbook-execution.md).

Automatyzacja procesów obsługuje integrację usług platformy Azure i innych systemów publicznych wymaganych podczas wdrażania, konfigurowania i zarządzania procesami end-to-end. Usługa umożliwia tworzenie [śmięty](automation-runbook-types.md) graficznie, w programie PowerShell lub za pomocą języka Python. Za pomocą [hybrydowego procesu roboczego życiówki](automation-hybrid-runbook-worker.md)można ujednolicić zarządzanie, organizując w środowiskach lokalnych. [Elementy webhook](automation-webhooks.md) umożliwiają spełnianie żądań i zapewnianie ciągłego dostarczania i operacji przez wyzwalanie automatyzacji z systemów ITSM, DevOps i monitorowania. 

## <a name="configuration-management"></a>Zarządzanie konfiguracją

[Konfiguracja stanu](automation-dsc-overview.md) usługi Azure Automation to rozwiązanie oparte na chmurze dla konfiguracji żądanego stanu programu PowerShell (DSC), które zapewnia usługi dla środowisk korporacyjnych. Za pomocą tej funkcji można zarządzać zasobami DSC w usłudze Azure Automation i stosować konfiguracje do maszyn wirtualnych lub fizycznych z serwera ściągania DSC w chmurze platformy Azure. Konfiguracje maszyn można monitorować i automatycznie aktualizować na maszynach fizycznych i wirtualnych w systemie Windows lub Linux w chmurze lub lokalnie. Obsługa zapasów umożliwia wykonywanie zapytań o zasoby gościa w celu wglądu w zainstalowane aplikacje i inne elementy konfiguracji.
 
Usługa konfiguracji stanu usługi Azure Automation zapewnia zaawansowane funkcje raportowania i wyszukiwania. Za pomocą tych funkcji można zlokalizować szczegółowe informacje o tym, co jest skonfigurowane w systemie operacyjnym. Usługa obsługuje śledzenie zmian w usługach, demonach, oprogramowaniu, rejestrze i plikach w twoim środowisku, aby ułatwić diagnozowanie niechcianych zmian i wysuwać alerty. Ważną funkcją pokrewną jest raportowanie głównych zdarzeń, na przykład zdarzeń wystawionych, gdy węzły odbiegają od przypisanych konfiguracji. 

## <a name="update-management"></a>Zarządzanie aktualizacjami

Usługa Azure Automation zawiera rozwiązanie [do zarządzania aktualizacjami](automation-update-management.md) dla systemów Windows i Linux w środowiskach hybrydowych. Dzięki temu rozwiązaniu można uzyskać wgląd w zgodność z aktualizacjami na platformie Azure i innych chmurach oraz w środowisku lokalnym. Zarządzanie aktualizacjami umożliwia tworzenie zaplanowanych wdrożeń, które organizują instalację aktualizacji w zdefiniowanym oknie konserwacji. Jeśli aktualizacja nie powinna być zainstalowana na komputerze, można użyć funkcji zarządzania aktualizacjami, aby wykluczyć ją z wdrożenia.

## <a name="shared-capabilities"></a>Współdzielone możliwości

Usługa Azure Automation oferuje szereg udostępnionych funkcji, w tym zasoby udostępnione, kontrolę dostępu opartą na rolach, elastyczne planowanie, integrację kontroli źródła, inspekcję i tagowanie.

### <a name="shared-resources"></a><a name="shared-resources"></a>Udostępnione zasoby

Usługa Azure Automation zawiera zestaw współdzielonych zasobów, które ułatwiają automatyzowanie i konfigurowanie środowiska odpowiednio do skali.

* **[Harmonogramy](automation-schedules.md)** — operacje automatyzacji wyzwalania w wstępnie zdefiniowanych godzinach.
* **[Moduły](automation-integration-modules.md)** — zarządzanie platformą Azure i innymi systemami. Moduły można importować do konta automatyzacji dla zasobów poleceń cmdlet i dsc firmy Microsoft, innych firm, społeczności i niestandardowych zdefiniowanych.
* **[Galeria modułów](automation-runbook-gallery.md)** — obsługuje natywną integrację z galerią programu PowerShell, aby umożliwić wyświetlanie śmiób runbook i importowanie ich do konta automatyzacji. Galeria umożliwia szybkie rozpoczęcie integracji i tworzenia procesów z galerii programu PowerShell i Centrum skryptów firmy Microsoft.
* **[Pakiety Python 2](python-packages.md)** - Obsługa 2 łańców ektonu Pythona dla twojego konta Automatyzacja.
* **[Poświadczenia](automation-credentials.md)** — bezpiecznie przechowywać poufne informacje, które runbook i konfiguracje mogą używać w czasie wykonywania.
* **[Połączenia](automation-connections.md)** — przechowuj pary nazw i wartości typowych informacji dla połączeń z systemami. Autor modułu definiuje połączenia w uruchomieniu i konfiguracje do użycia w czasie wykonywania.
* **[Certyfikaty](automation-certificates.md)** — definiowanie informacji, które mają być używane do uwierzytelniania i zabezpieczania wdrożonych zasobów, gdy są dostępne dla liczeni lub konfiguracji DSC w czasie wykonywania. 
* **[Zmienne](automation-variables.md)** — przytrzymaj zawartość, która może być używana w yjsce i konfiguracjach. Wartości zmiennych można zmieniać bez konieczności modyfikowania żadnych podstawowych podstawowych książek lub konfiguracji, które do nich odwołują.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Usługa Azure Automation obsługuje sterowanie dostępem oparte na rolach (RBAC), aby regulować dostęp do konta automatyzacji i jego zasobów. Aby dowiedzieć się więcej na temat konfigurowania funkcji RBAC na koncie automatyzacji, podręcznikach runbook i zadaniach, zobacz [Kontrola dostępu oparta na rolach dla usługi Azure Automation.](automation-role-based-access-control.md)

### <a name="source-control-integration"></a>Integracja kontroli źródła

Usługa Azure Automation umożliwia [integrację kontroli źródła.](source-control-integration.md) Ta funkcja promuje konfigurację jako kod, w którym elementy runbook lub konfiguracje mogą być zaewidencjonowane do systemu kontroli źródła.

## <a name="heterogeneous-support-windows-and-linux"></a>Obsługa heterogenii (Windows i Linux)

Automatyzacja została zaprojektowana do pracy w środowisku chmury hybrydowej, a także w systemach Windows i Linux. Zapewnia spójny sposób automatyzacji i konfigurowania wdrożonych obciążeń i systemów operacyjnych, które je uruchamiają.

## <a name="common-scenarios-for-automation"></a>Typowe scenariusze dotyczące usługi Automation

Usługa Azure Automation obsługuje zarządzanie w całym cyklu życia infrastruktury i aplikacji. Typowe scenariusze obejmują:

* **Napisz runbooki** — autora programów PowerShell, powershell Workflow, graficznych, Python 2 i DSC runbooks we wspólnych językach. 
* **Tworzenie i wdrażanie zasobów** — wdrażanie maszyn wirtualnych w środowisku hybrydowym przy użyciu żyłgorodków i szablonów usługi Azure Resource Manager. Integracja z narzędziami programistycznymi, takimi jak Jenkins i Azure DevOps.
* **Konfigurowanie maszyn wirtualnych** — ocenianie i konfigurowanie maszyn z systemem Windows i Linux za pomocą konfiguracji infrastruktury i aplikacji.
* **Dzielenie się wiedzą** — przesyłanie wiedzy do systemu na temat sposobu, w jaki twoja organizacja dostarcza i utrzymuje obciążenia. 
* **Pobierz zapasy** — pobierz kompletny spis wdrożonych zasobów do kierowania, raportowania i zgodności. 
* **Znajdź zmiany** — identyfikowanie zmian, które mogą powodować błędną konfigurację i poprawić zgodność operacyjną.
* **Monitor** — izolowanie zmian maszyn, które powodują problemy i korygować lub eskalować je do systemów zarządzania.
* **Ochrona** — maszyny kwarantanny, jeśli alerty zabezpieczeń są wywoływane. Ustaw wymagania dotyczące gościa.
* **Govern** - Skonfiguruj RBAC dla zespołów. Odzyskaj nieużywane zasoby.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Cennik usługi Automation

Możesz przejrzeć ceny skojarzone z usługą Azure Automation na stronie [cennika.](https://azure.microsoft.com/pricing/details/automation/)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie konta automatyzacji](automation-quickstart-create-account.md)

