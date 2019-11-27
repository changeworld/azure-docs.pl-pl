---
title: Omówienie tagów usługi platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się więcej o tagach usługi. Tagi usług pomagają zminimalizować złożoność tworzenia reguł zabezpieczeń.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 33ee7351e547ee5ef57ef07f67ba6f5f4410b57f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384140"
---
# <a name="virtual-network-service-tags"></a>Tagi usługi sieci wirtualnej 
<a name="network-service-tags"></a>

Tag usługi reprezentuje grupę prefiksów adresów IP z danej usługi platformy Azure. Pomaga zminimalizować złożoność częstych aktualizacji w regułach zabezpieczeń sieci. Za pomocą tagów usługi można definiować kontrolę dostępu do sieci w [grupach zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) lub w [zaporze platformy Azure](https://docs.microsoft.com/azure/firewall/service-tags). Podczas tworzenia reguł zabezpieczeń można użyć tagów usługi zamiast konkretnych adresów IP. Określając nazwę tagu usługi (np. **ApiManagement**) w odpowiednim polu *źródłowym* lub *docelowym* reguły, można zezwolić na ruch dla odpowiedniej usługi lub go odrzucić. Firma Microsoft zarządza prefiksami adresów, które obejmują tag usługi, i automatycznie aktualizuje tag usługi jako adresy. 

## <a name="available-service-tags"></a>Dostępne Tagi usług
Poniższa tabela zawiera wszystkie znaczniki usługi dostępne do użycia w regułach [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Kolumny wskazują, czy tag jest:

- Odpowiednie dla reguł obejmujących ruch przychodzący lub wychodzący
- Obsługa zakresu [regionalnego](https://azure.microsoft.com/regions) 
- Możliwe do użycia w regułach [zapory platformy Azure](https://docs.microsoft.com/azure/firewall/service-tags)

Domyślnie Tagi usług odzwierciedlają zakresy dla całej chmury.  Niektóre Tagi usług umożliwiają również dokładniejsze sterowanie ziarnem, ograniczając odpowiednie zakresy adresów IP do określonego regionu.  Na przykład, chociaż **Magazyn** tagów usługi reprezentuje usługę Azure Storage dla całej chmury, **Magazyn. Zachodnie** zawęża, że tylko zakresy adresów IP magazynu z regionu zachodniego.  Opisy każdego z poniższych tagów usługi wskazują, czy obsługują one zakres regionalny.  



| Tag | Przeznaczenie | Może korzystać z ruchu przychodzącego lub wychodzącego? | Może być regionalna? | Czy można używać z zaporą platformy Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Ruch związany z zarządzaniem dla wdrożeń dedykowanych APIM. | Obie | Nie | Yes |
| **AppService**    | Usługa App Service. Ten tag jest zalecany w przypadku reguł zabezpieczeń wychodzących WebApp frontony. | Wychodzący | Yes | Yes |
| **AppServiceManagement** | Ruch związany z zarządzaniem App Service Environment dedykowane wdrożenia. | Obie | Nie | Yes |
| **Usługi azureactivedirectory** | Usługa Azure Active Directory. | Wychodzący | Nie | Yes |
| **AzureActiveDirectoryDomainServices** | Ruch związany z zarządzaniem Azure Active Directory Domain Services dedykowane wdrożenia. | Obie | Nie | Yes |
| **AzureBackup** |Usługa Azure Backup.<br/><br/>*Uwaga:* Ten tag ma zależność od tagów **Storage** i **usługi azureactivedirectory** . | Wychodzący | Nie | Yes |
| **AzureCloud** | Wszystkie [publiczne adresy IP centrum](https://www.microsoft.com/download/details.aspx?id=41653)danych. | Wychodzący | Yes | Yes |
| **AzureConnectors** | Logic Apps łączniki dla połączeń sondy/zaplecza. | Przychodzący | Yes | Yes |
| **AzureContainerRegistry** | Usługa Azure Container Registry. | Wychodzący | Yes | Yes |
| **AzureCosmosDB** | Usługa Azure Cosmos Database. | Wychodzący | Yes | Yes |
| **AzureDataLake** | Usługa Azure Data Lake. | Wychodzący | Nie | Yes |
| **AzureIoTHub** | Usługa IoT Hub platformy Azure. | Wychodzący | Nie | Nie |
| **AzureKeyVault** | Usługa magazynu kluczy platformy Azure.<br/><br/>*Uwaga:* Ten tag ma zależność od znacznika **usługi azureactivedirectory** . | Wychodzący | Yes | Yes |
| **AzureLoadBalancer** | Moduł równoważenia obciążenia infrastruktury platformy Azure. Ten znacznik przekłada się na [wirtualny adres IP hosta](security-overview.md#azure-platform-considerations) (168.63.129.16), z którego pochodzą sondy kondycji platformy Azure. Jeśli nie jest używana usługa Azure Load Balancer, tę zasadę można przesłonić. | Obie | Nie | Nie |
| **AzureMachineLearning** | Usługa Azure Machine Learning. | Wychodzący | Nie | Yes |
| **AzureMonitor** | Log Analytics, App Insights, AzMon i metryki niestandardowe (rdzeniowym procesorem Endpoints).<br/><br/>*Uwaga:* W przypadku Log Analytics ten tag ma zależność od tagu **magazynu** . | Wychodzący | Nie | Yes |
| **AzurePlatformDNS** | Podstawowa infrastruktura (domyślna) usługi DNS.<br/><br>Za pomocą tego tagu można wyłączyć domyślny system DNS. Należy zachować ostrożność przy użyciu tego tagu. Zalecane jest zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Testowanie jest zalecane przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformIMDS** | IMDS, czyli podstawowa usługa infrastruktury.<br/><br/>Ten tag służy do wyłączania domyślnego IMDS.  Należy zachować ostrożność przy użyciu tego tagu. Zalecane jest zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Testowanie jest zalecane przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzurePlatformLKM** | Licencjonowanie systemu Windows lub usługa zarządzania kluczami.<br/><br/>Ten tag służy do wyłączania ustawień domyślnych dla licencjonowania. Należy zachować ostrożność przy użyciu tego tagu.  Zalecane jest zapoznanie się z [zagadnieniami dotyczącymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Testowanie jest zalecane przed użyciem tego tagu. | Wychodzący | Nie | Nie |
| **AzureTrafficManaged** | Adresy IP badania Traffic Manager platformy Azure.<br/><br/>Więcej informacji na temat adresów IP sondy usługi Traffic Manager można znaleźć w temacie [Usługa Azure Traffic Manager — często zadawane pytania](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Przychodzący | Nie | Yes |  
| **BatchNodeManagement** | Ruch związany z zarządzaniem Azure Batch dedykowane wdrożenia. | Obie | Nie | Yes |
| **CognitiveServicesManagement** | Zakresy adresów dla ruchu dla Cognitive Services | Wychodzący | Nie | Nie |
| **Dynamics365ForMarketingEmail** | Zakresy adresów dla usługi marketingowej poczty programu Dynamics 365. | Wychodzący | Yes | Nie |
| **EventHub** | Usługa EventHub platformy Azure. | Wychodzący | Yes | Yes |
| **Bramamanager** | Ruch związany z zarządzaniem na potrzeby dedykowanych wdrożeń bram sieci VPN/aplikacji. | Przychodzący | Nie | Nie |
| **Internet** | Przestrzeń adresów IP, która znajduje się poza siecią wirtualną i jest dostępna przez publiczny Internet.<br/><br/>Ten zakres adresów obejmuje [publiczną przestrzeń adresów IP należącą do platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Obie | Nie | Nie |
| **MicrosoftContainerRegistry** | Usługa Container Registry firmy Microsoft. | Wychodzący | Yes | Yes |
| **ServiceBus** | Usługa Azure Service Bus przy użyciu warstwy usługi Premium. | Wychodzący | Yes | Yes |
| **ServiceFabric** | Usługa Service Fabric. | Wychodzący | Nie | Nie |
| **Server** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL i Azure SQL Data Warehouse usługi.<br/><br/>*Uwaga:* Ten tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure SQL Database, ale nie konkretną bazę danych lub serwer SQL. | Wychodzący | Yes | Yes |
| **Xmlmanagement** | Ruch związany z zarządzaniem dedykowanymi wdrożeniami programu SQL Server. | Obie | Nie | Yes |
| **Storage** | Usługa Azure Storage. <br/><br/>*Uwaga:* Tag reprezentuje usługę, ale nie konkretne wystąpienia usługi. Na przykład tag reprezentuje usługę Azure Storage, ale nie konkretne konto usługi Azure Storage. | Wychodzący | Yes | Yes |
| **VirtualNetwork** | Przestrzeń adresowa sieci wirtualnej (wszystkie zakresy adresów IP zdefiniowane dla sieci wirtualnej), wszystkie połączone lokalne przestrzenie adresowe, [równorzędne](virtual-network-peering-overview.md) sieci wirtualne lub Sieć wirtualna połączona z [bramą sieci wirtualnej](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), [wirtualny adres IP adres hosta](security-overview.md#azure-platform-considerations) i prefiksy adresów używane w przypadku [tras zdefiniowanych przez użytkownika](virtual-networks-udr-overview.md). Należy pamiętać, że ten tag może również zawierać trasy domyślne. | Obie | Nie | Nie |

>[!NOTE]
>Podczas pracy w środowisku *klasycznym* (pre-Azure Resource Manager) jest obsługiwany zestaw powyższych tagów.  Używają one alternatywnej pisowni:

| Pisownia klasyczna | Odpowiednik tagu Menedżer zasobów |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Tagi usług platformy Azure określają prefiksy adresów określonych chmur, które są używane. na przykład zakresy źródłowych adresów IP odpowiadające wartości tagów **SQL** będą różnić się między chmurą publiczną platformy Azure a chmurą platformy Azure w Chinach.

> [!NOTE]
> W przypadku zaimplementowania [punktu końcowego usługi dla sieci wirtualnej](virtual-network-service-endpoints-overview.md) dla usługi takiej jak usługa Azure Storage lub Azure SQL Database, platforma Azure dodaje [trasę](virtual-networks-udr-overview.md#optional-default-routes) do podsieci sieci wirtualnej dla usługi. Prefiksy adresów dla trasy to te same prefiksy adresów lub zakresy CIDR, co w odpowiednim tagu usługi.



## <a name="service-tags-in-on-premises"></a>Tagi usług w środowisku lokalnym  
Możesz uzyskać informacje o bieżącym znaczniku i zakresie usługi, aby uwzględnić je w ramach konfiguracji zapory lokalnej.  Te informacje są bieżącą listą punktów w czasie dla zakresów adresów IP odpowiadających każdemu tagowi usługi.  Informacje można uzyskać programowo lub za pomocą pliku JSON, jak pokazano poniżej.

### <a name="service-tag-discovery-api-public-preview"></a>Interfejs API odnajdywania tagów usługi (publiczna wersja zapoznawcza)
Można programowo pobrać bieżącą listę tagów usługi z informacjami o zakresie adresów IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> W publicznej wersji zapoznawczej interfejs API odnajdowania może zwracać informacje, które nie są aktualne jako pliki JSON (poniżej).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Odnajdywanie tagów usługi przy użyciu plików JSON do pobrania 
Pliki JSON zawierające bieżącą listę tagów usługi można pobrać z informacjami o zakresie adresów IP. Są one aktualizowane i publikowane co tydzień.  Lokalizacje dla każdej chmury są następujące:

- [Usługa Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Chińska wersja platformy Azure](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure (Niemcy)](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Podzbiór tych informacji został wcześniej opublikowany w plikach XML dla usług [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Chiny](https://www.microsoft.com/download/details.aspx?id=42064) i [Azure (Niemcy](https://www.microsoft.com/download/details.aspx?id=54770)). Te pobrania XML będą przestarzałe do 30 czerwca 2020 i nie będą już dostępne po tej dacie. Należy przeprowadzić migrację do programu przy użyciu interfejsu API odnajdywania lub plików JSON, jak opisano powyżej.

### <a name="tips"></a>Porady 
- Możesz wykrywać aktualizacje z jednego publikowania na następne przy użyciu zwiększonych wartości *changeNumber* w pliku JSON. Każda podsekcja (na przykład **Storage. zachodni**) ma własne *changeNumber* , które są zwiększane, gdy wystąpią zmiany.  Najwyższy poziom *changeNumber* pliku jest zwiększany, gdy dowolna z podsekcji została zmieniona.
- Przykłady sposobu analizowania informacji z tagu usługi (np. pobrania wszystkich zakresów adresów dla magazynu w zachodniej) można znaleźć w dokumentacji [programu PowerShell dla interfejsu API odnajdywania tagów usług](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Następne kroki
- Dowiedz się, jak [utworzyć sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md).

