---
title: 'Sprawdź łączność — przewodnik rozwiązywania problemów z usługi ExpressRoute: Azure| Microsoft Docs'
description: Ta strona zawiera instrukcje dotyczące rozwiązywania problemów i sprawdzania poprawności łączność typu end to end obwodu usługi ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 888f4dedf2fda0f54297d42a5f813abf73ded748
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66117861"
---
# <a name="verifying-expressroute-connectivity"></a>Weryfikowanie połączenia usługi ExpressRoute
Ten artykuł pomoże Ci Sprawdź i rozwiązywanie problemów z łącznością usługi ExpressRoute. Usługi ExpressRoute, która rozszerza sieć lokalną na chmurę Microsoft za pośrednictwem połączenia prywatnego, który jest zapewniana przez dostawcę połączenia, obejmuje następujące trzy strefy odrębnych sieci:

-   Sieć klienta
-   Provider Network
-   Microsoft Datacenter

Ten dokument ma na celu pomóc użytkownikowi, aby wskazać, gdzie (lub nawet wtedy, gdy) istnieje problem z łącznością i w ramach której strefy, w ten sposób, aby poszukać pomocy z odpowiedniego zespołu, aby rozwiązać ten problem. W razie potrzeby pomocy technicznej firmy Microsoft jest aby rozwiązać problem, otwórz bilet pomocy technicznej za pomocą [Microsoft Support][Support].

> [!IMPORTANT]
> Ten dokument ma na celu ułatwić diagnozowanie i rozwiązywanie problemów z prostego. Nie ma być zastępczy pomocy technicznej firmy Microsoft. Otwórz bilet pomocy technicznej za pomocą [Microsoft Support] [ Support] Jeśli nie możesz rozwiązać problem, zgodnie ze wskazówkami.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
Na poniższym diagramie przedstawiono logiczne łączność sieci klientów do sieci firmy Microsoft przy użyciu usługi ExpressRoute.
[![1]][1]

Na powyższym diagramie liczby wskazują punkty klucza sieci. Liczbę punktów sieci odwołuje się często Niniejszy artykuł ich skojarzone z nimi numery.

W zależności od modelu łączności usługi ExpressRoute (chmura Exchange wspólnej lokalizacji, połączenia Ethernet typu punkt-punkt lub dowolna dowolna (IPVPN)) punktów sieci, 3 i 4 może być przełączników (Warstwa 2 urządzenia). Liczbę punktów sieci klucza przedstawione są następujące:

1.  Urządzenie do obliczeń klienta (na przykład serwer lub komputer)
2.  CEs: Routery brzegowe klienta uzyskują 
3.  PEs (CE z Internetem): Dostawca usługi edge routery czy przełączniki, które występują routery brzegowe klienta uzyskują. Nazywane PE CEs w tym dokumencie.
4.  PEs (MSEE z Internetem): Dostawca usługi edge routery czy przełączniki, które występują Msee. Nazywane Msee PE w tym dokumencie.
5.  Msee: Routery ExpressRoute Microsoft Edge Enterprise (MSEE)
6.  Brama sieci wirtualnej (VNet)
7.  Obliczenia urządzenia w sieci wirtualnej platformy Azure

Jeśli używane są modele połączeń w chmurze programu Exchange wspólnej lokalizacji lub połączenia Ethernet typu punkt-punkt, router graniczny klienta (2) ustalenie BGP zaglądanie z Msee (5). Sieci punktów 3 i 4 będzie nadal istnieje, lecz nieco przejrzystości jako urządzenia warstwy 2.

Jeśli używany jest model łączność dowolna dowolna (IPVPN), PEs (MSEE z Internetem) (4) może nawiązać komunikacji równorzędnej z Msee (5) Protokół BGP. Trasy będzie następnie propagowania sieci klienta za pośrednictwem sieci IPVPN dostawcy usługi.

> [!NOTE]
>Wysoką dostępność usługi ExpressRoute firma Microsoft wymaga nadmiarowej parze sesji protokołu BGP między rozwiązaniami Msee (5) a PE-Msee (4). Nadmiarowej parze ścieżek sieciowych zaleca się między siecią klienta i serwera CEs PE. Jednak w modelu połączenia dowolna dowolna (IPVPN) na jednym urządzeniu CE (2) może połączyć jeden lub więcej PEs (3).
>
>

