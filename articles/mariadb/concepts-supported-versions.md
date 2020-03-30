---
title: Obsługiwane wersje — usługa Azure Database for MariaDB
description: Dowiedz się, które wersje serwera MariaDB są obsługiwane w usłudze Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527711"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Obsługiwana usługa Azure Database dla wersji serwera MariaDB

Usługa Azure Database for MariaDB została opracowana z [serwera MariaDB](https://downloads.mariadb.org/)typu open source przy użyciu aparatu InnoDB.

MariaDB używa schematu nazewnictwa X.Y.Z. X jest główną wersją, Y jest wersją pomocniczą, a Z jest wersją patcha.

> [!NOTE]
> W usłudze brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient MySQL wyświetla wersję mariadb ustawioną w bramie, a nie rzeczywistą wersję uruchomiona w wystąpieniu serwera MariaDB. Aby określić wersję wystąpienia serwera MariaDB, użyj `SELECT VERSION();` polecenia.

Usługa Azure Database for MariaDB obsługuje obecnie następującą wersję:

## <a name="mariadb-version-102"></a>MariaDB wersja 10.2

Aktualizacja: 10.2.25

Zapoznaj się z [dokumentacją MariaDB,](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="mariadb-version-103"></a>MariaDB wersja 10.3

Aktualizacja: 10.3.16

Zapoznaj się z [dokumentacją MariaDB,](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza uaktualnieniami aktualizacji poprawek. Na przykład 10.2.21 do 10.2.23.  

Obecnie uaktualnienia wersji pomocniczych i głównych nie są obsługiwane. Na przykład uaktualnienie bazy danych MariaDB 10.2 do wersji 10.3 nie jest obsługiwane. Jeśli chcesz uaktualnić z 10.2 do 10.3, zrób [zrzut i przywróć](./howto-migrate-dump-restore.md) go na serwerze, który został utworzony z nową wersją aparatu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o określonych przydziałach zasobów i ograniczeniach opartych na **warstwie usług,** zobacz [Warstwy usług](./concepts-pricing-tiers.md).
