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
ms.openlocfilehash: 4aab699ff0141b648066fae0de445c4467be7509
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048390"
---
# <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

W warstwie obliczeniowej z zainicjowaną obsługą modelu zakupu opartego na rdzeń wirtualny można wymienić istniejące licencje dla obniżonych stawek na SQL Database przy użyciu [korzyść użycia hybrydowego platformy Azure do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Ta korzyść platformy Azure umożliwia oszczędzanie do 30 procent na Azure SQL Database przy użyciu lokalnych licencji SQL Server z programem Software Assurance. 

> [!NOTE]
> Zmiana na Korzyść użycia hybrydowego platformy Azure nie wymaga żadnych przestojów.

![cennik](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>Wybierz model licencji

Za pomocą Korzyść użycia hybrydowego platformy Azure Możesz wybrać opcję płacenia wyłącznie za podstawową infrastrukturę platformy Azure, korzystając z istniejącej licencji SQL Server dla aparatu bazy danych SQL (podstawowa cena obliczeniowa) lub płacisz za podstawową infrastrukturę i SQL Server Licencja (cena uwzględniona w licencji).

Możesz wybrać lub zmienić model licencjonowania przy użyciu Azure Portal lub przy użyciu jednego z następujących interfejsów API:

- Aby ustawić lub zaktualizować typ licencji przy użyciu programu PowerShell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu wiersza polecenia platformy Azure:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Aby ustawić lub zaktualizować typ licencji przy użyciu interfejsu API REST:

  - [Bazy danych — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Bazy danych — aktualizacja](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Wystąpienia zarządzane — Utwórz lub zaktualizuj](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Wystąpienia zarządzane — aktualizacja](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat opcji wdrażania SQL Database, zobacz [Wybieranie odpowiedniej opcji wdrażania w usłudze Azure SQL](sql-database-paas-vs-sql-server-iaas.md).
- Porównanie funkcji SQL Database można znaleźć w temacie [Azure SQL Database Features](sql-database-features.md).
