---
title: Instrukcje konfigurowania zapory adresów IP dla konta usługi Azure Cosmos
description: Dowiedz się, jak skonfigurować zasady kontroli dostępu IP obsługę zapory dla kont bazy danych Azure Cosmos DB.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: d9a00bccb83fc60c96594ffacc5abde98c0f8470
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51632586"
---
# <a name="how-to-configure-ip-firewall-for-your-azure-cosmos-account"></a>Instrukcje konfigurowania zapory adresów IP dla konta usługi Azure Cosmos

Można zabezpieczyć dane przechowywane na koncie usługi Azure Cosmos przy użyciu zaporach adresów IP. Usługa Azure Cosmos DB obsługuje kontrolę dostępu na podstawie adresu IP obsługę zapory dla ruchu przychodzącego. Zapory adresów IP można ustawić na konto usługi Cosmos Azure przy użyciu jednej z następujących sposobów:

1. W witrynie Azure portal
2. Deklaratywne, przy użyciu szablonu usługi Azure Resource Manager
3. Programowo przy użyciu wiersza polecenia platformy Azure lub programu Azure Powershell, aktualizując **ipRangeFilter** właściwości.

## <a id="configure-ip-policy"></a> Konfigurowanie zapory adresów IP przy użyciu witryny Azure portal

Aby ustawić zasady kontroli dostępu IP w witrynie Azure portal, przejdź na stronę konta usługi Azure Cosmos DB, kliknij przycisk **zapory i sieci wirtualne** w menu nawigacji, następnie zmienić **dostęp z** wartość Aby **wybrane sieci**, a następnie kliknij przycisk **Zapisz**. 

![Zrzut ekranu przedstawiający sposób otworzyć stronę zapory w witrynie Azure portal](./media/how-to-configure-firewall/azure-portal-firewall.png)

Po włączeniu kontroli dostępu IP witryny Azure portal zapewnia możliwość określenia adresów IP, zakresy adresów IP, a także przełączników, aby umożliwić dostęp do innych usług platformy Azure i portalu Azure. W poniższych sekcjach znajdują się szczegółowe informacje na temat tych parametrów.

> [!NOTE]
> Po włączeniu zasady kontroli dostępu IP dla konta usługi Azure Cosmos wszystkich żądań do konta usługi Azure Cosmos z maszyn spoza listy dozwolonych zakresów adresów IP są odrzucane. Przeglądanie zasobów usługi Azure Cosmos DB z portalu są również blokowane do zapewnienia integralności kontroli dostępu.

### <a name="allow-requests-from-the-azure-portal"></a>Zezwalaj na żądania z witryny Azure portal 

Po włączeniu zasady kontroli dostępu IP programowo, należy dodać adres IP dla portalu Azure w celu **ipRangeFilter** właściwości, aby zachować dostęp. Portal adresy IP są:

|Region|Adres IP|
|------|----------|
|Niemcy|51.4.229.218|
|Chiny|139.217.8.252|
|Rząd USA|52.244.48.71|
|We wszystkich regionach z wyjątkiem trzy powyższe|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|

Aby umożliwić dostęp do witryny Azure portal, wybierając **zezwolić na dostęp z witryny Azure portal** opcji, jak pokazano na poniższym zrzucie ekranu: 

![Zrzut ekranu pokazujący sposób umożliwić dostęp do portalu Azure](./media/how-to-configure-firewall/enable-azure-portal.png)

### <a name="allow-requests-from-global-azure-datacenters-or-other-sources-within-azure"></a>Zezwalaj na żądania z globalnych centrów danych platformy Azure lub innych źródeł, w obrębie platformy Azure

Jeśli dostęp do konta usługi Azure Cosmos z usług, które nie udostępniają statycznego adresu IP, na przykład usługa Azure Stream analytics, itp. z funkcji platformy Azure, nadal możesz użyć zapory adresów IP do ograniczania dostępu. Aby umożliwić dostęp do konta usługi Azure Cosmos z takich usług, należy włączyć ustawienie zapory. Aby włączyć to ustawienie zapory, należy dodać adres IP — 0.0.0.0 do listy dozwolonych adresów IP. 0.0.0.0 uniemożliwia żądań do konta usługi Azure Cosmos zakres adresów IP centrum danych platformy Azure. To ustawienie nie zezwala na dostęp dla innych zakresów adresów IP do swojego konta usługi Azure Cosmos.

