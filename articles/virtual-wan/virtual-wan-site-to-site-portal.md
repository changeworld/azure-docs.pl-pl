---
title: Korzystanie z usługi Azure Virtual WAN do tworzenia połączenia typu lokacja-lokacja z platformą Azure | Microsoft Docs
description: W tym samouczku dowiesz się, jak za pomocą usługi Azure Virtual WAN utworzyć połączenie sieci VPN typu lokacja-lokacja z platformą Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 357cc23c9dedb49dfd19dc897102762066ffd1b2
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679359"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Samouczek: Tworzenie połączenia typu lokacja-lokacja przy użyciu wirtualnej sieci WAN platformy Azure

W tym samouczku pokazano, jak używać wirtualnej sieci WAN do łączenia się z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv1 i IKEv2). Ten typ połączenia wymaga, aby urządzenie sieci VPN znajdowało się w środowisku lokalnym, które ma przypisany zewnętrznie publiczny adres IP. Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [Omówienie wirtualnej sieci WAN](virtual-wan-about.md).

> [!NOTE]
> W przypadku wielu witryn zwykle można utworzyć tę konfigurację przy użyciu [wirtualnego partnera sieci WAN](https://aka.ms/virtualwan) . Tę konfigurację można jednak utworzyć samodzielnie, jeśli masz doświadczenie w pracy z siecią i wolisz, jak skonfigurować własne urządzenie sieci VPN.
>

![Wirtualny diagram sieci WAN](./media/virtual-wan-about/virtualwan.png)

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie witryny
> * Tworzenie centrum
> * Podłączanie centrum do lokacji
> * Utwórz zgodną sieć wirtualną (jeśli jeszcze jej nie masz)
> * Łączenie sieci wirtualnej z centrum
> * Pobieranie i stosowanie konfiguracji urządzenia sieci VPN
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetl kondycję zasobu
> * Monitorowanie połączenia

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1. Tworzenie wirtualnej sieci WAN

W przeglądarce przejdź do [Azure Portal](https://aka.ms/azurevirtualwanpreviewfeatures) i zaloguj się przy użyciu konta platformy Azure.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="site"></a>2. Tworzenie witryny

Utwórz tyle witryn, ile potrzebujesz, które odpowiadają lokalizacji fizycznej. Na przykład jeśli masz oddział w biurze, oddziału w Londynie i biurze oddziału i LA, utworzysz trzy osobne lokacje. Te lokacje zawierają punkty końcowe lokalnego urządzenia sieci VPN. W tej chwili można określić tylko jedną prywatną przestrzeń adresową dla witryny.

1. Kliknij utworzoną sieć WAN. Na stronie sieci WAN w obszarze **Architektura wirtualnej sieci WAN**kliknij pozycję **Lokacje sieci VPN** , aby otworzyć stronę witryny sieci VPN.
2. Na stronie **witryny sieci VPN** kliknij pozycję **+ Utwórz lokację**.
3. Na stronie **Tworzenie lokacji** wypełnij następujące pola:

   * **Nazwa** — nazwa, przez którą chcesz odwołać się do lokacji lokalnej.
   * **Publiczny adres IP** — publiczny adres IP urządzenia sieci VPN znajdującego się w lokacji lokalnej.
   * **Prywatna przestrzeń adresowa** — jest to przestrzeń adresów IP znajdująca się w lokacji lokalnej. Ruch przeznaczony dla tej przestrzeni adresowej jest kierowany do lokacji lokalnej.
   * **Subskrypcja** — Weryfikuj subskrypcję.
   * **Grupa zasobów** — Grupa zasobów, której chcesz użyć.
   * **Lokalizacja**
4. Kliknij pozycję **Pokaż zaawansowane** , aby wyświetlić dodatkowe ustawienia. 

   Możesz wybrać protokół **BGP** , aby włączyć protokół BGP, co spowoduje włączenie funkcji BGP dla wszystkich połączeń utworzonych dla tej witryny na platformie Azure. Konfigurowanie protokołu BGP w wirtualnej sieci WAN jest równoznaczne z skonfigurowaniem protokołu BGP na bramie sieci VPN platformy Azure. Adres lokalnego elementu równorzędnego protokołu BGP *nie może* być taki sam jak publiczny adres IP sieci VPN do urządzenia lub przestrzeni adresowej sieci wirtualnej witryny sieci VPN. Użyj innego adresu IP na urządzeniu sieci VPN dla adresu IP elementu równorzędnego protokołu BGP. Może być adresem przypisanym do interfejsu sprzężenia zwrotnego na urządzeniu. Jednak *nie może* to być adresowanie APIPA (169,254. *x*. *x*). Określ ten adres w odpowiedniej bramie sieci lokalnej reprezentującej lokalizację. Wymagania wstępne dotyczące protokołu BGP zawiera temat [Informacje o protokole BGP z platformą Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

   Możesz również wprowadzić **Informacje o urządzeniu** (pola opcjonalne). Dzięki temu zespół platformy Azure może lepiej zrozumieć swoje środowisko w celu dodania do niego dodatkowych możliwości optymalizacji lub ułatwienia rozwiązywania problemów.
   
5. Kliknij przycisk **Potwierdź**.
6. Po kliknięciu przycisku **Potwierdź**Sprawdź stan na stronie witryny sieci VPN. Lokacja przejdzie od **aprowizacji** do **aprowizacji**.

## <a name="hub"></a>3. Tworzenie centrum

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="associate"></a>4. Skojarz lokacje z centrum

Centra powinny być zwykle skojarzone z lokacjami znajdującymi się w tym samym regionie, w którym znajduje się sieć wirtualna.

1. Na stronie **witryny sieci VPN** wybierz lokację lub Lokacje, które chcesz skojarzyć z centrum, a następnie kliknij pozycję **+ nowe skojarzenie centrum**.
2. Na stronie **Skojarz lokacje z jedną lub większą liczbą centrów** wybierz z listy rozwijanej centrum. Lokację można skojarzyć z dodatkowymi centrami, klikając pozycję **+ Dodaj skojarzenie**.
3. W tym miejscu możesz również dodać określony element **PSK** lub użyć go jako domyślnego.
4. Kliknij przycisk **Potwierdź**.
5. Stan połączenia można wyświetlić na stronie **witryny sieci VPN** .

## <a name="vnet"></a>5. Tworzenie sieci wirtualnej

Jeśli nie masz jeszcze sieci wirtualnej, możesz ją szybko utworzyć przy użyciu programu PowerShell lub Azure Portal. Jeśli masz już sieć wirtualną, sprawdź, czy spełnia ona wymagane kryteria i nie ma bramy sieci wirtualnej.

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="vnet"></a>6. Łączenie sieci wirtualnej z centrum

W tym kroku utworzysz połączenie komunikacji równorzędnej między centrum i siecią wirtualną. Powtórz te kroki dla każdej sieci wirtualnej, która ma zostać nawiązane połączenie.

1. Na stronie wirtualnej sieci WAN kliknij pozycję **połączenia sieci wirtualnej**.
2. Na stronie połączenie sieci wirtualnej kliknij pozycję **+ Dodaj połączenie**.
3. Na stronie **Dodawanie połączenia** wypełnij następujące pola:

    * **Nazwa połączenia** — Nazwij połączenie.
    * **Centra** — wybierz centrum, które chcesz skojarzyć z tym połączeniem.
    * **Subskrypcja** — Weryfikuj subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym centrum. Sieć wirtualna nie może mieć już istniejącej bramy sieci wirtualnej.
4. Kliknij przycisk **OK** , aby utworzyć połączenie komunikacji równorzędnej.

## <a name="device"></a>7. Pobieranie konfiguracji sieci VPN

Konfiguracja urządzenia sieci VPN służy do konfigurowania lokalnego urządzenia sieci VPN.

1. Na stronie wirtualnej sieci WAN kliknij pozycję **Przegląd**.
2. W górnej części strony Przegląd kliknij pozycję **Pobierz konfigurację sieci VPN**. Platforma Azure tworzy konto magazynu w grupie zasobów "Microsoft-Network-[Location]", gdzie lokalizacja jest lokalizacją sieci WAN. Po zastosowaniu konfiguracji do urządzeń sieci VPN można usunąć to konto magazynu.
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Zastosuj konfigurację do urządzenia sieci VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Informacje o pliku konfiguracji urządzenia sieci VPN

Plik konfiguracji urządzenia zawiera ustawienia, które mają być używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas wyświetlania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** W tej sekcji przedstawiono szczegóły urządzenia skonfigurowane jako lokacje łączące się z wirtualną siecią WAN. Zawiera nazwę i publiczny adres IP urządzenia rozgałęzienia.
* **vpnSiteConnections —** Ta sekcja zawiera informacje o następujących ustawieniach:

    * **Przestrzeń adresowa** sieci wirtualnej koncentratorów wirtualnych<br>Przykład:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Przestrzeń adresowa** sieci wirtualnych, która jest połączona z centrum<br>Przykład:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Adresy IP** koncentratora wirtualnego bramy vpngateway. Ponieważ każde połączenie bramy vpngateway składa się z dwóch tuneli w konfiguracji Active-Active, zobaczysz oba adresy IP wymienione w tym pliku. W tym przykładzie dla każdej lokacji są wyświetlane "Instance0" i "Wystąpienia1".<br>Przykład:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Bramy vpngateway szczegóły konfiguracji połączenia** , takie jak BGP, klucz wstępny itp. PSK to klucz wstępny, który jest generowany automatycznie. Możesz zawsze edytować połączenie na stronie Przegląd dla niestandardowego klucza PSK.
  
### <a name="example-device-configuration-file"></a>Przykładowy plik konfiguracji urządzenia

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.30.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

### <a name="configuring-your-vpn-device"></a>Konfigurowanie urządzenia sieci VPN

>[!NOTE]
> W przypadku pracy z rozwiązaniem wirtualnego partnera sieci WAN konfiguracja urządzeń sieci VPN odbywa się automatycznie. Kontroler urządzenia uzyskuje plik konfiguracji z platformy Azure i ma zastosowanie do urządzenia w celu skonfigurowania połączenia z platformą Azure. Oznacza to, że nie musisz wiedzieć, jak ręcznie skonfigurować urządzenie sieci VPN.
>

Jeśli potrzebujesz instrukcji dotyczących konfigurowania urządzenia, możesz użyć instrukcji na [stronie skrypty konfiguracji urządzenia sieci VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) z następującymi zastrzeżeniami:

* Instrukcje na stronie urządzenia sieci VPN nie są zapisywane dla wirtualnej sieci WAN, ale można użyć wartości wirtualnej sieci WAN z pliku konfiguracji, aby ręcznie skonfigurować urządzenie sieci VPN. 
* Skrypty konfiguracji urządzenia do pobrania dla VPN Gateway nie działają w przypadku wirtualnej sieci WAN, ponieważ konfiguracja różni się.
* Nowa wirtualna sieć WAN może obsługiwać zarówno protokół IKEv1, jak i IKEv2.
* Wirtualna sieć WAN może korzystać tylko z urządzeń sieci VPN opartych na trasach i instrukcji dotyczących urządzeń.

## <a name="viewwan"></a>8. Wyświetl wirtualną sieć WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na stronie Przegląd każdy punkt na mapie reprezentuje centrum. Umieść kursor nad dowolnym punktem, aby wyświetlić podsumowanie kondycji centrum.
3. W sekcji centra i połączenia można wyświetlić stan centrum, lokację, region, stan połączenia sieci VPN oraz liczbę bajtów do i wychodzące.

## <a name="viewhealth"></a>9. Wyświetl kondycję zasobów

1. Przejdź do sieci WAN.
2. Na stronie sieci WAN w sekcji **Pomoc techniczna i rozwiązywanie problemów** kliknij pozycję **kondycja** i Wyświetl zasób.

## <a name="connectmon"></a>10. monitorowanie połączenia

Utwórz połączenie do monitorowania komunikacji między maszyną wirtualną platformy Azure a lokacją zdalną. Informacje o sposobie konfigurowania monitora połączeń znajdują się w temacie [monitorowanie komunikacji sieciowej](~/articles/network-watcher/connection-monitor.md). Pole Źródło jest adresem IP maszyny wirtualnej na platformie Azure, a docelowy adres IP to adres IP lokacji.

## <a name="cleanup"></a>11. Wyczyść zasoby

Jeśli te zasoby nie są już potrzebne, możesz usunąć grupę zasobów i wszystkie zawarte w niej zasoby, używając polecenie [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Zastąp ciąg "Moja resourceName" nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania tych instrukcji:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie witryny
> * Tworzenie centrum
> * Podłączanie centrum do lokacji
> * Łączenie sieci wirtualnej z centrum
> * Pobieranie i stosowanie konfiguracji urządzenia sieci VPN
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetl kondycję zasobu
> * Monitorowanie połączenia

Aby dowiedzieć się więcej o wirtualnej sieci WAN, zobacz stronę [Omówienie wirtualnej sieci WAN](virtual-wan-about.md) .
