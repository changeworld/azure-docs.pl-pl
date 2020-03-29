---
title: Wdrażanie szablonu rozwiązania konsorcjum Ethereum Proof-of-Authority na platformie Azure
description: Użyj rozwiązania konsorcjum Ethereum Proof-of-Authority, aby wdrożyć i skonfigurować wieloosobową sieć Ethereum konsorcjum na platformie Azure
ms.date: 12/18/2019
ms.topic: article
ms.reviewer: coborn
ms.openlocfilehash: 7e9af5c501b58f6828360ee280440ea85698bf16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75387681"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Wdrażanie szablonu rozwiązania konsorcjum weryfikacji uprawnień Ethereum na platformie Azure

Za pomocą [szablonu rozwiązania platformy Azure w wersji zapoznawczej konsorcjum Ethereum w wersji zapoznawczej](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) można wdrożyć, skonfigurować i zarządzać wieloczłowieką sieci Ethereum weryfikacji urzędu przy minimalnej wiedzy na temat platformy Azure i Ethereum.

Szablon rozwiązania może służyć każdemu członkowi konsorcjum do aprowizowania śladu sieci blockchain przy użyciu usług obliczeniowych, sieciowych i magazynowych platformy Microsoft Azure. Każdy członek konsorcjum rozmiar sieci składa się z zestawu węzłów walidatora równoważenia obciążenia, które aplikacja lub użytkownik może wchodzić w interakcje z przesłać transakcje Ethereum.

## <a name="choose-an-azure-blockchain-solution"></a>Wybierz rozwiązanie platformy Azure Blockchain

Przed wybraniem opcji użycia szablonu rozwiązania konsorcjum certyfikatu urzędu Ethereum porównaj swój scenariusz z typowymi przypadkami użycia dostępnych opcji narzędzia Azure Blockchain.

Opcja | Model usługi | Typowy przypadek użycia
-------|---------------|-----------------
Szablony rozwiązań | IaaS | Szablony rozwiązań to szablony usługi Azure Resource Manager, których można użyć do aprowizowania w pełni skonfigurowaną topologię sieci blockchain. Szablony wdrażają i konfigurują usługi obliczeniowe, sieciowe i magazynowe platformy Microsoft Azure dla danego typu sieci łańcucha bloków.
[Usługa Azure Blockchain](../service/overview.md) | PaaS | Usługa Azure Blockchain Service Preview upraszcza tworzenie, zarządzanie i zarządzanie sieciami łańcucha bloków konsorcjum. Usługa Azure Blockchain Service umożliwia korzystanie z usługi Azure Blockchain w zakresie rozwiązań wymagających paas, zarządzania konsorcjum lub prywatności umów i transakcji.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS i PaaS | Azure Blockchain Workbench Preview to zbiór usług i funkcji platformy Azure zaprojektowanych w celu ułatwienia tworzenia i wdrażania aplikacji blockchain w celu udostępniania procesów biznesowych i danych innym organizacjom. Użyj programu Azure Blockchain Workbench do tworzenia prototypów rozwiązania blockchain lub weryfikacji koncepcji aplikacji blockchain.

## <a name="solution-architecture"></a>Architektura rozwiązania

Za pomocą szablonu rozwiązania Ethereum można wdrożyć wielojednoosobową sieć konsorcjum Ethereum opartą na jednym lub wielu regionach.

![architektura wdrażania](./media/ethereum-poa-deployment/deployment-architecture.png)

Każde wdrożenie członka konsorcjum obejmuje:

* Maszyny wirtualne do uruchamiania modułów sprawdzania poprawności PoA
* Moduł równoważenia obciążenia platformy Azure do dystrybucji żądań RPC, komunikacji równorzędnej i nadzoru DApp
* Usługa Azure Key Vault do zabezpieczania tożsamości walidatora
* Usługa Azure Storage do przechowywania trwałych informacji o sieci i koordynowania leasingu
* Usługa Azure Monitor do agregowania dzienników i statystyk wydajności
* Brama sieci wirtualnej (opcjonalnie) umożliwiająca korzystanie z połączeń sieci VPN w prywatnych sieciach wirtualnych

