---
title: Obsługiwane wersje w usłudze Azure Database dla serwera MariaDB
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database dla serwera MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065723"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Obsługiwane usługi Azure Database dla wersji serwera MariaDB

Azure Database dla serwera MariaDB został opracowany z "open source" [serwera MariaDB](https://downloads.mariadb.org/), za pomocą aparat InnoDB. Azure Database dla serwera MariaDB obecnie obsługuje następującą wersję:

## <a name="mariadb-version-102"></a>Wersja MariaDB 10.2

Zapoznaj się [dokumentacji MariaDB](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>W wersji 10.3 MariaDB

Zapoznaj się [dokumentacji MariaDB](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w MariaDB 10.3.14.

> [!NOTE]
> W usłudze bramy jest używana do przekierowania połączenia do wystąpienia serwera. Po nawiązaniu połączenia klienta MySQL Wyświetla wersję zestawu w bramie nie rzeczywistą wersję uruchomionego w wystąpieniu serwera MariaDB MariaDB. Aby ustalić wersji wystąpienia serwera MariaDB, użyj `SELECT VERSION();` polecenie w wierszu polecenia MySQL.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień

Usługa zarządza automatycznie poprawek dla aktualizacji wersji pomocniczej.

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md).
