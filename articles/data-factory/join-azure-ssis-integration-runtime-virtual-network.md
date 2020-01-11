---
title: Dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej
description: Dowiedz się, jak dołączać środowisko Azure-SSIS Integration Runtime do sieci wirtualnej platformy Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 58bfc35776e83df7754379a12ad4b7afca73e32c
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892341"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej

Korzystając z SQL Server Integration Services (SSIS) w Azure Data Factory, należy przyłączyć się do środowiska Azure-SSIS Integration Runtime (IR) do sieci wirtualnej platformy Azure w następujących scenariuszach:

- Chcesz połączyć się z lokalnymi magazynami danych z pakietów SSIS, które są uruchamiane na Azure-SSIS IR bez konfigurowania lub zarządzania własnym hostowanym programem IR jako serwer proxy. 

- Chcesz hostować bazę danych wykazu usług SSIS (SSISDB) w Azure SQL Database z regułami zapory IP/punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym. 

- Chcesz połączyć się z zasobami platformy Azure skonfigurowanymi za pomocą punktów końcowych usługi sieci wirtualnej z pakietów SSIS, które są uruchomione na Azure-SSIS IR.

- Chcesz nawiązać połączenie z magazynami danych/zasobami skonfigurowanymi przy użyciu reguł zapory adresów IP z pakietów SSIS uruchomionych na Azure-SSIS IR.

Data Factory umożliwia dołączenie Azure-SSIS IR do sieci wirtualnej utworzonej za pomocą klasycznego modelu wdrażania lub Azure Resource Managergo modelu wdrażania.

> [!IMPORTANT]
> Klasyczna Sieć wirtualna jest przestarzała, dlatego użyj w zamian Azure Resource Manager sieci wirtualnej.  Jeśli używasz już klasycznej sieci wirtualnej, przełącz się do Azure Resource Manager sieci wirtualnej najszybciej, jak to możliwe.

W przypadku [konfigurowania środowiska Integration Runtime (IR) platformy Azure — SQL Server Integration Services (SSIS) w celu dołączenia do samouczka dotyczącego sieci wirtualnej](tutorial-deploy-ssis-virtual-network.md) przedstawiono minimalne kroki w Azure Portal. Ten artykuł rozszerza się w samouczku i opisuje wszystkie opcjonalne zadania:

- Jeśli używasz sieci wirtualnej (klasycznej).
- Jeśli utworzysz własne publiczne adresy IP dla Azure-SSIS IR.
- Jeśli używasz własnego serwera DNS (Domain Name System).
- W przypadku używania sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w podsieci.
- Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR).
- Jeśli używasz niestandardowych Azure-SSIS IR.
- W przypadku korzystania z aprowizacji programu Azure PowerShell.

## <a name="access-to-on-premises-data-stores"></a>Dostęp do lokalnych magazynów danych

