---
title: Konfigurowanie prywatnego linku platformy Azure dla konta usługi Azure Cosmos
description: Dowiedz się, jak skonfigurować link prywatny platformy Azure, aby uzyskać dostęp do konta usługi Azure Cosmos przy użyciu prywatnego adresu IP w sieci wirtualnej.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: thweiss
ms.openlocfilehash: 3f987b9e05bcdcda9afe26a1eb1354e5e2450ac5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846532"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account-preview"></a>Konfigurowanie prywatnego linku platformy Azure dla konta usługi Azure Cosmos (wersja zapoznawcza)

Za pomocą prywatnego linku platformy Azure można nawiązać połączenie z kontem usługi Azure Cosmos za pośrednictwem prywatnego punktu końcowego. Prywatny punkt końcowy to zestaw prywatnych adresów IP w podsieci w sieci wirtualnej. Za pomocą linku prywatnego można ograniczyć dostęp do danego konta usługi Azure Cosmos za pośrednictwem prywatnych adresów IP. W połączeniu z ograniczonymi zasadami sieciowej grupy zabezpieczeń link prywatny pomaga ograniczyć ryzyko związane z eksfiltracji danych. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz artykuł [prywatny link do platformy Azure](../private-link/private-link-overview.md) .

Ponadto prywatny Link umożliwia dostęp do konta usługi Azure Cosmos z poziomu sieci wirtualnej lub dowolnej równorzędnej sieci wirtualnej. Zasoby zamapowane na link prywatny są również dostępne w środowisku lokalnym przez prywatną komunikację równorzędną za pośrednictwem sieci VPN lub ExpressRoute. 

Można nawiązać połączenie z kontem usługi Azure Cosmos skonfigurowanym za pomocą linku prywatnego przy użyciu metod zatwierdzania "Automatyczne" lub "ręczne". Aby dowiedzieć się więcej, zapoznaj się z sekcją [przepływ pracy zatwierdzania](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) w dokumentacji łącza prywatnego. W tym artykule opisano kroki tworzenia prywatnego linku przy założeniu, że używana jest metoda automatycznego zatwierdzania.

## <a name="create-a-private-link-using-the-azure-portal"></a>Tworzenie prywatnego linku przy użyciu Azure Portal

Wykonaj następujące kroki, aby utworzyć prywatny link do istniejącego konta usługi Azure Cosmos przy użyciu Azure Portal:

1. W okienku **wszystkie zasoby** znajdź konto Azure Cosmos DB, które chcesz zabezpieczyć.

1. Wybierz opcję **połączenie z prywatnym punktem końcowym** z menu Ustawienia i wybierz opcję **prywatny punkt końcowy** :

   ![Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal](./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png)

1. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — podstawowe**, w okienku wprowadź lub wybierz następujące szczegóły:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz grupę zasobów.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź dowolną nazwę prywatnego punktu końcowego; Jeśli ta nazwa jest wykonywana, utwórz unikatową. |
    |Region| Wybierz region, w którym chcesz wdrożyć link prywatny. Prywatny punkt końcowy powinien zostać utworzony w tej samej lokalizacji, w której znajduje się Twoja sieć wirtualna.|
    |||
1. Wybierz pozycję **Dalej: zasób**.
1. W obszarze **Utwórz prywatny punkt końcowy zasobu**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu. <br/><br/> Ta opcja umożliwia wybranie jednego z zasobów w celu skonfigurowania prywatnego linku lub nawiązania połączenia z zasobem innego użytkownika z IDENTYFIKATORem zasobu lub aliasem, który został Ci udostępniony.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. AzureCosmosDB/databaseAccounts**. |
    | Zasób |Wybierz swoje konto usługi Azure Cosmos |
    |Docelowy zasób podrzędny |Wybierz typ interfejsu API Cosmos DB, który chcesz zmapować. Ta wartość domyślna to tylko jeden wybór dla interfejsów API SQL, MongoDB i Cassandra. W przypadku interfejsów API Gremlin i tabel można również wybrać *SQL* , ponieważ te interfejsy API współdziałają z interfejsem API SQL. |
    |||

