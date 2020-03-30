---
title: Korzyść użycia hybrydowego platformy Azure
description: Użyj istniejących licencji programu SQL Server dla rabatów bazy danych SQL.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: d1a59e7ad86191bcc30b7d898d00f327c20fbc5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945623"
---
# <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

W warstwie obliczeniowej aprowizowanego modelu zakupów opartego na wynikach wirtualnych można wymienić istniejące licencje na zniżki w bazie danych SQL przy użyciu [korzyści hybrydowych platformy Azure dla programu SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/) Ta korzyść platformy Azure umożliwia zapisanie do 30 procent lub nawet więcej w usłudze Azure SQL Database przy użyciu lokalnych licencji programu SQL Server z pakietem Software Assurance. Użyj kalkulatora korzyści hybrydowych platformy Azure przy użyciu łącza wymienionego wcześniej dla poprawnych wartości. 

> [!NOTE]
> Zmiana na korzyść hybrydową platformy Azure nie wymaga żadnych przestojów.

![Ceny](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Wybieranie modelu licencji

Korzystając z korzyści hybrydowej platformy Azure, można płacić tylko za podstawową infrastrukturę platformy Azure przy użyciu istniejącej licencji programu SQL Server dla samego aparatu bazy danych SQL (cennik obliczeń podstawowych) lub za infrastrukturę podstawową i program SQL Server (ceny zawarte w licencji).

Możesz wybrać lub zmienić model licencjonowania przy użyciu witryny Azure portal lub przy użyciu jednego z następujących interfejsów API:

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Aby ustawić lub zaktualizować typ licencji przy użyciu programu PowerShell:

- [Nowa baza danych AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [Nowy-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu wiersza polecenia platformy Azure:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi tworzenie](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi aktualizacja](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[INTERFEJS API ODPOCZYNKU](#tab/rest)

Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu API REST:

- [Bazy danych — tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate)
- [Bazy danych — aktualizacja](/rest/api/sql/databases/update)
- [Wystąpienia zarządzane — tworzenie lub aktualizowanie](/rest/api/sql/managedinstances/createorupdate)
- [Wystąpienia zarządzane — aktualizacja](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>Następne kroki

- Aby wybrać opcję wdrażania bazy danych SQL, zobacz [Wybieranie odpowiedniej opcji wdrażania w usłudze Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Aby zapoznać się z porównaniem funkcji bazy danych SQL, zobacz [Funkcje bazy danych SQL platformy Azure](sql-database-features.md).
