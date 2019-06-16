---
title: Przykładowa konfiguracja urządzenia Cisco ASA nawiązywania połączenia z bramami Azure VPN Gateway | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera Przykładowa konfiguracja urządzenia Cisco ASA nawiązywania połączenia z bramami Azure VPN Gateway.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: e575fac61a1c5d9351391d39d200b87e34ff26cd
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648761"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Przykładowa konfiguracja: urządzenie ASA Cisco (IKEv2/bez protokołu BGP)
Ten artykuł zawiera przykładowe konfiguracje urządzeń łączących Cisco adaptacyjne zabezpieczeń urządzenia (ASA) do bram sieci VPN platformy Azure. Przykład dotyczy urządzenia Cisco ASA, które działają IKEv2 bez protokołu BGP (Border Gateway). 

## <a name="device-at-a-glance"></a>Urządzenia, w skrócie

|                        |                                   |
| ---                    | ---                               |
| Dostawca urządzenia          | Cisco                             |
| Model urządzenia           | ASA                               |
| Wersja docelowa         | 8.4 i nowszych                     |
| Przetestowane modelu           | ASA 5505                          |
| Przetestowanej wersji         | 9.2                               |
| Wersja IKE            | IKEv2                             |
| BGP                    | Nie                                |
| Typ bramy sieci VPN Azure | Brama sieci VPN opartej na trasach           |
|                        |                                   |

