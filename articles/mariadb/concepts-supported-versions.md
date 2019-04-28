---
title: Obsługiwane wersje w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60935555"
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
