---
title: Często zadawane pytania dotyczące serwera kopii zapasowych i programu DPM platformy Azure
description: W tym artykule odnajduj odpowiedzi na często zadawane pytania dotyczące programu Microsoft Azure Backup Server (MABS) i DPM (Data Protection Manager).
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 35957a1e8a3d6c3d9be06d9d44dbcd47efa0e6ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173156"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Serwer kopii zapasowych platformy Azure i program DPM — często zadawane pytania

## <a name="general-questions"></a>Pytania ogólne

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące serwera kopii zapasowych platformy Azure i programu DPM.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Czy można używać programu Azure Backup Server do tworzenia kopii zapasowej z odzyskiwaniem systemu od zera (BMR) serwera fizycznego?

Tak.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Czy mogę zarejestrować serwer w wielu magazynach?

Nie. Serwer DPM lub Usługa Azure Backup może być zarejestrowany tylko w jednym magazynie.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Czy można używać programu DPM do utworzenia kopii zapasowej aplikacji w usłudze Azure Stack?

Nie. Za pomocą usługi Azure Backup można chronić usługę Azure Stack, usługa Azure Backup nie obsługuje tworzenia kopii zapasowych aplikacji w usłudze Azure Stack przy użyciu programu DPM.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Jeśli mam zainstalowany agent usługi Azure Backup w celu ochrony moich plików i folderów, czy mogę zainstalować program System Center DPM w celu utworzenia kopii zapasowej obciążeń lokalnych na platformie Azure?

Tak. Należy jednak najpierw skonfigurować program DPM, a następnie zainstalować agenta usługi Azure Backup.  Instalowanie składników w tej kolejności gwarantuje, że agent usługi Azure Backup współpracuje z programem DPM. Instalowanie agenta przed zainstalowaniem programu DPM nie jest zalecane ani obsługiwane.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Dlaczego nie mogę dodać zewnętrznego serwera programu DPM po zainstalowaniu ur7 i najnowszego agenta usługi Azure Backup?

W przypadku serwerów programu DPM ze źródłami danych chronionymi w chmurze (przy użyciu pakietu zbiorczego aktualizacji wcześniejszego niż pakiet zbiorczy aktualizacji 7) należy poczekać co najmniej jeden dzień po zainstalowaniu agenta UR7 i najnowszego agenta usługi Azure Backup, aby uruchomić **serwer Dodaj zewnętrzny program DPM**. Jednodniowy okres czasu jest potrzebny do przekazania metadanych grup ochrony programu DPM na platformę Azure. Metadane grupy ochrony są przekazywane po raz pierwszy za pośrednictwem zadania nocnego.

## <a name="vmware-and-hyper-v-backup"></a>Kopia zapasowa VMware i Hyper-V

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Czy można tworzyć kopie zapasowe serwerów VMware vCenter na platformie Azure?

Tak. Za pomocą usługi Azure Backup Server można wykonać kopię zapasową hostów VMware vCenter Server i ESXi na platformie Azure.

- [Dowiedz się więcej](backup-mabs-protection-matrix.md) o obsługiwanych wersjach.
- [Wykonaj następujące kroki,](backup-azure-backup-server-vmware.md) aby wykonać zapasową kopii zapasowej serwera VMware.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Czy potrzebuję osobnej licencji, aby odzyskać pełny lokalny klaster VMware/Hyper-V?

Nie potrzebujesz oddzielnego licencjonowania dla ochrony VMware/Hyper-V.

- Jeśli jesteś klientem programu System Center, użyj Programu System Center Data Protection Manager (DPM) w celu ochrony maszyn wirtualnych VMware.
- Jeśli nie jesteś klientem programu System Center, możesz użyć usługi Azure Backup Server (płatność zgodnie z rzeczywistym użyciem), aby chronić maszyny wirtualne VMware.

## <a name="sharepoint"></a>Program SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Czy mogę odzyskać element programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu programu SQL AlwaysOn (z ochroną na dysku)?

Tak, element można odzyskać w oryginalnej witrynie programu SharePoint.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Czy można odzyskać bazę danych programu SharePoint do oryginalnej lokalizacji, jeśli program SharePoint jest skonfigurowany przy użyciu programu SQL AlwaysOn?

Ponieważ bazy danych programu SharePoint są skonfigurowane w programie SQL AlwaysOn, nie można ich modyfikować, chyba że grupa dostępności zostanie usunięta. W rezultacie program DPM nie może przywrócić bazy danych do oryginalnej lokalizacji. Bazę danych programu SQL Server można odzyskać w innym wystąpieniu programu SQL Server.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi często zadawanymi pytaniami:

- [Dowiedz się więcej](backup-support-matrix-mabs-dpm.md) o usłudze Azure Backup Server i macierzy obsługi programu DPM.
- [Dowiedz się więcej](backup-azure-mabs-troubleshoot.md) o wskazówki dotyczące rozwiązywania problemów z serwerem kopii zapasowych i programem DPM platformy Azure.
