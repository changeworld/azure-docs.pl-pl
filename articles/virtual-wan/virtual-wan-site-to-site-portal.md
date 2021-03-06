---
title: 'Wirtualna sieć WAN platformy Azure: tworzenie połączeń lokacja-lokacja'
description: Z tego samouczka dowiesz się, jak utworzyć połączenie lokacja-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: b4278cb2e8c5152f522258a37c37acda5efbacf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239686"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan"></a>Samouczek: tworzenie połączenia lokacja-lokacja przy użyciu usługi Azure Virtual WAN

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv1 i IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie koncentratora
> * Tworzenie lokacji
> * Łączenie lokacji z koncentratorem
> * Łączenie witryny sieci VPN z koncentratorem
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobieranie pliku konfiguracyjnego
> * Wyświetlanie wirtualnej sieci WAN

> [!NOTE]
> Jeśli masz wiele lokacji, skorzystaj z usług [partnera usługi Virtual WAN](https://aka.ms/virtualwan) w celu utworzenia tej konfiguracji. Możesz też utworzyć tę konfigurację samodzielnie, jeśli masz wiedzę z zakresu sieci i doświadczenie w konfigurowaniu urządzeń sieci VPN.
>

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Masz sieć wirtualną, z którą chcesz się połączyć. Sprawdź, czy żadna z podsieci sieci lokalnych nie pokrywa się z sieciami wirtualnymi, z którymi chcesz się połączyć. Aby utworzyć sieć wirtualną w witrynie Azure portal, zobacz [przewodnik Szybki start](../virtual-network/quick-create-portal.md).

* Sieć wirtualna nie ma żadnych bram sieci wirtualnej. Jeśli sieć wirtualna ma bramę (sieć VPN lub usługę ExpressRoute), należy usunąć wszystkie bramy. Ta konfiguracja wymaga połączenia sieci wirtualnych z bramą koncentratora wirtualnej sieci WAN.

* Uzyskaj zakres adresów IP w regionie koncentratora. Koncentrator jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Zakres adresów określony dla koncentratora nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi nawiązujesz połączenie. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, należy koordynować z kimś, kto może podać te szczegóły dla Ciebie.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Tworzenie wirtualnej sieci WAN

Przejdź w przeglądarce do witryny Azure Portal i zaloguj się przy użyciu konta platformy Azure.

1. Przejdź do strony Wirtualna sieć WAN. W portalu kliknij pozycję **+Utwórz zasób**. Wpisz **wirtualną sieć WAN** w polu wyszukiwania i wybierz pozycję Enter.
2. Wybierz **wirtualną sieć WAN** z wyników. Na stronie Wirtualna sieć WAN kliknij pozycję **Utwórz,** aby otworzyć stronę Utwórz sieć WAN.
3. Na stronie **Tworzenie wan** na karcie **Podstawy** wypełnij następujące pola:

   ![Wirtualna sieć WAN](./media/virtual-wan-site-to-site-portal/vwan.png)

   * **Subskrypcja** — wybierz subskrypcję, która ma być używana.
   * **Grupa zasobów** — tworzenie nowych lub używanie istniejących.
   * **Lokalizacja grupy zasobów** — wybierz lokalizację zasobu z listy rozwijanej. Sieć WAN to zasób globalny i nie znajduje się w konkretnym regionie. Konieczne jest jednak wybranie regionu, aby łatwiej zarządzać utworzonym zasobem sieci WAN i go lokalizować.
   * **Name** - Wpisz nazwę, którą chcesz wywołać w wan.
   * **Typ:** Podstawowy lub standardowy. Jeśli utworzysz podstawową sieć WAN, można utworzyć tylko centrum podstawowe. Podstawowe koncentratory mogą ować tylko łączność między lokacjami sieci VPN.
4. Po zakończeniu wypełniania pól wybierz pozycję **Przejrzyj +Utwórz**.
5. Po przejściu weryfikacji wybierz **pozycję Utwórz,** aby utworzyć wirtualną sieć WAN.

## <a name="create-a-hub"></a><a name="hub"></a>Tworzenie koncentratora

Koncentrator to sieć wirtualna, która może zawierać bramy dla funkcji lokacja-lokacja, usługi ExpressRoute lub point-to-site. Po utworzeniu koncentratora będzie za niego naliczana opłata, nawet jeśli nie dołączysz żadnych lokacji. Utworzenie bramy sieci VPN między lokacjami w centrum wirtualnym zajmuje 30 minut.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="create-a-site"></a><a name="site"></a>Tworzenie lokacji

Teraz możesz przystąpić do tworzenia witryn odpowiadających Twoim fizycznym lokalizacjom. Możesz utworzyć dowolną liczbę lokacji, tak aby odpowiadała liczbie lokalizacji fizycznych. Jeśli na przykład masz oddziały w Nowym Jorku, Londynie i Los Angeles, należy utworzyć trzy oddzielne lokacje. W tych lokacjach znajdują się punkty końcowe lokalnych urządzeń sieci VPN. W wirtualnej sieci WAN można utworzyć maksymalnie 1000 lokacji na koncentrator wirtualny. Jeśli masz wiele koncentratorów, można utworzyć 1000 na każdy z tych koncentratorów. Jeśli masz urządzenie CPE partnera wirtualnej sieci WAN (wstawianie łącza), skontaktuj się z nimi, aby dowiedzieć się więcej o ich automatyzacji na platformie Azure. Zazwyczaj automatyzacja oznacza proste środowisko kliknięcia, aby wyeksportować informacje o gałęziach na dużą skalę do platformy Azure i skonfigurować łączność z CPE do bramy sieci VPN wirtualnej platformy Azure Virtual WAN. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące automatyzacji od platformy Azure do partnerów CPE.](virtual-wan-configure-automation-providers.md)

