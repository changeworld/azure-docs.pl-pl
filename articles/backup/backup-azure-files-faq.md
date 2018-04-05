---
title: Tworzenie kopii zapasowej plików w usłudze Azure Files — często zadawane pytania
description: Ten artykuł zawiera szczegółowe informacje dotyczące ochrony plików w udziałach plików platformy Azure.
services: backup
keywords: Nie dodawaj ani nie edytuj słów kluczowych bez konsultacji z ekspertem SEO.
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 850d4d1e2ef6a13fcd8a072e6da210d558c7769b
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Pytania dotyczące tworzenia kopii zapasowej plików w usłudze Azure Files
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące tworzenia kopii zapasowej plików usługi Azure Files. W niektórych odpowiedziach znajdują się linki do artykułów zawierających szczegółowe informacje. Pytania dotyczące usługi Azure Backup można również zadawać na [forum dyskusyjnym](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Aby szybko przeskanować sekcje znajdujące się w tym artykule, użyj linków z prawej strony w obszarze **W tym artykule**.

## <a name="configuring-the-backup-job-for-azure-files"></a>Konfigurowanie zadania tworzenia kopii zapasowej dla usługi Azure Files

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares-br"></a>Dlaczego nie widzę niektórych kont magazynu, które chcę chronić, zawierających prawidłowe udziały plików platformy Azure? <br/>
W wersji zapoznawczej kopie zapasowe udziałów plików platformy Azure nie obsługują wszystkich typów kont magazynu. Lista obsługiwanych kont magazynu znajduje się [tutaj](troubleshoot-azure-files.md#preview-boundaries).

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Gdy próbuję skonfigurować kopię zapasową, nie widzę niektórych udziałów plików platformy Azure na koncie magazynu. Dlaczego? <br/>
Sprawdź, czy udział pliku platformy Azure nie jest już chroniony w tym samym magazynie usługi Recovery Services lub nie został ostatnio usunięty.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-file-sync-br"></a>Dlaczego nie mogę chronić udziałów plików połączonych z grupą synchronizacji w usłudze Azure File Sync? <br/>
Ochrona udziałów plików platformy Azure połączonych z grupami synchronizacji jest dostępna w ograniczonej wersji zapoznawczej. Napisz na adres [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com), podając identyfikator subskrypcji, i poproś o dostęp. 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>W których obszarach geograficznych można tworzyć kopie zapasowe udziałów plików usługi Azure Files? <br/>
Kopie zapasowe udziałów plików usługi Azure Files są obecnie dostępne w wersji zapoznawczej tylko w następujących obszarach geograficznych: 
-   Australia Południowo-Wschodnia (ASE) 
- Brazylia Południowa (BRS)
- Kanada Środkowa (CNC)
-   Kanada Wschodnia (CE)
-   Środkowe stany USA (CUS)
-   Azja Wschodnia (EA)
-   Australia Wschodnia (AE) 
-   Wschodnie stany USA (EUS)
-   Wschodnie stany USA 2 (EUS2)
-   Indie Środkowe (INC) 
-   Północno-środkowe stany USA (NCUS) 
-   Europa Północna (NE) 
-   Południowo-środkowe stany USA (SCUS) 
-   Azja Południowo-Wschodnia (SEA)
-   Południowe Zjednoczone Królestwo (UKS) 
-   Zachodnie Zjednoczone Królestwo (UKW) 
-   Europa Zachodnia (WE) 
-   Zachodnie stany USA (WUS)
-   Zachodnio-środkowe stany USA (WCUS)
-   Zachodnie stany USA 2 (WUS 2)

Napisz na adres [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) jeśli chcesz używać tej usługi w określonym obszarze geograficznym, którego nie ma na powyższej liście.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vaultbr"></a>Ile udziałów plików platformy Azure można chronić w magazynie?<br/>
W wersji zapoznawczej można chronić w jednym magazynie udziały plików platformy Azure znajdujące się na maksymalnie 25 kontach magazynu. Maksymalna liczba udziałów plików platformy Azure w jednym magazynie wynosi 200. 

## <a name="backup"></a>Backup

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Ile kopii zapasowych na żądanie można utworzyć na jeden udział plików? <br/>
W dowolnym momencie możesz mieć maksymalnie 200 migawek na jeden udział plików. Limit obejmuje migawki wykonane przez usługę Azure Backup na podstawie zasad. Jeśli tworzenie kopii zapasowych będzie kończyć się niepowodzeniem z powodu osiągnięcia tego limitu, usuń punkty przywracania utworzone na żądanie, aby zapewnić pomyślne tworzenie kopii zapasowych w przyszłości.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>Po włączeniu sieci wirtualnych na koncie magazynu tworzenie kopii zapasowej udziału plików na koncie kończy się niepowodzeniem. Dlaczego?
Kopie zapasowe udziałów plików platformy Azure nie obsługują kont magazynu, które mają włączone sieci wirtualne. Wyłącz sieci wirtualne na kontach magazynu, aby pomyślnie tworzyć kopie zapasowe. 

## <a name="restore"></a>Przywracanie

### <a name="can-i-recover-from-a-deleted-azure-file-share-br"></a>Czy można odzyskać dane z usuniętego udziału plików platformy Azure? <br/>
Podczas usuwania udziału plików platformy Azure zostanie wyświetlona lista kopii zapasowych, które również zostaną usunięte, z prośbą o potwierdzenie. Nie można przywrócić usuniętego udziału plików platformy Azure.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share-br"></a>Czy można przywrócić dane z kopii zapasowych, jeśli zatrzymano ochronę udziału plików platformy Azure? <br/>
Tak. Jeśli podczas zatrzymywania ochrony została wybrana opcja **Zachowaj dane kopii zapasowej**, można przywrócić dane z wszystkich istniejących punktów przywracania.

## <a name="manage-backup"></a>Zarządzanie kopiami zapasowymi

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Czy mogę uzyskać dostęp do migawek wykonywanych przez usługę Azure Backup i zainstalować je? <br/>
Do wszystkich migawek wykonanych przez usługę Azure Backup możesz uzyskać dostęp, wyświetlając migawki w portalu, w programie PowerShell lub przy użyciu interfejsu wiersza polecenia. Aby dowiedzieć się więcej na temat udostępniania migawek udziału usługi Azure Files, zobacz [Omówienie migawek udziału dla usługi Azure Files (wersja zapoznawcza)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Jaki jest maksymalny czas przechowywania, który mogę skonfigurować dla kopii zapasowych? <br/>
Kopie zapasowe udziałów plików platformy Azure tworzone codziennie można przechowywać przez maksymalnie 120 dni.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share-br"></a>Co się stanie, jeśli zmienię zasady tworzenia kopii zapasowych udziału plików platformy Azure? <br/>
Po zastosowaniu nowych zasad do udziału plików będzie obowiązywał harmonogram i okres przechowywania określony przez te nowe zasady. Jeśli okres przechowywania zostanie przedłużony, istniejące punkty odzyskiwania zostaną oznaczone, aby przechowywać je zgodnie z nowymi zasadami. W przypadku skrócenia okresu przechowywania zostaną one oznaczone do oczyszczenia w ramach następnego zadania oczyszczania, a następnie usunięte.

## <a name="see-also"></a>Zobacz też
Te informacje dotyczą wyłącznie tworzenia kopii zapasowych plików usługi Azure Files. Aby dowiedzieć się więcej na temat innych obszarów usługi Azure Backup, zobacz inne najczęściej zadawane pytania dotyczące tworzenia kopii zapasowych:
-  [Recovery Services vault FAQ (Magazyn usługi Recovery Services — często zadawane pytania)](backup-azure-backup-faq.md)
-  [Azure VM backup FAQ (Tworzenie kopii zapasowych maszyn wirtualnych — często zadawane pytania)](backup-azure-vm-backup-faq.md)
-  [Azure Backup agent FAQ (Agent usługi Azure Backup — często zadawane pytania)](backup-azure-file-folder-backup-faq.md)