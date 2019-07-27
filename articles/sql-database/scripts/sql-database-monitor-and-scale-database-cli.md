---
title: Przykładowy skrypt interfejsu wiersza polecenia — monitorowanie i skalowanie pojedynczej bazy danych Azure SQL Database | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure do monitorowania i skalowania pojedynczej bazy danych Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: 9468f5d631dd713c9e131c63de824c5e552178e3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569909"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Monitorowanie i skalowanie pojedynczej bazy danych SQL

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure skaluje pojedynczą bazę danych Azure SQL Database do innego rozmiaru obliczeniowego po uzyskaniu informacji o rozmiarze bazy danych za pomocą zapytania.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Użyj polecenia [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) w celu uzyskania listy operacji wykonanych na bazie danych lub użyj polecenia [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) w celu anulowania operacji aktualizacji w bazie danych.

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj poniższego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer usługi SQL Database hostujący pojedyncze bazy danych i elastyczne pule. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql#az-sql-show-usage) | Pokazuje informacje o użyciu rozmiaru dla pojedynczej bazy danych lub bazy danych w puli. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Aktualizuje właściwości bazy danych (takie jak warstwa usługi lub rozmiar obliczeniowy) lub przenosi bazę danych do pul elastycznych, poza nie lub między nimi. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