1. Wybierz pozycję **Dalej: Konfiguracja**.
1. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — Konfiguracja**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieć**| |
    | Sieć wirtualna| Wybierz sieć wirtualną. |
    | Podsieć | Wybierz podsieć. |
    |**Integracja Prywatna strefa DNS**||
    |Integracja z prywatną strefą DNS |Wybierz pozycję **Tak**. <br><br/> Aby połączyć się prywatnie z prywatnym punktem końcowym, potrzebny jest rekord DNS. Zalecamy integrację prywatnego punktu końcowego z prywatną strefą DNS. Możesz również użyć własnych serwerów DNS lub utworzyć rekordy DNS przy użyciu plików hosta na maszynach wirtualnych. |
    |Strefa Prywatna strefa DNS |Wybierz *privatelink.Documents.Azure.com* <br><br/> Strefa Prywatna strefa DNS jest określana automatycznie i nie można jej zmienić obecnie przy użyciu Azure Portal.|
    |||

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.
1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

Po zaakceptowaniu prywatnych linków dla konta usługi Azure Cosmos w obszarze Azure Portal opcja **wszystkie sieci** w okienku **Zapora i sieci wirtualne** jest wyszarzona.

W poniższej tabeli przedstawiono mapowanie między różnymi typami interfejsów API konta usługi Azure Cosmos, obsługiwanymi zasobami podrzędnymi i odpowiednimi nazwami stref prywatnych. Konta Gremlin i interfejs API tabel są dostępne za pomocą interfejsu API SQL, a więc dla tych interfejsów API istnieją 2 wpisy:

|Typ interfejsu API konta usługi Azure Cosmos  |Obsługiwane zasoby podrzędne (lub groupIds) |Nazwa strefy prywatnej  |
|---------|---------|---------|
|Sql    |   Sql      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  Sql       |  privatelink.documents.azure.com    |
|Tabela    |    Tabela     |   privatelink.table.cosmos.azure.com    |
|Tabela     |   Sql      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>Pobierz prywatne adresy IP

Po aprowizacji prywatnego punktu końcowego możesz wysyłać zapytania dotyczące adresów IP. Aby wyświetlić adresy IP z Azure Portal, wybierz pozycję **wszystkie zasoby**, Wyszukaj prywatny punkt końcowy utworzony wcześniej w tym przypadku jest to "dbPrivateEndpoint3" i wybierz kartę Przegląd, aby wyświetlić ustawienia DNS i adresy IP:

![Prywatne adresy IP w Azure Portal](./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png)

Utworzono wiele adresów IP dla prywatnego punktu końcowego:

* Jeden dla globalnego punktu końcowego (region-niezależny od) konta usługi Azure Cosmos.
* Jeden dla każdego regionu, w którym wdrożono konto usługi Azure Cosmos.

## <a name="create-a-private-link-using-azure-powershell"></a>Tworzenie prywatnego linku przy użyciu Azure PowerShell

Uruchom następujący skrypt PowerSehll, aby utworzyć prywatny link o nazwie "MyPrivateEndpoint" dla istniejącego konta usługi Azure Cosmos. Pamiętaj, aby zastąpić wartości zmiennych szczegółowymi informacjami dotyczącymi danego środowiska.

```azurepowershell-interactive
Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Cosmos DB account: Sql or MongoDB or Cassandra or Gremlin or Table
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

### <a name="integrate-the-private-endpoint-with-private-dns-zone"></a>Integrowanie prywatnego punktu końcowego z prywatną strefą DNS

Po utworzeniu prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS, używając następującego skryptu PowerSehll:

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

### <a name="fetch-the-private-ip-addresses"></a>Pobierz prywatne adresy IP

Po aprowizacji prywatnego punktu końcowego można wysyłać zapytania dotyczące adresów IP i mapowania nazw FQDN przy użyciu następującego skryptu programu PowerShell:

```azurepowershell-interactive

$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-link-using-a-resource-manager-template"></a>Tworzenie prywatnego linku przy użyciu szablonu Menedżer zasobów

Połączenie prywatne można skonfigurować przez utworzenie prywatnego punktu końcowego w podsieci sieci wirtualnej. Jest to osiągane przy użyciu szablonu Azure Resource Manager. Utwórz szablon Menedżer zasobów o nazwie "PrivateEndpoint_template. JSON" przy użyciu następującego kodu. Ten szablon służy do tworzenia prywatnego punktu końcowego dla istniejącego konta interfejsu API usługi Azure Cosmos w istniejącej sieci wirtualnej:

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