Jeśli pakiety SSIS mają dostęp do lokalnych magazynów danych, możesz przyłączyć Azure-SSIS IR do sieci wirtualnej, która jest połączona z siecią lokalną. Możesz też skonfigurować własne środowisko IR jako serwer proxy dla Azure-SSIS IR i zarządzać nim. Aby uzyskać więcej informacji, zobacz [Konfigurowanie własnego środowiska IR jako serwera proxy dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Podczas dołączania Azure-SSIS IR do sieci wirtualnej należy pamiętać o następujących kwestiach: 

- Jeśli żadna Sieć wirtualna nie jest połączona z siecią lokalną, najpierw Utwórz [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla Azure-SSIS IR do przyłączenia. Następnie skonfiguruj [połączenie bramy sieci VPN typu](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) lokacja-lokacja lub połączenie [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) z tej sieci wirtualnej z siecią lokalną. 

- Jeśli Azure Resource Manager Sieć wirtualna jest już połączona z siecią lokalną w tej samej lokalizacji co Azure-SSIS IR, można przyłączyć je do tej sieci wirtualnej. 

- Jeśli klasyczna Sieć wirtualna jest już połączona z siecią lokalną w innej lokalizacji z Azure-SSIS IR, można utworzyć [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla Azure-SSIS IR do przyłączenia. Następnie skonfiguruj połączenie z [siecią wirtualną w trybie klasycznym Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Jeśli Azure Resource Manager Sieć wirtualna jest już połączona z siecią lokalną w innej lokalizacji z Azure-SSIS IR, można najpierw utworzyć [Azure Resource Manager sieci wirtualnej](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla Azure-SSIS IR do przyłączenia. Następnie skonfiguruj połączenie sieci wirtualnej Azure Resource Manager Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hostowanie wykazu usług SSIS w SQL Database

Jeśli katalog SSIS jest hostowany w Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej, upewnij się, że dołączysz Azure-SSIS IR do tej samej sieci wirtualnej i podsieci.

Jeśli katalog usług SSIS jest hostowany w wystąpieniu zarządzanym z prywatnym punktem końcowym, należy się upewnić, że dołączysz Azure-SSIS IR do tej samej sieci wirtualnej, ale w innej podsieci niż wystąpienie zarządzane. Aby przyłączyć Azure-SSIS IR do innej sieci wirtualnej niż wystąpienie zarządzane, zalecamy wirtualne sieci równorzędne (które są ograniczone do tego samego regionu) lub połączenie z sieci wirtualnej z siecią wirtualną. Aby uzyskać więcej informacji, zobacz [łączenie aplikacji z wystąpieniem zarządzanym Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Dostęp do usług platformy Azure

Jeśli pakiety SSIS mają dostęp do zasobów platformy Azure, które obsługują [punkty końcowe usługi sieci wirtualnej](../virtual-network/virtual-network-service-endpoints-overview.md) i chcesz zabezpieczyć dostęp do tych zasobów z Azure-SSIS IR, możesz dołączyć Azure-SSIS IR do podsieci sieci wirtualnej skonfigurowanej dla punktów końcowych usługi sieci wirtualnej, a następnie dodać regułę sieci wirtualnej do odpowiednich zasobów platformy Azure, aby zezwolić na dostęp z tej samej podsieci.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Dostęp do źródeł danych chronionych przez regułę zapory adresów IP

Jeśli pakiety SSIS mają dostęp do magazynów danych/zasobów, które zezwalają na tylko określone statyczne publiczne adresy IP i chcesz zabezpieczyć dostęp do tych zasobów z Azure-SSIS IR, możesz przenieść własne [publiczne adresy IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) do Azure-SSIS IR podczas dołączania do sieci wirtualnej, a następnie dodać regułę zapory IP do odpowiednich zasobów, aby zezwolić na dostęp z tych adresów IP.

We wszystkich przypadkach sieć wirtualna można wdrożyć tylko za pomocą modelu wdrażania Azure Resource Manager.

Poniższe sekcje zawierają więcej szczegółów. 

## <a name="virtual-network-configuration"></a>Konfiguracja sieci wirtualnej

Skonfiguruj sieć wirtualną tak, aby spełniała te wymagania: 

- Upewnij się, że `Microsoft.Batch` jest zarejestrowanym dostawcą w ramach subskrypcji podsieci sieci wirtualnej, która hostuje Azure-SSIS IR. W przypadku korzystania z klasycznej sieci wirtualnej należy również dołączyć `MicrosoftAzureBatch` do roli współautora klasycznej maszyny wirtualnej dla tej sieci wirtualnej. 

- Upewnij się, że masz wymagane uprawnienia. Aby uzyskać więcej informacji, zobacz [Konfigurowanie uprawnień](#perms).

- Wybierz odpowiednią podsieć, aby hostować Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [wybierz podsieć](#subnet). 

- Jeśli chcesz uzyskać własne publiczne adresy IP dla Azure-SSIS IR, zobacz [Wybieranie statycznych publicznych adresów IP](#publicIP)

- Jeśli używasz własnego serwera DNS (Domain Name System) w sieci wirtualnej, zobacz [Konfigurowanie serwera DNS](#dns_server). 

- Jeśli używasz sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w podsieci, zobacz [Konfigurowanie sieciowej grupy zabezpieczeń](#nsg). 

- Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR), zobacz [Korzystanie z usługi Azure ExpressRoute lub UDR](#route). 

- Upewnij się, że grupa zasobów sieci wirtualnej (lub Grupa zasobów publiczne adresy IP), jeśli będziesz udostępniać własne publiczne adresy IP), może tworzyć i usuwać niektóre zasoby sieci platformy Azure. Aby uzyskać więcej informacji, zobacz [Konfigurowanie grupy zasobów](#resource-group). 

- W przypadku dostosowania Azure-SSIS IR zgodnie z opisem w temacie [Konfiguracja niestandardowa dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)węzły Azure-SSIS IR będą uzyskiwać prywatne adresy IP ze wstępnie zdefiniowanego zakresu od 172.16.0.0 do 172.31.255.255. Upewnij się, że zakresy prywatnych adresów IP sieci wirtualnych lub lokalnych nie kolidują z tym zakresem.

Ten diagram przedstawia wymagane połączenia dla Azure-SSIS IR:

![Środowisko IR Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Konfigurowanie uprawnień

Użytkownik, który tworzy Azure-SSIS IR, musi mieć następujące uprawnienia:

- W przypadku dołączania środowiska IR do programu SSIS do sieci wirtualnej Azure Resource Manager dostępne są dwie opcje:

  - Użyj wbudowanej roli współautor sieci. Ta rola jest dostępna z uprawnieniem _Microsoft. Network/\*_ , które ma znacznie większy zakres niż jest to konieczne.

  - Utwórz rolę niestandardową, która zawiera tylko wymagane uprawnienie _Microsoft. Network/virtualNetworks/\*/Join/Action_ . Jeśli chcesz również przenieść własne publiczne adresy IP do Azure-SSIS IR podczas dołączania do Azure Resource Manager sieci wirtualnej, w roli należy również uwzględnić uprawnienie _Microsoft. Network/adresów publicipaddress/*/Join/Action_ .

- Jeśli dołączysz środowisko IR usług SSIS do klasycznej sieci wirtualnej, zalecamy użycie wbudowanej roli współautor klasycznej maszyny wirtualnej. W przeciwnym razie musisz zdefiniować rolę niestandardową, która zawiera uprawnienie do przyłączenia do sieci wirtualnej.

### <a name="subnet"></a>Wybierz podsieć

Podczas wybierania podsieci: 

- Nie wybieraj GatewaySubnet do wdrożenia Azure-SSIS IR. Jest on przeznaczony dla bram sieci wirtualnej. 

- Upewnij się, że wybrana podsieć ma wystarczającą ilość dostępnej przestrzeni adresowej dla Azure-SSIS IR do użycia. Pozostaw dostępne adresy IP dla co najmniej dwóch razy numeru węzła IR. Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach. Nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów, a dla usług platformy Azure są używane trzy więcej adresów. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Nie należy używać podsieci, która jest przeznaczona wyłącznie dla innych usług platformy Azure (na przykład SQL Database wystąpienia zarządzanego, App Service itd.). 

### <a name="publicIP"></a>Wybierz statyczne publiczne adresy IP

Jeśli chcesz udostępnić własne statyczne publiczne adresy IP dla Azure-SSIS IR podczas dołączania do sieci wirtualnej, upewnij się, że spełniają one następujące wymagania:

- Należy podać dokładnie dwa nieużywane zasoby, które nie są jeszcze skojarzone z innymi zasobami platformy Azure. Ta dodatkowa zostanie użyta, gdy okresowo uaktualniamy Azure-SSIS IR.

- Obydwie te elementy powinny być statyczne dla typu standardowego. Aby uzyskać więcej informacji, zobacz [jednostki SKU publicznego adresu IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) .

- Powinny one mieć nazwę DNS. Jeśli nie podano nazwy DNS podczas jej tworzenia, można to zrobić na Azure Portal.

![Środowisko IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Te i Sieć wirtualna powinny znajdować się w tej samej subskrypcji i w tym samym regionie.

### <a name="dns_server"></a>Konfigurowanie serwera DNS 

Jeśli musisz użyć własnego serwera DNS w sieci wirtualnej przyłączonej przez Azure-SSIS IR, upewnij się, że może on rozpoznać globalne nazwy hostów platformy Azure (na przykład obiekt BLOB usługi Azure Storage o nazwie `<your storage account>.blob.core.windows.net`). 

Zalecane są następujące kroki: 

- Skonfiguruj niestandardowe DNS do przesyłania żądań do Azure DNS. Nierozpoznane rekordy DNS można przesłać dalej na adres IP tłumaczeń cyklicznych platformy Azure (168.63.129.16) na własnym serwerze DNS. 

- Skonfiguruj niestandardowy system DNS jako podstawowy serwer DNS dla sieci wirtualnej. Skonfiguruj Azure DNS jako pomocniczy serwer DNS. Zarejestruj adres IP tłumaczeń cyklicznych platformy Azure (168.63.129.16) jako pomocniczy serwer DNS w przypadku niedostępności własnego serwera DNS. 

Aby uzyskać więcej informacji, zobacz [rozpoznawanie nazw używające własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Konfigurowanie sieciowej grupy zabezpieczeń

Jeśli musisz zaimplementować sieciowej grupy zabezpieczeń dla podsieci używanej przez Azure-SSIS IR, Zezwól na ruch przychodzący i wychodzący przez następujące porty: 

| Kierunek | Protokół transportowy | Źródło | Zakres portów źródłowych | Cel | Zakres portów docelowych | Komentarze |
|---|---|---|---|---|---|---|
| Przychodzące | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (w przypadku dołączenia środowiska IR do sieci wirtualnej Menedżer zasobów) <br/><br/>10100, 20100, 30100 (Jeśli dołączysz środowisko IR do klasycznej sieci wirtualnej)| Usługa Data Factory używa tych portów do komunikacji z węzłami Azure-SSIS IR w sieci wirtualnej. <br/><br/> Niezależnie od tego, czy tworzysz sieciowej grupy zabezpieczeń poziomu podsieci, Data Factory zawsze konfiguruje sieciowej grupy zabezpieczeń na poziomie kart sieciowych podłączonych do maszyn wirtualnych, które obsługują Azure-SSIS IR. Tylko ruch przychodzący z Data Factory adresów IP na określonych portach jest dozwolony przez ten sieciowej grupy zabezpieczeń na poziomie karty sieciowej. Nawet w przypadku otwarcia tych portów do ruchu internetowego na poziomie podsieci ruch z adresów IP, które nie są Data Factory adresy IP, jest blokowany na poziomie karty sieciowej. |
| Wychodzące | TCP | VirtualNetwork | * | AzureCloud | 443 | Węzły Azure-SSIS IR w sieci wirtualnej używają tego portu do uzyskiwania dostępu do usług platformy Azure, takich jak Azure Storage i Azure Event Hubs. |
| Wychodzące | TCP | VirtualNetwork | * | Internet | 80 | Węzły Azure-SSIS IR w sieci wirtualnej używają tego portu do pobierania listy odwołania certyfikatów z Internetu. |
| Wychodzące | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Węzły Azure-SSIS IR w sieci wirtualnej używają tych portów do uzyskiwania dostępu do SSISDB hostowanego przez serwer SQL Database. Jeśli zasada połączenia serwera SQL Database jest ustawiona na **serwer proxy** zamiast **przekierowania**, wymagany jest tylko port 1433. Ta reguła zabezpieczeń dla ruchu wychodzącego nie ma zastosowania do SSISDB hostowanego przez wystąpienie zarządzane w sieci wirtualnej. |
||||||||

### <a name="route"></a>Korzystanie z usługi Azure ExpressRoute lub UDR

Po połączeniu obwodu [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) z infrastrukturą sieci wirtualnej w celu rozbudowania sieci lokalnej na platformę Azure w typowej konfiguracji jest używany tunel wymuszony (anonsowanie trasy BGP, 0.0.0.0/0, do sieci wirtualnej). To tunelowanie wymusza wychodzący ruch internetowy z przepływu sieci wirtualnej do lokalnego urządzenia sieciowego na potrzeby inspekcji i rejestrowania. 
 
Można też zdefiniować [UDR](../virtual-network/virtual-networks-udr-overview.md) , aby wymusić ruch wychodzący z podsieci, która hostuje Azure-SSIS IR w innej podsieci, która hostuje sieciowe urządzenie wirtualne (urządzenie WUS) jako zaporę lub zaporę platformy Azure na potrzeby inspekcji i rejestrowania. 

W obu przypadkach trasa ruchu będzie przerwać wymaganą łączność przychodzącą z usług Azure Data Factory zależnych (w zależności od Azure Batch usług zarządzania) do Azure-SSIS IR w sieci wirtualnej. Aby tego uniknąć, należy zdefiniować co najmniej jedną UDR w podsieci, która zawiera Azure-SSIS IR. 

Można zastosować trasę 0.0.0.0/0 z typem następnego przeskoku jako **Internet** w podsieci, która hostuje Azure-SSIS IR w scenariuszu usługi Azure ExpressRoute. Można też zmodyfikować istniejącą trasę 0.0.0.0/0 z typu następnego przeskoku jako **urządzenie wirtualne** do **Internetu** w scenariuszu urządzenie WUS.

![Dodawanie trasy](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Jeśli chodzi o utratę możliwości inspekcji wychodzącego ruchu internetowego z tej podsieci, można zdefiniować określone UDR, aby kierować ruchem tylko między Azure Batch usług zarządzania i Azure-SSIS IR z typem następnego przeskoku jako **Internet**.

Na przykład jeśli Azure-SSIS IR znajduje się w `UK South`, otrzymasz listę zakresów adresów IP `BatchNodeManagement.UKSouth` z [zakresu adresów IP znaczników usługi](https://www.microsoft.com/en-us/download/details.aspx?id=56519) lub za pośrednictwem [interfejsu API odnajdywania tagów](https://aka.ms/discoveryapi)usługi. Następnie zastosuj następujący UDR z pokrewnych tras zakresów adresów IP z typem następnego przeskoku jako **Internet**.

![Azure Batch ustawienia UDR](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Takie podejście wiąże się z dodatkowym kosztem konserwacji. Regularnie sprawdzaj zakres adresów IP i Dodaj nowe zakresy adresów IP do UDR, aby uniknąć przerwania Azure-SSIS IR. Zalecamy sprawdzanie zakresu adresów IP miesięcznie, ponieważ gdy nowy adres IP pojawia się w tagu usługi, zostanie wprowadzony kolejny miesiąc. 

### <a name="resource-group"></a>Konfigurowanie grupy zasobów

Azure-SSIS IR musi utworzyć określone zasoby sieciowe w ramach tej samej grupy zasobów co sieć wirtualna. Te zasoby obejmują:
- Moduł równoważenia obciążenia platformy Azure o nazwie *\<Identyfikator Guid >-azurebatch-cloudserviceloadbalancer*.
- Publiczny adres IP platformy Azure o nazwie *\<Identyfikator Guid >-azurebatch-cloudservicepublicip*.
- Grupa zabezpieczeń sieć służbowa o nazwie *\<Identyfikator Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Możesz teraz przenieść własne statyczne publiczne adresy IP do Azure-SSIS IR. W tym scenariuszu utworzysz tylko usługę Azure load module i sieciową grupę zabezpieczeń w ramach tej samej grupy zasobów co statyczne publiczne adresy IP zamiast sieci wirtualnej.

Te zasoby zostaną utworzone po rozpoczęciu Azure-SSIS IR. Zostaną one usunięte po zatrzymaniu Azure-SSIS IR. W przypadku wprowadzenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR nie zostaną one usunięte po zatrzymaniu Azure-SSIS IR. Aby zapobiec zablokowaniu Azure-SSIS IR z zatrzymywania, nie używaj ponownie tych zasobów sieciowych w innych zasobach. 

Upewnij się, że nie masz blokady zasobów w grupie zasobów/subskrypcji, do której należą Sieć wirtualna/Twoje statyczne publiczne adresy IP. W przypadku skonfigurowania blokady tylko do odczytu/usuwania, uruchamianie i zatrzymywanie Azure-SSIS IR zakończy się niepowodzeniem lub przestanie odpowiadać.

Upewnij się, że nie masz zasad platformy Azure, które uniemożliwiają utworzenie następujących zasobów w grupie zasobów/subskrypcji, do której należą Sieć wirtualna/statyczne publiczne adresy IP: 
- Microsoft. Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>FAQ

- Jak mogę chronić publiczny adres IP uwidoczniony w Azure-SSIS IR na potrzeby połączenia przychodzącego? Czy można usunąć publiczny adres IP?
 
  Teraz publiczny adres IP zostanie utworzony automatycznie, gdy Azure-SSIS IR zostanie przyłączony do sieci wirtualnej. Mamy sieciowej grupy zabezpieczeń na poziomie kart sieciowych, aby zezwalać na połączenia przychodzące tylko Azure Batch usług zarządzania z Azure-SSIS IR. Można także określić sieciowej grupy zabezpieczeń na poziomie podsieci na potrzeby ochrony ruchu przychodzącego.

  Jeśli nie chcesz ujawniać żadnych publicznych adresów IP, rozważ [skonfigurowanie własnego środowiska IR jako serwera proxy dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) zamiast przyłączania Azure-SSIS IR do sieci wirtualnej, jeśli ma to zastosowanie do danego scenariusza.
 
- Czy mogę dodać publiczny adres IP mojego Azure-SSIS IR do listy dozwolonych przez zaporę dla moich źródeł danych?

  Możesz teraz przenieść własne statyczne publiczne adresy IP do Azure-SSIS IR. W takim przypadku można dodać adresy IP do listy dozwolonych w zaporze dla źródeł danych. Możesz również uwzględnić inne poniższe opcje, aby zabezpieczyć dostęp do danych z Azure-SSIS IR w zależności od scenariusza:

  - Jeśli źródło danych jest lokalne, po nawiązaniu połączenia z siecią wirtualną z siecią lokalną i dołączeniu Azure-SSIS IR do podsieci sieci wirtualnej, możesz dodać zakres prywatnego adresu IP tej podsieci do listy dozwolonych przez zaporę dla źródła danych. .
  - Jeśli źródło danych jest usługą platformy Azure, która obsługuje punkty końcowe usługi sieci wirtualnej, można skonfigurować punkt końcowy usługi sieci wirtualnej w podsieci sieci wirtualnej i dołączyć do Azure-SSIS IR do tej podsieci. Następnie można dodać regułę sieci wirtualnej z tą podsiecią do zapory dla źródła danych.
  - Jeśli źródło danych jest usługą w chmurze spoza platformy Azure, możesz użyć UDR do kierowania ruchu wychodzącego z Azure-SSIS IR do zapory urządzenie WUS/Azure za pośrednictwem statycznego publicznego adresu IP. Następnie można dodać statyczny publiczny adres IP zapory urządzenie WUS/Azure do listy dozwolonych przez zaporę dla źródła danych.
  - Jeśli żadna z powyższych opcji nie spełnia Twoich potrzeb, należy rozważyć skonfigurowanie samodzielnego [środowiska IR jako serwera proxy dla Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Następnie można dodać statyczny publiczny adres IP maszyny, która hostuje własne środowisko IR na liście dozwolonych w zaporze dla źródła danych.

- Dlaczego muszę podać dwa statyczne adresy publiczne, jeśli chcę utworzyć własne dla Azure-SSIS IR?

  Azure-SSIS IR jest regularnie aktualizowana automatycznie. Podczas uaktualniania tworzone są nowe węzły, które zostaną usunięte. Jednak aby uniknąć przestoju, stare węzły nie zostaną usunięte, dopóki nowe nie będą gotowe. W ten sposób pierwszy statyczny publiczny adres IP używany przez stare węzły nie może zostać natychmiast wydzierżawiony, a do utworzenia nowych węzłów potrzebny jest drugi statyczny publiczny adres IP.

- Mam własne statyczne publiczne adresy IP dla Azure-SSIS IR, ale dlaczego nadal nie można uzyskać dostępu do moich źródeł danych?

  - Upewnij się, że dwa statyczne publiczne adresy IP są dodawane do listy dozwolonych w zaporze dla źródeł danych. Za każdym razem, gdy Azure-SSIS IR jest uaktualniana, jego statyczny publiczny adres IP jest przełączany między tymi, które zostały przez Ciebie połączone. Jeśli dodasz tylko jeden z nich do listy dozwolonych, dostęp do danych dla Azure-SSIS IR zostanie przerwany po uaktualnieniu.
  - Jeśli źródło danych jest usługą platformy Azure, sprawdź, czy została ona skonfigurowana za pomocą punktów końcowych usługi sieci wirtualnej. W takim przypadku ruch z Azure-SSIS IR do źródła danych zostanie przełączony w celu użycia prywatnych adresów IP zarządzanych przez usługi platformy Azure, a dodanie własnych statycznych publicznych adresów IP do listy dozwolonych przez zaporę dla źródła danych nie zacznie obowiązywać.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory interfejsie użytkownika)

W tej sekcji pokazano, jak przyłączyć istniejące Azure-SSIS IR do sieci wirtualnej (klasycznej lub Azure Resource Manager) za pomocą interfejsu użytkownika Azure Portal i Data Factory. 

Przed przyłączeniem Azure-SSIS IR do sieci wirtualnej należy prawidłowo skonfigurować sieć wirtualną. Postępuj zgodnie z instrukcjami w sekcji, które dotyczą typu sieci wirtualnej (klasyczne lub Azure Resource Manager). Następnie postępuj zgodnie z instrukcjami w trzeciej sekcji, aby dołączać Azure-SSIS IR do sieci wirtualnej. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurowanie sieci wirtualnej Azure Resource Manager

Użyj portalu, aby skonfigurować sieć wirtualną Azure Resource Manager przed podjęciem próby dołączenia do niej Azure-SSIS IR.

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 

1. Wybierz pozycję **więcej usług**. Odfiltruj i wybierz **sieci wirtualne**. 

1. Odfiltruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna** wybierz pozycję **Właściwości**. 

1. Wybierz przycisk Kopiuj dla **identyfikatora zasobu** , aby skopiować identyfikator zasobu dla sieci wirtualnej do Schowka. Zapisz identyfikator ze schowka w programie OneNote lub pliku. 

1. Z menu po lewej stronie wybierz pozycję **podsieci**. Upewnij się, że liczba dostępnych adresów jest większa niż węzły w Azure-SSIS IR. 

1. Sprawdź, czy dostawca Azure Batch jest zarejestrowany w subskrypcji platformy Azure z siecią wirtualną. Lub Zarejestruj dostawcę Azure Batch. Jeśli masz już konto Azure Batch w subskrypcji, Twoja subskrypcja jest zarejestrowana na potrzeby Azure Batch. (Jeśli utworzysz Azure-SSIS IR w portalu Data Factory, dostawca Azure Batch zostanie automatycznie zarejestrowany.) 

   1. W Azure Portal w menu po lewej stronie wybierz pozycję **subskrypcje**. 

   1. Wybierz subskrypcję. 

   1. Po lewej stronie wybierz pozycję **dostawcy zasobów**i upewnij się, że **Microsoft. Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie stanu zarejestrowanego](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli na liście nie widzisz elementu **Microsoft. Batch** , aby go zarejestrować, [utwórz puste konto Azure Batch](../batch/batch-account-create-portal.md) w ramach subskrypcji. Można go później usunąć. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurowanie klasycznej sieci wirtualnej

Użyj portalu, aby skonfigurować klasyczną sieć wirtualną przed podjęciem próby dołączenia do niej Azure-SSIS IR. 

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com). 

1. Wybierz pozycję **więcej usług**. Filtruj i wybieraj **sieci wirtualne (klasyczne)** . 

1. Odfiltruj i wybierz sieć wirtualną na liście. 

1. Na stronie **Sieć wirtualna (klasyczna)** wybierz pozycję **Właściwości**. 

   ![Identyfikator zasobu klasycznej sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Wybierz przycisk Kopiuj dla **identyfikatora zasobu** , aby skopiować identyfikator zasobu dla sieci klasycznej do Schowka. Zapisz identyfikator ze schowka w programie OneNote lub pliku. 

1. Z menu po lewej stronie wybierz pozycję **podsieci**. Upewnij się, że liczba dostępnych adresów jest większa niż węzły w Azure-SSIS IR. 

   ![Liczba dostępnych adresów w sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Dołącz **MicrosoftAzureBatch** do roli **współautora klasycznej maszyny wirtualnej** dla sieci wirtualnej. 

   1. W menu po lewej stronie wybierz pozycję **Kontrola dostępu (IAM)** , a następnie wybierz kartę **przypisania ról** . 

   ![Przyciski "kontrola dostępu" i "Dodaj"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Wybierz **Dodaj przypisanie roli**.

   1. Na stronie **Dodaj przypisanie roli** w polu **rola**wybierz opcję **współautor klasycznej maszyny wirtualnej**. W polu **wyboru** wklej **ddbf3205-c6bd-46ae-8127-60eb93363864**, a następnie wybierz pozycję **Microsoft Azure Batch** z listy wyników wyszukiwania. 

   ![Wyniki wyszukiwania na stronie "Dodawanie przypisania roli"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Wybierz pozycję **Zapisz** , aby zapisać ustawienia, a następnie zamknij stronę. 

   ![Zapisz ustawienia dostępu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Upewnij się, że na liście współautorów jest widoczny **Microsoft Azure Batch** . 

   ![Potwierdź dostęp Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Sprawdź, czy dostawca Azure Batch jest zarejestrowany w subskrypcji platformy Azure z siecią wirtualną. Lub Zarejestruj dostawcę Azure Batch. Jeśli masz już konto Azure Batch w subskrypcji, Twoja subskrypcja jest zarejestrowana na potrzeby Azure Batch. (Jeśli utworzysz Azure-SSIS IR w portalu Data Factory, dostawca Azure Batch zostanie automatycznie zarejestrowany.) 

   1. W Azure Portal w menu po lewej stronie wybierz pozycję **subskrypcje**. 

   1. Wybierz subskrypcję. 

   1. Po lewej stronie wybierz pozycję **dostawcy zasobów**i upewnij się, że **Microsoft. Batch** jest zarejestrowanym dostawcą. 

   ![Potwierdzenie stanu zarejestrowanego](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli na liście nie widzisz elementu **Microsoft. Batch** , aby go zarejestrować, [utwórz puste konto Azure Batch](../batch/batch-account-create-portal.md) w ramach subskrypcji. Można go później usunąć. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołącz Azure-SSIS IR do sieci wirtualnej

Po skonfigurowaniu sieci wirtualnej Azure Resource Manager lub klasycznej sieci wirtualnej można przyłączyć Azure-SSIS IR do sieci wirtualnej:

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory. 

1. W [Azure Portal](https://portal.azure.com)w menu po lewej stronie wybierz pozycję **fabryki danych**. Jeśli nie widzisz **fabryk danych** w menu, wybierz pozycję **więcej usług**, a następnie w sekcji **Analiza i analiza** wybierz pozycję **fabryki danych**. 

   ![Lista fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wybierz fabrykę danych z Azure-SSIS IR na liście. Zostanie wyświetlona strona główna fabryki danych. Wybierz kafelek **tworzenie & monitor** . Na oddzielnej karcie zobaczysz interfejs użytkownika Data Factory. 

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. W interfejsie użytkownika Data Factory przejdź do karty **Edycja** , wybierz pozycję **połączenia**i przejdź do karty **środowisko Integration Runtime** . 

   ![Karta "Integration Runtimes"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Jeśli Azure-SSIS IR jest uruchomiona, na liście **środowiska Integration Runtime** w kolumnie **Akcje** wybierz przycisk **Zatrzymaj** dla Azure-SSIS IR. Nie można edytować Azure-SSIS IR, dopóki nie zostanie zatrzymane. 

   ![Zatrzymaj środowisko IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na liście **środowiska Integration Runtime** w kolumnie **Akcje** wybierz przycisk **Edytuj** dla Azure-SSIS IR. 

   ![Edytuj środowisko Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. W panelu ustawień środowiska Integration Runtime przejdź do sekcji **Ustawienia ogólne** i **Ustawienia SQL** , wybierając przycisk **dalej** . 

1. W sekcji **Ustawienia zaawansowane** : 

   1. Wybierz **sieć wirtualną, do której chcesz dołączyć Azure-SSIS Integration Runtime, Zezwól na automatyczny dostęp do zasobów sieciowych, a opcjonalnie zaznacz pole wyboru statyczne publiczne adresy IP** . 

   1. W obszarze **subskrypcja**wybierz subskrypcję platformy Azure, która ma swoją sieć wirtualną.

   1. W polu **Lokalizacja**jest wybierana ta sama lokalizacja środowiska Integration Runtime.

   1. W polu **Typ**wybierz typ sieci wirtualnej: klasyczny lub Azure Resource Manager. Zalecamy wybranie Azure Resource Manager sieci wirtualnej, ponieważ klasyczne sieci wirtualne będą wkrótce przestarzałe.

   1. W polu **Nazwa**sieci wirtualnej wybierz nazwę swojej usługi. Powinna być taka sama, jak ta, która jest używana przez serwer Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania SSISDB. Lub powinien być taki sam, jak jeden połączony z siecią lokalną. W przeciwnym razie może to być dowolna Sieć wirtualna do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

   1. W polu **Nazwa podsieci**wybierz nazwę podsieci dla sieci wirtualnej. Powinna być taka sama, jak ta, która jest używana przez serwer Azure SQL Database z punktami końcowymi usługi sieci wirtualnej do hostowania SSISDB. Lub powinien być inną podsiecią niż używana dla wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

   1. Zaznacz pole wyboru **Przenieś statyczne publiczne adresy IP dla Azure-SSIS Integration Runtime** , aby wybrać, czy chcesz przenieść własne statyczne publiczne adresy ip dla Azure-SSIS IR, dzięki czemu możesz zezwolić na dostęp do nich w zaporze dla źródeł danych.

      Jeśli zaznaczysz pole wyboru, wykonaj następujące czynności.

      1. W polu **pierwszy statyczny publiczny adres IP**wybierz pierwszy statyczny publiczny adres IP, który [spełnia wymagania](#publicIP) Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.
      
      1. W polu **drugi statyczny publiczny adres IP**wybierz drugi statyczny publiczny adres IP, który [spełnia wymagania](#publicIP) Azure-SSIS IR. Jeśli nie masz żadnego z tych opcji, kliknij przycisk **Utwórz nowy** link, aby utworzyć statyczne publiczne adresy IP na Azure Portal a następnie kliknij przycisk Odśwież tutaj, aby je wybrać.

   1. Wybierz pozycję **weryfikacja sieci wirtualnej**. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz pozycję **Kontynuuj**. 

   ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia Azure-SSIS IR. Następnie wybierz pozycję **Aktualizuj**.

1. Rozpocznij Azure-SSIS IR, wybierając przycisk **Start** w kolumnie **Akcje** dla Azure-SSIS IR. Uruchomienie Azure-SSIS IR dołączania do sieci wirtualnej trwa od 20 do 30 minut. 

## <a name="azure-powershell"></a>Program Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Aby można było przyłączyć Azure-SSIS IR do sieci wirtualnej, należy skonfigurować sieć wirtualną. Aby automatycznie skonfigurować uprawnienia i ustawienia sieci wirtualnej dla Azure-SSIS IR w celu dołączenia do sieci wirtualnej, Dodaj następujący skrypt:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Tworzenie Azure-SSIS IR i dołączanie do sieci wirtualnej

Można utworzyć Azure-SSIS IR i przyłączyć ją do sieci wirtualnej w tym samym czasie. Aby uzyskać kompletny skrypt i instrukcje, zobacz [tworzenie Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Dołącz do istniejącej Azure-SSIS IR do sieci wirtualnej

W artykule [tworzenie Azure-SSIS IR](create-azure-ssis-integration-runtime.md) pokazano, jak utworzyć Azure-SSIS IR i dołączyć go do sieci wirtualnej w tym samym skrypcie. Jeśli masz już Azure-SSIS IR, wykonaj następujące kroki, aby dołączyć je do sieci wirtualnej: 
1. Zatrzymaj Azure-SSIS IR. 
1. Skonfiguruj Azure-SSIS IR, aby dołączyć do sieci wirtualnej. 
1. Uruchom Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definiuj zmienne

```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Zatrzymaj Azure-SSIS IR

Aby można było przyłączyć się do sieci wirtualnej, należy zatrzymać Azure-SSIS IR. To polecenie zwalnia wszystkie jego węzły i przerywa rozliczanie:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Skonfiguruj ustawienia sieci wirtualnej dla Azure-SSIS IR do przyłączenia

Aby skonfigurować ustawienia dla sieci wirtualnej, do których zostanie dołączona platforma Azure-SSIS, użyj tego skryptu: 

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

### <a name="configure-the-azure-ssis-ir"></a>Skonfiguruj Azure-SSIS IR

Aby dołączyć Azure-SSIS IR do sieci wirtualnej, uruchom polecenie `Set-AzDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Uruchom Azure-SSIS IR

Aby uruchomić Azure-SSIS IR, uruchom następujące polecenie: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

To polecenie zajmie od 20 do 30 minut.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure-SSIS IR, zobacz następujące artykuły: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera ogólne informacje koncepcyjne dotyczące urzędu skarbowego, w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten samouczek zawiera instrukcje krok po kroku dotyczące tworzenia Azure-SSIS IR. Używa Azure SQL Database do hostowania wykazu usług SSIS. 
- [Utwórz Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Ten artykuł rozszerza się w samouczku. Zawiera instrukcje dotyczące używania Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym w sieci wirtualnej do hostowania wykazu usług SSIS. Przedstawiono sposób przyłączania Azure-SSIS IR do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule pokazano, jak uzyskać informacje o Azure-SSIS IR. Zawiera opisy stanu dla zwracanych informacji. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania Azure-SSIS IR. Przedstawiono w nim również sposób skalowania Azure-SSIS IR przez dodanie węzłów.