> [!NOTE]
> Przykładowa konfiguracja nawiązanie połączenia z urządzenia Cisco ASA Azure **oparte na trasach** bramy sieci VPN. Połączenie korzysta z zasad protokołu IPsec/IKE niestandardowych **UsePolicyBasedTrafficSelectors** opcji, zgodnie z opisem w [w tym artykule](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Przykładowy skrypt wymaga używające urządzeń ASA **IKEv2** zasady oparte na liście dostępu do konfiguracji nie VTI na podstawie. Zapoznaj się z specyfikacji dostawcy urządzeń sieci VPN, tak aby sprawdzić, czy protokół IKEv2, zasady są obsługiwane na urządzeniach sieci VPN w środowisku lokalnym.


## <a name="vpn-device-requirements"></a>Wymagania dotyczące urządzeń sieci VPN
Bramy sieci VPN platformy Azure Użyj standardowych mechanizmów protokołu IPsec/IKE, aby ustanowić tuneli sieci VPN typu lokacja-lokacja (S2S). Szczegóły parametrów protokołu IPsec/IKE i domyślne algorytmy kryptograficzne dla bram Azure VPN Gateway, zobacz [urządzenia sieci VPN o](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Opcjonalnie można określić dokładne kombinacji algorytmy kryptograficzne i siły klucza dla określonego połączenia, zgodnie z opisem w [informacje o wymaganiach kryptograficznych](vpn-gateway-about-compliance-crypto.md). Jeśli określisz dokładnie kombinacji algorytmów i sił klucza, należy użyć odpowiedniej specyfikacje na urządzeniach sieci VPN.

## <a name="single-vpn-tunnel"></a>Jednego tunelu sieci VPN
Ta konfiguracja składa się z jednego tunelu sieci VPN S2S między bramą sieci VPN platformy Azure i lokalnym urządzeniem sieci VPN. Opcjonalnie można skonfigurować protokołu BGP przez tunel sieci VPN.

![Jednego tunelu sieci VPN S2S](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Aby uzyskać instrukcje krok po kroku kompilacji konfiguracji platformy Azure, zobacz [konfiguracji tunelu VPN pojedynczy](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Sieć wirtualną i dotyczących bramy sieci VPN
Ta sekcja zawiera listę parametrów dla przykładu.

| **Parametr**                | **Wartość**                    |
| ---                          | ---                          |
| Prefiksy adresów sieci wirtualnej        | 10.11.0.0/16<br>10.12.0.0/16 |
| Adres IP bramy sieci VPN platformy Azure         | Azure_Gateway_Public_IP      |
| Prefiksy adresów lokalnych | 10.51.0.0/16<br>10.52.0.0/16 |
| Adres IP urządzenia sieci VPN w środowisku lokalnym    | OnPrem_Device_Public_IP     |
| * Virtual network numer ASN protokołu BGP                | 65010                        |
| * Azure IP elementu równorzędnego protokołu BGP           | 10.12.255.30                 |
| * Numer ASN protokołu BGP w środowisku lokalnym         | 65050                        |
| * Adresu IP elementu równorzędnego protokołu BGP w środowisku lokalnym     | 10.52.255.254                |
|                              |                              |

\* Opcjonalny parametr dla protokołu BGP tylko.

### <a name="ipsecike-policy-and-parameters"></a>Zasady protokołu IPsec/IKE i parametrami
W poniższej tabeli wymieniono algorytmy protokołu IPsec/IKE i parametrów używanych w przykładzie. Zapoznaj się z specyfikację urządzenia sieci VPN można sprawdzić algorytmy, które są obsługiwane dla wersji oprogramowania układowego i modeli urządzeń sieci VPN.

| **IPsec/IKEv2**  | **Wartość**                            |
| ---              | ---                                  |
| Szyfrowanie IKEv2 | AES256                               |
| Integralność IKEv2  | SHA384                               |
| Grupa DH         | DHGroup24                            |
| * Szyfrowanie IPsec | AES256                               |
| * Integralność IPsec  | SHA1                                 |
| Grupa PFS        | PFS24                                |
| Okres istnienia skojarzeń zabezpieczeń QM   | 7200 sekund                         |
| Selektor ruchu | UsePolicyBasedTrafficSelectors $True |
| Klucz wstępny   | PreSharedKey                         |
|                  |                                      |

\* Na niektórych urządzeniach integralność IPsec musi być wartością null, gdy algorytm szyfrowania IPsec to AES-GCM.

### <a name="asa-device-support"></a>Obsługa urządzeń ASA

* Obsługa protokołu IKEv2 wymaga ASA w wersji 8.4 i nowszych.

* Obsługa grupa DH i Grupa PFS ponad 5 grupy wymaga wersji ASA 9.x.

* Obsługa szyfrowania IPsec za pomocą usługi GCM AES i integralności IPsec za pomocą algorytmu SHA-256, SHA-384 i SHA-512, wymaga wersji ASA 9.x. To wymaganie obsługi ma zastosowanie do nowszych urządzeniach ASA. Modele ASA 5505, 5510, 5520 5540, 5550 i 5580 nie obsługują tych algorytmów w momencie publikacji. Zapoznaj się z specyfikację urządzenia sieci VPN można sprawdzić algorytmy, które są obsługiwane dla wersji oprogramowania układowego i modeli urządzeń sieci VPN.


### <a name="sample-device-configuration"></a>Przykładowa konfiguracja urządzenia
Skrypt zawiera przykładowe, który jest oparty na konfiguracji i parametry, które są opisane w poprzednich sekcjach. Konfiguracja tunelu sieci VPN S2S składa się z następujących elementów:

1. Interfejsy i trasy
2. Dostęp do listy
3. Zasady protokołu IKE i parametrami (faza 1 lub trybu głównego)
4. Zasady protokołu IPsec i parametry (faza 2 lub tryb szybki)
5. Inne parametry, takie jak TCP MSS obcinanie

> [!IMPORTANT]
> Wykonaj poniższe kroki, aby użyć przykładowego skryptu. Zastąp wartości symboli zastępczych w skrypcie ustawienia urządzenia dla danej konfiguracji.

* Określ konfigurację interfejsu zarówno wewnętrzne i zewnętrzne interfejsy.
* Zidentyfikuj tras dla sieci wewnętrznych i prywatnych i publicznych/na zewnątrz.
* Upewnij się, wszystkie nazwy i numery zasad są unikatowe w urządzeniu.
* Upewnij się, że algorytmy kryptograficzne są obsługiwane na urządzeniu.
* Zastąp następujące **wartości symboli zastępczych** przy użyciu rzeczywistych wartości dla danej konfiguracji:
  - Poza nazwą interfejsu: **poza**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Sieć wirtualną i nazwy bramy sieci lokalnej: **VNetName** i **LNGName**
  - Adres sieciowy w sieci wirtualnej i lokalnej **prefiksów**
  - Odpowiednie **maski sieci**

#### <a name="sample-script"></a>Przykładowy skrypt

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Prostych poleceń debugowania

Użyj następujących poleceń ASA na potrzeby debugowania:

* Pokaż skojarzeń zabezpieczeń IPsec i IKE (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Wprowadź tryb debugowania:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug` Poleceń może generować znaczne danych wyjściowych na konsoli.

* Wyświetl bieżące konfiguracje urządzenia:
    ```
    show run
    ```
    Użyj `show` podpolecenia do listy konkretnych części konfiguracji urządzenia, na przykład:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby skonfigurować połączenia sieć wirtualna-sieć wirtualna i lokalizacjami aktywne aktywne, zobacz [Konfigurowanie bramy sieci VPN typu aktywne aktywne](vpn-gateway-activeactive-rm-powershell.md).
