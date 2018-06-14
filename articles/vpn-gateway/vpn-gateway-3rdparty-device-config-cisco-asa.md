---
title: Przykładowa konfiguracja urządzenia Cisco ASA nawiązywania połączenia bramy sieci VPN platformy Azure | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera Przykładowa konfiguracja urządzenia Cisco ASA nawiązywania połączenia bramy sieci VPN platformy Azure.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: fbe22b70b4fe3463ffc7b0e9a7ebd683f681117d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910762"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Przykładowa konfiguracja: Cisco ASA urządzenia (BGP IKEv2/nie)
W tym artykule przedstawiono przykładowe konfiguracje dla urządzenia firmy Cisco adaptacyjną zabezpieczeń urządzenia (ASA) do bram sieci VPN platformy Azure. Przykład dotyczy urządzenia Cisco ASA, które działają IKEv2 bez protokołu BGP (Border Gateway). 

## <a name="device-at-a-glance"></a>Urządzenia, w skrócie

|                        |                                   |
| ---                    | ---                               |
| Dostawca urządzenia          | Cisco                             |
| Model urządzenia           | ASA                               |
| Wersja docelowa         | 8.4 i nowsze                     |
| Przetestowany modelu           | ASA 5505                          |
| Wersja przetestowany         | 9.2                               |
| Wersja IKE            | IKEv2                             |
| BGP                    | Nie                                |
| Typ bramy sieci VPN platformy Azure | Brama sieci VPN opartej na trasach           |
|                        |                                   |

