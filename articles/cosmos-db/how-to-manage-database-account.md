---
title: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
description: Dowiedz się, jak zarządzać kontami bazy danych w usłudze Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 07d177987db1dea261520e8ee2543d871d552acb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240886"
---
# <a name="manage-an-azure-cosmos-account"></a>Zarządzanie kontem usługi Azure Cosmos

W tym artykule opisano sposób zarządzania różnych zadań na konto usługi Azure Cosmos przy użyciu witryny Azure portal, programu Azure PowerShell, interfejsu wiersza polecenia platformy Azure i szablonów usługi Azure Resource Manager.

## <a name="create-an-account"></a>Tworzenie konta

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Create an account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname' # must be lower case.

az cosmosdb create \
   --name $accountName \
   --resource-group $resourceGroupName \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations WestUS=0 EastUS=1 \
   --enable-multiple-write-locations true
```

### <a id="create-database-account-via-ps"></a>Azure PowerShell
```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="true"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="create-database-account-via-arm-template"></a>Szablon usługi Azure Resource Manager

Ten szablon usługi Azure Resource Manager utworzy konto usługi Azure Cosmos DB dla wszystkich obsługiwanych API skonfigurowany przy użyciu dwóch regionach i opcji, aby wybrać poziom spójności, automatycznej pracy awaryjnej i Multi-Master. Aby wdrożyć ten szablon, kliknij pozycję Wdróż na platformie Azure na stronie readme [Tworzenie usługi Azure Cosmos DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-create-multi-region-account)

