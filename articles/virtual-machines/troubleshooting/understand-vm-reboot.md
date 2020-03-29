---
title: Opis ponownego uruchomienia systemu dla maszyny Wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Wyświetla listę zdarzeń, które mogą spowodować ponowne uruchomienie maszyny Wirtualnej
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: e94ffb3d34082745c3d7ca86cfda2b93c0ed08da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919417"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Opis ponownego uruchomienia systemu dla maszyny Wirtualnej platformy Azure

Maszyny wirtualne platformy Azure (maszyny wirtualne) może czasami ponownie uruchomić komputer bez wyraźnego powodu, bez dowodów na zainicjowanie operacji ponownego uruchamiania. W tym artykule wymieniono akcje i zdarzenia, które mogą spowodować ponowne uruchomienie maszyn wirtualnych i zawiera szczegółowe informacje na temat unikania nieoczekiwanych problemów z ponownym uruchomieniem lub zmniejszenia wpływu takich problemów.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurowanie maszyn wirtualnych pod kątem wysokiej dostępności

Najlepszym sposobem ochrony aplikacji uruchomionej na platformie Azure przed ponownym uruchomieniem maszyn wirtualnych i przestojami jest skonfigurowanie maszyn wirtualnych pod kątem wysokiej dostępności.

Aby zapewnić ten poziom nadmiarowości aplikacji, zaleca się pogrupowanie dwóch lub więcej maszyn wirtualnych w zestawie dostępności. Ta konfiguracja zapewnia, że podczas zdarzenia planowanej lub nieplanowanej konserwacji dostępna jest co najmniej jedna maszyna wirtualna i spełniała umowy [SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/)99,95%.

Aby uzyskać więcej informacji o zestawach dostępności, zobacz [Zarządzanie dostępnością maszyn wirtualnych](../windows/manage-availability.md)

## <a name="resource-health-information"></a>Informacje o kondycji zasobów

