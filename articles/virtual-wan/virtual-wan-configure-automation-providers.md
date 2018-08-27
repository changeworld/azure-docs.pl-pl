---
title: Konfigurowanie automatyzacji Azure wirtualnego WAN — wirtualne sieci WAN partnerów | Dokumentacja firmy Microsoft
description: Ten artykuł pomaga łączności zdefiniowanych przez oprogramowanie rozwiązań partnerów Konfigurowanie WAN wirtualnych usługi Azure automation.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: cherylmc
Customer intent: As a Virtual WAN software-defined connectivity provider, I want to set up a provisioning environment.
ms.openlocfilehash: bac728f286c90550107b27da76a070623577ed82
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918904"
---
# <a name="configure-virtual-wan-automation---for-virtual-wan-partners-preview"></a>Konfigurowanie wirtualnej sieci WAN automatyzacji — partnerzy wirtualne sieci WAN (wersja zapoznawcza)

Ten artykuł pomaga zrozumieć sposób konfigurowania środowiska automatyzacji, aby nawiązać połączenie i skonfigurować urządzenie gałęzi (urządzenia sieci VPN klienta w środowisku lokalnym lub SDWAN) dla usługi Azure wirtualnego WAN. Jeśli jesteś dostawcę, który zawiera urządzenia gałęzi, które może obsłużyć połączenia sieci VPN za pośrednictwem protokołu IPsec/IKEv2, ten artykuł jest dla Ciebie.

Rozwiązania w zakresie połączeń zdefiniowanych przez oprogramowanie zazwyczaj używane do zarządzania urządzeniami gałęzi kontrolera lub urządzenia, inicjowanie obsługi administracyjnej Centrum. Kontroler można użyć interfejsów API usługi Azure do zautomatyzowania połączenie WAN wirtualnych platformy Azure. Ten typ połączenia wymaga SDWAN lub sieci VPN urządzenia znajdujące się w środowisku lokalnym, do których ma zewnętrzny publiczny adres IP przypisany do niego.

##  <a name="access"></a>Kontrola dostępu

Klienci musi mieć możliwość skonfigurować kontroli dostępu właściwe dla wirtualnej sieci WAN w interfejsu użytkownika urządzenia. Jest to zalecane, korzystania z jednostki usługi platformy Azure. Dostęp do usługi oparte na jednostce zapewnia uwierzytelniania odpowiedniego kontrolera urządzenia do przekazania informacji o gałęzi. Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).

##  <a name="site"></a>Przekazywanie informacji o gałęzi

