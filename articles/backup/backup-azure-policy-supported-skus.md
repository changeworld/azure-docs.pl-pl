---
title: Obsługiwane jednostki SKU maszyny wirtualnej dla usługi Azure Policy
description: Artykuł opisujący obsługiwane jednostki SKU maszyny Wirtualnej (według wydawcy, oferty obrazów i jednostki SKU obrazów), które są obsługiwane dla wbudowanych zasad platformy Azure dostarczanych przez program Kopia zapasowa
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980116"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Obsługiwane jednostki SKU maszyny wirtualnej dla usługi Azure Policy

Usługa Azure Backup udostępnia wbudowane zasady (przy użyciu usługi Azure Policy), które można przypisać do **wszystkich maszyn wirtualnych platformy Azure w określonej lokalizacji w ramach subskrypcji lub grupy zasobów.** Gdy ta zasada jest przypisana do danego zakresu, wszystkie nowe maszyny wirtualne utworzone w tym zakresie są automatycznie konfigurowane do tworzenia kopii zapasowych do **istniejącego magazynu w tej samej lokalizacji i subskrypcji**. W poniższej tabeli wymieniono wszystkie jednostki SKU maszyn wirtualnych obsługiwane przez te zasady.

### <a name="supported-vms"></a>**Obsługiwane maszyny wirtualne**

**Nazwa zasad:** Konfigurowanie kopii zapasowej na maszynach wirtualnych lokalizacji do istniejącego centralnego przechowalni w tej samej lokalizacji

Wydawca obrazów | Oferta graficzna | SKU obrazu
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centrum danych systemu Windows Server 2012 (centrum danych 2012)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centrum danych systemu Windows Server 2012 (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centrum danych systemu Windows Server 2012 R2 (centrum danych 2012-R2)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centrum danych systemu Windows Server 2012 R2 (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centrum danych systemu Windows Server 2016 (centrum danych 2016)
MicrosoftWindowsServer | WindowsServer | Centrum danych systemu Windows Server 2016 — rdzeń serwera (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centrum danych systemu Windows Server 2016 — Core serwera (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centrum danych systemu Windows Server 2016 (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core z kontenerami (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Host sesji usług pulpitu zdalnego systemu Windows Server 2016 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Centrum danych systemu Windows Server 2019 (centrum danych 2019)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core z kontenerami (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core with Containers (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centrum danych systemu Windows Server 2019 (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centrum danych systemu Windows Server 2019 z kontenerami (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Centrum danych systemu Windows Server 2019 z kontenerami (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Centrum danych systemu Windows Server 2019 (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiRoczna | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiRoczna | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiRoczna | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2016SP1-WS2016 | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2016-WS2016  | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2016-WS2012R2 | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | Wszystkie jednostki SKU obrazu
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | Wszystkie jednostki SKU obrazu
Serwer MicrosoftRServer | Serwer MLServer-WS2016 | Wszystkie jednostki SKU obrazu
MicrosoftVisualStudio | Visualstudio | Wszystkie jednostki SKU obrazu
MicrosoftVisualStudio | Windows | Wszystkie jednostki SKU obrazu
Program MicrosoftDynamicsAX | Dynamics  | Pre-Req-AX7-Onebox-U8
microsoft-reklamy | windows-data-science-vm | Wszystkie jednostki SKU obrazu
MicrosoftWindowsDesktop | Windows-10 | Wszystkie jednostki SKU obrazu
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-Priorytet | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-Priorytet | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-Standard | 12.X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS–LVM | 6.X, 7.X
OpenLogic | CentOS–SRIOV | 6.X, 7.X
Cloudera | cloudera-centos-os | 7.X