Usługa Azure Resource Health to usługa, która udostępnia kondycję poszczególnych zasobów platformy Azure i zawiera wskazówki dotyczące rozwiązywania problemów z obsługą. W środowisku chmury, w którym nie można bezpośrednio uzyskać dostępu do serwerów lub elementów infrastruktury, celem kondycji zasobów jest skrócenie czasu spędzanego na rozwiązywaniu problemów. W szczególności celem jest zmniejszenie czasu, który można poświęcić na określenie, czy główny problem leży w aplikacji lub w przypadku wewnątrz platformy Azure platformy. Aby uzyskać więcej informacji, zobacz [Rozumienie i używanie kondycji zasobów](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Akcje i zdarzenia, które mogą spowodować ponowne uruchomienie maszyny Wirtualnej

### <a name="planned-maintenance"></a>Planowana konserwacja

Microsoft Azure okresowo wykonuje aktualizacje na całym świecie, aby zwiększyć niezawodność, wydajność i bezpieczeństwo infrastruktury hosta, która stanowi podstawę maszyn wirtualnych. Wiele z tych aktualizacji, w tym aktualizacje zachowujące pamięć, są wykonywane bez wpływu na maszyny wirtualne lub usługi w chmurze.

Jednak niektóre aktualizacje wymagają ponownego uruchomienia komputera. W takich przypadkach maszyny wirtualne są zamykane podczas poprawki infrastruktury, a następnie maszyny wirtualne są ponownie uruchamiane.

Aby dowiedzieć się, czym jest planowana konserwacja platformy Azure i jak może ona wpłynąć na dostępność maszyn wirtualnych z systemem Linux, zobacz artykuły wymienione tutaj. Artykuły zawierają podstawowe informacje na temat procesu planowanej konserwacji platformy Azure oraz sposobu planowania tej konserwacji, aby jeszcze bardziej ograniczyć jej wpływ.

- [Planowana konserwacja maszyn wirtualnych platformy Azure](../windows/planned-maintenance.md)
- [How to schedule planned maintenance on Azure VMs (Jak zaplanować planowaną konserwację na maszynach wirtualnych platformy Azure)](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Aktualizacje pozwalające zachować stan pamięci

Dla tej klasy aktualizacji na platformie Microsoft Azure użytkownicy nie mają wpływu na ich uruchomione maszyny wirtualne. Wiele z tych aktualizacji dotyczy składników lub usług, które można zaktualizować bez zakłócania pracy uruchomionego wystąpienia. Niektóre z nich są aktualizacje infrastruktury platformy w systemie operacyjnym hosta, które mogą być stosowane bez ponownego uruchomienia maszyn wirtualnych.

Te aktualizacje pozwalające zachować stan pamięci są przeprowadzane za pomocą technologii umożliwiającej migrację na żywo w miejscu. Po zaktualizowaniu maszyna wirtualna jest umieszczana w stanie *wstrzymania.* Pozwala to zachować stan pamięci RAM, gdy w podstawowym systemie operacyjnym hosta wprowadzane są niezbędne aktualizacje i poprawki. Maszyna wirtualna jest wznawiana w ciągu 30 sekund od momentu wstrzymania. Po wznowieniu maszyny wirtualnej jej zegar jest automatycznie synchronizowany.

Ze względu na krótki okres wstrzymania wdrażanie aktualizacji za pośrednictwem tego mechanizmu znacznie zmniejsza wpływ na maszyny wirtualne. Jednak nie wszystkie aktualizacje można wdrożyć w ten sposób. 

Aktualizacje wielu wystąpień (dla maszyn wirtualnych w zestawie dostępności) są stosowane dla jednej domeny aktualizacji jednocześnie.

> [!NOTE]
> Maszyny z systemem Linux, które mają stare wersje jądra, są dotknięte paniką jądra podczas tej metody aktualizacji. Aby uniknąć tego problemu, zaktualizuj jądro w wersji 3.10.0-327.10.1 lub nowszej. Aby uzyskać więcej informacji, zobacz [Maszyna wirtualna systemu Azure z systemem Linux na jądrze opartym na 3.10 po uaktualnieniu węzła hosta](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Zainicjowane przez użytkownika akcje ponownego uruchomienia lub zamknięcia

Jeśli wykonasz ponowne uruchomienie z witryny Azure Portal, Azure PowerShell, interfejsu wiersza polecenia lub interfejsu API REST, zdarzenie można znaleźć w [dzienniku aktywności platformy Azure.](../../azure-monitor/platform/platform-logs-overview.md)

Jeśli wykonujesz akcję z systemu operacyjnego maszyny Wirtualnej, zdarzenie można znaleźć w dziennikach systemowych.

Inne scenariusze, które zwykle powodują ponowne uruchomienie maszyny Wirtualnej obejmują wiele akcji zmiany konfiguracji. Zwykle zobaczysz komunikat ostrzegawczy wskazujący, że wykonanie określonej akcji spowoduje ponowne uruchomienie maszyny Wirtualnej. Przykłady obejmują wszelkie operacje zmiany rozmiaru maszyny Wirtualnej, zmiana hasła konta administracyjnego i ustawienie statycznego adresu IP.

### <a name="azure-security-center-and-windows-update"></a>Centrum zabezpieczeń platformy Azure i usługa Windows Update

Usługa Azure Security Center monitoruje codzienne maszyny wirtualne z systemem Windows i Linux pod kątem brakujących aktualizacji systemu operacyjnego. Usługa Security Center pobiera listę dostępnych zabezpieczeń i aktualizacji krytycznych z usługi Windows Update lub Windows Server Update Services (WSUS), w zależności od tego, która usługa jest skonfigurowana na maszynie Wirtualnej systemu Windows. Usługa Security Center sprawdza również dostępność najnowszych aktualizacji dla systemów Linux. Jeśli na maszynie wirtualnej brakuje aktualizacji systemu, usługa Security Center zaleca zastosowanie aktualizacji systemu. Aplikacja tych aktualizacji systemu jest kontrolowana za pośrednictwem centrum zabezpieczeń w witrynie Azure portal. Po zastosowaniu niektórych aktualizacji może być wymagane ponowne uruchomienie maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Stosowanie aktualizacji systemu w usłudze Azure Security Center](../../security-center/security-center-apply-system-updates.md).

Podobnie jak na serwerach lokalnych, platforma Azure nie wypycha aktualizacji z usługi Windows Update na maszyny wirtualne systemu Windows, ponieważ te maszyny są przeznaczone do zarządzania przez ich użytkowników. Zachęcamy jednak do pozostawienia włączonego ustawienia automatycznej usługi Windows Update. Automatyczna instalacja aktualizacji z witryny Windows Update może również spowodować ponowne uruchomienie po zastosowaniu aktualizacji. Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące usługi Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Inne sytuacje wpływające na dostępność maszyny Wirtualnej

Istnieją inne przypadki, w których platforma Azure może aktywnie zawiesić korzystanie z maszyny Wirtualnej. Będziesz otrzymywać powiadomienia e-mail przed podjęciem tej akcji, więc będziesz mieć szansę na rozwiązanie podstawowych problemów. Przykłady problemów, które wpływają na dostępność maszyn wirtualnych obejmują naruszenia zabezpieczeń i wygaśnięcia metod płatności.

### <a name="host-server-faults"></a>Błędy serwera hosta

Maszyna wirtualna jest hostowana na serwerze fizycznym, który jest uruchomiony wewnątrz centrum danych platformy Azure. Serwer fizyczny uruchamia agenta o nazwie Agent hosta oprócz kilku innych składników platformy Azure. Gdy te składniki oprogramowania platformy Azure na serwerze fizycznym przestają odpowiadać, system monitorowania wyzwala ponowne uruchomienie serwera hosta w celu podjęcia próby odzyskania. Maszyna wirtualna jest zwykle dostępna ponownie w ciągu pięciu minut i nadal działa na tym samym hoście, co poprzednio.

Błędy serwera są zwykle spowodowane awarią sprzętu, taką jak awaria dysku twardego lub dysku SSD. Platforma Azure stale monitoruje te wystąpienia, identyfikuje podstawowe błędy i wprowadza aktualizacje po zaimplementowaniu i przetestowaniu środków zaradczych.

Ponieważ niektóre błędy serwera hosta mogą być specyficzne dla tego serwera, powtarzające się sytuacje ponownego uruchomienia maszyny Wirtualnej mogą zostać poprawione przez ręczne ponowne wdrożenie maszyny Wirtualnej do innego serwera hosta. Tę operację można wyzwolić przy użyciu opcji **ponownego wdrożenia** na stronie szczegółów maszyny Wirtualnej lub przez zatrzymanie i ponowne uruchomienie maszyny Wirtualnej w witrynie Azure portal.

### <a name="auto-recovery"></a>Automatyczne odzyskiwanie

Jeśli serwer hosta nie może ponownie uruchomić komputera z jakiegokolwiek powodu, platforma Azure inicjuje akcję automatycznego odzyskiwania w celu wykreślenia wadliwego serwera hosta z rotacji w celu dalszego zbadania. 

Wszystkie maszyny wirtualne na tym hoście są automatycznie przenoszone do innego, w dobrej kondycji serwera hosta. Ten proces jest zwykle zakończona w ciągu 15 minut. Aby dowiedzieć się więcej o procesie automatycznego odzyskiwania, zobacz [Automatyczne odzyskiwanie maszyn wirtualnych](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Nieplanowana konserwacja

W rzadkich przypadkach zespół operacyjny platformy Azure może być konieczne wykonanie działań konserwacyjnych w celu zapewnienia ogólnej kondycji platformy Azure. To zachowanie może mieć wpływ na dostępność maszyn wirtualnych i zwykle powoduje to taką samą akcję automatycznego odzyskiwania, jak opisano wcześniej.  

Nieplanowana konserwacja obejmuje następujące czynności:

- Pilna defragmentacja węzła
- Pilne aktualizacje przełączników sieciowych

### <a name="vm-crashes"></a>Awaria maszyny Wirtualnej

Maszyny wirtualne mogą zostać ponownie uruchomione z powodu problemów w samej maszynie wirtualnej. Obciążenie lub rola, która jest uruchomiona na maszynie Wirtualnej może wyzwolić sprawdzanie błędów w systemie operacyjnym gościa. Aby uzyskać pomoc w określeniu przyczyny awarii, wyświetl dzienniki systemu i aplikacji dla maszyn wirtualnych systemu Windows i dzienniki szeregowe dla maszyn wirtualnych z systemem Linux.

### <a name="storage-related-forced-shutdowns"></a>Wymuszone zamknięcia związane z magazynem

Maszyny wirtualne na platformie Azure polegają na dyskach wirtualnych dla systemu operacyjnego i magazynu danych, który jest hostowany w infrastrukturze usługi Azure Storage. Za każdym razem, gdy dostępność lub łączność między maszyną wirtualną a skojarzonymi dyskami wirtualnymi jest naruszona przez ponad 120 sekund, platforma Azure wykonuje wymuszone zamknięcie maszyn wirtualnych, aby uniknąć uszkodzenia danych. Maszyny wirtualne są automatycznie włączane po przywróceniu łączności magazynu. 

Czas trwania zamknięcia może być tak krótki, jak pięć minut, ale może być znacznie dłuższy. Poniżej przedstawiono jeden z konkretnych przypadków, który jest skojarzony z wymuszonymi zamknięciami związanymi z magazynem: 

**Przekroczenie limitów we/wy**

Maszyny wirtualne mogą być tymczasowo zamykane, gdy żądania we/wy są stale ograniczane, ponieważ ilość operacji we/wy na sekundę (We/Wy) przekracza limity we/wy dla dysku. (Standardowa pamięć masowa na dysku jest ograniczona do 500 we/wy). Aby rozwiązać ten problem, należy użyć rozkładania dysku lub skonfigurować miejsce do magazynowania wewnątrz maszyny Wirtualnej gościa, w zależności od obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie maszyn wirtualnych platformy Azure w celu zapewnienia optymalnej wydajności magazynu.](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

### <a name="other-incidents"></a>Inne incydenty

W rzadkich przypadkach powszechny problem może dotyczyć wielu serwerów w centrum danych platformy Azure. W przypadku wystąpienia tego problemu zespół platformy Azure wysyła powiadomienia e-mail do subskrypcji, których dotyczy problem. Możesz sprawdzić [pulpit nawigacyjny usługi Azure Service Health](https://azure.microsoft.com/status/) i portal Azure pod kątem stanu bieżących awarii i przeszłych zdarzeń.
