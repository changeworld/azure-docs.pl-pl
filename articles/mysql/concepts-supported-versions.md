---
title: Obsługiwane wersje w usłudze Azure Database for MySQL
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/13/2019
ms.openlocfilehash: e7e81632b2be135fb74d375ab8a11f1b4b3ef39d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525934"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane — Azure Database for MySQL server wersji

Usługa Azure Database for MySQL została opracowana z [MySQL Community Edition](https://www.mysql.com/products/community/), za pomocą aparat InnoDB.

MySQL wykorzystuje schemat nazewnictwa X.Y.Z. X to główny numer wersji, Y jest wersji pomocniczej i Z jest wersja poprawki. Aby uzyskać więcej informacji na temat systemu zobacz [dokumentacji MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Usługa Azure Database for MySQL obsługuje obecnie następujące wersje:

## <a name="mysql-version-56"></a>Wersja programu MySQL 5.6

Wersja poprawki: 5.6.42

Zapoznaj się MySQL [informacje o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w bazie danych MySQL 5.6.42.

## <a name="mysql-version-57"></a>Wersja programu MySQL 5.7

Wersja poprawki: 5.7.24

Zapoznaj się MySQL [informacje o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w bazie danych MySQL 5.7.24.

> [!NOTE]
> W usłudze bramy jest używana do przekierowania połączenia do wystąpienia serwera. Po nawiązaniu połączenia klienta MySQL Wyświetla wersję oprogramowania MySQL, ustaw w bramie nie rzeczywistą wersję uruchomionego w wystąpieniu serwera MySQL. Aby ustalić wersji wystąpienia serwera MySQL, użyj `SELECT VERSION();` polecenie w wierszu polecenia MySQL.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień
Usługa zarządza automatycznie poprawek dla poprawki wersji aktualizacji. Na przykład 5.7.20 do 5.7.21.  

Obecnie nie są obsługiwane uaktualnienia wersji pomocnicze, jak i głównej. Na przykład uaktualnianie z programu MySQL 5.6 do MySQL 5.7 nie jest obsługiwane. Jeśli chcesz uaktualnić do wersji 5.7 5.6, [zrzucanie i przywracanie](./concepts-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
