---
title: Dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej | Microsoft Docs
description: Dowiedz się, jak dołączać środowisko Azure-SSIS Integration Runtime do sieci wirtualnej platformy Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3a1e272fa332c0bf0ee4e5ececa3edd83aec1d46
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543134"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej
Korzystając z SQL Server Integration Services (SSIS) w Azure Data Factory (ADF), należy dołączyć do usługi Azure-SSIS Integration Runtime (IR) do sieci wirtualnej platformy Azure w następujących scenariuszach: 

- Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS uruchomionych na platformie Azure-SSIS IR bez konfigurowania i zarządzania własnym hostowanym programem IR jako serwer proxy. 

- Chcesz połączyć się z zasobami usługi platformy Azure obsługiwanymi przez punkty końcowe usługi sieci wirtualnej z pakietów SSIS uruchomionych na platformie Azure-SSIS IR.

- Zarządzasz bazą danych wykazu usług SSIS (SSISDB) w Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym w sieci wirtualnej. 

ADF umożliwia dołączenie środowiska Azure-SSIS IR do sieci wirtualnej utworzonej za pomocą klasycznego modelu wdrażania lub Azure Resource Managergo modelu wdrażania. 

> [!IMPORTANT]
> Klasyczna Sieć wirtualna jest obecnie przestarzała, dlatego użyj Azure Resource Manager sieci wirtualnej.  Jeśli używasz już klasycznej sieci wirtualnej, użyj Azure Resource Manager sieci wirtualnej najszybciej, jak to możliwe.

## <a name="access-to-on-premises-data-stores"></a>Dostęp do lokalnych magazynów danych
Jeśli pakiety SSIS mają dostęp do lokalnych magazynów danych, możesz dołączyć do środowiska Azure-SSIS IR do sieci wirtualnej, która jest połączona z siecią lokalną, lub skonfigurować i zarządzać własnym obsługiwanym programem IR jako serwerem proxy dla środowiska Azure-SSIS IR, zobacz [Konfigurowanie własnego środowiska IR jako serwer proxy dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) . W przypadku dołączania środowiska Azure-SSIS IR do sieci wirtualnej istnieje kilka istotnych kwestii, na które należy zwrócić uwagę: 

