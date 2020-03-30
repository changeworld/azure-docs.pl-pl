---
title: 'Azure ExpressRoute: Weryfikuj łączność — przewodnik rozwiązywania problemów'
description: Ta strona zawiera instrukcje dotyczące rozwiązywania problemów i sprawdzania poprawności połączenia typu end-to-end obwodu usługi ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 58ae39e8dfdf918ae14ca9bb8dac28405828999e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330961"
---
# <a name="verifying-expressroute-connectivity"></a>Weryfikowanie połączenia usługi ExpressRoute
Ten artykuł ułatwia weryfikowanie i rozwiązywanie problemów z łącznością usługi ExpressRoute. Usługa ExpressRoute rozszerza sieć lokalną do chmury firmy Microsoft za pośrednictwem połączenia prywatnego, które jest często ułatwione przez dostawcę łączności. Łączność usługi ExpressRoute tradycyjnie obejmuje trzy różne strefy sieciowe, w następujący sposób:

-   Sieć klienta
-   Sieć dostawców
-   Centrum danych firmy Microsoft

> [!NOTE]
> W modelu łączności bezpośredniej usługi ExpressRoute (oferowanym przy przepustowości 10/100 Gb/s) klienci mogą bezpośrednio łączyć się z portem routerów Microsoft Enterprise Edge (MSEE). W związku z tym w modelu łączności bezpośredniej istnieją tylko strefy sieciowe klientów i firmy Microsoft.
>


Celem tego dokumentu jest pomoc użytkownikowi w określeniu, czy i gdzie występuje problem z łącznością. W ten sposób, aby pomóc szukać pomocy od odpowiedniego zespołu, aby rozwiązać problem. Jeśli aby rozwiązać problem, potrzebna jest pomoc techniczna firmy [Microsoft,][Support]otwórz bilet pomocy technicznej za pomocą pomocy technicznej firmy Microsoft .

> [!IMPORTANT]
> Ten dokument ma na celu pomóc w diagnozowaniu i rozwiązywaniu prostych problemów. Nie jest przeznaczony do zastąpienia pomocy technicznej firmy Microsoft. Otwórz bilet pomocy technicznej z [pomocą techniczną firmy Microsoft,][Support] jeśli nie możesz rozwiązać problemu, korzystając z podanych wskazówek.
>
>

## <a name="overview"></a>Omówienie
Na poniższym diagramie przedstawiono logiczną łączność sieci klienta z siecią Firmy Microsoft przy użyciu usługi ExpressRoute.
[![1]][1]

Na poprzednim diagramie liczby wskazują kluczowe punkty sieciowe. Te punkty sieciowe są czasami odwoływane w tym artykule przez skojarzony z nimi numer. W zależności od modelu łączności usługi ExpressRoute — kolokacja cloud exchange, połączenie Ethernet typu punkt-punkt lub dowolny (IPVPN) — punkty sieciowe 3 i 4 mogą być przełączane (urządzenia warstwy 2) lub routery (urządzenia z warstwą 3). W modelu łączności bezpośredniej nie ma punktów sieciowych 3 i 4; zamiast tego CE (2) są bezpośrednio połączone z MSEE za pomocą ciemnego włókna. Przedstawione kluczowe punkty sieci są następujące:

1.  Urządzenie obliczeniowe klienta (na przykład serwer lub komputer)
2.  CE: Routery brzegowe klienta 
3.  PE (skierowane do CE): routery/przełączniki brzegowe dostawcy, które są skierowane do routerów brzegowych klienta. W niniejszym dokumencie określane jako PE-CE.
4.  PE (skierowane do MSEE): routery/przełączniki brzegowe dostawcy, które stoją w obliczu MSEE. W niniejszym dokumencie określane jako PE-MSEE.
5.  MSEE: routery Usługi ExpressRoute usługi Microsoft Enterprise Edge (MSEE)
6.  Brama sieci wirtualnej
7.  Urządzenie obliczeniowe w sieci wirtualnej platformy Azure

Jeśli używane są modele kolokcji cloud exchange, sieci Ethernet typu point-to-point lub łączności bezpośredniej, ce (2) ustanawiaj komunikację równorzędną BGP z MSEE (5). 

Jeśli używany jest model łączności typu "każdy do dowolnego" (IPVPN), pe-MSEE (4) ustanawia komunikację równorzędną BGP z MSEE (5). PE-MSEE propagują trasy otrzymane od firmy Microsoft z powrotem do sieci klientów za pośrednictwem sieci dostawców usług IPVPN.

