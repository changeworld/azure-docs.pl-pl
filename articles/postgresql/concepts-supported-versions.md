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
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630593"
---
# <a name="supported-postgresql-database-versions"></a>Wersje obsługiwanych bazy danych PostgreSQL
Microsoft dąży do obsługi n-2 wersje aparatu PostgreSQL w usługi Azure Database for postgresql w warstwie usługi, co oznacza aktualnie wydana wersja główna (n) i dwóch starszych wersjach głównych (-2).

Usługa Azure Database for postgresql w warstwie obecnie obsługuje następujące wersje:

## <a name="postgresql-version-104"></a>Wersja PostgreSQL 10.4
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-4.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-969"></a>Wersja PostgreSQL 9.6.9
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-9513"></a>Wersja PostgreSQL 9.5.13
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacji i uaktualnień
Usługa Azure Database for postgresql w warstwie automatycznie zarządza poprawek dla aktualizacji wersji pomocniczej. Obecnie nie jest obsługiwane uaktualnienie wersji głównej. Na przykład uaktualnienie z PostgreSQL 9.5 do PostgreSQL 9.6 nie jest obsługiwane. Jeśli chcesz uaktualnić do następnej wersji głównej, [zrzucanie i przywracanie](./howto-migrate-using-dump-and-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o obsłudze inne rozszerzenia PostgreSQL, zobacz [rozszerzenia PostgreSQL](concepts-extensions.md).
