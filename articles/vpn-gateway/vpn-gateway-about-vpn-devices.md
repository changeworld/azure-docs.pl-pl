---
title: Informacje na temat urządzeń sieci VPN | Microsoft Docs
description: W tym artykule omówiono urządzenia sieci VPN i parametry protokołu IPsec dla połączeń obejmujących wiele lokalizacji usługi S2S VPN Gateway. Zamieszczono linki do przykładów i instrukcji konfigurowania.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: yushwang
ms.openlocfilehash: 30558300036974a765765fe0eb0181e2a8dc73ca
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65508361"
---
# <a name="about-vpn-devices-and-ipsecike-parameters-for-site-to-site-vpn-gateway-connections"></a>Informacje na temat urządzeń sieci VPN i parametrów protokołu IPsec/IKE dla połączeń bramy VPN typu lokacja-lokacja

Urządzenie sieci VPN jest niezbędne do skonfigurowania połączenia sieci VPN typu lokacja-lokacja obejmującego wiele lokalizacji (S2S) przy użyciu bramy sieci VPN. Połączeń typu lokacja-lokacja można używać do tworzenia rozwiązań hybrydowych oraz bezpiecznych połączeń między sieciami lokalnymi i wirtualnymi. Ten artykuł zawiera listę zweryfikowanych urządzeń sieci VPN oraz listę parametrów protokołu IPsec/IKE dla bram sieci VPN.