> [!NOTE]
>Aby uzyskać wysoką dostępność, firma Microsoft ustanawia w pełni nadmiarową łączność równoległą między parami MSEE (5) i PE-MSEE (4). Zachęca się również do w pełni nadmiarowej równoległej ścieżki sieci między siecią klientów a parą PE-CE. Aby uzyskać więcej informacji na temat wysokiej dostępności, zobacz artykuł [Projektowanie wysokiej dostępności za pomocą usługi ExpressRoute][HA]
>
>

Poniżej przedstawiono logiczne kroki rozwiązywania problemów z obwodem usługi ExpressRoute:

* [Weryfikowanie inicjowania obsługi administracyjnej obwodów i stanu](#verify-circuit-provisioning-and-state)
  
* [Sprawdzanie poprawności konfiguracji komunikacji równorzędnej](#validate-peering-configuration)
  
* [Sprawdzanie poprawności ARP](#validate-arp)
  
* [Sprawdzanie poprawności protokołu BGP i tras na MSEE](#validate-bgp-and-routes-on-the-msee)
  
* [Potwierdź przepływ ruchu](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>Weryfikowanie inicjowania obsługi administracyjnej obwodów i stanu
Inicjowanie obsługi administracyjnej obwodu usługi ExpressRoute ustanawia nadmiarowe połączenia warstwy 2 między CE/PE-MSEE (2)/(4) i MSEE (5). Aby uzyskać więcej informacji na temat tworzenia, modyfikowania, inicjowania obsługi administracyjnej i weryfikowania obwodu usługi ExpressRoute, zobacz artykuł [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit].

>[!TIP]
>Klucz usługi jednoznacznie identyfikuje obwód usługi ExpressRoute. Jeśli potrzebujesz pomocy firmy Microsoft lub partnera usługi ExpressRoute w celu rozwiązania problemu z usługą ExpressRoute, podaj klucz usługi, aby łatwo zidentyfikować obwód.
>
>

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pośrednictwem portalu Azure
W witrynie Azure portal otwórz blok obwodu usługi ExpressRoute. W ![sekcji 3][3] ostrza podstawowe informacje o udziale w udziale w udziale usługi ExpressRoute są wymienione na poniższym zrzucie ekranu:

![4][4]    

W programie ExpressRoute Essentials *stan obwodu* wskazuje stan obwodu po stronie firmy Microsoft. *Stan dostawcy* wskazuje, czy obwód został *aprowizowany/nie aprowizowany* po stronie dostawcy usług. 

Aby obwód usługi ExpressRoute działał, *stan obwodu* musi być *włączony,* a *stan dostawcy* musi być *aprowizowany.*

> [!NOTE]
> Po skonfigurowaniu obwodu usługi ExpressRoute, jeśli *stan obwodu* zostanie uderzony w stanie niewłączona, skontaktuj się z [pomocą techniczną firmy Microsoft][Support]. Z drugiej strony, jeśli *status Dostawcy* zostanie uderzony w stanie nieuwzwiedzionego, skontaktuj się z usługodawcą.
>
>

### <a name="verification-via-powershell"></a>Weryfikacja za pośrednictwem programu PowerShell
Aby wyświetlić listę wszystkich obwodów usługi ExpressRoute w grupie zasobów, użyj następującego polecenia:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>Jeśli szukasz nazwy grupy zasobów, możesz ją uzyskać, wystawiając listę wszystkich grup zasobów w ramach subskrypcji, używając polecenia *Get-AzResourceGroup*
>


Aby wybrać określony obwód usługi ExpressRoute w grupie zasobów, użyj następującego polecenia:

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

Przykładową odpowiedzią jest:

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

Aby sprawdzić, czy obwód usługi ExpressRoute działa, należy zwrócić szczególną uwagę na następujące pola:

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Po skonfigurowaniu obwodu usługi ExpressRoute, jeśli *stan obwodu* zostanie uderzony w stanie niewłączona, skontaktuj się z [pomocą techniczną firmy Microsoft][Support]. Z drugiej strony, jeśli *status Dostawcy* zostanie uderzony w stanie nieuwzwiedzionego, skontaktuj się z usługodawcą.
>
>

## <a name="validate-peering-configuration"></a>Sprawdzanie poprawności konfiguracji komunikacji równorzędnej
Po zakończeniu obsługi administracyjnej obwodu usługi ExpressRoute można utworzyć wiele konfiguracji routingu opartego na eBGP za pośrednictwem obwodu usługi ExpressRoute między CE/MSEE-PE (2)/(4) i MSEE (5). Każdy obwód usługi ExpressRoute może mieć: prywatna komunikacja równorzędna platformy Azure (ruch do prywatnych sieci wirtualnych na platformie Azure) i/lub komunikacja równorzędna firmy Microsoft (ruch do publicznych punktów końcowych PaaS i SaaS). Aby uzyskać więcej informacji na temat tworzenia i modyfikowania konfiguracji routingu, zobacz artykuł [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].

### <a name="verification-via-the-azure-portal"></a>Weryfikacja za pośrednictwem portalu Azure

> [!NOTE]
> W modelu łączności IPVPN dostawcy usług obsługują odpowiedzialność za konfigurowanie komunikacji równorzędnej (usługi warstwy 3). W takim modelu po dostawcy usług skonfigurował komunikacji równorzędnej i jeśli komunikacja równorzędna jest pusta w portalu, spróbuj odświeżyć konfigurację obwodu za pomocą przycisku odświeżania w portalu. Ta operacja spowoduje pobranie bieżącej konfiguracji routingu z obwodu. 
>

W witrynie Azure portal stan komunikacji równorzędnej obwodu usługi ExpressRoute można sprawdzić w obszarze obwód usługi ExpressRoute bloku. W ![sekcji 3][3] ostrza, wymiany usługi ExpressRoute zostaną wyświetlone w poniższym zrzucie ekranu:

![5][5]

W poprzednim przykładzie, jak wspomniano private azure private peering jest aprowizowany, podczas gdy publiczne platformy Azure i komunikacji równorzędnej firmy Microsoft nie są aprowizacji. Pomyślnie zainicjowany kontekst komunikacji równorzędnej będzie również mieć podstawowe i pomocnicze podsieci punkt-punkt na liście. Podsieci /30 są używane dla adresu IP interfejsu MSEE i CE/PE-MSEE. Dla komunikacji równorzędnej, które są aprowizacji, aukcji wskazuje również, kto ostatnio zmodyfikowane konfiguracji. 

> [!NOTE]
> Jeśli włączenie komunikacji równorzędnej nie powiedzie się, sprawdź, czy przypisane podsieci podstawowe i pomocnicze są zgodne z konfiguracją w połączonym CE/PE-MSEE. Sprawdź również, czy poprawne *VlanId*, *AzureASN*i *PeerASN* są używane na MSEE i czy te wartości są mapone do tych używanych w połączonych CE/PE-MSEE. Jeśli wybrano mieszanie MD5, klucz udostępniony powinien być taki sam na parze MSEE i PE-MSEE/CE. Wcześniej skonfigurowany klucz udostępniony nie będzie wyświetlany ze względów bezpieczeństwa. Jeśli musisz zmienić dowolną z tych konfiguracji na routerze MSEE, zapoznaj się [z artykułem Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].  
>

> [!NOTE]
> W podsieci /30 przypisanej do interfejsu firma Microsoft wybierze drugi użyteczny adres IP podsieci dla interfejsu MSEE. W związku z tym upewnij się, że pierwszy użyteczny adres IP podsieci został przypisany w równorzędnym CE/PE-MSEE.
>


### <a name="verification-via-powershell"></a>Weryfikacja za pośrednictwem programu PowerShell
Aby uzyskać szczegółowe informacje o konfiguracji prywatnej komunikacji równorzędnej platformy Azure, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

Przykładowa odpowiedź dla pomyślnie skonfigurowanej prywatnej komunikacji równorzędnej jest:

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 Pomyślnie włączony kontekst komunikacji równorzędnej będzie miał prefiksy adresu podstawowego i pomocniczego na liście. Podsieci /30 są używane dla adresu IP interfejsu MSEE i CE/PE-MSEE.

Aby uzyskać szczegóły konfiguracji publicznej komunikacji równorzędnej platformy Azure, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Aby uzyskać szczegółowe informacje o konfiguracji komunikacji równorzędnej firmy Microsoft, użyj następujących poleceń:

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

Jeśli komunikacja równorzędna nie jest skonfigurowana, pojawi się komunikat o błędzie. Przykładowa odpowiedź, gdy określona komunikacja równorzędna (azure public peering w tym przykładzie) nie jest skonfigurowana w obwodzie:

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> Jeśli włączenie komunikacji równorzędnej nie powiedzie się, sprawdź, czy przypisane podsieci podstawowe i pomocnicze są zgodne z konfiguracją w połączonym CE/PE-MSEE. Sprawdź również, czy poprawne *VlanId*, *AzureASN*i *PeerASN* są używane na MSEE i czy te wartości są mapone do tych używanych w połączonych CE/PE-MSEE. Jeśli wybrano mieszanie MD5, klucz udostępniony powinien być taki sam na parze MSEE i PE-MSEE/CE. Wcześniej skonfigurowany klucz udostępniony nie będzie wyświetlany ze względów bezpieczeństwa. Jeśli musisz zmienić dowolną z tych konfiguracji na routerze MSEE, zapoznaj się [z artykułem Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].  
>
>

> [!NOTE]
> W podsieci /30 przypisanej do interfejsu firma Microsoft wybierze drugi użyteczny adres IP podsieci dla interfejsu MSEE. W związku z tym upewnij się, że pierwszy użyteczny adres IP podsieci został przypisany w równorzędnym CE/PE-MSEE.
>

## <a name="validate-arp"></a>Sprawdzanie poprawności ARP

Tabela ARP zawiera mapowanie adresu IP i adresu MAC dla określonej komunikacji równorzędnej. Tabela ARP dla komunikacji równorzędnej obwodu usługi ExpressRoute zawiera następujące informacje dla każdego interfejsu (podstawowego i pomocniczego):
* Mapowanie adresu IP interfejsu routera lokalnego na adres MAC
* Mapowanie adresu IP interfejsu routera Usługi ExpressRoute na adres MAC
* Wiek mapowania tabel ARP może pomóc w weryfikacji konfiguracji warstwy 2 i rozwiązywaniu podstawowych problemów z łącznością warstwy 2.


Zobacz [Uzyskiwanie tabel ARP w][ARP] dokumencie modelu wdrażania Menedżera zasobów, jak wyświetlić tabelę ARP komunikacji równorzędnej usługi ExpressRoute i jak używać informacji do rozwiązywania problemów z łącznością warstwy 2.


## <a name="validate-bgp-and-routes-on-the-msee"></a>Sprawdzanie poprawności protokołu BGP i tras na MSEE

Aby uzyskać tabelę routingu z MSEE w ścieżce *podstawowej* dla kontekstu routingu *prywatnego,* użyj następującego polecenia:

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

Przykładową odpowiedzią jest:

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> Jeśli stan komunikacji równorzędnej eBGP między MSEE a CE/PE-MSEE jest aktywny lub bezczynny, sprawdź, czy przypisane podsieci podstawowych i pomocniczych równorzędnych są zgodne z konfiguracją połączonego CE/PE-MSEE. Sprawdź również, czy poprawne *VlanId*, *AzureAsn*i *PeerAsn* są używane na MSEE i czy te wartości są mapone do tych używanych w połączonych PE-MSEE/CE. Jeśli wybrano mieszanie MD5, klucz udostępniony powinien być taki sam na parze MSEE i CE/PE-MSEE. Jeśli musisz zmienić dowolną z tych konfiguracji na routerze MSEE, zapoznaj się [z artykułem Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering].
>


> [!NOTE]
> Jeśli niektóre miejsca docelowe nie są osiągalne za pomocą komunikacji równorzędnej, sprawdź tabelę tras elementów CHEM pod kątem odpowiedniego kontekstu komunikacji równorzędnej. Jeśli pasujący prefiks (może być NATed IP) jest obecny w tabeli routingu, sprawdź, czy na ścieżce znajdują się zapory/NSG/ACL, które blokują ruch.
>


W poniższym przykładzie pokazano odpowiedź polecenia dla komunikacji równorzędnej, która nie istnieje:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>Potwierdź przepływ ruchu
Aby uzyskać połączone statystyki ruchu ścieżki podstawowej i pomocniczej — bajtów w kontekście komunikacji równorzędnej i poza niego, należy użyć następującego polecenia:

    Get-AzExpressRouteCircuitStats -ResourceGroupName $RG -ExpressRouteCircuitName $CircuitName -PeeringType 'AzurePrivatePeering'

Przykładowe dane wyjściowe polecenia to:

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

Przykładowe dane wyjściowe polecenia dla nieistniejącej komunikacji równorzędnej są następujące:

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji lub pomoc, zapoznaj się z następującymi łączami:

- [Pomoc techniczna firmy Microsoft][Support]
- [Tworzenie i modyfikowanie obwodu usługi ExpressRoute][CreateCircuit]
- [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "Logiczna łączność trasy ekspresowej"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "Ikona Wszystkie zasoby"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "Ikona przeglądu"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "Przykładowy zrzut ekranu z programem ExpressRoute Essentials"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "Przykładowy zrzut ekranu z programem ExpressRoute Essentials"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





