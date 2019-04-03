---
title: Często zadawane pytania dotyczące usługi Azure Backup
description: 'Odpowiedzi na często zadawane pytania dotyczące: Funkcje usługi Azure Backup w tym usługi Recovery Services magazynów, co ona może tworzyć kopie zapasowe, jak to działa, szyfrowania i limitów. '
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: 0981f4d5d9d5fcb243fc7ead6f4b529c096935d0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58885880"
---
# <a name="azure-backup---frequently-asked-questions"></a>Usługa Azure Backup — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Backup.

## <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Czy istnieje ograniczenie liczby magazynów, które można utworzyć w poszczególnych subskrypcjach platformy Azure?
Tak. Możesz utworzyć maksymalnie 500 magazynów usługi Recovery Services na obsługiwany region usługi Azure Backup na subskrypcję. Jeśli potrzebna jest większa liczba magazynów, należy utworzyć dodatkową subskrypcję.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Czy istnieją ograniczenia dotyczące liczby serwerów/maszyn, które można zarejestrować w każdym magazynie?
Można zarejestrować maksymalnie 1000 maszyn wirtualnych platformy Azure na magazyn. Jeśli używasz agenta usługi Microsoft Azure Backup należy zarejestrować maksymalnie 50 agentów MAB w jednym magazynie. I można zarejestrować 50 MAB serwerów/serwerów programu DPM w magazynie.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Jeśli organizacja ma jeden magazyn, w jaki sposób można odizolować dane z różnych serwerów w magazynie podczas przywracania danych?
Dane serwera, który chcesz odzyskać razem należy używać tego samego hasła podczas konfigurowania kopii zapasowej. Jeśli chcesz izolować odzyskiwania do określonego serwera lub serwerów, należy użyć hasła dla tego serwera lub tylko serwery. Na przykład serwery zarządzania zasobami ludzkimi mogą korzystać z jednego hasła szyfrowania, serwery księgowości z drugiego, a serwery pamięci masowej z trzeciego.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Czy mogę przenieść mój magazynu między subskrypcjami?
Tak. Aby przenieść magazyn usługi Recovery Services można znaleźć to [artykułu](backup-azure-move-recovery-services-vault.md)

### <a name="can-i-move-backup-data-to-another-vault"></a>Czy można przenieść dane kopii zapasowej w innym magazynie?
Nie. Nie można przenieść dane kopii zapasowej, przechowywane w magazynie w innym magazynie.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Można zmienić z GRS na LRS po utworzeniu kopii zapasowej?
Nie. Magazyn usługi Recovery Services można zmienić tylko opcje magazynu, zanim wszystkie kopie zapasowe są przechowywane.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Dla maszyn wirtualnych z kopii zapasowej w magazynie usługi Recovery Services mogą zrobić elementu poziom przywracania (ILR)?
- ILR jest obsługiwana dla maszyn wirtualnych platformy Azure, kopie zapasowe wykonywane kopii zapasowych maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [artykułu](backup-azure-restore-files-from-vm.md)
- ILR nie jest obsługiwane dla punktów odzyskiwania online maszyn wirtualnych w środowisku lokalnym kopii zapasowej przez usługę Azure backup Server lub programu System Center DPM.


## <a name="azure-backup-agent"></a>Agent usługi Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Gdzie mogę znaleźć często zadawane pytania dotyczące agenta usługi Azure Backup do tworzenia kopii zapasowych maszyn wirtualnych platformy Azure

- Dla agenta uruchomionego na maszynach wirtualnych platformy Azure, przeczytaj ten [— często zadawane pytania](backup-azure-vm-backup-faq.md).
- Dla agenta umożliwia tworzenie kopii zapasowej foldery plików platformy Azure, przeczytaj ten artykuł [— często zadawane pytania](backup-azure-file-folder-backup-faq.md).


## <a name="vmware-and-hyper-v-backup"></a>Kopia zapasowa VMware i funkcji Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Czy można tworzyć kopie zapasowe serwerów VMware vCenter na platformie Azure?
Tak. Usługi Azure Backup Server umożliwia tworzenie kopii zapasowej serwera VMware vCenter i hosty ESXi na platformie Azure.

