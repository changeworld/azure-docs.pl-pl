---
title: Usługa Azure ExpressRoute globalnego dotrzeć do scenariusza aplikacji | Dokumentacja firmy Microsoft
description: Ta strona zawiera scenariusz aplikacji w zasięgu globalnym.
documentationcenter: na
services: networking
author: cherylmc
manager: tracsman
ms.service: expressroute,global reach
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: cherylmc
ms.openlocfilehash: 2adb8debf641a9b3875c5c386df7a35273f2a258
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428242"
---
# <a name="expressroute-global-reach-application-scenario"></a>Usługa ExpressRoute zasięgu globalnym scenariusza aplikacji

Aby dowiedzieć się więcej o zasięgu globalnym usługi ExpressRoute, zobacz [zasięgu globalnym ExpressRoute][Global Reach]. W tym artykule umożliwia przeprowadzenie scenariusza aplikacji, porównaj rozwiązania ExpressRoute zasięgu globalnym, aby kilka innych rozwiązań, skonfigurować zasięgu globalnym przykładowy scenariusz i weryfikowanie połączeń. 

##<a name="application-scenario"></a>Scenariusz aplikacji

Firma Fabrikam, Inc. ma duże obecności fizycznej i wdrażania platformy Azure w regionie wschodnie stany USA. Firma Fabrikam ma zaplecza łączność między jej w środowisku lokalnym i wdrożeń platformy Azure za pośrednictwem usługi ExpressRoute. Contoso Ltd. ma obecności i wdrażania platformy Azure w regionie zachodnie stany USA. Firma Contoso ma wewnętrznej łączności między jej w środowisku lokalnym i wdrożeń platformy Azure za pośrednictwem usługi ExpressRoute.  

Fabrikam Inc. acquires Contoso Ltd. Po połączeniu firma Fabrikam chce łączenia sieci. Na poniższym rysunku przedstawiono scenariusz:

 [![1]][1]

Kreskowane strzałki w środku rysunku powyżej wskazują połączeń sieciowych wymagane. W poniższej tabeli przedstawiono podstawowe prywatnej komunikacji równorzędnej z usługi ExpressRoute firma Contoso Ltd. przed połączeniem tabeli tras.

[![2]][2]

W poniższej tabeli przedstawiono podstawowe prywatnej komunikacji równorzędnej usługi ExpressRoute firma Fabrikam Inc. przed połączeniem tabeli tras.

[![3]][3]

## <a name="traditional-solutions"></a>Tradycyjne rozwiązania

### <a name="option-1-interconnect-on-premises-networks"></a>Opcja 1: Łączenie sieci lokalnych

Na poniższym rysunku przedstawiono tę opcję. Jak widać, należy można łączenia dwóch sieci w środowisku lokalnym przy użyciu sieci VPN typu lokacja lokacja lub inne opcje połączenia szerokopasmowego routingu oraz zarządzanie nim wszystkie między dwiema jednostkami. 

[![4]][4]

Ta opcja ma następujące wady:

- Wymuszono międzyregionalnych komunikacji platformy Azure dla wdrożenia za pośrednictwem nieoptymalne trasy.
- W przypadku odmowy zalet wysokiej dostępności sieci szkieletowej firmy Microsoft komunikacji międzyregionalnych.
- Tworzenia pełną odpowiedzialność routingu międzyregionalnych komunikacji.

### <a name="option-2-expressroute-cross-connectivity-and-interconnect-on-premises-networks"></a>Opcja 2: Połączenia krzyżowego usługi ExpressRoute i łączenia sieci lokalnej

Na poniższym rysunku przedstawiono tę opcję.

[![5]][5]

Jak pokazano na rysunku powyżej, również można ustanowić łączności między obwodów usługi ExpressRoute między regionalnych sieciach wirtualnych. Krzyżowe regionalnych łączność między wirtualnymi obwodów usługi ExpressRoute umożliwiłby następujących komunikatów:

- Zachodnie stany USA i wschodnie stany USA sieci wirtualnych, do komunikowania się odpowiednio z firmy Fabrikam/Contoso sieci lokalnej.
- Sieć wirtualna zachodnie stany USA do komunikowania się z siecią wirtualną wschodnie stany USA.

