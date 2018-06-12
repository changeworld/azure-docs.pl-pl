---
title: Pojęcia dotyczące serwera bazy danych Azure dla programu MySQL
description: Ten temat zawiera zagadnienia i wskazówki dotyczące pracy z bazą danych Azure dla serwerów MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9d94f897546ea1e1190aab91e80eb9868224e5a7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265463"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Pojęcia dotyczące serwera bazy danych Azure dla programu MySQL
Ten artykuł zawiera zagadnienia i wskazówki dotyczące pracy z bazą danych Azure dla serwerów MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co to jest Azure bazy danych serwera MySQL?

Baza danych Azure MySQL serwera jest centralny punkt administracyjny dla wielu baz danych. Jest tym samym konstrukcja MySQL serwera można zapoznać się z na świecie lokalnych. W szczególności bazą danych Azure dla usługi MySQL jest zarządzany, zapewnia gwarancje wydajności i ujawnia dostępu i funkcji na poziomie serwera.

Azure bazy danych MySQL serwera:

- Zostanie utworzone w subskrypcji platformy Azure.
- Jest zasobem nadrzędnej dla baz danych.
- Obejmuje przestrzeń nazw dla baz danych.
- To kontener z semantyki silne istnienia — usuwanie serwera i usuwa zawartych baz danych.
- Collocates zasoby w regionie.
- Udostępnia punkt końcowy połączenia dla serwera i dostęp do bazy danych.
- Określa zakres dla zasad zarządzania, które są stosowane do jej baz danych: logowania, zapory, użytkowników, ról, konfiguracje itd.
- Jest dostępna w różnych wersjach. Aby uzyskać więcej informacji, zobacz [obsługiwane bazy danych Azure dla wersji bazy danych MySQL](./concepts-supported-versions.md).

Na serwerze usługi Azure Database for MySQL można utworzyć jedną lub wiele baz danych. Możesz wybrać opcję tworzenia pojedynczej bazy danych na serwer, aby korzystać ze wszystkich zasobów lub utworzyć wiele baz danych, aby udostępnić zasoby. Cennik jest strukturalnych dla poszczególnych serwerów, na podstawie konfiguracji warstwa cenowa, vCores i magazyn (GB). Aby uzyskać więcej informacji, zobacz [warstw cenowych](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak połączyć i uwierzytelniania do bazy danych MySQL serwera Azure?

Następujące elementy zapewnić bezpieczny dostęp do bazy danych.
|||
| :-- | :-- |
| **Uwierzytelnianie i autoryzacja** | Azure bazy danych MySQL serwera obsługuje natywnych uwierzytelnianie MySQL. Możesz łączyć i uwierzytelniania na serwerze z nazwą logowania administratora serwera. |
| **Protokół** | Usługa obsługuje protokół oparta na komunikatach używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP i za pośrednictwem gniazda domeny systemu Unix. |
| **Zapora** | Aby chronić dane, reguła zapory uniemożliwia wszystkie dostęp do serwer bazy danych, do momentu określenia komputery, które ma uprawnienia. Zobacz [bazą danych Azure dla reguł zapory serwera MySQL](./concepts-firewall-rules.md). |
| **SSL** | Usługa obsługuje wymuszenie połączenia SSL między aplikacjami a serwerem bazy danych.  Zobacz [Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak zarządzać serwerem?
Baza danych Azure dla serwerów MySQL można zarządzać za pomocą portalu Azure lub interfejsu wiersza polecenia Azure.

## <a name="next-steps"></a>Kolejne kroki
- Aby zapoznać się z omówieniem usługi, zobacz [Azure bazy danych MySQL — omówienie](./overview.md)
- Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-service-tiers.md)
- Aby uzyskać informacje dotyczące połączenia z usługą, zobacz [biblioteki połączeń dla bazy danych Azure dla programu MySQL](./concepts-connection-libraries.md).
