---
title: Tworzenie połączenia lokacja-lokacja z platformą Azure w usłudze Azure Virtual WAN | Microsoft Docs
description: Z tego samouczka dowiesz się, jak utworzyć połączenie lokacja-lokacja VPN z platformą Azure w usłudze Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 07/13/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my local site to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: ea36a3d4a2471cee6a18d70275aaf2e83ffc6f39
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159655"
---
# <a name="tutorial-create-a-site-to-site-connection-using-azure-virtual-wan-preview"></a>Samouczek: tworzenie połączenia lokacja-lokacja przy użyciu usługi Azure Virtual WAN (wersja zapoznawcza)

W tym samouczku pokazano, w jaki sposób przy użyciu usługi Azure Virtual WAN utworzyć połączenie z zasobami na platformie Azure za pośrednictwem połączenia sieci VPN protokołu IPsec/IKE (IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md)

> [!NOTE]
> Jeśli masz wiele lokacji, skorzystaj z usług [partnera usługi Virtual WAN](https://aka.ms/virtualwan) w celu utworzenia tej konfiguracji. Możesz też utworzyć tę konfigurację samodzielnie, jeśli masz wiedzę z zakresu sieci i doświadczenie w konfigurowaniu urządzeń sieci VPN.
>

![Diagram usługi Virtual WAN](./media/virtual-wan-about/virtualwan.png)

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie lokacji
> * Tworzenie koncentratora
> * Łączenie koncentratora z lokacją
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobieranie i stosowanie konfiguracji urządzenia sieci VPN
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetlanie kondycji zasobu
> * Monitorowanie połączenia

> [!IMPORTANT]
> Usługa Azure Virtual WAN jest obecnie dostępna w zarządzanej publicznej wersji zapoznawczej. Aby korzystać z usługi Virtual WAN, należy najpierw [zarejestrować się w wersji zapoznawczej](#enroll).
>
> Ten podgląd publiczny nie jest objęty umową dotyczącą poziomu usług i nie należy korzystać z niego w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości lub mogą nie być dostępne we wszystkich lokalizacjach platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Upewnij się, ze masz zgodne urządzenie sieci VPN oparte na trasach, które może korzystać z protokołu IKEv2, oraz masz do dyspozycji osobę, która potrafi je skonfigurować. Jeśli współpracujesz z partnerem usługi Virtual WAN, ustawienia konfiguracji zostaną utworzone automatycznie i nie musisz wiedzieć, jak skonfigurować urządzenie ręcznie.
* Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN. Ten adres IP nie może się znajdować za translatorem adresów sieciowych.
* Jeśli masz już sieć wirtualną, z którą chcesz nawiązać połączenie, sprawdź, czy żadna z podsieci Twojej sieci lokalnej nie pokrywa się z sieciami wirtualnymi, z którymi chcesz nawiązać połączenie. Sieć wirtualna nie wymaga podsieci bramy i nie może mieć żadnych bram sieci wirtualnej. Jeśli nie masz sieci wirtualnej, możesz ją utworzyć, wykonując czynności opisane w tym artykule.
* Uzyskaj zakres adresów IP w regionie koncentratora. Koncentrator jest siecią wirtualną, a zakres adresów określony dla regionu koncentratora nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi chcesz nawiązać połączenie. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="enroll"></a>1. Rejestracja w wersji zapoznawczej

Aby móc skonfigurować usługę Virtual WAN, należy najpierw zarejestrować swoją subskrypcję w wersji zapoznawczej. Jeśli tego nie zrobisz, usługa Virtual WAN nie będzie dostępna w portalu. Aby się zarejestrować, wyślij wiadomość e-mail na adres **azurevirtualwan@microsoft.com**, podając identyfikator subskrypcji. Gdy subskrypcja zostanie zarejestrowana, otrzymasz wiadomość e-mail.

## <a name="vnet"></a>2. Tworzenie sieci wirtualnej

Jeśli nie masz jeszcze sieci wirtualnej, możesz ją szybko utworzyć za pomocą programu PowerShell. Możesz również utworzyć sieć wirtualną w witrynie Azure Portal.

* Sprawdź, czy przestrzeń adresowa utworzona dla sieci wirtualnej nie pokrywa się z żadnymi zakresami adresów innych sieci wirtualnych, z którymi chcesz się połączyć, ani z przestrzeniami adresowymi sieci lokalnej. 
* Jeśli masz już sieć wirtualną, sprawdź, czy spełnia ona wymagane kryteria i nie ma bramy sieci wirtualnej.

Możesz łatwo utworzyć sieć wirtualną, klikając polecenie „Wypróbuj” w tym artykule, co spowoduje otwarcie konsoli programu PowerShell. Dopasuj wartości, a następnie skopiuj polecenia i wklej je do okna konsoli.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Dostosuj polecenia programu PowerShell, a następnie utwórz grupę zasobów.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Tworzenie sieci wirtualnej

Dostosuj polecenia programu PowerShell, aby utworzyć sieć wirtualną zgodną z Twoim środowiskiem.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```

## <a name="wan"></a>3. Tworzenie wirtualnej sieci WAN

1. Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. W tym momencie usługę Virtual WAN można znaleźć, przechodząc do pozycji **Wszystkie usługi** i wyszukując nazwę Virtual WAN. Możesz również wyszukać usługę Virtual WAN, używając pola wyszukiwania w górnej części witryny Azure Portal. Kliknij pozycję **Virtual WAN**, aby otworzyć stronę usługi.
3. Kliknij polecenie **Utwórz**, aby otworzyć stronę **Tworzenie sieci WAN**. Jeśli strona jest niedostępna, oznacza to, że użytkownik nie został jeszcze zatwierdzony do korzystania z wersji zapoznawczej.

  ![Tworzenie sieci WAN](./media/virtual-wan-site-to-site-portal/createwan.png)
4. Na stronie Tworzenie sieci WAN wypełnij następujące pola.

  * **Nazwa** — wybierz nazwę sieci WAN.
  * **Subskrypcja** — wybierz subskrypcję, która ma być używana.
  * **Grupa zasobów**— utwórz nową lub użyj istniejącej.
  * **Lokalizacja zasobu** — wybierz lokalizację zasobu z listy rozwijanej. Sieć WAN to zasób globalny i nie znajduje się w konkretnym regionie. Konieczne jest jednak wybranie regionu, aby łatwiej zarządzać utworzonym zasobem sieci WAN i go lokalizować.
5. Kliknij pozycję **Utwórz**, aby utworzyć sieć WAN.

## <a name="site"></a>4. Tworzenie lokacji

Możesz utworzyć dowolną liczbę lokacji, tak aby odpowiadała liczbie lokalizacji fizycznych. Jeśli na przykład masz oddziały w Nowym Jorku, Londynie i Los Angeles, należy utworzyć trzy oddzielne lokacje. W tych lokacjach znajdują się punkty końcowe lokalnych urządzeń sieci VPN. Obecnie można określić tylko jedną prywatną przestrzeń adresową na lokację.

1. Przejdź do pozycji **Wszystkie zasoby**.
2. Kliknij utworzoną wirtualną sieć WAN.
3. Kliknij przycisk **+ Utwórz lokację** w górnej części strony, aby otworzyć stronę **Tworzenie lokacji**.

  ![nowa lokacja](media/virtual-wan-site-to-site-portal/createsite.png)
4. Na stronie **Tworzenie lokacji** wypełnij następujące pola:

  *  **Nazwa** — jest to nazwa używana do odwoływania się do lokacji lokalnej.
  *  **Publiczny adres IP** — jest to publiczny adres IP urządzenia sieci VPN znajdującego się w lokacji lokalnej.
  *  **Prywatna przestrzeń adresowa** — to przestrzeń adresowa IP znajdująca się w lokacji lokalnej. Ruch do tej przestrzeni adresowej jest kierowany do lokacji lokalnej.
  *  **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
  *  **Grupa zasobów** — grupa zasobów, której chcesz używać.
5. Kliknij pozycję **Pokaż zaawansowane**, aby wyświetlić dodatkowe ustawienia. Możesz **włączyć protokół BGP** — jest to pole opcjonalne, które spowoduje włączenie tej funkcji dla wszystkich połączeń utworzonych dla tej lokacji na platformie Azure. Możesz też wprowadzić **Informacje o urządzeniu** (pole opcjonalne). Pomogą one zespołowi platformy Azure lepiej zrozumieć środowisko i dodać nowe opcje optymalizacji w przyszłości. Mogą też ułatwić Ci rozwiązywanie problemów.

  ![BGP](media/virtual-wan-site-to-site-portal/sitebgp.png)
6. Kliknij polecenie **Potwierdź**, aby utworzyć lokację.
7. Powtórz te czynności dla każdej lokacji, którą chcesz utworzyć.

## <a name="hub"></a>5. Tworzenie koncentratora i łączenie lokacji

1. Na stronie usługi Virtual WAN kliknij pozycję **Lokacje**.
2. W obszarze **Nieskojarzone lokacje** zostanie wyświetlona lista lokacji, które nie zostały jeszcze połączone z koncentratorem.
3. Wybierz lokacje, które chcesz skojarzyć.
4. Z listy rozwijanej wybierz region, z którym koncentrator ma zostać skojarzony. Należy skojarzyć koncentrator z regionem, w którym znajdują się sieci wirtualne, z którymi chcesz nawiązać połączenie.
5. Kliknij pozycję **Potwierdź**. Jeśli nie masz jeszcze koncentratora w tym regionie, zostanie automatycznie utworzona sieć wirtualna koncentratora. W takim przypadku zostanie wyświetlona strona **Utwórz centra regionalne**.
6. Na stronie **Utwórz centra regionalne** wprowadź zakres adresów dla sieci wirtualnej koncentratora. Jest to sieć wirtualna, która będzie zawierać usługi koncentratora. Wprowadzony tutaj zakres musi być zakresem prywatnych adresów IP i nie może pokrywać się z żadną z lokalnych przestrzeni adresowych lub przestrzeni adresowych sieci wirtualnej. Zostanie wtedy utworzony punkt końcowy sieci wirtualnej w sieci wirtualnej koncentratora. Automatyczne tworzenie koncentratora i bramy jest możliwe wyłącznie w portalu.
7. Kliknij pozycję **Utwórz**.

## <a name="vnet"></a>6. Łączenie sieci wirtualnej z koncentratorem

W tym kroku zostanie utworzone połączenie równorzędne pomiędzy koncentratorem i siecią wirtualną. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie usługi Virtual WAN kliknij pozycję **Połączenie sieci wirtualnej**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.

## <a name="device"></a>7. Pobieranie konfiguracji sieci VPN

Skorzystaj z konfiguracji urządzenia sieci VPN, aby skonfigurować swoje lokalne urządzenie sieci VPN.

1. Na stronie usługi Virtual WAN kliknij pozycję **Przegląd**.
2. W górnej części strony Przegląd kliknij pozycję **Pobierz konfigurację sieci VPN**. Platforma Azure utworzy konto magazynu w grupie zasobów „microsoft-network-[lokalizacja]”, gdzie „lokalizacja” to lokalizacja sieci WAN. Po zastosowaniu konfiguracji na urządzeniach sieci VPN możesz usunąć to konto magazynu.
3. Po zakończeniu tworzenia pliku możesz kliknąć link, aby go pobrać.
4. Zastosuj konfigurację na urządzeniu sieci VPN.

### <a name="understanding-the-vpn-device-configuration-file"></a>Opis pliku konfiguracji urządzenia sieci VPN

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** ta sekcja zawiera szczegółowe informacje o urządzeniu skonfigurowanym jako lokacja połączona z wirtualną sieci WAN. Zawiera nazwę i publiczny adres IP urządzenia w oddziale.
* **vpnSiteConnections —** ta sekcja zawiera informacje dotyczące następujących kwestii:

    * **Przestrzeń adresowa** sieci wirtualnej koncentratora<br>Przykład:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Przestrzeń adresowa** sieci wirtualnych, które są połączone z koncentratorem<br>Przykład:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Adresy IP** bramy vpngateway koncentratora wirtualnego. Ponieważ każde połączenie z bramą vpngateway obejmuje dwa tunele w konfiguracji aktywne-aktywne, w pliku wymienione będą oba adresy IP. W tym przykładzie są to wartości „Instance0” i „Instance1” dla każdej lokacji.<br>Przykład:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Szczegóły konfiguracji połączenia bramy vpngateway**, takie jak protokół BGP, klucz wstępny i tym podobne. PSK to klucz wstępny, który jest generowany automatycznie. Zawsze możesz edytować połączenie na stronie Przegląd, aby użyć niestandardowego klucza wstępnego.
  
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
> Jeśli używasz rozwiązania partnera usługi Virtual WAN, konfiguracja urządzenia sieci VPN odbywa się automatycznie, gdy kontroler urządzenia uzyskuje plik konfiguracji z platformy Azure i stosuje go na urządzeniu w celu skonfigurowania połączenia z platformą Azure. Oznacza to, że nie musisz wiedzieć, w jaki sposób skonfigurować urządzenie sieci VPN ręcznie.
>

Jeśli chcesz wiedzieć, jak skonfigurować urządzenie, możesz skorzystać z instrukcji na [stronie ze skryptami konfigurowania urządzenia sieci VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts), z następującymi zastrzeżeniami:

* Instrukcje na stronie urządzeń sieci VPN nie zostały napisane dla usługi Virtual WAN, ale możesz skorzystać z wartości z pliku konfiguracji usługi Virtual WAN, aby ręcznie skonfigurować urządzenie sieci VPN. 
* Skrypty konfiguracji urządzenia do pobrania przeznaczone dla usługi VPN Gateway nie działają w przypadku usługi Virtual WAN, ponieważ ich konfiguracja różni się.
* W usłudze Virtual WAN można używać tylko protokołu IKEv2, a nie IKEv1.
* W usłudze Virtual WAN można używać tylko instrukcji i urządzeń sieci VPN opartych na trasach.

## <a name="viewwan"></a>8. Wyświetlanie wirtualnej sieci WAN

1. Przejdź do wirtualnej sieci WAN.
2. Na stronie Przegląd każdy punkt na mapie przedstawia koncentrator. Umieść kursor na dowolnym punkcie, aby wyświetlić podsumowanie kondycji koncentratora.
3. W sekcji dotyczącej koncentratorów i połączeń możesz wyświetlić stan koncentratora, lokację, region, stan połączenia sieci VPN oraz bajty przychodzące i wychodzące.

## <a name="viewhealth"></a>9. Wyświetlanie kondycji zasobów

1. Przejdź do sieci WAN.
2. Na stronie sieci WAN w sekcji **Pomoc techniczna i rozwiązywanie problemów** kliknij pozycję **Kondycja** i wyświetl zasób.

## <a name="connectmon"></a>10. Monitorowanie połączenia

Utwórz połączenie, aby monitorować komunikację pomiędzy maszyną wirtualną platformy Azure a zdalną lokacją. Aby uzyskać informacje dotyczące konfigurowania monitora połączeń, zobacz [Monitorowanie komunikacji sieciowej](~/articles/network-watcher/connection-monitor.md). Pole Źródło zawiera adres IP maszyny wirtualnej na platformie Azure, a docelowy adres IP to adres IP lokacji.

## <a name="cleanup"></a>11. Oczyszczanie zasobów

Gdy grupa zasobów i zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Zastąp wartość „myResourceGroup” nazwą grupy zasobów, a następnie uruchom następujące polecenie programu PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="feedback"></a>Opinie dotyczące wersji zapoznawczej

Będziemy wdzięczni za przesłanie opinii. Wyślij wiadomość e-mail na adres <azurevirtualwan@microsoft.com>, jeśli chcesz zgłosić problem lub wyrazić swoją opinię (pozytywną lub negatywną) dotyczącą usługi Virtual WAN. W wierszu tematu wpisz nazwę firmy w nawiasie kwadratowym („[ ]”). Jeśli zgłaszasz problem, dołącz również swój identyfikator subskrypcji.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci WAN
> * Tworzenie lokacji
> * Tworzenie koncentratora
> * Łączenie koncentratora z lokacją
> * Łączenie sieci wirtualnej z koncentratorem
> * Pobieranie i stosowanie konfiguracji urządzenia sieci VPN
> * Wyświetlanie wirtualnej sieci WAN
> * Wyświetlanie kondycji zasobu
> * Monitorowanie połączenia

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