Nadal potrzebna jest wzajemne połączenie między dwiema sieciami lokalnymi dla sieci lokalnej do komunikowania się ze sobą.

Ta opcja umożliwia międzyregionalnych komunikacji platformy Azure w przypadku prywatne wdrożenie się za pośrednictwem sieci szkieletowej firmy Microsoft i komunikację między siecią lokalną, które mają zostać przeniesione z siecią zewnętrzną. Główną wadą rozwiązania jest jednak trzeba utworzyć wiele wielu połączeń regionalnych, które skomplikować obsługi i rozwiązywania problemów. Ponadto opcja nie umożliwia korzystanie z zalet wysokiej dostępności globalnej sieci szkieletowej firmy Microsoft do komunikacji między dwiema sieciami lokalnymi.

### <a name="option-3-vnet-peering-and-interconnect-on-premises-networks"></a>Opcja 3: Komunikacja równorzędna sieci wirtualnych i łączenia sieci lokalnej

Na poniższym rysunku przedstawiono tę opcję. Opcja przy użyciu równorzędnej międzyregionalnych komunikacji w sieci wirtualnej. Opcja, zgodnie z opisami, jest niekompletna zgodnie z jej nie adresów sieci wirtualnej między regionami, do komunikacji w środowisku lokalnym (wskazywane przez dwa wiersze kropkowanych strzałek w środku). Użyj lokalnej do połączenia w środowisku lokalnym (tak jak opcja 1) lub usługi ExpressRoute między łączności między regionami (jak opcja 2) do komunikacji w środowisku lokalnym.

[![6]][6]

Ta opcja zapewnia optymalny routing komunikacji między regionalnej sieci wirtualnej. Jednak znajduje się krótki, jeśli firma Fabrikam lub Contoso ma bardziej złożonym wdrożeniu platformy Azure, takich jak model sieci wirtualnej piasty i szprych. Takich jak poprzednie dwie opcje tej opcji nie pozwalają również korzystać z zalet wysokiej dostępności globalnej sieci szkieletowej firmy Microsoft do komunikacji między dwiema sieciami lokalnymi.

## <a name="global-reach"></a>Globalny zasięg

Usługa ExpressRoute zasięgu globalnym łączy prywatnej komunikacji równorzędnej obwodów usługi ExpressRoute, jak pokazano na poniższej ilustracji:

[![7]][7]

Konfigurowanie zasięgu globalnym między dwa obwody usługi ExpressRoute umożliwia komunikację prywatną między dwiema sieciami lokalnymi i wdrażanie platformy Azure w dwóch regionach. W związku z tym zasięgu globalnym spełnia cała komunikacja żądaną (wskazywane przez przerywana linia na rysunku pierwszej części tego artykułu) za pośrednictwem sieci szkieletowej firmy Microsoft.

### <a name="configure-global-reach"></a>Skonfiguruj na globalnym zasięgu

Aby dowiedzieć się, jak skonfigurować usługi ExpressRoute zasięgu globalnym, zobacz [skonfigurować zasięgu globalnym][Configure Global Reach]. 

Ponieważ firma Fabrikam, Inc. i Contoso Ltd. dołączono Azure jako osobne firmy, obwodów usługi ExpressRoute dla dwóch firm znajdują się w dwóch różnych subskrypcjach platformy Azure. Aby utworzyć zasięgu globalnym w subskrypcjach, musisz utworzyć autoryzacji w obwodzie usługi ExpressRoute należących do firmy Contoso Ltd. i przekazywać je do firmy Fabrikam, Inc. Obwód usługi ExpressRoute.


