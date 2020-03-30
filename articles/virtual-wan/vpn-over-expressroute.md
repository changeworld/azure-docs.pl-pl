---
title: 'Konfigurowanie szyfrowania usługi ExpressRoute: protokół IPsec za pomocą usługi ExpressRoute dla wirtualnej sieci WAN platformy Azure'
description: W tym samouczku dowiesz się, jak używać wirtualnej sieci WAN platformy Azure do tworzenia połączenia sieci VPN między lokacjami za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 03/19/2020
ms.author: cherylmc
ms.openlocfilehash: b1e6305d142530ab19849f61f12a122d0c6434aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059300"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Szyfrowanie usługi ExpressRoute: protokół IPsec w udziel y w u usług ExpressRoute dla wirtualnej sieci WAN

W tym artykule pokazano, jak używać wirtualnej sieci WAN platformy Azure do ustanawiania połączenia sieci VPN protokołu IPsec/IKE z sieci lokalnej do platformy Azure za pośrednictwem prywatnej komunikacji równorzędnej obwodu usługi Azure ExpressRoute. Ta technika może zapewnić szyfrowany tranzyt między sieciami lokalnymi i sieciami wirtualnymi platformy Azure za pośrednictwem usługi ExpressRoute, bez przechodzenia przez publiczny Internet lub przy użyciu publicznych adresów IP.

## <a name="topology-and-routing"></a>Topologia i routing

Na poniższym diagramie przedstawiono przykład łączności sieci VPN za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute:

