---
title: Konfigurowanie zapory IP dla konta usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować zasady kontroli dostępu do adresów IP dla obsługi zapory na kontach usługi Azure Cosmos.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 1c24782285ac9b06d5499351eebe1693ade07297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78162948"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurowanie zapory IP w usłudze Azure Cosmos DB

Dane przechowywane na koncie usługi Azure Cosmos DB można zabezpieczyć przy użyciu zapór IP. Usługa Azure Cosmos DB obsługuje formanty dostępu oparte na protokãołowych adresach IP dla obsługi zapory przychodzącej. Zaporę IP można ustawić na koncie usługi Azure Cosmos DB przy użyciu jednego z następujących sposobów:

* Z witryny Azure Portal
* Deklaratywnie za pomocą szablonu usługi Azure Resource Manager
* Programowo za pośrednictwem interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell przez zaktualizowanie właściwości **ipRangeFilter**

## <a name="configure-an-ip-firewall-by-using-the-azure-portal"></a><a id="configure-ip-policy"></a>Konfigurowanie zapory IP przy użyciu portalu Azure

Aby ustawić zasady kontroli dostępu do adresu IP w witrynie Azure portal, przejdź do strony konta usługi Azure Cosmos DB i wybierz **zapora i sieci wirtualne** w menu nawigacyjnym. Zmień **wartość Zezwalaj na dostęp z** wartości na **Wybrane sieci,** a następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu przedstawiający sposób otwierania strony Zapory w witrynie Azure portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Gdy kontrola dostępu do adresu IP jest włączona, portal Azure zapewnia możliwość określania adresów IP, zakresów adresów IP i przełączników. Przełączniki umożliwiają dostęp do innych usług platformy Azure i witryny Azure portal. W poniższych sekcjach podano szczegółowe informacje na temat tych przełączników.

> [!NOTE]
> Po włączeniu zasad kontroli dostępu do adresu IP dla konta usługi Azure Cosmos DB wszystkie żądania do konta usługi Azure Cosmos DB z komputerów spoza listy dozwolonych zakresów adresów IP są odrzucane. Przeglądanie zasobów usługi Azure Cosmos DB z portalu jest również zablokowany, aby zapewnić integralność kontroli dostępu.

### <a name="allow-requests-from-the-azure-portal"></a>Zezwalaj na żądania z witryny Azure portal

Po włączeniu zasady kontroli dostępu IP programowo, należy dodać adres IP dla portalu Azure do **właściwości ipRangeFilter,** aby zachować dostęp. Adresy IP portalu to:

|Region|Adres IP|
|------|----------|
|Niemcy|51.4.229.218|
|Chiny|139.217.8.252|
|US Gov|52.244.48.71|
|Wszystkie pozostałe regiony|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Możesz włączyć żądania dostępu do witryny Azure portal, wybierając opcję **Zezwalaj na dostęp z witryny Azure portal,** jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający sposób włączania dostępu do witryny Azure portal](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Zezwalaj na żądania z globalnych centrów danych platformy Azure lub innych źródeł na platformie Azure

Jeśli uzyskujesz dostęp do konta usługi Azure Cosmos DB z usług, które nie zapewniają statycznego adresu IP (na przykład usługi Azure Stream Analytics i usługi Azure Functions), nadal możesz ograniczyć dostęp do zapory IP. Dostęp z innych źródeł na platformie Azure można włączyć, wybierając opcję **Akceptuj połączenia z poziomu centrów danych platformy Azure,** jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający sposób otwierania strony Zapory w witrynie Azure portal](./media/how-to-configure-firewall/enable-azure-services.png)

Po włączeniu tej opcji `0.0.0.0` adres IP jest dodawany do listy dozwolonych adresów IP. Adres `0.0.0.0` IP ogranicza żądania do konta usługi Azure Cosmos DB z zakresu adresów IP centrum danych platformy Azure. To ustawienie nie zezwala na dostęp do konta usługi Azure Cosmos DB z jakiegokolwiek innego adresu IP.

