---
title: Przykład interfejsu wiersza polecenia — grupa trybu failover — pula elastyczna bazy danych SQL Azure
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure, aby utworzyć pulę elastyczną bazy danych SQL platformy Azure, dodać ją do grupy trybu failover i przetestować przełączenie w błąd.
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
ms.openlocfilehash: 2d6f18e373327b758e766dffba341c080622301f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061939"
---
# <a name="use-cli-to-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Dodawanie puli elastycznej bazy danych AZURE SQL database do grupy trybu failover za pomocą interfejsu wiersza polecenia

Ten przykład skryptu interfejsu wiersza polecenia platformy Azure tworzy pojedynczą bazę danych, dodaje ją do puli elastycznej, tworzy grupę trybu failover i testuje tryb failover.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Przykładowy skrypt

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Uruchamianie skryptu

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-cli.sh "Add elastic pool to a failover group")]

### <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Użyj następującego polecenia, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Przykładowy numer referencyjny

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| | |
|---|---|
| [az sql elastyczna pula](/cli/azure/sql/elastic-pool) | Polecenia puli elastycznej. |
| [az sql grupa trybu failover](/cli/azure/sql/failover-group) | Polecenia grupy trybu failover. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure/overview).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia platformy SQL Database azure można znaleźć w [skryptach interfejsu wiersza polecenia platformy Azure bazy danych SQL.](../sql-database-cli-samples.md)