Aby sprawdzić poprawność obwodu usługi ExpressRoute, (z punktem sieci, wskazywany przez skojarzone z nimi numery) omówione są następujące czynności:
1. [Weryfikowanie aprowizacji obwodu i stan (5)](#validate-circuit-provisioning-and-state)
2. [Sprawdzanie poprawności co najmniej jeden ExpressRoute komunikacji równorzędnej jest skonfigurowany (5)](#validate-peering-configuration)
3. [Sprawdź poprawność ARP między firmami Microsoft i usługi dostawcy (łącze do zakresu od 4 do 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Sprawdzanie poprawności protokołu BGP oraz tras na MSEE (BGP między 4 i 5 i 5 – 6 w przypadku sieci wirtualnej jest podłączony)](#validate-bgp-and-routes-on-the-msee)
5. [Sprawdź statystyki ruchu (ruchu przechodzącego przez 5)](#check-the-traffic-statistics)

Więcej operacji sprawdzania poprawności i testy zostaną dodane w przyszłości, wróć tu co miesiąc!

## <a name="validate-circuit-provisioning-and-state"></a>Weryfikowanie aprowizacji obwodu i stanu
Niezależnie od tego, model usługi łączności obwodu usługi ExpressRoute ma zostać utworzony, a zatem klucza usługi generowane dla aprowizacji obwodu. Inicjowanie obsługi administracyjnej obwodu usługi ExpressRoute ustanawia nadmiarowe połączenia warstwy 2 między PE-Msee (4) i Msee (5). Aby uzyskać więcej informacji na temat sposobu tworzenia, modyfikacji, udostępniania i sprawdź obwód usługi ExpressRoute, zobacz artykuł [tworzenie i modyfikowanie obwodu ExpressRoute][CreateCircuit].

>[!TIP]
>Klucz usługi jest unikatowym identyfikatorem obwodu usługi ExpressRoute. Ten klucz jest wymagany dla większości poleceń programu powershell wymienione w niniejszym dokumencie. Ponadto należy będziesz potrzebować pomocy przez firmę Microsoft lub partnerem usługi ExpressRoute w taki sposób, aby rozwiązać problem z usługi ExpressRoute, podaj klucz usługi, aby łatwo zidentyfikować obwodu.
>
>

### <a name="verification-via-the-azure-portal"></a>Weryfikacja w witrynie Azure portal
W witrynie Azure portal można sprawdzić stan obwodu usługi ExpressRoute, wybierając ![2][2] w menu po lewej stronie strony paska, a następnie wybierając obwód usługi ExpressRoute. Wybieranie usługi ExpressRoute obwodu, w obszarze "Wszystkie zasoby" spowoduje otwarcie bloku obwodu usługi ExpressRoute. W ![3][3] części bloku ExpressRoute essentials są wyświetlane, jak pokazano na poniższym zrzucie ekranu:

![4][4]    

W bloku usługi ExpressRoute podstawy *Circuit stan* wskazuje stan obwodu po stronie firmy Microsoft. *Stan dostawcy* wskazuje, czy obwód został *Aprowizowana/nie zainicjowano obsługi administracyjnej* po stronie dostawcy usług. 

Dla obwodu usługi ExpressRoute działać *Circuit stan* musi być *włączone* i *stan dostawcy* musi być *Aprowizowana*.

> [!NOTE]
> Jeśli *Circuit stan* jest wyłączona, skontaktuj się z pomocą [Microsoft Support][Support]. Jeśli *stan dostawcy* jest nie zainicjowano obsługi administracyjnej, skontaktuj się z usługodawcą.
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby wyświetlić listę wszystkich obwodów usługi ExpressRoute w grupie zasobów, użyj następującego polecenia:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Nazwa grupy zasobów można uzyskać za pośrednictwem platformy Azure. Zobacz podsekcję poprzedniej części tego dokumentu, a następnie należy pamiętać, że nazwa grupy zasobów znajduje się przykładowy zrzut ekranu.
>
>

Aby wybrać określonego obwodu usługi ExpressRoute w grupie zasobów, użyj następującego polecenia:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Przykładowa odpowiedź to:

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

Aby upewnić się, jeśli działa obwodu usługi ExpressRoute, należy zwrócić szczególną uwagę na następujące pola:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Jeśli *CircuitProvisioningState* jest wyłączona, skontaktuj się z pomocą [Microsoft Support][Support]. Jeśli *ServiceProviderProvisioningState* jest nie zainicjowano obsługi administracyjnej, skontaktuj się z usługodawcą.
>
>

### <a name="verification-via-powershell-classic"></a>Weryfikacja za pomocą programu PowerShell (wersja klasyczna)
Aby wyświetlić listę wszystkich obwodów usługi ExpressRoute w ramach subskrypcji, użyj następującego polecenia:

    Get-AzureDedicatedCircuit

Aby wybrać określonego obwodu usługi ExpressRoute, użyj następującego polecenia:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Przykładowa odpowiedź to:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Aby upewnić się, jeśli działa obwodu usługi ExpressRoute, należy zwrócić szczególną uwagę na następujące pola: ServiceProviderProvisioningState: Stan elastycznie: Enabled (Włączony)

> [!NOTE]
> Jeśli *stan* jest wyłączona, skontaktuj się z pomocą [Microsoft Support][Support]. Jeśli *ServiceProviderProvisioningState* jest nie zainicjowano obsługi administracyjnej, skontaktuj się z usługodawcą.
>
>

## <a name="validate-peering-configuration"></a>Sprawdź poprawność konfiguracji komunikacji równorzędnej
Po ukończeniu dostawca usługi aprowizacji obwodu usługi ExpressRoute można utworzyć konfiguracji routingu za pośrednictwem obwodu usługi ExpressRoute między MSEE żądania ściągnięcia (4) i Msee (5). Każdy obwód usługi ExpressRoute może mieć jednego, dwóch lub trzech konteksty routingu włączone: Azure prywatnej komunikacji równorzędnej (ruch prywatnych sieci wirtualnych na platformie Azure), Azure publicznej komunikacji równorzędnej (ruch do publicznych adresów IP na platformie Azure) i komunikacji równorzędnej (ruchu z firmy Microsoft do usługi Office 365 i Dynamics 365). Aby uzyskać więcej informacji na temat sposobu tworzenia i modyfikowanie konfiguracji routingu, zobacz artykuł [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Weryfikacja w witrynie Azure portal

> [!NOTE]
> Jeśli warstwa 3 są dostarczane przez dostawcę usług i komunikacji równorzędnej jest puste w portalu, należy odświeżyć konfiguracji obwodu, używając przycisku Odśwież w portalu. Ta operacja wejdzie odpowiednią konfigurację routingu w obwodu. 
>
>

W witrynie Azure portal można sprawdzić stan obwodu usługi ExpressRoute, wybierając ![2][2] w menu po lewej stronie strony paska, a następnie wybierając obwód usługi ExpressRoute. Wybranie usługi ExpressRoute obwodu, w obszarze "Wszystkie zasoby" spowoduje otwarcie bloku obwodu usługi ExpressRoute. W ![3][3] części bloku usługi ExpressRoute, otrzyma essentials, jak pokazano na poniższym zrzucie ekranu:

![5][5]

W poprzednim przykładzie jak wspomniano Azure prywatnej komunikacji równorzędnej kontekstu routingu jest włączone, natomiast publicznej i firmy Microsoft komunikacji równorzędnej konteksty routingu nie są włączone. Pomyślnie włączono kontekstu komunikacji równorzędnej będzie również musiał podsieci podstawowego i pomocniczego point-to-point (wymagane dla protokołu BGP), na liście. / 30 podsieci są używane dla adresu IP interfejsu rozwiązaniami Msee a PE Msee. 

> [!NOTE]
> Jeśli komunikacja równorzędna nie jest włączona, należy sprawdzić, jeśli przypisanych podsieci podstawowego i pomocniczego odpowiadać konfiguracji w PE Msee. Jeśli nie, aby zmienić konfigurację na routerach MSEE dotyczą [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby uzyskać Azure prywatnej komunikacji równorzędnej szczegółów konfiguracji, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Przykładowa odpowiedź dla pomyślnie skonfigurowano prywatnej komunikacji równorzędnej, to:

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

 Pomyślnie włączono kontekstu komunikacji równorzędnej musi wyświetlonych prefiksów adresu podstawowego i pomocniczego. / 30 podsieci są używane dla adresu IP interfejsu rozwiązaniami Msee a PE Msee.

Aby uzyskać Azure publicznej komunikacji równorzędnej szczegółów konfiguracji, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Aby uzyskać szczegóły konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Jeśli komunikacja równorzędna nie jest skonfigurowany, może to być komunikat o błędzie. Przykładowa odpowiedź, gdy podane komunikacji równorzędnej (Azure publicznej komunikacji równorzędnej w tym przykładzie) nie jest skonfigurowany w ramach obwodu:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Jeśli komunikacja równorzędna nie jest włączona, należy sprawdzić, jeśli przypisanych podsieci podstawowego i pomocniczego zgodna z konfiguracją w połączonej MSEE PE. Również Sprawdź, czy poprawny *VlanId*, *AzureASN*, i *PeerASN* są używane na Msee i jeśli te wartości jest mapowany do tych na połączonych MSEE PE. Jeśli wybrano wyznaczania wartości skrótu MD5 klucz współużytkowany powinien być ten sam w parę MSEE i PE MSEE. Aby zmienić konfigurację na routerami MSEE, zapoznaj się [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Weryfikacja za pomocą programu PowerShell (wersja klasyczna)
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

Pomyślnie włączono A kontekstu komunikacji równorzędnej musi podsieci podstawowego i pomocniczego elementu równorzędnego na liście. / 30 podsieci są używane dla adresu IP interfejsu rozwiązaniami Msee a PE Msee.

Aby uzyskać Azure publicznej komunikacji równorzędnej szczegółów konfiguracji, użyj następujących poleceń:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Aby uzyskać szczegóły konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Jeśli stan komunikacji równorzędnej warstwy 3 został ustawiony przez dostawcę usług, ustawienia komunikacje równorzędne usługi ExpressRoute za pośrednictwem portalu lub programu PowerShell zastąpienie ustawień dostawcy usługi. Resetowanie ustawień komunikacji równorzędnej po stronie dostawcy wymaga obsługi dostawcy usług. Komunikacje równorzędne usługi ExpressRoute należy modyfikować tylko, gdy jest pewne, że dostawca usług zapewnia usługi warstwy 2 tylko!
>
>

> [!NOTE]
> Jeśli komunikacja równorzędna nie jest włączona, należy sprawdzić, jeśli przypisanych podsieci podstawowego i pomocniczego elementu równorzędnego zgodna z konfiguracją w połączonej MSEE PE. Również Sprawdź, czy poprawny *VlanId*, *AzureAsn*, i *PeerAsn* są używane na Msee i jeśli te wartości jest mapowany do tych na połączonych MSEE PE. Aby zmienić konfigurację na routerami MSEE, zapoznaj się [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Sprawdź poprawność ARP między firmami Microsoft i dostawcy usług
Ta sekcja używa poleceń programu PowerShell (klasyczny). Jeśli masz doświadczenie z poleceń programu PowerShell usługi Azure Resource Manager, upewnij się, że administrator/współadministrator dostępu do subskrypcji. Rozwiązywanie problemów przy użyciu usługi Azure Resource Manager poleceń można znaleźć na stronie [tabele wprowadzenie ARP w modelu wdrażania usługi Resource Manager] [ ARP] dokumentu.

> [!NOTE]
>Aby uzyskać ARP, można się zarówno w witrynie Azure portal, jak i poleceń programu PowerShell usługi Resource Manager platformy Azure. Jeśli wystąpią błędy za pomocą poleceń programu PowerShell usługi Resource Manager platformy Azure, klasycznych poleceń programu PowerShell powinna działać jako klasyczny program PowerShell, poleceń również współpracować z programem obwodów usługi ExpressRoute Menedżera zasobów platformy Azure.
>
>

Aby uzyskać tabeli protokołu ARP routera podstawowego rozwiązania MSEE dla prywatnej komunikacji równorzędnej, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Przykładową odpowiedź dla polecenia, w tym scenariuszu pomyślnie:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Podobnie, można sprawdzić tabeli protokołu ARP z rozwiązania MSEE w *podstawowego*/*dodatkowej* ścieżki, dla *prywatnej*/*publiczne*  / *Microsoft* komunikacji równorzędnej.

Poniższy przykład pokazuje odpowiedzi polecenia dla komunikacji równorzędnej nie istnieje.

    ARP Info:
       
> [!NOTE]
> Jeśli tabeli protokołu ARP nie ma adresy IP interfejsów mapowane na adresy MAC, przejrzyj następujące informacje:
>1. Jeśli pierwszy adres IP/30 podsieci przypisane łącze między MSEE (PR) i rozwiązania MSEE jest używany w interfejsie żądania ściągnięcia MSEE Platforma Azure zawsze używa drugiego adresu IP dla Msee.
>2. Upewnij się, jeśli klienta (C-Tag) i znaczników sieci VLAN usługi (S-Tag) odpowiadają na parę MSEE (PR) i rozwiązania MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Sprawdzanie poprawności protokołu BGP oraz tras na MSEE
Ta sekcja używa poleceń programu PowerShell (klasyczny). Jeśli masz doświadczenie z poleceń programu PowerShell usługi Azure Resource Manager, upewnij się, że administrator/współadministrator dostępu do subskrypcji.

> [!NOTE]
>Aby uzyskać informacje protokołu BGP, można się zarówno w witrynie Azure portal, jak i poleceń programu PowerShell usługi Resource Manager platformy Azure. Jeśli wystąpią błędy za pomocą poleceń programu PowerShell usługi Resource Manager platformy Azure, klasycznych poleceń programu PowerShell powinna działać jako klasyczny program PowerShell, poleceń również współpracować z programem obwodów usługi ExpressRoute Menedżera zasobów platformy Azure.
>
>

Aby uzyskać podsumowanie tabeli routingu (sąsiada protokołu BGP) dla określonego kontekstu routingu, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Przykładową odpowiedź to:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Jak pokazano w powyższym przykładzie, polecenie jest przydatne na potrzeby określania, jak długo routingu kontekstu zostało ustanowione. Wskazuje ona także liczba prefiksów trasy anonsowane przez router komunikacji równorzędnej.

> [!NOTE]
> Jeśli stan jest aktywny lub bezczynności, sprawdź, jeśli przypisanych podsieci podstawowego i pomocniczego elementu równorzędnego zgodna z konfiguracją w połączonej MSEE PE. Również Sprawdź, czy poprawny *VlanId*, *AzureAsn*, i *PeerAsn* są używane na Msee i jeśli te wartości jest mapowany do tych na połączonych MSEE PE. Jeśli wybrano wyznaczania wartości skrótu MD5 klucz współużytkowany powinien być ten sam w parę MSEE i PE MSEE. Aby zmienić konfigurację na routerami MSEE, zapoznaj się [tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].
>
>

> [!NOTE]
> Jeśli niektóre miejsca docelowe są niedostępne za pośrednictwem określonego komunikacji równorzędnej, sprawdź tabeli tras Msee należących do danego kontekstu komunikacji równorzędnej. Jeśli zgodny prefiks (może być NATed IP) znajduje się w tabeli routingu, sprawdź, jeśli istnieją zapory / / listy ACL grupy NSG na ścieżce i mogą zezwalać na ruch.
>
>

Aby uzyskać pełną tabelę routingu z rozwiązania MSEE *podstawowego* ścieżki dla konkretnej *prywatnej* routingu kontekstu, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Przykład pomyślnego wyniku polecenia to:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Podobnie, można sprawdzić tabeli routingu z rozwiązania MSEE w *podstawowego*/*dodatkowej* ścieżki dla *prywatnej* /  *Publiczne*/*Microsoft* kontekstu komunikacji równorzędnej.

Poniższy przykład pokazuje odpowiedzi polecenia dla komunikacji równorzędnej nie istnieje:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Sprawdź statystyki ruchu
Aby uzyskać statystyki ruchu połączone ścieżki podstawowego i pomocniczego — bajtów i pomniejszać — kontekstu komunikacji równorzędnej, użyj następującego polecenia:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

To przykładowe dane wyjściowe polecenia:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Przykładowe dane wyjściowe polecenia dla nieistniejącego komunikację równorzędną to:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe informacje lub pomoc zapoznaj się z następujących linków:

- [Pomoc techniczna firmy Microsoft][Support]
- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "połączenie logiczne Express Route"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Wszystkie ikony zasobów firmy"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona — omówienie"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Zrzut ekranu przykładu podstawy usługi ExpressRoute"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Zrzut ekranu przykładu podstawy usługi ExpressRoute"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