![VPN przez usługę ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diagram przedstawia sieć w sieci lokalnej połączonej z bramą sieci VPN usługi Azure hub za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute. Ustanowienie łączności jest proste:

1. Ustanawianie łączności usługi ExpressRoute za pomocą obwodu usługi ExpressRoute i prywatnej komunikacji równorzędnej.
2. Ustal łączność sieci VPN zgodnie z opisem w tym artykule.

Ważnym aspektem tej konfiguracji jest routing między sieciami lokalnymi a platformą Azure za pośrednictwem ścieżek usługi ExpressRoute i VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Ruch z sieci lokalnych na platformę Azure

W przypadku ruchu z sieci lokalnych do platformy Azure prefiksy platformy Azure (w tym koncentrator wirtualny i wszystkie sieci wirtualne połączone z koncentratorem) są anonsowane zarówno za pośrednictwem usługi ExpressRoute private peering BGP, jak i vpn BGP. Powoduje to dwie trasy sieciowe (ścieżki) w kierunku platformy Azure z sieci lokalnych:

- Jeden nad ścieżką chronioną przez protokół IPsec
- Jeden bezpośrednio przez ExpressRoute *bez* ochrony IPsec 

Aby zastosować szyfrowanie do komunikacji, należy upewnić się, że dla sieci połączonej z siecią VPN na diagramie trasy platformy Azure za pośrednictwem lokalnej bramy sieci VPN są preferowane w stosunku do ścieżki bezpośredniego usługi ExpressRoute.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Ruch z platformy Azure do sieci lokalnych

To samo wymaganie dotyczy ruchu z platformy Azure do sieci lokalnych. Aby upewnić się, że ścieżka IPsec jest preferowana w stosunku do bezpośredniej ścieżki usługi ExpressRoute (bez protokołu IPsec), dostępne są dwie opcje:

- Anonsuj bardziej szczegółowe prefiksy w sesji protokołu VPN BGP dla sieci połączonej z siecią VPN. Można anonsować większy zakres, który obejmuje sieć połączoną z siecią VPN za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute, a następnie bardziej szczegółowe zakresy w sesji BGP sieci VPN. Na przykład anonsuj 10.0.0.0/16 za pośrednictwem usługi ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

- Anonsowanie prefiksów rozłącznych dla sieci VPN i usługi ExpressRoute. Jeśli zakresy sieci połączonych z siecią VPN są rozłączne od innych połączonych sieci usługi ExpressRoute, można anonsować prefiksy w sesjach protokołu BGP sieci VPN i usługi ExpressRoute. Na przykład anonsuj 10.0.0.0/24 za pośrednictwem usługi ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

W obu tych przykładach platforma Azure będzie wysyłać ruch do 10.0.1.0/24 za pośrednictwem połączenia sieci VPN, a nie bezpośrednio za pośrednictwem usługi ExpressRoute bez ochrony sieci VPN.

> [!WARNING]
> Jeśli anonsujesz *te same* prefiksy za pośrednictwem połączeń Usługi ExpressRoute i VPN, platforma Azure użyje ścieżki usługi ExpressRoute bezpośrednio bez ochrony sieci VPN.
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. Tworzenie wirtualnej sieci WAN i koncentratora z bramami

Przed kontynuowaniem należy wprowadzić następujące zasoby platformy Azure i odpowiadające im konfiguracje lokalne:

- Wirtualna sieć WAN platformy Azure
- Wirtualne centrum sieci WAN z [bramą usługi ExpressRoute](virtual-wan-expressroute-portal.md) i [bramą sieci VPN](virtual-wan-site-to-site-portal.md)

Aby zapoznać się z instrukcjami tworzenia wirtualnej sieci WAN platformy Azure i koncentratora ze skojarzeniem usługi ExpressRoute, zobacz [Tworzenie skojarzenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure.](virtual-wan-expressroute-portal.md) Aby zapoznać się z instrukcjami tworzenia bramy sieci VPN w wirtualnej sieci WAN, zobacz [Tworzenie połączenia lokacja-lokacja przy użyciu wirtualnej sieci WAN platformy Azure](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. Tworzenie witryny dla sieci lokalnej

Zasób lokacji jest taki sam jak witryny sieci VPN niepodstawowe dla wirtualnej sieci WAN. Adres IP lokalnego urządzenia sieci VPN może być teraz prywatnym adresem IP lub publicznym adresem IP w sieci lokalnej, do osiągnięcia za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute utworzonej w kroku 1.

> [!NOTE]
> Adres IP lokalnego urządzenia sieci VPN *musi* być częścią prefiksów adresów anonsowanych do wirtualnego centrum WAN za pośrednictwem prywatnej komunikacji równorzędnej usługi Azure ExpressRoute.
>

1. Przejdź do witryny Azure portal w przeglądarce. 
1. Wybierz utworzoną wan wan. Na stronie WAN w obszarze **Łączność**wybierz pozycję **Lokacje sieci VPN**.
1. Na stronie **Witryny sieci VPN** wybierz pozycję **+Utwórz witrynę**.
1. Na stronie **Tworzenie lokacji** wypełnij następujące pola:
   * **Subskrypcja**: Sprawdź subskrypcję.
   * **Grupa zasobów:** Wybierz lub utwórz grupę zasobów, której chcesz użyć.
   * **Region:** Wprowadź region platformy Azure dla zasobu lokacji sieci VPN.
   * **Nazwa:** Wprowadź nazwę, za pomocą której chcesz odwołać się do witryny lokalnej.
   * **Dostawca urządzenia**: Wprowadź dostawcę lokalnego urządzenia sieci VPN.
   * **Protokół Bramy granicznej:** Wybierz opcję "Włącz", jeśli sieć lokalna korzysta z protokołu BGP.
   * **Prywatna przestrzeń adresowa:** Wprowadź przestrzeń adresową IP, która znajduje się w witrynie lokalnej. Ruch przeznaczony dla tej przestrzeni adresowej jest kierowany do sieci lokalnej za pośrednictwem bramy sieci VPN.
   * **Koncentratory:** wybierz jeden lub więcej centrów, aby połączyć tę witrynę sieci VPN. Wybrane koncentratory muszą mieć już utworzone bramy sieci VPN.
1. Wybierz **dalej: Łącza >** dla ustawień łącza VPN:
   * **Nazwa łącza:** Nazwa, za pomocą której chcesz odwołać się do tego połączenia.
   * **Nazwa dostawcy**: Nazwa dostawcy usług internetowych dla tej witryny. W przypadku sieci lokalnej usługi ExpressRoute jest to nazwa dostawcy usług usługi Usługi Usługi Usługi ExpressRoute.
   * **Prędkość:** Szybkość łącza usługi internetowej lub obwodu usługi ExpressRoute.
   * **Adres IP:** publiczny adres IP urządzenia sieci VPN, które znajduje się w witrynie lokalnej. Lub w przypadku usługi ExpressRoute lokalnie jest to prywatny adres IP urządzenia sieci VPN za pośrednictwem usługi ExpressRoute.

   Jeśli protokół BGP jest włączony, będzie stosowany do wszystkich połączeń utworzonych dla tej witryny na platformie Azure. Konfigurowanie protokołu BGP w wirtualnej sieci WAN jest równoznaczne z konfigurowaniem protokołu BGP w bramie sieci VPN platformy Azure. 
   
   Lokalny adres równorzędny BGP *nie może* być taki sam jak adres IP sieci VPN na urządzeniu lub w wirtualnej przestrzeni adresowej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla równorzędnego adresu IP BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu. Jednak *nie może* to być APIPA (169.254.* x*. *x*) adres. Określ ten adres w odpowiedniej bramie sieci lokalnej reprezentującej lokalizację. Aby zapoznać się z wymaganiami wstępnymi protokołu BGP, zobacz [Informacje o Usłudze BGP z bramą sieci VPN platformy Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Wybierz **dalej: Przejrzyj + utwórz >,** aby sprawdzić wartości ustawień i utworzyć witrynę sieci VPN. Jeśli **wybrano koncentratory** do nawiązania połączenia, połączenie zostanie nawiązane między siecią lokalną a bramą sieci VPN koncentratora.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. Zaktualizuj ustawienie połączenia sieci VPN, aby używać usługi ExpressRoute

Po utworzeniu witryny sieci VPN i nawiązaniu połączenia z koncentratorem należy wykonać następujące czynności, aby skonfigurować połączenie do korzystania z prywatnej komunikacji równorzędnej usługi ExpressRoute:

1. Wróć do strony zasobów wirtualnej sieci WAN i wybierz zasób koncentratora. Możesz też przejść z witryny sieci VPN do połączonego koncentratora.
1. W obszarze **Łączność**wybierz pozycję **VPN (Lokacja lokacja).**
1. Wybierz wielokropek (**...**) w witrynie sieci VPN za pośrednictwem usługi ExpressRoute i wybierz pozycję **Edytuj połączenie VPN z tym koncentratorem**.
1. W przypadku **korzystania z prywatnego adresu IP platformy Azure**wybierz opcję **Tak**. To ustawienie konfiguruje bramę sieci VPN koncentratora do używania prywatnych adresów IP w zakresie adresów centralnych na bramie dla tego połączenia, a nie publicznych adresów IP. Zapewni to, że ruch z sieci lokalnej przechodzi przez prywatne ścieżki równorzędne usługi ExpressRoute, a nie przy użyciu publicznego Internetu dla tego połączenia sieci VPN. Poniższy zrzut ekranu przedstawia ustawienie.

   ![Ustawienie używania prywatnego adresu IP dla połączenia sieci VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
1. Wybierz **pozycję Zapisz**.

Po zapisaniu zmian brama sieci VPN centrum użyje prywatnych adresów IP w bramie sieci VPN do ustanowienia połączeń IPsec/IKE z lokalnym urządzeniem sieci VPN za pośrednictwem usługi ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. Pobierz prywatne adresy IP dla bramy sieci VPN centrum

Pobierz konfigurację urządzenia sieci VPN, aby uzyskać prywatne adresy IP bramy sieci VPN koncentratora. Te adresy są potrzebne do skonfigurowania lokalnego urządzenia sieci VPN.

1. Na stronie centrum wybierz pozycję **VPN (Lokacja lokacja)** w obszarze **Łączność**.
1. U góry strony **Przegląd** wybierz pozycję Pobierz aplikację **VPN Config**. 

   Platforma Azure tworzy konto magazynu w grupie zasobów "microsoft-network-[location]", gdzie *lokalizacja* jest lokalizacją sieci WAN. Po zastosowaniu konfiguracji do urządzeń sieci VPN można usunąć to konto magazynu.
1. Po utworzeniu pliku wybierz łącze, aby go pobrać.
1. Zastosuj konfigurację na urządzeniu sieci VPN.

### <a name="vpn-device-configuration-file"></a>Plik konfiguracji urządzenia SIECI VPN

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration**: Ta sekcja oznacza szczegóły urządzenia skonfigurowane jako witryna łącząca się z wirtualną siecią WAN. Zawiera nazwę i publiczny adres IP urządzenia oddziału.
* **vpnSiteConnections**: Ta sekcja zawiera informacje o następujących ustawieniach:

    * Przestrzeń adresowa sieci wirtualnej koncentratora wirtualnego.<br/>Przykład:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Przestrzeń adresowa sieci wirtualnych połączonych z koncentratorem.<br>Przykład:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Adresy IP bramy sieci VPN koncentratora wirtualnego. Ponieważ każde połączenie bramy sieci VPN składa się z dwóch tuneli w konfiguracji aktywny-aktywny, zobaczysz oba adresy IP wymienione w tym pliku. W tym przykładzie `Instance0` `Instance1` widzisz i dla każdej witryny i są to prywatne adresy IP zamiast publicznych adresów IP.<br>Przykład:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Szczegóły konfiguracji połączenia bramy sieci VPN, takie jak BGP i klucz wstępny. Klucz wstępny jest generowany automatycznie. Zawsze możesz edytować połączenie na stronie **Przegląd** niestandardowego klucza wstępnego.
  
### <a name="example-device-configuration-file"></a>Przykładowy plik konfiguracji urządzenia

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Konfigurowanie urządzenia sieci VPN

Jeśli chcesz wiedzieć, jak skonfigurować urządzenie, możesz skorzystać z instrukcji na [stronie ze skryptami konfigurowania urządzenia sieci VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), z następującymi zastrzeżeniami:

* Instrukcje na stronie urządzenia sieci VPN nie są zapisywane dla wirtualnej sieci WAN. Ale można użyć wirtualnych wartości sieci WAN z pliku konfiguracyjnego, aby ręcznie skonfigurować urządzenie sieci VPN. 
* Skrypty konfiguracji urządzenia do pobrania, które są dla bramy sieci VPN, nie działają dla wirtualnej sieci WAN, ponieważ konfiguracja jest inna.
* Nowa wirtualna sieć WAN może obsługiwać zarówno IKEv1, jak i IKEv2.
* Wirtualna sieć WAN może używać tylko urządzeń sieci VPN opartych na trasach i instrukcji urządzenia.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. Zobacz wirtualną sieć WAN

1. Przejdź do wirtualnej sieci WAN.
1. Na **stronie Przegląd** każdy punkt na mapie reprezentuje koncentrator.
1. W sekcji **Koncentratory i połączenia** można wyświetlić stan połączenia koncentratora, lokacji, regionu i sieci VPN. Bajty można również wyświetlać w i na zewnątrz.

## <a name="7-monitor-a-connection"></a><a name="connectmon"></a>7. Monitorowanie połączenia

Utwórz połączenie w celu monitorowania komunikacji między maszyną wirtualną platformy Azure (Maszyna wirtualna) a lokacją zdalną. Aby uzyskać informacje dotyczące konfigurowania monitora połączeń, zobacz [Monitorowanie komunikacji sieciowej](~/articles/network-watcher/connection-monitor.md). Pole źródłowe to adres IP maszyny Wirtualnej na platformie Azure, a docelowy adres IP jest adresem IP witryny.

## <a name="8-clean-up-resources"></a><a name="cleanup"></a>8. Oczyszczanie zasobów

Jeśli te zasoby nie są już potrzebne, można użyć [Remove-AzResourceGroup,](/powershell/module/az.resources/remove-azresourcegroup) aby usunąć grupę zasobów i wszystkie zasoby, które zawiera. Uruchom następujące polecenie programu PowerShell i zastąp `myResourceGroup` nazwą grupy zasobów:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Ten artykuł ułatwia tworzenie połączenia sieci VPN za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute przy użyciu wirtualnej sieci WAN. Aby dowiedzieć się więcej o wirtualnej sieci WAN i powiązanych funkcjach, zobacz [omówienie wirtualnej sieci WAN](virtual-wan-about.md).
