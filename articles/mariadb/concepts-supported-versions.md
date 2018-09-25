---
title: Obsługiwane wersje w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aa83b9955839aaa03aa2ebf46c9e464dc75c8d3a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977506"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Obsługiwane usługi Azure Database dla wersji serwera MariaDB
Azure Database dla serwera MariaDB został opracowany z "open source" [serwera MariaDB](https://downloads.mariadb.org/), za pomocą aparat InnoDB. Azure Database dla serwera MariaDB obecnie obsługuje następującą wersję:

## <a name="mariadb-version-10217"></a>Wersja MariaDB 10.2.17
Zapoznaj się [dokumentacji MariaDB](https://downloads.mariadb.org/mariadb/10.2.17/) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w MariaDB 10.2.17.

> [!NOTE]
> W usłudze bramy jest używana do przekierowania połączenia do wystąpienia serwera. Po nawiązaniu połączenia klienta MySQL Wyświetla wersję zestawu w bramie nie rzeczywistą wersję uruchomionego w wystąpieniu serwera MariaDB MariaDB. Aby ustalić wersji wystąpienia serwera MariaDB, użyj `SELECT VERSION();` polecenie w wierszu polecenia MySQL.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień
Usługa zarządza automatycznie poprawek dla aktualizacji wersji pomocniczej.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)