- [Dowiedz się więcej](backup-mabs-protection-matrix.md) o obsługiwanych wersjach.
- [Wykonaj następujące kroki](backup-azure-backup-server-vmware.md) do tworzenia kopii zapasowej serwera VMware.

### <a name="do-i-need-a-separate-license-to-recover-an-full-on-premises-vmwarehyper-v-cluster"></a>Czy muszę mieć oddzielnej licencji, aby odzyskać klaster VMware/funkcji Hyper-V pełnego w środowisku lokalnym?
Nie potrzebujesz oddzielnych licencji na ochronę programu VMware/funkcji Hyper-V.

- Jeśli jesteś klientem programu System Center, należy użyć System Center Data Protection Manager (DPM) do ochrony maszyn wirtualnych VMware.
- Jeśli nie jesteś klientem programu System Center, można użyć usługi Azure Backup Server (płatność za rzeczywiste użycie) do ochrony maszyn wirtualnych VMware.

## <a name="dpm-and-azure-backup-server-backup"></a>Kopia zapasowa programu DPM i Azure Backup Server

### <a name="which-dpm-versions-are-supported"></a>Które wersje programu DPM są obsługiwane?
Obsługiwane wersje programu DPM są podsumowane w [macierz obsługi](backup-azure-dpm-introduction.md#prerequisites-and-limitations). Zalecamy, aby zainstalować najnowsze aktualizacje programu DPM i uruchom [najnowszej wersji](https://aka.ms/azurebackup_agent) agenta usługi Azure Backup na serwerze programu DPM.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Czy można zarejestrować serwer w wielu magazynach?
Nie. Serwer programu DPM lub usługi Azure Backup można zarejestrować tylko w jednym magazynie.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Czy można używać programu Azure Backup Server do tworzenia kopii zapasowej z odzyskiwaniem systemu od zera (BMR) serwera fizycznego? <br/>
Tak.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Czy można użyć programu DPM, aby utworzyć kopię zapasową aplikacji w usłudze Azure Stack?
Nie. Usługa Azure Backup umożliwia ochronę usługi Azure Stack, usługa Azure Backup nie obsługuje przy użyciu programu DPM, aby utworzyć kopię zapasową aplikacji w usłudze Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Jeśli został zainstalowany agent usługi Azure Backup, aby chronić pliki i foldery, można zainstalować programu System Center DPM do tworzenia kopii zapasowych obciążeń lokalnych do platformy Azure?
Tak. Jednak należy najpierw skonfigurować program DPM, a następnie zainstaluj agenta usługi Azure Backup.  Instalowanie składników w tej kolejności zapewnia, że usługi Azure Backup agent działa przy użyciu programu DPM. Instalowanie agenta przed instalacją programu DPM nie jest zalecana lub jest obsługiwana.

## <a name="general-backup"></a>Ogólne kopii zapasowej

### <a name="are-there-limits-on-backup-scheduling"></a>Czy istnieją ograniczenia dotyczące planowania kopii zapasowych?
Tak.
- Utworzeniem kopii zapasowej systemu Windows Server lub Windows maszyny maksymalnie trzy razy dziennie. Można ustawić zasady harmonogramu dzienne lub tygodniowe harmonogramy.
- Można utworzyć kopię zapasową programu DPM maksymalnie dwa razy dziennie. Można ustawić zasady harmonogramu dzienne, tygodniowe, miesięczne i roczne.
- Możesz utworzyć kopię zapasową maszyn wirtualnych platformy Azure raz dziennie.

### <a name="what-operating-systems-are-supported-for-backup"></a>Jakie systemy operacyjne są obsługiwane dla kopii zapasowej?
Usługa Azure Backup obsługuje następujące systemy operacyjne do wykonywania kopii zapasowych plików i folderów oraz aplikacji chronionych przez usługi Azure Backup Server i programu DPM.

**System operacyjny** | **SKU** | **Szczegóły**
--- | --- | ---
Stacja robocza | |
System Windows 10 64-bitowy | Enterprise, Pro, Home | Powinna być uruchomiona maszyn, najnowsze pakiety usług i aktualizacje.
Windows 8.1 64-bitowy | Enterprise, Pro | Powinna być uruchomiona maszyn, najnowsze pakiety usług i aktualizacje.
Windows 8 64-bitowy | Enterprise, Pro | Powinna być uruchomiona maszyn, najnowsze pakiety usług i aktualizacje.
Windows 7 64-bitowy | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Powinna być uruchomiona maszyn, najnowsze pakiety usług i aktualizacje.
Serwer | |
Windows Server 2019 64 bitowych | Standard, Datacenter, Essentials | Przy użyciu najnowszych pakietów usługi/aktualizacji.
Windows Server 2016 w wersji 64-bitowych | Standard, Datacenter, Essentials | Przy użyciu najnowszych pakietów usługi/aktualizacji.
Windows Server 2012 R2 64-bitowy | Standard, Datacenter, Foundation | Przy użyciu najnowszych pakietów usługi/aktualizacji.
Windows Server 2012 64 bit | Datacenter, Foundation, Standard | Przy użyciu najnowszych pakietów usługi/aktualizacji.
Windows Storage Server 2016 64 bit | Standard, Workgroup | Przy użyciu najnowszych pakietów usługi/aktualizacji.
Windows Storage Server 2012 R2 64 bit | Wersje Standard, grupy roboczej, Essential | Przy użyciu najnowszych pakietów usługi/aktualizacji.
Windows Storage Server 2012 64 bit | Standard, Workgroup | Przy użyciu najnowszych pakietów usługi/aktualizacji.
Windows Server 2008 R2 z dodatkiem SP1 64-bitowy | Standard, Enterprise, Datacenter, Foundation | Z najnowszymi aktualizacjami.
Windows Server 2008 w wersji 64-bitowych | Standard, Enterprise i Datacenter | Z najnowszymi aktualizacjami.

Kopii zapasowych maszyn wirtualnych platformy Azure w systemie Linux obsługuje usługę Azure Backup [listę dystrybucje zalecane dla platformy Azure](../virtual-machines/linux/endorsed-distros.md), z wyjątkiem systemu operacyjnego Linux Core i 32-bitowym systemie operacyjnym. Inne dystrybucje systemu Linux bring your own mogą pracować tak długo, jak agent maszyny Wirtualnej jest dostępny na maszynie Wirtualnej i obsługiwany język Python.


### <a name="are-there-size-limits-for-data-backup"></a>Czy istnieją ograniczenia rozmiaru danych kopii zapasowej?
Limity rozmiarów są następujące:

System operacyjny/machine | Limit rozmiaru źródła danych
--- | --- 
Windows 8 lub nowszy | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 lub nowszy | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Maszyna wirtualna platformy Azure | dyski z danymi 16<br/><br/> Do 4095 GB na dysku danych

### <a name="how-is-the-data-source-size-determined"></a>W jaki sposób określić rozmiar źródła danych?
W poniższej tabeli opisano sposób ustalania rozmiaru dla każdego źródła danych.

**Źródło danych** | **Szczegóły**
--- | ---
Wolumin |Ilość danych kopii zapasowej z jednego woluminu tworzona kopia zapasowa maszyny Wirtualnej.
Baza danych programu SQL Server |Rozmiar pojedynczego rozmiar bazy danych SQL Trwa wykonywanie kopii zapasowej.
Sharepoint | Suma zawartości i konfiguracji bazy danych w farmie programu SharePoint, w których powstaje kopia zapasowa.
Exchange |Suma wszystkich baz danych programu Exchange w serwerze programu Exchange, w których powstaje kopia zapasowa.
Stan systemu/BMR |Każda pojedyncza kopia BMR lub stanu systemu komputera, w których powstaje kopia zapasowa.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Czy istnieje ograniczenie na ilość danych kopii zapasowej przy użyciu magazynu usługi Recovery Services?
Nie ma żadnego limitu ilości danych, które można utworzyć kopię zapasową przy użyciu magazynu usługi Recovery Services.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Dlaczego rozmiar danych przesyłanych do magazynu usługi Recovery Services mniejszy niż wybranych do utworzenia kopii zapasowej danych?
Dane kopii zapasowej z agenta usługi Azure Backup, program DPM, a serwer usługi Azure Backup jest kompresowane i szyfrowane przed przesłaniem. Dzięki kompresji i szyfrowania jest stosowany, dane w magazynie jest 30 – 40% mniejsze.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Czy mogę usunąć pojedyncze pliki z punktu odzyskiwania w magazynie
Nie, usługa Azure Backup nie obsługuje usuwania lub czyszczenia poszczególne elementy z kopii zapasowych przechowywanych.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Czy w przypadku anulowania zadania tworzenia kopii zapasowej po jego uruchomieniu, zostanie usunięty przeniesione dane kopii zapasowej?
Nie. Wszystkie dane, które zostało przesłane do magazynu, zanim zadanie tworzenia kopii zapasowej zostało anulowane pozostaje w magazynie.

- Usługa Azure Backup używa mechanizmu, który podczas tworzenia kopii zapasowej co pewien czas dodaje punkty kontrolne do danych kopii zapasowej.
- Ponieważ w danych kopii zapasowej umieszczone są punkty kontrolne, następny proces kopii zapasowej może sprawdzić integralność plików.
- Następnym zadaniem kopii zapasowej będzie przyrostowa kopia zapasowa tworzona w oparciu o wcześniej utworzoną kopię zapasową danych. Przyrostowe kopie zapasowe przesyłają tylko nowe lub zmienione dane, dzięki czemu zapewnia się lepsze wykorzystanie przepustowości.

Jeśli anulujesz zadanie kopii zapasowej dla maszyny wirtualnej platformy Azure, wszelkie przesłane dane zostaną zignorowane. Następne zadanie kopii zapasowej przesyła przyrostowe dane z ostatniego wykonanego zadania kopii zapasowej.

## <a name="retention-and-recovery"></a>Przechowywanie i odzyskiwanie

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Zasady przechowywania dla programu DPM i Windows maszyny bez programu DPM są takie same?
Tak, oba muszą zasady przechowywania dzienne, tygodniowe, miesięczne i roczne.

### <a name="can-i-customize-retention-policies"></a>Czy można dostosować zasady przechowywania?
Tak, ma dostosowania zasad. Na przykład można skonfigurować, co tydzień codziennie przechowywania, ale nie corocznej oraz wymagania co miesiąc.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Czy można używać różnych dniach, dla kopii zapasowych, planowanie i zasady przechowywania?
Nie. Zasady przechowywania mogą być stosowane wyłącznie w punktach kopii zapasowej. Na przykład ten obrazów przedstawiono zasady przechowywania dla kopii zapasowych wykonanych o 12: 00 i 18: 00.

![Harmonogram tworzenia kopii zapasowej i przechowywania](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Jeśli kopia zapasowa jest przechowywana przez długi czas, zajmuje więcej czasu na odzyskiwanie z wcześniejszego punktu danych? <br/>
Nie. Czas odzyskania najstarszego i najnowszego punktu jest taki sam. Każdy punkt odzyskiwania zachowuje się jak pełny punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Skoro każdy punkt odzyskiwania jest traktowany jak pełny punkt, czy ma to wpływ na całkowitą fakturowaną przestrzeń dyskową dla kopii zapasowych?
Typowe produkty punktów długoterminowego przechowywania przechowują dane kopii zapasowych jako pełne punkty.

    - Pełne punkty są *nieefektywne* z punktu widzenia magazynu, ale są łatwiejsze i szybsze do przywrócenia.
    - Przyrostowe kopie są magazynu *wydajne* , ale wymagają przywrócenia łańcucha danych, co ma wpływ na czas odzyskiwania

Architektura magazynu usługi Azure Backup oferuje zalety obu rozwiązań dzięki optymalnemu przechowywaniu danych, umożliwiającemu szybkie ich przywrócenie, oraz niskim kosztom magazynowania. Zapewnia to efektywne wykorzystanie przepustowości ruchu przychodzącego i wychodzącego. Ilość pamięci masowej i czas potrzebny do odzyskania danych jest ograniczone do minimum. Dowiedz się więcej o [przyrostowe kopie zapasowe](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Czy istnieje ograniczenie liczby punktów odzyskiwania, które można utworzyć?
Możesz utworzyć maksymalnie 9999 punktów odzyskiwania na każde pojedyncze chronione wystąpienie. Chronione wystąpienie to komputer, serwer (fizyczny lub wirtualny) lub obciążenia, który tworzy kopie zapasowe na platformie Azure.

- Dowiedz się więcej o [i przechowywania kopii zapasowych](./backup-introduction-to-azure-backup.md#backup-and-retention).
- Dowiedz się więcej o [chronionego wystąpienia](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)?

### <a name="how-many-times-can-i-recovery-data-thats-backed-up-to-azure"></a>Ile razy mogę dane odzyskiwania, która jest wykonywana kopia zapasowa na platformie Azure?
Nie ma ograniczenia liczby operacji odzyskiwania z usługi Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Czy podczas przywracania danych należy zapłacić za ruch wychodzący z platformy Azure?
Nie. Odzyskiwanie jest dostępny bezpłatnie i nie są pobierane za ruch wychodzący.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co się stanie, gdy zmienią się Moje zasad tworzenia kopii zapasowej?
Po zastosowaniu nowych zasad, harmonogram i okres przechowywania nowych zasad jest zakończony.

- Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami.
- W przypadku skrócenia okresu przechowywania zostaną one oznaczone do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usunięte.

## <a name="encryption"></a>Szyfrowanie

### <a name="is-the-data-sent-to-azure-encrypted"></a>Czy dane wysyłane do platformy Azure są szyfrowane?
Tak. Dane są szyfrowane na maszynie lokalnej przy użyciu AES256. Dane są przesyłane za pośrednictwem bezpiecznego linku HTTPS. Dane przesyłane w chmurze jest chroniony przez łącza HTTPS tylko między magazynu i przywracania usługi. Protokół iSCSI zabezpiecza dane przesyłane między maszyny odzyskiwania usługi i użytkownika. Bezpiecznego tunelowania jest używany do ochrony kanału iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Czy dane kopii zapasowej na platformie Azure są również szyfrowane?
Tak. Dane na platformie Azure są szyfrowane w spoczynku.

- Dla lokalnej kopii zapasowej szyfrowanie w spoczynku znajduje się przy użyciu hasła, podane podczas tworzenia kopii zapasowej na platformie Azure.
- W przypadku maszyn wirtualnych platformy Azure dane są szyfrowane w spoczynku przy użyciu szyfrowania usługi Storage (SSE).

Firma Microsoft nie odszyfrowuje danych kopii zapasowej w żadnym punkcie.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Jaka jest minimalna długość szyfrowania klucz używany do szyfrowania danych kopii zapasowej?
Klucz szyfrowania powinien składać się z co najmniej 16 znaków, gdy używasz agenta usługi Azure Backup. W przypadku maszyn wirtualnych platformy Azure nie ma limitu długości kluczy używanych przez usługę Azure KeyVault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Co się stanie, jeśli klucz szyfrowania zostanie zgubiony przez użytkownika? Czy można odzyskać dane? Microsoft może odzyskać dane?
Klucz używany do szyfrowania danych kopii zapasowej jest obecna tylko w witrynie. Firma Microsoft nie przechowuje kopii klucza na platformie Azure i nie ma do niego dostępu. Jeśli użytkownik zostanie zgubiony przez użytkownika klucza, Microsoft nie może odzyskać danych kopii zapasowej.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj inne — często zadawane pytania:

- [Często zadawane pytania dotyczące](backup-azure-vm-backup-faq.md) dotyczące kopii zapasowych maszyn wirtualnych platformy Azure.
- [Często zadawane pytania dotyczące](backup-azure-file-folder-backup-faq.md) dotyczące agenta usługi Azure Backup
