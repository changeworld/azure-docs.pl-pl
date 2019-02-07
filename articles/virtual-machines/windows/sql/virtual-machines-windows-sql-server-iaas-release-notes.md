---
title: Program SQL Server na maszynie Wirtualnej platformy Azure — informacje o wersji | Dokumentacja firmy Microsoft
description: Dowiedz się więcej na temat nowych funkcji i ulepszeń programu SQL Server na Maszynie wirtualnej platformy Azure
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2018
ms.author: mathoma
ms.openlocfilehash: 7c854759e9a0ba13b698dd07dabb25100895bc85
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766881"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Program SQL Server na maszynie wirtualnej platformy Azure — informacje o wersji

Platforma Azure umożliwia wdrażanie maszyny wirtualnej za pomocą obrazu programu SQL Server, wbudowane. Ten artykuł zawiera listę nowych funkcji i ulepszeń, które można oczekiwać, że w najnowszej wersji programu SQL Server wdrożony na maszynie wirtualnej platformy Azure. 

## <a name="december-2018"></a>Grudzień 2018 r.

| **Change** | Szczegóły |
| --- | --- |
| **Nowego dostawcę zasobów grupy klastra SQL** | Brak nowego dostawcę zasobów (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) definiującego metadane dotyczące klastra pracy awaryjnej Windows. Przyłączanie maszyny Wirtualnej z programu SQL Server do *SqlVirtualMachineGroups* usługę klastra pracy awaryjnej Windows używa do ładowania i dołączania maszyny Wirtualnej do klastra.  |
|**Automatyzowanie konfigurowania wdrożenia grupy dostępności przy użyciu szablonów szybkiego startu platformy Azure** |Teraz istnieje możliwość tworzenia klastra pracy awaryjnej Windows, przyłączyć się do niej maszyny wirtualne SQL Server, utwórz odbiornik i konfigurowanie wewnętrznego modułu równoważenia obciążenia przy użyciu dwa szablony szybkiego startu platformy Azure. Aby uzyskać więcej informacji, zobacz [tworzenia usługi WSFC, odbiornik i konfigurowanie wewnętrznego modułu równoważenia obciążenia dla zawsze włączonej grupy dostępności na maszynę Wirtualną programu SQL Server przy użyciu szablonu szybkiego startu platformy Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Rejestracja dostawcy zasobów automatyczne maszyny Wirtualnej SQL** | Maszyny wirtualne SQL Server wdrożony po miesiącem są automatycznie rejestrowane za pomocą nowego dostawcę zasobów programu SQL Server. Maszyny wirtualne SQL Server wdrożony przed miesiącem nadal należy ręcznie zarejestrować. Aby uzyskać więcej informacji, zobacz [zarejestrować istniejącej maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Listopada 2018 r.

| **Change** | Szczegóły |
| --- | --- |
| **Nowego dostawcę zasobów maszyny Wirtualnej SQL** |  Brak nowego dostawcę zasobów dla maszyny wirtualne SQL Server (Microsoft.SqlVirtualMachine) umożliwiający lepszego zarządzania maszyny wirtualnej programu SQL Server. Aby uzyskać więcej informacji na temat rejestrowania maszyny Wirtualnej, zobacz [zarejestrować istniejącej maszyny Wirtualnej SQL przy użyciu nowego dostawcę zasobów](virtual-machines-windows-sql-ahb.md#register-existing-sql-server-vm-with-sql-resource-provider). |
|**Przełącz model licencjonowania** |Możesz teraz przełączać się między model płatności za użycie i bring your own licencji dla maszyny Wirtualnej SQL przy użyciu wiersza polecenia platformy Azure lub programu Powershell. Aby uzyskać więcej informacji, zobacz [Jak zmienić model licencjonowania dla maszyny wirtualnej SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Dodatkowe zasoby

**Maszyny wirtualne z systemem Windows**:

* [Omówienie programu SQL Server na maszynie Wirtualnej Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Aprowizowanie Windows serwer SQL maszyny Wirtualnej](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrowanie bazy danych do programu SQL Server na maszynie Wirtualnej platformy Azure](virtual-machines-windows-migrate-sql.md)
* [Wysoka dostępność i odzyskiwanie po awarii programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Najlepsze rozwiązania w zakresie wydajności dla programu SQL Server w usłudze Azure Virtual Machines](virtual-machines-windows-sql-performance.md)
* [Wzorce aplikacji i strategie programowania dla programu SQL Server na maszynach wirtualnych platformy Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**Maszyny wirtualne z systemem Linux**:

* [Omówienie programu SQL Server na maszynie Wirtualnej systemu Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprowizowanie maszyny Wirtualnej systemu Linux z programu SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Często zadawane pytania (system Linux)](../../linux/sql/sql-server-linux-faq.md)
* [Dokumentacja programu SQL Server w systemie Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
