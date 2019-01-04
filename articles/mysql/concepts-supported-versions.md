---
title: Obsługiwane wersje w usłudze Azure Database for MySQL
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/10/2018
ms.openlocfilehash: 720644519eb0031f9f2837cc8321a0def39c37a6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53545709"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane — Azure Database for MySQL server wersji
Usługa Azure Database for MySQL została opracowana z [MySQL Community Edition](https://www.mysql.com/products/community/), za pomocą aparat InnoDB. Usługa Azure Database for MySQL obsługuje obecnie następujące wersje:

## <a name="mysql-version-5639"></a>Wersja programu MySQL 5.6.39
Zapoznaj się MySQL [dokumentacji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w bazie danych MySQL 5.6.39.

## <a name="mysql-version-5721"></a>Wersja programu MySQL 5.7.21
Zapoznaj się MySQL [dokumentacji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w bazie danych MySQL 5.7.21.

> [!NOTE]
> W usłudze bramy jest używana do przekierowania połączenia do wystąpienia serwera. Po nawiązaniu połączenia klienta MySQL Wyświetla wersję oprogramowania MySQL, ustaw w bramie nie rzeczywistą wersję uruchomionego w wystąpieniu serwera MySQL. Aby ustalić wersji wystąpienia serwera MySQL, użyj `SELECT VERSION();` polecenie w wierszu polecenia MySQL.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień
Usługa zarządza automatycznie poprawek dla poprawki wersji aktualizacji. Obecnie nie jest obsługiwane uaktualnienie wersji pomocniczej. Na przykład uaktualnianie z programu MySQL 5.6 do MySQL 5.7 nie jest obsługiwane. Jeśli chcesz uaktualnić do następnej wersji pomocniczej, [zrzucanie i przywracanie](./concepts-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