> [!NOTE]
> Ta opcja konfiguruje zaporę, aby zezwolić na wszystkie żądania z usługi Azure takie jak żądania z subskrypcji innych klientów wdrożonych na platformie Azure. Lista adresów IP jest dozwolony przez tę opcję, jest szeroka, więc ogranicza skuteczności zasad zapory. Ta opcja powinna służyć tylko wtedy, gdy żądań nie pochodzi z statyczne adresy IP lub podsieci w sieciach wirtualnych. Automatycznie po wybraniu tej opcji umożliwia dostęp z witryny Azure portal, ponieważ witryny Azure portal jest wdrażana na platformie Azure.

Aby umożliwić dostęp do witryny Azure portal, wybierając **akceptowania połączeń z publicznych centrów danych platformy Azure** opcji, jak pokazano na poniższym zrzucie ekranu: 

![Zrzut ekranu przedstawiający sposób otworzyć stronę zapory w witrynie Azure portal](./media/how-to-configure-firewall/enable-azure-services.png)

### <a name="requests-from-your-current-ip"></a>Żądania z Twojej bieżący adres IP

Można Uproszczenie projektowania aplikacji, witryny Azure portal ułatwia Identyfikuj i Dodaj adres IP swojej maszyny klienta do listy dozwolonych, dzięki czemu aplikacje działające na tym komputerze można uzyskać dostępu do konta usługi Azure Cosmos. Adres IP klienta jest wykrywany automatycznie przez portal. Być może adres IP klienta komputera lub adres IP bramy Twojej sieci. Upewnij się usunąć ten adres IP przed przełączania obciążeń w środowisku produkcyjnym. 

Aby włączyć Twój bieżący adres IP, zaznacz **Dodaj mój bieżący adres IP**, który dodaje Twój bieżący adres IP do listy adresów IP, a następnie kliknij przycisk **Zapisz**.

![Zrzut ekranu pokazujący sposób konfigurowania ustawień zapory dla bieżącego adresu IP](./media/how-to-configure-firewall/enable-current-ip.png)

### <a name="requests-from-cloud-services"></a>Żądania z usług cloud services

