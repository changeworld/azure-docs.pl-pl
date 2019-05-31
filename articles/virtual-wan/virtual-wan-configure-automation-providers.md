---
title: Wirtualne sieci WAN partnerzy portalu Azure | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga partnerów Konfigurowanie WAN wirtualnych usługi Azure automation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: cherylmc
ms.openlocfilehash: f286c02e0eb6e801f62d4f2e16f1197a1e9d44ce
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304547"
---
# <a name="virtual-wan-partners"></a>Wirtualne sieci WAN partnerów

Ten artykuł pomaga zrozumieć sposób konfigurowania środowiska automatyzacji, aby nawiązać połączenie i skonfigurować urządzenie gałęzi (urządzenia sieci VPN klienta w środowisku lokalnym lub SDWAN CPE) dla usługi Azure wirtualnego WAN. Jeśli jesteś dostawcę, który zawiera urządzenia gałęzi, które może obsłużyć połączenia sieci VPN przez protokół IPsec/protokół IKEv2 lub protokołu IPsec/IKEv1, ten artykuł jest dla Ciebie.

Urządzenie gałęzi (klienta na lokalnym urządzeniu sieci VPN lub SDWAN CPE) zwykle korzysta z pulpitu nawigacyjnego urządzenia/kontrolera do zainicjowania obsługi administracyjnej. Administratorzy SD-WAN rozwiązania można często przed udostępnieniem urządzenie przed jego pobiera podłączony do sieci przy użyciu konsoli zarządzania. To urządzenie stanie sieci VPN pobiera swojej logiki płaszczyzna formantu za pomocą kontrolera. Urządzenia sieci VPN lub kontroler SD-WAN można użyć interfejsów API usługi Azure do zautomatyzowania połączenie WAN wirtualnych platformy Azure. Ten typ połączenia wymaga lokalnego urządzenia mają dostępny zewnętrznie publiczny adres IP przypisany do niego.

## <a name ="before"></a>Przed przystąpieniem do pracy automatyzacji

