---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 01/28/2019
ms.author: genemi
ms.openlocfilehash: edeaa932abe4d1882f957d0ed26aaddd97dabe3f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198065"
---
<!-- sql-database-connect-query-prerequisites-create-db-includes.md -->

- Baza danych Azure SQL Database umieszczana na [serwerze logicznym](https://docs.microsoft.com/azure/sql-database/sql-database-single-index) lub w [wystąpieniu zarządzanym](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index). Bazę danych można utworzyć za pomocą jednej z poniższych metod:

| Serwer logiczny | Wystąpienie zarządzane |
| --- | --- |
| [Portal](../articles/sql-database/sql-database-get-started-portal.md) | [Portal](../articles/sql-database/sql-database-managed-instance-get-started.md) |
| [Interfejs wiersza polecenia](../articles/sql-database/sql-database-get-started-cli.md) | |
| [Program PowerShell](../articles/sql-database/sql-database-get-started-powershell.md) | |

- **Tylko serwer logiczny** — skonfigurowana na poziomie serwera reguła zapory, która umożliwia nawiązanie połączenia z serwerem logicznym. Aby uzyskać więcej informacji, zobacz temat [Tworzenie reguły zapory na poziomie serwera](../articles/sql-database/sql-database-get-started-portal-firewall.md).
- **Tylko wystąpienie zarządzane** — skonfigurowane połączenie z komputera, który uzyskuje dostęp do wystąpienia zarządzanego. Możesz użyć następujących opcji:
  - [Maszyna wirtualna platformy Azure](../articles/sql-database/sql-database-managed-instance-configure-vm.md) w tej samej sieci wirtualnej platformy Azure jako wystąpienie zarządzane, które może uzyskiwać dostęp do wystąpienia.
  - [Połączenie punkt-lokacja](../articles/sql-database/sql-database-managed-instance-configure-p2s.md) na komputerze, które umożliwi przyłączenie komputera do sieci wirtualnej, w której znajduje się wystąpienie zarządzane, i używanie wystąpienia zarządzanego tak jak innych programów SQL Server w używanej sieci.
