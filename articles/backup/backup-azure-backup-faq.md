---
title: Często zadawane pytania dotyczące usługi Azure Backup
description: 'Odpowiedzi na często zadawane pytania dotyczące: Azure Backup funkcje, w tym magazyny Recovery Services, do których można utworzyć kopię zapasową, jak działa, szyfrowanie i limity. '
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/07/2019
ms.author: dacurwin
ms.openlocfilehash: c60b2bfae0d974d454c03b7eba655cbdacab5943
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466690"
---
# <a name="azure-backup---frequently-asked-questions"></a>Azure Backup — często zadawane pytania
W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące usługi Azure Backup.

## <a name="recovery-services-vault"></a>Magazyn usług Recovery Services

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription"></a>Czy istnieje ograniczenie liczby magazynów, które można utworzyć w poszczególnych subskrypcjach platformy Azure?
Tak. Można utworzyć maksymalnie 500 magazynów Recovery Services, na obsługiwany region Azure Backup, na subskrypcję. Jeśli potrzebna jest większa liczba magazynów, należy utworzyć dodatkową subskrypcję.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault"></a>Czy istnieją ograniczenia dotyczące liczby serwerów/maszyn, które można zarejestrować w każdym magazynie?
Możesz zarejestrować do 1000 maszyn wirtualnych platformy Azure na magazyn. Jeśli używasz agenta Microsoft Azure Backup, możesz zarejestrować do 50 agentów MAB na magazyn. Można też zarejestrować serwery z systemem 50 i serwerów programu DPM do magazynu.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-data-from-different-servers-in-the-vault-when-restoring-data"></a>Jeśli Moja organizacja ma jeden magazyn, jak można izolować dane z różnych serwerów w magazynie podczas przywracania danych?
Dane serwera, które mają zostać odzyskane, powinny używać tego samego hasła podczas konfigurowania kopii zapasowej. Jeśli chcesz odizolować odzyskiwanie do określonego serwera lub serwerów, Użyj hasła tylko dla tego serwera lub serwerów. Na przykład serwery zarządzania zasobami ludzkimi mogą korzystać z jednego hasła szyfrowania, serwery księgowości z drugiego, a serwery pamięci masowej z trzeciego.

### <a name="can-i-move-my-vault-between-subscriptions"></a>Czy mogę przenieść magazyn między subskrypcjami?
Tak. Aby przenieść magazyn Recovery Services, zapoznaj [](backup-azure-move-recovery-services-vault.md) się z tym artykułem

### <a name="can-i-move-backup-data-to-another-vault"></a>Czy mogę przenieść dane kopii zapasowej do innego magazynu?
Nie. Nie można przenieść danych kopii zapasowej przechowywanych w magazynie do innego magazynu.

### <a name="can-i-change-from-grs-to-lrs-after-a-backup"></a>Czy mogę zmienić z GRS na LRS po utworzeniu kopii zapasowej?
Nie. Magazyn Recovery Services może zmienić opcje magazynu tylko przed zapisaniem jakichkolwiek kopii zapasowych.

### <a name="can-i-do-an-item-level-restore-ilr-for-vms-backed-up-to-a-recovery-services-vault"></a>Czy można wykonać przywracanie na poziomie elementu (ILR) dla maszyn wirtualnych, których kopia zapasowa jest wykonywana w magazynie Recovery Services?
- ILR jest obsługiwana w przypadku maszyn wirtualnych platformy Azure z kopią zapasową maszyny wirtualnej platformy Azure. Aby uzyskać więcej informacji, zobacz [artykuł](backup-azure-restore-files-from-vm.md)
- Usługa ILR nie jest obsługiwana w przypadku punktów odzyskiwania online lokalnych maszyn wirtualnych, których kopia zapasowa jest wykonywana przez serwer usługi Azure Backup lub program System Center DPM.


## <a name="azure-backup-agent"></a>Agent usługi Azure Backup

### <a name="where-can-i-find-common-questions-about-the-azure-backup-agent-for-azure-vm-backup"></a>Gdzie mogę znaleźć często zadawane pytania dotyczące agenta Azure Backup dla kopii zapasowej maszyny wirtualnej platformy Azure?

- Przeczytaj [często zadawane pytania](backup-azure-vm-backup-faq.md)dotyczące agenta uruchomionego na maszynach wirtualnych platformy Azure.
- Przeczytaj [często zadawane pytania](backup-azure-file-folder-backup-faq.md)dotyczące agenta używanego do tworzenia kopii zapasowych folderów plików platformy Azure.


## <a name="general-backup"></a>Ogólna kopia zapasowa

