---
title: Rozwiązywanie problemów z odzyskiwaniem po awarii w odzyskiwaniu po awarii maszyny Wirtualnej VMware za pomocą usługi Azure Site Recovery
description: W tym artykule opisano sposoby rozwiązywania problemów z powrotem po awarii i ponownego wycofywania podczas odzyskiwania po awarii maszyny Wirtualnej VMware na platformie Azure za pomocą usługi Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: b577b82585ffad0547818b4f19554a2f39cb830c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498101"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Rozwiązywanie problemów z powrotem po awarii do lokacji lokalnej z platformy Azure

W tym artykule opisano sposób rozwiązywania problemów, które mogą wystąpić po powiększaniu maszyn wirtualnych platformy Azure po awarii lokalnej infrastruktury VMware, po przejściu w tryb failover do platformy Azure przy użyciu [usługi Azure Site Recovery.](site-recovery-overview.md)

Powrót awaryjny zasadniczo obejmuje dwa główne kroki. W pierwszym kroku po przewijanie w błąd należy ponownie przeceniać maszyny wirtualne platformy Azure do lokalnych, tak aby rozpocząć replikację. Drugim krokiem jest uruchomienie pracy awaryjnej z platformy Azure, aby wrócić po awarii do lokacji lokalnej.

## <a name="common-issues"></a>Typowe problemy

