---
title: Obsługiwane wersje w usłudze Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano obsługiwane wersje w usłudze Azure Database for PostgreSQL — pojedynczy serwer.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 0fc4eb90d9bed3877b4618fd839d9e8bf1608718
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063739"
---
# <a name="supported-postgresql-database-versions"></a>Obsługiwane wersje bazy danych PostgreSQL
Cele firmy Microsoft do obsługi n-2 wersje aparatu PostgreSQL w usłudze Azure Database for PostgreSQL — pojedynczy serwer. Wersje byłoby bieżącej wersji głównej na platformie Azure (n) i dwóch starszych wersjach głównych (-2).

Usługa Azure Database for postgresql w warstwie obecnie obsługuje następujące wersje:

## <a name="postgresql-version-112"></a>Wersja PostgreSQL 11.2
Zapoznaj się [dokumentacji PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-2.html) Aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

>[!NOTE]
> PostgreSQL w wersji 11 jest dostępna w wersji zapoznawczej. Obsługa tworzenia przy użyciu portalu Azure jest wdrażana i mogą jeszcze być niedostępne w Twoim regionie. Możesz użyć [wiersza polecenia platformy Azure](quickstart-create-server-database-azure-cli.md) utworzyć serwer Postgres 11 w dowolnym regionie. Na przykład `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11`.

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
