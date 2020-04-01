---
title: Konfigurowanie łącza prywatnego platformy Azure dla konta usługi Azure Cosmos
description: Dowiedz się, jak skonfigurować łącze prywatne platformy Azure w celu uzyskania dostępu do konta usługi Azure Cosmos przy użyciu prywatnego adresu IP w sieci wirtualnej.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 9a6a1560e169c51256c198868dc7293a020189f4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421434"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Konfigurowanie łącza prywatnego platformy Azure dla konta usługi Azure Cosmos

Za pomocą usługi Azure Private Link, można połączyć się z kontem usługi Azure Cosmos za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w podsieci w sieci wirtualnej. Następnie można ograniczyć dostęp do konta usługi Azure Cosmos za pomocą prywatnych adresów IP. Gdy private link jest połączony z ograniczonymi zasadami sieciowej grupy sieciowej, pomaga zmniejszyć ryzyko eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [Azure Private Link.](../private-link/private-link-overview.md)

Łącze prywatne umożliwia użytkownikom dostęp do konta usługi Azure Cosmos z poziomu sieci wirtualnej lub dowolnej sieci wirtualnej równorzędnej. Zasoby mapowane do łącza prywatnego są również dostępne lokalnie za pośrednictwem prywatnej komunikacji równorzędnej za pośrednictwem sieci VPN lub usługi Azure ExpressRoute. 

Można połączyć się z kontem usługi Azure Cosmos skonfigurowanym przy użyciu łącza prywatnego przy użyciu metody automatycznego lub ręcznego zatwierdzania. Aby dowiedzieć się więcej, zobacz sekcję [Przepływ pracy zatwierdzanie](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) w dokumentacji łącza prywatnego. 

W tym artykule opisano kroki tworzenia prywatnego punktu końcowego. Przyjęto założenie, że używasz metody automatycznego zatwierdzania.

> [!NOTE]
> Obsługa prywatnych punktów końcowych jest obecnie ogólnie dostępna tylko w trybie połączenia bramy. W trybie bezpośrednim jest on dostępny jako funkcja podglądu.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Tworzenie prywatnego punktu końcowego przy użyciu portalu Azure

Aby utworzyć prywatny punkt końcowy dla istniejącego konta usługi Azure Cosmos przy użyciu portalu Azure portal, należy wykonać następujące kroki:

1. W okienku **Wszystkie zasoby** wybierz konto usługi Azure Cosmos.

