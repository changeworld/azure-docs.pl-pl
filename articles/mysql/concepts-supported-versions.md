---
title: Obsługiwane wersje w Azure Database for MySQL
description: Opisuje obsługiwane wersje w Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 28d635dc5042799790d032ef4b46bf28118cb326
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947171"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane wersje serwera Azure Database for MySQL

Azure Database for MySQL został opracowany z firmy [MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu InnoDB.

Baza danych MySQL używa schematu nazewnictwa X. Y. Z. X jest wersją główną, Y jest wersją pomocniczą, a Z Z to Poprawka błędu Release. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> W usłudze Brama jest używana do przekierowywania połączeń z wystąpieniami serwera. Po nawiązaniu połączenia klient MySQL wyświetla wersję zestawu MySQL ustawioną w bramie, a nie rzeczywistą wersję działającą w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj `SELECT VERSION();` polecenia w monicie programu MySQL.

Azure Database for MySQL obecnie obsługuje następujące wersje:

## <a name="mysql-version-56"></a>Baza danych MySQL w wersji 5,6

Wydanie poprawki usterek: 5.6.42

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) programu MySQL, aby dowiedzieć się więcej o ulepszeniach i poprawkach w programie MySQL 5.6.42.

## <a name="mysql-version-57"></a>Baza danych MySQL w wersji 5,7

Wydanie poprawki usterek: 5.7.24

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w programie MySQL 5.7.24.

## <a name="mysql-version-80"></a>Baza danych MySQL w wersji 8,0

> [!IMPORTANT]
> Baza danych MySQL 8,0 jest obecnie dostępna w wersji zapoznawczej.

Wydanie poprawki usterek: 8.0.15

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w programie MySQL 8.0.15.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawkami do poprawek błędów wersji. Na przykład 5.7.20 do 5.7.21.  

Obecnie wersje pomocnicze i główne nie są obsługiwane. Na przykład uaktualnienie z programu MySQL 5,6 do bazy danych MySQL 5,7 nie jest obsługiwane. Jeśli chcesz przeprowadzić uaktualnienie z 5,6 do 5,7, zrób [zrzut i Przywróć](./concepts-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
