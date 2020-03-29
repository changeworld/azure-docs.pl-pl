---
title: Automatyczne powiększanie magazynu — narzędzie interfejsu wiersza polecenia platformy Azure — usługa Azure Database dla mysql
description: W tym artykule opisano, jak włączyć automatyczne powiększanie magazynu przy użyciu interfejsu wiersza polecenia platformy Azure w bazie danych azure dla mysql.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 44ce852aaf2ed5839650132c6eae95728c27dc5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062631"
---
# <a name="auto-grow-azure-database-for-mysql-storage-using-the-azure-cli"></a>Automatyczne powiększanie usługi Azure Database dla magazynu MySQL przy użyciu interfejsu wiersza polecenia platformy Azure
W tym artykule opisano, jak skonfigurować usługę Azure Database dla magazynu serwera MySQL, aby rosła bez wpływu na obciążenie.

Serwer [osiągający limit miejsca do magazynowania](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#reaching-the-storage-limit)jest ustawiony na tylko do odczytu. Jeśli automatyczne powiększanie magazynu jest włączona, a następnie dla serwerów z mniej niż 100 GB aprowizowanego miejsca, aprowizowana wielkość magazynu zwiększa się o 5 GB, gdy tylko wolny magazyn jest poniżej większej niż 1 GB lub 10% aprowizowanego magazynu. W przypadku serwerów z więcej niż 100 GB aprowizowanego miejsca do magazynowania rozmiar aprowizowanego magazynu zwiększa się o 5%, gdy ilość wolnego miejsca do magazynowania jest mniejsza niż 5% rozmiaru aprowizowanego magazynu. Maksymalne limity magazynowania określone [w tym miejscu](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage) mają zastosowanie.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, potrzebujesz:
- Usługa [Azure Database dla serwera MySQL](quickstart-create-mysql-server-database-using-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> Ten przewodnik instrukcja wymaga użycia interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby potwierdzić wersję, w wierszu `az --version`polecenia interfejsu wiersza polecenia platformy Azure należy wprowadzić polecenie . Aby zainstalować lub uaktualnić, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="enable-mysql-server-storage-auto-grow"></a>Automatyczne włączanie pamięci masowej serwera MySQL

Włącz magazyn automatycznego powiększania serwera na istniejącym serwerze za pomocą następującego polecenia:

```azurecli-interactive
az mysql server update --name mydemoserver --resource-group myresourcegroup --auto-grow Enabled
```

Włącz magazyn automatycznego powiększania serwera podczas tworzenia nowego serwera za pomocą następującego polecenia:

```azurecli-interactive
az mysql server create --resource-group myresourcegroup --name mydemoserver  --auto-grow Enabled --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 5.7
```

## <a name="next-steps"></a>Następne kroki

Dowiedz [się, jak tworzyć alerty dotyczące danych](howto-alert-on-metric.md).