* Sprawdź, czy urządzenie obsługuje protokół IPsec IKEv1/IKEv2. Zobacz [domyślne zasady](#default).
* Zobacz [interfejsów API REST](https://docs.microsoft.com/rest/api/azure/) użyjesz do zautomatyzowania połączenie WAN wirtualnych platformy Azure.
* Przetestowanie środowiska portalu Azure wirtualnego WAN.
* Następnie zdecyduj, która część kroki łączności, które chcesz zautomatyzować. Jako minimum zalecamy Automatyzowanie:

  * Kontrola dostępu
  * Przekazywanie informacji o urządzeniu gałąź do usługi Azure wirtualnego WAN
  * Pobieranie konfiguracji platformy Azure i konfigurowania łączności z gałęzi urządzenia do usługi Azure wirtualnego WAN

* Zrozumienie oczekiwanego komfort w połączeniu z platformy Azure wirtualnego WAN.

  1. Wirtualne sieci WAN użytkownika rozpocznie się proces, tworząc zasobu wirtualnej sieci WAN.
  2. Użytkownik zostanie skonfigurowany dostęp grupy do zasobów na podstawie jednostki usługi systemu lokalnego (swojej gałęzi kontrolera lub oprogramowania aprowizacji urządzenia sieci VPN) do zapisu informacji o gałęzi do platformy Azure wirtualnego WAN.
  3. Użytkownik może podjąć decyzję w tej chwili, aby zalogować się do interfejsu użytkownika i skonfigurować poświadczenia nazwy głównej usługi. To znaczy po zakończeniu kontroler powinno być możliwe do przekazania informacji o gałęzi za pomocą usługi automation, które należy podać. Ręczne odpowiednik to po stronie platformy Azure jest tworzenie witryny.
  4. Gdy informacji o lokacji (urządzenie gałęzi) będzie dostępna na platformie Azure, użytkownik zostanie skojarzony lokacji z koncentratorem. Koncentrator wirtualny jest zarządzany przez firmę Microsoft sieci wirtualnej. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Koncentrator to podstawowy element sieci w danym regionie. Może istnieć tylko jedno Centrum na region platformy Azure i punktu końcowego sieci vpn (bramy vpngateway) wewnątrz niego jest tworzony w trakcie tego procesu. Brama sieci VPN jest skalowaną bramę, w których rozmiary odpowiednio na podstawie przepustowości i połączeń potrzeb. Można zautomatyzować koncentrator wirtualny i tworzenia bramy vpngateway ze swojego pulpitu nawigacyjnego z kontrolerem urządzenia gałęzi.
  5. Gdy koncentrator wirtualny jest skojarzona z witryną, generowany jest plik konfiguracji dla użytkownika ręcznie pobrać. Jest to, gdy jest dostępna w automatyzacji, a sprawia, że interfejs użytkownika jest bezproblemowe. Zamiast konieczności ręcznie Pobierz i skonfiguruj urządzenie gałęzi użytkownika można ustawić automatyzacji i zapewnia minimalne środowisko za pomocą kliknięć w interfejsie użytkownika, a tym samym złagodzenia łączności typowe problemy, takie jak udostępnione Niezgodność kluczy parametrów protokołu IPSec Niezgodność konfiguracji pliku czytelność itp.
  6. Na końcu tego kroku w rozwiązaniu użytkownik będzie miał optymalne połączenie lokacja lokacja między urządzeniem gałęzi i koncentrator wirtualny. Można też skonfigurować dodatkowe połączenia między innymi centrami. Każde połączenie jest tunel typu aktywne aktywne. Klient może wybrać innego usługodawcy internetowego dla każdego łącza dla tunelu.

## <a name ="understand"></a>Zrozumienie informacji o automatyzacji


###  <a name="access"></a>Kontrola dostępu

Klienci musi mieć możliwość skonfigurować kontroli dostępu właściwe dla wirtualnej sieci WAN w interfejsu użytkownika urządzenia. Jest to zalecane, korzystania z jednostki usługi platformy Azure. Dostęp do usługi oparte na jednostce zapewnia uwierzytelniania odpowiedniego kontrolera urządzenia do przekazania informacji o gałęzi. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Gdy ta funkcja jest poza oferty Azure wirtualnego WAN, listę poniżej typowe etapy, zarządzanie dostępem na platformie Azure, po upływie którego odpowiednie szczegóły są klatkach, w których do pulpitu nawigacyjnego zarządzania urządzeniami

* Tworzenie aplikacji usługi Azure Active Directory dla środowiska lokalnego kontrolera urządzenia.
* Pobierz aplikację Identyfikatora i klucza uwierzytelniania
* Pobieranie identyfikatora dzierżawy
* Przypisywanie aplikacji do roli "Współautor"

###  <a name="branch"></a>Przekazywanie informacji o urządzeniu gałęzi

Zaprojektuj środowisko użytkownika do przekazania informacji o gałęzi (lokacji lokalnej) na platformie Azure. [Interfejsów API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) dla VPNSite może służyć do tworzenia informacji o lokacji w wirtualnej sieci WAN. Można zapewnić wszystkich gałęzi SDWAN/urządzeń sieci VPN, lub wybrać dostosowania urządzenia zgodnie z potrzebami.


### <a name="device"></a>Pobieranie konfiguracji urządzenia i łączność

Ten krok polega na pobraniu konfiguracji platformy Azure i konfigurowania łączności z urządzenia gałąź do usługi Azure wirtualnego WAN. W tym kroku klient, który nie korzysta z dostawcy będzie ręcznie pobrać konfiguracji platformy Azure i zastosować je do swoich lokalnych SDWAN/urządzenia sieci VPN. Jako dostawca powinien zautomatyzować ten krok. Z kontrolerem urządzenia można wywołać interfejsu API REST "GetVpnConfiguration", aby pobrać konfiguracji platformy Azure, która zazwyczaj będzie wyglądać podobnie do następującego pliku.

**Uwagi dotyczące konfiguracji**

  * W przypadku sieci wirtualnych platformy Azure są podłączone do koncentratora wirtualnego, będą one występować jako ConnectedSubnets.
  * Połączenia sieci VPN korzysta z konfiguracji opartej na trasach i protokołu IKEv2/IKEv1.

#### <a name="understanding-the-device-configuration-file"></a>Opis pliku konfiguracji urządzenia

Plik konfiguracji urządzenia zawiera ustawienia używane podczas konfigurowania lokalnego urządzenia sieci VPN. Podczas przeglądania tego pliku należy zwrócić uwagę na następujące informacje:

* **vpnSiteConfiguration —** ta sekcja zawiera szczegółowe informacje o urządzeniu skonfigurowanym jako lokacja połączona z wirtualną sieci WAN. Zawiera nazwę i publiczny adres IP urządzenia w oddziale.
* **vpnSiteConnections —** ta sekcja zawiera informacje dotyczące następujących kwestii:

    * **Przestrzeń adresowa** z wirtualnego koncentratory sieci wirtualnej.<br>Przykład:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Przestrzeń adresowa** sieciach wirtualnych, które są podłączone do koncentratora.<br>Przykład:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.30.0.0/16"]
         ```
    * **Adresy IP** bramy vpngateway koncentratora wirtualnego. Ponieważ każde połączenie z bramą vpngateway obejmuje dwa tunele w konfiguracji aktywne-aktywne, w pliku wymienione będą oba adresy IP. W tym przykładzie są to wartości „Instance0” i „Instance1” dla każdej lokacji.<br>Przykład:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Szczegóły konfiguracji połączenia bramy vpngateway**, takie jak protokół BGP, klucz wstępny i tym podobne. PSK to klucz wstępny, który jest generowany automatycznie. Zawsze możesz edytować połączenie na stronie Przegląd, aby użyć niestandardowego klucza wstępnego.
  
#### <a name="example-device-configuration-file"></a>Przykładowy plik konfiguracji urządzenia

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

## <a name="default"></a>Domyślne zasady dla połączenia protokołu IPsec

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-include.md)]

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Czy wszystko, czego potrzebuje zgodność zasad bramy vpngateway koncentrator wirtualny i Moje lokalne SDWAN/urządzenia sieci VPN lub technologii SD-WAN konfiguracji?

Urządzenie SDWAN/VPN w środowisku lokalnym lub SD-WAN konfiguracji muszą być zgodne i zawiera następujące algorytmy i parametry, które można określić w zasadach protokołu IPsec/IKE platformy Azure.

* Algorytm szyfrowania IKE
* Algorytm integralności IKE
* Grupa DH
* Algorytm szyfrowania IPsec
* Algorytm integralności IPsec
* Grupa PFS

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wirtualnych sieci WAN, zobacz [o Azure wirtualne sieci WAN](virtual-wan-about.md) i [Azure wirtualne sieci WAN — często zadawane pytania](virtual-wan-faq.md).

Informacje dodatkowe, Wyślij wiadomość e-mail do <azurevirtualwan@microsoft.com>. W wierszu tematu wpisz nazwę firmy w nawiasie kwadratowym („[ ]”).