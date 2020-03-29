---
title: Magazyn automatycznego powiększania — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database for PostgreSQL — pojedynczy serwer
description: W tym artykule opisano, jak skonfigurować automatyczne powiększanie magazynu przy użyciu interfejsu wiersza polecenia platformy Azure w bazie danych azure dla postgreSQL — pojedynczy serwer.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 8/7/2019
ms.openlocfilehash: b0dc2fbb168d9325439ee18a227f71a3b88ef9c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74767963"
---
# <a name="auto-grow-azure-database-for-postgresql-storage---single-server-using-the-azure-cli"></a>Automatyczne powiększanie bazy danych platformy Azure dla magazynu PostgreSQL — pojedynczy serwer przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule opisano, jak skonfigurować usługę Azure Database dla magazynu serwera PostgreSQL, aby rosnąć bez wpływu na obciążenie.

Serwer [osiągający limit miejsca do magazynowania](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#reaching-the-storage-limit)jest ustawiony na tylko do odczytu. Jeśli automatyczne powiększanie magazynu jest włączona, a następnie dla serwerów z mniej niż 100 GB aprowizowanego miejsca, aprowizowana wielkość magazynu zwiększa się o 5 GB, gdy tylko wolny magazyn jest poniżej większej niż 1 GB lub 10% aprowizowanego magazynu. W przypadku serwerów z więcej niż 100 GB aprowizowanego miejsca do magazynowania rozmiar aprowizowanego magazynu zwiększa się o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 5% rozmiaru aprowizowanego magazynu. Maksymalne limity magazynowania określone [w tym miejscu](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage) mają zastosowanie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik instrukcja wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby potwierdzić wersję, w wierszu `az --version`polecenia interfejsu wiersza polecenia platformy Azure należy wprowadzić polecenie . Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="enable-postgresql-server-storage-auto-grow"></a>Automatyczne zwiększanie pamięci masowej serwera PostgreSQL

Włącz magazyn automatycznego powiększania serwera na istniejącym serwerze za pomocą następującego polecenia:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Włącz magazyn automatycznego powiększania serwera podczas tworzenia nowego serwera za pomocą następującego polecenia:

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 9.6
```

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące danych](howto-alert-on-metric.md).