### <a name="are-there-limits-on-backup-scheduling"></a>Czy istnieją limity planowania tworzenia kopii zapasowych?
Tak.
- Można utworzyć kopię zapasową maszyn z systemem Windows Server lub Windows na trzy razy dziennie. Można ustawić planowanie dzienne lub tygodniowe dla zasad planowania.
- Można utworzyć kopię zapasową programu DPM maksymalnie dwa razy dziennie. Zasady planowania można ustawić codziennie, co tydzień, co miesiąc i co rok.
- Kopie zapasowe maszyn wirtualnych platformy Azure są sporządzane raz dziennie.

### <a name="what-operating-systems-are-supported-for-backup"></a>Jakie systemy operacyjne są obsługiwane na potrzeby tworzenia kopii zapasowych?
Azure Backup obsługuje te systemy operacyjne na potrzeby tworzenia kopii zapasowych plików i folderów oraz aplikacji chronionych przez Azure Backup Server i program DPM.

**OS** | **SKU** | **Szczegóły**
--- | --- | ---
Stacja | |
Windows 10 64 bit | Enterprise, Pro, Home | Na maszynach powinna być uruchomiony najnowszy pakiet usług i aktualizacje.
Windows 8.1 64 bit | Enterprise, Pro | Na maszynach powinna być uruchomiony najnowszy pakiet usług i aktualizacje.
Windows 8 64 bit | Enterprise, Pro | Na maszynach powinna być uruchomiony najnowszy pakiet usług i aktualizacje.
Windows 7 64 bit | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter | Na maszynach powinna być uruchomiony najnowszy pakiet usług i aktualizacje.
Serwer | |
Windows Server 2019 64 bit | Standard, Datacenter, Essentials | Najnowsze dodatki Service Pack/aktualizacje.
Windows Server 2016 64 bit | Standard, Datacenter, Essentials | Najnowsze dodatki Service Pack/aktualizacje.
Windows Server 2012 R2 64 — bit | Standard, Datacenter, Foundation | Najnowsze dodatki Service Pack/aktualizacje.
Windows Server 2012 64 bit | Datacenter, Foundation, Standard | Najnowsze dodatki Service Pack/aktualizacje.
Windows Storage Server 2016 64 bit | Standard, Workgroup | Najnowsze dodatki Service Pack/aktualizacje.
Windows Storage Server 2012 R2 64 — bit | Standard, Grupa robocza, podstawowe | Najnowsze dodatki Service Pack/aktualizacje.
Windows Storage Server 2012 64 bit | Standard, Workgroup | Najnowsze dodatki Service Pack/aktualizacje.
Windows Server 2008 R2 z dodatkiem SP1 64 bit | Standard, Enterprise, Datacenter, Foundation | Z najnowszymi aktualizacjami.
Windows Server 2008 64 bit | Standard, Enterprise i Datacenter | Z najnowszymi aktualizacjami.

W przypadku kopii zapasowych w systemie Linux dla maszyny wirtualnej platformy Azure Azure Backup obsługuje [listę dystrybucji potwierdzonych przez platformę Azure](../virtual-machines/linux/endorsed-distros.md), z wyjątkiem podstawowego systemu operacyjnego Linux i 32-bitowego. Inne dystrybucje systemu Linux mogą współdziałać, o ile Agent maszyny wirtualnej jest dostępny na maszynie wirtualnej, i istnieje obsługa języka Python.


### <a name="are-there-size-limits-for-data-backup"></a>Czy istnieją limity rozmiaru kopii zapasowych danych?
Limity rozmiarów są następujące:

System operacyjny/maszyna | Limit rozmiaru źródła danych
--- | ---
Windows 8 lub nowszy | 54 400 GB
Windows 7 |1700 GB
Windows Server 2012 lub nowszy | 54 400 GB
Windows Server 2008, Windows Server 2008 R2 | 1700 GB
Maszyna wirtualna platformy Azure | 16 dysków z danymi<br/><br/> Dysk danych do 4095 GB

### <a name="how-is-the-data-source-size-determined"></a>Jak jest określany rozmiar źródła danych?
W poniższej tabeli opisano sposób ustalania rozmiaru dla każdego źródła danych.

**Źródło danych** | **Szczegóły**
--- | ---
Wolumin |Ilość danych, których kopia zapasowa jest tworzona z maszyny wirtualnej z pojedynczym woluminem.
Baza danych programu SQL Server |Rozmiar pojedynczego rozmiaru bazy danych SQL, którego kopia zapasowa jest tworzona.
Program SharePoint | Suma baz danych zawartości i konfiguracji w obrębie farmy programu SharePoint, których kopia zapasowa jest tworzona.
Exchange |Suma wszystkich baz danych programu Exchange znajdujących się w kopii zapasowej programu Exchange Server.
BMR/stan systemu |Każda indywidualna kopia BMR lub stanu systemu maszyny, której kopia zapasowa jest tworzona.

