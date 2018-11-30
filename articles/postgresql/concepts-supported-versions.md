---
title: Obsługiwane wersje w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database for PostgreSQL.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/12/2018
ms.openlocfilehash: a03ead5e577b261b99e635addf6f9a98d8f3cadd
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619698"
---
# <a name="supported-postgresql-database-versions"></a>Obsługiwane wersje bazy danych PostgreSQL
Microsoft dąży do obsługi n-2 wersje aparatu PostgreSQL w usłudze Azure Database for postgresql w warstwie usługi. Wersje byłoby bieżącej wersji głównej na platformie Azure (n) i dwóch starszych wersjach głównych (-2).

Usługa Azure Database for postgresql w warstwie obecnie obsługuje następujące wersje:

## <a name="postgresql-version-105"></a>Wersja PostgreSQL 10.5
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-5.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-9610"></a>Wersja PostgreSQL 9.6.10
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-9514"></a>Wersja PostgreSQL 9.5.14
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień
Usługa Azure Database for postgresql w warstwie automatycznie zarządza poprawki wersji pomocniczej. Obecnie nie jest obsługiwane uaktualnienie wersji głównej. Na przykład uaktualnienie z PostgreSQL 9.5 do PostgreSQL 9.6 nie jest obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, należy utworzyć bazę danych [zrzucanie i przywracanie](./howto-migrate-using-dump-and-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o obsłudze inne rozszerzenia PostgreSQL, zobacz [rozszerzenia PostgreSQL](concepts-extensions.md).
