---
title: Skrypt interfejsu wiersza polecenia platformy Azure — skalowanie serwera usługi Azure Database for MariaDB
description: Ten przykładowy skrypt interfejsu wiersza polecenia służy do skalowania serwera usługi Azure Database for MariaDB na inny poziom wydajności po wykonaniu zapytania względem metryk.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/07/2019
ms.openlocfilehash: 772c4de9c53f618d0c7c5d8b8d6acc2830595f55
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884142"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Monitorowanie i skalowanie serwera usługi Azure Database for MariaDB za pomocą interfejsu wiersza polecenia platformy Azure
Ten przykładowy skrypt interfejsu wiersza polecenia skaluje zasoby obliczeniowe i magazyn dla jednego serwera Azure Database for MariaDB po wykonaniu zapytania o metryki. Obliczenia można skalować w górę lub w dół. Magazyn można skalować w górę.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się uruchomić interfejs wiersza polecenia lokalnie, na potrzeby tego artykułu wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Sprawdź wersję, uruchamiając polecenie `az --version`. Aby zainstalować lub uaktualnić interfejs wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt
Zaktualizuj skrypt przy użyciu identyfikatora subskrypcji.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia
Po uruchomieniu skryptu użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Objaśnienia dla skryptu
Ten skrypt używa poleceń opisanych w poniższej tabeli:

| **Polecenie** | **Uwagi** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Tworzy serwer MariaDB hostujący bazy danych. |
| [AZ MariaDB Server Update](/cli/azure/mariadb/server#az-mariadb-server-update) | Aktualizuje właściwości serwera MariaDB. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Zwraca wartość metryki dla zasobów. |
| [az group delete](/cli/azure/group#az-group-delete) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat [Azure Database for MariaDB zasobów obliczeniowych i magazynu](../concepts-pricing-tiers.md)
- Wypróbuj dodatkowe skrypty: [Przykłady interfejsu wiersza polecenia platformy Azure dla usługi Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Dowiedz się więcej o [interfejsie wiersza polecenia platformy Azure](/cli/azure)
