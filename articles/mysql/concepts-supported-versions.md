---
title: Obsługiwane wersje w Azure Database for MySQL
description: Opisuje obsługiwane wersje w Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 25251b617522840412a4868331e155285f64a18c
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962581"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane wersje serwera Azure Database for MySQL

Azure Database for MySQL został opracowany z firmy [MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu InnoDB.

Baza danych MySQL używa schematu nazewnictwa X. Y. Z. X jest wersją główną, Y jest wersją pomocniczą, a Z Z to Poprawka błędu Release. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> W usłudze brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL.

Azure Database for MySQL obecnie obsługuje następujące wersje:

## <a name="mysql-version-56"></a>Baza danych MySQL w wersji 5,6

Wydanie poprawki usterek: 5.6.44

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-44.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="mysql-version-57"></a>Baza danych MySQL w wersji 5,7

Wydanie poprawki usterek: 5.7.26

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-26.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="mysql-version-80"></a>Baza danych MySQL w wersji 8,0

> [!IMPORTANT]
> Baza danych MySQL 8,0 jest obecnie dostępna w wersji zapoznawczej.

Wydanie poprawki usterek: 8.0.15

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawkami do poprawek błędów wersji. Na przykład 5.7.20 do 5.7.21.  

Obecnie wersje pomocnicze i główne nie są obsługiwane. Na przykład uaktualnienie z programu MySQL 5,6 do bazy danych MySQL 5,7 nie jest obsługiwane. Jeśli chcesz przeprowadzić uaktualnienie z 5,6 do 5,7, zrób [zrzut i Przywróć](./concepts-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
