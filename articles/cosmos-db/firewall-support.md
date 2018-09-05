---
title: Kontrola dostępu do usługi Azure Cosmos DB, obsługa zapory & IP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać zasad kontroli dostępu IP obsługę zapory dla kont bazy danych Azure Cosmos DB.
keywords: Kontrola dostępu do adresów IP, obsługa zapory
services: cosmos-db
author: kanshiG
manager: kfile
tags: azure-resource-manager
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: govindk
ms.openlocfilehash: b21debdd6baa0a6587318ad861a821840ec6879c
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666701"
---
# <a name="azure-cosmos-db-firewall-support"></a>Obsługa zapory w usłudze Azure Cosmos DB
Aby zabezpieczyć dane przechowywane na koncie bazy danych usługi Azure Cosmos DB, Azure Cosmos DB udostępnił pomocy technicznej na podstawie klucza tajnego [modelu autoryzacji](https://msdn.microsoft.com/library/azure/dn783368.aspx) , która zawiera kod uwierzytelniania wiadomości bazujących na skrótach silne (metoda HMAC). Oprócz modelu autoryzacji na podstawie tajnego usługi Azure Cosmos DB obsługuje teraz, zasad opartych na kontroli dostępu opartych na protokole IP, obsługi zapory dla ruchu przychodzącego. Ten model jest podobne do reguł zapory systemu tradycyjnych baz danych i zapewnia dodatkowy poziom zabezpieczeń do konta bazy danych Azure Cosmos DB. W tym modelu można teraz skonfigurować kontem bazy danych usługi Azure Cosmos DB, aby były dostępne tylko z zatwierdzonego zestawu maszyn i/lub usług w chmurze. Dostęp do zasobów usługi Azure Cosmos DB z tych zestawów zatwierdzone komputery i usługi nadal wymaga obiekt wywołujący, aby przedstawić prawidłowy token autoryzacji.

> [!NOTE]
> Obecnie Obsługa zapory jest dostępna dla kont usługi Azure Cosmos DB — interfejs API SQL i Mongo interfejsu API. Możliwość konfigurowania zapory dla innych interfejsów API i w chmurach suwerennych, takich jak Azure (Niemcy) lub Azure dla instytucji rządowych będą dostępne wkrótce. Jeśli planujesz skonfigurować punkt końcowy usługi listy ACL dla Twojego konta usługi Azure Cosmos DB, które ma istniejącej zapory adresów IP, które są skonfigurowane, należy pamiętać, konfigurację zapory, Usuń zapory adresów IP, a następnie skonfiguruj listy ACL punktów końcowych usługi. Po skonfigurowaniu punktu końcowego usługi, można ponownie włączyć zapory adresów IP, jeśli to konieczne.

## <a name="ip-access-control-overview"></a>Omówienie kontroli dostępu IP
Domyślnie konto usługi Azure Cosmos DB jest dostępne z publicznej sieci Internet, o ile do żądania dołączono prawidłowy token autoryzacji. Aby skonfigurować kontrolę dostępu opartą na zasadach IP, użytkownik musi podać zestaw adresów IP lub zakresów adresów IP w formacie CIDR, które będą uwzględniane jako lista dozwolonych adresów IP klienta dla danego konta bazy danych. Po zastosowaniu konfiguracji wszystkie żądania pochodzące z maszyn spoza tej listy dozwolonych będą blokowane przez serwer.  Połączenie przetwarzania przepływ kontroli dostępu opartych na protokole IP przedstawiono na poniższym diagramie:

![Diagram przedstawiający proces łączenia dla kontroli dostępu opartych na protokole IP](./media/firewall-support/firewall-support-flow.png)

## <a id="configure-ip-policy"></a> Konfigurowanie zasad kontroli dostępu IP
Zasady kontroli dostępu IP można ustawić w witrynie Azure portal lub programowo za pomocą [wiersza polecenia platformy Azure](cli-samples.md), [programu Azure Powershell](powershell-samples.md), lub [interfejsu API REST](/rest/api/cosmos-db/) , aktualizując **ipRangeFilter** właściwości. 

Aby ustawić zasady kontroli dostępu IP w witrynie Azure portal, przejdź na stronę konta usługi Azure Cosmos DB, kliknij przycisk **zapory i sieci wirtualne** w menu nawigacji, następnie zmienić **dostęp z** wartość Aby **wybrane sieci**, a następnie kliknij przycisk **Zapisz**. 

![Zrzut ekranu przedstawiający sposób otworzyć stronę zapory w witrynie Azure portal](./media/firewall-support/azure-portal-firewall.png)

Po włączeniu kontroli dostępu IP, portal zapewnia możliwość określenia adresów IP i zakresów, a także przełączników, aby umożliwić dostęp do innych usług platformy Azure i portalu Azure. Dodatkowe informacje na temat tych parametrów, podano w poniższych sekcjach.

> [!NOTE]
> Po włączeniu zasady kontroli dostępu IP dla Twojego konta bazy danych Azure Cosmos DB cały dostęp do Twojego konta bazy danych Azure Cosmos DB z maszyn spoza skonfigurowanych dozwolone listę zakresów adresów IP są zablokowane. Ze względu na ten model przeglądania operacji płaszczyzny danych z poziomu portalu będzie również zablokowany do zapewnienia integralności kontroli dostępu.

## <a name="connections-from-the-azure-portal"></a>Połączenia z witryny Azure portal 

Po włączeniu zasady kontroli dostępu IP programowo, należy dodać adres IP dla portalu Azure w celu **ipRangeFilter** właściwości, aby zachować dostęp. Portal adresy IP są:

|Region|Adres IP|
|------|----------|
|We wszystkich regionach z wyjątkiem tych określony poniżej|104.42.195.92,40.76.54.131,52.176.6.30,52.169.50.45,52.187.184.26|
|Niemcy|51.4.229.218|
|Chiny|139.217.8.252|
|Rząd USA|52.244.48.71|

Dostęp do portalu Azure jest domyślnie włączona, gdy zmienią się w ustawieniach zapory, aby **wybrane sieci** w witrynie Azure portal. 

![Zrzut ekranu pokazujący sposób umożliwić dostęp do portalu Azure](./media/firewall-support/enable-azure-portal.png)

## <a name="connections-from-global-azure-datacenters-or-azure-paas-services"></a>Połączenia z centrami danych globalnej platformy Azure lub usług PaaS platformy Azure

Usługi PaaS platformy Azure, takich jak Azure Stream analytics, Azure Functions itd są używane w połączeniu z usługą Azure Cosmos DB. Aby umożliwić aplikacjom z innymi usługami PaaS platformy Azure, nawiązać połączenia z zasobami usługi Azure Cosmos DB, należy włączyć ustawienie zapory. Aby włączyć to ustawienie zapory, należy dodać adres IP — 0.0.0.0 do listy dozwolonych adresów IP. 0.0.0.0 ogranicza połączenia do konta usługi Azure Cosmos DB z zakresu adresów IP centrum danych platformy Azure. To ustawienie nie zezwala na dostęp do innych zakresów adresów IP do konta usługi Azure Cosmos DB.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

Dostęp do połączeń z w ramach globalnych centrów danych platformy Azure jest domyślnie włączona, gdy zmienią się w ustawieniach zapory, aby **wybrane sieci** w witrynie Azure portal. 

![Zrzut ekranu przedstawiający sposób otworzyć stronę zapory w witrynie Azure portal](./media/firewall-support/enable-azure-services.png)

## <a name="connections-from-your-current-ip"></a>Połączenia z usługi bieżący adres IP

Można Uproszczenie projektowania aplikacji, witryny Azure portal ułatwia Identyfikuj i Dodaj adres IP swojej maszyny klienta do listy dozwolonych, dzięki czemu aplikacje działające na tym komputerze można uzyskać dostępu do konta usługi Azure Cosmos DB. Adres IP klienta w tym miejscu jest wykrywany widziany przez portal. Może to być adres IP klienta na komputerze, ale może być również adres IP bramy Twojej sieci. Pamiętaj go usunąć przed skierowaniem do produkcji.

Aby włączyć Twój bieżący adres IP, zaznacz **Dodaj mój bieżący adres IP**, który dodaje Twój bieżący adres IP do listy adresów IP, a następnie kliknij przycisk **Zapisz**.

![Zrzut ekranu pokazujący sposób konfigurowania ustawień zapory dla bieżącego adresu IP](./media/firewall-support/enable-current-ip.png)

## <a name="connections-from-cloud-services"></a>Połączenia z usługami w chmurze
Na platformie Azure usługi cloud services są często stosowaną metodą do hostowania logiką usługi warstwy środkowej za pomocą usługi Azure Cosmos DB. Aby włączyć dostęp do konta bazy danych usługi Azure Cosmos DB z usługi w chmurze, publiczny adres IP usługi w chmurze należy dodać do listy dozwolonych adresów IP skojarzonych z Twoim kontem bazy danych Azure Cosmos DB, [Konfigurowanie dostępu do adresów IP kontrolowanie zasad](#configure-ip-policy). Gwarantuje to, że wszystkie wystąpienia roli usług w chmurze mają dostęp do Twojego konta bazy danych Azure Cosmos DB. Możesz pobrać adresy IP dla usług cloud services w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu:

![Zrzut ekranu przedstawiający publiczny adres IP dla usługi w chmurze wyświetlane w witrynie Azure portal](./media/firewall-support/public-ip-addresses.png)

Podczas skalowania usługi w chmurze, dodając rolę dodatkowego wystąpienia tych nowych wystąpień automatycznie uzyskają dostęp do bazy danych usługi Azure Cosmos DB, ponieważ są one częścią tej samej usługi w chmurze.

## <a name="connections-from-virtual-machines"></a>Połączenia z maszynami wirtualnymi
[Maszyny wirtualne](https://azure.microsoft.com/services/virtual-machines/) lub [zestawy skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) może również służyć do hostowania usług warstwy środkowej za pomocą usługi Azure Cosmos DB.  Aby skonfigurować konto bazy danych Azure Cosmos DB, aby zezwolić na dostęp z maszyn wirtualnych, publiczne adresy IP maszyn wirtualnych i/lub maszyny wirtualnej zestawu skalowania musi być skonfigurowany jako jeden z dozwolonych adresów IP dla Twojego konta bazy danych Azure Cosmos DB, [Konfigurowanie zasad kontroli dostępu IP](#configure-ip-policy). Możesz pobrać adresy IP dla maszyn wirtualnych w witrynie Azure portal, jak pokazano na poniższym zrzucie ekranu.

![Zrzut ekranu przedstawiający publiczny adres IP dla maszyny wirtualnej, wyświetlane w witrynie Azure portal](./media/firewall-support/public-ip-addresses-dns.png)

Po dodaniu wystąpień dodatkowych maszyn wirtualnych do grupy są automatycznie dostarczane dostęp do Twojego konta bazy danych Azure Cosmos DB.

## <a name="connections-from-the-internet"></a>Połączenia z Internetu
Gdy uzyskujesz dostęp do konta bazy danych usługi Azure Cosmos DB z komputer połączony z Internetem, adres IP klienta lub zakres adresów IP komputera, należy dodać do listy dozwolonych adresów IP do bazy danych usługi Azure Cosmos DB. 

## <a name="using-azure-resource-manager-template-to-set-up-the-ip-access-control"></a>Aby skonfigurować kontroli dostępu IP przy użyciu szablonu Azure Resource Manager

Dodaj następujący kod JSON do szablonu, aby skonfigurować kontrolę dostępu do adresów IP. Szablon usługi Resource Manager dla konta mają atrybut ipRangeFilter, który jest listą zakresów adresów IP, które powinny być na liście dozwolonych.

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
     "ipRangeFilter":"10.0.0.1,10.0.0.2,183.240.196.255"
   }
   }
```

## <a name="troubleshooting-the-ip-access-control-policy"></a>Rozwiązywanie problemów z zasady kontroli dostępu IP
### <a name="portal-operations"></a>Operacje w portalu
Po włączeniu zasady kontroli dostępu IP dla Twojego konta bazy danych Azure Cosmos DB cały dostęp do Twojego konta bazy danych Azure Cosmos DB z maszyn spoza skonfigurowanych dozwolone listę zakresów adresów IP są zablokowane. W związku z tym jeśli chcesz włączyć operacje płaszczyzny danych portalu, takich jak przeglądanie kontenerów i tworzenia zapytań dotyczących dokumentów, musisz jawnie zezwolić na dostęp do portalu Azure za pomocą **zapory** strony w portalu. 

### <a name="sdk--rest-api"></a>Interfejs API zestawu SDK & Rest
Dla zabezpieczeń ze względu na dostęp za pośrednictwem zestawu SDK lub interfejsu API REST z komputerów nie ma na liście dozwolonych zwróci ogólny 404 Nie znaleziono odpowiedzi za pomocą dodatkowych szczegółów. Sprawdź IP skonfigurowany dla Twojego konta bazy danych Azure Cosmos DB listy dozwolonych, aby upewnić się, że konfiguracja zasad poprawne jest stosowany do Twojego konta bazy danych Azure Cosmos DB.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacji na temat porady dotyczące wydajności związane z siecią, zobacz [porady dotyczące wydajności](performance-tips.md).

