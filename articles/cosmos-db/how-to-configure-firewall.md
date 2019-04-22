---
title: Konfigurowanie zapory adresów IP dla swojego konta usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować zasady kontroli dostępu IP obsługę zapory dla kont bazy danych Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 26f2131fd62ddc83c2a6d93c4cff557402a88463
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281118"
---
# <a name="configure-ip-firewall-in-azure-cosmos-db"></a>Konfigurowanie zapory adresów IP w usłudze Azure Cosmos DB

Można zabezpieczyć dane przechowywane na koncie usługi Azure Cosmos DB przy użyciu zaporach adresów IP. Usługa Azure Cosmos DB obsługuje kontrolę dostępu na podstawie adresu IP obsługę zapory dla ruchu przychodzącego. Zapory adresów IP można ustawić na koncie usługi Azure Cosmos DB przy użyciu jednej z następujących sposobów:

* W witrynie Azure portal
* Deklaratywne, przy użyciu szablonu usługi Azure Resource Manager
* Programowo przy użyciu wiersza polecenia platformy Azure lub programu Azure PowerShell, aktualizując **ipRangeFilter** właściwości

## <a id="configure-ip-policy"></a> Konfigurowanie zapory adresów IP przy użyciu witryny Azure portal

Aby ustawić zasady kontroli dostępu IP w witrynie Azure portal, przejdź do strony konta usługi Azure Cosmos DB, a następnie wybierz **zapory i sieci wirtualne** w menu nawigacji. Zmiana **dostęp z** wartość, która **wybrane sieci**, a następnie wybierz pozycję **Zapisz**. 