> [!NOTE]
> Ta opcja konfiguruje zaporę, aby zezwalać na wszystkie żądania z platformy Azure, w tym żądania z subskrypcji innych klientów wdrożonych na platformie Azure. Lista serwerów IP dozwolonych przez tę opcję jest szeroka, więc ogranicza skuteczność zasad zapory. Tej opcji należy używać tylko wtedy, gdy żądania nie pochodzą ze statycznych adresów IP lub podsieci w sieciach wirtualnych. Wybranie tej opcji automatycznie umożliwia dostęp z witryny Azure portal, ponieważ portal Azure jest wdrażany na platformie Azure.

### <a name="requests-from-your-current-ip"></a>Żądania z bieżącego adresu IP

Aby uprościć programować, witryna Azure portal ułatwia identyfikowanie i dodawanie adresu IP komputera klienckiego do listy dozwolonych. Aplikacje z systemem komputera mogą następnie uzyskać dostęp do konta usługi Azure Cosmos DB.

Portal automatycznie wykrywa adres IP klienta. Może to być adres IP klienta komputera lub adres IP bramy sieciowej. Upewnij się, aby usunąć ten adres IP przed podjęciem obciążeń do produkcji.

Aby dodać bieżący adres IP do listy adresów IP, wybierz pozycję **Dodaj mój bieżący adres IP**. Następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu przedstawiający sposób konfigurowania ustawień zapory dla bieżącego adresu IP](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Żądania od usług w chmurze

Na platformie Azure usługi w chmurze są typowym sposobem hostingu logiki usługi warstwy środkowej przy użyciu usługi Azure Cosmos DB. Aby włączyć dostęp do konta usługi Azure Cosmos DB z usługi w chmurze, należy dodać publiczny adres IP usługi w chmurze do dozwolonej listy adresów IP skojarzonych z kontem usługi Azure Cosmos DB, [konfigurując zasady kontroli dostępu ip](#configure-ip-policy). Dzięki temu wszystkie wystąpienia ról usług w chmurze mają dostęp do konta usługi Azure Cosmos DB.

Adresy IP usług w chmurze można pobrać w witrynie Azure Portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP usługi w chmurze wyświetlany w witrynie Azure portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Po skalowaniu w poziomie usługi w chmurze przez dodanie wystąpień roli, te nowe wystąpienia będą automatycznie miały dostęp do konta usługi Azure Cosmos DB, ponieważ są częścią tej samej usługi w chmurze.

### <a name="requests-from-virtual-machines"></a>Żądania od maszyn wirtualnych

Można również użyć [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/) lub [zestawów skalowania maszyny wirtualnej](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) do obsługi usług warstwy środkowej przy użyciu usługi Azure Cosmos DB. Aby skonfigurować konto usługi Cosmos DB w taki sposób, aby umożliwiało dostęp z maszyn wirtualnych, należy skonfigurować publiczny adres IP zestawu skalowania maszyny wirtualnej i/lub maszyny wirtualnej jako jeden z dozwolonych adresów IP dla konta usługi Azure Cosmos DB, [konfigurując zasady kontroli dostępu do adresów IP](#configure-ip-policy).

Adresy IP maszyn wirtualnych można pobrać w portalu Azure, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP maszyny wirtualnej wyświetlany w witrynie Azure portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Po dodaniu wystąpień maszyny wirtualnej do grupy automatycznie otrzymują dostęp do konta usługi Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Prośby z Internetu

Podczas uzyskiwania dostępu do konta usługi Azure Cosmos DB z komputera w Internecie, adres IP klienta lub zakres adresów IP komputera musi zostać dodany do dozwolonej listy adresów IP dla konta.

## <a name="configure-an-ip-firewall-by-using-a-resource-manager-template"></a><a id="configure-ip-firewall-arm"></a>Konfigurowanie zapory IP przy użyciu szablonu Menedżera zasobów

Aby skonfigurować kontrolę dostępu do konta usługi Azure Cosmos DB, upewnij się, że szablon Menedżera zasobów określa atrybut **ipRangeFilter** z listą dozwolonych zakresów adresów IP. Jeśli konfiguruje zaporę IP na już `locations` wdrożone konto usługi Cosmos, upewnij się, że tablica jest zgodna z aktualnie wdrożonym. Nie można jednocześnie `locations` modyfikować tablicy i innych właściwości. Aby uzyskać więcej informacji i przykładów szablonów usługi Azure Resource Manager dla usługi Azure Cosmos DB, zobacz [szablony usługi Azure Resource Manager dla usługi Azure Cosmos DB](resource-manager-samples.md)

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "enableAutomaticFailover": "[parameters('automaticFailover')]",
    "ipRangeFilter":"40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
  }
}
```

## <a name="configure-an-ip-access-control-policy-by-using-the-azure-cli"></a><a id="configure-ip-firewall-cli"></a>Konfigurowanie zasad kontroli dostępu do adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure

Następujące polecenie pokazuje, jak utworzyć konto usługi Azure Cosmos DB z kontrolą dostępu do adresu IP:

```azurecli-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
ipRangeFilter='192.168.221.17,183.240.196.255,40.76.54.131'

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False \
    --ip-range-filter $ipRangeFilter
```

## <a name="configure-an-ip-access-control-policy-by-using-powershell"></a><a id="configure-ip-firewall-ps"></a>Konfigurowanie zasad kontroli dostępu do adresu IP przy użyciu programu PowerShell

W poniższym skrypcie pokazano, jak utworzyć konto usługi Azure Cosmos DB z kontrolą dostępu do adresu IP:

```azurepowershell-interactive
# Create a Cosmos DB account with default values and IP Firewall enabled
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$ipRangeFilter = "192.168.221.17,183.240.196.255,40.76.54.131"

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0; "isZoneRedundant"=False },
    @{ "locationName"="East US 2"; "failoverPriority"=1, "isZoneRedundant"=False }
)

