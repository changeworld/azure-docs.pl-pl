---
title: Omówienie tagów usługi platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o tagach usług. Tagi usługi pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 568fc880711d42941fd9aef2ea19b8ac3123793a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384249"
---
# <a name="virtual-network-service-tags"></a>Tagi usługi sieci wirtualnej
<a name="network-service-tags"></a>

Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Firma Microsoft zarządza prefiksami adresów objętymi tagiem usługi i automatycznie aktualizuje tag usługi w miarę zmiany adresów, minimalizując złożoność częstych aktualizacji reguł zabezpieczeń sieciowych.

Za pomocą tagów usługi można zdefiniować kontrolki dostępu do sieci w [sieciowych grupach](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) zabezpieczeń lub [Zaporze platformy Azure](https://docs.microsoft.com/azure/firewall/service-tags). Podczas tworzenia reguł zabezpieczeń należy używać tagów usług zamiast określonych adresów IP. Określając nazwę tagu usługi (na przykład **ApiManagement)** w odpowiednim polu *źródłowym* lub *docelowym* reguły, można zezwolić lub odmówić ruchu dla odpowiedniej usługi.

Za pomocą tagów usługi można osiągnąć izolację sieci i chronić zasoby platformy Azure przed ogólnym Internetem podczas uzyskiwania dostępu do usług platformy Azure, które mają publiczne punkty końcowe. Utwórz reguły grupy zabezpieczeń sieci przychodzącej/wychodzącej, aby odmówić ruchu do/z **Internetu** i zezwolić na ruch do/z **usługi AzureCloud** lub innych [dostępnych tagów usług](#available-service-tags) określonych usług platformy Azure.

## <a name="available-service-tags"></a>Dostępne tagi usług
Poniższa tabela zawiera wszystkie tagi usługi dostępne do użycia w regułach [sieciowej grupy zabezpieczeń.](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)

Kolumny wskazują, czy znacznik:

- Nadaje się do reguł, które obejmują ruch przychodzący lub wychodzący.
- Obsługuje zakres [regionalny.](https://azure.microsoft.com/regions)
- Jest użyteczny w [regułach zapory platformy Azure.](https://docs.microsoft.com/azure/firewall/service-tags)

Domyślnie tagi usługi odzwierciedlają zakresy dla całej chmury. Niektóre tagi usługi umożliwiają również bardziej szczegółową kontrolę, ograniczając odpowiednie zakresy adresów IP do określonego regionu. Na przykład tag usługi **Storage** reprezentuje usługę Azure Storage dla całej chmury, ale **Storage.WestUS** zawęża zakres tylko do zakresu adresów IP magazynu z regionu WestUS. W poniższej tabeli wskazano, czy każdy tag usługi obsługuje taki zakres regionalny.  

| Tag | Przeznaczenie | Czy można używać ruchu przychodzącego lub wychodzącego? | Może być regionalny? | Czy można używać z Zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Grupa akcji** | Grupa działania. | Przychodzący | Nie | Nie |
| **ApiManagement (ApiManagement)** | Ruch zarządzania dla wdrożeń dedykowanych usługi Azure API Management. <br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy usługi Usługi Azure API Management dla płaszczyzny kontroli na region. Dzięki temu klienci mogą wykonywać operacje zarządzania na interfejsach API, operacje, zasady, NamedValues skonfigurowane w usłudze zarządzania interfejsami API.  | Przychodzący | Tak | Tak |
| **AplikacjaInsightsDostępność** | Dostępność usługi Application Insights. | Przychodzący | Nie | Nie |
| **Konfiguracja aplikacji** | Konfiguracja aplikacji. | Wychodzący | Nie | Nie |
| **AppService**    | Azure App Service. Ten tag jest zalecany dla reguł zabezpieczeń wychodzących do frontonomi aplikacji sieci web. | Wychodzący | Tak | Tak |
| **AppServiceManagement (Usługi w aplikacjach)** | Ruch zarządzania dla wdrożeń dedykowanych środowisku usługi app service. | Obie | Nie | Tak |
| **Usługa AzureActiveDirectory** | Azure Active Directory. | Wychodzący | Nie | Tak |
| **Usługi AzureActiveDirectoryDomainServices** | Ruch zarządzania dla wdrożeń dedykowanych usługom domenowym Active Directory platformy Azure. | Obie | Nie | Tak |
| **AzureAdvancedThreatProtection** | Zaawansowana ochrona przed zagrożeniami platformy Azure. | Wychodzący | Nie | Nie |
| **Usługa AzureBackup** |Kopia zapasowa platformy Azure.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **Usługi Storage** i **AzureActiveDirectory.** | Wychodzący | Nie | Tak |
| **Usługa AzureBotService** | Azure Bot Service. | Wychodzący | Nie | Nie |
| **AzureCloud** | Wszystkie [publiczne adresy IP centrum danych](https://www.microsoft.com/download/details.aspx?id=56519). | Wychodzący | Tak | Tak |
| **Usługa AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Ten tag lub adresy IP objęte tym tagiem mogą służyć do udzielania indeksajerom bezpiecznego dostępu do źródeł danych. Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją połączenia indeksatora.](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) <br/><br/> *Uwaga:* Adres IP usługi wyszukiwania nie znajduje się na liście zakresów adresów IP dla tego tagu usługi, a **także musi zostać dodany** do zapory IP źródeł danych. | Przychodzący | Nie | Nie |
| **Połączenia azureowe** | Łączniki usługi Azure Logic Apps dla połączeń sondy/zaplecza. | Przychodzący | Tak | Tak |
| **AzureContainerRegistry** | Rejestru kontenerów platformy Azure. | Wychodzący | Tak | Tak |
| **Usługa AzureCosmosDB** | Azure Cosmos DB. | Wychodzący | Tak | Tak |
| **AzureDatabricks (Usługi AzureDatabricks)** | Azure Databricks. | Obie | Nie | Nie |
| **AzureDataExplorerManagement (AzureDataExplorerManagement)** | Zarządzanie eksploratorem danych platformy Azure. | Przychodzący | Nie | Nie |
| **Lake z danymi o danych Azure** | Azure Data Lake Storage Gen1. | Wychodzący | Nie | Tak |
| **Usługa AzureDevSpaces** | Azure Dev Spaces. | Wychodzący | Nie | Nie |
| **Usługa AzureEventGrid** | Azure Event Grid. <br/><br/>*Uwaga:* Ten tag obejmuje punkty końcowe usługi Azure Event Grid tylko w południowo-środkowej, amerykańskiej, wschodniej, wschodniej części stanów USA 2, u. zachodnie 2 i środkowej w stanach ZJEDNOCZONYCH. | Obie | Nie | Nie |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Drzwi frontowe platformy Azure. | Obie | Nie | Nie |
| **Moduł AzureInformationProtection** | Azure Information Protection.<br/><br/>*Uwaga:* Ten tag ma zależność od **azureactivedirectory**, **AzureFrontDoor.Frontend** i **AzureFrontDoor.FirstParty** tagów. | Wychodzący | Nie | Nie |
| **Usługa AzureIoTHub** | Azure IoT Hub. | Wychodzący | Nie | Nie |
| **Usługa AzureKeyVault** | Azure Key Vault.<br/><br/>*Uwaga:* Ten tag ma zależność od tagu **AzureActiveDirectory.** | Wychodzący | Tak | Tak |
| **AzureLoadBalancer** | Moduł równoważenia obciążenia infrastruktury platformy Azure. Tag tłumaczy się na [wirtualny adres IP hosta](security-overview.md#azure-platform-considerations) (168.63.129.16), z którego pochodzą sondy kondycji platformy Azure. Nie obejmuje to ruchu do zasobu modułu równoważenia obciążenia platformy Azure. Jeśli nie używasz modułu równoważenia obciążenia platformy Azure, możesz zastąpić tę regułę. | Obie | Nie | Nie |
| **AzureMachineLearning** | Uczenie maszynowe platformy Azure. | Obie | Nie | Tak |
| **Usługa AzureMonitor** | Usługa Log Analytics, usługa Application Insights, AzMon i metryki niestandardowe (punkty końcowe GiG).<br/><br/>*Uwaga:* W przypadku usługi Log Analytics ten tag ma zależność od tagu **Magazyn.** | Wychodzący | Nie | Tak |
| **Zestawy danych AzureOpen** | Zestawy danych azure open.<br/><br/>*Uwaga:* Ten tag ma zależność od **tagu AzureFrontDoor.Frontend** i **Storage.** | Wychodzący | Nie | Nie |
| **Usługa AzurePlatformDNS** | Podstawowa infrastruktura (domyślna) usługa DNS.<br/><br>Za pomocą tego tagu można wyłączyć domyślny system DNS. Należy zachować ostrożność podczas korzystania z tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczących platformy Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Zaleca się również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **Usługi AzurePlatformIMDS** | Usługa metadanych wystąpienia platformy Azure (IMDS), która jest podstawową usługą infrastruktury.<br/><br/>Za pomocą tego tagu można wyłączyć domyślny imds. Należy zachować ostrożność podczas korzystania z tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczących platformy Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Zaleca się również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformLKM** | Licencjonowanie systemu Windows lub usługa zarządzania kluczami.<br/><br/>Za pomocą tego tagu można wyłączyć ustawienia domyślne dla licencjonowania. Należy zachować ostrożność podczas korzystania z tego tagu. Zalecamy zapoznanie się z [zagadnieniami dotyczących platformy Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)  Zaleca się również przeprowadzenie testów przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzureResourceManager** | Azure Resource Manager. | Wychodzący | Nie | Nie |
| **Usługa AzureSignalR** | Azure SignalR. | Wychodzący | Nie | Nie |
| **Usługa AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Uwaga:* Ten tag ma zależność od **azureactivedirectory**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** i **storage** tagów. | Wychodzący | Nie | Nie |
| **AzureTrafficManager** | Usługa Azure Traffic Manager sonduje adresy IP.<br/><br/>Aby uzyskać więcej informacji na temat adresów IP sondy usługi Traffic Manager, zobacz [Często zadawane pytania dotyczące usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Przychodzący | Nie | Tak |  
| **PartiaNodeZarządzanie** | Ruch zarządzania dla wdrożeń dedykowanych usłudze Azure Batch. | Obie | Nie | Tak |
| **CognitiveServicesManagement (Usługi poznawcze)** | Zakresy adresów dla ruchu dla usług Azure Cognitive Services. | Wychodzący | Nie | Nie |
| **DataFactory (DaneFactory)**  | Azure Data Factory | Obie | Nie | Nie |
| **DataFactoryManagement (DaneFactoryManagement)** | Ruch zarządzania dla usługi Azure Data Factory. | Wychodzący | Nie | Nie |
| **Dynamics365ForMarketingEmail** | Zakresy adresów dla marketingowej usługi poczty e-mail dynamics 365. | Wychodzący | Tak | Nie |
| **ElasticAFD (ElasticAFD)** | Elastyczne drzwi frontowe platformy Azure. | Obie | Nie | Nie |
| **EventHub** | Azure Event Hubs. | Wychodzący | Tak | Tak |
| **GatewayManager** | Ruch zarządzania dla wdrożeń dedykowanych bramie sieci VPN platformy Azure i bramie aplikacji. | Przychodzący | Nie | Nie |
| **GuestAndHybridManagement** | Usługa Azure Automation i konfiguracja gościa. | Wychodzący | Nie | Tak |
| **HDInsight** | Azure HDInsight. | Przychodzący | Tak | Nie |
| **Internet** | Przestrzeń adresowa IP, która znajduje się poza siecią wirtualną i jest osiągalna przez publiczny Internet.<br/><br/>Zakres adresów obejmuje [należącą do platformy Azure publiczną przestrzeń adresową IP.](https://www.microsoft.com/download/details.aspx?id=41653) | Obie | Nie | Nie |
| **Aplikacje Logiczne** | Aplikacje logiki. | Obie | Nie | Nie |
| **LogicAppsManagement (Logiczne aplikacje)** | Zarządzanie ruchem dla aplikacji logiki. | Przychodzący | Nie | Nie |
| **MicrosoftCloudAppSecurity** | Zabezpieczenia aplikacji w chmurze firmy Microsoft. | Wychodzący | Nie | Nie |
| **MicrosoftContainerRegistry** | Rejestr kontenerów dla obrazów kontenerów firmy Microsoft. <br/><br/>*Uwaga:* Ten tag ma zależność od **tagu AzureFrontDoor.FirstParty.** | Wychodzący | Tak | Tak |
| **Usługa PowerQueryOnline** | Power Query Online. | Obie | Nie | Nie |
| **ServiceBus** | Ruch usługi Azure Service Bus, który używa warstwy usługi Premium. | Wychodzący | Tak | Tak |
| **ServiceFabric (Usługodawca)** | Sieci szkieletowej usług Azure.<br/><br/>*Uwaga:* Ten tag reprezentuje punkt końcowy usługi sieci szkieletowej usług dla płaszczyzny sterowania na region. Dzięki temu klienci mogą wykonywać operacje zarządzania dla swoich klastrów sieci szkieletowej usług z sieci wirtualnej (punkt końcowy np. https:// westus.servicefabric.azure.com) | Obie | Nie | Nie |
| **Sql** | Usługa Azure SQL Database, usługa Azure Database for MySQL, usługa Azure Database for PostgreSQL i usługa Azure SQL Data Warehouse.<br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL. Ten tag nie ma zastosowania do wystąpienia zarządzanego SQL. | Wychodzący | Tak | Tak |
| **SqlManagement (Narzędzie sql)** | Ruch zarządzania dla wdrożeń dedykowanych SQL. | Obie | Nie | Tak |
| **Magazyn** | Azure Storage. <br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage. | Wychodzący | Tak | Tak |
| **Usługa storagesyncservice** | Usługa synchronizacji magazynu. | Obie | Nie | Nie |
| **WindowsVirtualDesktop** | Pulpit wirtualny systemu Windows. | Obie | Nie | Nie |
| **Sieć wirtualna** | Przestrzeń adresowa sieci wirtualnej (wszystkie zakresy adresów IP zdefiniowane dla sieci wirtualnej), wszystkie połączone lokalne przestrzenie adresowe, [sieci wirtualne,](virtual-network-peering-overview.md) sieci wirtualne połączone z [bramą sieci wirtualnej,](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json) [wirtualny adres IP hosta](security-overview.md#azure-platform-considerations)i prefiksy adresów używane na [trasach zdefiniowanych przez użytkownika.](virtual-networks-udr-overview.md) Ten tag może również zawierać trasy domyślne. | Obie | Nie | Nie |

>[!NOTE]
>W klasycznym modelu wdrażania (przed usługą Azure Resource Manager) obsługiwany jest podzbiór tagów wymienionych w poprzedniej tabeli. Te tagi są pisane inaczej:
>
>| Pisownia klasyczna | Tag Menedżera zasobów równoważnych |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Tagi usługi usług platformy Azure oznaczają prefiksy adresów z używanej chmury. Na przykład podstawowe zakresy adresów IP, które odpowiadają wartości tagu **Sql** w chmurze publicznej platformy Azure będą się różnić od podstawowych zakresów w chmurze Azure China.

> [!NOTE]
> W przypadku zaimplementowania [punktu końcowego usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md) dla usługi takiej jak usługa Azure Storage lub Azure SQL Database, platforma Azure dodaje [trasę](virtual-networks-udr-overview.md#optional-default-routes) do podsieci sieci wirtualnej dla usługi. Prefiksy adresu w trasie są takie same prefiksy adresu lub zakresy CIDR, jak te odpowiedniego tagu usługi.

## <a name="service-tags-on-premises"></a>Tagi serwisowe lokalnie  
Można uzyskać bieżący tag usługi i informacje o zakresie, które mają być uwzględnione jako część konfiguracji zapory lokalnej. Te informacje są bieżącą listą zakresów adresów IP odpowiadających każdemu tagowi usługi. Informacje można uzyskać programowo lub za pośrednictwem pliku JSON do pobrania, zgodnie z opisem w poniższych sekcjach.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Korzystanie z interfejsu API odnajdowania znaczników usługi (publiczna wersja zapoznawcza)
Można programowo pobrać bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP:

- [Reszta](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Podczas gdy jest w publicznej wersji zapoznawczej, interfejs API odnajdywania może zwracać informacje, które są mniej aktualne niż informacje zwracane przez pliki do pobrania JSON. (Zobacz następną sekcję).


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Odnajdowanie tagów usług przy użyciu plików JSON do pobrania 
Można pobrać pliki JSON, które zawierają bieżącą listę tagów usługi wraz ze szczegółami zakresu adresów IP. Listy te są aktualizowane i publikowane co tydzień. Lokalizacje dla każdej chmury to:

- [Publiczna platforma Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure rząd USA](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Platforma Azure w Chinach](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Podzbiór tych informacji został opublikowany w plikach XML dla [platformy Azure Public,](https://www.microsoft.com/download/details.aspx?id=41653) [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)i Azure [Germany](https://www.microsoft.com/download/details.aspx?id=54770). Te pliki do pobrania XML zostaną wycofane do 30 czerwca 2020 r. i nie będą już dostępne po tej dacie. Należy przeprowadzić migrację do przy użyciu interfejsu API odnajdywania lub plików JSON do pobrania zgodnie z opisem w poprzednich sekcjach.

### <a name="tips"></a>Porady 
- Można wykryć aktualizacje z jednej publikacji do następnej, od notując zwiększone *zmianyWartości numerów* w pliku JSON. Każda podsekcja (na przykład **Storage.WestUS**) ma własną *changeNumber,* która jest zwiększana w miarę zachodzących zmian. Najwyższy poziom *pliku changeNumber* jest zwiększany po zmianie któregokolwiek z podsekcji.
- Przykłady sposobu analizowania informacji o tagu usługi (na przykład pobierz wszystkie zakresy adresów dla magazynu w usłudze WestUS), zobacz dokumentację [interfejsu API powershell odnajdywania tagów usług.](https://aka.ms/discoveryapi_powershell)

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [utworzyć grupę zabezpieczeń sieci .](tutorial-filter-network-traffic.md)
