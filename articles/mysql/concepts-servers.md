---
title: Pojęcia serwera w usłudze Azure Database for MySQL
description: Ten temat zawiera zagadnienia i wytyczne dotyczące pracy z usługą Azure Database dla serwerów MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 565e1bf7a4972e230b3cf56232ebd24519fcab5c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525849"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Pojęcia serwera w usłudze Azure Database for MySQL

Ten artykuł zawiera zagadnienia i wytyczne dotyczące pracy z usługą Azure Database dla serwerów MySQL.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Co to jest usługi Azure Database for MySQL server?

Serwer Azure Database for MySQL to centralny punkt administracyjny dla wielu baz danych. Jest tego samego serwera MySQL konstrukcja można zapoznać się z w środowisku lokalnym. W szczególności usługa Azure Database for MySQL jest zarządzany, zapewnia gwarancje wydajności i uwidacznia dostępu i funkcje na poziomie serwera.

Serwer Azure Database for MySQL:

- Jest tworzony w ramach subskrypcji platformy Azure.
- Jest zasobem nadrzędnym dla baz danych.
- Zapewnia przestrzeń nazw dla baz danych.
- To kontener z silną semantyką okresu istnienia — usuwanie serwera, a następnie usuwane zawartych baz danych.
- Rozmieszcza zasoby w regionie.
- Udostępnia punkt końcowy połączenia dla serwera i dostępu do bazy danych.
- Określa zakres dla zasad zarządzania, które są stosowane do jego baz danych: logowania, zapory, użytkowników, ról, konfiguracji itp.
- Jest dostępna w wielu wersji. Aby uzyskać więcej informacji, zobacz [obsługiwane usługi Azure Database dla wersji bazy danych MySQL](./concepts-supported-versions.md).

Na serwerze usługi Azure Database for MySQL można utworzyć jedną lub wiele baz danych. Możesz zdecydować się na tworzenie pojedynczej bazy danych na serwer na korzystanie z zasobów lub utworzyć wiele baz danych, współdzielących zasoby. Cennik jest strukturą poszczególnych serwerów, na podstawie konfiguracji ceny warstwy, rdzeni wirtualnych i magazynu (GB). Aby uzyskać więcej informacji, zobacz [warstw cenowych](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Jak połączyć i uwierzytelniania usługi Azure Database for MySQL server?

Następujące elementy zapewniają bezpieczny dostęp do bazy danych.

|     |     |
| :-- | :-- |
| **Uwierzytelnianie i autoryzacja** | Usługa Azure Database for MySQL server obsługuje uwierzytelnianie za pomocą natywnego MySQL. Można połączyć i uwierzytelniać się na serwerze z identyfikatorem logowania administratora serwera. |
| **Protokół** | Usługa obsługuje oparta na komunikatach protokół używany przez MySQL. |
| **TCP/IP** | Protokół jest obsługiwany za pośrednictwem protokołu TCP/IP, jak i za pośrednictwem gniazd domeny systemu Unix. |
| **Zapora** | Aby lepiej chronić swoje dane, regułę zapory uniemożliwia dostęp do serwera bazy danych, do momentu określenia komputerów, które mają uprawnienia. Zobacz [— Azure Database for reguły zapory serwera MySQL](./concepts-firewall-rules.md). |
| **SSL** | Usługa obsługuje wymuszenie połączenia SSL między aplikacjami i serwer bazy danych.  Zobacz [Konfigurowanie łączności SSL w aplikacji w celu bezpiecznego nawiązywania połączeń z usługą Azure Database for MySQL](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Jak zarządzać serwerem?

Usługi Azure Database dla serwerów MySQL można zarządzać za pomocą witryny Azure portal lub interfejsu wiersza polecenia platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

- Aby zapoznać się z omówieniem usługi, zobacz [— Azure Database for MySQL — omówienie](./overview.md)
- Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-service-tiers.md)
- Informacje dotyczące łączenia z usługą, zobacz [biblioteki połączeń dla usługi Azure Database for MySQL](./concepts-connection-libraries.md).