Na platformie Azure usługi cloud services są często stosowaną metodą do hostowania logiką usługi warstwy środkowej za pomocą usługi Azure Cosmos DB. Aby włączyć dostęp do konta usługi Azure Cosmos z usługi w chmurze, publiczny adres IP usługi w chmurze należy dodać do listy dozwolonych adresów IP skojarzonych z Twoim kontem usługi Azure Cosmos przez [Konfigurowanie zasad kontroli dostępu IP](#configure-ip-policy). Gwarantuje to, że wszystkich wystąpień roli usług w chmurze mają dostęp do Twojego konta usługi Azure Cosmos. Możesz pobrać adresy IP dla usług cloud services w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP dla usługi w chmurze wyświetlane w witrynie Azure portal](./media/how-to-configure-firewall/public-ip-addresses.png)

Podczas skalowania usługi w chmurze, dodając rolę dodatkowego wystąpienia tych nowych wystąpień automatycznie uzyskają dostęp do konta usługi Azure Cosmos, ponieważ są one częścią tej samej usługi w chmurze.

### <a name="requests-from-virtual-machines"></a>Żądania z maszyn wirtualnych

[Maszyny wirtualne](https://azure.microsoft.com/services/virtual-machines/) lub [zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) może również służyć do hostowania usług warstwy środkowej za pomocą usługi Azure Cosmos DB. Aby skonfigurować konto usługi Cosmos Aby zezwolić na dostęp z maszyn wirtualnych, publiczne adresy IP maszyn wirtualnych i/lub zestawu skalowania maszyn wirtualnych musi być skonfigurowany jako jeden z dozwolonych adresów IP dla konta usługi Azure Cosmos przez [Konfigurowanie adres IP zasady kontroli dostępu](#configure-ip-policy). Możesz pobrać adresy IP dla maszyn wirtualnych w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP dla maszyny wirtualnej, wyświetlane w witrynie Azure portal](./media/how-to-configure-firewall/public-ip-addresses-dns.png)

Po dodaniu wystąpień dodatkowych maszyn wirtualnych do grupy są automatycznie dostarczane dostęp do Twojego konta usługi Azure Cosmos.

### <a name="requests-from-the-internet"></a>Żądania z Internetu

Gdy uzyskujesz dostęp do Twojego konta usługi Azure Cosmos z komputer połączony z Internetem, adres IP klienta lub zakres adresów IP komputera, należy dodać do listy dozwolonych adresów IP dla swojego konta.

## <a id="configure-ip-firewall-arm"></a>Konfigurowanie zapory adresów IP przy użyciu szablonu usługi Resource Manager

Aby skonfigurować kontrolę dostępu do konta usługi Azure Cosmos, należy określić szablon usługi Resource Manager **ipRangeFilter** atrybutu z listy zakresów adresów IP, które powinny być na liście dozwolonych. Na przykład dodaj następujący kod JSON do szablonu:

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
     "ipRangeFilter":"183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
   }
```

## <a id="configure-ip-firewall-cli"></a>Konfigurowanie zasad kontroli dostępu IP przy użyciu wiersza polecenia platformy Azure

Następujące polecenie przedstawia sposób tworzenia konta usługi Azure Cosmos przy użyciu kontroli dostępu IP: 

```azurecli-interactive

name="<Azure Cosmos account name>"
resourceGroupName="<Resource group name>"

az cosmosdb create \
  --name $name \
  --kind GlobalDocumentDB \
  --resource-group $resourceGroupName \
  --max-interval 10 \
  --max-staleness-prefix 200 \
  --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

Aby zaktualizować ustawienia zapory dla istniejącego konta, uruchom następujące polecenie:

```azurecli-interactive
az cosmosdb update \
      --name $name \
      --resource-group $resourceGroupName \
      --ip-range-filter "183.240.196.255, 104.42.195.92,40.76.54.131, 52.176.6.30,52.169.50.45,52.187.184.26"
```

## <a id="troubleshoot-ip-firewall"></a>Jak rozwiązywać problemy zasad kontroli dostępu IP

Można rozwiązywać problemy zasad kontroli dostępu IP przy użyciu następujących opcji: 

### <a name="azure-portal"></a>Azure Portal 
Po włączeniu zasady kontroli dostępu IP dla konta usługi Azure Cosmos wszystkie żądania do Twojego konta z maszyn spoza listy dozwolonych zakresów adresów IP są blokowane. W związku z tym, jeśli chcesz włączyć operacje płaszczyzny danych portalu, takich jak przeglądanie kontenerów i tworzenia zapytań dotyczących dokumentów, musisz jawnie zezwolić na dostęp do portalu Azure za pomocą **zapory** okienku w portalu.

### <a name="sdks"></a>Zestawy SDK 
Gdy uzyskujesz dostęp do zasobów usługi Azure Cosmos przy użyciu zestawów SDK z komputerów, które nie są na liście dozwolonych **ogólnego 404 Nie znaleziono odpowiedzi jest zwracany za pomocą dodatkowych szczegółów**. Sprawdź listy dozwolonych adresów IP dla swojego konta i upewnij się, że konfiguracja zasad poprawne jest stosowane do Twojego konta usługi Cosmos. 

### <a name="check-source-ips-in-blocked-requests"></a>Sprawdź źródłowych adresów IP w zablokowane żądania
Włączanie diagnostyki rejestrowanie na Twoim koncie usługi Azure Cosmos te dzienniki pokazywałaby każdego żądania i odpowiedzi. **Komunikaty dotyczące zapory wewnętrznie są rejestrowane przy użyciu 403 kod powrotny**. Filtrując te komunikaty, możesz zobaczyć źródłowe adresy IP dla zablokowane żądania. Zobacz [rejestrowania diagnostycznego usługi Azure Cosmos DB](logging.md).

### <a name="requests-from-subnet-with-service-endpoint-for-azure-cosmos-database-enabled"></a>Żądania z podsieci z punktu końcowego usługi dla bazy danych Azure Cosmos włączone
Żądania z podsieci w sieci Wirtualnej, która ma punkt końcowy usługi dla usługi Azure Cosmos DB włączone tożsamość sieci Wirtualnej i podsieci wysyła do konta usługi Azure Cosmos. Te żądania nie ma publiczny adres IP źródła, dzięki czemu są one odrzucone przez filtry IP. Aby zezwolić na dostęp z określonych podsieci w sieciach wirtualnych, dodać listy kontroli dostępu do sieci Wirtualnej przedstawionych w [sposób konfigurowania sieci wirtualnej i dostępu dla konta usługi Azure Cosmos oparta na podsieci](how-to-configure-vnet-service-endpoint.md). Może upłynąć do 15 minut dla reguł zapory do zastosowania.


## <a name="next-steps"></a>Kolejne kroki

Aby skonfigurować punkt końcowy usługi sieci wirtualnej dla swojego konta usługi Azure Cosmos DB, zobacz następujące artykuły:

* [Kontrola dostępu w sieci Wirtualnej i podsieci dla swojego konta usługi Azure Cosmos](vnet-service-endpoint.md)
* [Jak skonfigurować sieć wirtualną i podsieć, na podstawie dostępu dla konta usługi Azure Cosmos](how-to-configure-vnet-service-endpoint.md)


