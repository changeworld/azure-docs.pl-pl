---
title: Często zadawane pytania dotyczące usługi Azure Backup
description: 'Odpowiedzi na typowe pytania dotyczące funkcji usługi Azure Backup, w tym magazynów usług Recovery Services, elementów, których kopie zapasowe można tworzyć, sposobu działania, szyfrowania i ograniczeń. '
services: backup
author: markgalioto
manager: carmonm
keywords: tworzenie kopii zapasowej i odzyskiwanie po awarii; usługa kopii zapasowej
ms.service: backup
ms.topic: conceptual
ms.date: 8/2/2018
ms.author: markgal
ms.openlocfilehash: aa26106460f305568fcb2dd1b84fbc54370e5f39
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543581"
---
# <a name="questions-about-the-azure-backup-service"></a>Pytania dotyczące usługi Azure Backup
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące składników usługi Azure Backup. W niektórych odpowiedziach znajdują się linki do artykułów zawierających szczegółowe informacje. Aby zadać pytanie dotyczące usługi Azure Backup, kliknij pozycję **Komentarze** (po prawej stronie). Komentarze są wyświetlane na dole tego artykułu. Aby komentować, musisz mieć konto Livefyre. Pytania dotyczące usługi Azure Backup można również zadawać na [forum dyskusyjnym](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Aby szybko przeskanować sekcje znajdujące się w tym artykule, użyj linków z prawej strony w obszarze **W tym artykule**.


## <a name="recovery-services-vault"></a>Magazyn usługi Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Czy istnieje ograniczenie liczby magazynów, które można utworzyć w poszczególnych subskrypcjach platformy Azure? <br/>
Tak. Możesz utworzyć maksymalnie 500 magazynów usługi Recovery Services na obsługiwany region usługi Azure Backup na subskrypcję. Jeśli potrzebna jest większa liczba magazynów, należy utworzyć dodatkową subskrypcję.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Czy istnieją ograniczenia dotyczące liczby serwerów/maszyn, które można zarejestrować w każdym magazynie? <br/>
Można zarejestrować maksymalnie 1000 maszynach wirtualnych platformy Azure na magazyn. Jeśli używasz agenta MAB, można zarejestrować maksymalnie 50 MAB agentów na magazyn. I można zarejestrować 50 MAB serwerów/serwerów programu DPM w magazynie.

### <a name="can-i-use-a-rest-api-to-query-the-size-of-protected-items-in-a-vault-br"></a>Czy można użyć interfejsu API REST do wykonywania zapytań rozmiar chronionych elementów w magazynie? <br/>
Tak, artykuł [użycia — listy za magazyny](https://t.co/2lgIrIaF0J), wyświetla informacje, które można uzyskać z magazynu usługi Recovery Services.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Jeśli organizacja ma jeden magazyn, to w jaki sposób można odizolować dane z jednego serwera od danych z innego serwera podczas przywracania danych?<br/>
Wszystkie serwery, które są zarejestrowane w tym samym magazynie, mogą odzyskać dane umieszczone w kopii zapasowej przez inne serwery, *które korzystają z tego samego hasła*. Jeśli dane kopii zapasowej pewnych serwerów mają zostać odizolowane od innych serwerów w organizacji, należy użyć w przypadku tych serwerów innego hasła. Na przykład serwery zarządzania zasobami ludzkimi mogą korzystać z jednego hasła szyfrowania, serwery księgowości z drugiego, a serwery pamięci masowej z trzeciego.

### <a name="can-i-migrate-my-vault-between-subscriptions-br"></a>Czy mogę zmigrować Moje magazynu między subskrypcjami? <br/>
Nie. Magazyn jest tworzone na poziomie subskrypcji i nie może zostać przypisany do innej subskrypcji.

### <a name="can-i-migrate-backup-data-to-another-vault-br"></a>Czy można migrować dane kopii zapasowej w innym magazynie? <br/>
Nie. Nie można przenieść dane kopii zapasowej, przechowywane w magazynie w innym magazynie.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup-br"></a>Można zmienić z GRS na LRS po utworzeniu kopii zapasowej? <br/>
Nie. Magazyn usługi Recovery Services można zmienić tylko opcje magazynu, zanim wszystkie kopie zapasowe są przechowywane. 

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-still-supported-br"></a>Magazyny usług Recovery Services są oparte na usłudze Resource Manager. Czy magazyny kopii zapasowych są nadal obsługiwane? <br/>
Magazyny kopii zapasowych zostały przekonwertowane na magazynów usługi Recovery Services. Jeśli nie zostały przekonwertowane magazynu kopii zapasowych w magazynie usługi Recovery Services, magazynu kopii zapasowych został przekonwertowany w magazynie usługi Recovery Services dla Ciebie. 

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Czy mogę przeprowadzić migrację magazynu usługi Backup do magazynu usługi Recovery Services? <br/>
Wszystkie magazyny kopii zapasowych zostały przekonwertowane na magazynów usługi Recovery Services. Jeśli nie zostały przekonwertowane magazynu kopii zapasowych w magazynie usługi Recovery Services, magazynu kopii zapasowych został przekonwertowany w magazynie usługi Recovery Services dla Ciebie.

## <a name="azure-backup-agent"></a>Agent usługi Azure Backup
Szczegółową listę pytań możesz znaleźć w temacie [FAQ on Azure file-folder backup (Często zadawane pytania dotyczące kopii zapasowych folderów plików na platformie Azure)](backup-azure-file-folder-backup-faq.md)

## <a name="azure-vm-backup"></a>Kopia zapasowa maszyny wirtualnej platformy Azure
Szczegółową listę pytań możesz znaleźć w temacie [FAQ on Azure VM backup (Często zadawane pytania dotyczące kopii zapasowych maszyn wirtualnych platformy Azure)](backup-azure-vm-backup-faq.md)

## <a name="back-up-vmware-servers"></a>Tworzenie kopii zapasowych serwerów VMware

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Czy można tworzyć kopie zapasowe serwerów VMware vCenter na platformie Azure?

Tak. Użyj usługi Azure Backup Server, aby utworzyć kopie zapasowe serwerów VMware vCenter i ESXi na platformie Azure. Informacje na temat obsługiwanych wersji oprogramowania VMware można znaleźć w artykule [Azure Backup Server protection matrix](backup-mabs-protection-matrix.md) (Zestawienie elementów chronionych przez usługę Azure Backup Server). Szczegółowe instrukcje zawiera temat [Use Azure Backup Server to back up a VMware server](backup-azure-backup-server-vmware.md) (Wykonywanie kopii zapasowej serwera VMware przy użyciu usługi Azure Backup Server).

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster-from-dpm-or-azure-backup-serverbr"></a>Czy muszę mieć oddzielnej licencji, aby odzyskać pełną lokalny klaster VMware/funkcji Hyper-V z programu DPM ani usługi Azure Backup Server?<br/>
Nie potrzebujesz oddzielnych licencji na ochronę programu VMware/funkcji Hyper-V. Jeśli jesteś klientem programu System Center, aby chronić maszyny wirtualne VMware przy użyciu programu DPM. Jeśli nie jesteś klientem programu System Center, można użyć usługi Azure Backup Server (płatność za rzeczywiste użycie) do ochrony maszyn wirtualnych VMware.

## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Programy Azure Backup Server i System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Czy można używać programu Azure Backup Server do tworzenia kopii zapasowej z odzyskiwaniem systemu od zera (BMR) serwera fizycznego? <br/>
Tak.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>Czy mogę zarejestrować serwer programu DPM w wielu magazynach? <br/>
Nie. Serwer DPM lub MABS można zarejestrować tylko w jednym magazynie.

### <a name="which-version-of-system-center-data-protection-manager-is-supported"></a>Która wersja programu System Center Data Protection Manager jest obsługiwana?

Zaleca się zainstalowanie [najnowszego](http://aka.ms/azurebackup_agent) agenta usługi Azure Backup z najnowszego pakietu zbiorczego aktualizacji (UR) dla programu System Center Data Protection Manager (DPM). 
- Programu System Center DPM 2012 R2 [14 pakiet zbiorczy aktualizacji](https://support.microsoft.com/help/4043315/update-rollup-14-for-system-center-2012-r2-data-protection-manager) jest najnowszą aktualizacją.
- Dla programu System Center DPM 2016 [pakietu zbiorczego aktualizacji 2](https://support.microsoft.com/en-us/help/3209593) jest najnowszą aktualizacją.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-protect-on-premises-applicationvm-workloads-to-azure"></a>Agent usługi Azure Backup został zainstalowany w celu ochrony plików i folderów. Czy można zainstalować programu System Center DPM do ochrony obciążeń aplikacji i maszyn wirtualnych w środowisku lokalnym na platformie Azure?

Tak. Jednak aby używać usługi Azure Backup usługi System Center Data Protection Manager (DPM), najpierw zainstaluj program DPM, a następnie zainstaluj agenta usługi Azure Backup. Instalowanie składników usługi Azure Backup w tej kolejności zapewnia współdziałanie agenta usługi Azure Backup z programem DPM. Nie zaleca się instalowania agenta usługi Azure Backup przed instalacją programu DPM. Takie rozwiązanie nie jest obsługiwane.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Czy można użyć programu DPM, aby utworzyć kopię zapasową aplikacji w usłudze Azure Stack?

Nie. Jeśli kopia zapasowa Azure można użyć do ochrony usługi Azure Stack, usługi Azure Backup nie obsługuje obecnie za pomocą programu DPM, aby utworzyć kopię zapasową aplikacji w usłudze Azure Stack.

## <a name="how-azure-backup-works"></a>Jak działa usługa Azure Backup
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Czy w przypadku anulowania uruchomionego zadania tworzenia kopii zapasowej przesyłane dane zostaną usunięte? <br/>
Nie. Wszystkie dane przesłane do magazynu przed momentem anulowania zadania utworzenia kopii zapasowej pozostaną w magazynie. Usługa Azure Backup używa mechanizmu, który podczas tworzenia kopii zapasowej co pewien czas dodaje punkty kontrolne do danych kopii zapasowej. Ponieważ w danych kopii zapasowej umieszczone są punkty kontrolne, następny proces kopii zapasowej może sprawdzić integralność plików. Następnym zadaniem kopii zapasowej będzie przyrostowa kopia zapasowa tworzona w oparciu o wcześniej utworzoną kopię zapasową danych. Przyrostowe kopie zapasowe przesyłają tylko nowe lub zmienione dane, dzięki czemu zapewnia się lepsze wykorzystanie przepustowości.

Jeśli anulujesz zadanie kopii zapasowej dla maszyny wirtualnej platformy Azure, wszelkie przesłane dane zostaną zignorowane. Następne zadanie kopii zapasowej przesyła przyrostowe dane z ostatniego wykonanego zadania kopii zapasowej.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Czy istnieją ograniczenia dotyczące czasu wykonywania zadań tworzenia kopii zapasowej i ich liczby?<br/>
Tak. Zadania tworzenia kopii zapasowej na serwerze lub stacjach roboczych z systemem Windows można uruchamiać maksymalnie trzy razy w ciągu dnia. Można uruchomić zadania tworzenia kopii zapasowej w programie System Center DPM maksymalnie dwa razy dziennie. Zadanie tworzenia kopii zapasowej maszyn wirtualnych IaaS można uruchamiać jeden raz w ciągu dnia. Korzystając z zasad planowania dla systemu Windows Server lub stacji roboczej, Windows, aby określić dzienne lub tygodniowe harmonogramy. Za pomocą programu System Center DPM można określić dzienne, tygodniowe, miesięczne i roczne harmonogramy.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Dlaczego rozmiar danych przesyłanych do magazynu usługi Recovery Services jest mniejszy niż rozmiar danych, dla których utworzono kopię zapasową?<br/>
 Wszystkie kopie zapasowe wykonywane przy użyciu agenta usługi Azure Backup, programu SCDPM lub serwera usługi Azure Backup są kompresowane i szyfrowane, zanim zostaną przesłane. Po zastosowaniu kompresji i szyfrowania danych w magazynie usługi Recovery Services jest 30 – 40% mniejsze.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vaultbr"></a>Czy mogę usunąć pojedyncze pliki z punktu odzyskiwania w magazynie<br/>
Nie, usługa Azure Backup nie obsługuje usuwania lub czyszczenia poszczególne elementy z kopii zapasowych przechowywanych.

## <a name="what-can-i-back-up"></a>Dla jakich danych mogę utworzyć kopię zapasową
### <a name="which-operating-systems-does-azure-backup-support-br"></a>Które systemy operacyjne obsługuje usługi Azure Backup? <br/>
Usługa Azure Backup obsługuje następujące systemy operacyjne w przypadku zabezpieczonego tworzenia kopii zapasowych: plików i folderów oraz kopii zapasowych aplikacji obciążeń przy użyciu programów Azure Backup Server i System Center Data Protection Manager (DPM).

| System operacyjny | Platforma | SKU |
|:--- | --- |:--- |
| Windows 8 i najnowsze dodatki Service Pack |64-bitowa |Enterprise, Pro |
| Windows 7 i najnowsze dodatki Service Pack |64-bitowa |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 i najnowsze dodatki Service Pack |64-bitowa |Enterprise, Pro |
| Windows 10 |64-bitowa |Enterprise, Pro, Home |
| Windows Server 2016 |64-bitowa |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 i najnowsze dodatki Service Pack |64-bitowa |Standard, Datacenter, Foundation |
| Windows Server 2012 i najnowsze dodatki Service Pack |64-bitowa |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 i najnowsze dodatki Service Pack |64-bitowa |Standard, Workgroup | 
| Windows Storage Server 2012 R2 i najnowsze dodatki Service Pack |64-bitowa |Standard, Workgroup |
| Windows Storage Server 2012 i najnowsze dodatki Service Pack |64-bitowa |Standard, Workgroup |
| Windows Server 2012 R2 i najnowsze dodatki Service Pack |64-bitowa |Essential |
| Windows Server 2008 R2 SP1 |64-bitowa |Standard, Enterprise, Datacenter, Foundation |

**W przypadku usługi kopii zapasowych maszyny wirtualnej platformy Azure:**

* **Linux**: usługa Azure Backup obsługuje [dystrybucje zalecane dla platformy Azure](../virtual-machines/linux/endorsed-distros.md) z wyjątkiem systemu operacyjnego Linux Core.  W przypadku pozostałych dystrybucji opartych na modelu „Bring Your Own Linux” usługa Azure Backup powinna działać, jeśli na maszynie wirtualnej jest dostępny agent maszyny wirtualnej oraz jest obsługiwany język Python.
* **Windows Server**: wersje starsze niż Windows Server 2008 R2 nie są obsługiwane.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Czy istnieje ograniczenie rozmiaru poszczególnych źródeł danych, dla których tworzona jest kopia zapasowa? <br/>
Usługa Azure Backup wymusza maksymalny rozmiar źródła danych, jednak są duże limity dla źródła. Według stanu na sierpień 2015 r. maksymalny rozmiar źródła danych dla obsługiwanych systemów operacyjnych wynosi:

| L.p. | System operacyjny | Maksymalny rozmiar źródła danych |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 lub nowszy |54 400 GB |
| 2 |Windows 8 lub nowszy |54 400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

W poniższej tabeli opisano sposób ustalania rozmiaru dla każdego źródła danych.

| Źródło danych | Szczegóły |
|:---:|:--- |
| Wolumin |Ilość danych kopii zapasowej z jednego woluminu komputera serwera lub klienta |
| Maszyna wirtualna z funkcją Hyper-V |Suma danych wszystkich dysków VHD maszyny wirtualnej, której kopia zapasowa jest wykonywana |
| Baza danych Microsoft SQL Server |Rozmiar pojedynczej bazy danych SQL, której kopia zapasowa jest wykonywana |
| Microsoft SharePoint |Suma baz danych z zawartością i danymi konfiguracyjnymi w farmie programu SharePoint, której kopia zapasowa jest wykonywana |
| Microsoft Exchange |Suma wszystkich baz danych programu Exchange w serwerze Exchange, którego kopia zapasowa jest wykonywana |
| Stan systemu/BMR |Każda pojedyncza kopia BMR lub stanu systemu komputera, którego kopia zapasowa jest wykonywana |

Do utworzenia kopii zapasowej maszyny Wirtualnej IaaS platformy Azure każda maszyna wirtualna może mieć maksymalnie 16 dysków danych, a każdy dysk danych może mieć maksymalnie 4095 GB.

### <a name="is-there-a-limit-on-the-amount-of-data-held-in-a-recovery-services-vault"></a>Czy istnieje ograniczenie na ilość danych przechowywanych w magazynie usługi Recovery Services?
Nie ma żadnego limitu ilości danych, które użytkownik może wykonywać kopie zapasowe usługi Recovery Services magazynu.

## <a name="retention-policy-and-recovery-points"></a>Zasady przechowywania i punkty odzyskiwania
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Czy istnieje różnica między zasadami przechowywania w programie DPM i systemie Windows Server/kliencie systemu Windows (tj. w systemie Windows Server bez programu DPM)?<br/>
Nie, zarówno program DPM, jak i system Windows Server/klient systemu Windows mają dzienne, tygodniowe, miesięczne i roczne zasady przechowywania.

### <a name="can-i-configure-my-retention-policies-selectively--that-is-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Czy mogę skonfigurować Mój zasady przechowywania selektywnie — to znaczy znajduje się w Konfigurowanie co tydzień i codziennie, ale nie co rok i miesiąc?<br/>
Tak, struktura przechowywania usługi Azure Backup umożliwia w pełni elastyczne definiowanie zasad przechowywania zgodnie z wymaganiami użytkownika.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Czy można „zaplanować utworzenie kopii zapasowej” o godzinie 18:00 i określić zasady przechowywania na inną godzinę?<br/>
Nie. Zasady przechowywania mogą być stosowane wyłącznie w punktach kopii zapasowej. Na poniższej ilustracji określono zasady przechowywania dla kopii zapasowych wykonanych o godzinie 12:00 i 18:00. <br/>

![Harmonogram tworzenia i przechowywania kopii zapasowych](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Czy w przypadku, gdy kopia zapasowa przechowywana jest przez długi czas, odzyskanie danych z wcześniejszego punktu danych trwa dłużej? <br/>
Nie — czas odzyskania najstarszego i najnowszego punktu jest taki sam. Każdy punkt odzyskiwania zachowuje się jak pełny punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Skoro każdy punkt odzyskiwania jest traktowany jak pełny punkt, czy ma to wpływ na całkowitą fakturowaną przestrzeń dyskową dla kopii zapasowych?<br/>
Typowe produkty punktów długoterminowego przechowywania przechowują dane kopii zapasowych jako pełne punkty. Pełne punkty są *nieefektywne* z punktu widzenia magazynu, ale są łatwiejsze i szybsze do przywrócenia. Przyrostowe kopie są *efektywne* z punktu widzenia magazynu, ale wymagają przywrócenia łańcucha danych, co wpływa na czas odzyskiwania. Architektura magazynu usługi Azure Backup oferuje zalety obu rozwiązań dzięki optymalnemu przechowywaniu danych, umożliwiającemu szybkie ich przywrócenie, oraz niskim kosztom magazynowania. Takie podejście do magazynu danych zapewnia efektywne wykorzystanie przepustowości ruchu przychodzącego i wychodzącego. Zarówno ilość pamięci masowej, jak i czas potrzebny do odzyskania danych są ograniczone do minimum. Dowiedz się więcej o tym, dlaczego [przyrostowe kopie zapasowe](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) są wydajne.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Czy istnieje ograniczenie liczby punktów odzyskiwania, które można utworzyć?<br/>
Możesz utworzyć maksymalnie 9999 punktów odzyskiwania na każde pojedyncze chronione wystąpienie. Chronione wystąpienie to komputer, serwer (fizyczny lub wirtualny) albo obciążenie, które skonfigurowano do tworzenia kopii zapasowych na platformie Azure. Aby uzyskać więcej informacji, zobacz objaśnienia w sekcjach [Tworzenie kopii zapasowej i przechowywanie](./backup-introduction-to-azure-backup.md#backup-and-retention) i [Co to jest chronione wystąpienie?](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance).

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Ile zadań odzyskiwania można wykonać na danych, których kopie zapasowe utworzono na platformie Azure?<br/>
Nie ma ograniczenia liczby operacji odzyskiwania z usługi Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Czy podczas przywracania danych należy zapłacić za ruch wychodzący z platformy Azure? <br/>
Nie. Operacje odzyskiwania są bezpłatne i nie są naliczane opłaty za ruch wyjściowy.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co się stanie, gdy zmienią się Moje zasad tworzenia kopii zapasowej?
Po zastosowaniu nowych zasad, harmonogram i okres przechowywania nowych zasad jest zakończony. Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami. W przypadku skrócenia okresu przechowywania zostaną one oznaczone do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usunięte.

## <a name="azure-backup-encryption"></a>Szyfrowanie w usłudze Azure Backup
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Czy dane wysyłane do platformy Azure są szyfrowane? <br/>
Tak. Dane są szyfrowane na lokalnym serwerze/kliencie/komputerze z programem SCDPM za pomocą algorytmu szyfrowania AES-256, a następnie przesyłane za pośrednictwem bezpiecznego łącza HTTPS.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Czy dane kopii zapasowej na platformie Azure są również szyfrowane?<br/>
Tak. Dane wysyłane do platformy Azure pozostają zaszyfrowane (nieaktywne). Firma Microsoft nie odszyfrowuje danych kopii zapasowej w żadnym punkcie. Podczas wykonywania kopii zapasowej maszyny wirtualnej platformy Azure usługa Azure Backup opiera się na szyfrowaniu maszyny wirtualnej. Jeśli na przykład maszyna wirtualna została zaszyfrowana przy użyciu usługi Azure Disk Encryption lub innej technologii szyfrowania, usługa Azure Backup używa tego szyfrowania do zabezpieczenia danych.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Jaka jest minimalna długość klucza szyfrowania używanego do szyfrowania danych w kopii zapasowej? <br/>
Klucz szyfrowania powinien składać się z co najmniej 16 znaków, gdy używasz agenta usługi Azure Backup. W przypadku maszyn wirtualnych platformy Azure nie ma limitu długości kluczy używanych przez usługę Azure KeyVault. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Co się stanie, jeśli klucz szyfrowania zostanie zgubiony przez użytkownika? Czy użytkownik lub firma Microsoft może odzyskać dane? <br/>
Klucz używany do szyfrowania danych kopii zapasowej jest przechowywany tylko lokalnie przez klienta. Firma Microsoft nie przechowuje kopii klucza na platformie Azure i nie ma do niego dostępu. Jeśli klient zgubi klucz, firma Microsoft nie może odzyskać danych kopii zapasowej.
