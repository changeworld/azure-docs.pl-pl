---
title: Przygotowywanie maszyn wirtualnych VMware do ponownego wycofywania i powrotu po awarii za pomocą usługi Azure Site Recovery
description: Przygotowanie do powrotu maszyn wirtualnych VMware po przeczesywaniu pracy awaryjnej za pomocą usługi Azure Site Recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257188"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Przygotowanie do ponownego wycofywania i powrotu po awarii maszyn wirtualnych VMware

Po [przeczesywi](site-recovery-failover.md) lokalnych maszynach wirtualnych VMware lub serwerach fizycznych na platformie Azure należy ponownie przesłać proces maszyny wirtualne platformy Azure utworzone po przekroczeniu wersji awaryjnej, tak aby były replikowane z powrotem do lokacji lokalnej. Dzięki replikacji z platformy Azure do lokalnego w miejscu, można następnie po awarii, uruchamiając pracy awaryjnej z platformy Azure do lokalnego, gdy jesteś gotowy.

Przed kontynuowaniem, uzyskać szybki przegląd z tego klipu wideo o tym, jak wrócić po awarii z platformy Azure do witryny lokalnej.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Składniki ponownego wycofywania/powrotu po awarii

Aby można było ponownie przecenić i przywrócić informacje po awarii z platformy Azure, należy wprowadzić szereg składników i ustawień.

**Składnik**| **Szczegóły**
--- | ---
**Lokalny serwer konfiguracji** | Lokalny serwer konfiguracji musi być uruchomiony i połączony z platformą Azure.<br/><br/> Maszyna wirtualna, której nie można przywrócić, musi istnieć w bazie danych serwera konfiguracji. Jeśli awaria wpływa na serwer konfiguracji, przywróć go przy tym samym adresie IP, aby upewnić się, że powrót po awarii działa.<br/><br/>  Jeśli adresy IP zreplikowanych maszyn zostały zachowane w trybie failover, łączność lokacja-lokacja (lub łączność Usługi ExpressRoute) powinna zostać ustanowiona między komputerami maszyn wirtualnych platformy Azure a kartą sieciową po awarii serwera konfiguracji. W przypadku zachowanych adresów IP serwer konfiguracji potrzebuje dwóch kart sieciowych — jednej dla łączności komputera źródłowego i jednej dla łączności po awarii platformy Azure. Pozwala to uniknąć nakładania się zakresów adresów podsieci dla źródła i nie powiodło się w przypadku maszyn wirtualnych.
**Serwer przetwarzania na platformie Azure** | Serwer procesów na platformie Azure jest potrzebny, zanim będzie można wrócić po awarii do lokacji lokalnej.<br/><br/> Serwer przetwarzania odbiera dane z chronionej maszyny Wirtualnej platformy Azure i wysyła je do lokacji lokalnej.<br/><br/> Potrzebujesz sieci o małym opóźnieniu między serwerem przetwarzania a chroniona maszyna wirtualna, dlatego zaleca się wdrożenie serwera przetwarzania na platformie Azure w celu zwiększenia wydajności replikacji.<br/><br/> Aby uzyskać potwierdzenie koncepcji, można użyć lokalnego serwera przetwarzania i usługi ExpressRoute z prywatną komunikacją równorzędną.<br/><br/> Serwer przetwarzania powinien znajdować się w sieci platformy Azure, w której znajduje się maszyna wirtualna po awarii. Serwer przetwarzania musi również mieć możliwość komunikowania się z lokalnym serwerem konfiguracji i głównym serwerem docelowym.
**Oddzielny główny serwer docelowy** | Główny serwer docelowy odbiera dane po awarii i domyślnie główny serwer docelowy systemu Windows jest uruchamiany na lokalnym serwerze konfiguracji.<br/><br/> Główny serwer docelowy może mieć do niego maksymalnie 60 dysków. Maszyny wirtualne, które nie powiodły się, mają więcej niż łącznie 60 dysków lub jeśli nie uda ci się odzyskać dużych woluminów ruchu, utwórz oddzielny serwer docelowy dla powrotu po awarii.<br/><br/> Jeśli maszyny są zbierane do grupy replikacji dla spójności wielu maszyn wirtualnych, maszyny wirtualne muszą być windows lub musi być linux. Dlaczego? Ponieważ wszystkie maszyny wirtualne w grupie replikacji muszą używać tego samego głównego serwera docelowego, a główny serwer docelowy musi mieć ten sam system operacyjny (w tej samej lub wyższej wersji) niż na zreplikowanych komputerach.<br/><br/> Główny serwer docelowy nie powinien mieć żadnych migawek na swoich dyskach, w przeciwnym razie reprotection i powrót po awarii nie będzie działać.<br/><br/> Główny cel nie może mieć parawirtualnego kontrolera SCSI. Kontroler może być tylko kontrolerem logiki LSI. Bez kontrolera logiki LSI reprotection nie powiedzie się.
**Zasady replikacji powrotu w powiększenie zwrotnym** | Aby replikować z powrotem do lokacji lokalnej, należy zasady powrotu po awarii. Ta zasada jest tworzona automatycznie podczas tworzenia zasad replikacji na platformie Azure.<br/><br/> Zasady zostaną automatycznie skojarzone z serwerem konfiguracji. Jest ustawiona na próg punktu odzyskiwania 15 minut, przechowywanie punktu odzyskiwania 24 godzin, a częstotliwość migawek spójna z aplikacją wynosi 60 minut. Zasady nie można edytować. 
**Prywatna komunikacja równorzędna między lokacjami a lokacją/usługi ExpressRoute** | Ponowne wycofywanie i powrót po awarii wymaga połączenia sieci VPN między lokacjami lub prywatnej komunikacji równorzędnej usługi ExpressRoute w celu replikowania danych. 


