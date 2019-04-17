---
title: Dołączyć środowisko Azure-SSIS integration runtime do sieci wirtualnej | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprzęgać środowiska Azure-SSIS integration runtime z siecią wirtualną platformy Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/08/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 6978b83e66f58e468d9f98394904861c8a4d8bd0
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59618145"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Dołącz do środowiska Azure-SSIS integration runtime do sieci wirtualnej
Dołącz do środowiska Azure-SSIS integration runtime (IR) z siecią wirtualną platformy Azure w następujących scenariuszach: 

- Chcesz połączyć się z lokalnymi magazynami danych z pakietów usług SSIS działającymi w środowisku Azure SSIS Integration Runtime. 

- Hostują bazy danych wykazu usług SQL Server Integration Services (SSIS) w usłudze Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej/zarządzanego wystąpienia. 

  Usługa Azure Data Factory umożliwia dołączenie środowiska Azure-SSIS integration runtime do sieci wirtualnej utworzonej za pomocą klasycznego modelu wdrażania lub modelu wdrażania usługi Azure Resource Manager. 

> [!IMPORTANT]
> Obecnie jest on przestarzały klasycznej sieci wirtualnej, dlatego należy użyć sieci wirtualnej usługi Azure Resource Manager, zamiast tego.  Jeśli już używasz klasycznej sieci wirtualnej, Przełącz możliwie jak używać sieci wirtualnej usługi Azure Resource Manager.

## <a name="access-to-on-premises-data-stores"></a>Dostęp do lokalnych magazynów danych
Jeśli pakiety usług SSIS dostępu do magazynów danych jedyna chmura publiczna, nie trzeba dołączyć środowisko Azure-SSIS IR do sieci wirtualnej. Jeśli pakiety usług SSIS dostępu do lokalnych magazynów danych, musisz dołączyć Azure-SSIS IR do sieci wirtualnej, która jest połączona z siecią lokalną. 

Poniżej przedstawiono kilka ważne punkty, które należy zwrócić uwagę: 

