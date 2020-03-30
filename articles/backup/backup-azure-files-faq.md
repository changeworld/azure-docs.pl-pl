---
title: Tworzenie kopii zapasowej plików w usłudze Azure Files — często zadawane pytania
description: W tym artykule odnajduj odpowiedzi na typowe pytania dotyczące ochrony udziałów plików platformy Azure za pomocą usługi Azure Backup.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: c69d4642aefbd599d3783dcdfa059a0cd9d129d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302546"
---
# <a name="questions-about-backing-up-azure-files"></a>Pytania dotyczące tworzenia kopii zapasowej plików w usłudze Azure Files

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej plików usługi Azure Files. W niektórych odpowiedziach znajdują się linki do artykułów zawierających szczegółowe informacje. Pytania dotyczące usługi Azure Backup można zadawać także na [forum dyskusyjnym](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Aby szybko przeskanować sekcje znajdujące się w tym artykule, użyj linków z prawej strony w obszarze **W tym artykule**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurowanie zadania tworzenia kopii zapasowej dla usługi Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares"></a>Dlaczego nie widzę niektórych kont magazynu, które chcę chronić, zawierających prawidłowe udziały plików platformy Azure?

W wersji zapoznawczej kopie zapasowe udziałów plików platformy Azure nie obsługują wszystkich typów kont magazynu. Lista obsługiwanych kont magazynu znajduje się [tutaj](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Być może konto magazynu, którego szukasz, jest już chronione lub zarejestrowane w innym magazynie. [Wyrejestruj się](troubleshoot-azure-files.md#configuring-backup) z magazynu, aby uwidocznić konto magazynu w innych magazynach w celu zapewnienia ochrony.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Gdy próbuję skonfigurować kopię zapasową, nie widzę niektórych udziałów plików platformy Azure na koncie magazynu. Dlaczego?

Sprawdź, czy udział pliku platformy Azure nie jest już chroniony w tym samym magazynie usługi Recovery Services lub nie został ostatnio usunięty.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Czy mogę chronić udziały plików połączonych z grupą synchronizacji w usłudze Azure Files Sync?

Tak. Ochrona udziałów plików platformy Azure połączonych z grupami synchronizacji została włączona i jest częścią publicznej wersji zapoznawczej.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Podczas próby utworzenia kopii zapasowej udziałów plików konto usługi Storage zostało kliknięte w celu odnalezienia w nim udziałów plików. Ale nie zostały one jednak objęte ochroną. Jak mogę chronić te udziały plików w innym magazynie?

Wybranie konta usługi Storage w celu odnalezienia udziałów plików podczas próby utworzenia kopii zapasowej powoduje zarejestrowanie konta usługi Storage w obrębie magazynu, z poziomu którego wykonano tę czynność. Jeśli chcesz chronić udziały plików przy użyciu innego magazynu, [wyrejestruj](troubleshoot-azure-files.md#configuring-backup) wybrane konto usługi Storage z tego magazynu.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Czy mogę zmienić Vault, do którego mogę wywrzeć zapas ową dziele zapasowe udziałów plików?

Tak. Musisz jednak [zatrzymać ochronę w udziale plików](manage-afs-backup.md#stop-protection-on-a-file-share) z podłączonego magazynu, [Wyrejestrować](troubleshoot-azure-files.md#configuring-backup) to konto magazynu, a następnie chronić je przed innym skarbcem.

### <a name="in-which-geos-can-i-back-up-azure-file-shares"></a>W jakich formatach geograficznych można nawładnia kopii zapasowej udziałów plików platformy Azure?

Kopie zapasowe udziałów plików usługi Azure Files są obecnie dostępne w wersji zapoznawczej tylko w następujących obszarach geograficznych:

- Australia Wschodnia (AE)
- Australia Południowo-Wschodnia (ASE)
- Brazylia Południowa (BRS)
- Kanada Środkowa (CNC)
- Kanada Wschodnia (CE)
- Środkowe stany USA (CUS)
- Azja Wschodnia (EA)
- Wschodnie stany USA (EUS)
- Wschodnie stany USA 2 (EUS2)
- Japonia Wschodnia (JPE)
- Japonia Zachodnia (JPW)
- Indie Środkowe (INC)
- Indie Południowe (INS)
- Korea Środkowa (KRC)
- Korea Południowa (KRS)
- Północno-środkowe stany USA (NCUS)
- Europa Północna (NE)
- Południowo-środkowe stany USA (SCUS)
- Azja Południowo-Wschodnia (SEA)
- Południowe Zjednoczone Królestwo (UKS)
- Zachodnie Zjednoczone Królestwo (UKW)
- Europa Zachodnia (WE)
- Zachodnie stany USA (WUS)
- Zachodnio-środkowe stany USA (WCUS)
- Zachodnie stany USA 2 (WUS 2)
- US Gov Arizona (UGA)
- US Gov Teksas (UGT)
- US Gov Wirginia (UGV)
- Australia Central (ACL)
- Indie Zachód (INW)
- Republika Południowej Afryki Północna (SAN)
- Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone (UAN)
- Francja Centralna (FRC)
- Niemcy Północne (GN)                       
- Niemcy Zachodnio-Środkowe (GWC)
- Republika Południowej Afryki Zachodnia (SAW)
- Zjednoczone Emiraty Zjednoczone Emiraty Zjednoczone (UAC)
- NWE (Norwegia Wschodnia)     
- NWW (Norwegia Zachodnia)
- SZN (Szwajcaria Północna)

Napisz [AskAzureBackupTeam@microsoft.com](mailto:askazurebackupteam@microsoft.com) do, jeśli chcesz go użyć w określonym geo, który nie jest wymieniony powyżej.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Ile udziałów plików platformy Azure można chronić w magazynie?

W wersji zapoznawczej można chronić w jednym magazynie udziały plików platformy Azure znajdujące się na maksymalnie 50 kontach magazynu. Maksymalna liczba udziałów plików platformy Azure w jednym magazynie wynosi 200.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Czy można chronić dwa różne udziały plików z poziomu tego samego konta usługi Storage w różnych magazynach?

Nie. Wszystkie udziały plików na koncie usługi Storage mogą być chronione tylko przez ten sam magazyn.

## <a name="backup"></a>Tworzenie kopii zapasowych

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>Ile zaplanowanych kopii zapasowych można skonfigurować na udział plików?

Usługa Azure Backup obsługuje obecnie konfigurowanie zaplanowanych kopii zapasowych raz dziennie udziałów plików platformy Azure.

### <a name="how-many-on-demand-backups-can-i-take-per-file-share"></a>Ile kopii zapasowych na żądanie można utworzyć na jeden udział plików?

W dowolnym momencie możesz mieć maksymalnie 200 migawek na jeden udział plików. Limit obejmuje migawki wykonane przez usługę Azure Backup na podstawie zasad. Jeśli tworzenie kopii zapasowych będzie kończyć się niepowodzeniem z powodu osiągnięcia tego limitu, usuń punkty przywracania utworzone na żądanie, aby zapewnić pomyślne tworzenie kopii zapasowych w przyszłości.

## <a name="restore"></a>Przywracanie

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Czy można odzyskać dane z usuniętego udziału plików platformy Azure?

Podczas usuwania udziału plików platformy Azure zostanie wyświetlona lista kopii zapasowych, które zostaną usunięte, z prośbą o potwierdzenie. Nie można przywrócić usuniętego udziału plików platformy Azure.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Czy można przywrócić dane z kopii zapasowych, jeśli zatrzymano ochronę udziału plików platformy Azure?

Tak. Jeśli podczas zatrzymywania ochrony została wybrana opcja **Zachowaj dane kopii zapasowej**, można przywrócić dane z wszystkich istniejących punktów przywracania.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Co się stanie, jeśli anuluję trwające zadanie przywracania?

Jeśli trwające zadanie przywracania zostanie anulowane, proces przywracania zostanie zatrzymany, a wszystkie pliki przywrócone przed anulowaniem, pozostanie w skonfigurowanym miejscu docelowym (oryginalna lub alternatywna lokalizacja) bez wycofywania.

## <a name="manage-backup"></a>Zarządzanie tworzeniem kopii zapasowych

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Czy można konfigurować i przywracać kopie zapasowe udziałów plików platformy Azure oraz zarządzać nimi za pomocą programu PowerShell?

Tak. Zapoznaj się ze szczegółową dokumentacją [tutaj](backup-azure-afs-automation.md)

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it"></a>Czy mogę uzyskać dostęp do migawek wykonywanych przez usługę Azure Backup i zainstalować je?

Do wszystkich migawek wykonanych przez usługę Azure Backup możesz uzyskać dostęp, wyświetlając migawki w portalu, w programie PowerShell lub przy użyciu interfejsu wiersza polecenia. Aby dowiedzieć się więcej na temat udostępniania migawek udziału usługi Azure Files, zobacz [Omówienie migawek udziału dla usługi Azure Files (wersja zapoznawcza)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Jaki jest maksymalny retencji można skonfigurować dla kopii zapasowych?

Kopia zapasowa udziałów plików platformy Azure umożliwia konfigurowanie w zasadach okresu przechowywania do 180 dni. Jednak przy użyciu [opcji „Kopia zapasowa na żądanie” w programie PowerShell](backup-azure-afs-automation.md#trigger-an-on-demand-backup) można przechowywać punkt odzyskiwania nawet przez 10 lat.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Co się stanie, jeśli zmienię zasady tworzenia kopii zapasowych udziału plików platformy Azure?

Po zastosowaniu nowych zasad do udziału plików będzie obowiązywał harmonogram i okres przechowywania określony przez te nowe zasady. Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami. W przypadku skrócenia okresu przechowywania są one oznaczane do oczyszczenia w ramach następnego zadania oczyszczania i usuwane.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innych obszarach usługi Azure Backup, zobacz niektóre z tych często zadawanych pytań dotyczących tworzenia kopii zapasowych:

- [Recovery Services vault FAQ (Magazyn usługi Recovery Services — często zadawane pytania)](backup-azure-backup-faq.md)
- [Azure VM backup FAQ (Tworzenie kopii zapasowych maszyn wirtualnych — często zadawane pytania)](backup-azure-vm-backup-faq.md)
- [Azure Backup agent FAQ (Agent usługi Azure Backup — często zadawane pytania)](backup-azure-file-folder-backup-faq.md)
