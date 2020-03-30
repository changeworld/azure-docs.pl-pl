---
title: Serwery — usługa Azure Database dla mariadb
description: Ten temat zawiera zagadnienia i wskazówki dotyczące pracy z usługą Azure Database dla serwerów MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527796"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Pojęcia dotyczące serwera w bazie danych platformy Azure dla mariadb
Ten artykuł zawiera zagadnienia i wskazówki dotyczące pracy z usługą Azure Database dla serwerów MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Co to jest serwer usługi Azure Database dla mariadb?

Serwer usługi Azure Database for MariaDB jest centralnym punktem administracyjnym dla wielu baz danych. Jest to ta sama konstrukcja serwera MariaDB, którą możesz znać w środowisku lokalnym. W szczególności usługa Azure Database for MariaDB jest zarządzana, zapewnia gwarancje wydajności i udostępnia dostęp i funkcje na poziomie serwera.

Usługa Azure Database dla serwera MariaDB:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Udostępnia obszar nazw dla baz danych.
- Jest kontenerem z silną semantyką okresu istnienia — usuń serwer i usuwa zawarte bazy danych.
- Kolokuje zasoby w regionie.
- Zapewnia punkt końcowy połączenia dla dostępu do serwera i bazy danych.
- Udostępnia zakres zasad zarządzania, które mają zastosowanie do jego baz danych: login, firewall, użytkownicy, role, konfiguracje itp.
- Jest dostępny w wersji silnika MariaDB 10.2. Aby uzyskać więcej informacji, zobacz [Obsługiwane usługi Azure Database dla wersji bazy danych MariaDB](./concepts-supported-versions.md).

Na serwerze usługi Azure Database for MariaDB można utworzyć jedną lub wiele baz danych. Można utworzyć jedną bazę danych na serwer, aby użyć wszystkich zasobów lub utworzyć wiele baz danych do udostępniania zasobów. Cennik jest skonstruowany na serwer, na podstawie konfiguracji warstwy cenowej, vCores i magazynu (GB). Aby uzyskać więcej informacji, zobacz [Warstwy cenowe](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Jak zabezpieczyć usługę Azure Database dla serwera MariaDB?

Następujące elementy pomagają zapewnić bezpieczny dostęp do bazy danych.

|||
| :--| :--|
| **Uwierzytelnianie i autoryzacja** | Serwer usługi Azure Database for MariaDB obsługuje natywne uwierzytelnianie MySQL. Można połączyć się i uwierzytelnić na serwerze za pomocą logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparty na wiadomościach używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany przez protokół TCP/IP i za pomocą gniazd domen uniksowych. |
| **Zapora** | Aby chronić dane, reguła zapory uniemożliwia dostęp do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zobacz [Usługi Azure Database dla mariadb reguł zapory serwera](./concepts-firewall-rules.md). |
| **Protokół SSL** | Usługa obsługuje wymuszanie połączeń SSL między aplikacjami a serwerem bazy danych. Zobacz [Konfigurowanie łączności SSL w aplikacji, aby bezpiecznie połączyć się z usługą Azure Database for MariaDB.](./howto-configure-ssl.md) |

## <a name="how-do-i-manage-a-server"></a>Jak zarządzać serwerem?
Usługi Azure Database dla serwerów MariaDB można zarządzać przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [Usługa Azure Database for MariaDB Overview](./overview.md)
- Aby uzyskać informacje o określonych przydziałach zasobów i ograniczeniach opartych na **warstwie usług,** zobacz [Warstwy usług](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
