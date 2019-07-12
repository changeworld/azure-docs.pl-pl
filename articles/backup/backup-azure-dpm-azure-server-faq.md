---
title: Azure Backup Server i program DPM — często zadawane pytania
description: 'Odpowiedzi na często zadawane pytania dotyczące: Usługa Azure Backup Server i programu DPM.'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 7a598038ee435b67b9ad8f06bdec2490bc1c53c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705108"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Usługa Azure Backup Server i DPM — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Azure Backup Server i program DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Czy można używać programu Azure Backup Server do tworzenia kopii zapasowej z odzyskiwaniem systemu od zera (BMR) serwera fizycznego? <br/>
Tak.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Czy można zarejestrować serwer w wielu magazynach?
Nie. Serwer programu DPM lub usługi Azure Backup można zarejestrować tylko w jednym magazynie.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Czy można użyć programu DPM, aby utworzyć kopię zapasową aplikacji w usłudze Azure Stack?
Nie. Usługa Azure Backup umożliwia ochronę usługi Azure Stack, usługa Azure Backup nie obsługuje przy użyciu programu DPM, aby utworzyć kopię zapasową aplikacji w usłudze Azure Stack.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Jeśli został zainstalowany agent usługi Azure Backup, aby chronić pliki i foldery, można zainstalować programu System Center DPM do tworzenia kopii zapasowych obciążeń lokalnych do platformy Azure?
Tak. Jednak należy najpierw skonfigurować program DPM, a następnie zainstaluj agenta usługi Azure Backup.  Instalowanie składników w tej kolejności zapewnia, że usługi Azure Backup agent działa przy użyciu programu DPM. Instalowanie agenta przed instalacją programu DPM nie jest zalecana lub jest obsługiwana.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Dlaczego nie można dodać zewnętrznego serwera programu DPM po zainstalowaniu pakietu zbiorczego aktualizacji 7 i najnowszą wersję agenta usługi Azure Backup?
Dla serwerów programu DPM ze źródłami danych, które są chronione w chmurze (przy użyciu pakietu zbiorczego aktualizacji starszych niż Update Rollup 7), należy poczekać co najmniej jeden dzień po zainstalowaniu pakietu zbiorczego aktualizacji 7 i najnowszą wersję agenta usługi Kopia zapasowa Azure, Rozpocznij **serwer Dodaj zewnętrzny program DPM**. Jeden dzień okresu jest potrzebny do przekazywania metadanych grup ochrony programu DPM na platformie Azure. Metadane grupy ochrony jest przekazywany za pośrednictwem nocne zadanie po raz pierwszy.

## <a name="vmware-and-hyper-v-backup"></a>Kopia zapasowa VMware i funkcji Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Czy można tworzyć kopie zapasowe serwerów VMware vCenter na platformie Azure?
Tak. Usługi Azure Backup Server umożliwia tworzenie kopii zapasowej serwera VMware vCenter i hosty ESXi na platformie Azure.

- [Dowiedz się więcej](backup-mabs-protection-matrix.md) o obsługiwanych wersjach.
- [Wykonaj następujące kroki](backup-azure-backup-server-vmware.md) do tworzenia kopii zapasowej serwera VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Czy muszę mieć oddzielnej licencji, aby odzyskać pełną lokalny klaster VMware/funkcji Hyper-V?
Nie potrzebujesz oddzielnych licencji na ochronę programu VMware/funkcji Hyper-V.

- Jeśli jesteś klientem programu System Center, należy użyć System Center Data Protection Manager (DPM) do ochrony maszyn wirtualnych VMware.
- Jeśli nie jesteś klientem programu System Center, można użyć usługi Azure Backup Server (płatność za rzeczywiste użycie) do ochrony maszyn wirtualnych VMware.


## <a name="sharepoint"></a>Program SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Czy można odzyskać element programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn (Ochrona na dysku)?
Tak, element można odzyskać do oryginalnej witryny programu SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Czy można odzyskać bazy danych programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu funkcji SQL AlwaysOn?
Bazy danych programu SharePoint są skonfigurowane w funkcji SQL AlwaysOn, nie można modyfikować, chyba że grupa dostępności zostanie usunięta. W rezultacie program DPM nie można przywrócić bazę danych do oryginalnej lokalizacji. Może odzyskać bazę danych programu SQL Server do innego wystąpienia programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Przeczytaj inne — często zadawane pytania:

- [Dowiedz się więcej](backup-support-matrix-mabs-dpm.md) macierz obsługi o usłudze Azure Backup Server i program DPM.
- [Dowiedz się więcej](backup-azure-mabs-troubleshoot.md) dotyczące usługi Azure Backup Server i program DPM wskazówki dotyczące rozwiązywania problemów.