> [!NOTE]
> Przykładowa konfiguracja łączy z urządzenia Cisco ASA Azure **opartej na trasach** bramy sieci VPN. Połączenie korzysta z niestandardowych zasad IPsec i IKE **UsePolicyBasedTrafficSelectors** opcji, zgodnie z opisem w [w tym artykule](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Przykład wymaga używające urządzeń ASA **IKEv2** zasad z konfiguracjami oparte na liście dostępu, nie VTI na podstawie. Zapoznaj się specyfikację dostawcy urządzenia sieci VPN do sprawdzenia, czy IKEv2 zasad jest obsługiwana na urządzeniach lokalnych sieci VPN.


## <a name="vpn-device-requirements"></a>Wymagania dotyczące urządzeń sieci VPN
Bramy sieci VPN platformy Azure umożliwia standardowych pakietów protokołu IPsec i IKE ustanowienia tuneli VPN lokacja-lokacja (S2S). Szczegółowe parametry protokołu IPsec i IKE i domyślne algorytmów kryptograficznych dla bram sieci VPN platformy Azure, zobacz [urządzenia sieci VPN o](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Opcjonalnie można określić dokładną kombinację algorytmów kryptograficznych i kluczy sile dla określonego połączenia, zgodnie z opisem w [o wymaganiach dotyczących kryptograficznych](vpn-gateway-about-compliance-crypto.md). Jeśli określisz dokładne kombinacja silnych kluczy i algorytmów, należy użyć odpowiedniego specyfikacje na urządzeniach sieci VPN.

## <a name="single-vpn-tunnel"></a>Jeden tunel VPN
Ta konfiguracja składa się z jednego tunelu S2S VPN między bramą sieci VPN platformy Azure i lokalnego urządzenia sieci VPN. Opcjonalnie można skonfigurować [BGP przez tunel VPN](#bgp).

![Jeden tunel S2S VPN](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Aby uzyskać instrukcje krok po kroku do tworzenia konfiguracji platformy Azure, zobacz [konfiguracji tunelu VPN pojedynczy](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Sieć wirtualna i informacji dotyczących bramy sieci VPN
W tej sekcji opisano parametry przykładowej.

| **Parameter**                | **Wartość**                    |
| ---                          | ---                          |
| Prefiksy adresów sieci wirtualnej        | 10.11.0.0/16<br>10.12.0.0/16 |
| Adres IP bramy sieci VPN platformy Azure         | Azure_Gateway_Public_IP      |
| Prefiksy adresów lokalnych | 10.51.0.0/16<br>10.52.0.0/16 |
| Lokalny adres IP urządzenia sieci VPN    | OnPrem_Device_Public_IP     |
| * Wirtualnej sieci BGP ASN                | 65010                        |
| * Azure IP elementu równorzędnego protokołu BGP           | 10.12.255.30                 |
| * Lokalnymi BGP ASN         | 65050                        |
| * IP elementu równorzędnego BGP lokalnej     | 10.52.255.254                |
|                              |                              |

\*Opcjonalny parametr dla protokołu BGP tylko.

### <a name="ipsecike-policy-and-parameters"></a>Zasady protokołu IPsec/IKE i parametry
W poniższej tabeli wymieniono Algorytmy IPsec i IKE i parametrów, które są używane w próbce. Zapoznaj się ze specyfikacją urządzenia sieci VPN można zweryfikować algorytmy, które są obsługiwane w przypadku modeli urządzenia sieci VPN i wersji oprogramowania układowego.

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

\*Na niektórych urządzeniach integralności IPsec musi być wartością null, gdy usługi GCM AES jest algorytm szyfrowania IPsec.

### <a name="asa-device-support"></a>Obsługa urządzeń ASA

* Obsługa protokołu IKEv2 wymaga ASA wersji 8.4 i nowszych.

* Obsługa grupa DH i Grupa PFS poza grupy 5 wymaga wersji ASA 9.x.

* Obsługa szyfrowania IPsec z usługą GCM AES i integralności IPsec z algorytmu SHA-256, SHA-384 i SHA-512, wymaga wersji ASA 9.x. To wymaganie Obsługa dotyczy nowych urządzeń ASA. W momencie publikacji modeli ASA 5505, 5510 5520, 5540, 5550 i 5580 nie obsługują te algorytmy. Zapoznaj się ze specyfikacją urządzenia sieci VPN można zweryfikować algorytmy, które są obsługiwane w przypadku modeli urządzenia sieci VPN i wersji oprogramowania układowego.


### <a name="sample-device-configuration"></a>Przykładowa konfiguracja urządzenia
Skrypt zawiera przykładowe, która jest oparta na konfiguracji i parametrów, które zostały opisane w poprzednich sekcjach. Konfiguracja tunelu S2S VPN składa się z następujących elementów:

1. Interfejsów i tras
2. Dostęp do listy
3. Parametry (faza 1 lub trybu głównego) i zasad IKE
4. Parametry (faza 2 lub trybu szybkiego) i zasad protokołu IPsec
5. Inne parametry, takie jak TCP MSS ładunku

> [!IMPORTANT]
> Wykonaj poniższe kroki, aby użyć przykładowego skryptu. Zastąp symbole zastępcze w skrypcie ustawienia urządzenia dla danej konfiguracji.

* Określ konfigurację interfejsu zarówno wewnątrz lub na zewnątrz interfejsów.
* Zidentyfikuj tras dla sieci wewnętrznej i prywatnego oraz publicznego i na zewnątrz.
* Upewnij się, wszystkie nazwy i numery zasad są unikatowe w urządzeniu.
* Upewnij się, że algorytmy kryptograficzne są obsługiwane na urządzeniu.
* Zastąp następujące **symbole zastępcze** rzeczywiste wartości dla konfiguracji:
  - Poza Nazwa interfejsu: **poza**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Sieć wirtualna i nazwy bramy sieci lokalnej: **VNetName** i **LNGName**
  - Adres sieciowy sieci wirtualnej i lokalnymi **prefiksów**
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
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
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

* Pokaż skojarzenia zabezpieczeń IPsec lub IKE (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Tryb debugowania:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug` Poleceń można wygenerować istotne dane wyjściowe do konsoli.

* Wyświetl bieżące konfiguracje urządzenia:
    ```
    show run
    ```
    Użyj `show` podpolecenia do listy poszczególnych części konfigurację urządzenia, na przykład:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Kolejne kroki
Aby skonfigurować połączenia do wirtualnymi i między różnymi lokalizacjami aktywny aktywny, zobacz [konfigurowania bramy sieci VPN aktywny aktywny](vpn-gateway-activeactive-rm-powershell.md).
