---
title: 'Weryfikowanie łączności: ExpressRoute Azure przewodnik rozwiązywania problemów | Dokumentacja firmy Microsoft'
description: Ta strona zawiera instrukcje dotyczące rozwiązywania problemów i weryfikowanie łączności kompleksowe obwodu usługi ExpressRoute.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: cherylmc
ms.openlocfilehash: 10d4779d05d95822ffd487db1ce8992d199c495f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753454"
---
# <a name="verifying-expressroute-connectivity"></a>Weryfikowanie połączenia usługi ExpressRoute
ExpressRoute, który rozciąga się sieci lokalnej do firmy Microsoft w chmurze prywatnej połączenie, które umożliwiają to dostawca połączenia, obejmuje następujące trzy strefy odrębnych sieci:

-   Sieci klienta
-   Dostawcy sieci
-   Microsoft Datacenter

Ten dokument ma na celu pomóc użytkownikowi ustalając, gdzie (lub nawet wtedy, gdy) występuje problem łączności i które strefie w ten sposób poszukiwania pomoc od odpowiedniego zespołu, aby rozwiązać ten problem. Jeśli wymagane jest pomocy technicznej firmy Microsoft, aby rozwiązać problem, otwórz bilet pomocy technicznej z [Microsoft Support][Support].

> [!IMPORTANT]
> Ten dokument ma na celu pomoc diagnozowania i rozwiązywania problemów proste. Nie ma być zastępczy pomocy technicznej firmy Microsoft. Otwórz bilet pomocy technicznej z [Microsoft Support] [ Support] Jeśli nie możesz rozwiązać problem przy użyciu wskazówki.
>
>

## <a name="overview"></a>Przegląd
Na poniższym diagramie przedstawiono logicznej łączność sieci klienta do sieci firmy Microsoft przy użyciu usługi ExpressRoute.
[![1]][1]

Na powyższym diagramie liczby wskazują punkty klucza sieci. Punkty sieci odwołuje się często za pośrednictwem tego artykułu numeru skojarzone.

W zależności od modelu łączności ExpressRoute (chmury Exchange wspólnej lokalizacji, bezpośrednie połączenie sieci Ethernet lub dowolny z każdym (IPVPN)) punktów sieci, 3 i 4 może być przełączników (warstwy 2 urządzenia). Punkty klucza sieciowego przedstawione są następujące:

1.  Urządzenie obliczeniowe klienta (na przykład serwera lub komputera)
2.  CEs: Routery brzegowe klienta uzyskują 
3.  PEs (skierowane do CE): Dostawca krawędzi routery czy przełączniki które stoją routery brzegowe klienta uzyskują. Nazywane PE CEs w niniejszym dokumencie.
4.  PEs (MSEE połączonej): Dostawca krawędzi routery czy przełączniki które stoją MSEEs. Nazywane PE MSEEs w niniejszym dokumencie.
5.  MSEEs: Routery Microsoft Edge przedsiębiorstwa (MSEE) ExpressRoute
6.  Brama sieci wirtualnej (VNet)
7.  Obliczenia bazy danych urządzenia w sieci wirtualnej Azure

Użycie chmury Exchange wspólnej lokalizacji lub połączenia Ethernet Point-to-Point modeli łączności router brzegowy klienta (2) czy ustanowić komunikację równorzędną za MSEEs (5) Protokół BGP. Sieci punkty 3 i 4 będzie nadal istnieje, ale nieco przejrzyste jako urządzenia warstwy 2.

Jeśli jest używany model usługi łączności dowolny z każdym (IPVPN), PEs (skierowane do MSEE) (4) czy nawiązania komunikacji równorzędnej z MSEEs (5) Protokół BGP. Trasy następnie będzie propagowane do sieci klienta za pośrednictwem sieci dostawcy usług IPVPN.

>[!NOTE]
>Wysoką dostępność usługi ExpressRoute firma Microsoft wymaga parę nadmiarowych sesje BGP między MSEEs (5) i PE-MSEEs (4). Parę nadmiarowych ścieżek sieciowych zaleca się między sieci klienta i serwera CEs PE. Jednak w modelu połączenia dowolny z każdym (IPVPN), może być połączone jednym urządzeniu CE (2) do co najmniej jeden PEs (3).
>
>

