---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 0683516d16bf1501eee83901c5171811b8c0e44d
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621551"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Zarządzanie kontami bazy danych w usłudze Azure Cosmos DB

W tym artykule opisano, jak zarządzać kontem usługi Azure Cosmos DB, aby skonfigurować używanie wielu adresów, dodać/usunąć region, skonfigurować wiele regionów zapisu i skonfigurować priorytety trybu failover. 

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>Konfigurowanie klientów pod kątem multihostingu

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new Connection Policy
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to Preferred locations
// The name of the location will match what you see in the portal/etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the Connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>Dodawanie/usuwanie regionów z poziomu konta bazy danych

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos DB i otwórz menu **Replikuj dane globalnie**.

2. Aby dodać regiony, wybierz jeden lub kilka regionów z mapy, klikając puste sześciokąty z etykietą **„+”** odpowiadające wybranym regionom. Region można również dodać, wybierając opcję **+ Dodaj region** i wybierając region z menu rozwijanego.

3. Aby usunąć regiony, usuń zaznaczenie jednego lub kilku regionów na mapie, klikając niebieskie sześciokąty ze znacznikiem wyboru, lub wybierz ikonę „kosz na śmieci” (🗑) po prawej stronie regionu.

4. Kliknij przycisk Zapisz, aby zapisać zmiany.

   ![Menu dodawania/usuwania regionów](./media/how-to-manage-database-account/add-region.png)

W trybie zapisu w jednym regionie nie można usunąć regionu zapisu. Przed usunięciem bieżącego regionu zapisu należy przełączyć do trybu failover w innym regionie.

W trybie zapisu w wielu regionach można dodawać/usuwać dowolne regiony, o ile pozostaje co najmniej jeden region.

### <a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>Konfigurowanie wielu regionów zapisu

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Podczas tworzenia konta bazy danych upewnij się, że jest włączone ustawienie **Moduły zapisujące obsługujące wiele regionów**.

![Zrzut ekranu tworzenia konta usługi Azure Cosmos](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Szablon usługi Resource Manager

Poniższy kod JSON jest przykładowym szablonem usługi Resource Manager. Przy jego użyciu można wdrożyć konto usługi Azure Cosmos z zasadami zgodności Powiązana nieaktualność, maksymalnym interwałem nieaktualności równym 5 sekund i maksymalną tolerowaną liczbą żądań nieaktualności równą 100. Aby dowiedzieć się więcej o formacie szablonu usługi Resource Manager oraz o składni, zapoznaj się dokumentacją [usługi Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
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


## <a id="manual-failover"></a>Włączanie ręcznego przejścia do trybu failover dla konta usługi Azure Cosmos

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos i otwórz menu **„Replikuj dane globalnie”**.

2. Kliknij przycisk **„Ręczne przejście do trybu failover”** u góry menu.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W menu **„Ręczne przejście do trybu failover”** wybierz nowy region zapisu i zaznacz pole, aby potwierdzić, że rozumiesz, że ta opcja spowoduje zmianę regionu zapisu.

4. Kliknij przycisk „OK”, aby wyzwolić tryb failover.

   ![Menu Ręczne przejście do trybu failover w portalu](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>Włączanie automatycznego przejścia do trybu failover dla konta usługi Azure Cosmos

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Z poziomu konta usługi Azure Cosmos otwórz okienko **„Replikuj dane globalnie”**. 

2. Kliknij przycisk **„Automatyczne przełączanie w tryb failover”** znajdujący się u góry okienka.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **„Automatyczne przełączanie w tryb failover”** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ.**. 

4. Kliknij pozycję Zapisz u dołu menu.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

W tym menu można również ustawić priorytety trybu failover.

### <a id="enable-automatic-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Z poziomu konta usługi Azure Cosmos otwórz okienko **„Replikuj dane globalnie”**. 

2. Kliknij przycisk **„Automatyczne przełączanie w tryb failover”** znajdujący się u góry okienka.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **„Automatyczne przełączanie w tryb failover”** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ.**. 

4. Priorytety trybu failover można modyfikować, klikając i przeciągając regiony odczytu za pośrednictwem trzech kropek znajdujących się po lewej stronie wiersza, który jest wyświetlany po umieszczeniu na nich wskaźnika myszy. 

5. Kliknij pozycję Zapisz u dołu menu.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

W tym menu nie można modyfikować regionu zapisu. Aby ręcznie zmienić region zapisu, należy wykonać ręczne przejście do trybu failover.

### <a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>Następne kroki

Poniższa dokumentacja zawiera informacje na temat zarządzania poziomami spójności i konfliktami danych w usłudze Azure Cosmos DB:

* [Jak zarządzać spójnością](how-to-manage-consistency.md)
* [Jak zarządzać konfliktami między regionami](how-to-manage-conflicts.md)

