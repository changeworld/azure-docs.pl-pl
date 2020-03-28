---
title: Skrypt programu PowerShell w celu uzyskania operacji klucza i ciągu połączenia dla konta usługi Azure Cosmos DB
description: Przykład skryptu programu Azure PowerShell — operacje klucza konta i ciągu połączenia dla konta usługi Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: f6dd9d1290ea9d18fc6a5f18196585926b2ab91a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366102"
---
# <a name="connection-string-and-account-key-operations-for-an-azure-cosmos-db-account-using-powershell"></a>Operacje na ciąg połączenia i klucze konta dla konta usługi Azure Cosmos DB przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Przykładowy skrypt

> [!NOTE]
> W tym przykładzie pokazano przy użyciu konta interfejsu API SQL. Aby użyć tego przykładu dla innych interfejsów API, skopiuj powiązane właściwości i zastosuj do skryptu specyficznego dla interfejsu API

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-keys-connection-strings.ps1 "Connection strings and account keys for Azure Cosmos account")]

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Po wykonaniu przykładowego skryptu możesz uruchomić następujące polecenie, aby usunąć grupę zasobów i wszystkie skojarzone z nią zasoby.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

W tym skrypcie użyto następujących poleceń. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
|**Azure Cosmos DB**| |
| [Klucz Dostępu DookosmosDBKtac](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccountkey) | Pobiera ciąg połączenia lub klucz (do odczytu i zapisu lub tylko do odczytu) dla konta usługi Cosmos DB. |
| [Nowy klucz AzCosmosDBAccountKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccountkey) | Ponownie wygeneruj określony klucz dla konta usługi Cosmos DB. |
|**Grupy zasobów platformy Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |
|||

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programu Azure PowerShell, zobacz [dokumentację programu Azure PowerShell](https://docs.microsoft.com/powershell/).

Więcej przykładowych skryptów programu PowerShell dla usługi Azure Cosmos DB można znaleźć w artykule [Azure Cosmos DB PowerShell scripts](../../../powershell-samples.md) (Skrypty programu PowerShell dla usługi Azure Cosmos DB).
