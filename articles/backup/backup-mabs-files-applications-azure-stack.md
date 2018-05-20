---
title: Utwórz kopię zapasową plików stosu Azure i aplikacji | Dokumentacja firmy Microsoft
description: Kopia zapasowa Azure umożliwia tworzenie kopii zapasowej i odzyskiwanie plików stosu Azure i aplikacji do środowiska Azure stosu.
services: backup
documentationcenter: ''
author: adiganmsft
manager: shivamg
editor: ''
keyword: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/11/2018
ms.author: adigan,markgal
ms.openlocfilehash: 19067b40e8e87c160515d13bb490e7c1604788b6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Kopie zapasowe plików i aplikacji na stosie Azure
Kopia zapasowa Azure umożliwia ochronę (lub utworzyć kopię zapasową) plików i aplikacji na stosie Azure. Aby utworzyć kopię zapasową plików i aplikacji, należy zainstalować serwer kopii zapasowej Microsoft Azure jako maszynę wirtualną działającą na stosie Azure. Można chronić wszystkie aplikacje uruchomione na każdym serwerze stosu Azure w tej samej sieci wirtualnej. Po zainstalowaniu serwera kopii zapasowej Azure, Dodawanie dysku systemu Azure, aby zwiększyć magazynu lokalnego, która jest dostępna dla krótkoterminowej kopii zapasowej danych. Serwer kopii zapasowej systemu Azure korzysta z magazynu Azure w celu przechowywania długoterminowego.

> [!NOTE]
> Jeśli serwer usługi Kopia zapasowa Azure i System Center Data Protection Manager (DPM) są podobne, program DPM nie jest obsługiwane do użycia z programem Azure stosu.
>


## <a name="azure-backup-server-protection-matrix"></a>Macierz ochrony usługi Azure Backup Server
Serwer kopii zapasowej systemu Azure chroni następujące obciążenia maszyny wirtualnej Azure stosu.

| Chronionego źródła danych | Ochrona i odzyskiwanie |
| --------------------- | ----------------------- |
| Windows Server częściowej roczna kanału - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2016 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 R2 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2012 — Entprise/Datacenter/Standard | Woluminy, pliki, foldery |
| Windows Server 2008 R2 - Enterprise/Datacenter/Standard | Woluminy, pliki, foldery |
| SQL Server 2016 | Database (Baza danych) |
| SQL Server 2014 | Database (Baza danych) |
| SQL Server 2012 z dodatkiem SP1 | Database (Baza danych) |
| SharePoint 2013 | Farma, baza danych, serwera sieci Web, serwer sieci web |
| SharePoint 2010 | Farma, baza danych, serwera sieci Web, serwer sieci web |


## <a name="install-azure-backup-server"></a>Zainstaluj serwer kopii zapasowej systemu Azure
Aby zainstalować serwer kopii zapasowej Azure na maszynie wirtualnej platformy Azure stosu, zapoznaj się z artykułem [przygotowanie do tworzenia kopii zapasowych obciążeń przy użyciu serwera usługi Kopia zapasowa Azure](backup-azure-microsoft-azure-backup.md). Przed zainstalowaniem i skonfigurowaniem serwera usługi Kopia zapasowa Azure, należy pamiętać o następujących czynności:

