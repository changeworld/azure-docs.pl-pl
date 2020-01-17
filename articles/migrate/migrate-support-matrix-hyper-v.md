---
title: Obsługa oceny funkcji Hyper-V w Azure Migrate
description: Dowiedz się więcej o obsłudze oceny funkcji Hyper-V za pomocą Azure Migrate.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 1a036e2f22bb1fd9dac65a3cc643224ecbea3c69
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154809"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Macierz obsługi dla oceny funkcji Hyper-V

W tym artykule opisano ustawienia i ograniczenia dotyczące oceny maszyn wirtualnych funkcji Hyper-V za pomocą [Azure Migrate: Ocena serwera](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Jeśli szukasz informacji na temat migrowania maszyn wirtualnych funkcji Hyper-V do platformy Azure, zapoznaj się z [matrycą obsługi migracji](migrate-support-matrix-hyper-v-migration.md).

## <a name="overview"></a>Przegląd

Aby ocenić maszyny lokalne na potrzeby migracji na platformę Azure z tego artykułu, należy dodać Azure Migrate: Narzędzie do oceny serwera do projektu Azure Migrate. Należy wdrożyć [urządzenie Azure Migrate](migrate-appliance.md). Urządzenie stale odnajduje maszyny lokalne i wysyła dane dotyczące konfiguracji i wydajności na platformę Azure. Po odnajdywaniu maszyn można zebrać odnalezione maszyny do grup i uruchomić ocenę dla grupy.


## <a name="limitations"></a>Ograniczenia

**Pomoc techniczna** | **Szczegóły**
--- | ---
**Limity oceny**| Odkrywaj i oceniaj do 35 000 maszyn wirtualnych funkcji Hyper-V w jednym [projekcie](migrate-support-matrix.md#azure-migrate-projects).
**Limity projektu** | Możesz utworzyć wiele projektów w ramach subskrypcji platformy Azure. Projekt może obejmować maszyny wirtualne VMware, maszyny wirtualne funkcji Hyper-V i serwery fizyczne oraz limity oceny.
**Discovery** (Odnajdywanie) | Urządzenie Azure Migrate może odnajdywać maksymalnie 5000 maszyn wirtualnych funkcji Hyper-V.<br/><br/> Urządzenie może połączyć się z maksymalnie 300 hostami funkcji Hyper-V.
**Ocena** | Można dodać do 35 000 maszyn w jednej grupie.<br/><br/> W ramach jednej oceny można ocenić do 35 000 maszyn wirtualnych.

[Dowiedz się więcej](concepts-assessment-calculation.md) na temat ocen.



## <a name="hyper-v-host-requirements"></a>Wymagania dotyczące hosta funkcji Hyper-V

| **Pomoc techniczna**                | **Szczegóły**               
| :-------------------       | :------------------- |
| **Wdrożenie hosta**       | Host funkcji Hyper-V może być autonomiczny lub wdrożony w klastrze. |
| **Uprawnienia**           | Wymagane są uprawnienia administratora na hoście funkcji Hyper-V. <br/> Alternatywnie, jeśli nie chcesz przypisywać uprawnień administratora, Utwórz konto użytkownika lokalnego lub domeny i Dodaj tego użytkownika do tych grup — Użytkownicy zarządzania zdalnego, Administratorzy funkcji Hyper-V i użytkownicy monitora wydajności. |
| **System operacyjny hosta** | Windows Server 2019, Windows Server 2016 lub Windows Server 2012 R2.<br/> Nie można ocenić maszyn wirtualnych znajdujących się na hostach funkcji Hyper-V z systemem Windows Server 2012. |
| **Komunikacja zdalna programu PowerShell**   | Musi być włączona na każdym hoście. |
| **Funkcja Hyper-V Replica**       | W przypadku korzystania z funkcji Hyper-V Replica (lub korzystania z wielu maszyn wirtualnych z tymi samymi identyfikatorami maszyn wirtualnych) i odnajdywania zarówno oryginalnej, jak i zreplikowanej maszyny wirtualnej przy użyciu Azure Migrate, Ocena wygenerowana przez Azure Migrate może być niedokładna. |


## <a name="hyper-v-vm-requirements"></a>Wymagania dotyczące maszyn wirtualnych funkcji Hyper-V

| **Pomoc techniczna**                  | **Szczegóły**               
| :----------------------------- | :------------------- |
| **System operacyjny** | Wszystkie systemy operacyjne [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) i [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , które są obsługiwane przez platformę Azure. |
| **Usługi integracji**       | [Usługi integracji funkcji Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) muszą być uruchomione na maszynach wirtualnych, które oceniasz, aby przechwycić informacje o systemie operacyjnym. |


## <a name="azure-migrate-appliance-requirements"></a>Wymagania dotyczące urządzenia Azure Migrate

Azure Migrate używa [urządzenia Azure Migrate](migrate-appliance.md) do odnajdowania i oceny. Urządzenie dla funkcji Hyper-V działa na maszynie wirtualnej funkcji Hyper-V i wdrożone przy użyciu skompresowanego wirtualnego dysku twardego funkcji Hyper-V pobranego z Azure Portal. 

- Dowiedz się więcej o [wymaganiach dotyczących urządzeń](migrate-appliance.md#appliance---hyper-v) dla funkcji Hyper-V.
- Informacje o [adresach URL](migrate-appliance.md#url-access) , do których urządzenie musi uzyskać dostęp.

## <a name="port-access"></a>Dostęp do portu

Poniższa tabela zawiera podsumowanie wymagań dotyczących portów dla oceny.

**urządzenia** | **Połączenie**
--- | ---
**Wprowadzony** | Połączenia przychodzące na porcie TCP 3389, aby zezwolić na połączenia pulpitu zdalnego z urządzeniem.<br/> Połączenia przychodzące na porcie 44368 do zdalnego dostępu do aplikacji do zarządzania urządzeniami przy użyciu adresu URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Połączenia wychodzące na portach 443, 5671 i 5672 do wysyłania metadanych odnajdywania i wydajności do Azure Migrate.
**Host/klaster funkcji Hyper-V** | Połączenia przychodzące na portach usługi WinRM 5985 (HTTP) i 5986 (HTTPS) do ściągania metadanych konfiguracji i wydajności maszyn wirtualnych funkcji Hyper-V przy użyciu sesji model wspólnych informacji (CIM).

## <a name="agent-based-dependency-visualization"></a>Wizualizacja zależności oparta na agencie

[Wizualizacja zależności](concepts-dependency-visualization.md) ułatwia wizualizację zależności między maszynami, które mają zostać poddane ocenie i przeprowadzeniem migracji. W przypadku wizualizacji opartej na agencie, wymagania i ograniczenia są podsumowane w poniższej tabeli.


**Wymaganie** | **Szczegóły**
--- | ---
**Wdrożenie** | Przed wdrożeniem wizualizacji zależności należy mieć projekt Azure Migrate na miejscu, z Azure Migrate: Narzędzie do oceny serwera dodane do projektu. Wizualizacja zależności jest wdrażana po skonfigurowaniu urządzenia Azure Migrate w celu odnalezienia maszyn lokalnych.<br/><br/> Wizualizacja zależności nie jest dostępna w Azure Government.
**Mapa usługi** | Wizualizacja zależności oparta na agentach używa rozwiązania [Service map](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) w [dziennikach Azure monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).<br/><br/> Aby wdrożyć program, należy skojarzyć nowy lub istniejący obszar roboczy Log Analytics z projektem Azure Migrate.
**Obszar roboczy usługi Log Analytics** | Obszar roboczy musi znajdować się w tej samej subskrypcji co projekt Azure Migrate.<br/><br/> Azure Migrate obsługuje obszary robocze znajdujące się w regionach Wschodnie stany USA, Azja Południowo-Wschodnia i Europa Zachodnia.<br/><br/>  Obszar roboczy musi znajdować się w regionie, w którym [jest obsługiwana Service map](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites).<br/><br/> Obszaru roboczego dla projektu Azure Migrate nie można modyfikować po jego dodaniu.
**Charges** (Opłaty) | Service Map rozwiązanie nie wiąże się z żadnymi opłatami za pierwsze 180 dni (od dnia skojarzonego Log Analytics obszaru roboczego z projektem Azure Migrate).<br/><br/> Po 180 dniach opłaty zostaną naliczone według standardowych Log Analytics.<br/><br/> Użycie dowolnego rozwiązania innego niż Service Map w skojarzonym Log Analytics obszarze roboczym spowoduje naliczenie standardowych Log Analytics opłat.<br/><br/> Jeśli usuniesz projekt Azure Migrate, obszar roboczy nie zostanie usunięty z nim. Po usunięciu projektu Service Map nie jest bezpłatny, a każdy węzeł będzie obciążany zgodnie z warstwą płatną Log Analytics obszarze roboczym.
**Pracownicy** | Wizualizacja zależności oparta na agentach wymaga zainstalowania dwóch agentów na każdej maszynie, która ma zostać przeanalizowana.<br/><br/> - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [Agent zależności](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)- . 
**Łączność z Internetem** | Jeśli maszyny nie są połączone z Internetem, należy zainstalować na nich bramę Log Analytics.


## <a name="next-steps"></a>Następne kroki

[Przygotowanie do oceny maszyn wirtualnych funkcji Hyper-V](tutorial-prepare-hyper-v.md)