## <a name="ports-for-reprotectionfailback"></a>Porty do ponownego odtwarzania/powrotu awaryjnego

Wiele portów musi być otwartych dla ponownego wycofywania/powrotu po awarii. Poniższa grafika przedstawia porty i przepływ ponownego odtwarzania/powrotu po awarii.

![Porty pracy awaryjnej i powrotu po awarii](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Wdrażanie serwera przetwarzania na platformie Azure

1. [Konfigurowanie serwera przetwarzania na](vmware-azure-set-up-process-server-azure.md) platformie Azure w celu powrotu po awarii.
2. Upewnij się, że maszyny wirtualne platformy Azure mogą dotrzeć do serwera przetwarzania. 
3. Upewnij się, że połączenie sieci VPN między lokacjami lub prywatna sieć równorzędna usługi ExpressRoute ma wystarczającą przepustowość, aby wysyłać dane z serwera przetwarzania do lokacji lokalnej.

## <a name="deploy-a-separate-master-target-server"></a>Wdrażanie oddzielnego głównego serwera docelowego

1. Zwróć uwagę na [wymagania i ograniczenia](#reprotectionfailback-components)głównego serwera docelowego .
2. Utwórz główny serwer docelowy [systemu Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) lub [Linux,](vmware-azure-install-linux-master-target.md) aby dopasować go do systemu operacyjnego maszyn wirtualnych, które mają zostać ponownie określone i po awarii.
3. Upewnij się, że nie używasz pamięci wirtualnej Magazynu dla głównego serwera docelowego lub powrót po awarii może zakończyć się niepowodzeniem. Nie można uruchomić maszyny wirtualnej, ponieważ dyski nie są dla niego dostępne.
    - Aby temu zapobiec, należy wykluczyć główny serwer docelowy z listy vMotion.
    - Jeśli główny obiekt docelowy zostanie poddany zadaniu vMotion magazynu po odtworzeniu, chronione dyski maszyn wirtualnych dołączone do głównego serwera docelowego zostaną przeniesione do obiektu docelowego zadania vMotion. Jeśli spróbujesz wrócić po awarii po tym, odłączenie dysku kończy się niepowodzeniem, ponieważ dyski nie są znalezione. Następnie trudno jest znaleźć dyski na kontach magazynu. W takim przypadku znajdź je ręcznie i dołącz je do maszyny Wirtualnej. Następnie można uruchomić lokalną maszynę wirtualną.

4. Dodaj dysk przechowywania do istniejącego głównego serwera docelowego systemu Windows. Dodaj nowy dysk i sformatować dysk. Dysk przechowywania służy do zatrzymania punktów w czasie, gdy maszyna wirtualna replikuje z powrotem do lokacji lokalnej. Należy zwrócić uwagę na te kryteria. Jeśli nie są one spełnione, dysk nie jest wymieniony dla głównego serwera docelowego:
    - Wolumin nie jest używany do innych celów, takich jak miejsce docelowe replikacji i nie jest w trybie blokady.
    - Wolumin nie jest woluminem pamięci podręcznej. Niestandardowy wolumin instalacyjny serwera przetwarzania i głównego obiektu docelowego nie kwalifikuje się do woluminu przechowywania. Gdy serwer przetwarzania i główny cel są zainstalowane na woluminie, wolumin jest woluminem pamięci podręcznej głównego obiektu docelowego.
    - Typ systemu plików woluminu nie jest FAT lub FAT32.
    - Pojemność woluminu jest niezerowa.
    - Domyślnym woluminem przechowywania dla systemu Windows jest wolumin R.
    - Domyślny wolumin przechowywania dla systemu Linux to /mnt/retention.

5. Dodaj dysk, jeśli używasz istniejącego serwera przetwarzania. Nowy dysk musi spełniać wymagania w ostatnim kroku. Jeśli dysk przechowywania nie jest obecny, nie pojawia się na liście rozwijanej wyboru w portalu. Po dodaniu dysku do lokalnego obiektu docelowego głównego trwa do 15 minut, aby dysk pojawił się w zaznaczeniu w portalu. Serwer konfiguracji można odświeżyć, jeśli dysk nie pojawi się po 15 minutach.
6. Zainstaluj narzędzia VMware lub narzędzia open-vm na głównym serwerze docelowym. Bez narzędzi nie można wykryć magazynów danych na hoście ESXi obiektu docelowego głównego.
7. Ustaw dysk. EnableUUID=ustawienie true w parametrach konfiguracji głównej maszyny wirtualnej docelowej w VMware. Jeśli ten wiersz nie istnieje, dodaj go. To ustawienie jest wymagane, aby zapewnić spójny identyfikator UUID do VMDK, tak aby został prawidłowo zainstalowany.
8. Sprawdź wymagania dostępu serwera vCenter Server:
    - Jeśli maszyna wirtualna, do której powracasz, znajduje się na hoście ESXi zarządzanym przez vMware vCenter Server, główny serwer docelowy potrzebuje dostępu do lokalnego pliku VMDK (VMDK), aby zapisać zreplikowane dane na dyskach maszyny wirtualnej. Upewnij się, że lokalny magazyn danych maszyny Wirtualnej jest zainstalowany na głównym hoście docelowym z dostępem do odczytu/zapisu.
    - Jeśli maszyna wirtualna nie znajduje się na hoście ESXi zarządzanym przez serwer vCenter VMware, usługa Site Recovery tworzy nową maszynę wirtualną podczas ponownego tworzenia. Ta maszyna wirtualna jest tworzona na hoście ESXi, na którym tworzysz maszynę wirtualną serwera docelowego. Wybierz hosta ESXi ostrożnie, aby utworzyć maszynę wirtualną na hoście, który chcesz. Dysk twardy maszyny wirtualnej musi znajdować się w magazynie danych dostępnym dla hosta, na którym uruchomiony jest główny serwer docelowy.
    - Inną opcją, jeśli lokalna maszyna wirtualna już istnieje dla powrotu po awarii, jest usunięcie go przed dokonaniem powrotu po awarii. Powrót po awarii następnie tworzy nową maszynę wirtualną na tym samym hoście co główny host ESXi docelowy. Po powrocie do lokalizacji alternatywnej dane są odzyskiwane do tego samego magazynu danych i tego samego hosta ESXi, który jest używany przez lokalny serwer docelowy.
9. W przypadku komputerów fizycznych, które nie wróciły do maszyn wirtualnych VMware, należy ukończyć odnajdowanie hosta, na którym jest uruchomiony główny serwer docelowy, zanim będzie można ponownie uruchomić komputer.
10. Sprawdź, czy host ESXi, na którym główna maszyna wirtualna docelowa ma co najmniej jeden magazyn danych systemu plików maszyny wirtualnej (VMFS) dołączony do niego. Jeśli nie są dołączone żadne magazyny danych VMFS, dane wejściowe magazynu danych w ustawieniach ponownego przekazywania jest pusty i nie można kontynuować.


## <a name="next-steps"></a>Następne kroki

[Ponowne rotect](vmware-azure-reprotect.md) maszyny Wirtualnej.
