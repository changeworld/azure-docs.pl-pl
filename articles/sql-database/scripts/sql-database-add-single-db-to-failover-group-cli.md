---
title: Przykład interfejsu wiersza polecenia — Dodawanie pojedynczej bazy danych do grupy trybu failover — Azure SQL Database
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure do utworzenia Azure SQL Database pojedynczej bazy danych, dodania jej do grupy trybu failover i przetestowania trybu failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: b80be0db41ea8bd3996a7b8843c80f510289545f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570059"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Korzystanie z interfejsu wiersza polecenia do przenoszenia bazy danych Azure SQL Database do elastycznej puli SQL

Ten przykładowy skrypt programu PowerShell tworzy pojedynczą bazę danych, tworzy grupę trybu failover, dodaje do niej bazę danych i testuje tryb failover. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to failover group")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [AZ Account Set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Ustawia subskrypcję jako bieżącą aktywną subskrypcję. | 
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [AZ SQL Server firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Tworzy reguły zapory serwera. | 
| [AZ SQL failover-Group Create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Tworzy grupę trybu failover. | 
| [AZ SQL failover-Group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Wyświetla listę grup trybu failover na serwerze. |
| [AZ SQL failover-Group Set-Primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Ustaw podstawową grupę trybu failover, przełączając się na wszystkie bazy danych z bieżącego serwera podstawowego. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