### <a name="is-there-a-limit-on-the-amount-of-data-backed-up-using-a-recovery-services-vault"></a>Czy istnieje ograniczenie ilości danych, których kopia zapasowa została utworzona przy użyciu Recovery Services magazynu?
Nie ma żadnego limitu ilości danych, których kopię zapasową można utworzyć przy użyciu magazynu Recovery Services.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-selected-for-backup"></a>Dlaczego rozmiar danych transferowanych do magazynu Recovery Services mniejszy niż dane wybrane do kopii zapasowej?
Dane utworzone przy użyciu Azure Backup agenta, programu DPM i Azure Backup Server są kompresowane i szyfrowane przed przekazaniem. W przypadku zastosowania kompresji i szyfrowania dane w magazynie są mniejsze od 30-40%.

### <a name="can-i-delete-individual-files-from-a-recovery-point-in-the-vault"></a>Czy mogę usunąć poszczególne pliki z punktu odzyskiwania w magazynie?
Nie, Azure Backup nie obsługuje usuwania lub przeczyszczania poszczególnych elementów z przechowywanych kopii zapasowych.

### <a name="if-i-cancel-a-backup-job-after-it-starts-is-the-transferred-backup-data-deleted"></a>Czy po rozpoczęciu zadania tworzenia kopii zapasowej zostaną usunięte przesłane dane kopii zapasowej?
Nie. Wszystkie dane, które zostały przesłane do magazynu przed anulowaniem zadania tworzenia kopii zapasowej, pozostają w magazynie.

- Usługa Azure Backup używa mechanizmu, który podczas tworzenia kopii zapasowej co pewien czas dodaje punkty kontrolne do danych kopii zapasowej.
- Ponieważ w danych kopii zapasowej umieszczone są punkty kontrolne, następny proces kopii zapasowej może sprawdzić integralność plików.
- Następnym zadaniem kopii zapasowej będzie przyrostowa kopia zapasowa tworzona w oparciu o wcześniej utworzoną kopię zapasową danych. Przyrostowe kopie zapasowe przesyłają tylko nowe lub zmienione dane, dzięki czemu zapewnia się lepsze wykorzystanie przepustowości.

Jeśli anulujesz zadanie kopii zapasowej dla maszyny wirtualnej platformy Azure, wszelkie przesłane dane zostaną zignorowane. Następne zadanie kopii zapasowej przesyła przyrostowe dane z ostatniego wykonanego zadania kopii zapasowej.

## <a name="retention-and-recovery"></a>Przechowywanie i odzyskiwanie

### <a name="are-the-retention-policies-for-dpm-and-windows-machines-without-dpm-the-same"></a>Czy zasady przechowywania dla maszyn programu DPM i systemu Windows bez programu DPM są takie same?
Tak, oba mają dzienne, tygodniowe, miesięczne i roczne zasady przechowywania.

### <a name="can-i-customize-retention-policies"></a>Czy mogę dostosować zasady przechowywania?
Tak, masz dostosowane zasady. Można na przykład skonfigurować cotygodniowe i codzienne wymagania dotyczące przechowywania, ale nie co rok i co miesiąc.

### <a name="can-i-use-different-times-for-backup-scheduling-and-retention-policies"></a>Czy mogę używać różnych godzin do zasad planowania i przechowywania kopii zapasowych?
Nie. Zasady przechowywania mogą być stosowane wyłącznie w punktach kopii zapasowej. Na przykład obrazy te przedstawiają zasady przechowywania dla kopii zapasowych wykonanych w 0 i 18:00.

![Harmonogram tworzenia kopii zapasowej i przechowywania](./media/backup-azure-backup-faq/Schedule.png)


### <a name="if-a-backup-is-kept-for-a-long-time-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Jeśli kopia zapasowa jest przechowywana przez dłuższy czas, czy odzyskiwanie starszego punktu danych trwa dłużej? <br/>
Nie. Czas odzyskiwania najstarszego lub najnowszego punktu jest taki sam. Każdy punkt odzyskiwania zachowuje się jak pełny punkt.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storage"></a>Skoro każdy punkt odzyskiwania jest traktowany jak pełny punkt, czy ma to wpływ na całkowitą fakturowaną przestrzeń dyskową dla kopii zapasowych?
Typowe produkty punktów długoterminowego przechowywania przechowują dane kopii zapasowych jako pełne punkty.