- Jeśli nie istnieje sieć wirtualna połączona z siecią lokalną, należy najpierw utworzyć [sieci wirtualnej usługi Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) lub [klasycznej sieci wirtualnej](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla integracji usługi Azure-SSIS środowisko uruchomieniowe do przyłączenia. Następnie należy skonfigurować site-to-site [połączenia bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) lub [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) połączenia z tej sieci wirtualnej z siecią lokalną. 

- Jeśli istnieje istniejącej usługi Azure Resource Manager lub klasycznej sieci wirtualnej podłączonej do sieci lokalnej w tej samej lokalizacji środowiska IR Azure-SSIS, możesz dołączyć środowiska IR do tej sieci wirtualnej. 

- W przypadku istniejących klasycznej sieci wirtualnej podłączonej do sieci w środowisku lokalnym, w innym miejscu ze środowiska IR Azure-SSIS, można najpierw utworzyć [klasycznej sieci wirtualnej](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla środowiska IR Azure-SSIS do przyłączenia. Następnie należy skonfigurować [klasycznego do klasycznej sieci wirtualnej](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) połączenia. Lub możesz utworzyć [sieci wirtualnej usługi Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) dla środowiska Azure-SSIS integration runtime do dołączenia do. Następnie skonfiguruj [sieci wirtualnej klasycznego do usługi Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) połączenia. 
 
- W przypadku istniejącej usługi Azure Resource Manager sieć wirtualna połączona z siecią lokalną w innej lokalizacji z środowiska IR Azure-SSIS, można najpierw utworzyć [sieci wirtualnej usługi Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) dla środowiska Azure-SSIS Środowisko IR do przyłączenia. Następnie należy skonfigurować połączenia sieci wirtualnej usługi Azure Resource Manager do usługi Azure Resource Manager. Lub można utworzyć [klasycznej sieci wirtualnej](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla środowiska IR Azure-SSIS do przyłączenia. Następnie należy skonfigurować [sieci wirtualnej klasycznego do usługi Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) połączenia. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Bazę danych wykazu usług SSIS w usłudze Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej/zarządzanego wystąpienia
Jeśli katalog usług SSIS jest hostowana w usłudze Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej lub wystąpienia zarządzanego, można przyłączyć środowiska IR Azure-SSIS do: 

- Tej samej sieci wirtualnej 
- Inną sieć wirtualną, który jest podłączony do sieci, z tą, która jest używana do wystąpienia zarządzanego 

Jeśli na serwerze wykazu usług SSIS w usłudze Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej, upewnij się, Dołącz do środowiska Azure-SSIS IR do tej samej sieci wirtualnej i podsieci.

Jeśli dołączysz do środowiska Azure-SSIS IR do tej samej sieci wirtualnej jako wystąpienia zarządzanego, upewnij się, że Azure-SSIS IR znajduje się w innej podsieci niż wystąpienia zarządzanego. Jeśli dołączysz do środowiska Azure-SSIS IR do sieci wirtualnej inną niż wystąpienie zarządzane, firma Microsoft zaleca komunikacji równorzędnej sieci wirtualnej (który jest ograniczona do tego samego regionu) lub sieci wirtualnej do połączenia sieci wirtualnej. Zobacz [połączyć aplikację z wystąpienia zarządzanego Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

We wszystkich przypadkach można wdrożyć za pomocą modelu wdrażania usługi Azure Resource Manager tylko sieć wirtualną.

Więcej szczegółów można znaleźć w poniższych sekcjach. 

## <a name="requirements-for-virtual-network-configuration"></a>Wymagania dotyczące konfiguracji sieci wirtualnej
-   Upewnij się, że `Microsoft.Batch` jest zarejestrowanego dostawcy w ramach subskrypcji z podsieci sieci wirtualnej, który hostuje Azure-SSIS IR. Jeśli używasz klasycznej sieci wirtualnej również dołączyć `MicrosoftAzureBatch` do roli współautora klasycznej maszyny wirtualnej dla tej sieci wirtualnej. 

-   Upewnij się, że masz wymagane uprawnienia. Zobacz [wymagane uprawnienia](#perms).

-   Wybierz odpowiednie podsieci w celu hostowania Azure-SSIS IR. Zobacz [Wybierz podsieć](#subnet). 

-   Jeśli używasz własnego serwera usługi nazw domen (DNS, Domain Name System) w sieci wirtualnej, zobacz [serwera usługi nazw domen](#dns_server). 

-   Jeśli używasz grupy zabezpieczeń sieci (NSG) w tej podsieci, zobacz [sieciowej grupy zabezpieczeń](#nsg) 

-   Jeśli są przy użyciu usługi Azure Expressroute lub konfigurowanie zdefiniowaną przez użytkownika (UDR), zobacz [użycia usługi Azure ExpressRoute lub trasy zdefiniowane użytkownika](#route). 

-   Upewnij się, grupy zasobów w sieci wirtualnej można tworzyć i usuwać niektórych zasobów sieciowych platformy Azure. Zobacz [wymagania dla grupy zasobów](#resource-group). 

Poniżej przedstawiono diagram przedstawiający połączenia wymagane dla środowiska IR Azure-SSIS:

![Środowisko IR Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> Wymagane uprawnienia

Użytkownik, który tworzy środowisko Azure-SSIS Integration Runtime musi mieć następujące uprawnienia:

- Jeśli dołączasz środowiska SSIS IR do sieci wirtualnej usługi Azure Resource Manager, masz dwie opcje:

  - Użyj wbudowanych *Współautor sieci* roli. Ta rola jest dostarczany z _Microsoft.Network/\*_  uprawnienie, które ma znacznie większym zakresie niż to konieczne.

  - Utwórz rolę niestandardową, która zawiera tylko niezbędne _Microsoft.Network/virtualNetworks/\*/join/Akcja_ uprawnień. 

- Jeśli dołączasz środowiska SSIS IR do klasycznej sieci wirtualnej, zalecane jest użycie wbudowanych *współautora klasycznej maszyny wirtualnej* roli. W przeciwnym razie należy zdefiniować rolę niestandardową, która obejmuje uprawnienie do dołączenia do sieci wirtualnej.

### <a name="subnet"></a> Wybierz podsieć
-   Nie należy wybierać GatewaySubnet wdrażania środowiska Azure-SSIS Integration Runtime, ponieważ jest to wersja dedykowana dla bramy sieci wirtualnej. 

-   Upewnij się, że podsieć, którą wybierzesz ma wystarczającą ilość miejsca dostępnego adresu na platformie Azure-SSIS IR do użycia. Pozostaw co najmniej 2 * liczba węzłów IR w dostępnych adresów IP. Platforma Azure rezerwuje pewnych adresów IP w każdej podsieci, a nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane dla zgodności protokołów, oraz trzy dodatkowe adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Nie używaj podsieci, która wyłącznie jest zajęta przez inne usługi platformy Azure (na przykład wystąpienie zarządzane usługi SQL Database, usługi App Service, itp.). 

### <a name="dns_server"></a> Serwer usługi nazw domen 
Jeśli musisz użyć własnego serwera usługi nazw domen (DNS, Domain Name System) w sieci wirtualnej, która jest dołączane za pomocą środowiska Azure-SSIS integration runtime, upewnij się, może rozpoznać nazwy hosta z publicznej platformy Azure (na przykład nazwę usługi Azure Storage blob, `<your storage account>.blob.core.windows.net`). 

Zaleca się następujące czynności: 

-   Konfigurowanie niestandardowych serwerów DNS do przekazywania żądań do usługi Azure DNS. Możesz przesłać dalej nierozwiązane rekordów DNS, adres IP programu rozpoznawania cyklicznego platformy Azure (168.63.129.16) na serwerze DNS. 

-   Konfigurowanie niestandardowych serwerów DNS jako podstawowy i usługi Azure DNS jako pomocniczy dla sieci wirtualnej. Rejestruje adres IP programu rozpoznawania cyklicznego platformy Azure (168.63.129.16) jako pomocniczy serwer DNS, w przypadku, gdy serwer DNS jest niedostępna. 

Aby uzyskać więcej informacji, zobacz [nazwę rozwiązania, który używa własnego serwera DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Sieciowa grupa zabezpieczeń
Jeśli musisz wdrożyć sieciową grupę zabezpieczeń (NSG) dla podsieci używane przez środowisko Azure-SSIS integration runtime, Zezwalaj ruchu przychodzącego/wychodzącego, za pomocą następujących portów: 

| Kierunek | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
|---|---|---|---|---|---|---|
| Przychodzący | TCP | AzureCloud<br/>(lub większym zakresie, takich jak Internet) | * | VirtualNetwork | 29876, 29877 (Jeśli dołączysz środowiska IR do sieci wirtualnej usługi Azure Resource Manager) <br/><br/>10100, 20100, 30100 (jeśli Podczerwieni przyłączyć się do klasycznej sieci wirtualnej)| Usługa Data Factory używa tych portów do komunikacji z węzłami Twojego środowiska Azure-SSIS integration runtime w sieci wirtualnej. <br/><br/> Czy tworzysz sieciowa grupa zabezpieczeń poziomu podsieci, czy nie, Data Factory zawsze konfiguruje sieciowej grupy zabezpieczeń na poziomie kart interfejsu sieciowego (NIC) dołączonych do maszyn wirtualnych, które hostują Azure-SSIS IR. Tylko dla ruchu przychodzącego ruchu z adresów IP fabryki danych w określonych portów jest dozwolone przy tym poziomie karty Sieciowej, sieciowej grupy zabezpieczeń. Nawet jeśli otworzysz te porty dla ruchu internetowego na poziomie podsieci, ruch z adresów IP, które nie są adresami IP fabryki danych jest zablokowany na poziomie karty Sieciowej. |
| Wychodzący | TCP | VirtualNetwork | * | AzureCloud<br/>(lub większym zakresie, takich jak Internet) | 443 | Ten port jest używany przez węzły Twojego środowiska Azure-SSIS integration Runtime w sieci wirtualnej na dostęp do usług platformy Azure, takich jak Azure Storage i Azure Event Hubs. |
| Wychodzący | TCP | VirtualNetwork | * | Internet | 80 | Węzły Twojego środowiska Azure-SSIS integration Runtime w sieci wirtualnej, ten port jest używany do pobierania listy odwołania certyfikatów z Internetu. |
| Wychodzący | TCP | VirtualNetwork | * | Sql<br/>(lub większym zakresie, takich jak Internet) | 1433, 11000-11999, 14000-14999 | Węzły Twojego środowiska Azure-SSIS integration Runtime w sieci wirtualnej użycie tych portów do dostępu do bazy danych SSISDB hostowanych przez serwer usługi Azure SQL Database — w tym celu nie ma zastosowania do danych SSISDB hostowaną przez wystąpienia zarządzanego. |
||||||||

### <a name="route"></a> Za pomocą usługi Azure ExpressRoute lub trasa zdefiniowana przez użytkownika
Możesz połączyć [usługi Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) obwodu do infrastruktury sieci wirtualnej w celu rozszerzenia swojej sieci lokalnej na platformę Azure. 

Typowa konfiguracja polega na korzystanie z tunelowania wymuszonego (anonsować trasy protokołu BGP, 0.0.0.0/0 do sieci wirtualnej) który wymusza wychodzącego ruchu internetowego z sieci wirtualnej przepływu lokalne urządzenie sieci inspekcji i rejestrowania. Ten przepływ ruchu przerywa łączność między Azure-SSIS IR w sieci wirtualnej za pomocą usługi Azure Data Factory zależne. Rozwiązaniem jest zdefiniowanie jednego (lub więcej) [trasy zdefiniowane przez użytkownika (Udr)](../virtual-network/virtual-networks-udr-overview.md) w podsieci, która zawiera Azure-SSIS IR. Trasa zdefiniowana przez użytkownika definiuje trasy specyficzne dla podsieci, które są uznawane zamiast trasy protokołu BGP. 

Lub można zdefiniować trasy zdefiniowane przez użytkownika (Udr), aby wymusić ruch wychodzący z Internetu z podsieci, który hostuje Azure-SSIS IR do innej podsieci, która obsługuje wirtualnego urządzenia sieciowego jako zapora lub hosta sieci Obwodowej, inspekcji i rejestrowania. 

W obu przypadkach należy zastosować trasę 0.0.0.0/0 z typem następnego przeskoku jako **Internet** w podsieci, który hostuje środowisko Azure-SSIS IR, tak, aby komunikacji między usługą Data Factory i Azure-SSIS IR to będzie możliwe. 

![Dodawanie trasy](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Jeśli interesujące Cię utraty możliwość kontroli wychodzącego ruchu internetowego z poziomu tej podsieci, możesz również dodać regułę sieciowej grupy zabezpieczeń w podsieci w celu ograniczenia ruchu wychodzącego miejsca docelowe, aby [adresów IP centrów danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

Zobacz [ten skrypt programu PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) przykład. Należy uruchomić skrypt co tydzień w celu zapewnienia aktualności listy adresów IP centrum danych platformy Azure. 

### <a name="resource-group"></a> Wymagania dotyczące grupy zasobów
-   Azure-SSIS IR potrzebne do utworzenia niektórych zasobów sieciowych w ramach tej samej grupie zasobów co sieć wirtualna. Te zasoby obejmują następujące czynności:
    -   Usługa Azure load balancer o nazwie  *\<Guid > - azurebatch - cloudserviceloadbalancer*.
    -   Azure publiczny adres IP, nazwą  *\<Guid > - azurebatch - cloudservicepublicip*.
    -   Grupę zabezpieczeń sieci pracy o nazwie  *\<Guid > - azurebatch - cloudservicenetworksecuritygroup*. 

-   Upewnij się, że nie masz żadnej blokady zasobu, grupy zasobów lub subskrypcji, do którego należy sieć wirtualna. Jeśli skonfigurujesz blokadę tylko do odczytu lub blokadę usuwania, uruchamiania i zatrzymywania środowisko IR może zakończyć się niepowodzeniem lub przestać odpowiadać. 

-   Upewnij się, że nie masz usługi Azure policy, co zapobiega tworzona w ramach grupy zasobów lub subskrypcji, do którego należy sieć wirtualna następujące zasoby: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Witryna Azure portal (interfejsu użytkownika Data Factory)
W tej sekcji dowiesz się, jak sprzęgać istniejących Azure-SSIS runtime do sieci wirtualnej (klasycznej lub na platformie Azure Resource Manager) za pomocą witryny Azure portal i interfejs użytkownika usługi Data Factory. Najpierw należy odpowiednio skonfigurować sieć wirtualną, przed dołączeniem do środowiska Azure-SSIS IR do niego. Wykonaj jedną z dwóch następnych sekcji, w zależności od typu Twojej sieci wirtualnej (klasycznej lub na platformie Azure Resource Manager). Następnie przejdź do trzeciej części dołączanie środowiska Azure-SSIS IR do sieci wirtualnej. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Konfigurowanie sieci wirtualnej usługi Azure Resource Manager za pomocą portalu
Musisz skonfigurować sieć wirtualną można było przyłączyć środowiska Azure-SSIS IR do niego. 

1. Uruchom przeglądarki Microsoft Edge lub Google Chrome. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko w tych przeglądarkach sieci web. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz **więcej usług**. Filtruj, a następnie wybierz pozycję **sieci wirtualne**. 

1. Filtruj, a następnie wybierz sieć wirtualną z listy. 

1. Na **sieć wirtualna** wybierz opcję **właściwości**. 

1. Wybierz przycisk kopiowania **identyfikator ZASOBU** można skopiować identyfikator zasobu dla sieci wirtualnej do Schowka. Zapisz identyfikator ze Schowka w programie OneNote lub pliku. 

1. Wybierz **podsieci** w menu po lewej stronie. Upewnij się, że liczba **dostępnymi adresami** jest większa niż liczba węzłów w środowiska Azure-SSIS integration runtime. 

1. Upewnij się, że dostawca usługi Azure Batch jest zarejestrowany w subskrypcji platformy Azure, która ma sieci wirtualnej. Możesz też zarejestrować dostawcę usługi Azure Batch. Jeśli masz już konto usługi Azure Batch w ramach subskrypcji, Twoja subskrypcja jest zarejestrowana dla usługi Azure Batch. (Jeśli tworzysz środowisko IR Azure-SSIS w portalu usługi Data Factory, dostawca usługi Azure Batch jest automatycznie rejestrowana.) 

   a. W witrynie Azure portal wybierz **subskrypcje** w menu po lewej stronie. 

   b. Wybierz subskrypcję. 

   c. Wybierz **dostawców zasobów** po lewej stronie i upewnij się, że **Microsoft.Batch** jest zarejestrowanego dostawcy. 

   ![Potwierdzenie stanu "Zarejestrowane"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli nie widzisz **Microsoft.Batch** na liście, aby zarejestrować go, [utworzyć konto usługi Azure Batch pusty](../batch/batch-account-create-portal.md) w ramach subskrypcji. Możesz je usunąć później. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Konfigurowanie klasyczną siecią wirtualną za pomocą portalu
Musisz skonfigurować sieć wirtualną można było przyłączyć środowiska Azure-SSIS IR do niego. 

1. Uruchom przeglądarki Microsoft Edge lub Google Chrome. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko w następujących przeglądarkach sieci web. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. Wybierz **więcej usług**. Filtruj, a następnie wybierz pozycję **sieci wirtualne (klasyczne)**. 

1. Filtruj, a następnie wybierz sieć wirtualną z listy. 

1. Na **sieć wirtualna (klasyczna)** wybierz opcję **właściwości**. 

   ![Identyfikator zasobu klasycznej sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Wybierz przycisk kopiowania **identyfikator ZASOBU** można skopiować identyfikator zasobu klasycznej sieci do Schowka. Zapisz identyfikator ze Schowka w programie OneNote lub pliku. 

1. Wybierz **podsieci** w menu po lewej stronie. Upewnij się, że liczba **dostępnymi adresami** jest większa niż liczba węzłów w środowiska Azure-SSIS integration runtime. 

   ![Liczba dostępnych adresów w sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Dołącz do **MicrosoftAzureBatch** do **współautora klasycznej maszyny wirtualnej** roli w ramach sieci wirtualnej. 

    a. Wybierz **kontrola dostępu (IAM)** w menu po lewej stronie, a następnie wybierz pozycję **przypisań ról** kartę. 

    !["Kontroli dostępu" i "Add" przycisków](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Wybierz **Dodaj przypisanie roli**.

    c. Na **Dodaj przypisanie roli** wybierz opcję **współautora klasycznej maszyny wirtualnej** dla **roli**. Wklej **ddbf3205-c6bd-46ae-8127-60eb93363864** w **wybierz** , a następnie wybierz **Microsoft Azure Batch** z listy wyników wyszukiwania. 

    ![Wyniki wyszukiwania na stronie "Dodawanie przypisania roli"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Wybierz **Zapisz** Aby zapisać ustawienia i zamknąć stronę. 

    ![Zapisz ustawienia dostępu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Upewnij się, że **Microsoft Azure Batch** na liście współautorów. 

    ![Potwierdzanie dostępu do usługi Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Upewnij się, że dostawca usługi Azure Batch jest zarejestrowany w subskrypcji platformy Azure, która ma sieci wirtualnej. Możesz też zarejestrować dostawcę usługi Azure Batch. Jeśli masz już konto usługi Azure Batch w ramach subskrypcji, Twoja subskrypcja jest zarejestrowana dla usługi Azure Batch. (Jeśli tworzysz środowisko IR Azure-SSIS w portalu usługi Data Factory, dostawca usługi Azure Batch jest automatycznie rejestrowana.) 

   a. W witrynie Azure portal wybierz **subskrypcje** w menu po lewej stronie. 

   b. Wybierz subskrypcję. 

   c. Wybierz **dostawców zasobów** po lewej stronie i upewnij się, że **Microsoft.Batch** jest zarejestrowanego dostawcy. 

   ![Potwierdzenie stanu "Zarejestrowane"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli nie widzisz **Microsoft.Batch** na liście, aby zarejestrować go, [utworzyć konto usługi Azure Batch pusty](../batch/batch-account-create-portal.md) w ramach subskrypcji. Możesz je usunąć później. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołączanie środowiska Azure-SSIS IR do sieci wirtualnej
1. Uruchom przeglądarki Microsoft Edge lub Google Chrome. Obecnie interfejs użytkownika usługi Data Factory jest obsługiwany tylko w tych przeglądarkach sieci web. 

1. W [witryny Azure portal](https://portal.azure.com), wybierz opcję **fabryk danych** w menu po lewej stronie. Jeśli nie widzisz **fabryk danych** menu, wybierz **więcej usług**, a następnie wybierz **fabryk danych** w **rozwiązania INTELIGENTNE + analiza**sekcji. 

   ![Listy fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wybór fabryki danych przy użyciu środowiska Azure-SSIS integration runtime na liście. Zostanie wyświetlona strona główna fabryki danych. Wybierz **tworzenie i wdrażanie** kafelka. Zostanie wyświetlony interfejs użytkownika usługi Data Factory na osobnej karcie. 

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. W Interfejsie Data Factory, przejdź do **Edytuj** zaznacz **połączeń**i przejdź do **środowiska Integration Runtime** kartę. 

   ![Karta "Integration runtimes"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Środowiska IR Azure-SSIS jest uruchamiania na liście integracji środowiska uruchomieniowego, wybierz opcję **zatrzymać** znajdujący się w **akcje** kolumny dla usługi Azure-SSIS IR. Nie można edytować środowisko IR, dopóki nie zostanie zatrzymana. 

   ![Zatrzymaj środowisko IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na liście środowisko uruchomieniowe integracji wybierz **Edytuj** znajdujący się w **akcje** kolumny dla usługi Azure-SSIS IR. 

   ![Edytuj środowiska integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Na **ustawienia ogólne** strony **Instalatora środowiska Integration Runtime** wybierz **dalej**. 

   ![Ogólne ustawienia środowiska IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

1. Na **ustawień serwera SQL** strony, wprowadź hasło administratora, a następnie wybierz **dalej**. 

   ![Ustawienia programu SQL Server IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

1. Na **Zaawansowane ustawienia** wykonaj następujące czynności: 

   a. Zaznacz pole wyboru dla **wybierz sieć wirtualną dla środowiska Azure-SSIS Integration Runtime do dołączenia i Zezwalaj usług platformy Azure na konfigurowanie uprawnień/ustawień sieci wirtualnej**. 

   b. Aby uzyskać **typu**wybierz, czy usługa virtual network jest klasyczną siecią wirtualną lub siecią wirtualną usługi Azure Resource Manager. 

   c. Aby uzyskać **nazwa sieci wirtualnej**, wybierz sieć wirtualną. 

   d. Aby uzyskać **Nazwa podsieci**, wybierz podsieć w sieci wirtualnej. 

   e. Kliknij przycisk **Weryfikacja sieci wirtualnej** i jeśli to się powiedzie, kliknij pozycję **aktualizacji**. 

   ![Zaawansowane ustawienia środowiska IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Teraz można uruchomić środowisko IR za pomocą **Start** znajdujący się w **akcje** kolumny dla usługi Azure-SSIS IR. Trwa około 20 – 30 minut, aby rozpocząć Azure-SSIS IR. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej
Musisz skonfigurować sieć wirtualną można było przyłączyć środowiska IR Azure-SSIS do niego. Aby automatycznie skonfigurować uprawnienia/ustawienia sieci wirtualnej dla środowiska Azure-SSIS integration runtime do dołączenia do sieci wirtualnej, Dodaj następujący skrypt:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Tworzenie środowiska Azure-SSIS IR i przyłącz ją do sieci wirtualnej
Można utworzyć środowisko Azure-SSIS IR i przyłącz ją do sieci wirtualnej, w tym samym czasie. Aby uzyskać kompletny skrypt i instrukcje, zobacz [tworzenia środowiska Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Dołącz do istniejącego Azure-SSIS IR do sieci wirtualnej
Skrypt w [tworzenia środowiska Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) artykułu dowiesz się, jak utworzyć środowisko Azure-SSIS IR i przyłącz ją do sieci wirtualnej, w tym samym skrypcie. Jeśli masz istniejące środowisko IR Azure-SSIS, wykonaj następujące kroki, aby dołączyć ją do sieci wirtualnej: 
1. Zatrzymaj Azure-SSIS IR. 
1. Skonfiguruj Azure-SSIS IR do dołączenia do sieci wirtualnej. 
1. Rozpocznij Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definiowanie zmiennych
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Zatrzymaj środowisko IR Azure-SSIS
Zatrzymaj środowisko Azure-SSIS integration runtime, zanim można było przyłączyć ją do sieci wirtualnej. To polecenie zwalnia wszystkie jego węzły i zatrzymuje rozliczeń:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurowanie ustawień sieci wirtualnej dla usługi Azure-SSIS IR do dołączenia do
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
Aby skonfigurować środowisko Azure-SSIS integration runtime do dołączenia do sieci wirtualnej, uruchom `Set-AzDataFactoryV2IntegrationRuntime` polecenia: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Uruchom środowisko Azure-SSIS IR
Aby uruchomić środowisko Azure-SSIS integration runtime, uruchom następujące polecenie: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

To polecenie wymaga 20 do 30 minut na zakończenie.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać więcej informacji na temat środowiskiem uruchomieniowym usług SSIS na platformie Azure zobacz następujące tematy: 
- [Środowisko Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowiska integration Runtime ogólnie rzecz biorąc, w tym Azure-SSIS IR. 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia środowiska Azure-SSIS IR. Używa usługi Azure SQL Database do hostowania wykazu usług SSIS. 
- [Create an Azure-SSIS integration runtime (Tworzenie środowiska Azure-SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md) Ten artykuł stanowi rozszerzenie samouczka i zawiera instrukcje dotyczące usługi Azure SQL Database za pomocą punktów końcowych usługi sieci wirtualnej/zarządzanego wystąpienia do hostowania wykazu usług SSIS i dołączania środowiska IR do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. On również pokazano, jak skalowanie środowiska IR Azure-SSIS, dodając węzły. 
