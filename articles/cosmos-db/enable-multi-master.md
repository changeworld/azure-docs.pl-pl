---
title: Włącz Multi-Master dla kont usługi Azure Cosmos DB
description: W tym artykule opisano sposób włączania obsługi wielu wzorców, podczas tworzenia konta usługi Azure Cosmos DB przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia lub szablonu usługi Azure Resource Manager.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b6fc04fc728f753dc8a3900b26c6c01f03cc7710
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077242"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Włącz Multi-Master dla kont usługi Azure Cosmos DB

Obsługa wielu wzorców jest włączona podczas tworzenia konta usługi Azure Cosmos DB. Konto usługi Azure Cosmos DB można tworzyć przy użyciu witryny Azure portal, programu PowerShell, interfejsu wiersza polecenia lub szablonu usługi Azure Resource Manager.

> [!IMPORTANT]
> Obecnie można włączyć obsługę wielu wzorców dla nowych kont usługi Azure Cosmos DB. Istniejące konta usługi Azure Cosmos DB nie można użyć funkcji. Firma Microsoft pracuje w celu zapewnienia obsługi istniejących kont i pojawi się informacja ta obsługa, gdy będzie ona dostępna.

Po utworzeniu konta usługi Azure Cosmos DB z obsługą wielu wzorców, można tworzyć bazy danych, kontenerów, przekazywać dokumenty i przypisać konflikt zasad rozpoznawania. Do rozwiązywania konfliktów w Multi-Master oraz przykłady kodu, zobacz [przykłady kodu wielu wzorców](multi-master-conflict-resolution.md#code-samples) artykułu.

## <a name="enable-multi-master-using-azure-portal"></a>Multi-Master Włącz przy użyciu witryny Azure portal

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij Wybierz **Utwórz zasób > bazy danych > usługi Azure Cosmos DB**.

3. W **nowe konto** okienku, wprowadź następujące ustawienia dla nowego konta usługi Azure Cosmos DB:

   |**Ustawienie**  |**Sugerowana wartość** |**Opis**|
   |---------|---------|---------|
   |Subskrypcja   | {Subscription}  |Wybierz subskrypcję platformy Azure do użycia dla tego konta usługi Azure Cosmos DB.  |
   |Grupa zasobów  |   {Nazwa grupy zasobów}    |  Wybierz istniejącą grupę zasobów lub wybierz **Utwórz nowy**, następnie wprowadź nową nazwę grupy zasobów dla swojego konta. |
   |Nazwa konta | {Nazwa Twojego konta}   |  Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB.        |
   |Interfejs API  |   Dowolne   |  Wybierz typ interfejsu API.   |
   |Lokalizacja  | Wybierz dowolny region   | Wybierz lokalizację geograficzną, w której będzie hostowane Twoje konto usługi Azure Cosmos DB. Można wybrać dowolny region, ponieważ to konto będzie w wielu regionach.  |
   |Włącz nadmiarowość geograficzną   |  Włączanie  |  Zaznacz, aby włączyć wielu główny, aby wybrać poniżej.   |
   |Włącz wiele wzorców | Włączanie  | Wybierz, aby umożliwić wielu wzorzec dla tego konta. |


## <a name="using-multi-master-in-sdks"></a>Za pomocą Multi-Master w zestawy SDK

Przy użyciu konta Multi-Master włączone w ramach aplikacji, możesz korzystać z zalet mastering wielu dzięki wykorzystaniu ConnectionPolicy, jak pokazano poniżej.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add(LocationNames.WestUS);
policy.PreferredLocations.Add(LocationNames.NorthEurope);
policy.PreferredLocations.Add(LocationNames.SoutheastAsia);
```

## <a name="enable-multi-master-using-powershell"></a>Włączanie wielu wzorców, przy użyciu programu PowerShell

Można również utworzyć wielu wzorców włączone konto usługi Cosmos DB, ustawiając `enableMultipleWriteLocations` parametru na wartość "true". Aby utworzyć konto usługi Azure Cosmos DB przy użyciu Multi-Master włączone, Otwórz okno programu PowerShell i uruchom następujący skrypt:

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Włączanie wielu wzorców, używając interfejsu wiersza polecenia

Można włączyć Multi-Master, ustawiając parametr enable wielu zapisu — lokalizacje na wartość "true". Aby utworzyć konto usługi Azure Cosmos DB z Multi-Master włączone, otworzyć wiersza polecenia platformy Azure lub cloud shell i uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Włączanie wielu wzorców przy użyciu szablonu usługi Resource Manager

Poniższy kod JSON jest przykładowy szablon usługi Resource Manager, którego można użyć do wdrożenia konta usługi Azure Cosmos DB. Aby dowiedzieć się więcej o formacie szablonu usługi Resource Manager i składnię, zobacz [usługi Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) dokumentacji. Parametr klucza, aby zobaczyć, w tym szablonie jest "enableMultipleWriteLocations": true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule przedstawiono sposób włączania wielu wzorców obsługi dla kont usługi Azure Cosmos DB. Następnie Przyjrzyj się następujące zasoby:

* [Używanie Multi-Master z bazy danych NoSQL typu open source](multi-master-oss-nosql.md)

* [Opis rozwiązywania konfliktów w usłudze Azure Cosmos DB](multi-master-conflict-resolution.md)