[!INCLUDE [Create a site](../../includes/virtual-wan-tutorial-s2s-site-include.md)]

## <a name="connect-the-vpn-site-to-the-hub"></a><a name="connectsites"></a>Łączenie witryny sieci VPN z centrum

W tym kroku należy połączyć witrynę sieci VPN z centrum.

[!INCLUDE [Connect VPN sites](../../includes/virtual-wan-tutorial-s2s-connect-vpn-site-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Podłączanie sieci wirtualnej do koncentratora

W tym kroku utworzysz połączenie między koncentratorem a siecią wirtualną. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie sieci wirtualnej WAN kliknij pozycję **Połączenia sieci wirtualnych**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij **przycisk OK,** aby utworzyć połączenie sieci wirtualnej.

## <a name="download-vpn-configuration"></a><a name="device"></a>Pobieranie konfiguracji sieci VPN

Skorzystaj z konfiguracji urządzenia sieci VPN, aby skonfigurować swoje lokalne urządzenie sieci VPN.

1. Na stronie usługi Virtual WAN kliknij pozycję **Przegląd**.
2. W górnej części strony **Hub ->VPNSite** kliknij pozycję **Pobierz usługę VPN config**. Platforma Azure tworzy konto magazynu w grupie zasobów "microsoft-network-[location]", gdzie lokalizacja jest lokalizacją sieci WAN. Po zastosowaniu konfiguracji na urządzeniach sieci VPN możesz usunąć to konto magazynu.
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Zastosuj konfigurację do lokalnego urządzenia sieci VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Opis pliku konfiguracji urządzenia sieci VPN

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** ta sekcja zawiera szczegółowe informacje o urządzeniu skonfigurowanym jako lokacja połączona z wirtualną sieci WAN. Zawiera nazwę i publiczny adres IP urządzenia w oddziale.
* **vpnSiteConnections -** Ta sekcja zawiera informacje o następujących ustawieniach:

    * **Przestrzeń adresowa** sieci wirtualnej koncentratora<br>Przykład:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Przestrzeń adresowa** sieci wirtualnych, które są połączone z koncentratorem<br>Przykład:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Adresy IP** bramy vpngateway koncentratora wirtualnego. Ponieważ każde połączenie vpngateway składa się z dwóch tuneli w konfiguracji aktywny-aktywny, zobaczysz oba adresy IP wymienione w tym pliku. W tym przykładzie są to wartości „Instance0” i „Instance1” dla każdej lokacji.<br>Przykład:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Szczegóły konfiguracji połączenia vpngateway,** takie jak BGP, klucz wstępny itp. PSK jest kluczem wstępnym, który jest generowany automatycznie dla Ciebie. Zawsze możesz edytować połączenie na stronie Przegląd, aby użyć niestandardowego klucza wstępnego.
  
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
                  "10.3.0.0/16"
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
> Jeśli używasz rozwiązania partnera usługi Virtual WAN, konfiguracja urządzenia sieci VPN odbywa się automatycznie. Kontroler urządzenia uzyskuje plik konfiguracji z platformy Azure i stosuje go do urządzenia w celu skonfigurowania połączenia z platformą Azure. Oznacza to, że nie musisz wiedzieć, w jaki sposób skonfigurować urządzenie sieci VPN ręcznie.
>

Jeśli chcesz wiedzieć, jak skonfigurować urządzenie, możesz skorzystać z instrukcji na [stronie ze skryptami konfigurowania urządzenia sieci VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), z następującymi zastrzeżeniami:

* Instrukcje na stronie urządzeń sieci VPN nie zostały napisane dla usługi Virtual WAN, ale możesz skorzystać z wartości z pliku konfiguracji usługi Virtual WAN, aby ręcznie skonfigurować urządzenie sieci VPN. 
* Skrypty konfiguracji urządzenia do pobrania przeznaczone dla usługi VPN Gateway nie działają w przypadku usługi Virtual WAN, ponieważ ich konfiguracja różni się.
* Nowa wirtualna sieć WAN może obsługiwać zarówno IKEv1, jak i IKEv2.
* Wirtualna sieć WAN może używać zarówno opartych na zasadach, jak i opartych na trasach urządzeń sieci VPN oraz instrukcji urządzenia.

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na **stronie Przegląd** każdy punkt na mapie reprezentuje koncentrator. Umieść wskaźnik myszy na dowolnym punkcie, aby wyświetlić podsumowanie kondycji koncentratora, stan połączenia i bajty w i na zewnątrz.
3. W sekcji Koncentratory i połączenia można wyświetlać stan koncentratora, witryn sieci VPN itp. Możesz kliknąć określoną nazwę centrum i przejść do witryny sieci VPN, aby uzyskać dodatkowe informacje.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