### <a name="define-the-template-parameters-file"></a>Zdefiniuj plik parametrów szablonu

Utwórz plik parametrów dla szablonu i nadaj mu nazwę "PrivateEndpoint_parameters. JSON". Dodaj następujący kod do pliku parametrów:

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

### <a name="deploy-the-template-by-using-a-powershell-script"></a>Wdrażanie szablonu przy użyciu skryptu programu PowerShell

Następnie utwórz skrypt programu PowerShell z poniższym kodem. Przed uruchomieniem skryptu pamiętaj, aby zastąpić identyfikator subskrypcji, nazwę grupy zasobów i inne wartości zmiennych szczegółowymi informacjami dotyczącymi danego środowiska:

```azurepowershell-interactive
### This script creates a private endpoint for an existing Cosmos DB account in an existing VNet

## Step 1: Fill in these details, make sure to replace the variable values with the details specific to your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Cosmos DB and VNet resources live
$ResourceGroupName = "myResourceGroup"
# Name of the Cosmos DB account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Cosmos DB account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing VNet
$VNetName = "myVnet"
# Name of the target subnet in the VNet
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

W skrypcie programu PowerShell zmienna "GroupId" może zawierać tylko jedną wartość, która jest typem interfejsu API konta. Dozwolone wartości to: SQL, MongoDB, Cassandra, Gremlin i Table. Niektóre typy kont usługi Azure Cosmos są dostępne za pomocą wielu interfejsów API. Na przykład:

* Do konta interfejsu API Gremlin można uzyskać dostęp zarówno z kont usługi Gremlin, jak i interfejsu API SQL.
* Do konta interfejs API tabel można uzyskać dostęp z poziomu kont interfejsu API zarówno w tabeli, jak i SQL.

W przypadku takich kont należy utworzyć jeden prywatny punkt końcowy dla każdego typu interfejsu API z odpowiednim typem interfejsu API określonym w tablicy "GroupId".

Po pomyślnym wdrożeniu szablonu można zobaczyć dane wyjściowe podobne do przedstawionego na poniższej ilustracji. Wartość provisioningState to "powodzenie", jeśli prywatne punkty końcowe są poprawnie skonfigurowane.

![Dane wyjściowe wdrożenia szablonu Menedżer zasobów](./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png)

Po wdrożeniu szablonu prywatne adresy IP są zastrzeżone w podsieci. Reguła zapory dla konta usługi Azure Cosmos jest skonfigurowana do akceptowania połączeń tylko z prywatnego punktu końcowego.

## <a name="configure-custom-dns"></a>Konfigurowanie niestandardowego systemu DNS

Należy używać prywatnej usługi DNS w podsieci, w której został utworzony prywatny punkt końcowy. I skonfiguruj punkty końcowe tak, aby każdy prywatny adres IP był mapowany na wpis DNS (Sprawdź Właściwość "nazwy FQDN" w odpowiedzi pokazanej powyżej).

Podczas tworzenia prywatnego punktu końcowego można zintegrować go z prywatną strefą DNS na platformie Azure. Jeśli zdecydujesz się nie zintegrować prywatnego punktu końcowego z prywatną strefą DNS na platformie Azure i zamiast tego użyć niestandardowego serwera DNS, musisz skonfigurować system DNS do dodawania rekordów DNS dla wszystkich prywatnych adresów IP zarezerwowanych dla prywatnego punktu końcowego.

## <a name="firewall-configuration-with-private-link"></a>Konfiguracja zapory za pomocą linku prywatnego

W przypadku korzystania z prywatnego linku w połączeniu z regułami zapory są następujące różne sytuacje i wyniki:

* Jeśli nie ma skonfigurowanych reguł zapory, domyślnie konto usługi Azure Cosmos jest dostępne dla całego ruchu.

* Jeśli jest skonfigurowany publiczny ruch lub punkt końcowy usługi i tworzone są prywatne punkty końcowe, wówczas różne typy ruchu przychodzącego są autoryzowane przez odpowiedni typ reguły zapory.

* Jeśli nie jest skonfigurowany publiczny ruch lub punkt końcowy usługi i tworzone są prywatne punkty końcowe, konto usługi Azure Cosmos jest dostępne tylko za pomocą prywatnych punktów końcowych. Jeśli nie jest skonfigurowany publiczny ruch lub punkt końcowy usługi, po odrzuceniu lub usunięciu wszystkich zatwierdzonych prywatnych punktów końcowych konto jest otwarte dla całej sieci.

## <a name="update-private-endpoint-when-you-add-or-remove-a-region"></a>Aktualizowanie prywatnego punktu końcowego po dodaniu lub usunięciu regionu

Dodawanie lub usuwanie regionów na koncie usługi Azure Cosmos wymaga dodania lub usunięcia wpisów DNS dla tego konta. Te zmiany należy odpowiednio zaktualizować w prywatnym punkcie końcowym. Obecnie należy ręcznie wprowadzić tę zmianę, wykonując następujące czynności:

1. Administrator Azure Cosmos DB dodaje lub usuwa regiony. Następnie Administratorzy sieci są powiadamiani o oczekujących zmianach. Prywatny punkt końcowy zmapowany na konto usługi Azure Cosmos widzi jego właściwości "ActionsRequired", które zostały zmienione z "none" na "Recreate". Następnie administrator sieci aktualizuje prywatny punkt końcowy, wydając żądanie PUT przy użyciu tego samego ładunku Menedżer zasobów, który został użyty do jego utworzenia.

1. Po wykonaniu tej operacji należy również zaktualizować prywatną usługę DNS podsieci w celu odzwierciedlenia dodanych lub usuniętych wpisów DNS oraz ich prywatnych adresów IP.

Na przykład w przypadku wdrożenia konta usługi Azure Cosmos w 3 regionach: "zachodnie stany USA", "środkowe stany US" i "Europa Zachodnia". W przypadku tworzenia prywatnego punktu końcowego dla konta w podsieci są zastrzeżone 4 prywatne adresy IP. Jeden dla każdego regionu, który liczy się do łącznej liczby 3 i jeden dla punktu końcowego Global/region-niezależny od.

Później, jeśli dodasz nowy region, na przykład "Wschodnie stany USA" do konta usługi Azure Cosmos. Domyślnie nowy region nie jest dostępny z istniejącego prywatnego punktu końcowego. Administrator konta usługi Azure Cosmos powinien odświeżyć połączenie prywatnego punktu końcowego przed uzyskaniem dostępu do niego z nowego regionu. 

Po uruchomieniu polecenia ` Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>` dane wyjściowe polecenia zawierają parametr `actionsRequired`, który jest ustawiony na "Recreate". Ta wartość wskazuje, że prywatny punkt końcowy powinien być odświeżony. Następnie administrator konta usługi Azure Cosmos uruchamia polecenie `Set-AzPrivateEndpoint`, aby wyzwolić odświeżanie prywatnego punktu końcowego.

```powershell
$pe = Get-AzPrivateEndpoint -Name <your private endpoint name> -ResourceGroupName <your resource group name>

