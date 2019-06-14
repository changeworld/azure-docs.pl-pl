---
title: Omówienie ponownego uruchamiania systemu na Maszynie wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Wyświetla listę zdarzeń, które mogą spowodować ponowne uruchomienie maszyny Wirtualnej
services: virtual-machines
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 70a6845349b90cf614a84e13680ebb6fc6b3e2a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60443759"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Omówienie ponownego uruchamiania systemu dla maszyny Wirtualnej platformy Azure

Maszyn wirtualnych (VM) może czasami ponowne uruchomienie nie widocznej przyczyny bez dowód swojej, posiadające zainicjował operację ponownego uruchomienia. W tym artykule wymieniono działania i zdarzeń, które mogą spowodować ponowne uruchomienie maszyn wirtualnych i umożliwia analizę sposobu uniknąć problemów nieoczekiwane ponowne uruchomienie komputera lub ograniczyć wpływ takich problemów.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurowanie maszyn wirtualnych w celu zapewnienia wysokiej dostępności

Najlepszym sposobem na ochronę aplikacji, która działa na platformie Azure maszynę Wirtualną pod kątem rozruchu i przestój trwa do konfiguracji maszyn wirtualnych w celu zapewnienia wysokiej dostępności.

Aby zapewnić ten poziom nadmiarowości aplikacji, zalecamy zgrupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Ta konfiguracja gwarantuje, że podczas każdej planowanego lub nieplanowanego zdarzenia konserwacji, co najmniej jedna maszyna wirtualna jest dostępna i spełnia dostępności wynoszącej 99,95 procent [umowy SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Aby uzyskać więcej informacji na temat zestawów dostępności zobacz następujące artykuły:

- [Zarządzanie dostępnością maszyn wirtualnych](../windows/manage-availability.md)
- [Konfigurowanie dostępności maszyn wirtualnych](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Informacje o kondycji zasobu

Kondycja zasobów Azure to usługa, która przedstawia kondycję poszczególnych zasobów platformy Azure i zapewnia wiarygodne wskazówki dotyczące rozwiązywania problemów. W środowisku chmury, których nie można bezpośrednio uzyskać dostęp do serwerów lub elementy infrastruktury celem Resource Health jest skrócić czas poświęcany na temat rozwiązywania problemów. W szczególności celem jest skrócenie czasu, który możesz wydać określająca, czy źródło problemu, znajduje się w aplikacji lub w zdarzeniu na platformie Azure. Aby uzyskać więcej informacji, zobacz [poznawanie i używanie Resource Health](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Akcje i zdarzenia, które mogą spowodować ponowne uruchomienie maszyny Wirtualnej

### <a name="planned-maintenance"></a>Planowana konserwacja

Microsoft Azure wykonuje okresowe aktualizacje na całym świecie w celu poprawy niezawodności, wydajności i bezpieczeństwa infrastruktury hosta, która jest podporządkowana narzędziu maszyn wirtualnych. Większość tych aktualizacji, w tym chroniących pamięć aktualizacjach, jest przeprowadzana bez żadnego wpływu na maszynach wirtualnych lub usług w chmurze.

Jednak niektóre aktualizacje wymagają ponownego uruchomienia systemu. W takich przypadkach maszyny wirtualne są zamknięte podczas stosowania poprawek infrastruktury, a następnie zostaną ponownie uruchomione maszyny wirtualne.

Aby zrozumieć, jakie planowana konserwacja platformy Azure i jak go wpływa na dostępność maszyn wirtualnych systemu Linux, zobacz artykuły wymienione w tym miejscu. Artykuły zawierają podstawowe informacje na temat procesu planowanej konserwacji platformy Azure oraz sposobu planowania tej konserwacji, aby jeszcze bardziej ograniczyć jej wpływ.

- [Planowana konserwacja maszyn wirtualnych platformy Azure](../windows/planned-maintenance.md)
- [How to schedule planned maintenance on Azure VMs (Jak zaplanować planowaną konserwację na maszynach wirtualnych platformy Azure)](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Aktualizacje pozwalające zachować stan pamięci

Dla tej klasy aktualizacji w systemie Microsoft Azure są osiągane bez wpływu na ich działających maszyn wirtualnych. Wiele z tych aktualizacji dotyczy składników lub usług, które można zaktualizować bez zakłócania pracy uruchomionego wystąpienia. Niektóre są aktualizacji infrastruktury platformy w systemie operacyjnym hosta, które mogą być stosowane bez ponownego uruchomienia maszyn wirtualnych.

Te aktualizacje pozwalające zachować stan pamięci są przeprowadzane za pomocą technologii umożliwiającej migrację na żywo w miejscu. Gdy jest on aktualizowany, maszyna wirtualna jest umieszczana w *wstrzymana* stanu. Pozwala to zachować stan pamięci RAM, gdy w podstawowym systemie operacyjnym hosta wprowadzane są niezbędne aktualizacje i poprawki. Maszyna wirtualna jest wznawiana w ciągu 30 sekund od momentu wstrzymania. Po wznowieniu maszyny wirtualnej jej zegar jest automatycznie synchronizowany.

Ze względu na krótki czas trwania przerwy wdrażania aktualizacji za pomocą tego mechanizmu znacznie zmniejsza wpływ na maszynach wirtualnych. Jednak nie wszystkie aktualizacje można wdrożyć w ten sposób. 

Aktualizacje wielu wystąpień (dla maszyn wirtualnych w zestawie dostępności) są stosowane dla jednej domeny aktualizacji jednocześnie.

> [!NOTE]
> Maszyny z systemem Linux, które mają starsze wersje jądra są zagrożone jądra podczas tej metody aktualizacji. Aby uniknąć tego problemu, należy zaktualizować 3.10.0-327.10.1 wersja jądra lub nowszej. Aby uzyskać więcej informacji, zobacz [rozruchem maszyny Wirtualnej systemu Linux platformy Azure na podstawie 3.10 jądra po uaktualnieniu węzła hosta](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Akcje ponownego rozruchu lub zamknięcia zainicjowanego przez użytkownika

Jeśli ponowne uruchomienie jest wykonywane z witryny Azure portal, programu Azure PowerShell, interfejsu wiersza polecenia lub interfejsu API REST, można znaleźć zdarzenia w [dziennika aktywności platformy Azure](../../azure-monitor/platform/activity-logs-overview.md).

Po wykonaniu akcji z systemu operacyjnego maszyny Wirtualnej można znaleźć zdarzenia w dziennikach systemu.

Inne scenariusze, które zazwyczaj powodują ponowne uruchomienie maszyny Wirtualnej obejmują wiele akcji zmiany konfiguracji. Zazwyczaj zobaczysz komunikat ostrzegawczy wskazujący, które wykonuje określoną czynność spowoduje ponowne uruchomienie maszyny wirtualnej. Przykłady obejmują czynnościom zmiany rozmiaru maszyny Wirtualnej: Zmienianie hasła konta administratora i Ustawianie statycznego adresu IP.

### <a name="azure-security-center-and-windows-update"></a>Usługa Azure Security Center i Windows Update

Usługa Azure Security Center monitoruje codziennie Windows i maszyn wirtualnych systemu Linux dla brakujących aktualizacji systemu operacyjnego. Usługa Security Center pobiera listę dostępnych zabezpieczeń i aktualizacje krytyczne z witryny Windows Update lub Windows Server Update Services (WSUS) w zależności od którego usługa jest skonfigurowana na maszyny Wirtualnej z systemem Windows. Usługa Security Center sprawdza również uzyskać najnowsze aktualizacje dla systemów Linux. Jeśli maszyna wirtualna jest Brak aktualizacji systemu, usługa Security Center zaleca się stosowanie aktualizacji systemu. Zastosowanie tych aktualizacji systemu jest kontrolowana za pomocą usługi Security Center w witrynie Azure portal. Po zastosowaniu niektóre aktualizacje ponownych rozruchów maszyn wirtualnych może być wymagane. Aby uzyskać więcej informacji, zobacz [Zastosuj aktualizacje systemu w usłudze Azure Security Center](../../security-center/security-center-apply-system-updates.md).

Na serwerach lokalnych, np. Azure nie wypychanie aktualizacji z witryny Windows Update do maszyn wirtualnych Windows, ponieważ te maszyny mają być zarządzane przez ich użytkowników. Jesteś, jednak zaleca się, aby Pozostaw włączone automatyczne ustawienie Windows Update. Automatyczne instalowanie aktualizacji z witryny Windows Update powodują ponowne uruchamianie po zastosowaniu aktualizacji. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące programu Windows Update](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Innych sytuacjach wpływu na dostępność maszyny Wirtualnej

Istnieją inne przypadki, w których Azure aktywnie może zawiesić korzystanie z maszyny Wirtualnej. Będzie otrzymywał powiadomienia e-mail przed podjęciem tej akcji, dzięki czemu będziesz mieć możliwość rozwiązania problemów w podstawowej. Przykłady problemów, które mają wpływ na dostępność maszyny Wirtualnej naruszenia zabezpieczeń i wygaśnięcie formy płatności.

### <a name="host-server-faults"></a>Błędy serwera hosta

Maszyna wirtualna jest hostowana na serwerze fizycznym, który działa wewnątrz centrum danych platformy Azure. Serwer fizyczny uruchamia agenta o nazwie agenta hosta, oprócz kilka składników platformy Azure. Jeśli te składniki oprogramowania platformy Azure na serwerze fizycznym przestanie odpowiadać, system monitorowania wyzwala ponowne uruchomienie serwera hosta, aby spróbować odzyskiwania. Maszyna wirtualna jest zwykle dostępne ponownie w ciągu pięciu minut i na żywo na tym samym hoście, jak poprzednio w dalszym ciągu.

Błędy serwera są zazwyczaj spowodowane awarii sprzętu, takich jak awaria dysku twardego lub dysku SSD. Azure stale monitoruje tych wystąpień, identyfikuje błędy podstawowych i wdraża aktualizacje po ukończeniu zaimplementować i przetestować środki zaradcze.

Ponieważ niektóre błędy serwera hosta mogą być specyficzne dla tego serwera, powtarzanych sytuacji ponowny rozruch maszyny Wirtualnej można zwiększyć przez ręczne ponowne wdrożenie maszyny Wirtualnej do innego serwera hosta. Ta operacja może być uruchamiane przy użyciu **ponownie wdrożyć** opcja na stronie szczegółów maszyny wirtualnej lub zatrzymywania i ponownego uruchamiania maszyny Wirtualnej w witrynie Azure portal.

### <a name="auto-recovery"></a>Automatycznego odzyskiwania

Jeśli serwer hosta nie można ponownie uruchomić jakiegokolwiek powodu, platformy Azure powoduje zainicjowanie akcji automatycznego odzyskiwania Przełącz serwer hosta wadliwe z rotacji w celu bliższego zbadania problemu. 

Wszystkie maszyny wirtualne na tym hoście są automatycznie przeniesiona na serwer hosta różnych, dobrej kondycji. Ten proces jest zwykle ukończone w ciągu 15 minut. Aby dowiedzieć się więcej na temat procesu automatycznego odzyskiwania, zobacz [automatyczne odzyskiwanie maszyn wirtualnych](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines).

### <a name="unplanned-maintenance"></a>Nieplanowana konserwacja

W rzadkich przypadkach zespół operacji na platformie Azure może być konieczne wykonanie czynności konserwacyjnych w celu zapewnienia dobrej ogólnej kondycji platformy Azure. To zachowanie może wpłynąć na dostępność maszyn wirtualnych i zwykle powoduje ona to ta sama akcja automatycznego odzyskiwania zgodnie z wcześniejszym opisem.  

Nieplanowana konserwacja są następujące:

- Defragmentacja pilne węzła
- Aktualizacje przełącznik sieci pilnych

### <a name="vm-crashes"></a>Awaria maszyny Wirtualnej

Maszyny wirtualne może uruchamiać się ponownie z powodu problemów z poziomu samej maszyny Wirtualnej. Obciążenie, czy rola, która jest uruchomiona na maszynie Wirtualnej może wyzwolić sprawdzanie błędów w systemie operacyjnym gościa. Pomoc dotyczącą określania przyczynę awarii wyświetlić systemu, dzienniki aplikacji w przypadku maszyn wirtualnych Windows i szeregowe dzienniki dla maszyn wirtualnych systemu Linux.

### <a name="storage-related-forced-shutdowns"></a>Związane z magazynowaniem wymuszenie zamknięcia systemu

Maszyny wirtualne na platformie Azure korzystają z dysków wirtualnych dla systemu operacyjnego i magazynu danych, który znajduje się w infrastrukturze usługi Azure Storage. Zawsze, gdy dostępności lub połączenie między maszyną Wirtualną i powiązanymi dyskami wirtualnymi ma wpływ na więcej niż 120 sekund, platforma Azure przeprowadza wymuszone zamknięcie maszyny wirtualne, aby uniknąć uszkodzenia danych. Maszyny wirtualne są automatycznie ponownie włączona po przywróceniu łączności z magazynu. 

Czas trwania zamknięcie może składać się z pięciu minut, ale może być znacznie dłuższe. Oto jeden z określonych przypadków, które jest skojarzone z związane z magazynowaniem wymuszenie zamknięcia systemu: 

**We/Wy przekroczenie limitów**

Maszyny wirtualne mogą być tymczasowo zamknięcie żądań We/Wy są zawsze ograniczone, ponieważ liczby operacji We/Wy na sekundę (IOPS) przekracza limit operacji We/Wy dysku. (Magazyn dysków w warstwie standardowa jest ograniczona do 500 operacji We/Wy). Aby rozwiązać ten problem, należy użyć rozkładanie lub skonfigurować ilość miejsca do magazynowania w systemie gościa maszyny Wirtualnej, w zależności od obciążenia. Aby uzyskać więcej informacji, zobacz [konfigurowania maszyn wirtualnych platformy Azure pod kątem optymalnej wydajności magazynu](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx).

### <a name="other-incidents"></a>Inne zdarzenia

W rzadkich przypadkach to powszechne problem może mieć wpływ na wiele serwerów w centrum danych platformy Azure. Jeśli ten problem wystąpi, zespół platformy Azure wysyła powiadomienia e-mail do których to dotyczy subskrypcji. Możesz sprawdzić [pulpit nawigacyjny kondycji usługi platformy Azure](https://azure.microsoft.com/status/) i witrynie Azure portal stan trwającej awarii i zdarzenia przeszłe.
