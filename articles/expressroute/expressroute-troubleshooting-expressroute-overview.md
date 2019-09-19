---
title: 'Sprawdź Przewodnik rozwiązywania problemów z łącznością ExpressRoute: Azure| Microsoft Docs'
description: Ta strona zawiera instrukcje dotyczące rozwiązywania problemów i weryfikowania kompleksowej łączności obwodu usługi ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 09/26/2017
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 026900e3dcbf7c20750bb8e17e44ba64897c9a30
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123443"
---
# <a name="verifying-expressroute-connectivity"></a>Weryfikowanie połączenia usługi ExpressRoute
Ten artykuł ułatwia sprawdzanie i rozwiązywanie problemów z łącznością ExpressRoute. ExpressRoute, która rozszerza sieć lokalną do chmury firmy Microsoft przez połączenie prywatne, które jest obsługiwane przez dostawcę połączeń, obejmuje następujące trzy odrębne strefy sieciowe:

-   Sieć klienta
-   Sieć dostawcy
-   Microsoft Datacenter

Celem tego dokumentu jest ułatwienie użytkownikowi zidentyfikowania, gdzie występuje problem z łącznością i w której strefie, a tym samym, aby uzyskać pomoc od odpowiedniego zespołu w celu rozwiązania problemu. Jeśli pomoc techniczna firmy Microsoft jest wymagana do rozwiązania problemu, należy otworzyć bilet pomocy technicznej w [Pomoc techniczna firmy Microsoft][Support].

> [!IMPORTANT]
> Ten dokument ma pomóc w diagnozowaniu i rozwiązywaniu prostych problemów. Nie jest to zamiennik dla pomocy technicznej firmy Microsoft. Otwórz bilet pomocy technicznej z [Pomoc techniczna firmy Microsoft][Support] , jeśli nie możesz rozwiązać problemu przy użyciu podanych wskazówek.
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Przegląd
Na poniższym diagramie przedstawiono logiczne połączenie sieci klienta z siecią firmy Microsoft przy użyciu ExpressRoute.
[![1]][1]

Na powyższym diagramie liczba wskazuje kluczowe punkty sieciowe. Punkty sieciowe są często przywoływane w tym artykule przez ich numer skojarzony.

W zależności od modelu łączności usługi ExpressRoute (w przypadku lokalizacji w chmurze, połączenia Ethernet typu punkt-punkt lub dowolnego typu dowolnego) (IPVPN) punkty sieciowe 3 i 4 mogą być przełącznikami (urządzenia warstwy 2). Najważniejsze przedstawiane punkty sieci są następujące:

1.  Urządzenie obliczeniowe klienta (na przykład serwer lub komputer)
2.  Ce Routery brzegowe klienta 
3.  PEs (CE): Routery/przełączniki brzegowe dostawcy, które są skierowane do routerów brzegowych klienta. Określany jako "PE-CE" w tym dokumencie.
4.  PEs (MSEE): Routery/przełączniki brzegowe dostawcy, które są połączone MSEE. Określany jako MSEE PE w tym dokumencie.
5.  MSEE Routery ExpressRoute Microsoft Enterprise Edge (MSEE)
6.  Brama Virtual Network (VNet)
7.  Urządzenie obliczeniowe w sieci wirtualnej platformy Azure

Jeśli używane są modele łączności między lokalizacjami w chmurze lub połączenia Ethernet typu punkt-punkt, router graniczny klienta (2) ustanowi komunikację równorzędną BGP z MSEE (5). Punkty sieciowe 3 i 4 nadal istnieją, ale są nieco widoczne jako urządzenia warstwy 2.

Jeśli używany jest model łączności any-to-Any (IPVPN), PEs (MSEEd) (4) ustanowi komunikację równorzędną BGP z MSEE (5). Trasy byłyby następnie propagowane do sieci klienta za pośrednictwem sieci dostawcy usług IPVPN.

