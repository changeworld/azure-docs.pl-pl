---
title: Korzyść użycia hybrydowego platformy Azure
description: Użyj istniejących licencji SQL Server dla SQL Database rabatów.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 394c674da63bbda643246c3d61fb670d6ac8d1f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928620"
---
# <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

W warstwie obliczeniowej z zainicjowaną obsługą modelu zakupu opartego na rdzeń wirtualny można wymienić istniejące licencje dla obniżonych stawek na SQL Database przy użyciu [korzyść użycia hybrydowego platformy Azure do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Ta korzyść platformy Azure umożliwia oszczędzanie do 30 procent na Azure SQL Database przy użyciu lokalnych licencji SQL Server z programem Software Assurance. 

> [!NOTE]
> Zmiana na Korzyść użycia hybrydowego platformy Azure nie wymaga żadnych przestojów.

![cennik](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Wybierz model licencji

Za pomocą Korzyść użycia hybrydowego platformy Azure Możesz wybrać opcję płacenia wyłącznie za podstawową infrastrukturę platformy Azure, korzystając z istniejącej licencji SQL Server dla aparatu bazy danych SQL (podstawowa cena obliczeniowa) lub płacisz za podstawową infrastrukturę i SQL Server Licencja (cena uwzględniona w licencji).

Możesz wybrać lub zmienić model licencjonowania przy użyciu Azure Portal lub przy użyciu jednego z następujących interfejsów API:

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby ustawić lub zaktualizować typ licencji przy użyciu programu PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu wiersza polecenia platformy Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [AZ SQL mi Update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[Interfejs API REST](#tab/rest)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu API REST:

- [Bazy danych — Utwórz lub zaktualizuj](/rest/api/sql/databases/createorupdate)
- [Bazy danych — aktualizacja](/rest/api/sql/databases/update)
- [Wystąpienia zarządzane — Utwórz lub zaktualizuj](/rest/api/sql/managedinstances/createorupdate)
- [Wystąpienia zarządzane — aktualizacja](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat opcji wdrażania SQL Database, zobacz [Wybieranie odpowiedniej opcji wdrażania w usłudze Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Porównanie funkcji SQL Database można znaleźć w temacie [Azure SQL Database Features](sql-database-features.md).