# Make sure there are no spaces in the comma-delimited list of IP addresses or CIDR ranges.
$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "ipRangeFilter"=$ipRangeFilter
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Rozwiązywanie problemów z zasadami kontroli dostępu do adresu IP

Problemy z zasadami kontroli dostępu do adresu IP można rozwiązać, korzystając z następujących opcji:

### <a name="azure-portal"></a>Portal Azure

Włączając zasady kontroli dostępu do adresu IP dla konta usługi Azure Cosmos DB, można zablokować wszystkie żądania do konta z komputerów spoza listy dozwolonych zakresów adresów IP. Aby włączyć operacje na płaszczyźnie danych portalu, takie jak przeglądanie kontenerów i wykonywanie zapytań o dokumenty, należy jawnie zezwolić na dostęp do witryny Azure portal przy użyciu **okienka zapory** w portalu.

### <a name="sdks"></a>Zestawy SDK

Podczas uzyskiwania dostępu do zasobów usługi Azure Cosmos DB przy użyciu zestawów SDK z komputerów, które nie znajdują się na liście dozwolonych, ogólna odpowiedź **403 Zabronione** jest zwracana bez dodatkowych szczegółów. Sprawdź listę dozwolonych adresów IP dla swojego konta i upewnij się, że do konta usługi Azure Cosmos DB jest stosowana poprawna konfiguracja zasad.

### <a name="source-ips-in-blocked-requests"></a>Źródłowe wiadomości IP w zablokowanych żądaniach

Włącz rejestrowanie diagnostyczne na koncie usługi Azure Cosmos DB. Te dzienniki pokazują każde żądanie i odpowiedź. Komunikaty związane z zaporą są rejestrowane za pomocą kodu zwrotnego 403. Filtrując te komunikaty, można zobaczyć źródłowe wiadomości IP dla zablokowanych żądań. Zobacz [rejestrowanie diagnostyczne usługi Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Żądania z podsieci z włączonym punktem końcowym usługi dla usługi Azure Cosmos DB

Żądania z podsieci w sieci wirtualnej, która ma punkt końcowy usługi dla usługi Azure Cosmos DB włączone wysyła tożsamości sieci wirtualnej i podsieci do kont usługi Azure Cosmos DB. Te żądania nie mają publicznego adresu IP źródła, więc filtry IP je odrzucają. Aby zezwolić na dostęp z określonych podsieci w sieciach wirtualnych, dodaj listę kontroli dostępu zgodnie z opisem w [temacie Jak skonfigurować dostęp oparty na sieci wirtualnej i podsieci dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Zastosowanie reguł zapory może potrwać do 15 minut.

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować punkt końcowy usługi sieci wirtualnej dla konta usługi Azure Cosmos DB, zobacz następujące artykuły:

* [Kontrola dostępu do sieci wirtualnej i podsieci dla konta usługi Azure Cosmos DB](vnet-service-endpoint.md)
* [Konfigurowanie dostępu opartego na sieci wirtualnej i podsieci dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
