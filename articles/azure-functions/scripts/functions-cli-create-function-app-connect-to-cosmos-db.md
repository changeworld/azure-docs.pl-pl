---
title: Tworzenie funkcji platformy Azure łączącej się z usługą Azure Cosmos DB | Microsoft Docs
description: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — tworzenie funkcji platformy Azure łączącej się z usługą Azure Cosmos DB
services: functions
documentationcenter: functions
author: ggailey777
manager: cfowler
editor: ''
tags: functions
ms.assetid: ''
ms.service: functions
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 01/22/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 092e0681b0491fc1c54c19e234aafdac6d428fd1
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29843225"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Tworzenie funkcji platformy Azure łączącej się z usługą Azure Cosmos DB

Ten przykładowy skrypt usługi Azure Functions tworzy aplikację funkcji i łączy funkcję z bazą danych usługi Azure Cosmos DB. Ustawienie utworzonej aplikacji, które zawiera parametry połączenia, może być używane z [wyzwalaczem lub powiązaniem usługi Azure Cosmos DB](..\functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli używasz interfejsu wiersza polecenia lokalnie, upewnij się, że korzystasz z interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Przykładowy skrypt

Ten przykładowy skrypt tworzy aplikację funkcji platformy Azure i dodaje do jej ustawień punkt końcowy i klucz dostępu usługi Azure Cosmos DB.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login) | Loguje się do platformy Azure. |
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Tworzy grupę zasobów w lokalizacji. |
| [az storage accounts create](https://docs.microsoft.com/cli/azure/storage/account) | Tworzenie konta magazynu |
| [az functionapp create](https://docs.microsoft.com/cli/azure/functionapp#az_functionapp_create) | Tworzy nową aplikację funkcji. |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az_cosmosdb_create) | Tworzy bazę danych cosmosdb. |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az_group_delete) | Czyszczenie |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure).

Więcej przykładowych skryptów interfejsu wiersza polecenia dla usługi Azure Functions można znaleźć w [dokumentacji usługi Azure Functions](../functions-cli-samples.md).