- Jeśli użytkownik tylko do odczytu odnajduje i chroni maszyny wirtualne, ochrona powiedzie się i pracy awaryjnej. Podczas ponownego przekazywania, pracy awaryjnej kończy się niepowodzeniem, ponieważ nie można odnajdyć magazynów danych. Symptomem jest to, że magazyny danych nie są wymienione podczas ponownego przekazywania. Aby rozwiązać ten problem, można zaktualizować poświadczenia vCenter za pomocą odpowiedniego konta, które ma uprawnienia, a następnie ponowić próbę zadania.
- Po wykonaniu komputera z systemem Wirtualnym systemu Linux i uruchomieniu jej lokalnie widać, że pakiet Menedżera sieci został odinstalowany z komputera. Ta dezinstalacja występuje, ponieważ pakiet Usługi Network Manager jest usuwany, gdy maszyna wirtualna jest odzyskiwana na platformie Azure.
- Gdy maszyna wirtualna systemu Linux jest skonfigurowana ze statycznym adresem IP i jest przejęta awaryjnie na platformie Azure, adres IP jest pobierany z usługi DHCP. Po przełączeniu w trybie awaryjnym do lokalnego, maszyna wirtualna nadal używa dhcp do uzyskania adresu IP. Ręcznie zaloguj się do urządzenia, a następnie ustaw adres IP z powrotem na adres statyczny, jeśli to konieczne. Maszyna wirtualna systemu Windows może ponownie uzyskać swój statyczny adres IP.
- Jeśli używasz bezpłatnej wersji ESXi 5.5 lub wersji wolnej od hypervisora vSphere 6, przejście w tył w tył kończy się pomyślnie, ale powrót po awarii nie powiedzie się. Aby włączyć powrót po awarii, uaktualnij do licencji ewaluacyjnej jednego z programów.
- Jeśli nie możesz dotrzeć do serwera konfiguracji z serwera przetwarzania, użyj telnetu, aby sprawdzić łączność z serwerem konfiguracji na porcie 443. Można również spróbować wykonać ping serwera konfiguracji z serwera przetwarzania. Serwer przetwarzania powinien mieć również puls, gdy jest podłączony do serwera konfiguracji.
- Serwer z dodatku SP1 dla systemu Windows Server 2008 R2, który jest chroniony jako fizyczny serwer lokalny, nie może zostać powiódł się z platformy Azure do lokacji lokalnej.
- W następujących okolicznościach nie można cofnąć się w następujących okolicznościach:
    - Zmigrowano maszyny na platformę Azure. [Dowiedz się więcej](migrate-overview.md#what-do-we-mean-by-migration).
    - Maszyna wirtualna została przeniesiona do innej grupy zasobów.
    - Usunięto maszynę wirtualną platformy Azure.
    - Wyłączono ochronę maszyny Wirtualnej.
    - Maszynę wirtualną utworzono ręcznie na platformie Azure. Komputer powinien być początkowo chroniony lokalnie i przejęty awaryjnie na platformie Azure przed reprotection.
    - Można zakończyć niepowodzeniem tylko dla hosta ESXi. Maszyny wirtualne vmware po awarii ani serwery fizyczne nie mogą po awarii do hostów funkcji Hyper-V, komputerów fizycznych ani stacji roboczych VMware.


## <a name="troubleshoot-reprotection-errors"></a>Rozwiązywanie problemów z błędami ponownego ceł

W tej sekcji opisano typowe błędy ponownego ceł i sposób ich poprawiania.

### <a name="error-code-95226"></a>Kod błędu 95226

**Usługa Reprotect nie powiodła się, ponieważ maszyna wirtualna platformy Azure nie mogła dotrzeć do lokalnego serwera konfiguracji.**

Ten błąd występuje, gdy:

* Maszyna wirtualna platformy Azure nie może dotrzeć do lokalnego serwera konfiguracji. Maszyny Wirtualnej nie można odnajdować i zarejestrować na serwerze konfiguracji.
* Usługa aplikacji InMage Scout nie jest uruchomiona na maszynie Wirtualnej platformy Azure po przeczesaniu pracy awaryjnej. Usługa jest potrzebna do komunikacji z lokalnym serwerem konfiguracji.

Aby rozwiązać ten problem:

* Sprawdź, czy sieć maszyn wirtualnych platformy Azure umożliwia maszynie Wirtualnej platformy Azure komunikowanie się z lokalnym serwerem konfiguracji. Można skonfigurować sieć VPN lokacji do lokacji w lokalnym centrum danych lub skonfigurować połączenie Usługi Azure ExpressRoute z prywatną komunikacją równorzędną w sieci wirtualnej maszyny Wirtualnej platformy Azure.
* Jeśli maszyna wirtualna może komunikować się z lokalnym serwerem konfiguracji, zaloguj się do maszyny Wirtualnej. Następnie sprawdź usługę aplikacji InMage Scout. Jeśli widzisz, że nie jest uruchomiona, uruchom usługę ręcznie. Sprawdź, czy typ uruchomienia usługi jest ustawiony na **Automatyczny**.

### <a name="error-code-78052"></a>Kod błędu 78052

**Nie można ukończyć ochrony dla maszyny wirtualnej.**

Ten problem może się zdarzyć, jeśli istnieje już maszyna wirtualna o tej samej nazwie na głównym serwerze docelowym, do którego jesteś w odpowiedzi.

Aby rozwiązać ten problem:

* Wybierz inny główny serwer docelowy na innym hoście, aby reprotection tworzył komputer na innym hoście, gdzie nazwy nie zderzają się.
* Można również użyć vMotion przenieść główny obiekt docelowy do innego hosta, gdzie kolizji nazwy nie nastąpi. Jeśli istniejąca maszyna wirtualna jest bezpańskim komputerem, zmień jej nazwę, tak aby można było utworzyć nową maszynę wirtualną na tym samym hoście ESXi.


### <a name="error-code-78093"></a>Kod błędu 78093

**Maszyna wirtualna nie jest uruchomiona, w stanie zawieszenia lub niedostępne.**

Aby rozwiązać ten problem:

Aby ponownie uruchomić maszynę wirtualną, która uległa awarii, maszyna wirtualna platformy Azure musi być uruchomiona, aby usługa mobilności rejestrowała się na serwerze konfiguracji lokalnie i mogła rozpocząć replikowanie, komunikując się z serwerem przetwarzania. Jeśli komputer znajduje się w niepoprawnej sieci lub nie jest uruchomiony (nie odpowiada lub nie jest zamknięty), serwer konfiguracji nie może dotrzeć do usługi mobilności na maszynie Wirtualnej, aby rozpocząć ponowne odtwarzanie.

* Uruchom ponownie maszynę wirtualną, aby mogła rozpocząć komunikację z powrotem w środowisku lokalnym.
* Uruchom ponownie zadanie ponownego uruchamiania po uruchomieniu maszyny wirtualnej platformy Azure.

### <a name="error-code-8061"></a>Kod błędu 8061

**Magazyn danych nie jest dostępny z hosta ESXi.**

Sprawdź [wymagania wstępne głównego obiektu docelowego i obsługiwane magazyny danych](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) pod kątem powrotu po awarii.


## <a name="troubleshoot-failback-errors"></a>Rozwiązywanie problemów z błędami po awarii

W tej sekcji opisano typowe błędy, które mogą wystąpić podczas powrotu po awarii.

### <a name="error-code-8038"></a>Kod błędu 8038

**Nie można wywołać lokalnej maszyny wirtualnej z powodu błędu.**

Ten problem występuje, gdy lokalna maszyna wirtualna jest obsługiwana na hoście, który nie ma wystarczającej ilości pamięci aprowizowanej. 

Aby rozwiązać ten problem:

* Aprowizować więcej pamięci na hoście ESXi.
* Ponadto można użyć vMotion przenieść maszynę wirtualną do innego hosta ESXi, który ma wystarczającą ilość pamięci do uruchomienia maszyny Wirtualnej.