1. Wybierz **pozycję Prywatne połączenia punktów końcowych** z listy ustawień, a następnie wybierz pozycję Prywatny punkt **końcowy:**

   ![Wybory dotyczące tworzenia prywatnego punktu końcowego w witrynie Azure portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. W okienku **Tworzenie prywatnego punktu końcowego — podstawy** wprowadź lub wybierz następujące szczegóły:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz grupę zasobów.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź dowolną nazwę prywatnego punktu końcowego. Jeśli ta nazwa zostanie odebrana, utwórz unikatową. |
    |Region| Wybierz region, w którym chcesz wdrożyć łącze prywatne. Utwórz prywatny punkt końcowy w tej samej lokalizacji, w której istnieje sieć wirtualna.|
    |||
1. Wybierz **dalej: Zasób**.
1. W **obszarze Tworzenie prywatnego punktu końcowego — zasób**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz **pozycję Połącz z zasobem platformy Azure w moim katalogu**. <br/><br/> Następnie można wybrać jeden z zasobów, aby skonfigurować łącze prywatne. Możesz też połączyć się z zasobem innej osoby przy użyciu identyfikatora zasobu lub aliasu, który ci udostępniono.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz **pozycję Microsoft.AzureCosmosDB/databaseAccounts**. |
    | Zasób |Wybierz swoje konto usługi Azure Cosmos. |
    |Docelowy podsób |Wybierz typ interfejsu API usługi Azure Cosmos DB, który chcesz zamapować. Domyślnie jest to tylko jeden wybór dla interfejsów API SQL, MongoDB i Cassandra. W przypadku interfejsów API gremlin i tabel można również **wybrać** sql, ponieważ te interfejsy API są interoperacyjne z interfejsem API SQL. |
    |||

1. Wybierz **dalej: Konfiguracja**.
1. W **obszarze Tworzenie prywatnego punktu końcowego — konfiguracja**, wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Networking**| |
    | Sieć wirtualna| Wybierz sieć wirtualną. |
    | Podsieć | Wybierz podsieć. |
    |**Prywatna integracja z systemem DNS**||
    |Integracja z prywatną strefą DNS |Wybierz **pozycję Tak**. <br><br/> Aby połączyć się prywatnie z prywatnym punktem końcowym, potrzebny jest rekord DNS. Zaleca się zintegrowanie prywatnego punktu końcowego z prywatną strefą DNS. Można również użyć własnych serwerów DNS lub utworzyć rekordy DNS przy użyciu plików hosta na maszynach wirtualnych. |
    |Prywatna strefa DNS |Wybierz **privatelink.documents.azure.com**. <br><br/> Prywatna strefa DNS jest określana automatycznie. Nie można go zmienić przy użyciu witryny Azure portal.|
    |||

1. Wybierz pozycję **Przegląd + utwórz**. Na **review + create** strony Azure sprawdza poprawność konfiguracji.
1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

Po zatwierdzeniu łącza prywatnego dla konta usługi Azure Cosmos w witrynie Azure portal opcja **Wszystkie sieci** w **okienku Zapora i sieci wirtualne** jest niedostępna.

W poniższej tabeli przedstawiono mapowanie między różnymi typami interfejsu API konta usługi Azure Cosmos, obsługiwanymi zasobami podrzędnymi i odpowiadającymi im nazwami stref prywatnych. Można również uzyskać dostęp do kont interfejsu API Gremlin i tabeli za pośrednictwem interfejsu API SQL, więc istnieją dwa wpisy dla tych interfejsów API.

|Typ interfejsu API konta usługi Azure Cosmos  |Obsługiwane zasoby podrzędne (lub identyfikatory grup) |Nazwa strefy prywatnej  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Pobieranie prywatnych adresów IP

Po zainicjowaniu obsługi administracyjnej prywatnego punktu końcowego można wysyłać zapytania do adresów IP. Aby wyświetlić adresy IP z witryny Azure portal:

1. Wybierz pozycję **Wszystkie zasoby**.
1. Wyszukaj prywatny punkt końcowy utworzony wcześniej. W tym przypadku jest to **cdbPrivateEndpoint3**.
1. Wybierz kartę **Przegląd,** aby wyświetlić ustawienia DNS i adresy IP.

![Prywatne adresy IP w witrynie Azure portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Na prywatny punkt końcowy tworzy się wiele adresów IP:

* Jeden dla globalnego punktu końcowego (regionu niezależnego) konta usługi Azure Cosmos
* Po jednym dla każdego regionu, w którym jest wdrażane konto usługi Azure Cosmos

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Tworzenie prywatnego punktu końcowego przy użyciu programu Azure PowerShell

Uruchom następujący skrypt programu PowerShell, aby utworzyć prywatny punkt końcowy o nazwie "MyPrivateEndpoint" dla istniejącego konta usługi Azure Cosmos. Zastąp wartości zmiennych szczegółami dla swojego środowiska.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integracja prywatnego punktu końcowego z prywatną strefą DNS

Po utworzeniu prywatnego punktu końcowego można go zintegrować z prywatną strefą DNS przy użyciu następującego skryptu programu PowerShell:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>Pobieranie prywatnych adresów IP

Po zainicjowaniu obsługi administracyjnej prywatnego punktu końcowego można wysyłać zapytania do adresów IP i mapowania FQDN przy użyciu następującego skryptu programu PowerShell:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Tworzenie prywatnego punktu końcowego przy użyciu interfejsu wiersza polecenia platformy Azure

Uruchom następujący skrypt interfejsu wiersza polecenia platformy Azure, aby utworzyć prywatny punkt końcowy o nazwie "myPrivateEndpoint" dla istniejącego konta usługi Azure Cosmos. Zastąp wartości zmiennych szczegółami dla swojego środowiska.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integracja prywatnego punktu końcowego z prywatną strefą DNS

Po utworzeniu prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS przy użyciu następującego skryptu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Tworzenie prywatnego punktu końcowego przy użyciu szablonu Menedżera zasobów

Łącze prywatne można skonfigurować, tworząc prywatny punkt końcowy w podsieci sieci wirtualnej. Można to osiągnąć przy użyciu szablonu usługi Azure Resource Manager.

Użyj następującego kodu, aby utworzyć szablon Menedżera zasobów o nazwie "PrivateEndpoint_template.json". Ten szablon tworzy prywatny punkt końcowy dla istniejącego konta interfejsu API SQL usługi Azure Cosmos w istniejącej sieci wirtualnej.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Definiowanie pliku parametrów dla szablonu**

Utwórz plik parametrów dla szablonu i nazwij go "PrivateEndpoint_parameters.json". Dodaj następujący kod do pliku parametrów:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**Wdrażanie szablonu przy użyciu skryptu programu PowerShell**

Utwórz skrypt programu PowerShell przy użyciu następującego kodu. Przed uruchomieniem skryptu należy zastąpić identyfikator subskrypcji, nazwę grupy zasobów i inne wartości zmiennych szczegółami dla środowiska.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

W skrypcie programu `GroupId` PowerShell zmienna może zawierać tylko jedną wartość. Ta wartość jest typem interfejsu API konta. Dozwolone `Sql`wartości to: `MongoDB` `Cassandra`, `Gremlin`, `Table`, i . Niektóre typy kont usługi Azure Cosmos są dostępne za pośrednictwem wielu interfejsów API. Przykład:

* Konto interfejsu API Gremlin można uzyskać dostęp zarówno z kont api Gremlin, jak i SQL.
* Dostęp do konta interfejsu API tabeli można uzyskać zarówno z kont interfejsu API tabeli, jak i z nich.

Dla tych kont należy utworzyć jeden prywatny punkt końcowy dla każdego typu interfejsu API. Odpowiedni typ interfejsu API `GroupId` jest określony w tablicy.

Po pomyślnym wdrożeniu szablonu można zobaczyć dane wyjściowe podobne do tego, co pokazuje poniższy obraz. Wartość `provisioningState` jest, `Succeeded` jeśli prywatne punkty końcowe są poprawnie skonfigurowane.

![Dane wyjściowe wdrożenia dla szablonu Menedżera zasobów](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Po wdrożeniu szablonu prywatne adresy IP są zarezerwowane w podsieci. Reguła zapory konta usługi Azure Cosmos jest skonfigurowana do akceptowania połączeń tylko z prywatnego punktu końcowego.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Integracja prywatnego punktu końcowego z prywatną strefą DNS

Użyj następującego kodu, aby utworzyć szablon Menedżera zasobów o nazwie "PrivateZone_template.json". Ten szablon tworzy prywatną strefę DNS dla istniejącego konta interfejsu API SQL usługi Azure Cosmos w istniejącej sieci wirtualnej.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Użyj następującego kodu, aby utworzyć szablon Menedżera zasobów o nazwie "PrivateZoneRecords_template.json".

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Definiowanie pliku parametrów dla szablonu**

Utwórz następujący plik dwóch parametrów dla szablonu. Utwórz "PrivateZone_parameters.json". z następującym kodem:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Utwórz "PrivateZoneRecords_parameters.json". z następującym kodem:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**Wdrażanie szablonu przy użyciu skryptu programu PowerShell**

Utwórz skrypt programu PowerShell przy użyciu następującego kodu. Przed uruchomieniem skryptu należy zastąpić identyfikator subskrypcji, nazwę grupy zasobów i inne wartości zmiennych szczegółami dla środowiska.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Konfigurowanie niestandardowego systemu DNS

Należy użyć prywatnej strefy DNS w podsieci, w której utworzono prywatny punkt końcowy. Skonfiguruj punkty końcowe tak, aby każdy prywatny adres IP był mapowany na wpis DNS. (Zobacz `fqdns` właściwość w odpowiedzi pokazanej wcześniej).

Podczas tworzenia prywatnego punktu końcowego, można zintegrować go z prywatnej strefy DNS na platformie Azure. Jeśli zamiast tego użyjesz niestandardowej strefy DNS, musisz skonfigurować ją tak, aby dodawać rekordy DNS dla wszystkich prywatnych adresów IP zarezerwowanych dla prywatnego punktu końcowego.

## <a name="private-link-combined-with-firewall-rules"></a>Łącze prywatne w połączeniu z regułami zapory

Następujące sytuacje i wyniki są możliwe podczas korzystania z łącza prywatnego w połączeniu z regułami zapory:

* Jeśli nie skonfigurujesz żadnych reguł zapory, domyślnie cały ruch może uzyskać dostęp do konta usługi Azure Cosmos.

* Jeśli skonfigurujesz ruch publiczny lub punkt końcowy usługi i utworzysz prywatne punkty końcowe, różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory.

* Jeśli nie skonfigurujesz żadnego ruchu publicznego lub punktu końcowego usługi i utworzysz prywatne punkty końcowe, konto usługi Azure Cosmos będzie dostępne tylko za pośrednictwem prywatnych punktów końcowych. Jeśli nie skonfigurujesz ruchu publicznego ani punktu końcowego usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych konto jest otwarte dla całej sieci.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizowanie prywatnego punktu końcowego podczas dodawania lub usuwania regionu

Dodawanie lub usuwanie regionów do konta usługi Azure Cosmos wymaga dodania lub usunięcia wpisów DNS dla tego konta. Po dodaniu lub usunięciu regionów można zaktualizować prywatną strefę DNS podsieci, aby odzwierciedlić dodane lub usunięte wpisy DNS i odpowiadające im prywatne adresy IP.

Załóżmy na przykład, że wdrożysz konto usługi Azure Cosmos w trzech regionach: "Zachodnie stany USA", "Środkowe stany USA" i "Europa Zachodnia". Podczas tworzenia prywatnego punktu końcowego dla konta, cztery prywatne adresy IP są zarezerwowane w podsieci. Istnieje jeden adres IP dla każdego z trzech regionów i jest jeden adres IP dla globalnego/regionu niezależnego punktu końcowego.

Później można dodać nowy region (na przykład "Wschodnie stany USA") do konta usługi Azure Cosmos. Po dodaniu nowego regionu należy dodać odpowiedni rekord DNS do prywatnej strefy DNS lub niestandardowego systemu DNS.

Te same kroki można wykonać po usunięciu regionu. Po usunięciu regionu należy usunąć odpowiedni rekord DNS z prywatnej strefy DNS lub niestandardowego systemu DNS.

## <a name="current-limitations"></a>Bieżące ograniczenia

Następujące ograniczenia mają zastosowanie podczas korzystania z funkcji Łącze prywatne z kontem usługi Azure Cosmos:

* Podczas korzystania z łącza prywatnego z kontem usługi Azure Cosmos przy użyciu połączenia w trybie bezpośrednim można używać tylko protokołu TCP. Protokół HTTP nie jest jeszcze obsługiwany.

* Obsługa prywatnych punktów końcowych jest obecnie ogólnie dostępna tylko w trybie połączenia bramy. W trybie bezpośrednim jest on dostępny jako funkcja podglądu.

* Podczas korzystania z interfejsu API usługi Azure Cosmos DB dla kont MongoDB prywatny punkt końcowy jest obsługiwany tylko dla kont na serwerze `*.mongo.cosmos.azure.com`w wersji 3.6 (czyli kont przy użyciu punktu końcowego w formacie ). Private Link nie jest obsługiwany dla kont na serwerze w wersji 3.2 `*.documents.azure.com`(czyli kontach używających punktu końcowego w formacie). Aby użyć łącza prywatnego, należy przeprowadzić migrację starych kont do nowej wersji.

* Podczas korzystania z interfejsu API usługi Azure Cosmos DB dla kont MongoDB, które mają łącze prywatne, nie można używać narzędzi, takich jak Robo 3T, Studio 3T i Mongoose. Punkt końcowy może mieć obsługę łącza `appName=<account name>` prywatnego tylko wtedy, gdy parametr jest określony. Może to być na przykład `replicaSet=globaldb&appName=mydbaccountname`. Ponieważ te narzędzia nie przekazują nazwy aplikacji w ciągu połączenia do usługi, nie można użyć łącza prywatnego. Ale nadal można uzyskać dostęp do tych kont za pomocą sterowników SDK w wersji 3.6.

* Nie można przenieść ani usunąć sieci wirtualnej, jeśli zawiera ona łącze prywatne.

* Nie można usunąć konta usługi Azure Cosmos, jeśli jest ono dołączone do prywatnego punktu końcowego.

* Nie można po awarii konta usługi Azure Cosmos do regionu, który nie jest mapowany do wszystkich prywatnych punktów końcowych dołączonych do konta.

* Administrator sieci powinien otrzymać co najmniej uprawnienie "*/PrivateEndpointConnectionsApproval" w zakresie konta usługi Azure Cosmos do tworzenia automatycznie zatwierdzonych prywatnych punktów końcowych.

### <a name="limitations-to-private-dns-zone-integration"></a>Ograniczenia integracji prywatnej strefy DNS

Rekordy DNS w prywatnej strefie DNS nie są usuwane automatycznie po usunięciu prywatnego punktu końcowego lub usunięciu regionu z konta usługi Azure Cosmos. Rekordy DNS należy usunąć ręcznie przed:

* Dodawanie nowego prywatnego punktu końcowego połączonego z tą prywatną strefą DNS.
* Dodawanie nowego regionu do dowolnego konta bazy danych, które ma prywatne punkty końcowe połączone z tą prywatną strefą DNS.

Jeśli nie wyczyścisz rekordów DNS, mogą wystąpić nieoczekiwane problemy z płaszczyzną danych. Te problemy obejmują awarię danych do regionów dodanych po usunięciu prywatnego punktu końcowego lub usunięciu regionu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o funkcjach zabezpieczeń usługi Azure Cosmos DB, zobacz następujące artykuły:

* Aby skonfigurować zaporę dla usługi Azure Cosmos DB, zobacz [Obsługa zapory](firewall-support.md).

* Aby dowiedzieć się, jak skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos, zobacz [Konfigurowanie dostępu z sieci wirtualnych](how-to-configure-vnet-service-endpoint.md).

* Aby dowiedzieć się więcej o łączu prywatnym, zobacz dokumentację [łącza prywatnego platformy Azure.](../private-link/private-link-overview.md)
