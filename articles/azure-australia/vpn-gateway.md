---
title: VPN Gateway platformy Azure w Australii platformy Azure
description: Zaimplementowanie VPN Gateway w Australii platformy Azure w celu zapewnienia zgodności z usługami ISM i skutecznie chronić australijskie agencje rządowe
author: galey801
ms.service: azure-australia
ms.topic: article
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 00588042fa11ace51eef40cdedbae14c1bd99801
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575422"
---
# <a name="azure-vpn-gateway-in-azure-australia"></a>VPN Gateway platformy Azure w Australii platformy Azure

Krytyczna usługa z jakąkolwiek chmurą publiczną to bezpieczne połączenie zasobów i usług w chmurze z istniejącymi systemami lokalnymi. Usługa udostępniająca tę możliwość na platformie Azure jest VPN Gateway platformy Azure. W tym artykule opisano kluczowe kwestie, które należy wziąć pod uwagę w przypadku skonfigurowania bramy sieci VPN w celu zachowania zgodności z przepisami w zakresie [kontroli bezpieczeństwa informacji (](https://acsc.gov.au/infosec/ism/)ASD) dla programu Australia.

Brama sieci VPN jest używana do wysyłania zaszyfrowanego ruchu między siecią wirtualną na platformie Azure a inną siecią. Następujące trzy scenariusze są rozwiązywane przez bramy sieci VPN:

- Lokacja-lokacja (S2S)
- Punkt-lokacja (P2S)
- Sieć-sieć

Ten artykuł koncentruje się na bramach sieci VPN S2S. Na diagramie 1 przedstawiono przykład konfiguracji bramy sieci VPN S2S.

![Brama sieci VPN z połączeniami z obsługą kilku lokacji](media/vpngateway-multisite-connection-diagram.png)

*Diagram 1 — VPN Gateway S2S platformy Azure*

## <a name="key-design-considerations"></a>Podstawowe zagadnienia dotyczące projektowania

Istnieją trzy opcje sieciowe umożliwiające połączenie platformy Azure z firmowymi klientami rządowymi:

- IKONA
- Azure ExpressRoute
- Publiczny Internet

W przypadku korzystania z trzech opcji sieciowych w programie Cybernetycznymie Podręcznik klienta usługi Microsoft Security Centre [dla systemu Azure](https://servicetrust.microsoft.com/viewpage/Australia) zaleca się używanie VPN Gateway (lub RÓWNOWAŻNEj chronionej w tej usłudze innej firmy). To zalecenie ma na celu upewnienie się, że połączenia są zgodne z kontrolkami ISM na potrzeby szyfrowania i integralności.

### <a name="encryption-and-integrity"></a>Szyfrowanie i integralność

Domyślnie usługa VPN negocjuje algorytmy szyfrowania i integralności oraz parametry podczas ustanawiania połączenia w ramach uzgodnień IKE. Podczas uzgadniania IKE konfiguracja i kolejność preferencji są zależne od tego, czy Brama sieci VPN jest inicjatorem, czy obiektem odpowiadającym. To oznaczenie jest kontrolowane przez urządzenie sieci VPN. Końcowa Konfiguracja połączenia jest kontrolowana przez konfigurację urządzenia sieci VPN. Aby uzyskać więcej informacji na temat zweryfikowanych urządzeń sieci VPN i ich konfiguracji, zobacz [Informacje o usługach sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

Bramy sieci VPN mogą kontrolować szyfrowanie i integralność przez skonfigurowanie niestandardowych zasad protokołu IPsec/IKE w połączeniu.

### <a name="resource-operations"></a>Operacje zasobów

Bramy sieci VPN tworzą połączenie między środowiskami platformy Azure i firmami nienależącymi do platformy Azure za pośrednictwem publicznego Internetu. ISM ma kontrolę, która odnosi się do jawnej autoryzacji połączeń. Domyślnie można używać bram sieci VPN do tworzenia nieautoryzowanych tuneli w bezpiecznych środowiskach. Niezwykle ważne jest, aby organizacje używały kontroli dostępu opartej na rolach (RBAC) na platformie Azure w celu kontrolowania, kto może tworzyć i modyfikować bramy sieci VPN i ich połączenia. Platforma Azure nie ma wbudowanej roli do zarządzania bramami sieci VPN, więc wymagana jest rola niestandardowa.

Dostęp do ról współautor, współautora i sieci jest ściśle kontrolowany. Zalecamy również używanie Azure Active Directory Privileged Identity Management w celu uzyskania bardziej szczegółowej kontroli dostępu.

### <a name="high-availability"></a>Wysoka dostępność

Bramy sieci VPN platformy Azure mogą mieć wiele połączeń i obsługiwać wiele lokalnych urządzeń sieci VPN w tym samym środowisku lokalnym. Zobacz Diagram 1.

Sieci wirtualne na platformie Azure mogą mieć wiele bram sieci VPN, które można wdrożyć w konfiguracjach niezależne, pasywne lub aktywne-aktywne.

Zalecamy wdrożenie wszystkich bram sieci VPN w [konfiguracji o wysokiej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)dostępności. Przykładem są dwa lokalne urządzenia sieci VPN połączone z dwoma bramami sieci VPN w trybie aktywny-pasywny lub aktywny-aktywny. Zobacz diagram 2.

![Nadmiarowe połączenia bramy sieci VPN](media/dual-redundancy.png)

*Diagram 2 — bramy sieci VPN typu aktywne-aktywne i dwa urządzenia sieci VPN*

### <a name="forced-tunneling"></a>Wymuszone tunelowanie

Wymuszone przekierowanie tunelowania, czyli wymuszanie, cały ruch związany z Internetem z powrotem do środowiska lokalnego za pośrednictwem bramy sieci VPN na potrzeby inspekcji i inspekcji. Bez wymuszonego tunelowania ruch związany z Internetem z maszyn wirtualnych na platformie Azure przechodzi bezpośrednio przez infrastrukturę sieciową platformy Azure do publicznej sieci Internet bez opcji inspekcji lub inspekcji ruchu. Wymuszone tunelowanie ma krytyczne znaczenie, gdy organizacja jest wymagana do korzystania z bezpiecznej bramy internetowej (SIG) dla środowiska.

## <a name="detailed-configuration"></a>Konfiguracja szczegółowa

### <a name="service-attributes"></a>Atrybuty usługi

Bramy sieci VPN dla połączeń S2S skonfigurowanych dla instytucji rządowych Australii muszą mieć następujące atrybuty:

|Atrybut | Najpierw|
|--- | --- |
|gatewayType | POŁĄCZENIE|
|

Ustawienia atrybutów wymagane do zapewnienia zgodności z kontrolkami ISM dla chronionych są następujące:

|Atrybut | Najpierw|
|--- |---|
|vpnType |RouteBased|
|vpnClientConfiguration/vpnClientProtocols | IkeV2|
|

Bramy sieci VPN platformy Azure obsługują wiele algorytmów kryptograficznych ze standardów protokołu IPsec i IKE. Zasady domyślne określają maksymalną współdziałanie z szeroką gamę urządzeń sieci VPN innych firm. W związku z tym możliwe jest, że podczas uzgadniania IKE konfiguracja niezgodnego może zostać wynegocjowana. Zdecydowanie zalecamy stosowanie [niestandardowych parametrów zasad protokołu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) do vpnClientConfiguration w BRAMACH sieci VPN, aby upewnić się, że połączenia są zgodne z kontrolkami ISM dla połączeń środowiska lokalnego z platformą Azure. Atrybuty klucza przedstawiono w poniższej tabeli.

|Atrybut|Mam|Najpierw|
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

Dla dhGroup i pfsGroup w poprzedniej tabeli, ECP256 i ECP384 są preferowane, nawet jeśli można użyć innych ustawień.

### <a name="related-services"></a>Powiązane usługi

Podczas projektowania i konfigurowania bramy sieci VPN platformy Azure należy również posiadać i skonfigurować wiele powiązanych usług.

|Usługa | Wymagana akcja|
|--- | ---|
|Sieć wirtualna | Bramy sieci VPN są dołączone do sieci wirtualnej. Utwórz sieć wirtualną przed utworzeniem nowej bramy sieci VPN.|
|Publiczny adres IP | Bramy sieci VPN S2S muszą mieć publiczny adres IP, aby nawiązać połączenie między lokalnym urządzeniem sieci VPN i bramą sieci VPN. Utwórz publiczny adres IP przed utworzeniem bramy sieci VPN S2S.|
|Subnet | Utwórz podsieć sieci wirtualnej dla bramy sieci VPN.|
|

## <a name="implementation-steps-using-powershell"></a>Kroki implementacji przy użyciu programu PowerShell

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

1. Utwórz rolę niestandardową. Przykładem jest współautor virtualNetworkGateway. Utwórz rolę, która ma zostać przypisana do użytkowników, którzy będą mogli tworzyć i modyfikować bramy sieci VPN. Rola niestandardowa powinna zezwalać na następujące operacje:

   Microsoft. Network/virtualNetworkGateways/*  
   Microsoft. Network/Connections/*  
   Microsoft. Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft. Network/adresów publicipaddress/*  
   Microsoft. Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Dodaj rolę niestandardową do użytkowników, którzy mogą tworzyć i zarządzać bramami sieci VPN i połączeniami z środowiskami lokalnymi.

### <a name="create-a-vpn-gateway"></a>Tworzenie bramy sieci VPN

W tych krokach przyjęto założenie, że sieć wirtualna została już utworzona.

1. Utwórz nowy publiczny adres IP.
2. Utwórz podsieć bramy sieci VPN.
3. Utwórz plik konfiguracji adresu IP bramy sieci VPN.
4. Utwórz bramę sieci VPN.
5. Utwórz bramę sieci lokalnej dla lokalnego urządzenia sieci VPN.
6. Utwórz zasady protokołu IPsec. W tym kroku przyjęto założenie, że używasz niestandardowych zasad protokołu IPsec/IKE.
7. Utwórz połączenie między bramą sieci VPN i bramą sieci lokalnej przy użyciu zasad protokołu IPsec.

### <a name="enforce-tunneling"></a>Wymuś tunelowanie

Jeśli tunelowanie wymuszone jest wymagane, przed utworzeniem bramy sieci VPN:

1. Utwórz tabelę tras i reguły tras.
2. Skojarz tabelę tras z odpowiednimi podsieciami.

Po utworzeniu bramy sieci VPN:

- Ustaw GatewayDefaultSite na środowisko lokalne w bramie sieci VPN.

### <a name="example-powershell-script"></a>Przykładowy skrypt programu PowerShell

Przykładowy skrypt programu PowerShell służący do tworzenia niestandardowych zasad protokołu IPsec/IKE jest zgodny z usługami ISM Controls dla australijskiej CHRONIONEj klasyfikacji zabezpieczeń.

Przyjęto założenie, że istnieje sieć wirtualna, Brama sieci VPN i bramy lokalne.

#### <a name="create-an-ipsecike-policy"></a>Tworzenie zasad protokołu IPsec/IKE

Poniższy przykładowy skrypt tworzy zasady protokołu IPsec/IKE z następującymi algorytmami i parametrami:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, okres istnienia SA 14 400 sekund i 102 400 000 KB

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

W tym artykule omówiono konkretną konfigurację VPN Gateway w celu spełnienia wymagań określonych w podręczniku bezpieczeństwa informacji w celu zabezpieczania danych chronionych przez rząd Australii podczas przesyłania. Aby uzyskać instrukcje dotyczące sposobu konfigurowania bramy sieci VPN, zobacz:

- [Omówienie bramy sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/)  
- [Co to jest VPN Gateway?](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
- [Tworzenie sieci wirtualnej z połączeniem sieci VPN typu lokacja-lokacja przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
- [Tworzenie usługi VPN Gateway i zarządzanie nią](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)