Aby utworzyć autoryzacji dla obwodu usługi ExpressRoute firmy Contoso, pierwsze logowanie na konto platformy Azure z firmy Contoso i wybierz odpowiednią subskrypcję (w przypadku wielu subskrypcji). Dostępne są następujące polecenia programu PowerShell dla tych kroków:

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```
Poniżej przedstawiono procedurę tworzenia autoryzacja obwodu usługi ExpressRoute:

```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```

Dane wyjściowe "Set-AzureRmExpressRouteCircuit" spowoduje wyświetlenie listy ExpressRouteCircuit. Należy pamiętać, prywatnej komunikacji równorzędnej Identyfikatora i klucza autoryzacji, wymienionego pod koniec listy. Zobacz przykład programu PowerShell dane wyjściowe poniższy fragment kodu:

```powershell
ServiceKey                       : <serviceKey>
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 65020,
                                       "PrimaryPeerAddressPrefix": "192.168.11.16/30",
                                       "SecondaryPeerAddressPrefix": "192.168.11.20/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 110,
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "4",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : [
                                     {
                                       "Name": "Auth4-ASH-Cust11",
                                       "Etag": "W/\"78dfeeae-0485-4cda-b5aa-99e780589677\"",
                                       "Id": "/subscriptions/<subscriptionId>/resourceGroups/SEA-Cust11/providers/Microsoft.Network/expressRouteCircuits/SEA-Cust11-ER/authorizations/Auth4-ASH-Cust11",
                                       "AuthorizationKey": "<authorizationKey>",
                                       "AuthorizationUseStatus": "Used",
                                       "ProvisioningState": "Succeeded"
                                     }
                                   ]
