---
title: Wskazówki dotyczące automatyzacji partnerów wirtualnej sieci WAN platformy Azure | Dokumenty firmy Microsoft
description: Ten artykuł pomaga partnerom skonfigurować automatyzację wirtualnej sieci WAN platformy Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 7848dda09b39f446dd218b7ce1eb2a07664bcaa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190417"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Wskazówki dotyczące automatyzacji dla partnerów wirtualnej sieci WAN

Ten artykuł pomaga zrozumieć, jak skonfigurować środowisko automatyzacji do łączenia się i konfigurowania urządzenia w oddziale (lokalnego urządzenia sieci VPN klienta lub SDWAN CPE) dla wirtualnej sieci WAN platformy Azure. Jeśli jesteś dostawcą, który udostępnia urządzenia w oddziale, które mogą pomieścić łączność sieci VPN za pośrednictwem protokołu IPsec/IKEv2 lub IPsec/IKEv1, ten artykuł jest dla Ciebie.

Urządzenie oddziałowe (lokalne urządzenie sieci VPN klienta lub kontroler SDWAN CPE) zazwyczaj używa pulpitu nawigacyjnego kontrolera/urządzenia do obsługi administracyjnej. Administratorzy rozwiązań SD-WAN często mogą używać konsoli zarządzania do wstępnej aprowizowania urządzenia przed podłączeniem go do sieci. To urządzenie obsługujące sieć VPN pobiera logikę płaszczyzny sterowania z kontrolera. Urządzenie sieci VPN lub kontroler SD-WAN może używać interfejsów API platformy Azure do automatyzacji łączności z wirtualną siecią WAN platformy Azure. Ten typ połączenia wymaga, aby urządzenie lokalne miało zewnętrznie publicznego adresu IP przypisane do niego.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Przed rozpoczęciem automatyzacji

