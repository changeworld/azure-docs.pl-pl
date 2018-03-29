---
title: Omówienie programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux | Microsoft Docs
description: Dowiedz się, jak uruchomić pełne wersje programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux. Uzyskaj bezpośrednie linki do wszystkich obrazów maszyn wirtualnych programu SQL Server z systemem Linux i powiązanej zawartości.
services: virtual-machines-linux
documentationcenter: ''
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 03/22/2018
ms.author: jroth
ms.openlocfilehash: e752ad844a6efe572564e7081ebac87193e9c2a7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Omówienie programu SQL Server na maszynach wirtualnych platformy Azure (system Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

W tym temacie opisano opcje uruchamiania programu SQL Server na maszynach wirtualnych platformy Azure z systemem Linux oraz zamieszczono [linki do obrazów portalu](#create).

> [!NOTE]
> Jeśli znasz już program SQL Server i chcesz tylko zobaczyć, jak wdrożyć maszynę wirtualną programu SQL Server z systemem Linux, zobacz temat [Provision a Linux SQL Server VM in Azure (Aprowizowanie maszyny wirtualnej z programem SQL Server i systemem Linux na platformie Azure](provision-sql-server-linux-virtual-machine.md). Jeśli natomiast chcesz utworzyć maszynę wirtualną z systemem Windows i programem SQL Server, zobacz [Provision a Windows SQL Server VM in Azure (Aprowizowanie maszyny wirtualnej z programem SQL Server i systemem Windows na platformie Azure)](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Jeśli jesteś administratorem bazy danych lub projektantem, możesz skorzystać z oferowanych przez maszyny wirtualne platformy Azure możliwości przenoszenia lokalnych obciążeń i aplikacji programu SQL Server do chmury.

## <a name="scenarios"></a>Scenariusze

Istnieje wiele powodów, dla których warto zdecydować się na hosting danych w usłudze Azure. Jeśli tworzysz lub migrujesz swoją aplikację na platformę Azure, poprawiasz jej wydajność tak, że możesz umieścić również dane zaplecza na platformie Azure. Automatycznie uzyskujesz dostęp do wielu centrów danych, co zapewnia globalną obecność i odzyskiwanie po awarii. Dane są również objęte zaawansowanymi zabezpieczeniami, co wpływa także na ich trwałość.

Program SQL Server uruchomiony na maszynach wirtualnych platformy Azure stanowi jedną z opcji przechowywania danych relacyjnych w systemie Azure. Masz także możliwość korzystania z usługi Azure SQL Database. Aby uzyskać więcej informacji o wybieraniu między programem SQL Server na maszynach wirtualnych a usługą Azure SQL Database, zobacz [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS) (Wybieranie opcji chmury programu SQL Server: bazy danych Azure SQL Database [PaaS] lub program SQL Server na maszynach wirtualnych platformy Azure [IaaS])](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a id="create"></a> Tworzenie nowej maszyny wirtualnej SQL

Samouczek [Provision a Linux SQL Server VM in Azure (Aprowizowanie maszyny wirtualnej z programem SQL Server i systemem Linux na platformie Azure)](provision-sql-server-linux-virtual-machine.md) zawiera wskazówki krok po kroku dotyczące tworzenia nowej maszyny wirtualnej z bazą danych SQL.

Poniższa tabela zawiera macierz najnowszych obrazów programu SQL Server w galerii maszyn wirtualnych. Kliknij dowolny link, aby rozpocząć tworzenie nowej maszyny wirtualnej z programem SQL o określonej wersji, wybranym wydaniu i z danym systemem operacyjnym.

> [!TIP]
> Aby zapoznać się z cennikiem tych obrazów maszyn wirtualnych i oprogramowania SQL, zobacz [stronę cen dla maszyn wirtualnych z programem SQL Server i systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Wersja | System operacyjny | Wersja |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Aby wyświetlić dostępne obrazy maszyn wirtualnych z programem SQL Server i systemem Windows, zobacz [Overview of SQL Server on Azure Virtual Machines (Windows) (Omówienie programu SQL Server na maszynach wirtualnych platformy Azure [system Windows])](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Zainstalowane pakiety

Po skonfigurowaniu programu SQL Server w systemie Linux zainstaluj pakiet aparatu bazy danych, a następnie kilka opcjonalnych pakietów w zależności od swoich wymagań. Obrazy maszyn wirtualnych z systemem Linux dla programu SQL Server automatycznie zainstalują większość pakietów. W poniższej tabeli przedstawiono pakiety instalowane dla poszczególnych dystrybucji.

| Dystrybucja | [Aparat bazy danych](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Narzędzia](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Program SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Wyszukiwanie pełnotekstowe](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Dodatek wysokiej dostępności](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nie](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![nie](./media/sql-server-linux-virtual-machines-overview/no.png) | ![nie](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![tak](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat sposobu konfigurowania i używania programu SQL Server w systemie Linux, zobacz [Overview of SQL Server on Linux (Omówienie programu SQL Server w systemie Linux)](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
