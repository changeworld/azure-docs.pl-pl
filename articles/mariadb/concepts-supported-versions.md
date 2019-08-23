---
title: Obsługiwane wersje w Azure Database for MariaDB
description: Opisuje obsługiwane wersje w Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 950294094584958e83f6a16630a6e1f897785e46
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897283"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Obsługiwane wersje serwera Azure Database for MariaDB

Azure Database for MariaDB został opracowany z [serwera MariaDB](https://downloads.mariadb.org/)"open source" przy użyciu aparatu InnoDB. 

MariaDB używa schematu nazewnictwa X. Y. Z. X jest wersją główną, Y jest wersją pomocniczą i Z jest wersją poprawki.

> [!NOTE]
> W usłudze Brama jest używana do przekierowywania połączeń z wystąpieniami serwera. Po nawiązaniu połączenia klient MySQL wyświetla wersję zestawu MariaDB w bramie, a nie rzeczywistą wersję działającą w wystąpieniu serwera MariaDB. Aby określić wersję wystąpienia serwera MariaDB, użyj `SELECT VERSION();` polecenia.

Azure Database for MariaDB obecnie obsługuje następującą wersję:

## <a name="mariadb-version-102"></a>MariaDB w wersji 10,2

Wersja poprawki: 10.2.23

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w programie MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>MariaDB w wersji 10,3

Wersja poprawki: 10.3.14

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w programie MariaDB 10.3.14.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza uaktualnieniami aktualizacji poprawek. Na przykład 10.2.21 do 10.2.23.  

Obecnie wersje pomocnicze i główne nie są obsługiwane. Na przykład uaktualnienie z MariaDB 10,2 do MariaDB 10,3 nie jest obsługiwane. Jeśli chcesz przeprowadzić uaktualnienie z 10,2 do 10,3, zrób [zrzut i Przywróć](./howto-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md).
