---
title: Obsługiwane wersje w bazie danych Azure dla programu MySQL
description: W tym artykule opisano obsługiwane wersje w bazie danych Azure dla programu MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/22/2018
ms.openlocfilehash: cfebdbe7485f0ffaa15828803d72c2a3f97c118d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane platformy Azure bazy danych MySQL wersji serwera
Bazy danych platformy Azure dla programu MySQL został opracowany z [MySQL Community Edition](https://www.mysql.com/products/community/), przy użyciu aparatu InnoDB.  Bazy danych platformy Azure dla programu MySQL obecnie obsługuje następujące wersje:

## <a name="mysql-version-5638"></a>Wersja MySQL 5.6.38
Zapoznaj się MySQL [dokumentacji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-38.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w MySQL 5.6.38.

## <a name="mysql-version-5720"></a>Wersja MySQL 5.7.20
Zapoznaj się MySQL [dokumentacji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-20.htmll) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w MySQL 5.7.20.

> [!NOTE]
> W usłudze brama służy do przekierowywania połączenia do wystąpienia serwera. Po nawiązaniu połączenia klienta MySQL Wyświetla wersję MySQL w bramie nie tylko wersja uruchomione w wystąpieniu serwera MySQL. Można ustalić wersji wystąpienia serwera MySQL, użyj `SELECT VERSION();` polecenie w wierszu polecenia programu MySQL. 

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawki aktualizacji wersji pomocniczej. Nie są obsługiwane uaktualnienia wersji głównej (np. Uaktualnianie z MySQL 5.6 do MySQL 5.7).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące określonego zasobu Przydziały i ograniczenia na podstawie Twojej **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
