---
title: Tworzenie połączenia sieci VPN typu lokacja-lokacja za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute w usłudze Azure Virtual WAN | Microsoft Docs
description: W tym samouczku dowiesz się, jak za pomocą usługi Azure Virtual WAN utworzyć połączenie sieci VPN typu lokacja-lokacja za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515033"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Tworzenie połączenia sieci VPN typu lokacja-lokacja za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute za pomocą wirtualnej sieci WAN platformy Azure

W tym artykule pokazano, jak używać wirtualnej sieci WAN do nawiązywania połączenia sieci VPN IPsec/IKE z sieci lokalnej na platformę Azure za pośrednictwem prywatnej komunikacji równorzędnej obwodu usługi ExpressRoute. Może to zapewnić zaszyfrowane tranzyt między sieciami lokalnymi i sieciami wirtualnymi platformy Azure za pośrednictwem usługi ExpressRoute, bez przechodzenia przez publiczny Internet lub korzystając z publicznych adresów IP.

## <a name="topology-and-routing"></a>Topologia i Routing

Na poniższym diagramie przedstawiono przykład sieci VPN za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute:

![Sieć VPN przez ExpressRoute](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

Diagram przedstawia sieć w sieci lokalnej połączonej z bramą sieci VPN usługi Azure Hub przez prywatną komunikację równorzędną ExpressRoute. Przedsiębiorstwo łączności jest proste:

1. Nawiązywanie łączności ExpressRoute z obwodem usługi ExpressRoute i prywatną komunikację równorzędną
2. Ustanów połączenie sieci VPN zgodnie z opisem w tym dokumencie

Ważnym aspektem tej konfiguracji jest Routing między sieciami lokalnymi i platformą Azure przez ścieżki ExpressRoute i sieci VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Ruch z sieci lokalnych do platformy Azure

W przypadku ruchu z sieci lokalnych na platformę Azure, prefiksy platformy Azure (w tym centrum wirtualne i wszystkie sieci wirtualne szprych podłączonych do centrum) będą anonsowane za pośrednictwem protokołu BGP i sieci VPN BGP usługi ExpressRoute. Spowoduje to powstanie dwóch tras sieciowych (ścieżek) w kierunku platformy Azure z sieci lokalnych. jeden za pośrednictwem ścieżki chronionej przez protokół IPsec i bezpośrednio w ExpressRoute **bez** ochrony IPsec. Aby upewnić się, że do komunikacji zastosowano szyfrowanie, należy upewnić się, że dla sieci połączonej z siecią VPN na diagramie są preferowane trasy platformy Azure za pośrednictwem lokalnej bramy sieci VPN.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Ruch z platformy Azure do sieci lokalnych

To samo wymaganie dotyczy ruchu z platformy Azure do sieci lokalnych. Aby zapewnić, że ścieżka IPsec jest preferowana przez ścieżkę ExpressRoute Direct (bez protokołu IPsec), dostępne są dwie opcje:

- Anonsowanie bardziej szczegółowych prefiksów w sesji protokołu BGP sieci VPN dla sieci połączonej z siecią VPN. Można anonsować większy zakres obejmujący "sieć podłączoną do sieci VPN" za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute, a następnie bardziej szczegółowych zakresów w sesji protokołu BGP sieci VPN. Na przykład Anonsuj 10.0.0.0/16 w ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

- Anonsowanie rozłączonych prefiksów dla sieci VPN i ExpressRoute. Jeśli zakresy sieci połączone z siecią VPN są rozłączane z innej połączonej sieci ExpressRoute, można anonsować prefiksy odpowiednio w sesjach protokołu BGP sieci VPN i ExpressRoute. Na przykład Anonsuj 10.0.0.0/24 przez ExpressRoute i 10.0.1.0/24 za pośrednictwem sieci VPN.

W obu tych przykładach usługa Azure wyśle ruch do 10.0.1.0/24 przez połączenie sieci VPN, a nie bezpośrednio za pośrednictwem ExpressRoute bez ochrony sieci VPN.

> [!WARNING]
> Jeśli anonsuje te **same** prefiksy dla połączeń EXPRESSROUTE i VPN, platforma Azure będzie **używać ścieżki ExpressRoute bezpośrednio bez ochrony sieci VPN**.
>

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Utwórz wirtualną sieć WAN i koncentrator przy użyciu bram

Przed kontynuowaniem należy wykonać następujące zasoby platformy Azure i odpowiednie konfiguracje lokalne:

1. Wirtualna sieć WAN platformy Azure
2. Wirtualne Centrum sieci WAN z [bramą ExpressRoute](virtual-wan-expressroute-portal.md) i [bramą sieci VPN](virtual-wan-site-to-site-portal.md)

Zapoznaj się z tematem [Tworzenie skojarzenia ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure](virtual-wan-expressroute-portal.md) , aby uzyskać instrukcje dotyczące tworzenia wirtualnej sieci WAN platformy Azure i centrum z skojarzeniem ExpressRoute oraz [utworzyć połączenie lokacja-lokacja przy użyciu wirtualnej sieci WAN platformy Azure](virtual-wan-site-to-site-portal.md) w celu wykonania kroków tworzenia sieci VPN Brama w wirtualnej sieci WAN.

## <a name="site"></a>2. Utwórz witrynę dla sieci lokalnej

Zasób lokacji jest taki sam jak Lokacje sieci VPN inne niż ExpressRoute dla wirtualnej sieci WAN. Kluczową kwestią jest to, że adres IP lokalnego urządzenia sieci VPN może teraz być prywatnym adresem IP lub publicznym adresem IP w sieci lokalnej dostępnej za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute utworzonej w kroku 1.

> [!NOTE]
> Adres IP lokalnego urządzenia sieci VPN musi być częścią prefiksów adresów anonsowanego dla wirtualnego koncentratora sieci WAN za pośrednictwem prywatnej komunikacji równorzędnej Azure ExpressRoute.
>

1. Przejdź do Azure Portal w przeglądarce. Kliknij utworzoną sieć WAN. Na stronie sieć WAN w obszarze **łączność**kliknij pozycję **Lokacje sieci VPN** , aby otworzyć stronę witryny sieci VPN.

2. Na stronie **Lokacje sieci VPN** kliknij pozycję **+Utwórz lokację**.

3. Na stronie **Tworzenie lokacji** wypełnij następujące pola:

   * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
   * **Grupa zasobów** — wybierz lub Utwórz grupę zasobów, której chcesz użyć.
   * **Region** — region platformy Azure dla zasobu witryny sieci VPN.
   * **Nazwa** — nazwa, przez którą chcesz odwołać się do lokacji lokalnej.
   * **Dostawca urządzenia** — dostawca lokalnego urządzenia sieci VPN.
   * **Border Gateway Protocol** — wybierz pozycję "Włącz", jeśli sieć lokalna używa protokołu BGP.
   * **Prywatna przestrzeń adresowa** — to przestrzeń adresowa IP znajdująca się w lokacji lokalnej. Ruch przeznaczony dla tej przestrzeni adresowej jest kierowany do sieci lokalnej za pośrednictwem bramy sieci VPN.
   * **Centra** — wybierz co najmniej jeden koncentrator, aby połączyć tę LOKACJĘ sieci VPN. Wybrane centra muszą mieć już utworzone bramy sieci VPN.

4. Kliknij przycisk **Dalej: linki >** ustawień łącza sieci VPN:

   * **Nazwa łącza** — nazwa, za pomocą której chcesz odwołać się do tego połączenia.
   * **Nazwa dostawcy** — Nazwa usługodawcy internetowego dla tej lokacji. W przypadku sieci lokalnej ExpressRoute nazwa dostawcy usług ExpressRoute.
   * **Szybkość** — szybkość łącza usługi internetowej lub obwodu ExpressRoute.
   * **Adres IP** — publiczny adres IP urządzenia sieci VPN znajdującego się w lokacji lokalnej. Lub, w przypadku ExpressRoute lokalnego, prywatny adres IP urządzenia sieci VPN za pośrednictwem ExpressRoute.

   Jeśli protokół BGP jest włączony, będzie miał zastosowanie do wszystkich połączeń utworzonych dla tej witryny na platformie Azure. Konfigurowanie protokołu BGP w wirtualnej sieci WAN jest równoznaczne z skonfigurowaniem protokołu BGP na bramie sieci VPN platformy Azure. Adres lokalnego elementu równorzędnego protokołu BGP *nie może* być taki sam, jak adres IP sieci VPN do urządzenia lub przestrzeń adresowa wirtualnej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla adresu IP elementu równorzędnego protokołu BGP. Może to być adres przypisany do interfejsu sprzężenia zwrotnego na urządzeniu. Jednak *nie może* to być adresowanie APIPA (169,254. *x*. *x*). Ten adres należy określić w odpowiedniej bramie sieci lokalnej reprezentującej daną lokalizację. Wymagania wstępne dotyczące protokołu BGP zawiera temat [Informacje o protokole BGP z platformą Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

5. Kliknij przycisk **Dalej: przegląd + utwórz >** , aby sprawdzić wartości ustawień i utworzyć witrynę sieci VPN. W przypadku wybrania **centrów** do połączenia zostanie nawiązane połączenie między siecią lokalną a centralną bramą sieci VPN.

## <a name="hub"></a>3. Zaktualizuj ustawienie połączenia sieci VPN, aby używać ExpressRoute

Po utworzeniu witryny sieci VPN i połączeniu z centrum wykonaj następujące kroki, aby skonfigurować połączenie do korzystania z prywatnej komunikacji równorzędnej ExpressRoute:

1. Wróć do strony zasobów wirtualnej sieci WAN, a następnie kliknij zasób centrum. Lub przejdź między lokacją sieci VPN a podłączonym koncentratorem.

2. W obszarze **łączność**kliknij pozycję **VPN (lokacja-lokacja).**

3. Kliknij pozycję "..." w witrynie sieci VPN za pośrednictwem ExpressRoute i wybierz pozycję "**Edytuj połączenie sieci VPN z tym centrum**"

4. Wybierz pozycję "tak" w obszarze "**Użyj prywatnego adresu IP platformy Azure**". Ustawienie konfiguruje bramę sieci VPN Hub do używania prywatnych adresów IP w ramach zakresu adresów koncentratora na bramie dla tego połączenia zamiast publicznych adresów IP. Zapewni to, że ruch z sieci lokalnej przechodzi przez prywatne ścieżki komunikacji równorzędnej ExpressRoute zamiast używać publicznego Internetu dla tego połączenia sieci VPN. Poniższy zrzut ekranu przedstawia okno Ustawienia.

   ![Ustawienie połączenia sieci VPN](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. Kliknij pozycję **Zapisz**.

Po zapisaniu Brama sieci VPN centrum będzie używać prywatnych adresów IP w bramie sieci VPN do nawiązywania połączeń protokołu IPsec/IKE z lokalnym urządzeniem sieci VPN za pośrednictwem usługi ExpressRoute.

## <a name="associate"></a>4. Pobierz prywatne adresy IP bramy sieci VPN Hub

Pobierz konfigurację urządzeń sieci VPN w celu uzyskania prywatnych adresów IP bramy sieci VPN centrum. Są one konieczne do konfigurowania lokalnego urządzenia sieci VPN.

1. Na stronie centrum kliknij pozycję **Sieć VPN (lokacja-lokacja)** w obszarze **łączność** .

2. W górnej części strony Przegląd kliknij pozycję **Pobierz konfigurację sieci VPN**. Platforma Azure tworzy konto magazynu w grupie zasobów "Microsoft-Network-[Location]", gdzie lokalizacja jest lokalizacją sieci WAN. Po zastosowaniu konfiguracji na urządzeniach sieci VPN możesz usunąć to konto magazynu.

3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.

4. Zastosuj konfigurację na urządzeniu sieci VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Opis pliku konfiguracji urządzenia sieci VPN

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** ta sekcja zawiera szczegółowe informacje o urządzeniu skonfigurowanym jako lokacja połączona z wirtualną sieci WAN. Zawiera nazwę i publiczny adres IP urządzenia w oddziale.
* **vpnSiteConnections —** Ta sekcja zawiera informacje o następujących ustawieniach:

    * **Przestrzeń adresowa** sieci wirtualnej koncentratora<br/>Przykład:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * **Przestrzeń adresowa** sieci wirtualnych, które są połączone z koncentratorem<br>Przykład:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * **Adresy IP** bramy vpngateway koncentratora wirtualnego. Ponieważ każde połączenie bramy vpngateway składa się z dwóch tuneli w konfiguracji Active-Active, zobaczysz oba adresy IP wymienione w tym pliku. W tym przykładzie zobaczysz "Instance0" i "Wystąpienia1" dla każdej witryny i są one prywatnymi adresami IP, a nie publicznymi adresami IP.<br>Przykład:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * **Bramy vpngateway szczegóły konfiguracji połączenia** , takie jak BGP, klucz wstępny itp. PSK to klucz wstępny, który jest generowany automatycznie. Zawsze możesz edytować połączenie na stronie Przegląd, aby użyć niestandardowego klucza wstępnego.
  
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

* Instrukcje na stronie urządzeń sieci VPN nie zostały napisane dla usługi Virtual WAN, ale możesz skorzystać z wartości z pliku konfiguracji usługi Virtual WAN, aby ręcznie skonfigurować urządzenie sieci VPN. 
* Skrypty konfiguracji urządzenia do pobrania przeznaczone dla usługi VPN Gateway nie działają w przypadku usługi Virtual WAN, ponieważ ich konfiguracja różni się.
* Nowa usługa Virtual WAN może obsługiwać zarówno protokół IKEv1, jak i IKEv2.
* W usłudze Virtual WAN można używać tylko instrukcji i urządzeń sieci VPN opartych na trasach.

## <a name="viewwan"></a>5. Wyświetl wirtualną sieć WAN

1. Przejdź do wirtualnej sieci WAN.

2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator. Umieść kursor na dowolnym punkcie, aby wyświetlić podsumowanie kondycji koncentratora.

3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.

## <a name="viewhealth"></a>6. Wyświetl kondycję zasobów

1. Przejdź do sieci WAN.

2. Na stronie sieci WAN w sekcji **Pomoc techniczna i rozwiązywanie problemów** kliknij pozycję **Kondycja** i wyświetl zasób.

## <a name="connectmon"></a>7. monitorowanie połączenia

Utwórz połączenie, aby monitorować komunikację pomiędzy maszyną wirtualną platformy Azure a zdalną lokacją. Aby uzyskać informacje dotyczące konfigurowania monitora połączeń, zobacz [Monitorowanie komunikacji sieciowej](~/articles/network-watcher/connection-monitor.md). Pole Źródło zawiera adres IP maszyny wirtualnej na platformie Azure, a docelowy adres IP to adres IP lokacji.

## <a name="cleanup"></a>8. Wyczyść zasoby

Jeśli te zasoby nie są już potrzebne, możesz usunąć grupę zasobów i wszystkie zawarte w niej zasoby, używając polecenie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

Ten artykuł pomaga utworzyć połączenie sieci VPN przez prywatną komunikację równorzędną ExpressRoute za pomocą wirtualnej sieci WAN. Aby dowiedzieć się więcej o wirtualnej sieci WAN i innych powiązanych funkcjach, zobacz stronę [Omówienie wirtualnej sieci WAN](virtual-wan-about.md) .
