---
title: Dołącz do sieci wirtualnej środowiska uruchomieniowego integracji usług SSIS Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprzęgać środowiska uruchomieniowego integracji usług SSIS Azure z sieci wirtualnej platformy Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: douglasl
ms.openlocfilehash: b998b47cdc65be91f62543369f5c3f18e4f270c4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619647"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Dołącz do środowiska uruchomieniowego integracji usług SSIS Azure do sieci wirtualnej
Dołącz do programu Azure SSIS integracji runtime (IR) do sieci wirtualnej platformy Azure w następujących scenariuszach: 

- Obsługiwana baza danych SQL Server Integration Services (SSIS) katalogu w wystąpieniu usługi Azure SQL bazy danych zarządzanych (wersja zapoznawcza) w sieci wirtualnej.
- Chcesz połączyć się z lokalnymi magazynami danych z pakietów usług SSIS działającymi w środowisku Azure SSIS Integration Runtime.

 Fabryka danych Azure w wersji 2 (wersja zapoznawcza) umożliwia dołączenie Twojego środowiska uruchomieniowego integracji usług SSIS Azure do sieci wirtualnej, która została utworzona za pośrednictwem klasycznego modelu wdrażania lub modelu wdrażania usługi Azure Resource Manager. 

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest zazwyczaj dostępności (GA), zobacz [fabryki danych w wersji 1 dokumentacji](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Dostęp do lokalnych magazynów danych
Pakiety usług SSIS dostęp do chmury publicznej tylko magazynów danych, nie trzeba dołączyć IR Azure SSIS do sieci wirtualnej. Jeśli pakiety usług SSIS uzyskać dostęp do lokalnych magazynów danych, IR Azure SSIS należy dołączyć do sieci wirtualnej, która jest połączona z siecią lokalną. 

Oto kilka ważnych kwestii, które należy pamiętać: 

- Jeśli nie istnieje sieci wirtualnej podłączonej do sieci lokalnej, najpierw utwórz [sieci wirtualnej Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) lub [klasycznej sieci wirtualnej](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla integracją Azure SSIS środowisko uruchomieniowe do przyłączenia. Następnie należy skonfigurować site-to-site [połączenie bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) lub [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) połączenie z tej sieci wirtualnej sieci lokalnej.
- Jeśli istnieje istniejącej usługi Azure Resource Manager lub klasycznej sieci wirtualnej podłączone do sieci lokalnej w tej samej lokalizacji co Twoje IR Azure SSIS, IR można dołączyć do tej sieci wirtualnej.
- W przypadku istniejącej sieci wirtualnej klasycznego podłączone do sieci lokalnej w innej lokalizacji z Twojej IR Azure SSIS, można najpierw utworzyć [klasycznej sieci wirtualnej](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla Twojego IR Azure SSIS do przyłączenia. Następnie należy skonfigurować [klasycznego do klasycznej sieci wirtualnej](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) połączenia. Lub można utworzyć [sieci wirtualnej Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) Twojego środowiska uruchomieniowego integracji Azure SSIS do przyłączenia. Następnie skonfiguruj [sieci wirtualnej klasycznego do usługi Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) połączenia.
- W przypadku istniejącego Menedżera zasobów Azure sieci wirtualnej podłączone do sieci lokalnej w innej lokalizacji z Twojej IR Azure SSIS, można najpierw utworzyć [sieci wirtualnej Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) dla usług Azure-SSIS IR do przyłączenia. Skonfiguruj połączenie wirtualnej sieci usługi Azure Resource Manager do usługi Azure Resource Manager. Można utworzyć [klasycznej sieci wirtualnej](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) dla Twojego IR Azure SSIS do przyłączenia. Następnie należy skonfigurować [sieci wirtualnej klasycznego do usługi Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) połączenia.

## <a name="host-the-ssis-catalog-database-on-azure-sql-database-managed-instance"></a>Bazę danych usług SSIS katalogu wystąpienia zarządzane bazy danych SQL Azure 
Jeżeli katalog SSIS znajduje się w wystąpieniu zarządzane bazy danych SQL (wersja zapoznawcza) w sieci wirtualnej, możesz także dołączyć do IR Azure SSIS, aby:

- Tej samej sieci wirtualnej.
- Inną sieć wirtualną mającą połączenie siecią sieci z jedną, która ma wystąpienia zarządzane bazy danych SQL (wersja zapoznawcza). 

Sieć wirtualną można wdrożyć przy użyciu klasycznego modelu wdrażania lub modelu wdrażania usługi Azure Resource Manager. Jeśli planujesz przyłączyć IR Azure SSIS *tej samej sieci wirtualnej* zawierającego wystąpienia zarządzane bazy danych SQL (wersja zapoznawcza), upewnij się, że IR Azure SSIS znajduje się w *innej podsieci* z jedną, która ma SQL Zarządzane wystąpienia bazy danych (wersja zapoznawcza).   

Więcej informacji można znaleźć w poniższych sekcjach.

## <a name="requirements-for-virtual-network-configuration"></a>Wymagania dotyczące konfiguracji sieci wirtualnej

-   Upewnij się, że `Microsoft.Batch` zarejestrowanego dostawcę w ramach subskrypcji obsługującego IR. Azure SSIS podsieć sieci wirtualnej Jeśli używasz klasycznej sieci wirtualnej również dołączyć `MicrosoftAzureBatch` do roli klasycznego współautora maszyny wirtualnej dla tej sieci wirtualnej.

-   Wybierz odpowiednie podsieci do obsługi podczerwieni Azure SSIS. Zobacz [wybrać podsieć](#subnet).

-   Jeśli używasz serwera usługi nazw domen (DNS) w sieci wirtualnej, zobacz [serwera usługi nazw domeny](#dns_server).

-   Jeśli korzystasz z grupy zabezpieczeń sieci (NSG) w tej podsieci, zobacz [grupy zabezpieczeń sieci](#nsg)

-   Jeśli używasz Azure Express Route lub konfiguracji użytkownika zdefiniowanych tras przez, zobacz [używana usługa Azure ExpressRoute lub trasy zdefiniowany przez użytkownika](#route).

-   Upewnij się, grupy zasobów w sieci wirtualnej może tworzyć i usuwać określonych zasobów sieci platformy Azure. Zobacz [wymagania dla grupy zasobów](#resource-group).

### <a name="subnet"></a> Wybierz podsieć
-   Nie wybieraj GatewaySubnet wdrażania środowiska uruchomieniowego integracji usług SSIS Azure, ponieważ jest przeznaczony dla bram sieci wirtualnej.

-   Upewnij się, że podsieci, którą wybierzesz ma wystarczającą ilość miejsca dostępnego adresu na IR Azure SSIS do użycia. Pozostaw co najmniej 2 * numer węzła IR w dostępnych adresów IP. Azure rezerwuje niektórych adresów IP w każdej podsieci, a nie można użyć tych adresów. Imię i nazwisko adresów IP podsieci są zastrzeżone dla protokołu zgodność, wraz z trzech więcej adresów używanych na potrzeby usług Azure. Aby uzyskać więcej informacji, zobacz [istnieją wszystkie ograniczenia dotyczące używania adresów IP w ramach tych podsieci?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

-   Nie używaj podsieci, które wyłącznie jest zajęta przez inne usługi platformy Azure (na przykład wystąpienia bazy danych SQL zarządzane, usługi App Service).

### <a name="dns_server"></a> Serwer usługi nazw domeny 
Jeśli należy użyć serwera usługi nazw domen (DNS) w sieci wirtualnej dołączane za pomocą Twojego środowiska uruchomieniowego integracji usług SSIS Azure, upewnij się, można rozwiązać nazwy publicznej Azure hosta (na przykład nazwą obiektu blob magazynu Azure, `<your storage account>.blob.core.windows.net`).

Zaleca się następujące czynności:

-   Skonfiguruj niestandardowe DNS do przekazywania żądań do usługi Azure DNS. Na serwerze DNS, może przesłać dalej nierozwiązane rekordy DNS na adres IP rozwiązujący cykliczne platformy Azure (168.63.129.16).

-   Skonfiguruj niestandardowe DNS jako podstawowa i usługi Azure DNS jako dodatkowej sieci wirtualnej. Rejestruje adres IP platformy Azure cykliczne programy rozpoznawania nazw (168.63.129.16) jako pomocniczy serwer DNS w przypadku, gdy serwer DNS jest niedostępna.

Aby uzyskać więcej informacji, zobacz [rozpoznawanie, który używa serwera DNS nazw](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="nsg"></a> Grupy zabezpieczeń sieci
Jeśli musisz wdrożyć grupę zabezpieczeń sieci (NSG) w sieci wirtualnej dołączane za pomocą programu Azure SSIS runtime integracji, Zezwalaj ruchu przychodzącego/wychodzącego przez następujące porty:

| Kierunek | Protokół transportu | Element źródłowy | Zakres portów źródła | Element docelowy | Port docelowy zakres | Komentarze |
|---|---|---|---|---|---|---|
| Przychodzący | TCP | Internet | * | VirtualNetwork | 29876, 29877 (Jeśli dołączysz IR sieci wirtualnej platformy Azure Resource Manager) <br/><br/>10100, 20100, 30100 (Jeśli dołączysz IR klasycznej sieci wirtualnej)| Usługi fabryka danych te porty są używane do komunikacji z węzłami Twojego środowiska uruchomieniowego integracji usług SSIS Azure w sieci wirtualnej. <br/><br/> Czy lub nie określono grupy NSG, fabryki danych zawsze konfiguruje grupy NSG na poziomie kart interfejsu sieciowego (NIC) dołączonych do maszyn wirtualnych, które host podczerwieni Azure SSIS. Dozwolone jest tylko na ruch przychodzący z adresów IP fabryki danych. Nawet jeśli możesz otworzyć te porty, aby ruch internetowy, ruch sieciowy z adresów IP, które nie są adresami IP fabryki danych jest zablokowany na poziomie karty Sieciowej. |
| Wychodzący | TCP | VirtualNetwork | * | Internet | 443 | Węzły sieci środowiska uruchomieniowego integracji usług SSIS Azure w sieci wirtualnej użyć tego portu dostępu do usług Azure, takich jak usługi Azure Storage i Azure Event Hubs. |
| Wychodzący | TCP | VirtualNetwork | * | Internet lub Sql | 1433, 11000 11999, 14000 14999 | Węzły sieci środowiska uruchomieniowego integracji usług SSIS Azure w sieci wirtualnej umożliwia dostęp do bazy danych SSISDB obsługiwanych przez serwer bazy danych SQL Azure te porty. (Ten cel, nie ma zastosowania do wystąpienia zarządzane bazy danych SQL (wersja zapoznawcza) na użytek bazy danych SSISDB). |
||||||||

### <a name="route"></a> Użyj usługi Azure ExpressRoute lub trasy zdefiniowane przez użytkownika

Możesz połączyć [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) obwód do infrastruktury sieci wirtualnej rozszerzenie sieci lokalnej na platformie Azure. 

Typowa konfiguracja jest użycie tunelowania wymuszonego (anonsowanie trasy protokołu BGP, 0.0.0.0/0 do sieci wirtualnej) który wymusza wychodzący ruch internetowy pochodzący z przepływów sieci wirtualnej do lokalnego urządzenia sieci inspekcji i rejestrowania. Ten przepływ ruchu dzieli łączność między IR Azure SSIS w sieci wirtualnej z usług zależnych usługi fabryka danych Azure. Rozwiązanie jest określenie jednego (lub więcej) [trasy zdefiniowane przez użytkownika (Udr)](../virtual-network/virtual-networks-udr-overview.md) w podsieci, która zawiera podczerwieni Azure SSIS. PRZEZ definiuje tras specyficzne dla podsieci, które są honorowane zamiast trasy protokołu BGP.

Albo można zdefiniować trasy zdefiniowane przez użytkownika (Udr), aby wymusić wychodzący ruch internetowy pochodzący z podsieci, które obsługuje IR Azure SSIS do innej podsieci, które obsługuje urządzenie wirtualne sieci jako zapora lub hosta DMZ inspekcji i rejestrowania.

W obu przypadkach należy zastosować trasy 0.0.0.0/0 Typ następnego przeskoku jako **Internet** w podsieci, które obsługuje IR Azure SSIS, umożliwiając komunikację między usługi fabryka danych i IR jest Azure SSIS mogła zakończyć się pomyślnie. 

![Dodaj trasę](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Jeśli masz obawy utraty możliwości inspekcji wychodzący ruch internetowy pochodzący od tej podsieci, można również dodać reguły NSG podsieci ruchu wychodzącego miejsca docelowe, aby ograniczyć [adresy IP centrum danych Azure](https://www.microsoft.com/download/details.aspx?id=41653).

Zobacz [ten skrypt programu PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) przykład. Należy uruchomić skrypt co tydzień w celu zapewnienia aktualności listy adresów IP centrum danych Azure.

### <a name="resource-group"></a> Wymagania dla grupy zasobów.
IR Azure SSIS potrzebuje do utworzenia niektórych zasobów sieciowych w tej samej grupie zasobów co sieć wirtualną, w tym do modułu równoważenia obciążenia Azure, Azure publicznego adresu IP i grupę zabezpieczeń sieci.

-   Upewnij się, że nie masz żadnych Blokada zasobu, grupy zasobów lub subskrypcji, do którego należy sieć wirtualna. Jeśli skonfigurujesz blokady tylko do odczytu lub usunięcia blokady uruchamianie i zatrzymywanie IR może zakończyć się niepowodzeniem lub zawieszenie.

-   Upewnij się, że nie masz Azure zasad, która zapobiega utworzeniu w ramach grupy zasobów lub subskrypcji, do którego należy sieć wirtualna następujące zasoby:
    -   Microsoft.Network/LoadBalancers
    -   Microsoft.Network/NetworkSecurityGroups
    -   Microsoft.Network/PublicIPAddresses

## <a name="azure-portal-data-factory-ui"></a>Portalu Azure (UI fabryki danych)
W tej sekcji przedstawiono sposób Dołącz do istniejącego środowiska uruchomieniowego Azure SSIS do sieci wirtualnej (klasyczne lub usługi Azure Resource Manager) za pomocą portalu Azure i interfejsu użytkownika z fabryki danych. Najpierw należy odpowiednio skonfigurować sieć wirtualną przed dołączeniem do niej Twojej IR Azure SSIS. Przejdź do jednej z dwóch następnych sekcjach na podstawie typu sieci wirtualnej (klasyczne lub usługi Azure Resource Manager). Następnie należy kontynuować sekcji trzeci sprzęgać z IR Azure SSIS do sieci wirtualnej. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Konfigurowanie sieci wirtualnej platformy Azure Resource Manager za pomocą portalu
Należy skonfigurować sieć wirtualną, zanim dołączysz IR Azure SSIS do niego.

1. Uruchom Microsoft Edge lub Google Chrome. Obecnie interfejsu użytkownika z fabryki danych jest obsługiwana tylko w tych przeglądarek sieci web.
2. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
3. Wybierz **więcej usług**. Filtruj i wybierz **sieci wirtualnych**.
4. Filtruj i wybierz z listy sieci wirtualnej. 
5. Na **sieci wirtualnej** wybierz pozycję **właściwości**. 
6. Wybierz przycisk kopia **identyfikator ZASOBU** można skopiować do Schowka identyfikator zasobu dla sieci wirtualnej. Zapisz identyfikator ze Schowka w programie OneNote lub pliku.
7. Wybierz **podsieci** w menu po lewej stronie. Upewnij się, że liczba **dostępnych adresów** jest większa niż w węzłach programu runtime integracji usług SSIS Azure.
8. Upewnij się, że dostawca partii zadań Azure jest zarejestrowany w subskrypcji platformy Azure, który ma sieci wirtualnej. Lub zarejestruj dostawcę w partii zadań Azure. Jeśli masz już konto partii zadań Azure w ramach subskrypcji, subskrypcja jest zarejestrowana dla partii zadań Azure. (Jeśli tworzysz IR Azure SSIS w portalu usługi fabryka danych dostawcy partii zadań Azure jest automatycznie rejestrowana.)

   a. W portalu Azure, wybierz **subskrypcje** w menu po lewej stronie.

   b. Wybierz subskrypcję. 
   
   c. Wybierz **dostawców zasobów** po lewej stronie i upewnij się, że **Microsoft.Batch** zarejestrowanego dostawcy.     
      ![Potwierdzenie stanu "Zarejestrowanej"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli nie widzisz **Microsoft.Batch** na liście, aby zarejestrować go, [jest tworzone puste konto partii zadań Azure](../batch/batch-account-create-portal.md) w ramach subskrypcji. Później można usunąć go.

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Skonfiguruj sieć wirtualną klasycznego za pomocą portalu
Należy skonfigurować sieć wirtualną, zanim dołączysz IR Azure SSIS do niego.

1. Uruchom Microsoft Edge lub Google Chrome. Obecnie interfejsu użytkownika z fabryki danych jest obsługiwana tylko w tych przeglądarek sieci web.
2. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
3. Wybierz **więcej usług**. Filtruj i wybierz **sieci wirtualnych (klasyczne)**.
4. Filtruj i wybierz z listy sieci wirtualnej. 
5. Na **sieć wirtualna (klasyczna)** wybierz pozycję **właściwości**. 

    ![Identyfikator zasobu klasycznej sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Wybierz przycisk kopia **identyfikator ZASOBU** można skopiować identyfikator zasobu sieci klasycznego do Schowka. Zapisz identyfikator ze Schowka w programie OneNote lub pliku.
6. Wybierz **podsieci** w menu po lewej stronie. Upewnij się, że liczba **dostępnych adresów** jest większa niż w węzłach programu runtime integracji usług SSIS Azure.

    ![Liczba dostępnych adresów w sieci wirtualnej](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Dołącz **MicrosoftAzureBatch** do **klasycznego współautora maszyny wirtualnej** roli dla sieci wirtualnej.

    a. Wybierz **(IAM) kontroli dostępu** w menu po lewej stronie, a następnie wybierz **Dodaj** na pasku narzędzi. 
       !["Kontrola dostępu" i "Dodaj" przycisków](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Na **dodać uprawnienia** wybierz pozycję **klasycznego współautora maszyny wirtualnej** dla **roli**. Wklej **ddbf3205-c6bd-46ae-8127-60eb93363864** w **wybierz** , a następnie wybierz **partia zadań Microsoft Azure** z listy wyników wyszukiwania.   
       ![Wyniki wyszukiwania na stronie "Dodaj uprawnienia"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Wybierz **zapisać** Aby zapisać ustawienia i zamknąć stronę.  
       ![Zapisz ustawienia dostępu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Upewnij się, że widoczny **partia zadań Microsoft Azure** na liście uczestników.  
       ![Potwierdź dostęp partii zadań Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Upewnij się, że dostawca partii zadań Azure jest zarejestrowany w subskrypcji platformy Azure, który ma sieci wirtualnej. Lub zarejestruj dostawcę w partii zadań Azure. Jeśli masz już konto partii zadań Azure w ramach subskrypcji, subskrypcja jest zarejestrowana dla partii zadań Azure. (Jeśli tworzysz IR Azure SSIS w portalu usługi fabryka danych dostawcy partii zadań Azure jest automatycznie rejestrowana.)

   a. W portalu Azure, wybierz **subskrypcje** w menu po lewej stronie.

   b. Wybierz subskrypcję.

   c. Wybierz **dostawców zasobów** po lewej stronie i upewnij się, że **Microsoft.Batch** zarejestrowanego dostawcy.     
      ![Potwierdzenie stanu "Zarejestrowanej"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli nie widzisz **Microsoft.Batch** na liście, aby zarejestrować go, [jest tworzone puste konto partii zadań Azure](../batch/batch-account-create-portal.md) w ramach subskrypcji. Później można usunąć go. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołącz IR Azure SSIS do sieci wirtualnej

1. Uruchom Microsoft Edge lub Google Chrome. Obecnie interfejsu użytkownika z fabryki danych jest obsługiwana tylko w tych przeglądarek sieci web.
2. W [portalu Azure](https://portal.azure.com), wybierz pozycję **fabryki danych** w menu po lewej stronie. Jeśli nie widzisz **fabryki danych** menu, wybierz **więcej usług**, a następnie wybierz polecenie **fabryki danych** w **analizy i analiza**sekcji. 
    
   ![Lista fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. Wybierz fabrykę danych ze środowiskiem uruchomieniowym integracji usług SSIS Azure na liście. Zostanie wyświetlona strona główna z fabryki danych. Wybierz **tworzenie i wdrażanie** kafelka. Fabryka danych interfejsu użytkownika zostanie wyświetlony na osobnej karcie. 

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. W Interfejsie fabryki danych, przełącz się do **Edytuj** wybierz opcję **połączeń**i przejdź do **środowisk uruchomieniowych integracji** kartę. 

   ![Karta "Środowisk uruchomieniowych integration"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Jeśli Twoje IR Azure SSIS działa na liście środowiska uruchomieniowego integracji wybierz **zatrzymać** przycisk **akcje** kolumny dla Twojego podczerwieni Azure SSIS. Nie można edytować IR, aż do momentu wyłączenia. 

   ![Zatrzymaj IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Na liście środowiska uruchomieniowego integracji, wybierz **Edytuj** przycisk **akcje** kolumny dla Twojego podczerwieni Azure SSIS.

   ![Edytuj środowiska uruchomieniowego integracji](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Na **ustawienia ogólne** strony **ustawień środowiska uruchomieniowego integracji** wybierz **dalej**. 

   ![Ogólne ustawienia IR.](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Na **ustawienia SQL** strony, wprowadź hasło administratora, a następnie wybierz **dalej**.

   ![Ustawienia programu SQL Server IR.](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Na **Zaawansowane ustawienia** wykonaj następujące czynności: 

   a. Zaznacz pole wyboru dla **wybierz sieć wirtualną dla Twojego środowiska uruchomieniowego integracji Azure SSIS do przyłączania a Zezwalaj usługom platformy Azure można skonfigurować ustawienia uprawnień sieci wirtualnej**.

   b. Aby uzyskać **typu**, określ, czy sieci wirtualnej jest klasycznej sieci wirtualnej lub sieci wirtualnej platformy Azure Resource Manager. 

   c. Aby uzyskać **nazwa sieci wirtualnej**, wybierz sieci wirtualnej.

   d. Aby uzyskać **nazwy podsieci**, wybierz podsieć w sieci wirtualnej.

   e. Wybierz **aktualizacji**. 

   ![Zaawansowane ustawienia IR.](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Teraz, można uruchomić IR za pomocą **Start** przycisk **akcje** kolumny dla Twojego podczerwieni Azure SSIS. Trwa około 20 minut, aby rozpocząć podczerwieni Azure SSIS. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Skonfiguruj sieć wirtualną
Należy skonfigurować sieć wirtualną, zanim dołączysz IR Azure SSIS do niego. Aby automatycznie skonfigurować sieć wirtualną/ustawienia uprawnień dla Twojego środowiska uruchomieniowego integracji Azure SSIS można dołączyć do sieci wirtualnej, należy dodać następujący skrypt:

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Utwórz IR Azure SSIS i przyłączyć go do sieci wirtualnej
Można utworzyć IR Azure SSIS i przyłączyć go do sieci wirtualnej, w tym samym czasie. Aby uzyskać kompletny skrypt oraz instrukcje, zobacz [utworzyć środowiska uruchomieniowego integracji usług SSIS Azure](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Dołącz do istniejącego IR Azure SSIS do sieci wirtualnej
Skrypt w [utworzyć środowiska uruchomieniowego integracji usług SSIS Azure](create-azure-ssis-integration-runtime.md) artykule przedstawiono sposób tworzenia IR Azure SSIS i przyłączyć go do sieci wirtualnej, w tym samym skrypcie. Jeśli masz istniejące IR Azure SSIS, wykonaj następujące kroki, aby przyłączyć się do sieci wirtualnej: 

1. Zatrzymaj podczerwieni Azure SSIS.
2. Skonfiguruj IR Azure SSIS, aby dołączyć do sieci wirtualnej. 
3. Uruchom podczerwieni Azure SSIS. 

### <a name="define-the-variables"></a>Zdefiniuj zmienne

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Zatrzymaj IR Azure SSIS
Zatrzymać środowiska uruchomieniowego integracji usług SSIS Azure, zanim można było przyłączyć ją do sieci wirtualnej. To polecenie zwalnia wszystkich jego węzłów i zatrzymuje rozliczeń:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurowanie ustawień sieci wirtualnej dla IR Azure SSIS dołączyć

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Skonfiguruj IR Azure SSIS
Aby skonfigurować dołączyć do sieci wirtualnej środowiska uruchomieniowego integracji usług SSIS Azure, uruchom `Set-AzureRmDataFactoryV2IntegrationRuntime` polecenia: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Uruchom IR Azure SSIS
Aby uruchomić środowiska uruchomieniowego integracji usług SSIS Azure, uruchom następujące polecenie: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
To polecenie pobiera 20-30 minut.

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać informacje o środowisku uruchomieniowym usług SSIS Azure zobacz następujące tematy: 

- [Środowisko uruchomieniowe integracji usług SSIS Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). Ten artykuł zawiera informacje o pojęciach dotyczących środowiska uruchomieniowe integracji ogólnie rzecz biorąc, w tym IR. Azure SSIS 
- [Samouczek: Wdrażanie pakietów usług SSIS na platformie Azure](tutorial-create-azure-ssis-runtime-portal.md). Ten artykuł zawiera szczegółowe instrukcje dotyczące tworzenia podczerwieni Azure SSIS. Używa bazy danych Azure SQL do hostowania katalogu usług SSIS. 
- [Create an Azure-SSIS integration runtime (Tworzenie środowiska Azure-SSIS Integration Runtime)](create-azure-ssis-integration-runtime.md) W tym artykule rozszerzenie samouczka i zawiera instrukcje dotyczące przy użyciu usługi Azure bazy danych zarządzanych wystąpienia SQL (wersja zapoznawcza) i dołączenie IR do sieci wirtualnej. 
- [Monitor an Azure-SSIS IR (Monitorowanie środowiska Azure-SSIS IR)](monitor-integration-runtime.md#azure-ssis-integration-runtime). W tym artykule przedstawiono sposób pobierania informacji o środowisku Azure-SSIS IR i opisów stanów w pobranych informacjach. 
- [Manage an Azure-SSIS IR (Zarządzanie środowiskiem Azure-SSIS IR)](manage-azure-ssis-integration-runtime.md). W tym artykule przedstawiono sposób zatrzymywania, uruchamiania lub usuwania środowiska Azure-SSIS IR. On również pokazano, jak do skalowania w poziomie z IR Azure SSIS, dodając węzły. 