## <a name="addremove-regions-from-your-database-account"></a>Dodawanie/usuwanie regionów z poziomu konta bazy danych

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Zaloguj się w [portalu Azure](https://portal.azure.com). 

1. Przejdź do konta usługi Azure Cosmos, a następnie otwórz **Replikuj dane globalnie** menu.

1. Aby dodać regionów, wybierz Sześciokąty na mapie za pomocą **+** etykietę, która odpowiada na regiony, które żądaną. Aby dodać region, wybierz opcję **+ Dodaj region** opcji, a następnie wybierz region, z menu rozwijanego.

1. Aby usunąć regiony, usuń co najmniej jeden region z mapy, wybierając niebieskie sześciokąty ze znacznikami wyboru. Możesz też wybrać ikonę „kosza na śmieci” (🗑) z prawej strony regionu.

1. Aby zapisać zmiany, wybierz pozycję **OK**.

   ![Menu dodawania lub usuwania regionów](./media/how-to-manage-database-account/add-region.png)

W jednym regionie tryb zapisu, nie można usunąć regionu zapisu. Użytkownik musi pracy awaryjnej w innym regionie przed usunięciem bieżącego regionu zapisu.

W wielu regionach zapisu trybu, można dodać lub usunąć dowolny region, w przypadku, jeśli masz co najmniej jednego regionu.

### <a id="add-remove-regions-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

# Create an account with 1 region
az cosmosdb create --name $accountName --resource-group $resourceGroupName --locations westus=0

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0
```

### <a id="add-remove-regions-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Create an account with 1 region
$resourceGroupName = "myResourceGroup"
$location = "West US"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )
$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy
}
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Add a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( 
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="East Us"; "failoverPriority"=1 } 
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties

# Azure Resource Manager does not wait on the resource update
Write-Host "Confirm region added before continuing..."

# Remove a region
$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @( @{ "locationName"="West US"; "failoverPriority"=0 } )

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a id="configure-multiple-write-regions"></a>Konfigurowanie wielu regionów zapisu

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

Otwórz **Replikuj dane globalnie** kartę, a następnie wybierz pozycję **Włącz** umożliwiające zapisów w wielu regionach. Po włączeniu zapisów w wielu regionach, wszystkich regionów odczytu, które mają obecnie na koncie będzie stają się odczytu i zapisu regionów. 

> [!NOTE]
> Po włączeniu zapisów w wielu regionach, nie można wyłączyć. 

![Konto usługi Cosmos Azure umożliwia skonfigurowanie wielu wzorców zrzut ekranu](./media/how-to-manage-database-account/single-to-multi-master.png)

Skontaktuj się z askcosmosdb@microsoft.com alias dodatkowe pytania na temat tej funkcji. 

### <a id="configure-multiple-write-regions-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'
az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Update an Azure Cosmos Account from single to multi-master

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$account.Properties.enableMultipleWriteLocations = "true"
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="configure-multiple-write-regions-arm"></a>Szablon usługi Resource Manager

Konto można przeprowadzić migrację z pojedynczego elementu głównego do Multi-Master przez wdrożenie szablonu usługi Resource Manager, używanego do utworzenia konta i ustawienie `enableMultipleWriteLocations: true`. Następujący szablon usługi Azure Resource Manager jest szablonem minimalne systemu od zera, który wdroży konta usługi Azure Cosmos DB dla interfejsu API SQL za pomocą jednego regionu i Multi-Master włączone.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations": [
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a id="automatic-failover"></a>Włączanie automatycznego przejścia do trybu failover dla konta usługi Azure Cosmos DB

Opcji automatycznej pracy awaryjnej umożliwia usługi Azure Cosmos DB w tryb failover do regionu z najwyższym priorytetem trybu failover z żadna akcja użytkownika powinna region staną się niedostępne. Po włączeniu automatycznej pracy awaryjnej, mogą być modyfikowane priorytetu regionu. Konto musi mieć co najmniej dwóch regionach w celu włączenia automatycznej pracy awaryjnej.

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Z poziomu konta usługi Azure Cosmos DB otwórz okienko **Replikuj dane globalnie**.

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**. 

4. Wybierz pozycję **Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="enable-automatic-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Enable automatic failover on an existing account
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --enable-automatic-failover true
```

### <a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$account.Properties.enableAutomaticFailover="true";
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Ustawianie priorytetów trybu failover dla konta usługi Azure Cosmos

Po skonfigurowaniu konta usługi Cosmos do automatycznej pracy awaryjnej można zmienić priorytet trybu failover dla regionów.

> [!IMPORTANT]
> Nie można zmodyfikować regionie zapisu (trybu failover priorytet równy zero) gdy konto jest skonfigurowane do automatycznej pracy awaryjnej. Aby zmienić region zapisu, należy wyłączyć automatycznej pracy awaryjnej i wykonaj ręcznej pracy awaryjnej.

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Z konta usługi Azure Cosmos Otwórz **Replikuj dane globalnie** okienka.

2. U góry okienka wybierz pozycję **Automatyczne przełączanie w tryb failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W okienku **Automatyczne przełączanie w tryb failover** upewnij się, że przełącznik **Włącz automatyczne przejście w tryb failover** jest ustawiony w pozycji **WŁ**.

4. Aby zmodyfikować priorytety trybu failover, przeciągnij regiony odczytu za pośrednictwem trzech kropek znajdujących się po lewej stronie wiersza, który jest wyświetlany po umieszczeniu na nich wskaźnika myszy.

5. Wybierz pozycję **Zapisz**.

   ![Menu Automatyczne przełączanie w tryb failover w portalu](./media/how-to-manage-database-account/automatic-failover.png)

### <a id="set-failover-priorities-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Assume region order is initially eastus=0 westus=1 southeastasia=2 on account creation
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb failover-priority-change --name $accountName --resource-group $resourceGroupName --failover-policies eastus=0 southeastasia=1 westus=2
```

### <a id="set-failover-priorities-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1, Southeast Asia = 2
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$failoverPolicies = @(
    @{ "locationName"="West US"; "failoverPriority"=0 },
    @{ "locationName"="Southeast Asia"; "failoverPriority"=1 },
    @{ "locationName"="East US"; "failoverPriority"=2 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a id="manual-failover"></a>Wykonaj ręcznej pracy awaryjnej przy użyciu konta usługi Azure Cosmos

> [!IMPORTANT]
> Konto usługi Cosmos Azure musi być skonfigurowany do ręcznej pracy awaryjnej dla tej operacji.

Proces przeprowadzania ręcznego przełączania trybu failover obejmuje zmiana regionu zapisu dla konta (priorytetem trybu failover = 0) do innego regionu skonfigurowany dla konta.

> [!NOTE]
> Wielu wzorców kont nie można ręcznie przełączone w tryb failover. Dla aplikacji przy użyciu zestawu SDK usługi Azure Cosmos DB zestaw SDK będzie Wykryj, kiedy region staje się niedostępny, a następnie nastąpi automatyczne przekierowanie do następnego regionu najbliższego Jeśli za pomocą międzyregionalnych interfejsów API w zestawie SDK.

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Przejdź do konta usługi Azure Cosmos, a następnie otwórz **Replikuj dane globalnie** menu.

2. W górnej części menu wybierz pozycję **Ręczne przejście do trybu failover**.

   ![Menu globalnego replikowania danych](./media/how-to-manage-database-account/replicate-data-globally.png)

3. W menu **Ręczne przejście do trybu failover** wybierz nowy region zapisu. Zaznacz pole wyboru, aby wskazać, że rozumiesz, iż ta opcja zmienia region zapisu.

4. Aby wyzwolić tryb failover, wybierz pozycję **OK**.

   ![Menu Ręczne przejście do trybu failover w portalu](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
# Assume account currently has regions with priority: eastus=0 westus=1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = 'myResourceGroup'
$accountName = 'myaccountname'

az cosmosdb update --name $accountName --resource-group $resourceGroupName --locations westus=0 eastus=1
```

### <a id="enable-manual-failover-via-ps"></a>Azure PowerShell

```azurepowershell-interactive
# Assume account currently has regions with priority: West US = 0, East US = 1
# Change the priority order to trigger a failover of the write region
$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName

$locations = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

$account.Properties.locations=$locations;
$CosmosDBProperties = $account.Properties;

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji i przykładów na temat zarządzania konta usługi Azure Cosmos, a także bazy danych i kontenerów przeczytaj następujące artykuły:

* [Zarządzanie przy użyciu programu Azure PowerShell usługi Azure Cosmos DB](manage-with-powershell.md)
* [Zarządzanie przy użyciu wiersza polecenia platformy Azure usługi Azure Cosmos DB](manage-with-cli.md)
