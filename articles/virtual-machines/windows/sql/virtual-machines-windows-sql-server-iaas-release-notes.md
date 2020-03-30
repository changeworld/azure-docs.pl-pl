---
title: Zmiany dokumentacji programu SQL Server na maszynach wirtualnych platformy Azure| Dokumenty firmy Microsoft
description: Dowiedz się więcej o nowych funkcjach i ulepszeniach programu SQL Server na maszynie Wirtualnej platformy Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/06/2020
ms.openlocfilehash: 27682863a96e2f190a0dafe6e4d783029e987453
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201649"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Zmiany dokumentacji programu SQL Server na maszynach wirtualnych platformy Azure

Platforma Azure umożliwia wdrożenie maszyny wirtualnej (VM) z wbudowanym obrazem programu SQL Server. W tym artykule podsumowano zmiany w dokumentacji związane z nowymi funkcjami i ulepszeniami w ostatnich wersjach [programu SQL Server na maszynach wirtualnych platformy Azure.](https://azure.microsoft.com/services/virtual-machines/sql-server/) 


## <a name="january-2020"></a>Styczeń 2020 r.

| Zmiany | Szczegóły |
| --- | --- |
| **Pomoc techniczna platformy Azure dla instytucji rządowych** | Teraz można zarejestrować maszyny wirtualne programu SQL Server u dostawcy zasobów maszyny wirtualnej SQL dla maszyn wirtualnych hostowanych w chmurze [platformy Azure dla instytucji rządowych.](https://azure.microsoft.com/global-infrastructure/government/) | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Zmiany | Szczegóły |
 --- | --- |
| **Bezpłatna replika odzyskiwania po awarii na platformie Azure** | Jeśli masz pakiet [Software Assurance,](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)można hostować [bezpłatne pasywne wystąpienie](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) odzyskiwania po awarii na platformie Azure dla lokalnego wystąpienia programu SQL Server. | 
| **Rejestracja dostawcy zasobów zbiorczych** | Teraz można [zbiorczo rejestrować](virtual-machines-windows-sql-bulk-register-with-resource-provider.md) maszyny wirtualne SQL z dostawcą zasobów. | 
|**Konfiguracja magazynu zoptymalizowana pod kątem wydajności** | Teraz można [w pełni dostosować konfigurację magazynu](virtual-machines-windows-sql-server-storage-configuration.md#new-vms) podczas tworzenia nowej maszyny Wirtualnej programu SQL Server. |
|**Udział plików premium dla FCI** | Teraz można utworzyć wystąpienie klastra trybu failover przy użyciu [udziału plików premium](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md) zamiast oryginalnej metody bezpośrednie miejsca do [magazynowania](virtual-machines-windows-portal-sql-create-failover-cluster.md). 
| **Dedykowany host platformy Azure** | Maszynę wirtualną programu SQL Server można uruchomić na dedykowanym [hoście platformy Azure.](virtual-machines-windows-sql-dedicated-host.md) | 
| **Przenoszenie maszyny Wirtualnej SQL do innego regionu** | Użyj usługi Azure Site Recovery, aby [przeprowadzić migrację maszyny Wirtualnej programu SQL Server z jednego regionu do drugiego.](virtual-machines-windows-sql-move-different-region.md) |
|  **Nowe tryby instalacji sql iaaS** | Teraz można zainstalować rozszerzenie IaaS programu SQL Server w [trybie lekkim,](virtual-machines-windows-sql-server-agent-extension.md) aby uniknąć ponownego uruchomienia usługi PROGRAMU SQL Server.  |
| **Modyfikacja wersji programu SQL Server** | Teraz można zmienić [właściwość edition](virtual-machines-windows-sql-change-edition.md) dla maszyny Wirtualnej programu SQL Server. |
| **Zmiany dostawcy zasobów maszyny Wirtualnej SQL** | [Maszynę wirtualną programu SQL Server można zarejestrować u dostawcy zasobów maszyny Wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md) przy użyciu nowych trybów IaaS SQL. Ta funkcja obejmuje obrazy [systemu Windows Server 2008.](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes)|
| **Przynieś własne obrazy licencji przy użyciu korzyści hybrydowej platformy Azure** | Obrazy "Bring-your-own-license" wdrożone w portalu Azure Marketplace mogą teraz przełączać swój typ licencji na płatności zgodnie z [rzeczywistym użyciem.](virtual-machines-windows-sql-ahb.md#remarks)| 
| **Nowe zarządzanie maszyną wirtualną programu SQL Server w witrynie Azure portal** | Teraz istnieje sposób zarządzania maszyną wirtualną programu SQL Server w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Zarządzanie maszynami wirtualnymi programu SQL Server w witrynie Azure portal](virtual-machines-windows-sql-manage-portal.md).  | 
| **Rozszerzona obsługa programu SQL Server 2008/2008 R2** | [Rozszerz obsługę](virtual-machines-windows-sql-server-2008-eos-extend-support.md) programów SQL Server 2008 i SQL Server 2008 R2, migrując *tak samo na* maszynie Wirtualnej platformy Azure. | 
| **Niestandardowa obsługa obrazów** | Teraz można zainstalować [rozszerzenie IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) na niestandardowe obrazy systemu operacyjnego i SQL, które oferuje ograniczoną funkcjonalność [elastycznego licencjonowania.](virtual-machines-windows-sql-ahb.md) Podczas rejestrowania obrazu niestandardowego u dostawcy zasobów SQL określ typ licencji jako "AHUB". W przeciwnym razie rejestracja zakończy się niepowodzeniem. | 
| **Obsługa nazwanych wystąpień** | Teraz można użyć [rozszerzenia IaaS programu SQL Server](virtual-machines-windows-sql-server-agent-extension.md#installation) z nazwanym wystąpieniem, jeśli wystąpienie domyślne zostało poprawnie odinstalowane. | 
| **Ulepszenie portalu** | Środowisko portalu Azure do wdrażania maszyny Wirtualnej programu SQL Server zostało przebudowane, aby poprawić użyteczność. Aby uzyskać więcej informacji, zobacz krótki [przewodnik szybki start](quickstart-sql-vm-create-portal.md) i dokładniejsze [instrukcje dotyczące](virtual-machines-windows-portal-sql-server-provision.md) wdrażania maszyny wirtualnej programu SQL Server.|
| **Ulepszenie portalu** | Teraz można zmienić model licencjonowania maszyny Wirtualnej programu SQL Server z płatności zgodnie z rzeczywistym użyciem na własną licencję przy użyciu [portalu Azure.](virtual-machines-windows-sql-ahb.md#vms-already-registered-with-the-resource-provider)|
| **Uproszczenie wdrażania grupy dostępności za pomocą interfejsu wiersza polecenia maszyny Wirtualnej usługi Azure SQL Server** | Teraz jest łatwiejsze niż kiedykolwiek do wdrożenia grupy dostępności na maszynie wirtualnej programu SQL Server na platformie Azure. Za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) można utworzyć klaster trybu failover systemu Windows, wewnętrzny moduł równoważenia obciążenia i detektory grup dostępności z wiersza polecenia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy dostępności zawsze włączone dla programu SQL Server na maszynie wirtualnej platformy Azure za pomocą interfejsu wiersza polecenia maszyny Wirtualnej programu Azure.](virtual-machines-windows-sql-availability-group-cli.md) | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Zmiany | Szczegóły |
| --- | --- |
|  **Nowy dostawca zasobów dla klastra programu SQL Server** | Nowy dostawca zasobów (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definiuje metadane klastra trybu failover systemu Windows. Dołączanie do maszyny Wirtualnej programu SQL Server z *programem SqlVirtualMachineGroups* uruchamia usługę WSFC (Windows Server Failover Cluster) i dołącza do maszyny Wirtualnej do klastra.  |
| **Zautomatyzowana konfiguracja wdrażania grupy dostępności za pomocą szablonów szybki start platformy Azure** |Teraz można utworzyć klaster trybu failover systemu Windows, dołączyć do niego maszyny wirtualne programu SQL Server, utworzyć odbiornik i skonfigurować wewnętrzny moduł równoważenia obciążenia za pomocą dwóch szablonów szybkiego startu platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy dostępności zawsze włączone dla programu SQL Server na maszynie wirtualnej platformy Azure za pomocą szablonów szybkiego startu platformy Azure.](virtual-machines-windows-sql-availability-group-quickstart-template.md) | 
| **Automatyczna rejestracja u dostawcy zasobów maszyny Wirtualnej SQL** | Maszyny wirtualne programu SQL Server wdrożone po tym miesiącu są automatycznie rejestrowane u nowego dostawcy zasobów programu SQL Server. Maszyny wirtualne programu SQL Server wdrożone przed tym miesiącem nadal muszą być rejestrowane ręcznie. Aby uzyskać więcej informacji, zobacz [Rejestrowanie maszyny wirtualnej programu SQL Server na platformie Azure u dostawcy zasobów maszyny wirtualnej SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)|
|**Nowy dostawca zasobów maszyny wirtualnej SQL** |  Nowy dostawca zasobów (Microsoft.SqlVirtualMachine) zapewnia lepsze zarządzanie maszynami wirtualnymi programu SQL Server. Aby uzyskać więcej informacji na temat rejestrowania maszyn wirtualnych, zobacz [Rejestrowanie maszyny wirtualnej programu SQL Server na platformie Azure u dostawcy zasobów maszyn wirtualnych SQL.](virtual-machines-windows-sql-register-with-resource-provider.md) |
|**Model licencjonowania przełączników** | Teraz można przełączać się między modelami pay-per-usage i bring-your-own-license dla maszyny Wirtualnej programu SQL Server przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania maszyny wirtualnej programu SQL Server na platformie Azure](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Zasoby dodatkowe

**Maszyny wirtualne z systemem Windows**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Aprowizuj maszynę wirtualną systemu Windows z systemem SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Najważniejsze wskazówki dotyczące wydajności programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie rozwoju programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprowizuj maszynę wirtualną programu SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
