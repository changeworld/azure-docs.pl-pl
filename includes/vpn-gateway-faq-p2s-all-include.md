---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 072c16a0e50a4922d44dd354b632f39b33d23cdd
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
### <a name="supportedclientos"></a>Których systemów operacyjnych klienta można używać z połączeniami typu punkt-lokacja?

Obsługiwane są następujące systemy operacyjne klientów:

* Windows 7 (32-bitowy i 64-bitowy)
* Windows Server 2008 R2 (tylko 64-bitowy)
* Windows 8 (32-bitowy i 64-bitowy)
* Windows 8.1 (32-bitowy i 64-bitowy)
* Windows Server 2012 (tylko 64-bitowy)
* Windows Server 2012 R2 (tylko 64-bitowy)
* Windows Server 2016 (tylko 64-bitowy)
* Windows 10
* Wersja systemu Mac OS X 10.11 (El Capitan)
* Mac OS X w wersji 10.12 (Sierra)

### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>Ile punktów końcowych klienta sieci VPN może obejmować konfiguracja punkt-lokacja?

Obsługiwanych jest maksymalnie 128 klientów VPN, które mogą jednocześnie nawiązać połączenie z siecią wirtualną.

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>Czy można pominąć serwery proxy i zapory, korzystając z funkcji punkt-lokacja?

Platforma Azure obsługuje dwa typy połączeń punkt-lokacja w sieci VPN:

* Protokół Secure Socket Tunneling Protocol (SSTP). Protokół SSTP jest własnym rozwiązaniem firmy Microsoft bazującym na protokole SSL. Może przechodzić przez zapory, ponieważ większość zapór otwiera port TCP 443, z którego korzysta protokół SSL.

* Sieć VPN z protokołem IKEv2. Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN IPsec, które korzysta z portów UDP 500 i 4500 i protokołu IP nr 50. Zapory nie zawsze otwierają te porty, więc istnieje możliwość, że sieć VPN z protokołem IKEv2 nie będzie mogła nawiązywać połączeń przez serwery proxy i zapory.

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>Czy w przypadku ponownego uruchomienia komputera klienckiego skonfigurowanego pod kątem połączenia typu punkt-lokacja połączenie z siecią VPN zostanie nawiązane automatycznie?

Domyślnie komputer kliencki nie przywraca automatycznie połączenia z siecią VPN.

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>Czy w przypadku połączeń punkt-lokacja jest obsługiwane automatyczne ponowne nawiązywanie połączenia i DDNS na klientach sieci VPN?

Automatyczne ponowne nawiązywanie połączenia i DDNS nie są obecnie obsługiwane w przypadku połączeń VPN typu punkt-lokacja.

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>Czy z konfiguracji typu lokacja-lokacja i punkt-lokacja można korzystać równolegle w ramach tej samej sieci wirtualnej?

Tak. W przypadku modelu wdrażania przy użyciu usługi Resource Manager dla bramy trzeba mieć sieć VPN opartą na trasach. W przypadku klasycznego modelu wdrażania należy użyć bramy dynamicznej. Nie obsługujemy połączeń typu punkt-lokacja dla bram VPN Gateway z routingiem statycznym lub bram VPN Gateway opartych na zasadach.

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>Czy można skonfigurować klienta typu punkt-lokacja pod kątem jednoczesnego nawiązywania połączenia z wieloma sieciami wirtualnymi?

Nie. Klient połączenia typu punkt-lokacja może łączyć się wyłącznie z zasobami w sieci wirtualnej, w której znajduje się brama sieci wirtualnej.

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>Jakiej przepływności można oczekiwać w przypadku połączeń typu lokacja-lokacja lub punkt-lokacja?

Trudno jest utrzymać dokładną przepływność tuneli VPN. Protokoły IPsec i SSTP należą do niejawnie ciężkich protokołów sieci VPN. Przepływność ograniczają również opóźnienia i przepustowość między lokalizacjami lokalnymi i Internetem. W przypadku bramy VPN Gateway z samymi połączeniami typu punkt-lokacja IKEv2 przez sieć VPN całkowita przepływność, której można oczekiwać, zależy od jednostki SKU bramy. Aby uzyskać więcej informacji o przepływności, zobacz [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) (Jednostki SKU bramy).

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>Czy mogę używać dowolnego programowego klienta sieci VPN dla połączeń typu punkt-lokacja obsługujących protokoły SSTP i/lub IKEv2?

Nie. Możesz używać wyłącznie natywnego klienta sieci VPN w systemie Windows dla protokołu SSTP i natywnego klienta sieci VPN na komputerach Mac dla protokołu IKEv2. Zapoznaj się z listą obsługiwanych systemów operacyjnych klienta.

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Czy platforma Azure obsługuje sieć VPN z protokołem IKEv2 w systemie Windows?

Protokół IKEv2 jest obsługiwany w systemie Windows 10 i Server 2016. Jednak aby można było używać protokołu IKEv2, należy zainstalować aktualizacje i ustaw wartość klucza rejestru lokalnie. Wersje systemu operacyjnego starsze niż Windows 10 nie są obsługiwane i może używać tylko protokołu SSTP.

Do przygotowywania systemu Windows 10 lub Server 2016 dla protokołu IKEv2:

1. Zainstaluj aktualizację.

  | Wersja systemu operacyjnego | Date | Number/Link |
  |---|---|---|---|
  | Windows Server 2016<br>Windows 10 w wersji 1607 | 17 stycznia 2018 r. | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
  | Wersja systemu Windows 10 1703 | 17 stycznia 2018 r. | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
  |  |  |  |  |

2. Ustaw wartość klucza rejestru. Utwórz lub ustawić REG_DWORD "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload" klucza rejestru na wartość 1.

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>Co się dzieje podczas konfigurowania zarówno SSTP i IKEv2 dla połączeń sieci VPN P2S?

Po skonfigurowaniu zarówno SSTP i IKEv2 w środowisku mieszanym (składające się z urządzeniami z systemem Windows i Mac), zawsze będą usiłować tunelowania IKEv2 najpierw klienta VPN systemu Windows, ale powróci do SSTP Jeśli połączenia IKEv2 nie powiedzie. MacOSX połączy się tylko za pomocą protokołu IKEv2.

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Jakie platformy, oprócz systemów Windows i Mac, platforma Azure obsługuje dla sieci VPN P2S?

Platforma Azure obsługuje tylko systemy Windows i Mac dla sieci VPN typu punkt-lokacja.

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Mam już wdrożoną usługę Azure VPN Gateway. Na nim można włączyć usługi RADIUS i/lub IKEv2 sieci VPN?

Tak, aby umożliwić te nowe funkcje w bramach już wdrożony przy użyciu programu Powershell lub w portalu Azure, pod warunkiem, że jednostka SKU, używanej bramy obsługuje usługi RADIUS i/lub IKEv2. Na przykład podstawowa jednostka SKU bramy sieci VPN nie obsługuje usługi RADIUS lub IKEv2.