Set-AzPrivateEndpoint -PrivateEndpoint $pe
```

Nowy prywatny adres IP jest automatycznie zarezerwowany w podsieci w ramach tego prywatnego punktu końcowego, a wartość `actionsRequired` zostanie `None`. Jeśli nie masz żadnej integracji prywatnej strefy DNZ (innymi słowy, jeśli używasz niestandardowego prywatnego systemu DNS), musisz skonfigurować prywatną usługę DNS, aby dodać nowy rekord DNS dla prywatnego adresu IP odpowiadającego nowemu regionowi.

Po usunięciu regionu można użyć tych samych kroków. Prywatny adres IP usuniętego regionu jest automatycznie odzyskiwany, a flaga `actionsRequired` zostanie `None`. Jeśli nie masz żadnej integracji prywatnej strefy DNZ, musisz skonfigurować prywatną usługę DNS, aby usunąć rekord DNS dla usuniętego regionu.

Rekordy DNS w prywatnej strefie DNS nie są usuwane automatycznie po usunięciu prywatnego punktu końcowego lub usunięciu regionu z konta usługi Azure Cosmos. Należy ręcznie usunąć rekordy DNS.

## <a name="current-limitations"></a>Bieżące ograniczenia

W przypadku korzystania z prywatnego linku do konta usługi Azure Cosmos stosowane są następujące ograniczenia:

* Obsługa linków prywatnych dla kont usługi Azure Cosmos i sieci wirtualnych jest dostępna tylko w określonych regionach. Aby zapoznać się z listą obsługiwanych regionów, zobacz sekcję [dostępne regiony](../private-link/private-link-overview.md#availability) w artykule dotyczącym łącza prywatnego. **Aby można było utworzyć prywatny punkt końcowy, zarówno konto sieci wirtualnej, jak i usługa Azure Cosmos powinny znajdować się w obsługiwanych regionach**.

* W przypadku używania linków prywatnych z kontem usługi Azure Cosmos przy użyciu połączenia trybu bezpośredniego można korzystać tylko z protokołu TCP. Protokół HTTP nie jest jeszcze obsługiwany

* W przypadku korzystania z interfejsu API Azure Cosmos DB dla kont MongoDB prywatny punkt końcowy jest obsługiwany tylko dla kont na serwerze w wersji 3,6 (czyli kont korzystających z punktu końcowego w formacie `*.mongo.cosmos.azure.com`). Link prywatny nie jest obsługiwany w przypadku kont na serwerze w wersji 3,2 (czyli kontach korzystających z punktu końcowego w formacie `*.documents.azure.com`). Aby użyć linku prywatnego, należy przeprowadzić migrację starych kont do nowej wersji.

* W przypadku korzystania z interfejsu API Azure Cosmos DB dla kont MongoDB z linkiem prywatnym nie można używać narzędzi takich jak Robo 3T, Studio 3T, Mongoose itd. Punkt końcowy może mieć obsługę linku prywatnego tylko wtedy, gdy jest określony parametr nazwa_aplikacji =<account name>. Na przykład: replicaSet = globaldb & nazwa_aplikacji = mydbaccountname. Ponieważ te narzędzia nie przekazują nazwy aplikacji w parametrach połączenia do usługi, dlatego nie można używać linku prywatnego. Można jednak nadal uzyskiwać dostęp do tych kont ze sterownikami zestawu SDK w wersji 3,6.

* Nie można przenieść ani usunąć sieci wirtualnej, jeśli zawiera ona link prywatny.

* Nie można usunąć konta usługi Azure Cosmos, jeśli jest ono dołączone do prywatnego punktu końcowego.

* Nie można przełączyć konta usługi Azure Cosmos do trybu failover w regionie, który nie jest zamapowany na wszystkie prywatne punkty końcowe dołączone do tego konta. Aby uzyskać więcej informacji, zobacz Dodawanie lub usuwanie regionów w poprzedniej sekcji.

* Administratorowi sieci należy udzielić co najmniej uprawnienia "*/PrivateEndpointConnectionsApproval" w zakresie konta usługi Azure Cosmos przez administratora w celu utworzenia automatycznie zatwierdzonych prywatnych punktów końcowych.

### <a name="private-dns-zone-integration-limitations"></a>Ograniczenia integracji stref Prywatna strefa DNS

Rekordy DNS w prywatnej strefie DNS nie są usuwane automatycznie po usunięciu prywatnego punktu końcowego lub usunięciu regionu z konta usługi Azure Cosmos. Rekordy DNS należy usunąć ręcznie przed:

* Dodawanie nowego prywatnego punktu końcowego połączonego z tą prywatną strefą DNS.
* Dodawanie nowego regionu do dowolnego konta bazy danych z prywatnymi punktami końcowymi połączonymi z tą prywatną strefą DNS.

Bez czyszczenia rekordów DNS nieoczekiwane problemy z płaszczyzną danych, takie jak awaria danych w regionach dodawanych po usunięciu prywatnego punktu końcowego lub usunięciu regionu mogą się zdarzyć

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innych funkcji zabezpieczeń Azure Cosmos DB, zobacz następujący artykuł:

* Aby skonfigurować zaporę dla Azure Cosmos DB, zobacz [Obsługa zapory](firewall-support.md).

* [Jak skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos.](how-to-configure-vnet-service-endpoint.md)

* Aby dowiedzieć się więcej na temat linku prywatnego, zapoznaj się z dokumentacją [prywatnego linku do platformy Azure](../private-link/private-link-overview.md) .
