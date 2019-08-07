---
title: SQL Server na temat informacji o wersji maszyny wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat nowych funkcji i ulepszeń SQL Server na maszynie wirtualnej platformy Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/01/2019
ms.openlocfilehash: e656cd4d901ad9f3180963047a85cd980e56df8b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774212"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Informacje o wersji usługi Azure Virtual Machine SQL Server

System Azure umożliwia wdrożenie maszyny wirtualnej przy użyciu obrazu SQL Server wbudowanego. Ten artykuł zawiera podsumowanie nowych funkcji i ulepszeń w ostatnich wersjach [SQL Server na maszynach wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Artykuł zawiera również listę istotnych aktualizacji zawartości, które nie są bezpośrednio związane z wersją, ale opublikowane w tym samym przedziale czasowym. Aby uzyskać ulepszenia dotyczące innych usług platformy Azure, zobacz [Aktualizacje usług](https://azure.microsoft.com/updates)

## <a name="july-2019"></a>Lipiec 2019

### <a name="documentation-improvements"></a>Udoskonalenia dokumentacji

| Dokumentacja | Szczegóły |
| --- | --- |
| **Przenoszenie maszyny wirtualnej SQL do innego regionu** | Użyj Azure Site Recovery, aby [migrować maszynę wirtualną SQL Server z jednego regionu do innego](virtual-machines-windows-sql-move-different-region.md). |
| &nbsp; | &nbsp; |

## <a name="june-2019"></a>Czerwiec 2019

### <a name="service-improvements"></a>Ulepszenia usługi

| Ulepszenia usługi | Szczegóły |
| --- | --- |
| **Nowe tryby instalacji programu SQL IaaS** | Teraz można zainstalować rozszerzenie SQL IaaS w [trybie uproszczonym](virtual-machines-windows-sql-server-agent-extension.md) , aby uniknąć ponownego uruchamiania usługi SQL Server.  |
| **Modyfikacja wersji SQL Server** | Teraz możesz zmienić [Właściwość wersji](virtual-machines-windows-sql-change-edition.md) dla maszyny wirtualnej SQL Server. |
| **Zmiany RP maszyny wirtualnej SQL** | Maszynę [wirtualną SQL Server można zarejestrować za pomocą obrazów dostawcy zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) — nawet dla [systemu Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) — przy użyciu nowych trybów IaaS języka SQL. |
| **BYOL obrazów przy użyciu — AHUB** | Obrazy BYOL wdrożone z portalu Marketplace mogą teraz zmienić [Typ licencji na "PAYG"](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>2019 maja

### <a name="service-improvements"></a>Ulepszenia usługi

| Ulepszenia usługi | Szczegóły |
| --- | --- |
| **Nowe zarządzanie MASZYNami wirtualnymi SQL w Azure Portal** | Obecnie istnieje nowy sposób zarządzania SQL Server maszyną wirtualną w Azure Portal. Aby uzyskać więcej informacji, zobacz [zarządzanie maszyną wirtualną SQL Server w Azure Portal](virtual-machines-windows-sql-manage-portal.md).  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Udoskonalenia dokumentacji

| Dokumentacja | Szczegóły |
| --- | --- |
| **Nowe Zarządzanie portalem maszyny wirtualnej SQL** | Informacje o dziesiątych artykułach zostały zaktualizowane do nowego środowiska portalu zarządzania maszyną wirtualną SQL. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Kwiecień 2019

### <a name="service-improvements"></a>Ulepszenia usługi

| Ulepszenia usługi | Szczegóły |
| --- | --- |
| **Rozszerzona pomoc techniczna dla SQL Server 2008/2008R2** | [Rozszerzona pomoc techniczna](virtual-machines-windows-sql-server-2008-eos-extend-support.md) dla SQL Server 2008 i SQL Server 2008 R2 przez Migrowanie *jako-* do maszyny wirtualnej platformy Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Marzec 2019

| Ulepszenia usługi | Szczegóły |
| --- | --- |
| **Obsługa obrazów niestandardowych** | Teraz możesz zainstalować [rozszerzenie SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) w przypadku niestandardowych systemów operacyjnych i obrazów SQL, które oferują ograniczoną funkcjonalność elastycznej [licencji](virtual-machines-windows-sql-ahb.md). Podczas rejestrowania niestandardowego obrazu przy użyciu dostawcy zasobów SQL należy określić typ licencji jako "— AHUB", ponieważ w przeciwnym razie rejestracja zakończy się niepowodzeniem. | 
| **Obsługa nazwanych wystąpień** | Teraz można użyć [rozszerzenia SQL IaaS](virtual-machines-windows-sql-server-agent-extension.md#installation) z nazwanym wystąpieniem, jeśli domyślne wystąpienie zostało odinstalowane prawidłowo. | 
| **Rozszerzenie portalu** | Azure Portal środowisko wdrażania SQL Server maszyny wirtualnej zostało odnowionych w celu poprawy użyteczności. Aby uzyskać więcej informacji, zobacz krótkie Przewodnik [szybkiego startu](quickstart-sql-vm-create-portal.md) i dokładniejsze [instrukcje](virtual-machines-windows-portal-sql-server-provision.md) dotyczące wdrażania SQL Server maszyny wirtualnej.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>Luty 2019

| Ulepszenia usługi | Szczegóły |
| --- | --- |
| **Udoskonalenie portalu** | Teraz można zmienić model licencjonowania dla maszyny wirtualnej SQL Server z usługi płatność zgodnie z rzeczywistym użyciem, aby przenieść własną licencję przy użyciu [Azure Portal](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Uproszczenie wdrażania przy użyciu interfejsu wiersza polecenia usługi Azure SQL VM** | Wdrażanie grupy dostępności na maszynie wirtualnej SQL Server na platformie Azure jest teraz łatwiejsze niż kiedykolwiek wcześniej. [Interfejs wiersza polecenia usługi Azure SQL VM](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) umożliwia tworzenie wszystkich odbiorników WSFC, ILB i AG z wiersza poleceń i w czasie rejestracji. Aby uzyskać więcej informacji, zobacz [Używanie interfejsu wiersza polecenia usługi Azure SQL VM do konfigurowania zawsze włączonych grup dostępności dla SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Grudzień 2018 r.

| Ulepszenia usługi | Szczegóły |
| --- | --- |
| **Nowy dostawca zasobów grupy klastrów SQL** | Nowy dostawca zasobów (Microsoft. SqlVirtualMachine/SqlVirtualMachineGroups), który definiuje metadane klastra trybu failover systemu Windows. Przyłączanie maszyny wirtualnej SQL Server do *SqlVirtualMachineGroups* Bootstrap usługa klastrowania trybu failover systemu Windows i przyłączanie maszyny wirtualnej do klastra.  |
|**Automatyzowanie konfigurowania wdrożenia grupy dostępności przy użyciu szablonów szybkiego startu platformy Azure** |Teraz można utworzyć klaster trybu failover systemu Windows, dołączyć do niego SQL Server maszyny wirtualne, utworzyć odbiornik i skonfigurować Load Balancer wewnętrzny przy użyciu dwóch szablonów szybkiego startu platformy Azure. Aby uzyskać więcej informacji, zobacz [Korzystanie z szablonu szybkiego startu platformy Azure w celu skonfigurowania zawsze włączone grupy dostępności dla SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Automatyczna rejestracja dostawcy zasobów maszyny wirtualnej SQL** | SQL Server maszyny wirtualne wdrożone po tym miesiącu są automatycznie rejestrowane przy użyciu nowego dostawcy zasobów SQL Server. SQL Server maszyny wirtualne wdrożone przed tym miesiącem nadal muszą zostać zarejestrowane ręcznie. Aby uzyskać więcej informacji, zobacz [Rejestrowanie istniejącej maszyny wirtualnej SQL z dostawcą zasobów maszyny wirtualnej SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Listopad 2018

| Ulepszenia usługi | Szczegóły |
| --- | --- |
| **Nowy dostawca zasobów maszyny wirtualnej SQL** |  Nowy dostawca zasobów dla SQL Server maszyn wirtualnych (Microsoft. SqlVirtualMachine), który zapewnia lepsze zarządzanie SQL Server maszynie wirtualnej. Aby uzyskać więcej informacji na temat rejestrowania maszyny wirtualnej, zobacz [Rejestrowanie istniejącej maszyny wirtualnej SQL przy użyciu nowego dostawcy zasobów](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Przełącz model licencjonowania** | Teraz możesz przełączać się między modelem licencji na korzystanie z funkcji płatność za użycie i przenoszeniem własnych dla maszyny wirtualnej SQL przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Dodatkowe zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do SQL Server na maszynie wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie programowania dla SQL Server na platformie Azure Virtual Machines](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie SQL Server na maszynie wirtualnej z systemem Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Inicjowanie obsługi administracyjnej maszyny wirtualnej z systemem SQL Server Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