### <a name="determining-size-of-virtual-machine"></a>Ustalanie rozmiaru maszyny wirtualnej
Aby uruchomić serwera usługi Kopia zapasowa Azure na maszynie wirtualnej platformy Azure stosu, użyj rozmiaru A2 lub większym. Aby uzyskać pomoc przy wyborze rozmiaru maszyny wirtualnej, należy pobrać [kalkulatora rozmiaru maszyny Wirtualnej Azure stosu](https://www.microsoft.com/download/details.aspx?id=56832).

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Sieci wirtualne na maszynach wirtualnych Azure stosu
Wszystkie maszyny wirtualne używane w stos Azure obciążenia muszą należeć do tej samej sieci wirtualnej platformy Azure i subskrypcję Azure.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Przechowywanie danych kopii zapasowej na dysku lokalnym oraz na platformie Azure
Serwer kopii zapasowej systemu Azure przechowuje dane kopii zapasowej na dyskach platformy Azure dołączonych do maszyny wirtualnej dla operacyjnych dotyczących odzyskiwania. Gdy dyski i miejsca do magazynowania są dołączone do maszyny wirtualnej, serwer kopii zapasowej Azure zarządza magazynu. Ilość miejsca w magazynie danych kopii zapasowych zależy od liczby i rozmiaru dysków dołączonych do każdego [maszyny wirtualnej Azure stosu](../azure-stack/user/azure-stack-storage-overview.md). Każdego rozmiaru maszyny Wirtualnej Azure stosu ma maksymalną liczbę dysków, które może zostać dołączony do maszyny wirtualnej. Na przykład A2 jest cztery dyski. A3 jest ośmiu dysków. A4 jest 16 dysków. Ponownie rozmiaru i liczby dysków określa puli całkowita magazynu kopii zapasowych.

> [!IMPORTANT]
> Należy **nie** zachowania danych operacyjnych dotyczących odzyskiwania (kopia zapasowa) na dyskach dołączone do serwera kopii zapasowej Azure przez więcej niż pięć dni.
>

Przechowywania danych kopii zapasowej na platformie Azure zmniejsza infrastruktury kopii zapasowych na stosie Azure. Jeśli dane są więcej niż pięć dni, powinny być przechowywane na platformie Azure.

Aby przechowywać dane kopii zapasowej na platformie Azure, należy utworzyć lub użyć magazynu usług odzyskiwania. Podczas przygotowywania do tworzenia kopii zapasowych obciążeń serwera usługi Kopia zapasowa Azure możesz [skonfigurować magazyn usług odzyskiwania](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Po skonfigurowaniu każdym uruchomieniu zadania tworzenia kopii zapasowej, punkt odzyskiwania jest tworzony w magazynie. Każdy magazyn usług odzyskiwania zawiera punkty odzyskiwania do 9999. W zależności od liczby punktów odzyskiwania utworzonych i jak długo są przechowywane przez wiele lat można zachować dane kopii zapasowej. Na przykład można utworzyć miesięczne punktów odzyskiwania i zachować je do pięciu lat.
 
### <a name="using-sql-server"></a>Za pomocą programu SQL Server
Jeśli chcesz użyć zdalnego serwera SQL dla bazy danych serwera usługi Kopia zapasowa Azure, wybierz tylko stosu maszyny Wirtualnej platformy Azure działa program SQL Server.

### <a name="azure-backup-server-vm-performance"></a>Wydajność maszyny Wirtualnej serwera kopii zapasowej platformy Azure
Jeśli udostępniony innym maszynom wirtualnym, rozmiar konta magazynu i limity liczby mogą obniżyć wydajność maszyny wirtualnej serwera usługi Kopia zapasowa Azure. Z tego powodu należy użyć oddzielnego konta magazynu dla maszyny wirtualnej Azure Utwórz kopię zapasową serwera. Agent usługi Kopia zapasowa Azure uruchomionych na serwerze kopii zapasowej Azure wymaga tymczasowego magazynu dla:
    - własnego użytku (lokalizacja pamięci podręcznej),
    - danych przywróconych z chmury (lokalny obszar przemieszczania)
  
### <a name="configuring-azure-backup-temporary-disk-storage"></a>Konfigurowanie magazynu dysku tymczasowym kopia zapasowa Azure
Każda maszyna wirtualna Azure stosu jest dostarczany z magazynu na dysku tymczasowym, który jest dostępny dla użytkownika jako wolumin `D:\`. Lokalny obszar przemieszczania wymagany dla usługi Kopia zapasowa Azure można skonfigurować w celu znajdują się w `D:\`, i lokalizację pamięci podręcznej można umieścić na `C:\`. W ten sposób magazynu nie trzeba używać przeciwną na dyskach danych dołączonych do maszyny wirtualnej serwera usługi Kopia zapasowa Azure.

### <a name="scaling-deployment"></a>Skalowania wdrożenia
Jeśli chcesz skalowania wdrożenia, dostępne są następujące opcje:
  - Skalowanie w górę — zwiększenie rozmiaru maszyny wirtualnej serwera usługi Kopia zapasowa Azure z serii D serii i zwiększyć Magazyn lokalny [zgodnie z instrukcjami maszyny wirtualnej Azure stosu](../azure-stack/user/azure-stack-manage-vm-disks.md).
  - Korzystaj — wysyłanie starszych danych do serwera usługi Kopia zapasowa Azure i zachowywanie tylko najnowszych danych w magazynie dołączonym do serwera kopii zapasowej Azure.
  - Skalowanie w poziomie — dodawanie kolejnych serwerów kopia zapasowa Azure w celu włączenia ochrony obciążeń.

## <a name="see-also"></a>Zobacz także
Uzyskać na ochrona innych obciążeń za pomocą serwera usługi Kopia zapasowa Azure zobacz następujące artykuły:
- [Tworzenie kopii zapasowej farmy programu SharePoint](backup-azure-backup-sharepoint-mabs.md)
- [Tworzenie kopii zapasowych programu SQL server](backup-azure-sql-mabs.md)
