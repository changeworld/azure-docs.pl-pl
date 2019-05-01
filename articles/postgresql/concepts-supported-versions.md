---
title: Obsługiwane wersje w usłudze Azure Database for PostgreSQL
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database for PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 16064d662c5b101e30f8d2fbb64b39db0848f49f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702306"
---
# <a name="supported-postgresql-database-versions"></a>Obsługiwane wersje bazy danych PostgreSQL
Microsoft dąży do obsługi n-2 wersje aparatu PostgreSQL w usłudze Azure Database for postgresql w warstwie usługi. Wersje byłoby bieżącej wersji głównej na platformie Azure (n) i dwóch starszych wersjach głównych (-2).

Usługa Azure Database for postgresql w warstwie obecnie obsługuje następujące wersje:

## <a name="postgresql-version-107"></a>Wersja PostgreSQL 10,7
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-7.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-9612"></a>Wersja PostgreSQL 9.6.12
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-9516"></a>Wersja PostgreSQL 9.5.16
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień
Usługa Azure Database for postgresql w warstwie automatycznie zarządza poprawki wersji pomocniczej. Obecnie nie jest obsługiwane uaktualnienie wersji głównej. Na przykład uaktualnienie z PostgreSQL 9.5 do PostgreSQL 9.6 nie jest obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, należy utworzyć bazę danych [zrzucanie i przywracanie](./howto-migrate-using-dump-and-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o obsłudze inne rozszerzenia PostgreSQL, zobacz [rozszerzenia PostgreSQL](concepts-extensions.md).
