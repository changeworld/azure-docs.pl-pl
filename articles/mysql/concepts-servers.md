---
title: Pojęcia dotyczące serwera — usługa Azure Database for MySQL
description: Ten temat zawiera zagadnienia i wskazówki dotyczące pracy z usługą Azure Database dla serwerów MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537010"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Pojęcia dotyczące serwera w bazie danych platformy Azure dla mysql

Ten artykuł zawiera zagadnienia i wskazówki dotyczące pracy z usługą Azure Database dla serwerów MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co to jest usługa Azure Database dla serwera MySQL?

Serwer usługi Azure Database for MySQL jest centralnym punktem administracyjnym dla wielu baz danych. Jest to ta sama konstrukcja serwera MySQL, którą możesz znać w środowisku lokalnym. W szczególności usługa Azure Database for MySQL jest zarządzana, zapewnia gwarancje wydajności i udostępnia dostęp i funkcje na poziomie serwera.

Usługa Azure Database dla serwera MySQL:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Udostępnia obszar nazw dla baz danych.
- Jest kontenerem z silną semantyką okresu istnienia — usuń serwer i usuwa zawarte bazy danych.
- Kolokuje zasoby w regionie.
- Zapewnia punkt końcowy połączenia dla dostępu do serwera i bazy danych.
- Udostępnia zakres zasad zarządzania, które mają zastosowanie do jego baz danych: login, firewall, użytkownicy, role, konfiguracje itp.
- Jest dostępny w wielu wersjach. Aby uzyskać więcej informacji, zobacz [Obsługa usługi Azure Database dla wersji bazy danych MySQL](./concepts-supported-versions.md).

Na serwerze usługi Azure Database for MySQL można utworzyć jedną lub wiele baz danych. Można utworzyć jedną bazę danych na serwer, aby użyć wszystkich zasobów lub utworzyć wiele baz danych do udostępniania zasobów. Cennik jest skonstruowany na serwer, na podstawie konfiguracji warstwy cenowej, vCores i magazynu (GB). Aby uzyskać więcej informacji, zobacz [Warstwy cenowe](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak połączyć się i uwierzytelnić w bazie danych usługi Azure dla serwera MySQL?

Następujące elementy pomagają zapewnić bezpieczny dostęp do bazy danych.

|     |     |
| :-- | :-- |
| **Uwierzytelnianie i autoryzacja** | Usługa Azure Database for MySQL serwer obsługuje natywne uwierzytelnianie MySQL. Można połączyć się i uwierzytelnić na serwerze za pomocą logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparty na wiadomościach używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany przez protokół TCP/IP i za pomocą gniazd domen uniksowych. |
| **Zapora** | Aby chronić dane, reguła zapory uniemożliwia dostęp do serwera bazy danych, dopóki nie określisz, które komputery mają uprawnienia. Zobacz [Azure Database for MySQL Server firewall rules](./concepts-firewall-rules.md). |
| **Protokół SSL** | Usługa obsługuje wymuszanie połączeń SSL między aplikacjami a serwerem bazy danych.  Zobacz [Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak zarządzać serwerem?

Usługę Azure Database dla serwerów MySQL można zarządzać przy użyciu witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem usługi, zobacz [Usługa Azure Database for MySQL Overview](./overview.md)
- Aby uzyskać informacje o określonych przydziałach zasobów i ograniczeniach opartych na **warstwie usług,** zobacz [Warstwy usług](./concepts-service-tiers.md)
- Aby uzyskać informacje dotyczące łączenia się z usługą, zobacz [Biblioteki połączeń dla usługi Azure Database for MySQL](./concepts-connection-libraries.md).
