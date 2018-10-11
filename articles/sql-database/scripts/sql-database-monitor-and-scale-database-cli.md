---
title: Przykładowy skrypt interfejsu wiersza polecenia — monitorowanie i skalowanie pojedynczej bazy danych Azure SQL Database | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure do monitorowania i skalowania pojedynczej bazy danych Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/02/2018
ms.openlocfilehash: 9021f04dad41f81b935b9412900336687011e87a
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249268"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Monitorowanie i skalowanie pojedynczej bazy danych SQL

Ten przykładowy skrypt interfejsu wiersza polecenia platformy Azure skaluje pojedynczą bazę danych usługi Azure SQL Database do innego poziomu wydajności po uzyskaniu informacji o rozmiarze bazy danych za pomocą zapytania. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Użyj polecenia [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) w celu uzyskania listy operacji wykonanych na bazie danych lub użyj polecenia [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) w celu anulowania operacji aktualizacji w bazie danych.

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Tworzy serwer logiczny hostujący bazę danych. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-show-usage) | Pokazuje informacje o użyciu rozmiaru dla bazy danych. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Aktualizuje właściwości bazy danych (takie jak poziom wydajności lub rozmiar obliczeniowy) lub przenosi bazę danych do pul elastycznych, poza nie lub między nimi. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi SQL Database można znaleźć w [dokumentacji usługi Azure SQL Database](../sql-database-cli-samples.md).
