---
title: Przejście do OpenVPN lub IKEv2 z SSTP | Brama sieci VPN platformy Azure
description: Ten artykuł pomaga zrozumieć sposoby przezwyciężenia limitu 128 równoczesnych połączeń SSTP.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: alzam
ms.openlocfilehash: 60c3a7f4f12993f475b3d03fd0915971d7673a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80143107"
---
# <a name="transition-to-openvpn-protocol-or-ikev2-from-sstp"></a>Przejście do protokołu OpenVPN lub IKEv2 z protokołu SSTP

Połączenie bramy VPN Gateway typu punkt-lokacja pozwala utworzyć bezpieczne połączenie z siecią wirtualną z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. Ten artykuł dotyczy modelu wdrażania Menedżera zasobów i mówi o sposobach przezwyciężenia limitu 128 równoczesnych połączeń sstp przez przejście do protokołu OpenVPN lub IKEv2.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jakiego protokołu używa P2S?

Sieć VPN typu punkt-lokacja może używać jednego z następujących protokołów:

* **Protokół OpenVPN&reg; **, protokół VPN oparty na SSL/TLS. Rozwiązanie SSL VPN może przenikać przez zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, którego używa SSL. OpenVPN może być używany do łączenia się z Androidem, iOS (wersje 11.0 i nowszych), Windows, Linux i Mac (wersje OSX 10.13 i nowsze).

* **Secure Socket Tunneling Protocol (SSTP)**– zastrzeżony protokół VPN oparty na protokole SSL. Rozwiązanie SSL VPN może przenikać przez zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, którego używa SSL. Protokół SSTP jest obsługiwany tylko na urządzeniach z systemem Windows. Platforma Azure obsługuje wszystkie wersje systemu Windows z protokółem SSTP (Windows 7 lub nowszym). **Protokół SSTP obsługuje do 128 jednoczesnych połączeń tylko niezależnie od jednostki SKU bramy.**

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).


>[!NOTE]
>IKEv2 i OpenVPN dla P2S są dostępne tylko dla modelu wdrażania Menedżera zasobów. Nie są one dostępne dla klasycznego modelu wdrażania. Podstawowa jednostka SKU bramy nie obsługuje protokołów IKEv2 lub OpenVPN. Jeśli używasz podstawowej jednostki SKU, trzeba będzie usunąć i ponownie utworzyć produkcyjną bramę sieci wirtualnej SKU.
>

## <a name="migrating-from-sspt-to-ikev2-or-openvpn"></a>Migracja z SSPT do IKEv2 lub OpenVPN

Mogą wystąpić przypadki, gdy chcesz obsługiwać więcej niż 128 równoczesnych połączeń P2S z bramą sieci VPN, ale używasz protokołu SSTP. W takim przypadku należy przejść do protokołu IKEv2 lub OpenVPN.

### <a name="option-1---add-ikev2-in-addition-to-sstp-on-the-gateway"></a>Opcja 1 — dodawanie protokołu IKEv2 oprócz protokołu SSTP w bramie

Jest to najprostsza opcja. SSTP i IKEv2 mogą współistnieć na tej samej bramie i zapewnić większą liczbę równoczesnych połączeń. Można po prostu włączyć IKEv2 na istniejącej bramie i ponownie pobrać klienta.

Dodanie protokołu IKEv2 do istniejącej bramy sieci VPN SSTP nie wpłynie na istniejących klientów i można skonfigurować ich do używania protokołu IKEv2 w małych partiach lub po prostu skonfigurować nowych klientów do używania protokołu IKEv2. Jeśli klient systemu Windows jest skonfigurowany dla sstp i protokołu IKEv2, najpierw spróbuje połączyć się przy użyciu protokołu IKEV2, a jeśli to się nie powiedzie, powróci do protokołu SSTP.

**IKEv2 używa niestandardowych portów UDP, dlatego należy upewnić się, że te porty nie są blokowane na zaporze użytkownika. Porty w użyciu są UDP 500 i 4500.**

Aby dodać protokół IKEv2 do istniejącej bramy, wystarczy przejść do karty "konfiguracja lokacji" w obszarze Brama sieci wirtualnej w portalu i wybierz pozycję **IKEv2 i SSTP (SSL)** z listy rozwijanej.

![punkt do lokacji](./media/ikev2-openvpn-from-sstp/sstptoikev2.png "IKEv2")


### <a name="option-2---remove-sstp-and-enable-openvpn-on-the-gateway"></a>Opcja 2 - Usuń SSTP i włącz OpenVPN na bramie

Ponieważ protokół SSTP i OpenVPN są protokołami opartymi na TLS, nie mogą współistnieć na tej samej bramie. Jeśli zdecydujesz się odejść z SSTP do OpenVPN, będziesz musiał wyłączyć SSTP i włączyć OpenVPN na bramie. Ta operacja spowoduje, że istniejący klienci utracą łączność z bramą sieci VPN, dopóki nowy profil nie zostanie skonfigurowany na kliencie.