> [!IMPORTANT]
> Jeśli występują problemy z połączeniem między lokalnymi urządzeniami sieci VPN i bramami sieci VPN, zapoznaj się z sekcją [Znane problemy dotyczące zgodności urządzeń](#known).
>

### <a name="items-to-note-when-viewing-the-tables"></a>Kwestie, które należy wziąć pod uwagę podczas przeglądania tabeli:

* Nastąpiła zmiana terminologii w zakresie bram Azure VPN Gateway. Zmianie uległy tylko nazwy. Nie nastąpiła żadna zmiana funkcjonalności.
  * Routing statyczny = PolicyBased
  * Routing dynamiczny = RouteBased
* Specyfikacje dotyczące bramy VPN typu HighPerformance i bramy VPN typu RouteBased są takie same, o ile nie określono inaczej. Na przykład zweryfikowane urządzenia sieci VPN, które są zgodne z bramami sieci VPN typu RouteBased, są również zgodne z bramą sieci VPN typu HighPerformance.

## <a name="devicetable"></a>Zweryfikowane urządzenia sieci VPN i przewodniki konfiguracji urządzenia

> [!NOTE]
> Podczas konfigurowania połączenia typu lokacja-lokacja wymagane jest użycie publicznego adresu IPv4 dla urządzenia sieci VPN.
>

We współpracy z dostawcami urządzeń zweryfikowaliśmy szereg standardowych urządzeń sieci VPN. Wszystkie urządzenia z rodzin znajdujących się na poniższej liście powinny współpracować z bramami sieci VPN. Zobacz [Informacje na temat ustawień usługi VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md#vpntype), aby poznać zasady użycia typu sieci VPN (PolicyBased lub RouteBased) dla rozwiązania usługi VPN Gateway, które chcesz skonfigurować.

Aby łatwiej skonfigurować urządzenie sieci VPN, zapoznaj się z linkami odpowiadającymi ich poszczególnym rodzinom. Linki do instrukcji konfiguracji zostały podane na zasadzie największej staranności. W celu uzyskania pomocy technicznej w zakresie urządzenia sieci VPN należy skontaktować się z jego producentem.

|**Dostawca**          |**Rodzina urządzeń**     |**Minimalna wersja systemu operacyjnego** |**Instrukcje dotyczące konfiguracji typu PolicyBased** |**Instrukcje dotyczące konfiguracji typu RouteBased** |
| ---                | ---                  | ---                   | ---            | ---           |
| A10 Networks, Inc. |Thunder CFW           |ACOS 4.1.1             |Niezgodne  |[Przewodnik po konfiguracji](https://www.a10networks.com/resources/deployment-guides/a10-thunder-cfw-ipsec-vpn-interoperability-azure-vpn-gateways)|
| Allied Telesis     |Routery sieci VPN z serii AR |AR-Series 5.4.7+               |Dostępne wkrótce     |[Przewodnik po konfiguracji](https://www.alliedtelesis.com/documents/how-to/configure/site-to-site-vpn-between-azure-and-ar-series-router)|
| Barracuda Networks, Inc. |Barracuda NextGen Firewall z serii F |PolicyBased: 5.4.3<br>RouteBased: 6.2.0 |[Przewodnik po konfiguracji](https://techlib.barracuda.com/NGF/AzurePolicyBasedVPNGW) |[Przewodnik po konfiguracji](https://techlib.barracuda.com/NGF/AzureRouteBasedVPNGW) |
| Barracuda Networks, Inc. |Barracuda NextGen Firewall z serii X |Barracuda Firewall 6.5 |[Przewodnik po konfiguracji](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway) |Niezgodne |
| Check Point |Security Gateway |R80.10 |[Przewodnik po konfiguracji](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |[Przewodnik po konfiguracji](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275) |
| Cisco              |ASA       |8.3<br>8.4+ (IKEv2*) |Obsługiwane |[Przewodnik po konfiguracji*](https://www.cisco.com/c/en/us/support/docs/security/adaptive-security-appliance-asa-software/214109-configure-asa-ipsec-vti-connection-to-az.html) |
| Cisco |ASR |PolicyBased: IOS 15.1<br>RouteBased: IOS 15.2 |Obsługiwane |Obsługiwane |
| Cisco |ISR |PolicyBased: IOS 15.0<br>RouteBased*: IOS 15.1 |Obsługiwane |Obsługiwane |
| Cisco |Meraki |ND |Niezgodne |Niezgodne |
| Citrix |NetScaler MPX, SDX, VPX |10.1 lub nowsze |[Przewodnik po konfiguracji](https://docs.citrix.com/en-us/netscaler/11-1/system/cloudbridge-connector-introduction/cloudbridge-connector-azure.html) |Niezgodne |
| F5 |Seria BIG-IP |12.0 |[Przewodnik po konfiguracji](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip) |[Przewodnik po konfiguracji](https://devcentral.f5.com/articles/big-ip-to-azure-dynamic-ipsec-tunneling) |
| Fortinet |FortiGate |FortiOS 5.6 |  |[Przewodnik po konfiguracji](https://cookbook.fortinet.com/ipsec-vpn-microsoft-azure-56/) |
| Internet Initiative Japan (IIJ) |Seria SEIL |SEIL/X 4.60<br>SEIL/B1 4.60<br>SEIL/x86 3.20 |[Przewodnik po konfiguracji](https://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf) |Niezgodne |
| Juniper |SRX |PolicyBased: JunOS 10.2<br>Routebased: JunOS 11.4 |Obsługiwane |[Skrypt konfiguracji](vpn-gateway-download-vpndevicescript.md) |
| Juniper |Seria J |PolicyBased: JunOS 10.4r9<br>RouteBased: JunOS 11.4 |Obsługiwane |[Skrypt konfiguracji](vpn-gateway-download-vpndevicescript.md) |
| Juniper |ISG |ScreenOS 6.3 |Obsługiwane |[Skrypt konfiguracji](vpn-gateway-download-vpndevicescript.md) |
| Juniper |SSG |ScreenOS 6.2 |Obsługiwane |[Skrypt konfiguracji](vpn-gateway-download-vpndevicescript.md) |
| Juniper |MX |JunOS 12.x|Obsługiwane |[Skrypt konfiguracji](vpn-gateway-download-vpndevicescript.md) |
| Microsoft |Routing and Remote Access Service |Windows Server 2012 |Niezgodne |Obsługiwane |
| Open Systems AG |Mission Control Security Gateway |ND |[Przewodnik po konfiguracji](https://www.open.ch/_pdf/Azure/AzureVPNSetup_Installation_Guide.pdf) |Niezgodne |
| Palo Alto Networks |Wszystkie urządzenia z systemem PAN-OS |PAN-OS<br>PolicyBased: 6.1.5 lub nowszy<br>RouteBased: 7.1.4 |[Przewodnik po konfiguracji](https://live.paloaltonetworks.com/t5/Configuration-Articles/How-to-Configure-VPN-Tunnel-Between-a-Palo-Alto-Networks/ta-p/59065) |[Przewodnik po konfiguracji](https://knowledgebase.paloaltonetworks.com/KCSArticleDetail?id=kA10g000000Cm6WCAS) |
| ShareTech | UTM następnej generacji (seria NU) | 9.0.1.3 | Niezgodne | [Przewodnik po konfiguracji](http://www.sharetech.com.tw/images/file/Solution/NU_UTM/S2S_VPN_with_Azure_Route_Based_en.pdf) |
| SonicWall |Seria TZ, seria NSA<br>Seria SuperMassive<br>Seria E-Class NSA |SonicOS 5.8.x<br>SonicOS 5.9.x<br>SonicOS 6.x |Niezgodne |[Przewodnik po konfiguracji](https://www.sonicwall.com/support/knowledge-base/170505320011694) |
| Sophos | XG Next Gen Firewall | XG v17 | | [Przewodnik po konfiguracji](https://community.sophos.com/kb/127546)<br><br>[Przewodnik po konfiguracji — wiele sygnatury dostępu współdzielonego](https://community.sophos.com/kb/en-us/133154) |
| Ubiquiti | EdgeRouter | EdgeOS v1.10 |  | [Protokół BGP za pośrednictwem IKEv2 i IPsec](https://help.ubnt.com/hc/en-us/articles/115012374708)<br><br>[VTI za pośrednictwem IKEv2 i IPsec](https://help.ubnt.com/hc/en-us/articles/115012305347)
| WatchGuard |Wszyscy |Fireware XTM<br> PolicyBased: v11.11.x<br>RouteBased: v11.12.x |[Przewodnik po konfiguracji](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA2F00000000LI7KAM&lang=en_US) |[Przewodnik po konfiguracji](http://watchguardsupport.force.com/publicKB?type=KBArticle&SFDCID=kA22A000000XZogSAG&lang=en_US)|

> [!NOTE]
>
> (*) Produkt Cisco ASA w wersji 8.4 i nowszych ma obsługę protokołu IKEv2 i może łączyć się z bramą Azure VPN Gateway za pomocą zasad IPsec/IKE z opcją „UsePolicyBasedTrafficSelectors”. Zapoznaj się z tym [artykułem z instrukcjami](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> (\*\*) Routery z serii ISR 7200 obsługują tylko sieci VPN oparte na zasadach.

## <a name="configscripts"></a>Pobieranie skryptów konfiguracji urządzenia sieci VPN na platformie Azure

W przypadku niektórych urządzeń, możesz pobrać skrypty konfiguracji bezpośrednio na platformie Azure. Aby uzyskać więcej informacji i instrukcje pobierania, zobacz [skryptów konfiguracji urządzenia VPN Pobierz](vpn-gateway-download-vpndevicescript.md).

### <a name="devices-with-available-configuration-scripts"></a>Urządzeń za pomocą skryptów konfiguracyjnych dostępnych

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="additionaldevices"></a>Niezweryfikowane urządzenia sieci VPN

Jeśli urządzenie nie zostało ujęte w tabeli zweryfikowanych urządzeń sieci VPN, być może będzie działało w ramach połączenia typu lokacja-lokacja. Dodatkową pomoc oraz instrukcje dotyczące konfiguracji można uzyskać, kontaktując się z producentem urządzenia.

## <a name="editing"></a>Edytowanie przykładów konfiguracji urządzenia

Po pobraniu dostarczonej przykładowej konfiguracji urządzenia sieci VPN należy zastąpić niektóre z wartości w celu odzwierciedlenia ustawień własnego środowiska.

### <a name="to-edit-a-sample"></a>Aby edytować przykładową konfigurację:

1. Otwórz przykładową konfigurację za pomocą Notatnika.
2. Wyszukaj i zamień wszystkie ciągi <*text*> z wartościami, które odnoszą się do używanego środowiska. Nie zapomnij o nawiasach < i >. Podczas określenia nazwy należy zwrócić uwagę na to, aby była ona unikatowa. Jeśli polecenie nie działa, zapoznaj się z dokumentacją producenta urządzenia.

| **Przykładowy tekst** | **Zmień na** |
| --- | --- |
| &lt;RP_OnPremisesNetwork&gt; |Nazwę tego obiektu definiuje użytkownik. Przykład: myOnPremisesNetwork |
| &lt;RP_AzureNetwork&gt; |Nazwę tego obiektu definiuje użytkownik. Przykład: myAzureNetwork |
| &lt;RP_AccessList&gt; |Nazwę tego obiektu definiuje użytkownik. Przykład: myAzureAccessList |
| &lt;RP_IPSecTransformSet&gt; |Nazwę tego obiektu definiuje użytkownik. Przykład: myIPSecTransformSet |
| &lt;RP_IPSecCryptoMap&gt; |Nazwę tego obiektu definiuje użytkownik. Przykład: myIPSecCryptoMap |
| &lt;SP_AzureNetworkIpRange&gt; |Określ zakres. Przykład: 192.168.0.0 |
| &lt;SP_AzureNetworkSubnetMask&gt; |Określ maskę podsieci. Przykład: 255.255.0.0 |
| &lt;SP_OnPremisesNetworkIpRange&gt; |Określ zakres lokalny. Przykład: 10.2.1.0 |
| &lt;SP_OnPremisesNetworkSubnetMask&gt; |Określ lokalną maskę podsieci. Przykład: 255.255.255.0 |
| &lt;SP_AzureGatewayIpAddress&gt; |Te informacje dotyczące konkretnej sieci wirtualnej znajdują się w portalu zarządzania usługą, w obszarze **Adres IP bramy**. |
| &lt;SP_PresharedKey&gt; |Te informacje dotyczące konkretnej sieci wirtualnej znajdują się w portalu zarządzania usługą, w obszarze Zarządzaj kluczem. |

## <a name="ipsec"></a>Parametry protokołu IPsec/IKE

> [!IMPORTANT]
> 1. Tabele poniżej zawierają kombinacje algorytmów i parametrów używanych przez bramy Azure VPN Gateway w konfiguracji domyślnej. W przypadku bram VPN Gateway opartych na trasach, utworzonych za pomocą modelu wdrażania z użyciem zarządzania zasobami platformy Azure, można określić niestandardowe zasady dla każdego pojedynczego połączenia. Zapoznaj się z artykułem [Configure IPsec/IKE policy](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Konfigurowanie zasad IPsec/IKE) w celu uzyskania szczegółowych instrukcji.
>
> 2. Dodatkowo musisz określić ograniczenie wartości TCP **MSS** na **1350**. Jeśli natomiast Twoje urządzenia sieci VPN nie obsługują ograniczania wartości MSS, możesz zamiast tego ustawić wartość **MTU** w interfejsie tunelu na **1400**.
>

W poniższych tabelach:

* SA — skojarzenia zabezpieczeń
* Protokół IKE — faza 1 jest również określany jako „Tryb główny”
* Protokół IKE — faza 2 jest również określany jako „Tryb szybki”

### <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokołu IKE — faza 1 (tryb główny)

| **Property**          |**PolicyBased**    | **RouteBased**    |
| ---                   | ---               | ---               |
| Wersja IKE           |IKEv1              |IKEv2              |
| Grupa Diffie’ego-Hellmana  |Grupa 2 (1024 bity) |Grupa 2 (1024 bity) |
| Metoda uwierzytelniania |Klucz wstępny     |Klucz wstępny     |
| Szyfrowanie i algorytmy wyznaczania wartości skrótu |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |1. AES256, SHA1<br>2. AES256, SHA256<br>3. AES128, SHA1<br>4. AES128, SHA256<br>5. 3DES, SHA1<br>6. 3DES, SHA256 |
| Okres istnienia skojarzeń zabezpieczeń           |28 800 sekund     |28 800 sekund     |

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokołu IKE — faza 2 (tryb szybki)

| **Property**                  |**PolicyBased**| **RouteBased**                              |
| ---                           | ---           | ---                                         |
| Wersja IKE                   |IKEv1          |IKEv2                                        |
| Szyfrowanie i algorytmy wyznaczania wartości skrótu |1. AES256, SHA256<br>2. AES256, SHA1<br>3. AES128, SHA1<br>4. 3DES, SHA1 |[Oferty skojarzeń zabezpieczeń trybu szybkiego RouteBased](#RouteBasedOffers) |
| Okres istnienia skojarzeń zabezpieczeń (czas)            |3600 sekund  |27 000 sekund                                |
| Okres istnienia skojarzeń zabezpieczeń (bajty)           |102 400 000 KB | -                                           |
| Doskonałe utajnienie przekazywania (PFS) |Nie             |[Oferty skojarzeń zabezpieczeń trybu szybkiego RouteBased](#RouteBasedOffers) |
| Wykrywanie nieaktywnych elementów równorzędnych (DPD, Dead Peer Detection)     |Nieobsługiwane  |Obsługiwane                                    |


### <a name ="RouteBasedOffers"></a>Oferty skojarzeń zabezpieczeń protokołu IPsec połączenia VPN typu RouteBased (skojarzenia zabezpieczeń trybu szybkiego protokołu IKE)

W poniższej tabeli znajduje się lista ofert skojarzeń zabezpieczeń protokołu IPsec (tryb szybki protokołu IKE). Oferty są wymienione w kolejności preferencji, w jakiej oferta jest przedstawiona lub zaakceptowana.

#### <a name="azure-gateway-as-initiator"></a>Brama Azure jako inicjator

|-  |**Szyfrowanie**|**Uwierzytelnianie**|**Grupa PFS**|
|---| ---          |---               |---          |
| 1 |GCM AES256    |GCM (AES256)      |Brak         |
| 2 |AES256        |SHA1              |Brak         |
| 3 |3DES          |SHA1              |Brak         |
| 4 |AES256        |SHA256            |Brak         |
| 5 |AES128        |SHA1              |Brak         |
| 6 |3DES          |SHA256            |Brak         |

#### <a name="azure-gateway-as-responder"></a>Brama Azure jako obiekt odpowiadający

|-  |**Szyfrowanie**|**Uwierzytelnianie**|**Grupa PFS**|
|---| ---          | ---              |---          |
| 1 |GCM AES256    |GCM (AES256)      |Brak         |
| 2 |AES256        |SHA1              |Brak         |
| 3 |3DES          |SHA1              |Brak         |
| 4 |AES256        |SHA256            |Brak         |
| 5 |AES128        |SHA1              |Brak         |
| 6 |3DES          |SHA256            |Brak         |
| 7 |DES           |SHA1              |Brak         |
| 8 |AES256        |SHA1              |1            |
| 9 |AES256        |SHA1              |2            |
| 10|AES256        |SHA1              |14           |
| 11|AES128        |SHA1              |1            |
| 12|AES128        |SHA1              |2            |
| 13|AES128        |SHA1              |14           |
| 14|3DES          |SHA1              |1            |
| 15|3DES          |SHA1              |2            |
| 16|3DES          |SHA256            |2            |
| 17|AES256        |SHA256            |1            |
| 18|AES256        |SHA256            |2            |
| 19|AES256        |SHA256            |14           |
| 20|AES256        |SHA1              |24           |
| 21|AES256        |SHA256            |24           |
| 22|AES128        |SHA256            |Brak         |
| 23|AES128        |SHA256            |1            |
| 24|AES128        |SHA256            |2            |
| 25|AES128        |SHA256            |14           |
| 26|3DES          |SHA1              |14           |

* Wartość NULL szyfrowania ESP protokołu IPsec możesz określić z bramami sieci VPN typu RouteBased i HighPerformance. Szyfrowanie oparte na wartości null nie zapewnia ochrony danych w trakcie ich przesyłania i powinno być używane wyłącznie w przypadku, gdy wymagana jest maksymalna przepływność i minimalne opóźnienia. Klienci mogą skorzystać z tej możliwości w kontekście scenariuszy komunikacji między sieciami wirtualnymi lub w przypadku, gdy szyfrowanie jest stosowane w innym obszarze rozwiązania.
* Chcąc korzystać z łączności przez Internet obejmującej wiele lokalizacji, należy użyć ustawień domyślnych usługi Azure VPN Gateway z szyfrowaniem i algorytmami skrótu wymienionymi w tabelach powyżej w celu zapewnienia bezpieczeństwa komunikacji o krytycznym znaczeniu.

## <a name="known"></a>Znane problemy dotyczące zgodności urządzeń

> [!IMPORTANT]
> Opisano tu znane problemy ze zgodnością między urządzeniami sieci VPN innych firm i bramami sieci VPN platformy Azure. Zespołu platformy Azure aktywnie współpracuje z dostawcami w celu rozwiązania opisanych poniżej problemów. Po rozwiązaniu problemów ta strona zostanie zaktualizowana o najbardziej aktualne informacje. Sprawdzaj ją okresowo.
>
>

### <a name="feb-16-2017"></a>16 lutego 2017 r.

**Urządzenia Palo Alto Networks z wersji wcześniejszej niż 7.1.4** dla usługi Azure VPN oparta na trasach: Jeśli używasz urządzenia sieci VPN z Palo Alto Networks z PAN-OS w wersji wcześniejszej niż 7.1.4 i występują problemy z połączeniem bramy sieci VPN opartej na trasach platformy Azure, wykonaj następujące czynności:

1. Sprawdź wersję oprogramowania układowego urządzenia Palo Alto Networks. Jeśli system PAN-OS jest w wersji wcześniejszej niż 7.1.4, przeprowadź uaktualnienie do wersji 7.1.4.
2. Na urządzeniu Palo Alto Networks zmień okres istnienia Phase 2 SA (lub Quick Mode SA) na 28 800 sekund (8 godzin) podczas nawiązywania połączenia z bramą VPN platformy Azure.
3. Jeśli nadal masz problemy z łącznością, otwórz żądanie obsługi w witrynie Azure Portal.