Domyślnie punkty końcowe RPC i komunikacji równorzędnej są dostępne za pośrednictwem publicznego adresu IP, aby umożliwić uproszczoną łączność między subskrypcjami i chmurami. W przypadku kontroli dostępu na poziomie aplikacji można użyć [umów uprawnień parzystości](https://wiki.parity.io/Permissioning). Obsługiwane są sieci wdrożone za sieciami VPN, które wykorzystują bramy sieci wirtualnych do łączności między subskrypcjami. Ponieważ wdrożenia sieci VPN i sieci wirtualnej są bardziej złożone, można rozpocząć od publicznego modelu IP podczas tworzenia prototypów rozwiązania.

Kontenery platformy Docker są używane do niezawodności i modułowości. Usługa Azure Container Registry służy do hostowania i obsługi obrazów wersjona w ramach każdego wdrożenia. Obrazy kontenerów składają się z:

* Orchestrator — generuje tożsamości i kontrakty zarządzania. Przechowuje tożsamości w magazynie tożsamości.
* Klient parzystości — dzierżawy tożsamości z magazynu tożsamości. Odnajduje i łączy się z rówieśnikami.
* EthStats Agent — zbiera lokalne dzienniki i statystyki za pośrednictwem rpc i wypycha informacje do usługi Azure Monitor.
* Governance DApp — interfejs sieci Web do interakcji z kontraktami nadzoru.

### <a name="validator-nodes"></a>Węzły walidatora

W protokole proof-of-authority węzły walidatora zajmują miejsce tradycyjnych węzłów górniczych. Każdy walidator ma unikatową tożsamość Ethereum, która umożliwia mu udział w procesie tworzenia bloku. Każdy członek konsorcjum może aprowizować dwa lub więcej węzłów walidatora w pięciu regionach w celu zapewnienia nadmiarowości geograficznej. Węzły walidatora komunikują się z innymi węzłami walidatora, aby osiągnąć konsensus co do stanu podstawowej księgi rozproszonej. Aby zapewnić sprawiedliwy udział w sieci, każdemu członkowi konsorcjum zabrania się używania większej liczby walidatorów niż pierwszy członek sieci. Na przykład jeśli pierwszy element członkowski wdraża trzy moduły sprawdzania poprawności, każdy element członkowski może mieć tylko maksymalnie trzy moduły sprawdzania poprawności.

### <a name="identity-store"></a>Magazyn tożsamości

Magazyn tożsamości jest wdrażany w subskrypcji każdego członka, który bezpiecznie przechowuje wygenerowane tożsamości Ethereum. Dla każdego walidatora kontener aranżacji generuje klucz prywatny Ethereum i przechowuje go w usłudze Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Wdrażanie sieci konsorcjum Ethereum

Załóżmy, że w tym trzęsieniu tworzysz wielopartyjną sieć konsorcjum Ethereum. Następujący przepływ jest przykładem wdrożenia wielopartyjnego:

1. Po trzech członków generuje konto Ethereum za pomocą MetaMask
1. *Członek A* wdraża Ethereum PoA, zapewniając swoje adresy publiczne Ethereum
1. *Państwo członkowskie A* udostępnia adres URL konsorcjum *państwom członkowskim B* i *c.*
1. *Państwa członkowskie B* i *c* wdrażają Ethereum PoA, podając adres publiczny Ethereum i adres URL konsorcjum państwa *członkowskiego A*
1. *Członek A* głosuje w *państwie członkowskim B* jako administrator
1. *Zarówno członek A,* jak i *członek B* głosują *na członka C* jako administratora

W następnych sekcjach pokazano, jak skonfigurować ślad pierwszego członka w sieci.

### <a name="create-resource"></a>Tworzenie zasobu

W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** w lewym górnym rogu.

Wybierz **Konsorcjum Blockchain** > **Ethereum Proof-of-Authority Consortium (wersja zapoznawcza).**

### <a name="basics"></a>Podstawy

W obszarze **Podstawy**określ wartości parametrów standardowych dla każdego wdrożenia.

![Podstawy](./media/ethereum-poa-deployment/basic-blade.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Tworzenie nowej sieci lub dołączanie do istniejącej sieci | Można utworzyć nową sieć konsorcjum lub dołączyć do istniejącej sieci konsorcjum. Dołączenie do istniejącej sieci wymaga dodatkowych parametrów. | Tworzenie nowego elementu
Adres e-mail | Po zakończeniu wdrażania zostanie wyświetlone powiadomienie e-mail z informacjami o wdrożeniu. | Prawidłowy adres e-mail
Nazwa użytkownika maszyny Wirtualnej | Nazwa użytkownika administratora każdej wdrożonej maszyny Wirtualnej | 1-64 znaki alfanumeryczne
Typ uwierzytelniania | Metoda uwierzytelniania na maszynie wirtualnej. | Hasło
Hasło | Hasło dla konta administratora dla każdej z wdrożonych maszyn wirtualnych. Wszystkie maszyny wirtualne początkowo mają to samo hasło. Hasło można zmienić po zainicjowaniu obsługi administracyjnej. | 12-72 znaków 
Subskrypcja | Subskrypcja, do której ma być wdrażana sieć konsorcjum |
Grupa zasobów| Grupa zasobów, do której ma być wdrażana sieć konsorcjum. | myResourceGroup
Lokalizacja | Region platformy Azure dla grupy zasobów. | Zachodnie stany USA 2

Kliknij przycisk **OK**.

### <a name="deployment-regions"></a>Regiony wdrażania

W obszarze *Regiony wdrażania*określ liczbę regionów i lokalizacji dla każdego z nich. Można wdrożyć w maksymalnie pięciu regionach. Pierwszy region powinien być zgodny z lokalizacją grupy zasobów z sekcji *Podstawy.* W przypadku sieci deweloperskich lub testowych można użyć jednego regionu na element członkowski. W przypadku produkcji należy wdrożyć w dwóch lub więcej regionach w celu uzyskania wysokiej dostępności.

![regionów rozmieszczenia](./media/ethereum-poa-deployment/deployment-regions.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Liczba regionów|Liczba regionów do wdrożenia sieci konsorcjum| 2
Pierwszy region | Pierwszy region do wdrożenia sieci konsorcjum | Zachodnie stany USA 2
Drugi region | Drugi region do wdrożenia sieci konsorcjum. Dodatkowe regiony są widoczne, gdy liczba regionów jest dwa lub większa. | Wschodnie stany USA 2

Kliknij przycisk **OK**.

### <a name="network-size-and-performance"></a>Rozmiar i wydajność sieci

W obszarze *Rozmiar i wydajność sieci*określ dane wejściowe dla wielkości sieci konsorcjum. Rozmiar magazynu węzła walidatora określa potencjalny rozmiar łańcucha bloków. Rozmiar można zmienić po wdrożeniu.

![Rozmiar i wydajność sieci](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Liczba węzłów walidatora z równoważenia obciążenia | Liczba węzłów walidatora do inicjowania obsługi administracyjnej jako części sieci. | 2
Wydajność magazynu węzła walidatora | Typ dysku zarządzanego dla każdego z wdrożonych węzłów walidatora. Aby uzyskać szczegółowe informacje na temat cen, zobacz [ceny magazynowania](https://azure.microsoft.com/pricing/details/managed-disks/) | Dysk SSD w warstwie Standardowa
Rozmiar maszyny wirtualnej węzła walidatora | Rozmiar maszyny wirtualnej używany dla węzłów walidatora. Szczegółowe informacje na temat cen można znaleźć w [cenniku maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Standard D2 v3

Warstwa maszyny wirtualnej i magazynu wpływa na wydajność sieci.  Skorzystaj z poniższej tabeli, aby wybrać efektywność kosztową:

Jednostka SKU maszyny wirtualnej|Warstwa magazynu|Price|Przepływność|Opóźnienie
---|---|---|---|---
F1|Dysk SSD w warstwie Standardowa|Niskie|Niskie|wysoka
D2_v3|Dysk SSD w warstwie Standardowa|średni|średni|średni
F16|Dysk SSD w warstwie Premium|wysoka|wysoka|Niskie

Kliknij przycisk **OK**.

### <a name="ethereum-settings"></a>Ustawienia Ethereum

W obszarze *Ustawienia Ethereum*określ ustawienia konfiguracji związane z Ethereum.

![Ustawienia Ethereum](./media/ethereum-poa-deployment/ethereum-settings.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Identyfikator członka konsorcjum | Identyfikator skojarzony z każdym członkiem uczestniczącym w sieci konsorcjów. Służy do konfigurowania przestrzeni adresów IP, aby uniknąć kolizji. W przypadku sieci prywatnej identyfikator członka powinien być unikatowy w różnych organizacjach w tej samej sieci.  Unikatowy identyfikator członka jest potrzebny nawet wtedy, gdy ta sama organizacja wdraża w wielu regionach. Zanotuj wartość tego parametru, ponieważ musisz udostępnić go innym członkom łączącym, aby upewnić się, że nie ma kolizji. Prawidłowy zakres wynosi od 0 do 255. | 0
Identyfikator sieci | Identyfikator sieci dla wdrażanych sieci Ethereum konsorcjum. Każda sieć Ethereum ma swój własny identyfikator sieci, z 1 jest identyfikatorem sieci publicznej. Prawidłowy zakres wynosi od 5 do 999 999 999 | 10101010
Adres Ethereum administratora | Adres konta Ethereum używany do udziału w zarządzaniu PoA. MetaMask służy do generowania adresu Ethereum. |
Opcje zaawansowane | Zaawansowane opcje ustawień Ethereum | Włączanie
Wdrażanie przy użyciu publicznego adresu IP | Jeśli wybrano prywatną sieć wirtualną, sieć jest wdrażana za bramą sieci wirtualnej i usuwa dostęp komunikacji równorzędnej. W przypadku prywatnej sieci wirtualnej wszyscy członkowie muszą używać bramy sieci wirtualnej, aby połączenie było zgodne. | Publiczny adres IP
Limit gazu blokowego | Limit gazu bloku rozruchowego sieci. | 50000000
Okres ponownego zamówienia bloku (s) | Częstotliwość, z jaką puste bloki będą tworzone, gdy nie ma żadnych transakcji w sieci. Wyższa częstotliwość będzie miała szybszą ostateczność, ale zwiększone koszty pamięci masowej. | 15
Umowa o zezwoleniu na transakcję | Bajtowy kod umowy o uprawnieniach do transakcji. Ogranicza inteligentne wdrażanie i wykonywanie umów do dozwolonej listy kont Ethereum. |

Kliknij przycisk **OK**.

### <a name="monitoring"></a>Monitorowanie

Monitorowanie umożliwia skonfigurowanie zasobu dziennika dla sieci. Agent monitorowania zbiera i powierzchnie przydatne metryki i dzienniki z sieci, zapewniając możliwość szybkiego sprawdzania kondycji sieci lub debugowania problemów.

![Monitor platformy Azure](./media/ethereum-poa-deployment/azure-monitor.png)

Parametr | Opis | Przykładowa wartość
----------|-------------|--------------
Monitorowanie | Możliwość włączenia monitorowania | Włączanie
Łączenie się z istniejącymi dziennikami usługi Azure Monitor | Opcja utworzenia nowego wystąpienia dziennika usługi Azure Monitor lub dołączenia do istniejącego wystąpienia | Tworzenie nowego elementu
Lokalizacja | Region, w którym jest wdrażane nowe wystąpienie | Wschodnie stany USA
Istniejący identyfikator obszaru roboczego analizy dzienników (Łączenie się z istniejącymi dziennikami usługi Azure Monitor = Dołącz istniejące)|Identyfikator obszaru roboczego istniejącego wystąpienia dzienników usługi Azure Monitor||Nie dotyczy
Istniejący klucz podstawowy analizy dzienników (Łączenie z istniejącymi dziennikami usługi Azure Monitor = Dołącz istniejące)|Klucz podstawowy używany do łączenia się z istniejącym wystąpieniem dzienników usługi Azure Monitor||Nie dotyczy

Kliknij przycisk **OK**.

### <a name="summary"></a>Podsumowanie

Kliknij podsumowanie, aby przejrzeć określone dane wejściowe i uruchomić podstawowe sprawdzanie poprawności przed wdrożeniem. Przed wdrożeniem można pobrać szablon i parametry.

Wybierz **pozycję Utwórz** do wdrożenia.

Jeśli wdrożenie obejmuje bramy sieci wirtualnej, wdrożenie może potrwać od 45 do 50 minut.

## <a name="deployment-output"></a>Dane wyjściowe wdrożenia

Po zakończeniu wdrażania można uzyskać dostęp do niezbędnych parametrów za pomocą witryny Azure portal.

### <a name="confirmation-email"></a>E-mail z potwierdzeniem

Jeśli podasz adres e-mail[(Sekcja Podstawy),](#basics)zostanie wysłana wiadomość e-mail zawierająca informacje o wdrożeniu i łącza do tej dokumentacji.

![adres e-mail wdrożenia](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Po pomyślnym zakończeniu wdrażania i udostępnieniu wszystkich zasobów można wyświetlić parametry wyjściowe w grupie zasobów.

1. Przejdź do grupy zasobów w portalu.
1. Wybierz **opcję Przegląd > wdrożenia .**

    ![Omówienie grupy zasobów](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Wybierz **wdrożenie microsoft-azure-blockchain.azure-blockchain-....**
1. Wybierz sekcję **Wyjścia.**

    ![Wyjścia wdrożeniowe](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Rozwój konsorcjum

Aby rozwinąć konsorcjum, należy najpierw połączyć sieć fizyczną. Jeśli wdrażanie za siecią VPN, zobacz sekcji [Łączenie bramy sieci wirtualnej](#connecting-vnet-gateways) skonfigurować połączenie sieciowe jako część nowego wdrożenia elementu członkowskiego. Po zakończeniu wdrażania użyj [nadzoru DApp,](#governance-dapp) aby stać się administratorem sieci.

### <a name="new-member-deployment"></a>Wdrożenie nowego elementu członkowskiego

Udostępnij następujące informacje członkowi łączącemu. Informacje znajdują się w wiadomości e-mail po wdrożeniu lub w danych wyjściowych wdrożenia portalu.

* Adres URL danych konsorcjum
* Liczba wdrożonych węzłów
* Identyfikator zasobu bramy sieci wirtualnej (w przypadku korzystania z sieci VPN)

Wdrażający element członkowski powinien używać tego samego szablonu rozwiązania konsorcjum Ethereum Proof-of-Authority podczas wdrażania ich obecności w sieci przy użyciu następujących wskazówek:

* Wybierz **dołącz istniejące**
* Wybierz taką samą liczbę węzłów walidatora, jak reszta członków sieci, aby zapewnić uczciwą reprezentację
* Użyj tego samego adresu AdministratorA Ethereum
* Użyj podanego *adresu URL danych konsorcjum* w *ustawieniach Ethereum*
* Jeśli reszta sieci znajduje się za siecią VPN, wybierz **prywatną sieć wirtualną** w sekcji zaawansowanej

### <a name="connecting-vnet-gateways"></a>Łączenie bram sieci wirtualnej

Ta sekcja jest wymagana tylko wtedy, gdy wdrożono przy użyciu prywatnej sieci wirtualnej. Tę sekcję można pominąć, jeśli używasz publicznych adresów IP.

W przypadku sieci prywatnej różne elementy członkowskie są połączone za pośrednictwem połączeń bramy sieci wirtualnej. Zanim członek może dołączyć do sieci i zobaczyć ruch transakcji, istniejący członek musi wykonać ostateczną konfigurację na swojej bramie sieci VPN, aby zaakceptować połączenie. Węzły Ethereum elementu członkowskiego dołączania nie będą uruchamiane, dopóki nie zostanie nawiązane połączenie. Aby zmniejszyć szanse pojedynczego punktu awarii, należy utworzyć nadmiarowe połączenia sieciowe w konsorcjum.

Po wdrożeniu nowego elementu członkowskiego istniejący element członkowski musi ukończyć połączenie dwukierunkowe, konfigurując połączenie bramy sieci wirtualnej z nowym członkiem. Istniejący członek potrzebuje:

* Identyfikator zasobów bramy sieci wirtualnej łącznika. Zobacz [dane wyjściowe wdrożenia](#deployment-output).
* Klucz połączenia udostępnionego.

Istniejący element członkowski musi uruchomić następujący skrypt programu PowerShell, aby zakończyć połączenie. Usługi Azure Cloud Shell można użyć znajdującego się w prawym górnym rogu paska nawigacyjnego w portalu.

![powłoka chmury](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Monitorowanie usługi

Portal usługi Azure Monitor można zlokalizować, klikając łącze w wiadomości e-mail wdrożenia lub lokalizując parametr w danych wyjściowych wdrożenia [OMS_PORTAL_URL].

Portal najpierw wyświetli statystyki sieci wysokiego poziomu i przegląd węzłów.

![Kategorie monitorów](./media/ethereum-poa-deployment/monitor-categories.png)

Wybranie **przeglądu węzła** pokazuje statystyki infrastruktury dla węzłów.

![Statystyki węzłów](./media/ethereum-poa-deployment/node-stats.png)

Wybranie **statystyk sieciowych** pokazuje statystyki sieci Ethereum.

![Statystyki sieciowe](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

Można zbadać dzienniki monitorowania, aby zbadać błędy lub alerty o progu konfiguracji. Następujące zapytania są przykładami, które można uruchomić w narzędziu *wyszukiwania dzienników:*

Listy bloków, które zostały zgłoszone przez więcej niż jedno zapytanie walidatora może być przydatne do znalezienia rozwidle łańcuchowe.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Uzyskaj średnią liczbę elementów równorzędnych dla określonego węzła walidatora uśrednioną przez 5-minutowe zasobniki.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>Dostęp do SSH

Ze względów bezpieczeństwa dostęp do portu SSH jest domyślnie odrzucany przez regułę zabezpieczeń grupy sieciowej. Aby uzyskać dostęp do wystąpień maszyny wirtualnej w sieci PoA, należy zmienić następujące zabezpieczenia jest *regułą Zezwalaj*.

1. Przejdź do sekcji **Omówienie** wdrożonej grupy zasobów w witrynie Azure portal.

    ![przegląd ssh](./media/ethereum-poa-deployment/ssh-overview.png)

1. Wybierz **grupę zabezpieczeń sieci** dla regionu maszyny Wirtualnej, do której chcesz uzyskać dostęp.

    ![ssh nsg](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Wybierz regułę **allow-ssh.**

    ![ssh-allow](./media/ethereum-poa-deployment/ssh-allow.png)

1. Zmień **akcję,** aby **zezwolić**

    ![ssh włączyć zezwalać](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Wybierz **pozycję Zapisz**. Wprowadzenie zmian może potrwać kilka minut.

Można zdalnie połączyć się z maszynami wirtualnymi dla węzłów walidatora za pośrednictwem protokołu SSH za pomocą podanej nazwy użytkownika administratora i klucza hasła/SSH. Polecenie SSH, aby uzyskać dostęp do pierwszego węzła walidatora znajduje się w danych wyjściowych wdrożenia szablonu. Przykład:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Aby uzyskać dodatkowe węzły transakcji, należy zwiększać numer portu o jeden.

Jeśli wdrożono w więcej niż jednym regionie, zmień polecenie na nazwę DNS lub adres IP modułu równoważenia obciążenia w tym regionie. Aby znaleźć nazwę DNS lub adres IP innych regionów, znajdź zasób z konwencją ** \* \* \* \* \*nazewnictwa -lbpip-reg\# ** i wyświetl jego nazwę DNS i właściwości adresu IP.

## <a name="azure-traffic-manager-load-balancing"></a>Równoważenie obciążenia usługi Azure Traffic Manager

Usługa Azure Traffic Manager może pomóc zmniejszyć czas przestojów i poprawić czas reakcji sieci PoA, kierując ruch przychodzący przez wiele wdrożeń w różnych regionach. Wbudowane kontrole kondycji i automatyczne przekierowanie pomagają zapewnić wysoką dostępność punktów końcowych RPC i dapp nadzoru. Ta funkcja jest przydatna, jeśli wdrożono w wielu regionach i gotowe do produkcji.

Użyj usługi Traffic Manager, aby poprawić dostępność sieci PoA dzięki automatycznemu trybowi pracy awaryjnej. Za pomocą usługi Traffic Manager można również zwiększyć szybkość reakcji sieci, kierując użytkowników końcowych do lokalizacji platformy Azure z najniższym opóźnieniem sieci.

Jeśli zdecydujesz się utworzyć profil usługi Traffic Manager, możesz użyć nazwy DNS profilu, aby uzyskać dostęp do sieci. Po dodaniu innych członków konsorcjum do sieci, Usługa Traffic Manager może również służyć do równoważenia obciążenia w ich wdrożonych modułów sprawdzania poprawności.

### <a name="creating-a-traffic-manager-profile"></a>Tworzenie profilu usługi Traffic Manager

1. W [witrynie Azure portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób** w lewym górnym rogu.
1. Wyszukaj **profil usługi Traffic Manager**.

    ![Wyszukiwanie usługi Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Nadaj profilowi unikatową nazwę i wybierz grupę zasobów, która została użyta do wdrożenia PoA.

1. Wybierz **pozycję Utwórz** do wdrożenia.

    ![Tworzenie Menedżera ruchu](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Po wdrożeniu wybierz wystąpienie w grupie zasobów. Nazwę DNS dostępu do menedżera ruchu można znaleźć na karcie Przegląd.

    ![Lokalizowanie usługi DNS menedżera ruchu](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Wybierz kartę **Punkty końcowe** i wybierz przycisk **Dodaj.**
1. Nadaj punktowi końcowemu unikatową nazwę.
1. W przypadku **typu zasobu docelowego**wybierz pozycję **Publiczny adres IP**.
1. Wybierz publiczny adres IP modułu równoważenia obciążenia pierwszego regionu.

    ![Menedżer ruchu routingu](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Powtórz tę czynność dla każdego regionu w wdrożonej sieci. Gdy punkty końcowe są w **stanie włączone,** są one automatycznie ładowane i regionu równoważenia na nazwę DNS menedżera ruchu. Teraz można użyć tej nazwy DNS zamiast parametru [CONSORTIUM_DATA_URL] w innych krokach artykułu.

## <a name="data-api"></a>Interfejs API danych

Każdy członek konsorcjum zawiera informacje niezbędne do łączenia się z siecią przez inne osoby. Aby włączyć łatwość łączności, każdy element członkowski hostuje zestaw informacji o połączeniu w punkcie końcowym interfejsu API danych.

Istniejący element członkowski udostępnia [CONSORTIUM_DATA_URL] przed wdrożeniem elementu członkowskiego. Po wdrożeniu element członkowski dołączający będzie pobierał informacje z interfejsu JSON w następującym punkcie końcowym:

`<CONSORTIUM_DATA_URL>/networkinfo`

Odpowiedź zawiera informacje przydatne do łączenia członków (blok Genesis, ABI zestaw walidatora, bootnodes) i informacje przydatne dla istniejącego elementu członkowskiego (adresy walidatora). Ta standaryzacja służy do rozszerzania konsorcjum między dostawcami chmury. Ten interfejs API zwraca sformatowaną odpowiedź JSON z następującą strukturą:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>Zarządzanie DApp

Sednem dowodu władzy jest zdecentralizowane zarządzanie. Ponieważ dowód upoważnienia opiera się na dozwolonej liście organów sieciowych, aby utrzymać sieć w dobrej kondycji, ważne jest zapewnienie sprawiedliwego mechanizmu wprowadzania zmian do tej listy uprawnień. Każde wdrożenie jest dostarczany z zestawem inteligentnych kontraktów i portalem do zarządzania w łańcuchu tej dozwolonej listy. Gdy proponowana zmiana osiągnie większość głosów członków konsorcjum, zmiana zostaje uchwalona. Głosowanie umożliwia dodawanie nowych uczestników konsensusu lub usuwanie uczestników, którzy są zagrożeni, w przejrzysty sposób, który zachęca do uczciwej sieci.

Zarządzanie DApp to zestaw wstępnie wdrożonych [inteligentnych kontraktów](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) i aplikacji sieci web, które są używane do rządzenia władzami w sieci. Urzędy są podzielone na tożsamości administratora i węzły walidatora.
Administratorzy mają uprawnienia do delegowania uczestnictwa konsensusu do zestawu węzłów walidatora. Administratorzy mogą również głosować na innych administratorów do lub z sieci.

![Zarządzanie DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Zdecentralizowane zarządzanie:** Zmiany w ucho.
* **Delegowanie walidatora:** Władze mogą zarządzać ich węzłów walidatora, które są skonfigurowane w każdym wdrożeniu PoA.
* **Historia zmian podlegania inspekcji:** Każda zmiana jest rejestrowana w łańcuchu bloków zapewniającym przejrzystość i możliwość audytu.

### <a name="getting-started-with-governance"></a>Wprowadzenie do zarządzania

Aby przeprowadzać wszelkiego rodzaju transakcje za pośrednictwem aplikacji DApp, należy użyć portfela Ethereum. Najprostszym podejściem jest użycie portfela w przeglądarce, takiego jak [MetaMask;](https://metamask.io) Jednak ponieważ te inteligentne kontrakty są wdrażane w sieci można również zautomatyzować interakcje z umową nadzoru.

Po zainstalowaniu MetaMask przejdź do nadzoru DApp w przeglądarce.  Adres URL można zlokalizować za pośrednictwem witryny Azure portal w danych wyjściowych wdrożenia.  Jeśli nie masz zainstalowanego portfela w przeglądarce, nie będziesz w stanie wykonać żadnych działań; można jednak wyświetlić stan administratora.  

### <a name="becoming-an-admin"></a>Zostań administratorem

Jeśli jesteś pierwszym członkiem, który został wdrożony w sieci, automatycznie stajesz się administratorem, a węzły parzystości są wyświetlane jako moduły sprawdzania poprawności. Jeśli dołączasz do sieci, musisz uzyskać głos jako administrator większością głosów (ponad 50%) istniejącego zestawu administracyjnego. Jeśli nie zdecydujesz się zostać administratorem, twoje węzły nadal synchronizują i weryfikują łańcuch bloków; jednak nie uczestniczą w procesie tworzenia bloku. Aby rozpocząć proces głosowania, aby zostać administratorem, wybierz **opcję Nominuj** i wprowadź adres ethereum i alias.

![Nominowanie](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Kandydatów

Wybranie karty **Kandydaci** pokazuje bieżący zestaw administratorów kandydatów.  Gdy kandydat uzyska większość głosów obecnych administratorów, kandydat zostanie awansowany do administratora.  Aby zagłosować na kandydata, wybierz wiersz i wybierz **opcję Głosuj w**. Jeśli zmienisz zdanie w sprawie głosowania, wybierz kandydata i wybierz **odstąpienie od głosu.**

![Kandydatów](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Administratorzy

Karta **Administratorzy** pokazuje bieżący zestaw administratorów i umożliwia głosowanie przeciwko.  Gdy administrator traci ponad 50% wsparcia, są one usuwane jako administrator w sieci. Wszystkie węzły walidatora, które jest właścicielem administratora utracić stan walidatora i stają się węzłami transakcji w sieci. Administrator może zostać usunięty z dowolnej liczby powodów; jednak to konsorcjum musi uzgodnić politykę z wyprzedzeniem.

![Administratorzy](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Walidatory

Wybranie karty **Walidatory** powoduje wyświetlenie bieżących wdrożonych węzłów parzystości dla wystąpienia i ich bieżącego stanu (typ węzła). Każdy członek konsorcjum ma inny zestaw modułów sprawdzania poprawności na tej liście, ponieważ ten widok reprezentuje bieżącego wdrożonego członka konsorcjum. Jeśli wystąpienie jest nowo wdrożone i nie dodano modułów sprawdzania poprawności, zostanie dostępna opcja **Dodaj moduły sprawdzania poprawności**. Dodawanie modułów sprawdzania poprawności automatycznie wybiera regionalnie zrównoważony zestaw węzłów parzystości i przypisuje je do zestawu walidatora. Jeśli wdrożono więcej węzłów niż dozwolona pojemność, pozostałe węzły stają się węzłami transakcji w sieci.

Adres każdego walidatora jest automatycznie przypisywany za pośrednictwem [magazynu tożsamości](#identity-store) na platformie Azure.  Jeśli węzeł ulegnie upadkowi, rezygnuje ze swojej tożsamości, umożliwiając innym węzłom we wdrożeniu jego miejsce. Proces ten gwarantuje, że twój udział w konsensusie jest wysoce dostępny.

![Walidatory](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Nazwa konsorcjum

Każdy administrator może zaktualizować nazwę konsorcjum.  Wybierz ikonę koła zębatego w lewym górnym rogu, aby zaktualizować nazwę konsorcjum.

### <a name="account-menu"></a>Menu Konto

W prawym górnym rogu znajduje się alias konta Ethereum i identyfikator.  Jeśli jesteś administratorem, masz możliwość aktualizacji aliasu.

![Konto](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Rozwój Ethereum<a id="tutorials"></a>

Aby skompilować, wdrożyć i przetestować inteligentne kontrakty, oto kilka opcji, które można wziąć pod uwagę w przypadku rozwoju Ethereum:
* [Truffle Suite](https://www.trufflesuite.com/docs/truffle/overview) - Środowisko programistyczne Ethereum oparte na kliencie
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) - Oparte na przeglądarce i lokalne środowisko programistyczne Ethereum

### <a name="compile-deploy-and-execute-smart-contract"></a>Kompilowanie, wdrażanie i wykonywanie inteligentnego kontraktu

W poniższym przykładzie utworzysz prosty kontrakt inteligentny. Truffle służy do kompilowania i wdrażania inteligentnego kontraktu w sieci blockchain. Po wdrożeniu wywołanie funkcji inteligentnego kontraktu za pośrednictwem transakcji.

#### <a name="prerequisites"></a>Wymagania wstępne

* Zainstaluj [Pythona 2.7.15](https://www.python.org/downloads/release/python-2715/). Python jest potrzebny dla Trufli i Web3. Wybierz opcję instalacji, aby uwzględnić Pythona w ścieżce.
* Zainstaluj Trufla v5.0.5 `npm install -g truffle@v5.0.5`. Trufla wymaga zainstalowania kilku narzędzi, w tym [Node.js](https://nodejs.org), [Git.](https://git-scm.com/) Aby uzyskać więcej informacji, zobacz [dokumentacja trufli](https://github.com/trufflesuite/truffle).

### <a name="create-truffle-project"></a>Utwórz projekt trufli

Przed kompilacją i wdrożeniem inteligentnego kontraktu należy utworzyć projekt trufli.

1. Otwórz wiersz polecenia lub powłokę.
1. Utwórz folder o nazwie `HelloWorld`.
1. Zmień katalog na `HelloWorld` nowy folder.
1. Zainicjować nowy projekt trufli `truffle init`za pomocą polecenia .

    ![Tworzenie nowego projektu trufli](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Dodawanie inteligentnego kontraktu

Stwórz swoje inteligentne kontrakty w podkatalogu **kontraktów** projektu Trufli.

1. Utwórz plik w `postBox.sol` nazwanym podkatalogu **kontraktów** projektu trufli.
1. Dodaj następujący kod Solidity do **postBox.sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Wdrażanie inteligentnego kontraktu przy użyciu trufli

Projekty trufli zawierają plik konfiguracyjny dla szczegółów połączenia sieciowego łańcucha bloków. Zmodyfikuj plik konfiguracyjny, aby uwzględnić informacje o połączeniu dla sieci.

> [!WARNING]
> Nigdy nie wysyłaj klucza prywatnego Ethereum przez sieć. Upewnij się, że każda transakcja jest podpisana lokalnie, a podpisana transakcja jest wysyłana przez sieć.

1. Potrzebujesz frazy mnemonicznej dla [konta administratora Ethereum używanego podczas wdrażania sieci blockchain.](#ethereum-settings) Jeśli do utworzenia konta użyto MetaMask, można pobrać mnemonic z MetaMask. Wybierz ikonę konta administratora w prawym górnym rogu rozszerzenia MetaMask i wybierz **pozycję Ustawienia > Zabezpieczenia & Prywatność > Odsłonięcie wyrazów źródłowych**.
1. Zastąp `truffle-config.js` zawartość projektu trufli następującą zawartością. Zastąp punkt końcowy symbolu zastępczego i wartości mnemoniczne.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Ponieważ korzystamy z dostawcy trufli HD Wallet, zainstaluj moduł `npm install truffle-hdwallet-provider --save`w swoim projekcie za pomocą polecenia .

Truffle używa skryptów migracji do wdrażania inteligentnych kontraktów w sieci blockchain. Do wdrożenia nowego kontraktu inteligentnego potrzebny jest skrypt migracji.

1. Dodaj nową migrację, aby wdrożyć nowy kontrakt. Utwórz `2_deploy_contracts.js` plik w **podkatalogu migracji** projektu Truffle.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Wdrażanie w sieci PoA za pomocą polecenia migracji trufli. W wierszu polecenia w katalogu projektu trufli uruchom:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Wywoływanie inteligentnej funkcji kontraktu

Teraz, gdy umowa inteligentna jest wdrożona, można wysłać transakcję, aby wywołać funkcję.

1. W katalogu projektu Truffle utwórz nowy `sendtransaction.js`plik o nazwie .
1. Dodaj następującą zawartość do **pliku sendtransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Wykonaj skrypt za pomocą polecenia wykonanie trufli.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Wykonywanie skryptu do wywołania funkcji za pośrednictwem transakcji](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Pomoc techniczna aplikacji WebAssembly (WASM)

Obsługa webassembly jest już włączona w nowo wdrożonych sieciach PoA. Pozwala na inteligentne rozwoju umowy w dowolnym języku, który transpiles do Web-Assembly (Rust, C, C++). Aby uzyskać więcej informacji, zobacz: [Omówienie parzystości webassembly](https://wiki.parity.io/WebAssembly-Home) i [samouczek z parzystości Tech](https://github.com/paritytech/pwasm-tutorial)

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Zauważyłem, że istnieje wiele transakcji w sieci, których nie wysłałem. Skąd pochodzą?

Odblokowanie [osobistego interfejsu API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)jest niebezpieczne. Boty słuchają odblokowanych kont Ethereum i próbują wysysać fundusze. Bot zakłada, że te konta zawierają rzeczywisty eter i próbuje być pierwszym, aby syfon równowagi. Nie należy włączać osobistego interfejsu API w sieci. Zamiast tego wstępnie podpisać transakcje ręcznie przy użyciu portfela, takich jak MetaMask lub programowo.

### <a name="how-to-ssh-onto-a-vm"></a>Jak SSH na maszynie wirtualnej?

Port SSH nie jest narażony ze względów bezpieczeństwa. Postępuj zgodnie [z tym przewodnikiem, aby włączyć port SSH](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Jak skonfigurować członka inspekcji lub węzły transakcji?

Węzły transakcji to zestaw klientów parzystości, którzy są równorzędni z siecią, ale nie uczestniczą w konsensusie. Te węzły mogą nadal służyć do przesyłania transakcji Ethereum i odczytywania stanu inteligentnego kontraktu. Mechanizm ten ma na celu zapewnienie możliwości audytu członkom konsorcjum niebędącym organami w sieci. Aby to osiągnąć, wykonaj kroki opisane w [growing the Consortium](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Dlaczego transakcje MetaMask zajmują dużo czasu?

Aby upewnić się, że transakcje są odbierane w odpowiedniej kolejności, każda transakcja Ethereum jest zawiera przyrostowy nonce. Jeśli używasz konta w MetaMask w innej sieci, musisz zresetować wartość nonce. Kliknij ikonę ustawień (trzy paski), Ustawienia, Resetuj konto. Historia transakcji zostanie wyczyszczona i teraz możesz ponownie przesłać transakcję.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Czy muszę podawać opłatę za gaz w MetaMask?

Eter nie służy celowi w konsorcjum proof-of-authority. W związku z tym nie ma potrzeby określania opłaty gazowej przy składaniu transakcji w MetaMask.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Co należy zrobić, jeśli wdrożenie nie powiedzie się z powodu niepowodzenia w aprowizje usługi Azure OMS?

Monitorowanie jest funkcją opcjonalną. W niektórych rzadkich przypadkach, gdy wdrożenie nie powiedzie się z powodu braku możliwości pomyślnego aprowizowania zasobu usługi Azure Monitor, można ponownie wdrożyć bez usługi Azure Monitor.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Czy publiczne wdrożenia adresów IP są zgodne z wdrożeniami sieci prywatnych?

Nie. Komunikacja równorzędna wymaga komunikacji dwukierunkowej, więc cała sieć musi być publiczna lub prywatna.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Jaka jest oczekiwana przepływność transakcji proof-of-authority?

Przepływność transakcji będzie w dużym stopniu zależna od typów transakcji i topologii sieci. Korzystając z prostych transakcji, porównaliśmy średnio 400 transakcji na sekundę z siecią wdrożoną w wielu regionach.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Jak subskrybować zdarzenia inteligentnego kontraktu?

Ethereum Proof-of-Authority obsługuje teraz gniazda internetowe.  Sprawdź dane wyjściowe wdrożenia, aby zlokalizować adres URL i port gniazda internetowego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej rozwiązań azure blockchain, zobacz [dokumentację narzędzia Azure Blockchain](https://docs.microsoft.com/azure/blockchain/).