> [!NOTE]
>Aby zapewnić wysoką dostępność ExpressRoute, firma Microsoft wymaga nadmiarowej pary sesji BGP między MSEE (5) i PE-MSEE (4). Zaleca się również nadmiarową parę ścieżek sieciowych między sieciami klienta i PE-CE. Jednak w przypadku modelu połączeń "dowolny do dowolnego" (IPVPN) jedno urządzenie CE (2) może być połączone z co najmniej jednym elementem PEs (3).
>
>

Aby sprawdzić obwód obwodu ExpressRoute, podano następujące czynności (z punktem sieci wskazanym przez skojarzoną liczbę):
1. [Weryfikacja aprowizacji i stanu obwodu (5)](#validate-circuit-provisioning-and-state)
2. [Sprawdź, czy skonfigurowano co najmniej jedną komunikację równorzędną ExpressRoute (5)](#validate-peering-configuration)
3. [Weryfikowanie protokołu ARP między firmą Microsoft i dostawcą usług (link do zakresu od 4 do 5)](#validate-arp-between-microsoft-and-the-service-provider)
4. [Weryfikowanie protokołu BGP i tras w MSEE (BGP z przedziału od 4 do 5 i od 5 do 6, jeśli sieć wirtualna jest połączona)](#validate-bgp-and-routes-on-the-msee)
5. [Sprawdź statystyki ruchu (ruch przechodzący przez 5)](#check-the-traffic-statistics)

W przyszłości zostaną dodane kolejne operacje sprawdzania poprawności i sprawdzenia. Wróć do miesiąca.

## <a name="validate-circuit-provisioning-and-state"></a>Weryfikacja aprowizacji i stanu obwodu
Bez względu na model łączności, obwód usługi ExpressRoute musi zostać utworzony i w związku z tym został wygenerowany klucz usług dla aprowizacji obwodów. Zainicjowanie obwodu ExpressRoute ustanawia nadmiarowe połączenia warstwy 2 między PE-MSEE (4) i MSEE (5). Aby uzyskać więcej informacji na temat tworzenia, modyfikowania, inicjowania obsługi i weryfikowania obwodu usługi ExpressRoute, zobacz artykuł [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit].

>[!TIP]
>Klucz usługi jednoznacznie identyfikuje obwód ExpressRoute. Ten klucz jest wymagany w przypadku większości poleceń programu PowerShell opisanych w tym dokumencie. Ponadto, jeśli chcesz uzyskać pomoc od firmy Microsoft lub partnera ExpressRoute, aby rozwiązać problem związany z ExpressRoute, Podaj klucz usługi, aby łatwo zidentyfikować obwód.
>
>

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pomocą Azure Portal
W Azure Portal można sprawdzić stan obwodu ExpressRoute, wybierając pozycję ![2][2] w menu po lewej stronie, a następnie wybierając obwód ExpressRoute. Wybranie obwodu ExpressRoute na liście w obszarze "wszystkie zasoby" spowoduje otwarcie bloku obwodu ExpressRoute. W sekcji ![3][3] w bloku ExpressRoute podstawowe informacje są wyświetlane jak pokazano na poniższym zrzucie ekranu:

![4][4]    

W ExpressRoute Essentials *stan obwodu* wskazuje stan obwodu po stronie firmy Microsoft. *Stan dostawcy* wskazuje, czy *nie zainicjowano* obsługi obwodu na stronie Dostawca usług. 

Aby obwód usługi ExpressRoute był uruchomiony, musi być *włączony* *stan obwodu* , a *stan dostawcy* musi być *zainicjowany*.

> [!NOTE]
> Jeśli *stan obwodu* nie jest włączony, skontaktuj się z [Pomoc techniczna firmy Microsoft][Support]. Jeśli *stan dostawcy* nie jest obsługiwany, skontaktuj się z dostawcą usług.
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby wyświetlić listę wszystkich obwodów usługi ExpressRoute w grupie zasobów, użyj następującego polecenia:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Nazwę grupy zasobów można uzyskać za pomocą platformy Azure. Zapoznaj się z poprzednią podsekcją tego dokumentu i zwróć uwagę na to, że nazwa grupy zasobów jest wymieniona na przykładowym zrzucie ekranu.
>
>

Aby wybrać konkretny obwód ExpressRoute w grupie zasobów, użyj następującego polecenia:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Przykładowa odpowiedź:

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

Aby potwierdzić, czy obwód ExpressRoute działa, należy zwrócić szczególną uwagę na następujące pola:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Jeśli *CircuitProvisioningState* nie jest włączona, skontaktuj się z [Pomoc techniczna firmy Microsoft][Support]. Jeśli *ServiceProviderProvisioningState* nie jest inicjowany, skontaktuj się z dostawcą usług.
>
>

### <a name="verification-via-powershell-classic"></a>Weryfikacja za pomocą programu PowerShell (wersja klasyczna)
Aby wyświetlić listę wszystkich obwodów usługi ExpressRoute w ramach subskrypcji, użyj następującego polecenia:

    Get-AzureDedicatedCircuit

Aby wybrać określony obwód ExpressRoute, użyj następującego polecenia:

    Get-AzureDedicatedCircuit -ServiceKey **************************************

Przykładowa odpowiedź:

    andwidth                         : 100
    BillingType                      : UnlimitedData
    CircuitName                      : Test-ER-Ckt
    Location                         : westus2
    ServiceKey                       : **************************************
    ServiceProviderName              : ****
    ServiceProviderProvisioningState : Provisioned
    Sku                              : Standard
    Status                           : Enabled

Aby potwierdzić, czy obwód ExpressRoute działa, należy zwrócić szczególną uwagę na następujące pola: ServiceProviderProvisioningState : Stan aprowizacji: Włączono

> [!NOTE]
> Jeśli *stan* nie jest włączony, skontaktuj się z [Pomoc techniczna firmy Microsoft][Support]. Jeśli *ServiceProviderProvisioningState* nie jest inicjowany, skontaktuj się z dostawcą usług.
>
>

## <a name="validate-peering-configuration"></a>Weryfikuj konfigurację komunikacji równorzędnej
Po zakończeniu aprowizacji obwodu usługi ExpressRoute przez dostawcę usług można utworzyć konfigurację routingu za pośrednictwem obwodu ExpressRoute między MSEE-żądań ściągnięcia (4) i MSEE (5). Każdy obwód usługi ExpressRoute może mieć włączone jeden, dwa lub trzy konteksty routingu: Prywatna Komunikacja równorzędna Azure (ruch do prywatnych sieci wirtualnych na platformie Azure), publiczna Komunikacja równorzędna Azure (ruch do publicznych adresów IP na platformie Azure) i Komunikacja równorzędna firmy Microsoft (ruch do pakietu Office 365). Aby uzyskać więcej informacji na temat tworzenia i modyfikowania konfiguracji routingu, zobacz artykuł [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pomocą Azure Portal

> [!NOTE]
> Jeśli dostawca usług udostępnia warstwę 3, a Komunikacja równorzędna jest pusta w portalu, Odśwież konfigurację obwodu za pomocą przycisku Odśwież w portalu. Ta operacja spowoduje zastosowanie odpowiedniej konfiguracji routingu w obwodzie. 
>
>

W Azure Portal można sprawdzić stan obwodu ExpressRoute, wybierając pozycję ![2][2] w menu po lewej stronie, a następnie wybierając obwód ExpressRoute. Wybranie obwodu ExpressRoute na liście w obszarze "wszystkie zasoby" spowoduje otwarcie bloku obwodu ExpressRoute. W sekcji ![3][3] w bloku ExpressRoute Essentials zostanie wyświetlona, jak pokazano na poniższym zrzucie ekranu:

![5][5]

W powyższym przykładzie, jak zanotowano kontekst routingu prywatnej komunikacji równorzędnej platformy Azure, a kontekst routingu komunikacji równorzędnej platformy Azure nie jest włączony. Pomyślnie włączono kontekst komunikacji równorzędnej, który ma również podstawowe i pomocnicze punkty typu punkt-punkt (wymagane w przypadku protokołu BGP) na liście. Podsieci/30 są używane dla adresu IP interfejsu MSEE i PE-MSEE. 

> [!NOTE]
> Jeśli Komunikacja równorzędna nie jest włączona, sprawdź, czy przypisane podsieci podstawowej i pomocniczej są zgodne z konfiguracją w środowisku PE-MSEE. Jeśli tak nie jest, aby zmienić konfigurację na routerach MSEE, zapoznaj się z tematem [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pomocą programu PowerShell
Aby uzyskać szczegółowe informacje o konfiguracji prywatnej komunikacji równorzędnej Azure, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Przykładowa odpowiedź dla pomyślnie skonfigurowanej prywatnej komunikacji równorzędnej:

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

 Pomyślnie włączono kontekst komunikacji równorzędnej będzie zawierał prefiksy adresów podstawowych i pomocniczych. Podsieci/30 są używane dla adresu IP interfejsu MSEE i PE-MSEE.

Aby uzyskać szczegółowe informacje o konfiguracji publicznej komunikacji równorzędnej platformy Azure, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Aby uzyskać szczegółowe informacje dotyczące konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Jeśli Komunikacja równorzędna nie zostanie skonfigurowana, zostanie wyświetlony komunikat o błędzie. Przykładowa odpowiedź, gdy podana Komunikacja równorzędna (publiczna Komunikacja równorzędna Azure w tym przykładzie) nie jest skonfigurowana w ramach obwodu:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Jeśli Komunikacja równorzędna nie jest włączona, sprawdź, czy przypisana podstawowa i pomocnicza podsieć jest zgodna z konfiguracją w połączonym środowisku PE-MSEE. Sprawdź również, czy poprawne *VlanId*, *AzureASN*i *PeerASN* są używane w MSEE, i czy te wartości są mapowane na te, które są używane w połączonym środowisku PE-MSEE. Jeśli zostanie wybrane Mieszanie MD5, klucz współużytkowany powinien być taki sam w parze MSEE i PE-MSEE. Aby zmienić konfigurację na routerach MSEE, zapoznaj się z tematem [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].  
>
>

### <a name="verification-via-powershell-classic"></a>Weryfikacja za pomocą programu PowerShell (wersja klasyczna)
Aby uzyskać szczegółowe informacje o konfiguracji prywatnej komunikacji równorzędnej platformy Azure, użyj następującego polecenia:

    Get-AzureBGPPeering -AccessType Private -ServiceKey "*********************************"

Przykładowa odpowiedź dla pomyślnie skonfigurowanej prywatnej komunikacji równorzędnej to:

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

Pomyślnie włączony kontekst komunikacji równorzędnej będzie zawierał podstawową i pomocniczą podsieć równorzędną. Podsieci/30 są używane dla adresu IP interfejsu MSEE i PE-MSEE.

Aby uzyskać szczegółowe informacje o konfiguracji publicznej komunikacji równorzędnej platformy Azure, użyj następujących poleceń:

    Get-AzureBGPPeering -AccessType Public -ServiceKey "*********************************"

Aby uzyskać szczegółowe informacje dotyczące konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

    Get-AzureBGPPeering -AccessType Microsoft -ServiceKey "*********************************"

> [!IMPORTANT]
> Jeśli Komunikacja równorzędna warstwy 3 została ustawiona przez dostawcę usługi, ustawienie komunikacji równorzędnej ExpressRoute za pośrednictwem portalu lub programu PowerShell powoduje zastąpienie ustawień dostawcy usług. Resetowanie ustawień komunikacji równorzędnej po stronie dostawcy wymaga obsługi dostawcy usług. Należy modyfikować tylko komunikację równorzędną ExpressRoute, jeśli jest pewność, że dostawca usług udostępnia tylko usługi warstwy 2.
>
>

> [!NOTE]
> Jeśli Komunikacja równorzędna nie jest włączona, sprawdź, czy jej podstawowa i pomocnicza podsieć równorzędna są przypisane do konfiguracji w połączonym środowisku PE-MSEE. Sprawdź również, czy poprawne *VlanId*, *AzureAsn*i *PeerAsn* są używane w MSEE, i czy te wartości są mapowane na te, które są używane w połączonym środowisku PE-MSEE. Aby zmienić konfigurację na routerach MSEE, zapoznaj się z tematem [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].
>
>

## <a name="validate-arp-between-microsoft-and-the-service-provider"></a>Weryfikowanie protokołu ARP między firmą Microsoft a dostawcą usług
W tej sekcji są wykorzystywane polecenia programu PowerShell (klasyczne). Jeśli używasz poleceń Azure Resource Manager programu PowerShell, upewnij się, że masz dostęp administratora/współadministratora do subskrypcji. Rozwiązywanie problemów przy użyciu poleceń Azure Resource Manager można znaleźć [w tabeli pobieranie ARP w dokumencie Menedżer zasobów model wdrażania][ARP] .

> [!NOTE]
>Aby uzyskać protokół ARP, można użyć poleceń Azure Portal i Azure Resource Manager programu PowerShell. W przypadku napotkania błędów przy użyciu poleceń Azure Resource Manager PowerShell, klasyczne polecenia programu PowerShell powinny funkcjonować w ramach klasycznych poleceń programu PowerShell, a także współpracują z Azure Resource Manager obwodów ExpressRoute.
>
>

Aby uzyskać tabelę ARP z podstawowego routera MSEE dla prywatnej komunikacji równorzędnej, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringArpInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Przykładowa odpowiedź dla polecenia w scenariuszu zakończonym powodzeniem:

    ARP Info:

                 Age           Interface           IpAddress          MacAddress
                 113             On-Prem       10.0.0.1           e8ed.f335.4ca9
                   0           Microsoft       10.0.0.2           7c0e.ce85.4fc9

Podobnie można sprawdzić tabelę ARP z MSEE w *podstawowej*/ścieżce*pomocniczej* dla *prywatnych*/*publicznych*/połączeń równorzędnych*firmy Microsoft* .

Poniższy przykład pokazuje, że odpowiedź polecenia dla komunikacji równorzędnej nie istnieje.

    ARP Info:
       
> [!NOTE]
> Jeśli tabela ARP nie zawiera adresów IP interfejsów mapowanych na adresy MAC, zapoznaj się z następującymi informacjami:
>1. Jeśli pierwszy adres IP podsieci/30 przypisany dla linku między MSEE-PR i MSEE jest używany w interfejsie MSEE-PR. Platforma Azure zawsze używa drugiego adresu IP dla MSEE.
>2. Sprawdź, czy Tagi sieci VLAN klienta (C-tag) i Service (S-tag) pasują zarówno do pary MSEE-PR i MSEE.
>
>

## <a name="validate-bgp-and-routes-on-the-msee"></a>Weryfikowanie protokołu BGP i tras na MSEE
W tej sekcji są wykorzystywane polecenia programu PowerShell (klasyczne). Jeśli używasz poleceń Azure Resource Manager programu PowerShell, upewnij się, że masz dostęp administratora/współadministratora do subskrypcji.

> [!NOTE]
>Aby uzyskać informacje dotyczące protokołu BGP, można użyć poleceń Azure Portal i Azure Resource Manager programu PowerShell. W przypadku napotkania błędów przy użyciu poleceń Azure Resource Manager PowerShell, klasyczne polecenia programu PowerShell powinny funkcjonować w ramach klasycznych poleceń programu PowerShell, a także współpracują z Azure Resource Manager obwodów ExpressRoute.
>
>

Aby uzyskać podsumowanie tabeli routingu (sąsiada BGP) dla danego kontekstu routingu, użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringRouteTableSummary -AccessType Private -Path Primary -ServiceKey "*********************************"

Przykład odpowiedzi:

    Route Table Summary:

            Neighbor                   V                  AS              UpDown         StatePfxRcd
            10.0.0.1                   4                ####                8w4d                  50

Jak pokazano w poprzednim przykładzie, polecenie jest przydatne do określenia, jak długo został ustanowiony kontekst routingu. Wskazuje również liczbę prefiksów tras anonsowanych przez router komunikacji równorzędnej.

> [!NOTE]
> Jeśli stan jest aktywny lub bezczynny, sprawdź, czy przypisane podsieci podstawowej i pomocniczej elementu równorzędnego są zgodne z konfiguracją w połączonym środowisku PE-MSEE. Sprawdź również, czy poprawne *VlanId*, *AzureAsn*i *PeerAsn* są używane w MSEE, i czy te wartości są mapowane na te, które są używane w połączonym środowisku PE-MSEE. Jeśli zostanie wybrane Mieszanie MD5, klucz współużytkowany powinien być taki sam w parze MSEE i PE-MSEE. Aby zmienić konfigurację na routerach MSEE, zapoznaj się z tematem [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].
>
>

> [!NOTE]
> Jeśli niektóre lokalizacje docelowe nie są dostępne dla określonej komunikacji równorzędnej, należy sprawdzić tabelę tras MSEE należącej do określonego kontekstu komunikacji równorzędnej. Jeśli w tabeli routingu występuje pasujący prefiks (może to być parametr "Jand"), sprawdź, czy na ścieżce znajdują się zapory/sieciowej grupy zabezpieczeń/listy ACL i czy zezwalają one na ruch.
>
>

Aby uzyskać pełną tabelę routingu z MSEE na ścieżce *podstawowej* dla danego kontekstu routingu *prywatnego* , użyj następującego polecenia:

    Get-AzureDedicatedCircuitPeeringRouteTableInfo -AccessType Private -Path Primary -ServiceKey "*********************************"

Przykładowy wynik pomyślnego polecenia to:

    Route Table Info:

             Network             NextHop              LocPrf              Weight                Path
         10.1.0.0/16            10.0.0.1                                       0    #### ##### #####     
         10.2.0.0/16            10.0.0.1                                       0    #### ##### #####
    ...

Podobnie można sprawdzić tabelę routingu z MSEE w *podstawowej*/ścieżce*pomocniczej* dla *prywatnego*/*publicznego*/kontekstu komunikacji równorzędnej*Microsoft* .

Poniższy przykład pokazuje odpowiedź polecenia dla komunikacji równorzędnej nie istnieje:

    Route Table Info:

## <a name="check-the-traffic-statistics"></a>Sprawdź statystyki ruchu
Aby uzyskać dane statystyczne dotyczące połączonej ścieżki podstawowej i pomocniczej — liczba bajtów w kontekście komunikacji równorzędnej, użyj następującego polecenia:

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

Przykładowe dane wyjściowe polecenia to:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Przykładowe dane wyjściowe polecenia dla nieistniejącej komunikacji równorzędnej to:

    Get-AzureDedicatedCircuitStats : ResourceNotFound: Can not find any subinterface for peering type 'Public' for circuit '97f85950-01dd-4d30-a73c-bf683b3a6e5c' .
    At line:1 char:1
    + Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf ...
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : CloseError: (:) [Get-AzureDedicatedCircuitStats], CloudException
        + FullyQualifiedErrorId : Microsoft.WindowsAzure.Commands.ExpressRoute.GetAzureDedicatedCircuitPeeringStatsCommand

## <a name="next-steps"></a>Następne kroki
Aby uzyskać dodatkowe informacje lub pomoc zapoznaj się z następujących linków:

- [pomoc techniczna firmy Microsoft][Support]
- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png  "Łączności logicznej Express Route"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikona wszystkie zasoby"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona przeglądu"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Przykładowy zrzut ekranu ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Przykładowy zrzut ekranu ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager






