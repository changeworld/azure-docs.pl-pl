---
title: Informacje o połączeniach sieci VPN typu punkt-lokacja platformy Azure | Brama sieci VPN
description: Ten artykuł ułatwia zrozumienie połączeń typu point-to-site i pomaga zdecydować, którego typu uwierzytelniania bramy sieci VPN P2S użyć.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: cherylmc
ms.openlocfilehash: 78ed07560fdb15efb2de13c194549f5b433b775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500596"
---
# <a name="about-point-to-site-vpn"></a>Informacje o sieci VPN typu punkt-lokacja

Połączenie bramy VPN Gateway typu punkt-lokacja pozwala utworzyć bezpieczne połączenie z siecią wirtualną z indywidualnego komputera klienckiego. Połączenie typu punkt-lokacja jest ustanawiane przez uruchomienie z komputera klienckiego. To rozwiązanie jest przydatne dla osób pracujących zdalnie, które chcą łączyć się z sieciami wirtualnymi platformy Azure z lokalizacji zdalnej, na przykład z domu lub sali konferencyjnej. Połączenie sieci VPN typu punkt-lokacja jest również przydatne zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Ten artykuł ma zastosowanie w modelu wdrażania usługi Resource Manager.

## <a name="what-protocol-does-p2s-use"></a><a name="protocol"></a>Jakiego protokołu używa P2S?

Sieć VPN typu punkt-lokacja może używać jednego z następujących protokołów:

* **OpenVPN® Protocol**, protokół VPN oparty na SSL/TLS. Rozwiązanie SSL VPN może przenikać przez zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, którego używa SSL. OpenVPN może być używany do łączenia się z Androidem, iOS (wersje 11.0 i nowszych), Windows, Linux i Mac (wersje OSX 10.13 i nowsze).

* Secure Socket Tunneling Protocol (SSTP), zastrzeżony protokół VPN oparty na SSL. Rozwiązanie SSL VPN może przenikać przez zapory, ponieważ większość zapór otwiera port TCP 443 wychodzący, którego używa SSL. Protokół SSTP jest obsługiwany tylko na urządzeniach z systemem Windows. Platforma Azure obsługuje wszystkie wersje systemu Windows z protokółem SSTP (Windows 7 lub nowszym).

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).


>[!NOTE]
>IKEv2 i OpenVPN dla P2S są dostępne tylko dla modelu wdrażania Menedżera zasobów. Nie są one dostępne dla klasycznego modelu wdrażania.
>

## <a name="how-are-p2s-vpn-clients-authenticated"></a><a name="authentication"></a>W jaki sposób uwierzytelniają się klienci sieci VPN P2S?

Zanim platforma Azure zaakceptuje połączenie sieci VPN P2S, użytkownik musi najpierw zostać uwierzytelniony. Istnieją dwa mechanizmy, które oferuje platforma Azure do uwierzytelniania łączącego się użytkownika.

### <a name="authenticate-using-native-azure-certificate-authentication"></a>Uwierzytelnianie przy użyciu natywnego uwierzytelniania certyfikatów platformy Azure

Podczas korzystania z natywnego uwierzytelniania certyfikatu platformy Azure certyfikat klienta, który jest obecny na urządzeniu jest używany do uwierzytelniania użytkownika łączącego. Certyfikaty klienta są generowane na podstawie zaufanego certyfikatu głównego, a następnie instalowane na każdym komputerze klienckim. Można użyć certyfikatu głównego, który został wygenerowany przy użyciu rozwiązania Enterprise lub można wygenerować certyfikat z podpisem własnym.

Sprawdzanie poprawności certyfikatu klienta jest wykonywane przez bramę sieci VPN i odbywa się podczas ustanawiania połączenia sieci VPN P2S. Certyfikat główny jest wymagany do sprawdzania poprawności i musi zostać przekazany na platformę Azure.

### <a name="authenticate-using-native-azure-active-directory-authentication"></a>Uwierzytelnianie przy użyciu natywnego uwierzytelniania usługi Azure Active Directory

Uwierzytelnianie usługi Azure AD umożliwia użytkownikom łączenie się z platformą Azure przy użyciu poświadczeń usługi Azure Active Directory. Uwierzytelnianie usługi Azure AD natywne jest obsługiwane tylko dla protokołu OpenVPN i systemu Windows 10 i wymaga użycia [klienta sieci VPN platformy Azure.](https://go.microsoft.com/fwlink/?linkid=2117554)

Dzięki natywnego uwierzytelniania usługi Azure AD możesz korzystać z dostępu warunkowego usługi Azure AD, a także funkcji uwierzytelniania wieloskładnikowego (MFA) dla sieci VPN.

Na wysokim poziomie należy wykonać następujące kroki, aby skonfigurować uwierzytelnianie usługi Azure AD:

1. [Konfigurowanie dzierżawy usługi Azure AD](openvpn-azure-ad-tenant.md)

2. [Włączanie uwierzytelniania usługi Azure AD na bramie](openvpn-azure-ad-tenant.md#enable-authentication)

3. [Pobieranie i konfigurowanie klienta sieci VPN platformy Azure](https://go.microsoft.com/fwlink/?linkid=2117554)


### <a name="authenticate-using-active-directory-ad-domain-server"></a>Uwierzytelnij się przy użyciu serwera domeny usługi Active Directory (AD)

Uwierzytelnianie domeny usługi AD umożliwia użytkownikom łączenie się z platformą Azure przy użyciu poświadczeń domeny organizacji. Wymaga serwera RADIUS, który integruje się z serwerem usługi AD. Organizacje mogą również korzystać z istniejącego wdrożenia usługi RADIUS.
  
Serwer RADIUS można wdrożyć lokalnie lub w sieci wirtualnej platformy Azure. Podczas uwierzytelniania brama sieci VPN platformy Azure działa jako przekazywanie i przesyłanie dalej wiadomości uwierzytelniania między serwerem RADIUS a urządzeniem łączącym. Dlatego łatwość dostępu bramy do serwera RADIUS jest ważna. Jeśli serwer RADIUS jest obecny lokalnie, połączenie SIECI VPN S2S z platformy Azure do lokacji lokalnej jest wymagane dla osiągnięcia osiągwalności.  
  
Serwer RADIUS może również integrować się z usługami certyfikatów usługi AD. Dzięki temu można używać serwera RADIUS i wdrożenia certyfikatu przedsiębiorstwa do uwierzytelniania certyfikatów P2S jako alternatywy dla uwierzytelniania certyfikatów platformy Azure. Zaletą jest to, że nie trzeba przekazywać certyfikatów głównych i odwołane certyfikaty na platformie Azure.

Serwer RADIUS można również zintegrować z innymi zewnętrznymi systemami tożsamości. Otwiera to wiele opcji uwierzytelniania dla P2S VPN, w tym opcje wieloskładnikowe.

![punkt do lokacji](./media/point-to-site-about/p2s.png "Punkt-lokacja")

## <a name="what-are-the-client-configuration-requirements"></a>Jakie są wymagania konfiguracyjne klienta?

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

## <a name="which-gateway-skus-support-p2s-vpn"></a><a name="gwsku"></a>Które jednostki SKU bramy obsługują sieć VPN P2S?

[!INCLUDE [aggregate throughput sku](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

* Aby zapoznać się z zaleceniami jednostek SKU bramy, zobacz [Ustawienia bramy sieci VPN — informacje](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

>[!NOTE]
>Podstawowa jednostka SKU nie obsługuje uwierzytelniania za pomocą protokołu IKEv2 ani usługi RADIUS.
>

## <a name="what-ikeipsec-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="IKE/IPsec policies"></a>Jakie zasady IKE/IPsec są skonfigurowane w bramkach sieci VPN dla P2S?


**IKEv2 (WEREV2)**

|**Szyfrowania** | **Integralność** | **Prf** | **Grupa DH** |
|---        | ---           | ---       | ---   |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA384    | GROUP_ECP256 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_24 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_14 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP384 |
|GCM_AES256 |   GCM_AES256  | SHA256    | GROUP_ECP256 |
|AES256     |   SHA384      | SHA384    | GROUP_24 |
|AES256     |   SHA384      | SHA384    | GROUP_14 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP384 |
|AES256     |   SHA384      | SHA384    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_24 |
|AES256     |   SHA256      | SHA256    | GROUP_14 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP384 |
|AES256     |   SHA256      | SHA256    | GROUP_ECP256 |
|AES256     |   SHA256      | SHA256    | GROUP_2 |

**Ipsec**

|**Szyfrowania** | **Integralność** | **Grupa PFS** |
|---        | ---           | ---       |
|GCM_AES256 | GCM_AES256 | GROUP_NONE |
|GCM_AES256 | GCM_AES256 | GROUP_24 |
|GCM_AES256 | GCM_AES256 | GROUP_14 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP384 |
|GCM_AES256 | GCM_AES256 | GROUP_ECP256 |
| AES256    | SHA256 | GROUP_NONE |
| AES256    | SHA256 | GROUP_24 |
| AES256    | SHA256 | GROUP_14 |
| AES256    | SHA256 | GROUP_ECP384 |
| AES256    | SHA256 | GROUP_ECP256 |
| AES256    | SHA1 | GROUP_NONE |

## <a name="what-tls-policies-are-configured-on-vpn-gateways-for-p2s"></a><a name="TLS policies"></a>Jakie zasady TLS są skonfigurowane w bramkach sieci VPN dla P2S?
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

## <a name="how-do-i-configure-a-p2s-connection"></a><a name="configure"></a>Jak skonfigurować połączenie P2S?

Konfiguracja P2S wymaga sporo konkretnych kroków. Następujące artykuły zawierają kroki, które mają przebiegać przez konfigurację P2S, oraz łącza do konfigurowania urządzeń klienckich sieci VPN:

* [Konfigurowanie połączenia P2S — uwierzytelnianie RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — uwierzytelnianie certyfikatów natywnych platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

* [Konfigurowanie oprogramowania OpenVPN](vpn-gateway-howto-openvpn.md)

### <a name="to-remove-the-configuration-of-a-p2s-connection"></a>Aby usunąć konfigurację połączenia P2S

Aby uzyskać instrukcje, zobacz [faq](#removeconfig), poniżej.
 
## <a name="faq-for-native-azure-certificate-authentication"></a><a name="faqcert"></a>Często zadawane pytania dotyczące natywnego uwierzytelniania certyfikatów platformy Azure

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="faq-for-radius-authentication"></a><a name="faqradius"></a>Często zadawane pytania dotyczące uwierzytelniania RADIUS

[!INCLUDE [vpn-gateway-point-to-site-faq-include](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie połączenia P2S — uwierzytelnianie RADIUS](point-to-site-how-to-radius-ps.md)

* [Konfigurowanie połączenia P2S — uwierzytelnianie certyfikatów natywnych platformy Azure](vpn-gateway-howto-point-to-site-rm-ps.md)

**"OpenVPN" jest znakiem towarowym firmy OpenVPN Inc.**
