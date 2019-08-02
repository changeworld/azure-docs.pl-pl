---
title: VPN Gateway platformy Azure w Australii platformy Azure
description: Zaimplementowanie VPN Gateway w Australii platformy Azure w celu zapewnienia zgodności z usługami ISM i skutecznie chronić australijskie agencje rządowe
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 442ad6334a1775033018005d4a85875dbcb08ada
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571850"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>VPN Gateway platformy Azure w Australii platformy Azure

Krytyczna usługa z jakąkolwiek chmurą publiczną to bezpieczne połączenie zasobów i usług w chmurze z istniejącymi systemami lokalnymi.  Usługa udostępniająca tę możliwość na platformie Azure to VPN Gateway platformy Azure (VPN Gateway). W tym artykule opisano kluczowe zagadnienia dotyczące konfigurowania VPN Gateway w celu zachowania zgodności z formantami ręcznymi (ISM) z [zabezpieczeniami informacyjnymi](https://acsc.gov.au/infosec/ism/) (ASD) w systemie.

VPN Gateway służy do wysyłania zaszyfrowanego ruchu między siecią wirtualną na platformie Azure a inną siecią.  Bramy sieci VPN są rozwiązywane przez następujące trzy scenariusze:

- **Lokacja-lokacja** S2S
- **Punkt-lokacja** P2S
- **VNet-to-VNet**

Ten artykuł koncentruje się na bramach sieci VPN S2S. Na diagramie 1 przedstawiono przykładową konfigurację bramy sieci VPN typu lokacja-lokacja.

![VPN Gateway z połączeniami z obsługą kilku lokacji](media/vpngateway-multisite-connection-diagram.png)

*Diagram 1 — VPN Gatewaya lokacja-lokacja na platformie Azure*

## <a name="key-design-considerations"></a>Podstawowe zagadnienia dotyczące projektowania

Istnieją trzy opcje sieciowe umożliwiające połączenie platformy Azure z firmowymi klientami rządowymi:

- **IKONA**
- **ExpressRoute**
- **Publiczny Internet**

W programie australijski Podręcznik klienta usługi cybernetycznymi Security [dla systemu Azure](https://servicetrust.microsoft.com/viewpage/Australia) zaleca się używanie VPN Gateway (lub RÓWNOWAŻNEj chronionej, certyfikowanej usług innej firmy) w połączeniu z trzema opcjami sieci, aby upewnić się, że połączenia są zgodne z ISM kontroluje szyfrowanie i integralność.

### <a name="encryption-and-integrity"></a>Szyfrowanie i integralność

Domyślnie usługa VPN negocjuje algorytmy szyfrowania i integralności oraz parametry podczas ustanawiania połączenia w ramach uzgodnień IKE.  Podczas uzgadniania IKE konfiguracja i kolejność preferencji zależą od tego, czy VPN Gateway jest inicjatorem, czy obiektem odpowiadającym (NB: jest to kontrolowane przez urządzenie sieci VPN).  Końcowa Konfiguracja połączenia jest kontrolowana przez konfigurację urządzenia sieci VPN.  Aby uzyskać szczegółowe informacje na temat zweryfikowanych urządzeń sieci VPN i ich konfiguracji, zobacz tutaj: [Informacje o urządzeniach sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

Bramy sieci VPN mogą kontrolować szyfrowanie i integralność przez skonfigurowanie niestandardowych zasad protokołu IPsec/IKE w połączeniu.

### <a name="resource-operations"></a>Operacje zasobów

Bramy sieci VPN tworzą połączenie między środowiskami platformy Azure i firmami nienależącymi do platformy Azure za pośrednictwem publicznego Internetu.  ISM ma kontrolę, która odnosi się do jawnej autoryzacji połączeń.  Domyślnie można używać bram sieci VPN do tworzenia nieautoryzowanych tuneli w bezpiecznych środowiskach.  W związku z tym ważne jest, aby organizacje używały Access Control opartej na rolach (RBAC) na platformie Azure w celu kontrolowania, kto może tworzyć i modyfikować bramy sieci VPN i ich połączenia.  Platforma Azure nie ma roli "wbudowane" do zarządzania bramami sieci VPN w związku z tym wymaga roli niestandardowej.

Dostęp do ról "Owner", "Współautor" i "Współautor sieci" jest ściśle kontrolowany.  Zaleca się również, aby Azure AD Privileged Identity Management jest używany w celu uzyskania bardziej szczegółowej kontroli dostępu.

### <a name="high-availability"></a>Wysoka dostępność

Bramy sieci VPN platformy Azure mogą mieć wiele połączeń (zobacz diagram 1) i obsługiwać wiele lokalnych urządzeń sieci VPN w tym samym środowisku lokalnym.  

Sieci wirtualne na platformie Azure mogą mieć wiele bram sieci VPN, które można wdrożyć w konfiguracjach niezależne, pasywne lub aktywne-aktywne.

Zaleca się, aby wszystkie bramy sieci VPN zostały wdrożone w [konfiguracji o wysokiej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)dostępności: na przykład dwa lokalne urządzenia sieci VPN połączone z dwoma bramami sieci VPN w trybie aktywny-pasywny lub aktywny-aktywny (patrz diagram 2).

![VPN Gateway nadmiarowe połączenia](media/dual-redundancy.png)

*Diagram 2 — bramy sieci VPN typu aktywne-aktywne i dwa urządzenia sieci VPN*

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Wymuszone przekierowania tunelowania lub "wymusza" cały ruch związany z Internetem z powrotem do środowiska lokalnego za pośrednictwem VPN Gateway na potrzeby inspekcji i inspekcji. Bez wymuszonego tunelowania ruch związany z Internetem z maszyn wirtualnych na platformie Azure przechodzi bezpośrednio przez infrastrukturę sieciową platformy Azure do publicznej sieci Internet bez opcji inspekcji lub inspekcji ruchu.  Jest to ważne, gdy organizacja jest wymagana do korzystania z bezpiecznej bramy internetowej (SIG) dla środowiska.

## <a name="detailed-configuration"></a>Konfiguracja szczegółowa

### <a name="service-attributes"></a>Atrybuty usługi

Bramy sieci VPN dla połączeń S2S skonfigurowanych dla instytucji rządowych Australii muszą mieć następujące atrybuty:

|Atrybut | NAJPIERW|
|--- | --- |
|gatewayType | POŁĄCZENIE|
|

Ustawienia atrybutów wymagane do zapewnienia zgodności z kontrolkami ISM dla chronionych są następujące:

|Atrybut | NAJPIERW|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Bramy sieci VPN platformy Azure obsługują wiele algorytmów kryptograficznych ze standardów protokołu IPsec i IKE.  Domyślne zestawy zasad maksymalizują współdziałanie z szeroką gamę urządzeń sieci VPN innych firm.  W związku z tym jest możliwe, że podczas uzgadniania protokołu IKE niezgodna konfiguracja zostanie wynegocjowana.  Dlatego zdecydowanie zaleca się stosowanie [niestandardowych parametrów zasad protokołu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) do VpnClientConfiguration w BRAMACH sieci VPN, aby upewnić się, że połączenia spełniają kontrolę ISM dla połączeń środowiska lokalnego z platformą Azure.  Atrybuty klucza są następujące:

|Atrybut|MAM|NAJPIERW|
|---|---|---|
|saLifeTimeSeconds|< wartość 14400 s|> 300 s|
|saDataSizeKilobytes| |> 1024 KB|
|ipsecEncryption| |AES256 — GCMAES256|
|ipsecIntegrity| |SHA256 — GCMAES256|
|ikeEncryption| |AES256 — GCMAES256|
|ikeIntegrity| |SHA256 — GCMAES256|
|dhGroup|DHGroup14, DHGroup24, ECP256, ECP384|DHGroup2|
|pfsGroup|PFS2048, PFS24, ECP256, ECP384||
|

*Dla dhGroup i pfsGroup w powyższej tabeli, ECP256 i ECP384 są preferowane, mimo że można użyć innych ustawień*

### <a name="related-services"></a>Powiązane usługi

Podczas projektowania i konfigurowania usługi Azure VPN Gateway istnieje wiele powiązanych usług, które również muszą istnieć i zostały skonfigurowane:

|Usługa | Wymagana akcja|
|--- | ---|
|Sieć wirtualna | Bramy sieci VPN są dołączone do sieci wirtualnej.  Przed utworzeniem nowego VPN Gateway należy utworzyć sieć wirtualną.|
|Publiczny adres IP | Bramy sieci VPN S2S muszą mieć publiczny adres IP, aby nawiązać połączenie między lokalnym urządzeniem sieci VPN a VPN Gateway.  Publiczny adres IP musi zostać utworzony przed utworzeniem VPN Gateway S2S.|
|Subnet | Należy utworzyć podsieć sieci wirtualnej dla VPN Gateway.|
|

## <a name="implementation-steps-using-powershell"></a>Kroki implementacji przy użyciu programu PowerShell

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)

1. Utwórz rolę niestandardową (na przykład współautor virtualNetworkGateway).  Utwórz rolę, która ma zostać przypisana do użytkowników, którzy będą mogli tworzyć i modyfikować bramy sieci VPN. Rola niestandardowa powinna zezwalać na następujące operacje:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/adresów publicipaddress/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Dodaj rolę niestandardową do użytkowników, którzy mogą tworzyć i zarządzać bramami sieci VPN i połączeniami z środowiskami lokalnymi.

### <a name="create-vpn-gateway"></a>Utwórz VPN Gateway

*W tych krokach przyjęto, że sieć wirtualna została już utworzona*

1. Utwórz nowy publiczny adres IP
2. Tworzenie podsieci VPN Gateway
3. Utwórz konfigurację VPN Gateway IP
4. Utwórz bramę VPN Gateway
5. Tworzenie bramy sieci lokalnej dla lokalnego urządzenia sieci VPN
6. Tworzenie zasad protokołu IPsec (przy założeniu przy użyciu niestandardowych zasad protokołu IPsec/IKE)
7. Utwórz połączenie między VPN Gateway i bramą sieci lokalnej przy użyciu zasad protokołu IPsec

### <a name="enforce-tunneling"></a>Wymuś tunelowanie

Jeśli Wymuszone tunelowanie jest wymagane, przed utworzeniem VPN Gateway:

1. Utwórz tabelę tras i reguły tras
2. Skojarz tabelę tras z odpowiednimi podsieciami

Po utworzeniu VPN Gateway:

1. Ustaw GatewayDefaultSite na środowisko lokalne na VPN Gateway

### <a name="example-powershell-script"></a>Przykładowy skrypt programu PowerShell

Przykładowy skrypt programu PowerShell służący do tworzenia niestandardowych zasad protokołu IPSEC/IKE, które są zgodne z usługami ISM Controls dla australijskiej CHRONIONEj klasyfikacji zabezpieczeń.

Przyjęto założenie, że istnieje sieć wirtualna, VPN Gateway i bramy lokalne.

#### <a name="create-an-ipsecike-policy"></a>Tworzenie zasad protokołu IPsec/IKE

Poniższy przykładowy skrypt tworzy zasady protokołu IPsec/IKE z następującymi algorytmami i parametrami:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, okres istnienia SA 14 400 sekund & 102400000 KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### <a name="create-a-s2s-vpn-connection-with-the-custom-ipsecike-policy"></a>Tworzenie połączenia sieci VPN S2S przy użyciu niestandardowych zasad protokołu IPsec/IKE

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono konkretną konfigurację VPN Gateway w celu spełnienia wymagań określonych w podręczniku zabezpieczeń informacji (ISM) na potrzeby zabezpieczania danych chronionych przez rząd Australii podczas przesyłania. Aby uzyskać szczegółowe instrukcje dotyczące konfigurowania VPN Gateway:

- [Omówienie usługi Azure Virtual Network Gateway](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Co to jest VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Tworzenie usługi VPN Gateway i zarządzanie nią](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)