![Zrzut ekranu przedstawiający sposób otworzyć stronę zapory w witrynie Azure portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Po włączeniu kontroli dostępu IP witryny Azure portal zapewnia możliwość określenia adresów IP, zakresy adresów IP i przełączników. Przełączniki zapewnianie dostępu do innych usług platformy Azure i portalu Azure. W poniższych sekcjach znajdują się szczegółowe informacje na temat tych parametrów.

> [!NOTE]
> Po włączeniu zasady kontroli dostępu IP dla konta usługi Azure Cosmos DB, wszystkie żądania do swojego konta usługi Azure Cosmos DB z maszyn spoza listy dozwolonych zakresów adresów IP są odrzucane. Przeglądanie zasobów usługi Azure Cosmos DB z portalu także jest blokowana w celu zapewnienia integralności kontroli dostępu.

### <a name="allow-requests-from-the-azure-portal"></a>Zezwalaj na żądania z witryny Azure portal 

Po włączeniu zasady kontroli dostępu IP programowo, należy dodać adres IP dla portalu Azure w celu **ipRangeFilter** właściwości, aby zachować dostęp. Portal adresy IP są:

|Region|Adres IP|
|------|----------|
|Niemcy|51.4.229.218|
|Chiny|139.217.8.252|
|Rząd USA|52.244.48.71|
|Inne regiony|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Aby umożliwić dostęp do witryny Azure portal, wybierając **zezwolić na dostęp z witryny Azure portal** opcji, jak pokazano na poniższym zrzucie ekranu: 

![Zrzut ekranu pokazujący sposób umożliwić dostęp do portalu Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Zezwalaj na żądania z globalnych centrów danych platformy Azure lub innych źródeł, w obrębie platformy Azure

Jeśli uzyskujesz dostęp do konta usługi Azure Cosmos DB z usług, które nie udostępniają statycznego adresu IP (na przykład usługi Azure Stream Analytics i Azure Functions), możesz nadal używać zapory adresów IP, aby ograniczyć dostęp. Aby umożliwić dostęp do konta usługi Azure Cosmos DB z takich usług, należy dodać adres IP 0.0.0.0 do listy dozwolonych adresów IP. 0.0.0.0 adres ogranicza żądania do konta usługi Azure Cosmos DB z zakresu adresów IP centrum danych platformy Azure. To ustawienie nie zezwala na dostęp do innych zakresów adresów IP do swojego konta usługi Azure Cosmos DB.

> [!NOTE]
> Ta opcja konfiguruje zaporę, aby zezwolić na wszystkie żądania z platformy Azure, w tym realizowania żądań z subskrypcji innych klientów wdrożonych na platformie Azure. Listy adresów IP jest dozwolony przez tę opcję, jest szerokie, więc ogranicza skuteczności zasad zapory. Użyj tej opcji tylko wtedy, gdy żądań nie pochodzi z statyczne adresy IP lub podsieci w sieciach wirtualnych. Automatycznie po wybraniu tej opcji umożliwia dostęp z witryny Azure portal, ponieważ witryny Azure portal jest wdrażana na platformie Azure.

Aby umożliwić dostęp do witryny Azure portal, wybierając **akceptowania połączeń z w ramach publicznych centrów danych platformy Azure** opcji, jak pokazano na poniższym zrzucie ekranu: 

![Zrzut ekranu przedstawiający sposób otworzyć stronę zapory w witrynie Azure portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Żądania z Twojej bieżący adres IP

Aby uprościć programowanie, witryny Azure portal ułatwia Identyfikuj i Dodaj adres IP swojej maszyny klienta do listy dozwolonych. Aplikacje działające na tym komputerze mogą uzyskiwać dostęp do konta usługi Azure Cosmos DB. 

Portal automatycznie wykrywa adres IP klienta. Może być adres IP klienta komputera lub adres IP bramy Twojej sieci. Upewnij się usunąć ten adres IP, przed podjęciem dalszych swoje obciążenia do środowiska produkcyjnego. 

Aby dodać swoje bieżący adres IP do listy adresów IP, wybierz **Dodaj mój bieżący adres IP**. Następnie wybierz pozycję **Zapisz**.

![Zrzut ekranu pokazujący sposób konfigurowania ustawień zapory dla bieżącego adresu IP](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Żądania z usług cloud services

Na platformie Azure usługi cloud services są często stosowaną metodą do hostowania usługi warstwy środkowej logiki za pomocą usługi Azure Cosmos DB. Aby włączyć dostęp do swojego konta usługi Azure Cosmos DB z usługi w chmurze, należy dodać publiczny adres IP usługi w chmurze, do listy dozwolonych adresów IP skojarzonych z Twoim kontem usługi Azure Cosmos DB, [Konfigurowanie zasad kontroli dostępu IP](#configure-ip-policy). Gwarantuje to, że wszystkie wystąpienia roli usług w chmurze mają dostęp do konta usługi Azure Cosmos DB. 

Możesz pobrać adresy IP dla usług cloud services w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP dla usługi w chmurze wyświetlane w witrynie Azure portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Podczas skalowania usługi w chmurze, dodając wystąpień roli tych nowych wystąpień automatycznie uzyskają dostęp do konta usługi Azure Cosmos DB, ponieważ są one częścią tej samej usługi w chmurze.

### <a name="requests-from-virtual-machines"></a>Żądania z maszyn wirtualnych

Można również użyć [maszyn wirtualnych](https://azure.microsoft.com/services/virtual-machines/) lub [zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) do hostowania usług warstwy środkowej za pomocą usługi Azure Cosmos DB. Aby skonfigurować konto usługi Cosmos DB, aby zezwolić na dostęp z maszyn wirtualnych, należy skonfigurować publiczny adres IP maszyny wirtualnej i/lub zestawie jako jeden z dozwolonych adresów IP dla swojego konta usługi Azure Cosmos DB, skalowania maszyn wirtualnych [ Konfigurowanie zasad kontroli dostępu IP](#configure-ip-policy). 

Możesz pobrać adresy IP dla maszyn wirtualnych w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP dla maszyny wirtualnej, wyświetlane w witrynie Azure portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Po dodaniu wystąpień maszyn wirtualnych do grupy, automatycznie otrzymują dostęp do Twojego konta usługi Azure Cosmos DB.

### <a name="requests-from-the-internet"></a>Żądania z Internetu

Gdy uzyskujesz dostęp do konta usługi Azure Cosmos DB z komputer połączony z Internetem, adres IP klienta lub zakres adresów IP komputera, należy dodać do listy dozwolonych adresów IP dla swojego konta.

## <a id="configure-ip-firewall-arm"></a>Konfigurowanie zapory adresów IP przy użyciu szablonu usługi Resource Manager

Aby skonfigurować kontrolę dostępu do konta usługi Azure Cosmos DB, upewnij się, że szablon usługi Resource Manager Określa **ipRangeFilter** atrybutu z listy dozwolonych zakresów adresów IP. Na przykład dodaj następujący kod JSON do szablonu:

```json
   {
     "apiVersion": "2015-04-08",
     "type": "Microsoft.DocumentDB/databaseAccounts",
     "kind": "GlobalDocumentDB",
     "name": "[parameters('databaseAccountName')]",
     "location": "[resourceGroup().location]",
     "properties": {
       "databaseAccountOfferType": "Standard",
       "name": "[parameters('databaseAccountName')]",
       "ipRangeFilter":"183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
     }
   }
```

## <a id="configure-ip-firewall-cli"></a>Konfigurowanie zasad kontroli dostępu IP przy użyciu wiersza polecenia platformy Azure

Następujące polecenie pokazuje, jak utworzyć konto usługi Azure Cosmos DB przy użyciu kontroli dostępu IP: 

```azurecli-interactive

name="<Azure Cosmos DB account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

Aby zaktualizować ustawienia zapory dla istniejącego konta, uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255,104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Rozwiązywanie problemów z zasad kontroli dostępu IP

Sposoby rozwiązywania problemów przy użyciu zasad kontroli dostępu IP, korzystając z następujących opcji: 

### <a name="azure-portal"></a>Azure Portal 
Po włączeniu zasady kontroli dostępu IP dla swojego konta usługi Azure Cosmos DB możesz zablokować wszystkie żądania do Twojego konta z maszyn spoza listy dozwolonych zakresów adresów IP. Aby włączyć portal operacje płaszczyzny danych, takie jak przeglądanie kontenerów i wykonywanie zapytań względem dokumentów, musisz jawnie zezwolić na dostęp do portalu Azure za pomocą **zapory** okienku w portalu.

### <a name="sdks"></a>Zestawy SDK 
Jeśli uzyskujesz dostęp do zasobów usługi Azure Cosmos DB przy użyciu zestawów SDK z komputerów, które nie są na liście dozwolonych ogólnego **403 Zabroniony** odpowiedzi jest zwracany za pomocą dodatkowych szczegółów. Sprawdź listy dozwolonych adresów IP dla swojego konta, a następnie upewnij się, że konfiguracja prawidłowe zasady jest stosowana do swojego konta usługi Azure Cosmos DB. 

### <a name="source-ips-in-blocked-requests"></a>Źródłowe adresy IP w zablokowane żądania
Włącz rejestrowanie diagnostyczne na Twoim koncie usługi Azure Cosmos DB. Te dzienniki przedstawiają każdego żądania i odpowiedzi. Komunikaty dotyczące zapory są rejestrowane przy użyciu 403 kod powrotny. Filtrując te komunikaty, możesz zobaczyć źródłowe adresy IP dla zablokowane żądania. Zobacz [rejestrowania diagnostycznego usługi Azure Cosmos DB](logging.md).

### <a name="requests-from-a-subnet-with-a-service-endpoint-for-azure-cosmos-db-enabled"></a>Włączone żądań z podsieci z punktem końcowym usługi dla usługi Azure Cosmos DB
Żądań z podsieci w sieci wirtualnej, która zawiera punkt końcowy usługi dla usługi Azure Cosmos DB włączone wysyła wirtualnego tożsamość sieci i podsieci, do kont usługi Azure Cosmos DB. Te żądania nie ma publiczny adres IP źródła, więc filtry IP Odrzuć je. Aby zezwolić na dostęp z określonych podsieci w sieciach wirtualnych, dodać listy kontroli dostępu, zgodnie z opisem w [sposób konfigurowania sieci wirtualnej i podsieci na podstawie dostępu dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md). Może upłynąć do 15 minut dla reguł zapory do zastosowania.


## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować punkt końcowy usługi sieci wirtualnej dla swojego konta usługi Azure Cosmos DB, zobacz następujące artykuły:

* [Wirtualne sieci i podsieci kontroli dostępu dla konta usługi Azure Cosmos DB](vnet-service-endpoint.md)
* [Konfigurowanie sieci wirtualnej i podsieci na podstawie dostępu dla konta usługi Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)