Zaprojektuj środowisko użytkownika do przekazania informacji o gałęzi (lokacji lokalnej) na platformie Azure. [Interfejsów API REST](https://docs.microsoft.com/rest/api/virtualwan/vpnsites) dla **VPNSite** może służyć do tworzenia informacji o lokacji w wirtualnej sieci WAN. Możesz zapewnić wszystkich gałęzi SDWAN/urządzeń sieci VPN lub wybierz dostosowania urządzenia zgodnie z potrzebami.

##  <a name="hub"></a>Centrum i usługi

Po przekazaniu urządzenia gałęzi na platformie Azure klient zazwyczaj musi upewnić się opcje region Centrum i/lub usług w witrynie Azure portal, która odwołuje się zestaw operacji tworzenia sieci wirtualnej koncentratora i punktu końcowego sieci VPN wewnątrz koncentratora. Brama sieci VPN jest skalowaną bramę, w których rozmiary odpowiednio na podstawie przepustowości i połączeń potrzeb.

## <a name="device"></a>Konfiguracja urządzenia

W tym kroku klient, który nie korzysta z dostawcy będzie ręcznie pobrać konfiguracji platformy Azure i zastosować je do swoich lokalnych SDWAN/urządzenia sieci VPN. Jako dostawca powinien zautomatyzować ten krok. Kontroler może wywołać **GetVpnConfiguration** interfejsu API REST można pobrać konfiguracji platformy Azure, która zazwyczaj będzie wyglądać podobnie do następującego pliku.

**Uwagi dotyczące konfiguracji**

  * W przypadku sieci wirtualnych platformy Azure są podłączone do koncentratora wirtualnego, będą one występować jako ConnectedSubnets.
  * Połączenia sieci VPN korzysta z konfiguracji opartej na trasach i IKEv2.

### <a name="understanding-the-device-configuration-file"></a>Opis pliku konfiguracji urządzenia

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

## <a name="default"></a>Domyślne zasady

### <a name="initiator"></a>Inicjator

W poniższych sekcjach wymieniono kombinacji zasad obsługiwanych, gdy platforma Azure to inicjatora dla tunelu.

**Faza 1**

* DH_GROUP_2 AES_256, SHA1,
* DH_GROUP_2 AES_256, SHA_256,
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* DH_GROUP_2 3DES, SHA1,
* DH_GROUP_2 3DES, SHA_256,

**Faza 2**

* GCM_AES_256 GCM_AES_256, PFS_NONE
* AES_256 SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE


### <a name="responder"></a>Obiekt odpowiadający

W poniższych sekcjach wymieniono kombinacji zasad obsługiwanych, gdy platforma Azure to obiekt odpowiadający w trybie dla tunelu.

**Faza 1**

* DH_GROUP_2 AES_256, SHA1,
* DH_GROUP_2 AES_256, SHA_256,
* AES_128, SHA1, DH_GROUP_2
* AES_128, SHA_256, DH_GROUP_2
* DH_GROUP_2 3DES, SHA1,
* DH_GROUP_2 3DES, SHA_256,

**Faza 2**

* GCM_AES_256 GCM_AES_256, PFS_NONE
* AES_256 SHA_1, PFS_NONE
* CBC_3DES, SHA_1, PFS_NONE
* AES_256, SHA_256, PFS_NONE
* AES_128, SHA_1, PFS_NONE
* CBC_3DES, SHA_256, PFS_NONE
* PFS_NONE CBC_DES, SHA_1, 
* PFS_1 AES_256, SHA_1,
* PFS_2 AES_256, SHA_1,
* PFS_14 AES_256, SHA_1,
* AES_128, SHA_1, PFS_1
* AES_128, SHA_1, PFS_2
* AES_128, SHA_1, PFS_14
* PFS_1 CBC_3DES, SHA_1,
* PFS_2 CBC_3DES, SHA_1,
* PFS_2 CBC_3DES, SHA_256,
* PFS_1 AES_256, SHA_256,
* PFS_2 AES_256, SHA_256,
* PFS_14 AES_256, SHA_256,
* PFS_24 AES_256, SHA_1,
* PFS_24 AES_256, SHA_256,
* AES_128, SHA_256, PFS_NONE
* AES_128, SHA_256, PFS_1
* AES_128, SHA_256, PFS_2
* AES_128, SHA_256, PFS_14
* PFS_14 CBC_3DES, SHA_1,

### <a name="does-everything-need-to-match-between-the-virtual-hub-vpngateway-policy-and-my-on-premises-sdwanvpn-device-or-sd-wan-configuration"></a>Czy wszystko, czego potrzebuje zgodność zasad bramy vpngateway koncentrator wirtualny i Moje lokalne SDWAN/urządzenia sieci VPN lub technologii SD-WAN konfiguracji?

Urządzenie SDWAN/VPN w środowisku lokalnym lub SD-WAN konfiguracji muszą być zgodne i zawiera następujące algorytmy i parametry, które można określić w zasadach protokołu IPsec/IKE platformy Azure. Okresy istnienia skojarzenia zabezpieczeń są tylko specyfikacjami lokalnymi i nie muszą być zgodne.

* Algorytm szyfrowania IKE
* Algorytm integralności IKE
* Grupa DH
* Algorytm szyfrowania IPsec
* Algorytm integralności IPsec
* Grupa PFS

## <a name="feedback"></a>Opinie dotyczące wersji zapoznawczej

Będziemy wdzięczni za przesłanie opinii. Wyślij wiadomość e-mail na adres <azurevirtualwan@microsoft.com>, jeśli chcesz zgłosić problem lub wyrazić swoją opinię (pozytywną lub negatywną) dotyczącą usługi Virtual WAN. W wierszu tematu wpisz nazwę firmy w nawiasie kwadratowym („[ ]”). Jeśli zgłaszasz problem, dołącz również swój identyfikator subskrypcji.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat wirtualnych sieci WAN, zobacz [o Azure wirtualne sieci WAN](virtual-wan-about.md) i [Azure wirtualne sieci WAN — często zadawane pytania](virtual-wan-faq.md).