Aby sprawdzić poprawność obwodu usługi ExpressRoute, następujące czynności są objęte (z punktem sieci określona przez liczbę skojarzone):
1. [Sprawdź poprawność aprowizacji obwodów i stan (5)](#validate-circuit-provisioning-and-state)
2. [Sprawdzanie poprawności co najmniej jeden ExpressRoute komunikacji równorzędnej jest skonfigurowany (5)](#validate-peering-configuration)
3. [Sprawdzanie poprawności ARP między firmą Microsoft a usługi dostawcy (łącze od 4 do 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Sprawdź poprawność protokołu BGP oraz tras na MSEE (BGP od 4 do 5 i 5-6 Jeśli sieci wirtualnej jest podłączona)](#validate-bgp-and-routes-on-the-msee)
5. [Sprawdź statystyki ruchu (ruchu przechodzącego przez 5)](#check-the-traffic-statistics)

Więcej operacji sprawdzania poprawności i kontroli zostaną dodane w przyszłości, zajrzyj tu co miesiąc!

## <a name="validate-circuit-provisioning-and-state"></a>Sprawdź poprawność aprowizacji obwodów i stanu
Niezależnie od tego modelu łączności obwodu usługi ExpressRoute musi być utworzony i w związku z tym wygenerowany klucz usługi dla aprowizacji obwodów. Inicjowanie obsługi administracyjnej obwodu usługi ExpressRoute ustanawia nadmiarowych połączeń warstwy 2 między PE-MSEEs (4) i MSEEs (5). Aby uzyskać więcej informacji na temat sposobu tworzenia, modyfikowania, udostępnić i sprawdzić obwodu usługi ExpressRoute, zobacz artykuł [tworzenia i modyfikowania obwodu usługi expressroute][CreateCircuit].

>[!TIP]
>Klucz usługi unikatowo identyfikuje obwodu usługi ExpressRoute. Ten klucz jest wymagany dla większości poleceń programu powershell wymienione w niniejszym dokumencie. Ponadto należy będziesz potrzebować pomocy firmy Microsoft lub partnerem ExpressRoute, aby rozwiązać problem ExpressRoute, podaj klucz usługi, aby łatwo zidentyfikować obwodu.
>
>

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pomocą portalu Azure
W portalu Azure można sprawdzić stan obwodu usługi ExpressRoute, wybierając ![2][2] w menu po lewej stronie paska i wybierając obwodu usługi expressroute. Wybieranie ExpressRoute obwodu wymienione w obszarze "Wszystkie zasoby" spowoduje otwarcie bloku obwodu usługi ExpressRoute. W ![3][3] bloku ExpressRoute essentials przedstawiono, jak pokazano na poniższym zrzucie ekranu:

![4][4]    

W ExpressRoute Essentials *obwodu stan* wskazuje stan obwodu po stronie firmy Microsoft. *Dostawca stanu* wskazuje, czy został obwodu *obsługiwane administracyjnie nie zainicjowano obsługę administracyjną* po stronie dostawcy usług. 

Dla obwodu usługi ExpressRoute działać *obwodu stan* musi być *włączone* i *stan dostawcy* musi być *obsługiwane administracyjnie*.

>[!NOTE]
>Jeśli *obwodu stan* jest wyłączona, skontaktuj się z [Microsoft Support][Support]. Jeśli *stan dostawcy* jest nieudostępniane, skontaktuj się z dostawcą usług.
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby wyświetlić listę wszystkich obwody usługi ExpressRoute w grupie zasobów, użyj następującego polecenia:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Nazwę grupy zasobów można uzyskać za pośrednictwem platformy Azure. Zapoznaj się z podsekcją poprzedniej tego dokumentu i należy pamiętać, że nazwa grupy zasobów jest wymieniony w zrzut ekranu przykład.
>
>

Aby wybrać danego obwodu usługi expressroute w grupie zasobów, użyj następującego polecenia:

    Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Przykładowa odpowiedź jest:

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

Aby upewnić się, jeśli działa obwodu usługi ExpressRoute, zwracając szczególną uwagę na następujące pola:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

>[!NOTE]
>Jeśli *CircuitProvisioningState* jest wyłączona, skontaktuj się z [Microsoft Support][Support]. Jeśli *ServiceProviderProvisioningState* jest nieudostępniane, skontaktuj się z dostawcą usług.
>
>

### <a name="verification-via-powershell-classic"></a>Weryfikacja za pomocą programu PowerShell (klasyczne)
Aby wyświetlić listę wszystkich obwody usługi ExpressRoute w ramach subskrypcji, użyj następującego polecenia:

    Get-AzureDedicatedCircuit

Aby wybrać danego obwodu ExpressRoute, użyj następującego polecenia:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Przykładowa odpowiedź jest:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Aby upewnić się, jeśli działa obwodu usługi ExpressRoute, zwracając szczególną uwagę na następujące pola: ServiceProviderProvisioningState: stan elastycznie: włączone

>[!NOTE]
>Jeśli *stan* jest wyłączona, skontaktuj się z [Microsoft Support][Support]. Jeśli *ServiceProviderProvisioningState* jest nieudostępniane, skontaktuj się z dostawcą usług.
>
>

## <a name="validate-peering-configuration"></a>Sprawdź poprawność konfiguracji komunikacji równorzędnej
Po zakończeniu dostawcę usługi obwodu ExpressRoute inicjowania obsługi routingu konfiguracji mogą być tworzone za pośrednictwem obwodu ExpressRoute między MSEE-PRs (4) i MSEEs (5). Każdy obwód usługi ExpressRoute może mieć jedną, dwie lub trzy konteksty routingu włączona: prywatnej komunikacji równorzędnej platformy Azure (ruch do prywatnej sieci wirtualnych na platformie Azure), publicznej komunikacji równorzędnej platformy Azure (ruch na publiczne adresy IP na platformie Azure) i komunikacji równorzędnej firmy Microsoft (ruch do usługi Office 365 i Dynamics 365). Aby uzyskać więcej informacji na temat sposobu tworzenia i modyfikowania konfiguracji routingu, zobacz artykuł [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pomocą portalu Azure

>[!NOTE]
>Jeśli warstwy 3 jest udostępniany przez dostawcę usług i komunikacji równorzędnych znajdują się w portalu, należy odświeżyć konfiguracji obwód, używając przycisku Odśwież w portalu. Ta operacja zostanie przeprowadzona odpowiednią konfigurację routingu na obwodu. 
>
>

W portalu Azure można sprawdzić stan obwodu usługi ExpressRoute, wybierając ![2][2] w menu po lewej stronie paska i wybierając obwodu usługi expressroute. Wybieranie ExpressRoute obwodu wymienione w obszarze "Wszystkie zasoby" zostaną otwarte bloku obwodu usługi ExpressRoute. W ![3][3] bloku ExpressRoute otrzyma essentials, jak pokazano na poniższym zrzucie ekranu:

![5][5]

W poprzednim przykładzie jako dostrzeżone Azure prywatnej komunikacji równorzędnej kontekstu routingu jest włączone, natomiast Azure publicznego i konteksty routingu komunikacji równorzędnej firmy Microsoft nie są włączone. Pomyślnie włączono kontekstu komunikacji równorzędnej musi również podsieci podstawowego i pomocniczego point-to-point (wymagane dla protokołu BGP) na liście. / 30 podsieci są używane dla adresu IP interfejsu MSEEs i PE MSEEs. 

>[!NOTE]
>Element równorzędny nie jest włączone, sprawdź, czy przypisanych podsieci podstawowego i zapasowego jest zgodna z konfiguracją na PE MSEEs. Jeśli nie, aby zmienić konfigurację na routerach MSEE odwołują się do [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby uzyskać Azure prywatnej komunikacji równorzędnej szczegółów konfiguracji, użyj następujących poleceń:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Przykładowa odpowiedź dla pomyślnie skonfigurowano prywatnej komunikacji równorzędnej, jest:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : ####
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 300
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Pomyślnie włączono kontekstu komunikacji równorzędnej musi wyświetlonych prefiksów adresu podstawowego i pomocniczego. / 30 podsieci są używane dla adresu IP interfejsu MSEEs i PE MSEEs.

Aby uzyskać Azure publicznej komunikacji równorzędnej szczegółów konfiguracji, użyj następujących poleceń:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Aby uzyskać szczegóły konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

    $ckt = Get-AzureRmExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzureRmExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Jeśli nie skonfigurowano komunikacji równorzędnej, może to być komunikat o błędzie. Przykładowa odpowiedź, jeśli podane komunikacji równorzędnej (Azure publicznej komunikacji równorzędnej, w tym przykładzie) nie został skonfigurowany w ramach obwodu:

    Get-AzureRmExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzureRmExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzureRmExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


<p/>
>[!NOTE]
>Jeśli element równorzędny nie jest włączona, upewnij się, jeśli przypisanych podsieci podstawowego i zapasowego jest zgodna z konfiguracją w połączonej MSEE PE. Sprawdź także, czy poprawny *VlanId*, *AzureASN*, i *PeerASN* są używane na MSEEs i jeśli te wartości mapowany używane na połączonych MSEE PE. Jeśli wybrano opcję tworzenia skrótu MD5, udostępniony klucz powinny być takie same na pary MSEE i PE MSEE. Aby zmienić konfigurację na routerach MSEE, zajrzyj do [Utwórz i zmodyfikuj routingu dla obwodu usługi ExpressRoute] [CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Weryfikacja za pomocą programu PowerShell (klasyczne)
Aby uzyskać Azure prywatnej komunikacji równorzędnej szczegółów konfiguracji, użyj następującego polecenia:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Przykładowa odpowiedź dla pomyślnie skonfigurowano prywatnej komunikacji równorzędnej jest:

    AdvertisedPublicPrefixes       : 
    AdvertisedPublicPrefixesState  : Configured
    AzureAsn                       : 12076
    CustomerAutonomousSystemNumber : 
    PeerAsn                        : ####
    PrimaryAzurePort               : 
    PrimaryPeerSubnet              : 10.0.0.0/30
    RoutingRegistryName            : 
    SecondaryAzurePort             : 
    SecondaryPeerSubnet            : 10.0.0.4/30
    State                          : Enabled
    VlanId                         : 100

Pomyślnie włączono A kontekstu komunikacji równorzędnej, musi na liście podsieci podstawowego i pomocniczego elementu równorzędnego. / 30 podsieci są używane dla adresu IP interfejsu MSEEs i PE MSEEs.

Aby uzyskać Azure publicznej komunikacji równorzędnej szczegółów konfiguracji, użyj następujących poleceń:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Aby uzyskać szczegóły konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

>[!IMPORTANT]
>Jeśli komunikacji równorzędnych warstwy 3 zostały określone przez dostawcę usług, ustawienia komunikacji równorzędnych ExpressRoute za pośrednictwem portalu lub programu PowerShell zastąpienie ustawień dostawcy usługi. Resetowanie ustawień komunikacji równorzędnej dostawcy po stronie wymaga obsługi dostawcy usług. Komunikacji równorzędnych ExpressRoute należy modyfikować tylko wtedy, gdy jest pewne, czy dostawcy usług zapewnia tylko warstwy 2 usługi!
>
>

<p/>
>[!NOTE]
>Jeśli element równorzędny nie jest włączona, upewnij się, jeśli przypisanych podsieci podstawowego i pomocniczego elementu równorzędnego jest zgodna z konfiguracją w połączonej MSEE PE. Sprawdź także, czy poprawny *VlanId*, *AzureAsn*, i *PeerAsn* są używane na MSEEs i jeśli te wartości mapowany używane na połączonych MSEE PE. Aby zmienić konfigurację na routerach MSEE, zajrzyj do [Utwórz i zmodyfikuj routingu dla obwodu usługi ExpressRoute] [CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Sprawdź poprawność ARP między firmy Microsoft i dostawcy usług
Ta sekcja używa poleceń programu PowerShell (klasyczny). Używając polecenia programu PowerShell usługi Azure Resource Manager, upewnij się, że masz dostęp administratora/współadministratora do subskrypcji. Rozwiązywanie problemów przy użyciu usługi Azure Resource Manager polecenia można znaleźć na stronie [pobierania ARP tabele w modelu wdrażania usługi Resource Manager] [ ARP] dokumentu.

>[!NOTE]
>Aby uzyskać ARP, można użyć Azure portal i poleceń programu PowerShell usługi Azure Resource Manager. Jeśli za pomocą poleceń programu PowerShell usługi Azure Resource Manager zostaną napotkane błędy, klasycznym poleceń programu PowerShell powinna działać jako klasycznego środowiska PowerShell poleceń również współpracować z obwody usługi ExpressRoute Menedżera zasobów Azure.
>
>

Można odczytać tabeli protokołu ARP routera MSEE głównej dla prywatnej komunikacji równorzędnej, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Oto przykład odpowiedzi dla polecenia, w tym scenariuszu powiodło się:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Podobnie można sprawdzić w tabeli protokołu ARP z MSEE w *głównej*/*dodatkowej* ścieżki dla *prywatnej*/*publiczny*  / *Microsoft* komunikacji równorzędnych.

W poniższym przykładzie przedstawiono odpowiedzi polecenia dla komunikacji równorzędnej nie istnieje.

    ARP Info:
       
>[!NOTE]
>Jeśli w tabeli protokołu ARP nie mają adresy IP interfejsów mapowane na adresy MAC, przejrzyj następujące informacje:
>1. Jeśli adres IP pierwszego /30 podsieci dla skojarzenia między MSEE PR i MSEE jest używany w interfejsie MSEE PR. Azure zawsze używa jako drugiego adresu IP dla MSEEs.
>2. Upewnij się, jeśli klienta (C-znacznik) oraz znaczników sieci VLAN usługi (S-Tag) zgodne na pary MSEE PR i MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Sprawdź poprawność protokołu BGP oraz tras na MSEE
Ta sekcja używa poleceń programu PowerShell (klasyczny). Używając polecenia programu PowerShell usługi Azure Resource Manager, upewnij się, że masz dostęp administratora/współadministratora do subskrypcji.

>[!NOTE]
>Aby uzyskać informacje dotyczące protokołu BGP, można użyć Azure portal i poleceń programu PowerShell usługi Azure Resource Manager. Jeśli za pomocą poleceń programu PowerShell usługi Azure Resource Manager zostaną napotkane błędy, klasycznym poleceń programu PowerShell powinna działać jako klasycznego środowiska PowerShell poleceń również współpracować z obwody usługi ExpressRoute Menedżera zasobów Azure.
>
>

Aby uzyskać podsumowanie tabeli routingu (sąsiadów BGP) dla określonego kontekstu routingu, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Oto przykład odpowiedzi jest:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Jak pokazano w poprzednim przykładzie, polecenie jest przydatne do określenia, jak długo routingu kontekstu została ustanowiona. Wskazuje ona także liczby prefiksów trasy anonsowane przez router komunikacji równorzędnej.

>[!NOTE]
>Jeśli stan jest aktywny lub bezczynności, sprawdź, czy przypisanych podsieci podstawowego i pomocniczego elementu równorzędnego jest zgodna z konfiguracją na połączonych MSEE PE. Sprawdź także, czy poprawny *VlanId*, *AzureAsn*, i *PeerAsn* są używane na MSEEs i jeśli te wartości mapowany używane na połączonych MSEE PE. Jeśli wybrano opcję tworzenia skrótu MD5, udostępniony klucz powinny być takie same na pary MSEE i PE MSEE. Aby zmienić konfigurację na routerach MSEE, zapoznaj się [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].
>
>

<p/>
>[!NOTE]
>Jeśli niektóre miejsca docelowe są niedostępne w szczególności komunikacji równorzędnej, sprawdź tabelę tras z MSEEs należących do danego kontekstu komunikacji równorzędnej. Jeśli zgodny prefiks (może być NATed IP) znajduje się w tabeli routingu, następnie sprawdź, czy istnieją zapór / / listy ACL grupy NSG na ścieżce i jeżeli pozwalają ruchu.
>
>

Można pobrać pełnej tabeli routingu z MSEE *głównej* ścieżki dla konkretnej *prywatnej* routingu kontekstu, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Przykład pomyślnego wyniku dla polecenia jest:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Analogicznie, można sprawdzić tabeli routingu z MSEE w *głównej*/*dodatkowej* ścieżki dla *prywatnej* /  *Publiczny*/*Microsoft* komunikacji równorzędnej kontekstu.

W poniższym przykładzie przedstawiono odpowiedzi polecenia dla komunikacji równorzędnej nie istnieje:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Sprawdź statystyki ruchu
Aby uzyskać statystyki ruchu połączone ścieżki podstawowego i pomocniczego — bajtów i wylogowywanie — kontekstu komunikacji równorzędnej, użyj następującego polecenia:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Przykładowe dane wyjściowe polecenia jest:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Przykładowe dane wyjściowe polecenia dla nieistniejącego komunikacji równorzędnej jest:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji lub uzyskać pomoc zapoznaj się następujących łączy:

- [Pomoc techniczna firmy Microsoft][Support]
- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "połączenie logiczne Express Route"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Wszystkie zasoby ikony"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona — omówienie"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Zrzut ekranu przykładu ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Zrzut ekranu przykładu ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