* Sprawdź, czy urządzenie obsługuje protokół IPsec IKEv1/IKEv2. Zobacz [zasady domyślne](#default).
* Wyświetlanie [interfejsów API REST,](#additional) których używasz do automatyzacji łączności z wirtualną siecią WAN platformy Azure.
* Przetestuj środowisko portalu wirtualnej sieci WAN platformy Azure.
* Następnie zdecyduj, która część kroków łączności ma być zautomatyzowana. Co najmniej zalecamy automatyzację:

  * Kontrola dostępu
  * Przekazywanie informacji o urządzeniu w oddziale do wirtualnej sieci WAN platformy Azure
  * Pobieranie konfiguracji platformy Azure i konfigurowanie łączności z urządzenia oddziału do wirtualnej sieci WAN platformy Azure

### <a name="additional-information"></a><a name ="additional"></a>Dodatkowe informacje

* [INTERFEJS API REST](https://docs.microsoft.com/rest/api/virtualwan/virtualhubs) do automatyzacji tworzenia centrum wirtualnego
* [Interfejs API REST](https://docs.microsoft.com/rest/api/virtualwan/vpngateways) do automatyzacji bramy sieci VPN platformy Azure dla wirtualnej sieci WAN
* [INTERFEJS API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnconnections) do łączenia witryny VPNSite z centrum sieci VPN platformy Azure
* [Domyślne zasady protokołu IPsec](#default)

## <a name="customer-experience"></a><a name ="ae"></a>obsługa klienta

Poznaj oczekiwane środowisko klienta w połączeniu z wirtualną siecią WAN platformy Azure.

  1. Zazwyczaj wirtualny użytkownik sieci WAN rozpocznie proces, tworząc zasób wirtualnej sieci WAN.
  2. Użytkownik skonfiguruje dostęp do grupy zasobów opartych na jednostkach usługi dla systemu lokalnego (kontrolera oddziału lub oprogramowania do inicjowania obsługi administracyjnej urządzeń sieci VPN) w celu zapisania informacji o gałęzi w wirtualnej sieci WAN platformy Azure.
  3. Użytkownik może zdecydować w tej chwili, aby zalogować się do interfejsu użytkownika i skonfigurować poświadczenia jednostki usługi. Po zakończeniu kontroler powinien mieć możliwość przekazywania informacji o gałęzi za pomocą dostępnej automatyzacji. Ręcznym odpowiednikiem tego po stronie platformy Azure jest "Utwórz witrynę".
  4. Gdy informacje o lokacji (urządzeniu oddziałowym) będą dostępne na platformie Azure, użytkownik połączy witrynę z koncentratorem. Centrum wirtualne to sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. Może istnieć tylko jedno centrum na regionie platformy Azure, a punkt końcowy sieci VPN (vpngateway) wewnątrz niego jest tworzony podczas tego procesu. Brama sieci VPN to skalowalna brama, która rozmiaruje się odpowiednio w zależności od potrzeb przepustowości i połączenia. Możesz zautomatyzować tworzenie wirtualnego koncentratora i vpngateway z pulpitu nawigacyjnego kontrolera urządzenia w oddziale.
  5. Po skojarzeniu centrum wirtualnego z lokacją generowany jest plik konfiguracyjny, aby użytkownik można było ręcznie pobrać. W tym miejscu pojawia się automatyzacja, która sprawia, że korzystanie z komputera jest bezproblemowe. Zamiast ręcznego pobierania i konfigurowania urządzenia w odgałęzieniu, można ustawić automatyzację i zapewnić minimalne wrażenia klikania w interfejsie użytkownika, a tym samym złagodzić typowe problemy z łącznością, takie jak współpasowanie klucza współdzielonego, parametr IPSec niezgodność, czytelność plików konfiguracyjnych itp.
  6. Po zakończeniu tego kroku w rozwiązaniu użytkownik będzie miał bezproblemowe połączenie lokacja z lokacją między urządzeniem w oddziale a koncentratorem wirtualnym. Można również skonfigurować dodatkowe połączenia między innymi koncentratorami. Każde połączenie jest tunelem aktywnym i aktywnym. Klient może zdecydować się na użycie innego usługodawcy bardzo sycowego dla każdego z łączy tunelu.
  7. Rozważ udostępnienie możliwości rozwiązywania problemów i monitorowania w interfejsie zarządzania CPE. Typowe scenariusze obejmują "Klient nie może uzyskać dostępu do zasobów platformy Azure z powodu problemu CPE", "Pokaż parametry IPsec po stronie CPE" itp.

## <a name="automation-details"></a><a name ="understand"></a>Szczegóły automatyzacji

###  <a name="access-control"></a><a name="access"></a>Kontrola dostępu

Klienci muszą mieć możliwość skonfigurowania odpowiedniej kontroli dostępu dla wirtualnej sieci WAN w interfejsie użytkownika urządzenia. Jest to zalecane przy użyciu jednostki usługi Azure. Dostęp oparty na jednostkach usługi zapewnia kontrolerowi urządzenia odpowiednie uwierzytelnianie do przekazywania informacji o gałęzi. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Chociaż ta funkcja znajduje się poza ofertą wirtualnej sieci WAN platformy Azure, poniżej znajduje się lista typowych kroków podjętych w celu skonfigurowania dostępu na platformie Azure, po czym odpowiednie szczegóły są wprowadzane do pulpitu nawigacyjnego zarządzania urządzeniami

* Utwórz aplikację usługi Azure Active Directory dla lokalnego kontrolera urządzeń.
* Pobierz identyfikator aplikacji i klucz uwierzytelniania
* Pobieranie identyfikatora dzierżawy
* Przypisywanie aplikacji do roli "Współautor"

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Przekazywanie informacji o urządzeniu w oddziale

Należy zaprojektować środowisko użytkownika, aby przekazać informacje o gałęzi (lokacji lokalnej) na platformę Azure. Za pomocą [interfejsów API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) dla sieci VPNSite można utworzyć informacje o lokacji w wirtualnej sieci WAN. Możesz podać wszystkie urządzenia SDWAN/VPN w oddziale lub odpowiednio wybrać dostosowania urządzeń.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Pobieranie konfiguracji urządzenia i łączność

Ten krok obejmuje pobieranie konfiguracji platformy Azure i konfigurowanie łączności z urządzenia w oddziale do wirtualnej sieci WAN platformy Azure. W tym kroku klient, który nie używa dostawcy ręcznie pobrać konfigurację platformy Azure i zastosować go do lokalnego urządzenia SDWAN/VPN. Jako dostawca należy zautomatyzować ten krok. Zobacz [pobrane interfejsy API REST,](https://docs.microsoft.com/rest/api/virtualwan/vpnsitesconfiguration/download) aby uzyskać dodatkowe informacje. Kontroler urządzeń można wywołać "GetVpnConfiguration" REST API, aby pobrać konfigurację platformy Azure.

**Uwagi dotyczące konfiguracji**

  * Jeśli sieci wirtualne platformy Azure są dołączone do centrum wirtualnego, będą wyświetlane jako ConnectedSubnets.
  * Łączność sieci VPN korzysta z konfiguracji opartej na trasach i obsługuje zarówno protokoły IKEv1, jak i IKEv2.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Plik konfiguracji urządzenia

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** ta sekcja zawiera szczegółowe informacje o urządzeniu skonfigurowanym jako lokacja połączona z wirtualną sieci WAN. Zawiera nazwę i publiczny adres IP urządzenia w oddziale.
* **vpnSiteConnections —** ta sekcja zawiera informacje dotyczące następujących kwestii:

    * **Przestrzeń adresowa** wirtualnej sieci wirtualnej.<br>Przykład:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Przestrzeń adresowa** sieci wirtualnych, które są połączone z koncentratorem.<br>Przykład:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **Adresy IP** bramy vpngateway koncentratora wirtualnego. Ponieważ każde połączenie z bramą vpngateway obejmuje dwa tunele w konfiguracji aktywne-aktywne, w pliku wymienione będą oba adresy IP. W tym przykładzie są to wartości „Instance0” i „Instance1” dla każdej lokacji.<br>Przykład:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Szczegóły konfiguracji połączenia vpngateway,** takie jak BGP, klucz wstępny itp. PSK jest kluczem wstępnym, który jest generowany automatycznie dla Ciebie. Zawsze możesz edytować połączenie na stronie Przegląd, aby użyć niestandardowego klucza wstępnego.
  
**Przykładowy plik konfiguracji urządzenia**

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

## <a name="connectivity-details"></a><a name="default"></a>Szczegóły dotyczące łączności

Lokalne urządzenie SDWAN/VPN lub konfiguracja SD-WAN musi być zgodna lub zawierać następujące algorytmy i parametry, które można określić w zasadach IPsec/IKE platformy Azure.

* Algorytm szyfrowania IKE
* Algorytm integralności IKE
* Grupa DH
* Algorytm szyfrowania IPsec
* Algorytm integralności IPsec
* Grupa PFS

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Zasady domyślne dla łączności protokołu IPsec

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Zasady niestandardowe dla łączności IPsec

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [Informacje o wirtualnej sieci WAN platformy Azure](virtual-wan-about.md) i często [zadawanych pytaniach dotyczących wirtualnej sieci WAN platformy Azure.](virtual-wan-faq.md)

Aby uzyskać dodatkowe informacje, wyślij <azurevirtualwan@microsoft.com>wiadomość e-mail na adres . W wierszu tematu wpisz nazwę firmy w nawiasie kwadratowym („[ ]”).