- Jeśli nie istnieje żadna Sieć wirtualna połączona z siecią lokalną, najpierw Utwórz [sieć wirtualną Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) lub [klasyczną sieć wirtualną](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla środowiska Azure-SSIS Integration Runtime, aby dołączyć. Następnie skonfiguruj [połączenie bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) typu lokacja-lokacja lub połączenie [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) z tej sieci wirtualnej z siecią lokalną. 

- Jeśli istnieje istniejąca Azure Resource Manager lub klasyczna Sieć wirtualna podłączona do sieci lokalnej w tej samej lokalizacji co środowisko IR Azure-SSIS, możesz dołączyć do tej sieci wirtualnej. 

- Jeśli istnieje klasyczna Sieć wirtualna połączona z siecią lokalną w innej lokalizacji z poziomu środowiska Azure-SSIS IR, możesz najpierw utworzyć [klasyczną sieć wirtualną](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla środowiska Azure-SSIS IR, aby dołączyć. Następnie skonfiguruj [klasyczne i klasyczne połączenie z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) . Możesz też utworzyć [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla środowiska Azure-SSIS Integration Runtime, aby dołączyć. Następnie skonfiguruj połączenie z [siecią wirtualną w trybie klasycznym Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Jeśli istnieje Azure Resource Manager sieci wirtualnej podłączonej do sieci lokalnej w innej lokalizacji z poziomu środowiska Azure-SSIS IR, można najpierw utworzyć [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md##create-a-virtual-network) dla środowiska Azure-SSIS IR do przyłączenia. Następnie skonfiguruj połączenie sieci wirtualnej Azure Resource Manager Azure Resource Manager. Możesz też utworzyć [klasyczną sieć wirtualną](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla środowiska Azure-SSIS IR do przyłączenia. Następnie skonfiguruj połączenie z [siecią wirtualną w trybie klasycznym Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 

## <a name="access-to-azure-services-with-virtual-network-service-endpoints"></a>Dostęp do usług platformy Azure za pomocą punktów końcowych usługi sieci wirtualnej
Jeśli pakiety SSIS mają dostęp do zasobów usługi platformy Azure obsługiwanych przez [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) i chcesz zabezpieczyć te zasoby na platformie Azure-SSIS IR, możesz dołączyć do środowiska Azure-SSIS IR do podsieci sieci wirtualnej skonfigurowanej za pomocą sieci wirtualnej punkt końcowy usługi, a następnie Dodaj regułę sieci wirtualnej do zasobu usługi platformy Azure, aby umożliwić dostęp z tej samej podsieci.

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Hostowanie bazy danych wykazu usług SSIS w Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym
Jeśli katalog SSIS jest hostowany w Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej, upewnij się, że dołączysz środowisko Azure-SSIS IR do tej samej sieci wirtualnej i podsieci.

Jeśli dołączysz środowisko Azure-SSIS IR do tej samej sieci wirtualnej co wystąpienie zarządzane, upewnij się, że środowisko Azure-SSIS IR znajduje się w innej podsieci niż wystąpienie zarządzane. Jeśli dołączysz środowisko Azure-SSIS IR do innej sieci wirtualnej niż wystąpienie zarządzane, zalecamy komunikację równorzędną sieci wirtualnej (która jest ograniczona do tego samego regionu) lub sieć wirtualną z połączeniem sieci wirtualnej. Zobacz [łączenie aplikacji z Azure SQL Database wystąpieniem zarządzanym](../sql-database/sql-database-managed-instance-connect-app.md).

We wszystkich przypadkach sieć wirtualna można wdrożyć tylko za pomocą Azure Resource Managergo modelu wdrażania.

Poniższe sekcje zawierają więcej szczegółów. 

## <a name="requirements-for-virtual-network-configuration"></a>Wymagania dotyczące konfiguracji sieci wirtualnej
-   Upewnij się, `Microsoft.Batch` że jest zarejestrowanym dostawcą w ramach subskrypcji podsieci sieci wirtualnej, która obsługuje środowisko Azure-SSIS IR. Jeśli używasz klasycznej sieci wirtualnej, Dołącz `MicrosoftAzureBatch` także do roli współautor klasycznej maszyny wirtualnej dla tej sieci wirtualnej. 

-   Upewnij się, że masz wymagane uprawnienia. Zobacz [wymagane uprawnienia](#perms).

-   Wybierz odpowiednią podsieć, aby hostować środowisko Azure-SSIS IR. Zobacz [wybierz podsieć](#subnet). 

-   Jeśli używasz własnego serwera usługi nazw domen (DNS) w sieci wirtualnej, zobacz [serwer usług nazw domen](#dns_server). 

-   Jeśli w podsieci jest używana sieciowa Grupa zabezpieczeń (sieciowej grupy zabezpieczeń), zobacz [Network Security Group](#nsg) 

-   Jeśli używasz usługi Azure Express Route lub konfigurowania trasy zdefiniowanej przez użytkownika (UDR), zobacz [Korzystanie z usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika](#route). 

-   Upewnij się, że grupa zasobów sieci wirtualnej umożliwia tworzenie i usuwanie niektórych zasobów sieci platformy Azure. Zobacz [wymagania dotyczące grupy zasobów](#resource-group). 

-   Jeśli dostosowujesz środowisko Azure-SSIS IR zgodnie z opisem w artykule Konfiguracja niestandardowa środowiska [Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) , węzły podczerwieni platformy Azure-SSIS zostaną przydzieleni do prywatnych adresów IP ze wstępnie zdefiniowanego zakresu 172.16.0.0-172.31.255.255, dlatego należy się upewnić, że prywatny adres IP zakresy adresów sieci wirtualnych/lokalnych nie kolidują z tym zakresem.

Oto diagram przedstawiający wymagane połączenia dla środowiska Azure-SSIS IR:

![Środowisko IR Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Wymagane uprawnienia

Użytkownik, który tworzy Integration Runtime Azure-SSIS, musi mieć następujące uprawnienia:

- W przypadku dołączania środowiska IR do programu SSIS do sieci wirtualnej Azure Resource Manager dostępne są dwie opcje:

  - Użyj wbudowanej roli *współautor sieci* . Ta rola jest dostarczana z _firmą Microsoft.\* Network/_ uprawnieniem o znacznie większym zakresie niż jest to konieczne.

  - Utwórz rolę niestandardową, która zawiera tylko wymagane uprawnienie _Microsoft. Network/\*virtualNetworks//Join/Action_ . 

- Jeśli dołączysz środowisko IR usług SSIS do klasycznej sieci wirtualnej, zalecamy użycie wbudowanej roli *współautor klasycznej maszyny wirtualnej* . W przeciwnym razie musisz zdefiniować rolę niestandardową, która zawiera uprawnienie do przyłączenia do sieci wirtualnej.

### <a name="subnet"></a>Wybierz podsieć
-   Nie wybieraj GatewaySubnet na potrzeby wdrażania Integration Runtime platformy Azure — SSIS, ponieważ jest ona przeznaczona dla bram sieci wirtualnej. 

-   Upewnij się, że wybrana podsieć ma wystarczającą ilość dostępnej przestrzeni adresowej dla środowiska Azure-SSIS IR do użycia. Pozostaw co najmniej 2 * numer węzła IR w dostępnych adresach IP. Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach i nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów oraz trzy kolejne adresy używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Nie należy używać podsieci, która jest przeznaczona wyłącznie dla innych usług platformy Azure (na przykład SQL Database wystąpienia zarządzanego, App Service itp.). 

### <a name="dns_server"></a>Serwer usług nazw domen 
Jeśli musisz użyć własnego serwera usługi nazw domen (DNS) w sieci wirtualnej przyłączonej do środowiska Azure-SSIS Integration Runtime, upewnij się, że może on rozpoznać globalne nazwy hostów platformy Azure (na przykład nazwę `<your storage account>.blob.core.windows.net`obiektu BLOB usługi Azure Storage). 

Zalecane są następujące kroki: 

-   Skonfiguruj niestandardowe DNS do przesyłania żądań do Azure DNS. Nierozpoznane rekordy DNS można przesłać dalej na adres IP tłumaczeń rekurencyjnych platformy Azure (168.63.129.16) na własnym serwerze DNS. 

-   Skonfiguruj niestandardowy serwer DNS jako podstawowy i Azure DNS jako pomocniczy dla sieci wirtualnej. Zarejestruj adres IP tłumaczeń cyklicznych platformy Azure (168.63.129.16) jako pomocniczy serwer DNS w przypadku niedostępności własnego serwera DNS. 

Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw używające własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Sieciowa Grupa zabezpieczeń
Jeśli musisz zaimplementować sieciową grupę zabezpieczeń (sieciowej grupy zabezpieczeń) dla podsieci używanej przez środowisko Azure-SSIS Integration Runtime, Zezwól na ruch przychodzący/wychodzący przez następujące porty: 

| Direction | Protokół transportowy | Source | Zakres portów źródłowych | Miejsce docelowe | Zakres portów docelowych | Komentarze |
|---|---|---|---|---|---|---|
| Przychodzący | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (w przypadku dołączenia środowiska IR do sieci wirtualnej Azure Resource Manager) <br/><br/>10100, 20100, 30100 (Jeśli dołączysz środowisko IR do klasycznej sieci wirtualnej)| Usługa Data Factory używa tych portów do komunikowania się z węzłami środowiska Azure-SSIS Integration Runtime w sieci wirtualnej. <br/><br/> Niezależnie od tego, czy tworzysz sieciowej grupy zabezpieczeń na poziomie podsieci, Data Factory zawsze konfiguruje sieciowej grupy zabezpieczeń na poziomie kart sieciowych podłączonych do maszyn wirtualnych, które obsługują środowisko Azure-SSIS IR. Tylko ruch przychodzący z Data Factory adresów IP na określonych portach jest dozwolony przez ten sieciowej grupy zabezpieczeń na poziomie karty sieciowej. Nawet w przypadku otwarcia tych portów w ruchu internetowym na poziomie podsieci ruch z adresów IP, które nie są Data Factory adresy IP, jest blokowany na poziomie karty sieciowej. |
| Wychodzące | TCP | VirtualNetwork | * | AzureCloud | 443 | Węzły środowiska Azure-SSIS Integration Runtime w sieci wirtualnej używają tego portu do uzyskiwania dostępu do usług platformy Azure, takich jak Azure Storage i Azure Event Hubs. |
| Wychodzące | TCP | VirtualNetwork | * | Internet | 80 | Węzły środowiska Azure-SSIS Integration Runtime w sieci wirtualnej używają tego portu do pobierania listy odwołania certyfikatów z Internetu. |
| Wychodzące | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Węzły środowiska Azure-SSIS Integration Runtime w sieci wirtualnej używają tych portów do uzyskiwania dostępu do SSISDB hostowanych przez serwer Azure SQL Database. Jeśli zasada połączenia serwera Azure SQL Database jest ustawiona na **serwer proxy** zamiast przekierowania, wymagany jest tylko port 1433. Ta reguła zabezpieczeń ruchu wychodzącego nie ma zastosowania do SSISDB hostowanego przez wystąpienie zarządzane w sieci wirtualnej. |
||||||||

### <a name="route"></a>Korzystanie z usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika
Po podłączeniu obwodu [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) do infrastruktury sieci wirtualnej w celu zwiększenia sieci lokalnej na platformę Azure typową konfiguracją jest użycie wymuszonego tunelowania (anonsowanie trasy BGP, 0.0.0.0/0, do sieci wirtualnej), która wymusza wychodzący ruch internetowy z przepływu sieci wirtualnej do lokalnego urządzenia sieciowego na potrzeby inspekcji i rejestrowania. 
 
Można też zdefiniować [trasy zdefiniowane przez użytkownika (UDR)](../virtual-network/virtual-networks-udr-overview.md) , aby wymusić ruch wychodzący z podsieci, która hostuje środowisko Azure-SSIS IR w innej podsieci, która hostuje urządzenie Virtual Network (urządzenie WUS) jako zaporę lub zaporę platformy Azure na potrzeby inspekcji i rejestrowania.
 
W obu przypadkach trasa ruchu będzie przerwać wymaganą łączność przychodzącą z usług Azure Data Factory zależnych (Azure Batch Management Services) do środowiska Azure-SSIS IR w sieci wirtualnej. 
 
Rozwiązanie polega na zdefiniowaniu co najmniej jednej trasy zdefiniowanej przez użytkownika (UDR) w podsieci zawierającej środowisko Azure-SSIS IR. 

- Można zastosować trasę 0.0.0.0/0 z typem następnego przeskoku jako **Internet** w podsieci, która hostuje środowisko Azure-SSIS IR w scenariuszu usługi Azure ExpressRoute, lub zmodyfikować istniejącą trasę 0.0.0.0/0 z typu następnego przeskoku jako **urządzenie wirtualne** do **Internetu** w usłudze urządzenie WUS scenariusz.

![Dodawanie trasy](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)
- Jeśli obawiasz się, że utracisz możliwość sprawdzenia wychodzącego ruchu internetowego z tej podsieci. Można zdefiniować konkretne UDR, aby kierować ruchem między usługami zarządzania Azure Batch i Azure-SSIS IR z typem następnego przeskoku jako **Internet**.
Na przykład Jeśli Twoje środowisko Azure-SSIS IR znajduje się `UK South`w lokalizacji, należy uzyskać listę `BatchNodeManagement.UKSouth` zakresów adresów IP z zakresu adresów [IP znaczników usługi](https://www.microsoft.com/en-us/download/details.aspx?id=56519) lub za pośrednictwem [interfejsu API odnajdywania tagów](https://aka.ms/discoveryapi)usług. Następnie Zastosuj poniżej UDR powiązanej trasy zakresu adresów IP z typem następnego przeskoku jako **Internet**.

![Ustawienia UDR AzureBatch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)
> [!NOTE]
> Istnieje dodatkowy koszt konserwacji tego podejścia, które należy regularnie sprawdzać zakres adresów IP i dodawać nowy zakres adresów IP do UDR, aby uniknąć przerwania działania środowiska Azure-SSIS IR. Gdy nowy adres IP pojawia się w tagu usługi, pozostanie on w innym miesiącu, aby nowy adres IP zaczęł obowiązywać. Dlatego zalecamy sprawdzenie zakresu adresów IP miesięcznie. 

### <a name="resource-group"></a>Wymagania dotyczące grupy zasobów
-   Środowisko Azure-SSIS IR musi utworzyć określone zasoby sieciowe w ramach tej samej grupy zasobów co sieć wirtualna. Te zasoby obejmują następujące elementy:
    -   Moduł równoważenia obciążenia platformy Azure o nazwie  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
    -   Publiczny adres IP platformy Azure o nazwie  *\<GUID >-azurebatch-cloudservicepublicip*.
    -   Grupa zabezpieczeń sieć służbowa o nazwie  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

    Te zasoby zostaną utworzone po uruchomieniu i usunięciu podczerwieni po zatrzymaniu podczerwieni. Nie używaj ponownie tych zasobów w innych zasobach, w przeciwnym razie Zablokuj zatrzymanie podczerwieni. 

-   Upewnij się, że nie masz blokady zasobów w grupie zasobów lub subskrypcji, do której należy Sieć wirtualna. W przypadku skonfigurowania blokady tylko do odczytu lub blokady usuwania uruchamianie i zatrzymywanie środowiska IR może zakończyć się niepowodzeniem lub przestać reagować. 

-   Upewnij się, że nie masz zasad platformy Azure, które uniemożliwiają utworzenie następujących zasobów w grupie zasobów lub subskrypcji, do której należy Sieć wirtualna: 
    -   Microsoft. Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>FAQ

- Jak chronimy publiczny adres IP uwidoczniony na platformie Azure-SSIS IR na potrzeby połączenia przychodzącego? Czy można usunąć publiczny adres IP?
 
    Teraz publiczny adres IP zostanie automatycznie utworzony podczas łączenia z siecią wirtualną Azure-SSIS IR. Mamy sieciowej grupy zabezpieczeń na poziomie kart sieciowych, aby zezwalać tylko na Azure Batch usług zarządzania na łączenie przychodzące z platformą Azure-SSIS IR i można określić poziom podsieci sieciowej grupy zabezpieczeń dla ochrony dla ruchu przychodzącego.

    Jeśli nie chcesz ujawniać publicznego adresu IP, możesz rozważyć podejście konfigurowania samodzielnego środowiska [IR jako serwera proxy dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) zamiast sieci wirtualnej, jeśli są one stosowane w danym scenariuszu.
 
- Czy istnieje statyczny adres IP platformy Azure-SSIS IR, który może zostać umieszczony w obszarze Zezwalaj na dostęp do źródła danych przez zaporę?
 
    - Jeśli źródło danych jest lokalne, po połączeniu sieci wirtualnej z siecią lokalną i dołączeniu środowiska Azure-SSIS IR do tej podsieci sieci wirtualnej można umieścić zakres adresów IP tej podsieci na liście dozwolonych.
    - Jeśli źródło danych jest obsługiwane przez usługę platformy Azure dla punktu końcowego usługi sieci wirtualnej, w sieci wirtualnej można nawiązać konfigurację punktu usługi sieci wirtualnej i dołączyć do niego środowisko Azure-SSIS IR do tej podsieci sieci wirtualnej, a następnie będzie można użyć reguły sieci wirtualnej usług platformy Azure, a nie z zakresu adresów IP, aby zezwolić na dostęp.
    - Jeśli źródło danych jest innym źródłem danych w chmurze, możesz użyć UDR do kierowania ruchu wychodzącego z usługi Azure-SSIS IR do urządzenie WUS lub zapory platformy Azure ze statycznym publicznym adresem IP, aby można było umieścić ten publiczny adres IP urządzenie WUS lub zapory platformy Azure na liście dozwolonych.
    - Jeśli żaden z powyższych elementów nie spełnia wymagań użytkownika, można sprawdzić, czy dostęp do źródła danych można przeprowadzić przez skonfigurowanie samodzielnego środowiska [IR jako serwera proxy dla środowiska Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis), a następnie można umieścić adres IP komputera, na którym jest hostowane środowisko IR hosta samoobsługowego, zamiast Przyłączanie środowiska Azure-SSIS IR do sieci wirtualnej.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory interfejsie użytkownika)
W tej sekcji przedstawiono sposób przyłączania istniejącego środowiska uruchomieniowego Azure-SSIS do sieci wirtualnej (klasycznej lub Azure Resource Manager) za pomocą interfejsu użytkownika Azure Portal i Data Factory. Najpierw należy odpowiednio skonfigurować sieć wirtualną przed dołączeniem do niej środowiska Azure-SSIS IR. Przejdź do jednej z dwóch następnych sekcji w oparciu o typ sieci wirtualnej (klasyczny lub Azure Resource Manager). Następnie przejdź do trzeciej sekcji, aby dołączyć środowisko Azure-SSIS IR do sieci wirtualnej. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Konfigurowanie Azure Resource Manager sieci wirtualnej przy użyciu portalu
Aby można było dołączyć do niego środowisko Azure-SSIS IR, należy skonfigurować sieć wirtualną. 

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w tych przeglądarkach sieci Web. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz pozycję **więcej usług**. Odfiltruj i wybierz **sieci wirtualne**. 

1. Odfiltruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna** wybierz pozycję **Właściwości**. 

1. Wybierz przycisk Kopiuj dla **identyfikatora zasobu** , aby skopiować identyfikator zasobu dla sieci wirtualnej do Schowka. Zapisz identyfikator ze schowka w programie OneNote lub pliku. 

1. W menu po lewej stronie wybierz pozycję **podsieci** . Upewnij się, że liczba **dostępnych adresów** jest większa niż węzły w środowisku Azure-SSIS Integration Runtime. 

1. Sprawdź, czy dostawca Azure Batch jest zarejestrowany w subskrypcji platformy Azure z siecią wirtualną. Lub Zarejestruj dostawcę Azure Batch. Jeśli masz już konto Azure Batch w subskrypcji, Twoja subskrypcja jest zarejestrowana dla Azure Batch. (Jeśli utworzysz środowisko Azure-SSIS IR w portalu Data Factory, dostawca Azure Batch zostanie automatycznie zarejestrowany dla Ciebie). 

   a. W obszarze Azure Portal wybierz pozycję **subskrypcje** w menu po lewej stronie. 

   b. Wybierz subskrypcję. 

   c. Wybierz pozycję **dostawcy zasobów** po lewej stronie, a następnie upewnij się, że **Microsoft. Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie stanu zarejestrowanego](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli na liście nie widzisz elementu **Microsoft. Batch** , aby go zarejestrować, [utwórz puste konto Azure Batch](../batch/batch-account-create-portal.md) w ramach subskrypcji. Można go później usunąć. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Konfigurowanie klasycznej sieci wirtualnej przy użyciu portalu
Aby można było dołączyć do niego środowisko Azure-SSIS IR, należy skonfigurować sieć wirtualną. 

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w tych przeglądarkach sieci Web. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz pozycję **więcej usług**. Filtruj i wybieraj **sieci wirtualne (klasyczne)** . 

1. Odfiltruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna (klasyczna)** wybierz pozycję **Właściwości**. 

   ![Identyfikator zasobu klasycznej sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Wybierz przycisk Kopiuj dla **identyfikatora zasobu** , aby skopiować identyfikator zasobu dla sieci klasycznej do Schowka. Zapisz identyfikator ze schowka w programie OneNote lub pliku. 

1. W menu po lewej stronie wybierz pozycję **podsieci** . Upewnij się, że liczba **dostępnych adresów** jest większa niż węzły w środowisku Azure-SSIS Integration Runtime. 

   ![Liczba dostępnych adresów w sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Dołącz **MicrosoftAzureBatch** do roli **współautora klasycznej maszyny wirtualnej** dla sieci wirtualnej. 

    a. Wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie, a następnie wybierz kartę **przypisania ról** . 

    ![Przyciski "kontrola dostępu" i "Dodaj"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Wybierz **Dodaj przypisanie roli**.

    c. Na stronie **Dodawanie przypisania roli** wybierz opcję **współautor klasycznej maszyny wirtualnej** dla **roli**. Wklej **ddbf3205-c6bd-46ae-8127-60eb93363864** w polu **Wybierz** , a następnie wybierz pozycję **Microsoft Azure Batch** z listy wyników wyszukiwania. 

    ![Wyniki wyszukiwania na stronie "Dodawanie przypisania roli"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Wybierz pozycję **Zapisz** , aby zapisać ustawienia i zamknąć stronę. 

    ![Zapisz ustawienia dostępu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Upewnij się, że na liście współautorów jest widoczny **Microsoft Azure Batch** . 

    ![Potwierdź dostęp Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Sprawdź, czy dostawca Azure Batch jest zarejestrowany w subskrypcji platformy Azure z siecią wirtualną. Lub Zarejestruj dostawcę Azure Batch. Jeśli masz już konto Azure Batch w subskrypcji, Twoja subskrypcja jest zarejestrowana dla Azure Batch. (Jeśli utworzysz środowisko Azure-SSIS IR w portalu Data Factory, dostawca Azure Batch zostanie automatycznie zarejestrowany dla Ciebie). 

   a. W obszarze Azure Portal wybierz pozycję **subskrypcje** w menu po lewej stronie. 

   b. Wybierz subskrypcję. 

   c. Wybierz pozycję **dostawcy zasobów** po lewej stronie, a następnie upewnij się, że **Microsoft. Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie stanu zarejestrowanego](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli na liście nie widzisz elementu **Microsoft. Batch** , aby go zarejestrować, [utwórz puste konto Azure Batch](../batch/batch-account-create-portal.md) w ramach subskrypcji. Można go później usunąć. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołączanie środowiska Azure-SSIS IR do sieci wirtualnej
1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie interfejs użytkownika Data Factory jest obsługiwany tylko w tych przeglądarkach sieci Web. 

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **fabryki danych** w menu po lewej stronie. Jeśli nie widzisz **fabryk danych** w menu, wybierz pozycję **więcej usług**, a następnie wybierz pozycję **fabryki danych** w sekcji **Analiza i analiza** . 

   ![Lista fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wybierz fabrykę danych z platformą Azure-SSIS Integration Runtime na liście. Zostanie wyświetlona strona główna fabryki danych. Wybierz kafelek **tworzenie & Wdróż** . Na oddzielnej karcie zobaczysz interfejs użytkownika Data Factory. 

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. W interfejsie użytkownika Data Factory przejdź do karty **Edycja** , wybierz pozycję **połączenia**i przejdź do karty **środowisko Integration Runtime** . 

   ![Karta "Integration Runtimes"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Jeśli działa środowisko Azure-SSIS IR, na liście działania w programie Integration Runtime wybierz przycisk **Zatrzymaj** w kolumnie **Akcje** dla środowiska Azure-SSIS IR. Nie można edytować środowiska IR, dopóki nie zostanie ono zatrzymane. 

   ![Zatrzymaj środowisko IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na liście środowisko uruchomieniowe Integration wybierz przycisk **Edytuj** w kolumnie **Akcje** dla środowiska Azure-SSIS IR. 

   ![Edytuj środowisko Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. W panelu **konfiguracja Integration Runtime** przejdź do strony **Ustawienia ogólne** i **Ustawienia SQL** , klikając przycisk **dalej** . 

1. Na stronie **Ustawienia zaawansowane** wykonaj następujące czynności: 

   a. Zaznacz pole wyboru **Wybierz sieć wirtualną...** . 

   b. W obszarze **subskrypcja**wybierz subskrypcję platformy Azure, w której możesz wybrać istniejącą sieć wirtualną. 
  
   c. W obszarze **Nazwa**sieci wirtualnej wybierz sieć wirtualną. 

   d. W polu **Nazwa podsieci**wybierz podsieć w sieci wirtualnej. 

   e. Jeśli chcesz również skonfigurować własne środowisko IR jako serwer proxy dla środowiska Azure-SSIS IR i zarządzać nim, zaznacz pole wyboru Konfiguruj samoobsługowe **...** , a także zobacz sekcję [Konfigurowanie własnego środowiska IR jako serwera proxy dla systemu Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) .

   f. Kliknij przycisk **Walidacja sieci wirtualnej** , a jeśli to się powiedzie, kliknij przycisk **dalej** . 

   ![Ustawienia zaawansowane konfiguracji środowiska IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia dotyczące środowiska Azure-SSIS IR, a następnie kliknij przycisk **Aktualizuj** .

1. Teraz możesz uruchomić środowisko Azure-SSIS IR, klikając przycisk **Start** w kolumnie **Akcje** dla środowiska Azure-SSIS IR. Uruchomienie środowiska Azure-SSIS IR do sieci wirtualnej trwa około 20 – 30 minut. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej
Aby można było dołączyć do niego środowisko Azure-SSIS IR, należy skonfigurować sieć wirtualną. Aby automatycznie skonfigurować uprawnienia/ustawienia sieci wirtualnej dla środowiska Azure-SSIS Integration Runtime w celu dołączenia do sieci wirtualnej, Dodaj następujący skrypt:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Utwórz środowisko Azure-SSIS IR i Dołącz je do sieci wirtualnej
Możesz utworzyć środowisko Azure-SSIS IR i dołączyć je do sieci wirtualnej w tym samym czasie. Aby uzyskać kompletny skrypt i instrukcje, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Przyłączanie istniejącego środowiska Azure-SSIS IR do sieci wirtualnej
Skrypt w artykule [Tworzenie środowiska Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md) pokazuje, jak utworzyć środowisko Azure-SSIS IR i dołączyć je do sieci wirtualnej w tym samym skrypcie. Jeśli masz istniejące środowisko Azure-SSIS IR, wykonaj następujące kroki, aby dołączyć je do sieci wirtualnej: 
1. Zatrzymaj środowisko Azure-SSIS IR. 
1. Skonfiguruj środowisko Azure-SSIS IR, aby dołączyć do sieci wirtualnej. 
1. Uruchom środowisko Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definiuj zmienne
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Zatrzymaj środowisko Azure-SSIS IR
Zatrzymaj środowisko Azure-SSIS Integration Runtime, zanim będzie można przyłączyć je do sieci wirtualnej. To polecenie zwalnia wszystkie jego węzły i przerywa rozliczanie:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Skonfiguruj ustawienia sieci wirtualnej dla środowiska Azure-SSIS IR do przyłączenia
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurowanie środowiska Azure-SSIS IR
Aby skonfigurować środowisko Azure-SSIS Integration Runtime do dołączenia do sieci wirtualnej, `Set-AzDataFactoryV2IntegrationRuntime` Uruchom polecenie: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Uruchom środowisko Azure-SSIS IR
Aby rozpocząć środowisko Azure-SSIS Integration Runtime, uruchom następujące polecenie: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

To polecenie zajmie od 20 do 30 minut.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat środowiska Azure SSIS Integration Runtime, zobacz następujące tematy: 
- [Środowisko Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje koncepcyjne na temat środowisk Integration Runtime, w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia środowiska Azure-SSIS IR. Używa Azure SQL Database do hostowania wykazu usług SSIS. 
- [Create an Azure-SSIS integration runtime (Tworzenie środowiska Azure-SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md) Ten artykuł rozszerza się w samouczku i zawiera instrukcje dotyczące używania Azure SQL Database z punktami końcowymi usługi sieci wirtualnej/wystąpieniem zarządzanym w sieci wirtualnej w celu hostowania wykazu usług SSIS i dołączenia środowiska Azure-SSIS IR do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i zawiera opisy stanu w zwróconych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. Przedstawiono w nim również sposób skalowania środowiska Azure-SSIS IR przez dodanie węzłów.