Możesz włączyć OpenVPN wraz z IKEv2, jeśli chcesz. OpenVPN jest oparty na TLS i używa standardowego portu TCP 443. Aby przełączyć się na OpenVPN, przejdź do zakładki "konfiguracja lokacji" w obszarze Brama sieci wirtualnej w portalu i wybierz **openvpn (SSL)** lub **IKEv2 i OpenVPN (SSL)** z listy rozwijanej.

![punkt do lokacji](./media/ikev2-openvpn-from-sstp/sstptoopenvpn.png "OpenVPN")

Po skonfigurowaniu bramy istniejący klienci nie będą mogli nawiązać połączenia, dopóki nie wdrożysz i nie [skonfigurujesz klientów OpenVPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-openvpn-clients)

Jeśli korzystasz z systemu Windows 10, możesz również użyć [klienta sieci VPN platformy Azure dla systemu Windows](https://docs.microsoft.com/azure/vpn-gateway/openvpn-azure-ad-client#to-download-the-azure-vpn-client)


## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="what-are-the-client-configuration-requirements"></a>Jakie są wymagania konfiguracyjne klienta?

>[!NOTE]
>W przypadku klientów systemu Windows musisz mieć prawa administratora na urządzeniu klienckim, aby zainicjować połączenie sieci VPN z urządzenia klienckiego na platformę Azure.
>

Użytkownicy korzystają z natywnych klientów sieci VPN na urządzeniach z systemem Windows i Mac dla P2S. Platforma Azure udostępnia plik zip konfiguracji klienta sieci VPN, który zawiera ustawienia wymagane przez tych klientów natywnych do łączenia się z platformą Azure.

* W przypadku urządzeń z systemem Windows konfiguracja klienta sieci VPN składa się z pakietu instalacyjnego, który użytkownicy instalują na swoich urządzeniach.
* W przypadku urządzeń Mac składa się z pliku mobileconfig, który użytkownicy instalują na swoich urządzeniach.

Plik zip zawiera również wartości niektórych ważnych ustawień po stronie platformy Azure, których można użyć do utworzenia własnego profilu dla tych urządzeń. Niektóre wartości obejmują adres bramy sieci VPN, skonfigurowane typy tuneli, trasy i certyfikat główny do sprawdzania poprawności bramy.

>[!NOTE]
>[!INCLUDE [TLS version changes](../../includes/vpn-gateway-tls-change.md)]
>

### <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Które jednostki SKU bramy obsługują sieć VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Aby zapoznać się z zaleceniami jednostek SKU bramy, zobacz [Ustawienia bramy sieci VPN — informacje](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Podstawowa jednostka SKU nie obsługuje uwierzytelniania za pomocą protokołu IKEv2 ani usługi RADIUS.
>

### <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jakie zasady IKE/IPsec są skonfigurowane w bramkach sieci VPN dla P2S?


**IKEv2 (WEREV2)**

|**Szyfrowania** | **Integralność** | **Prf** | **Grupa DH** |
|---        | ---            | ---        | ---     |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA384    | GROUP_ECP256 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_24 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_14 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP384 |
|GCM_AES256 |    GCM_AES256    | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384        | SHA384    | GROUP_24 |
|AES256     |   SHA384        | SHA384    | GROUP_14 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384        | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_24 |
|AES256     |   SHA256        | SHA256    | GROUP_14 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256        | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256        | SHA256    | GROUP_2 |

**Ipsec**

|**Szyfrowania** | **Integralność** | **Grupa PFS** |
|---        | ---            | ---        |
|GCM_AES256    | GCM_AES256 | GROUP_NONE |
|GCM_AES256    | GCM_AES256 | GROUP_24 |
|GCM_AES256    | GCM_AES256 | GROUP_14 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256    | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

### <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jakie zasady TLS są skonfigurowane w bramkach sieci VPN dla P2S?
**Tls**

|**Zasady** |
|---| 
|TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384 |
|TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384 |
|TLS_RSA_WITH_AES_128_GCM_SHA256 |
|TLS_RSA_WITH_AES_256_GCM_SHA384 |
|TLS_RSA_WITH_AES_128_CBC_SHA256 |
|TLS_RSA_WITH_AES_256_CBC_SHA256 |

### <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Jak skonfigurować połączenie P2S?

Konfiguracja P2S wymaga sporo konkretnych kroków. Następujące artykuły zawierają kroki, które mają przebiegać przez konfigurację P2S, oraz łącza do konfigurowania urządzeń klienckich sieci VPN:

* [Konfigurowanie połączenia P2S — uwierzytelnianie RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — uwierzytelnianie certyfikatów natywnych platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurowanie oprogramowania OpenVPN](vpn-gateway-howto-openvpn.md)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie połączenia P2S — uwierzytelnianie RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — uwierzytelnianie certyfikatów natywnych platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" jest znakiem towarowym firmy OpenVPN Inc.**
