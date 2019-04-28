---
title: Pojęcia serwera w usłudze Azure Database dla serwera MariaDB
description: Ten temat zawiera zagadnienia i wytyczne dotyczące pracy z usługą Azure Database dla serwerów MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f61f8740c9514f6276afb2ee84bcdccdc54c0710
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61040925"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Pojęcia serwera w usłudze Azure Database dla serwera MariaDB
Ten artykuł zawiera zagadnienia i wytyczne dotyczące pracy z usługą Azure Database dla serwerów MariaDB.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Co to jest usługa Azure Database dla serwera MariaDB?

Azure Database dla serwera MariaDB to centralny punkt administracyjny dla wielu baz danych. Jest tego samego serwera MariaDB konstrukcja można zapoznać się z w środowisku lokalnym. W szczególności usługa Azure Database for MariaDB jest zarządzany, zawiera gwarancje wydajności i uwidacznia dostępu i funkcje na poziomie serwera.

Azure Database dla serwera MariaDB:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Zapewnia przestrzeń nazw dla baz danych.
- To kontener z silną semantyką okresu istnienia — usuwanie serwera, a następnie usuwane zawartych baz danych.
- Rozmieszcza zasoby w regionie.
- Udostępnia punkt końcowy połączenia dla serwera i dostępu do bazy danych.
- Określa zakres dla zasad zarządzania, które są stosowane do jego baz danych: logowania, zapory, użytkowników, ról, konfiguracji itp.
- Jest dostępna w wersji aparatu MariaDB 10.2. Aby uzyskać więcej informacji, zobacz [obsługiwane usługi Azure Database dla wersji bazy danych MariaDB](./concepts-supported-versions.md).

Na serwerze usługi Azure Database for MariaDB można utworzyć jedną lub wiele baz danych. Możesz zdecydować się na tworzenie pojedynczej bazy danych na serwer na korzystanie z zasobów lub utworzyć wiele baz danych, współdzielących zasoby. Cennik jest strukturą poszczególnych serwerów, na podstawie konfiguracji ceny warstwy, rdzeni wirtualnych i magazynu (GB). Aby uzyskać więcej informacji, zobacz [warstw cenowych](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Jak zabezpieczyć usługi Azure Database dla serwera MariaDB?

Następujące elementy zapewniają bezpieczny dostęp do bazy danych.

|||
| :--| :--|
| **Uwierzytelnianie i autoryzacja** | Azure Database dla serwera MariaDB obsługuje uwierzytelnianie za pomocą natywnego MySQL. Można połączyć i uwierzytelniać się na serwerze z identyfikatorem logowania administratora serwera. |
| **Protokół** | Usługa obsługuje oparta na komunikatach protokół używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP, jak i za pośrednictwem gniazd domeny systemu Unix. |
| **Zapora** | Aby lepiej chronić swoje dane, regułę zapory uniemożliwia dostęp do serwera bazy danych, do momentu określenia komputerów, które mają uprawnienia. Zobacz [— Azure Database for reguły zapory serwera MariaDB](./concepts-firewall-rules.md). |
| **SSL** | Usługa obsługuje wymuszenie połączenia SSL między aplikacjami i serwer bazy danych. Zobacz [Konfigurowanie łączności SSL w aplikacji w celu nawiązania bezpiecznego połączenia usługi Azure Database dla serwera MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak zarządzać serwerem?
— Azure Database dla serwerów MariaDB można zarządzać za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [— Azure Database for MariaDB — omówienie](./overview.md)
- Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