- Pełne punkty są *nieefektywne* z punktu widzenia magazynu, ale są łatwiejsze i szybsze do przywrócenia.
- Przyrostowe kopie są *wydajne* dla magazynu, ale wymagają przywrócenia łańcucha danych, co wpływa na czas odzyskiwania.

Architektura magazynu usługi Azure Backup oferuje zalety obu rozwiązań dzięki optymalnemu przechowywaniu danych, umożliwiającemu szybkie ich przywrócenie, oraz niskim kosztom magazynowania. Zapewnia to efektywne wykorzystanie przepustowości ruchu przychodzącego i wychodzącego. Ilość miejsca w magazynie danych i czas wymagany do odzyskania danych są ograniczone do minimum. Dowiedz się więcej na temat [przyrostowych kopii zapasowych](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-created"></a>Czy istnieje ograniczenie liczby punktów odzyskiwania, które można utworzyć?
Możesz utworzyć maksymalnie 9999 punktów odzyskiwania na każde pojedyncze chronione wystąpienie. Chronione wystąpienie jest komputerem, serwerem (fizycznym lub wirtualnym) lub obciążeniem, które tworzy kopię zapasową na platformie Azure.

- Dowiedz się więcej na temat [tworzenia kopii zapasowych i przechowywania](./backup-overview.md#backup-and-retention).


### <a name="how-many-times-can-i-recover-data-thats-backed-up-to-azure"></a>Ile razy można odzyskać dane, których kopia zapasowa jest wykonywana na platformie Azure?
Nie ma ograniczenia liczby operacji odzyskiwania z usługi Azure Backup.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure"></a>Czy podczas przywracania danych należy zapłacić za ruch wychodzący z platformy Azure?
Nie. Odzyskiwanie jest bezpłatne i nie jest naliczana opłata za ruch wychodzący.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Co się stanie, jeśli zmienię zasady tworzenia kopii zapasowych?
Po zastosowaniu nowych zasad zostanie wykonane planowanie i przechowywanie nowych zasad.

- Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami.
- W przypadku skrócenia okresu przechowywania zostaną one oznaczone do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usunięte.

## <a name="encryption"></a>Szyfrowanie

### <a name="is-the-data-sent-to-azure-encrypted"></a>Czy dane wysyłane do platformy Azure są szyfrowane?
Tak. Dane są szyfrowane na maszynie lokalnej przy użyciu AES256. Dane są przesyłane za pośrednictwem bezpiecznego linku HTTPS. Dane przesyłane w chmurze są chronione za pośrednictwem połączenia HTTPS tylko między usługą Storage i odzyskiwaniem. protokół iSCSI zabezpiecza dane przesyłane między usługą Recovery Service i komputerem użytkownika. Bezpieczny tunelowanie jest używane do ochrony kanału iSCSI.

### <a name="is-the-backup-data-on-azure-encrypted-as-well"></a>Czy dane kopii zapasowej na platformie Azure są również szyfrowane?
Tak. Dane na platformie Azure są szyfrowane na poziomie spoczynku.

- W przypadku kopii zapasowych w środowisku lokalnym szyfrowanie jest dostarczane przy użyciu hasła podanego podczas tworzenia kopii zapasowej na platformie Azure.
- W przypadku maszyn wirtualnych platformy Azure dane są szyfrowane w czasie spoczynku przy użyciu szyfrowanie usługi Storage (SSE).

Firma Microsoft nie odszyfrowuje danych kopii zapasowej w żadnym punkcie.

### <a name="what-is-the-minimum-length-of-encryption-the-key-used-to-encrypt-backup-data"></a>Jaka jest minimalna długość szyfrowania klucza używanego do szyfrowania danych kopii zapasowej?
Klucz szyfrowania powinien składać się z co najmniej 16 znaków, gdy używasz agenta usługi Azure Backup. W przypadku maszyn wirtualnych platformy Azure nie ma limitu długości kluczy używanych przez usługę Azure KeyVault.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-can-microsoft-recover-the-data"></a>Co się stanie, jeśli klucz szyfrowania zostanie zgubiony przez użytkownika? Czy mogę odzyskać dane? Czy firma Microsoft może odzyskać dane?
Klucz używany do szyfrowania danych kopii zapasowej znajduje się tylko w witrynie. Firma Microsoft nie przechowuje kopii klucza na platformie Azure i nie ma do niego dostępu. Jeśli klucz nie zostanie umieszczony, firma Microsoft nie może odzyskać danych kopii zapasowej.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z innymi często zadawanymi NZP:

- [Często zadawane pytania](backup-azure-vm-backup-faq.md) dotyczące kopii zapasowych maszyn wirtualnych platformy Azure.
- [Często zadawane pytania](backup-azure-file-folder-backup-faq.md) dotyczące agenta Azure Backup