AllowClassicOperations           : False
GatewayManagerEtag               : 4
```

Za pomocą komunikacji równorzędnej Identyfikatora i klucza autoryzacji możesz utworzyć zasięgu globalnym w ramach obwodu usługi ExpressRoute firmy Fabrikam. Zaloguj się do konta platformy Azure z firmy Fabrikam. Jeśli istnieje więcej niż jedną subskrypcję, wybierz odpowiednią subskrypcję.

Globalny zasięg tworzy zestaw nadmiarowych połączeń typu punkt-punkt między dwiema pary rozwiązania MSEE. W przypadku połączeń dwóch point-to-point należy określić wartość/29 prefiksu adresu (na przykład uruchamianie Użyjmy 192.168.11.64/29). Aby utworzyć połączenie zasięgu globalnym, użyj następujących poleceń:

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix "__.__.__.__/29" -AuthorizationKey "########-####-####-####-############"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Gdy powyższe polecenia są wykonywane, potrwa kilka minut na utworzenie nadmiarowych połączeń zasięgu globalnym.

### <a name="verify-global-reach"></a>Sprawdź globalny zasięg

W poniższej tabeli przedstawiono podstawowe prywatnej komunikacji równorzędnej z usługi ExpressRoute firma Contoso Ltd. po skonfigurowaniu zasięgu globalnym tabeli tras.

[![8]][8]

W poniższej tabeli przedstawiono podstawowe prywatnej komunikacji równorzędnej usługi ExpressRoute firma Fabrikam Inc. po skonfigurowaniu zasięgu globalnym tabeli tras.

[![9]][9]

W tabelach powyżej widać wszystkie prefiksy "Sieć" oczekiwanego docelowy i należą odpowiednie "KOLEJNEGO Przeskoku".

Powyżej zostanie wyświetlony blok "Get route table" dostępnej w witrynie Azure portal w obszarze "Prywatnej komunikacji równorzędnej" dla obwodu usługi ExpressRoute. Można także wyświetlić listę tabeli tras usługi ExpressRoute za pomocą następującego polecenia programu PowerShell:

```powershell
Get-AzExpressRouteCircuitRouteTable -DevicePath 'primary' -ExpressRouteCircuitName "Your_circuit_name" -PeeringType AzurePrivatePeering -ResourceGroupName "Your_resource_group"
```

Do tabeli tras dodatkowego rozwiązania MSEE znajduje się zastąpić podstawowego, za pomocą słowa kluczowego "secondary" w poleceniu powyżej.

Załóżmy również sprawdzić łączność płaszczyzny danych przez badanie różnych miejsc docelowych z różnych sieci. Następujące trzy polecenia ping sprawdź łączność płaszczyzny danych z sieci lokalnej firmy Contoso, Contoso sieci wirtualnej platformy Azure, a siecią wirtualną platformy Azure firma Fabrikam z firmy Fabrikam sieci lokalnej.


    C:\Users\FabrikamUser>ping 10.1.11.10
    
    Pinging 10.1.11.10 with 32 bytes of data:
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    Reply from 10.1.11.10: bytes=32 time=69ms TTL=122
    
    Ping statistics for 10.1.11.10:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 69ms, Maximum = 69ms, Average = 69ms
    
    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=82ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=81ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=124
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 82ms, Average = 78ms
    
    C:\Users\FabrikamUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=2ms TTL=125
    Reply from 10.10.11.4: bytes=32 time=3ms TTL=125
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 2ms, Maximum = 3ms, Average = 2ms


Następujące dwa polecenia ping sprawdź łączność płaszczyzny danych z sieci wirtualnej platformy Azure Contoso i Fabrikam sieci wirtualnej z platformy Azure z sieci lokalnej firmy Contoso.

    C:\Users\ContosoUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=6ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=5ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 5ms, Maximum = 6ms, Average = 5ms
    
    C:\Users\ContosoUser>ping 10.10.11.4
    
    Pinging 10.10.11.4 with 32 bytes of data:
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=74ms TTL=124
    Reply from 10.10.11.4: bytes=32 time=73ms TTL=124
    
    Ping statistics for 10.10.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 73ms, Maximum = 74ms, Average = 73ms

Następujące polecenie ping sprawdza łączność płaszczyzny danych sieci wirtualnej platformy Azure Contoso z siecią wirtualną platformy Azure firma Fabrikam.

    C:\Users\FabrikamUser>ping 10.17.11.4
    
    Pinging 10.17.11.4 with 32 bytes of data:
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=77ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=76ms TTL=125
    Reply from 10.17.11.4: bytes=32 time=78ms TTL=125
    
    Ping statistics for 10.17.11.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 76ms, Maximum = 78ms, Average = 77ms

## <a name="optimization-for-latency-sensitive-traffic"></a>Optymalizacja dla ruchu poufnych opóźnienia

Globalny zasięg kieruje ruch za pośrednictwem urządzeń Microsoft Edge. Dla konkretnego scenariusza, w tym artykule można osiągnąć więcej optymalny routing między dwiema sieciami wirtualnymi, należy włączyć wirtualną sieć równorzędną między nimi. Podobnie można osiągnąć więcej optymalny routing między dwiema sieciami lokalnymi za pośrednictwem dostawcy usług, który może zapewnić bardziej bezpośrednie połączenie WAN między lokacjami. W takich przypadkach można użyć zasięgu globalnym routingu jako opcję wstecz kończyć się niepowodzeniem dla tych połączeń. 

## <a name="next-steps"></a>Kolejne kroki

Globalny zasięg, jest wdrażana na podstawie kraju, kraju. Aby zobaczyć, czy zasięgu globalnym jest dostępna w krajach, które mają, zobacz [zasięgu globalnym ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/expressroute-globalreach-applicationscenario/PreMergerScenario.png "scenariusza aplikacji"
[2]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Premerger.png "tabeli tras usługi ExpressRoute firma Contoso przed połączeniem"
[3]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Premerger.png "tabeli tras usługi ExpressRoute firma Fabrikam przed połączeniem"
[4]: ./media/expressroute-globalreach-applicationscenario/S2SVPN-Solution.png "połączeń z sieciami lokalnymi"
[5]: ./media/expressroute-globalreach-applicationscenario/ExR-XConnect-Solution.png "ExpressRoute Cross Connect"
[6]: ./media/expressroute-globalreach-applicationscenario/VNet-peering-Solution.png "komunikacja równorzędna sieci wirtualnych"
[7]: ./media/expressroute-globalreach-applicationscenario/GlobalReach-Solution.png "globalny zasięg"
[8]: ./media/expressroute-globalreach-applicationscenario/ContosoExR-RT-Postmerger.png "tabeli tras usługi ExpressRoute firma Contoso o zasięgu globalnym"
[9]: ./media/expressroute-globalreach-applicationscenario/FabrikamExR-RT-Postmerger.png "tabeli tras usługi ExpressRoute firma Fabrikam o zasięgu globalnym"

<!--Link References-->
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach





