---
title: Obsługiwane wersje — usługa Azure Database for MySQL
description: Dowiedz się, które wersje serwera MySQL są obsługiwane w usłudze Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 197b3100190711a51cfe125fe1214a59c18e1491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536976"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwana usługa Azure Database dla wersji serwerów MySQL

Usługa Azure Database for MySQL została opracowana z [programu MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu InnoDB.

MySQL używa schematu nazewnictwa X.Y.Z. X jest główną wersją, Y jest wersją pomocniczą, a Z jest wydaniem poprawki błędów. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> W usłudze brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL.

Usługa Azure Database for MySQL obsługuje obecnie następujące wersje:

## <a name="mysql-version-56"></a>MySQL wersja 5.6

Wydanie poprawki błędu: 5.6.45

Zapoznaj się z [uwagami dotyczącymi wersji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) MySQL, aby dowiedzieć się więcej o ulepszeniach i poprawkach w tej wersji.

## <a name="mysql-version-57"></a>MySQL wersja 5.7

Wydanie poprawki błędu: 5.7.27

Zapoznaj się z [uwagami dotyczącymi wersji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) MySQL, aby dowiedzieć się więcej o ulepszeniach i poprawkach w tej wersji.

## <a name="mysql-version-80"></a>MySQL wersja 8.0

Wydanie poprawki błędu: 8.0.15

Zapoznaj się z [uwagami dotyczącymi wersji](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) MySQL, aby dowiedzieć się więcej o ulepszeniach i poprawkach w tej wersji.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawkami dla aktualizacji wersji poprawki błędów. Na przykład od 5.7.20 do 5.7.21.  

Obecnie uaktualnienia wersji pomocniczych i głównych nie są obsługiwane. Na przykład uaktualnienie bazy danych MySQL 5.6 do wersji 5.7 nie jest obsługiwane. Jeśli chcesz przeprowadzić uaktualnienie z wersji 5.6 do 5.7, wykonaj [zrzut i przywróć go](./concepts-migrate-dump-restore.md) na serwer, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o określonych przydziałach zasobów i ograniczeniach opartych na **warstwie usług,** zobacz [Warstwy usług](./concepts-pricing-tiers.md)
