---
title: Azure SQL Database — Korzyść użycia hybrydowego platformy Azure
description: Użyj istniejących licencji SQL Server dla SQL Database rabatów.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/08/2019
ms.openlocfilehash: 9a09f7aec4966c02ccfa2101f4359c510ae8b111
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691088"
---
# <a name="azure-hybrid-benefit"></a>Korzyść użycia hybrydowego platformy Azure

W warstwie obliczeniowej z zainicjowaną obsługą modelu zakupu opartego na rdzeń wirtualny można wymienić istniejące licencje dla obniżonych stawek na SQL Database przy użyciu [korzyść użycia hybrydowego platformy Azure do SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Ta korzyść platformy Azure umożliwia oszczędzanie do 30 procent na Azure SQL Database przy użyciu lokalnych licencji SQL Server z programem Software Assurance.

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
