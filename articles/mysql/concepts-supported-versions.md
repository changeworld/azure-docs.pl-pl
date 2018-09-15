---
title: Obsługiwane wersje w usłudze Azure Database for MySQL
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/23/2018
ms.openlocfilehash: 1b6dded1521489353e65b630ef5432ba6ff8f3e8
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631569"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane — Azure Database for MySQL server wersji
Usługa Azure Database for MySQL została opracowana z [MySQL Community Edition](https://www.mysql.com/products/community/), za pomocą aparat InnoDB.  Usługa Azure Database for MySQL obsługuje obecnie następujące wersje:

## <a name="mysql-version-5639"></a>Wersja programu MySQL 5.6.39
Zapoznaj się MySQL [dokumentacji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w bazie danych MySQL 5.6.39.

## <a name="mysql-version-5721"></a>Wersja programu MySQL 5.7.21
Zapoznaj się MySQL [dokumentacji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w bazie danych MySQL 5.7.21.

> [!NOTE]
> W usłudze bramy jest używana do przekierowania połączenia do wystąpienia serwera. Po nawiązaniu połączenia klienta MySQL Wyświetla wersję oprogramowania MySQL, ustaw w bramie nie rzeczywistą wersję uruchomionego w wystąpieniu serwera MySQL. Aby ustalić wersji wystąpienia serwera MySQL, użyj `SELECT VERSION();` polecenie w wierszu polecenia MySQL.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień
Usługa zarządza automatycznie poprawek dla aktualizacji wersji pomocniczej. Obecnie nie jest obsługiwane uaktualnienie wersji głównej. Na przykład uaktualnianie z programu MySQL 5.6 do MySQL 5.7 nie jest obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, [zrzucanie i przywracanie](./concepts-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
