---
title: 'Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure w sieci hybrydowej przy użyciu witryny Azure portal'
description: W tym samouczku dowiesz się, jak wdrożyć i skonfigurować Zaporę platformy Azure przy użyciu witryny Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/24/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 208a7a677bdf0b76ffed83e679c6f1ff3041d50d
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239683"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-the-azure-portal"></a>Samouczek: Wdrażanie i konfigurowanie Zapory platformy Azure w sieci hybrydowej przy użyciu witryny Azure portal

W przypadku łączenia sieci lokalnej z siecią wirtualną platformy Azure w celu utworzenia sieci hybrydowej ważną częścią ogólnego planu zabezpieczeń jest możliwość kontrolowania dostępu do zasobów sieciowych platformy Azure.

Aby kontrolować dostęp do sieci hybrydowej korzystającej z reguł, które definiują dozwolony i zabroniony ruch sieciowy, możesz użyć usługi Azure Firewall.

W tym samouczku zostaną utworzone trzy sieci wirtualne:

- **VNet-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VNet-Spoke** — sieć wirtualna będąca szprychą reprezentuje pakiet roboczy na platformie Azure.
- **VNet-Onprem** — lokalna sieć wirtualna reprezentuje sieć lokalną. W rzeczywistym wdrożeniu może być połączony przez sieć VPN lub połączenie usługi ExpressRoute. Dla ułatwienia w tym samouczku zostanie wykorzystane połączenie za pośrednictwem bramy VPN Gateway, a do reprezentowania sieci lokalnej zostanie wykorzystana sieć wirtualna zlokalizowana na platformie Azure.

![Zapora w sieci hybrydowej](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Deklarowanie zmiennych
> * Tworzenie sieci wirtualnej koncentratora zapory
> * Tworzenie sieci wirtualnej będącej szprychą
> * Tworzenie lokalnej sieci wirtualnej
> * Konfigurowanie i wdrażanie zapory
> * Tworzenie i łączenie bram sieci VPN
> * Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy
> * Tworzenie tras
> * Tworzenie maszyn wirtualnych
> * Testowanie zapory

Jeśli chcesz zamiast tego użyć programu Azure PowerShell do wykonania tej procedury, zobacz [Wdrażanie i konfigurowanie Zapory platformy Azure w sieci hybrydowej przy użyciu programu Azure PowerShell.](tutorial-hybrid-ps.md)

## <a name="prerequisites"></a>Wymagania wstępne

Sieć hybrydowa używa modelu architektury koncentratora i szprychy do kierowania ruchu między sieciami wirtualnymi platformy Azure i sieciami lokalnymi. Architektura piasty i szprychy ma następujące wymagania:

- Ustaw **AllowGatewayTransit** podczas równania usługi VNet-Hub do sieci wirtualnej-Spoke. W architekturze sieci koncentratora i szprychy tranzyt bramy umożliwia sieciom wirtualnym szprychy udostępnianie bramy sieci VPN w centrum zamiast wdrażania bram sieci VPN w każdej sieci wirtualnej szprychy. 

   Ponadto trasy do sieci wirtualnych połączonych z bramą lub sieci lokalnych będą automatycznie propagowane do tabel routingu dla równorzędnych sieci wirtualnych przy użyciu przesyłania bramy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie przesyłania bramy sieci VPN do komunikacji równorzędnej w sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Ustaw **UseRemoteGateways** podczas równorzędnej sieci wirtualnej-spoke do sieci wirtualnej-Hub. Jeśli **UseRemoteGateways** jest ustawiona i **AllowGatewayTransit** na zdalnej komunikacji równorzędnej jest również ustawiona, szprychowa sieć wirtualna używa bram zdalnej sieci wirtualnej do przesyłania.
- Aby rozsyłać ruch podsieci szprychowej przez zaporę koncentratora, potrzebna jest trasa zdefiniowana przez użytkownika (UDR), która wskazuje zaporę z **wyłączeniem opcji propagacji trasy bramy sieci wirtualnej.** Opcja **Propagacja trasy bramy sieci wirtualnej wyłączona** uniemożliwia dystrybucję trasy do podsieci szprychy. Zapobiega to konfliktowi wyuczonych tras z twoim UDR.
- Skonfiguruj UDR w podsieci bramy koncentratora, który wskazuje adres IP zapory jako następny przeskok do sieci szprych. W podsieci usługi Azure Firewall nie jest wymagana trasa zdefiniowana przez użytkownika, ponieważ uzyskuje ona informacje o trasach na podstawie protokołu BGP.

Zobacz sekcję [Tworzenie tras](#create-the-routes) w tym samouczku, aby zobaczyć, jak tworzone są te trasy.

>[!NOTE]
>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli azurefirewallsubnet uczy się domyślnej trasy do sieci lokalnej za pośrednictwem protokołu BGP, należy zastąpić to z 0.0.0.0/0 UDR z **NextHopType** wartość ustawiona jako **Internet,** aby utrzymać bezpośrednią łączność z Internetem.
>
>Zaporę platformy Azure można skonfigurować do obsługi wymuszonego tunelowania. Aby uzyskać więcej informacji, zobacz [Azure Firewall wymuszone tunelowanie](forced-tunneling.md).

>[!NOTE]
>Ruch między wirtualnymi sieciami równorzędnymi połączonymi bezpośrednio jest kierowany bezpośrednio nawet wtedy, gdy trasa zdefiniowana przez użytkownika wskazuje usługę Azure Firewall jako bramę domyślną. Aby w tym scenariuszu wysyłać ruch między podsieciami do zapory, trasa zdefiniowana przez użytkownika musi jawnie zawierać prefiks podsieci docelowej w obu podsieciach.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-the-firewall-hub-virtual-network"></a>Tworzenie sieci wirtualnej koncentratora zapory

Najpierw utwórz grupę zasobów zawierającą zasoby do celów tego samouczka:

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. Na stronie głównej portalu platformy Azure wybierz pozycję **Dodawanie grup** > zasobów **.**
3. W **przypadku nazwy grupy zasobów**wpisz **FW-Hybrid-Test**.
4. W polu **Subskrypcja** wybierz subskrypcję.
5. W obszarze **Region**wybierz pozycję **Wschodnie stany USA**. Wszystkie zasoby, które można utworzyć później musi znajdować się w tej samej lokalizacji.
6. Wybierz **pozycję Recenzja + Utwórz**.
7. Wybierz **pozycję Utwórz**.

Teraz utwórz sieć wirtualną:

> [!NOTE]
> Rozmiar podsieci AzureFirewallSubnet to /26. Aby uzyskać więcej informacji na temat rozmiaru podsieci, zobacz [Często zadawane pytania dotyczące zapory platformy Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W obszarze **Sieć**wybierz pozycję **Sieć wirtualna**.
4. W przypadku **programu Name**wpisz **VNet-hub**.
5. W przypadku **przestrzeni adresowej**wpisz **10.5.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W przypadku **grupy zasobów**wybierz **pozycję FW-Hybrid-Test**.
8. W obszarze **Lokalizacja**wybierz pozycję **Wschodnie stany USA**.
9. W obszarze **Podsieci** w polu **Nazwa** wpisz wartość **AzureFirewallSubnet**. Zapora będzie znajdować się w tej podsieci, a nazwą podsieci **musi** być AzureFirewallSubnet.
10. W przypadku **zakresu adresów**wpisz **10.5.0.0/26**. 
11. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

## <a name="create-the-spoke-virtual-network"></a>Tworzenie sieci wirtualnej będącej szprychą

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W obszarze **Sieć**wybierz pozycję **Sieć wirtualna**.
4. W przypadku **nazwy**wpisz **VNet-Spoke**.
5. W przypadku **przestrzeni adresowej**wpisz **10.6.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W przypadku **grupy zasobów**wybierz **pozycję FW-Hybrid-Test**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. W obszarze **Podsieć** w polu **Nazwa** wpisz **SN-Workload**.
10. W przypadku **zakresu adresów**wpisz **10.6.0.0/24**.
11. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

## <a name="create-the-on-premises-virtual-network"></a>Tworzenie lokalnej sieci wirtualnej

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W obszarze **Sieć**wybierz pozycję **Sieć wirtualna**.
4. W wierszu **"Nazwa"** wpisz **VNet-OnPrem**.
5. W polu **Przestrzeń adresowa** wpisz wartość **192.168.0.0/16**.
6. W polu **Subskrypcja** wybierz subskrypcję.
7. W przypadku **grupy zasobów**wybierz **pozycję FW-Hybrid-Test**.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. W **obszarze Podsieć**dla **typu Nazwa** **SN-Corp**.
10. W polu **Zakres adresów** wpisz wartość **192.168.1.0/24**.
11. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

Teraz utwórz drugą podsieć dla bramy.

1. Na stronie **VNet-Onprem** wybierz pozycję **Podsieci**.
2. Wybierz **+Podsieć**.
3. W **yjd u.,** **wpisz GatewaySubnet**.
4. Dla **zakresu adresów (blok CIDR)** typu **192.168.2.0/24**.
5. Kliknij przycisk **OK**.

### <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

Jest to publiczny adres IP używany dla bramy lokalnej.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym wyszukiwania wpisz **publiczny adres IP** i naciśnij klawisz **Enter**.
3. Wybierz **publiczny adres IP,** a następnie wybierz pozycję **Utwórz**.
4. Dla nazwy wpisz **VNet-Onprem-GW-pip**.
5. W przypadku grupy zasobów wpisz **FW-Hybrid-Test**.
6. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
7. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurowanie i wdrażanie zapory

Teraz wdrożyć zaporę w sieci wirtualnej centrum zapory.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W lewej kolumnie wybierz pozycję **Sieć**, a następnie wybierz pozycję **Zapora .**
4. Na stronie **Tworzenie zapory** strony skorzystaj z poniższej tabeli, aby skonfigurować zaporę:

   |Ustawienie  |Wartość  |
   |---------|---------|
   |Subskrypcja     |\<Twoja subskrypcja\>|
   |Grupa zasobów     |**Test FW-Hybrid** |
   |Nazwa     |**AzFW01 ( AzFW01 )**|
   |Lokalizacja     |Wybierz tę samą lokalizację, której użyto poprzednio|
   |Wybieranie sieci wirtualnej     |**Use Existing** (Użyj istniejącej):<br> **Koncentrator sieci wirtualnej**|
   |Publiczny adres IP     |Utwórz nowy: <br>**Nazwa** - **fw-pip**. |

5. Wybierz pozycję **Przegląd + utwórz**.
6. Przejrzyj podsumowanie, a następnie wybierz pozycję **Utwórz,** aby utworzyć zaporę.

   Trwa to kilka minut, aby wdrożyć.
7. Po zakończeniu wdrażania przejdź do grupy zasobów **FW-Hybrid-Test** i wybierz zaporę **AzFW01.**
8. Zanotuj prywatny adres IP. Użyjesz go później podczas tworzenia trasy domyślnej.

### <a name="configure-network-rules"></a>Konfigurowanie reguł sieci

Najpierw dodaj regułę sieciową, aby zezwolić na ruch internetowy.

1. Na stronie **AzFW01** wybierz **pozycję Reguły**.
2. Wybierz kartę **Zbieranie reguł sieciowych.**
3. Wybierz **pozycję Dodaj kolekcję reguł sieciowych**.
4. Dla **Name**, wpisz **RCNet01**.
5. Dla **priorytetu**, typ **100**.
6. W polu **Akcja** wybierz opcję **Zezwalaj**.
6. W obszarze **Reguły**, dla **Name**, wpisz **AllowWeb**.
7. W polu **Protokół** wybierz **TCP**.
8. W przypadku **typu źródła**wybierz **adres IP**.
9. Dla **źródła**, typ **192.168.1.0/24**.
10. Dla **adresu docelowego**, wpisz **10.6.0.0/16**
11. W przypadku **portów docelowych**wpisz **80**.

Teraz dodaj regułę zezwalania na ruch RDP.

W drugim wierszu reguły wpisz następujące informacje:

1. **Name**, wpisz **AllowRDP**.
2. W polu **Protokół** wybierz **TCP**.
3. W przypadku **typu źródła**wybierz **adres IP**.
4. Dla **źródła**, typ **192.168.1.0/24**.
5. Dla **adresu docelowego**, wpisz **10.6.0.0/16**
6. W przypadku **portów docelowych**wpisz **3389**.
7. Wybierz pozycję **Dodaj**.

## <a name="create-and-connect-the-vpn-gateways"></a>Tworzenie i łączenie bram sieci VPN

Sieć wirtualna koncentratora i lokalna sieć wirtualna są połączone za pośrednictwem bram sieci VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Tworzenie bramy sieci VPN dla sieci wirtualnej koncentratora

Teraz utwórz bramę sieci VPN dla sieci wirtualnej koncentratora. Konfiguracje połączeń między sieciami wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym wyszukiwania wpisz **bramę sieci wirtualnej** i naciśnij klawisz **Enter**.
3. Wybierz **wirtualną bramę sieciową**i wybierz pozycję **Utwórz**.
4. W przypadku **nazwy**wpisz **GW-hub**.
5. W przypadku **regionu**wybierz ten sam region, który był poprzednio używany.
6. W przypadku **typu bramy**wybierz pozycję **VPN**.
7. W przypadku **typu VPN**wybierz opcję Oparte **na marszrutach**.
8. W przypadku **jednostki SKU**wybierz opcję **Podstawowe**.
9. W przypadku **sieci wirtualnej**wybierz **koncentrator sieci wirtualnej**.
10. W przypadku **publicznego adresu IP**wybierz pozycję **Utwórz nowy**i wpisz dla nazwy opcję **VNet-hub-GW-pip.**
11. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przejrzyj + utwórz**.
12. Przejrzyj konfigurację, a następnie wybierz pozycję **Utwórz**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Tworzenie bramy sieci VPN dla lokalnej sieci wirtualnej

Teraz utwórz bramę sieci VPN dla lokalnej sieci wirtualnej. Konfiguracje połączeń między sieciami wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym wyszukiwania wpisz **bramę sieci wirtualnej** i naciśnij klawisz **Enter**.
3. Wybierz **wirtualną bramę sieciową**i wybierz pozycję **Utwórz**.
4. Dla **Name**, wpisz **GW-Onprem**.
5. W przypadku **regionu**wybierz ten sam region, który był poprzednio używany.
6. W przypadku **typu bramy**wybierz pozycję **VPN**.
7. W przypadku **typu VPN**wybierz opcję Oparte **na marszrutach**.
8. W przypadku **jednostki SKU**wybierz opcję **Podstawowe**.
9. W przypadku **sieci wirtualnej**wybierz pozycję **VNet-Onprem**.
10. W przypadku **publicznego adresu IP**wybierz pozycję **Utwórz nowy**i wpisz dla nazwy sieć **wirtualna-Onprem-GW-pip.**
11. Zaakceptuj pozostałe wartości domyślne, a następnie wybierz pozycję **Przejrzyj + utwórz**.
12. Przejrzyj konfigurację, a następnie wybierz pozycję **Utwórz**.

### <a name="create-the-vpn-connections"></a>Tworzenie połączeń sieci VPN

Teraz możesz utworzyć połączenia sieci VPN między bramami koncentratora i bramy lokalne.

W tym kroku utworzysz połączenie z sieci wirtualnej koncentratora do lokalnej sieci wirtualnej. W przykładach zastosowano odwołania do klucza współużytkowanego. Możesz wybrać własne wartości dla klucza współużytkowanego. Ważne jest, aby klucz współużytkowany był zgodny z obydwoma połączeniami. Tworzenie połączenia może nieco potrwać.

1. Otwórz grupę zasobów **FW-Hybrid-Test** i wybierz bramę **gw-hub.**
2. Wybierz **pozycję Połączenia** w lewej kolumnie.
3. Wybierz pozycję **Dodaj**.
4. Nazwa połączenia wpisz **Hub-to-Onprem**.
5. Wybierz **opcję Sieci wirtualnej do sieci wirtualnej** dla **typu połączenia**.
6. Dla **drugiej bramy sieci wirtualnej**wybierz **GW-Onprem**.
7. W przypadku **klucza udostępnionego (PSK)** wpisz **AzureA1b2C3**.
8. Kliknij przycisk **OK**.

Utwórz połączenie z lokalnej sieci wirtualnej do sieci wirtualnej koncentratora. Ten krok jest podobny do poprzedniego, jednak w tym przypadku tworzysz połączenie z sieci VNet-Onprem do sieci VNet-hub. Upewnij się, że klucze współużytkowane są zgodne. Po kilku minutach połączenie zostanie ustanowione.

1. Otwórz grupę zasobów **FW-Hybrid-Test** i wybierz bramę **GW-Onprem.**
2. Wybierz **pozycję Połączenia** w lewej kolumnie.
3. Wybierz pozycję **Dodaj**.
4. Nazwa połączenia wpisz **Onprem-to-Hub**.
5. Wybierz **opcję Sieci wirtualnej do sieci wirtualnej** dla **typu połączenia**.
6. W przypadku **drugiej bramy sieci wirtualnej**wybierz **koncentrator GW**.
7. W przypadku **klucza udostępnionego (PSK)** wpisz **AzureA1b2C3**.
8. Kliknij przycisk **OK**.


#### <a name="verify-the-connection"></a>Weryfikowanie połączenia

Po około pięciu minutach stan obu połączeń powinien być **połączony.**

![Połączenia bramy](media/tutorial-hybrid-portal/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy

Teraz nawiąż komunikację równorzędną pomiędzy siecią wirtualną koncentratora i siecią wirtualną będącą szprychą.

1. Otwórz grupę zasobów **FW-Hybrid-Test** i wybierz sieć **wirtualną koncentratora** sieci wirtualnej.
2. W lewej kolumnie wybierz **pozycję Peerings**.
3. Wybierz pozycję **Dodaj**.
4. W **yjście Nazwa**wpisz **HubtoSpoke**.
5. W przypadku **sieci wirtualnej**wybierz **vnet-spoke**
6. Aby uzyskać nazwę komunikacji równorzędnej z sieci wirtualnychnapowietne do centrum sieci wirtualnej, wpisz **SpoketoHub**.
7. Wybierz **pozycję Zezwalaj na tranzyt bramy**.
8. Kliknij przycisk **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Konfigurowanie dodatkowych ustawień komunikacji równorzędnej SpoketoHub

Musisz włączyć **zezwalaj na ruch przesyłany dalej** w komunikacji równorzędnej SpoketoHub.

1. Otwórz grupę zasobów **FW-Hybrid-Test** i wybierz sieć **wirtualną-Spoke.**
2. W lewej kolumnie wybierz **pozycję Peerings**.
3. Wybierz **spoketoHub** komunikacji równorzędnej.
4. W obszarze **Zezwalaj na ruch przesyłany dalej z koncentratora sieci wirtualnej do sieci wirtualnej -Spoke**wybierz pozycję **Włączone**.
5. Wybierz **pozycję Zapisz**.

## <a name="create-the-routes"></a>Tworzenie tras

Następnie należy utworzyć kilka tras:

- Trasa z podsieci bramy koncentratora do podsieci będącej szprychą za pośrednictwem adresu IP zapory
- Trasa domyślna z podsieci będącej szprychą za pośrednictwem adresu IP zapory

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym wyszukiwania wpisz **tabelę tras** i naciśnij klawisz **Enter**.
3. Wybierz **pozycję Tabela Marszruta**.
4. Wybierz **pozycję Utwórz**.
5. Dla nazwy wpisz **UDR-Hub-Spoke**.
6. Wybierz **FW-Hybrid-Test** dla grupy zasobów.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
9. Wybierz **pozycję Utwórz**.
10. Po utworzeniu tabeli tras wybierz ją, aby otworzyć stronę tabeli trasy.
11. Wybierz **pozycję Trasy** w lewej kolumnie.
12. Wybierz pozycję **Dodaj**.
13. W przypadku nazwy trasy wpisz **ToSpoke**.
14. W przypadku prefiksu adresu wpisz **10.6.0.0/16**.
15. W przypadku następnego typu przeskoku wybierz pozycję **Urządzenie wirtualne**.
16. W przypadku następnego adresu przeskoku wpisz prywatny adres IP zapory, który został wcześniej odnotowany.
17. Kliknij przycisk **OK**.

Teraz skojarzyć trasę z podsiecią.

1. Na stronie **UDR-Hub-Spoke - Routes (Trasy)** wybierz pozycję **Podsieci**.
2. Wybierz **pozycję Skojarz**.
3. Wybierz **pozycję Wybierz sieć wirtualną**.
4. Wybierz **koncentrator sieci wirtualnej**.
5. Wybierz **GatewaySubnet**.
6. Kliknij przycisk **OK**.

Teraz utwórz trasę domyślną z podsieci szprychowej.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym wyszukiwania wpisz **tabelę tras** i naciśnij klawisz **Enter**.
3. Wybierz **pozycję Tabela Marszruta**.
5. Wybierz **pozycję Utwórz**.
6. Aby uzyskać nazwę, wpisz **UDR-DG**.
7. Wybierz **FW-Hybrid-Test** dla grupy zasobów.
8. W polu **Lokalizacja** wybierz tę samą lokalizację, która była używana poprzednio.
4. W obszarze **Propagacja trasy bramy sieci wirtualnej**wybierz pozycję **Wyłączone**.
1. Wybierz **pozycję Utwórz**.
2. Po utworzeniu tabeli tras wybierz ją, aby otworzyć stronę tabeli trasy.
3. Wybierz **pozycję Trasy** w lewej kolumnie.
4. Wybierz pozycję **Dodaj**.
5. Aby uzyskać nazwę trasy, wpisz **ToHub**.
6. W przypadku prefiksu adresu wpisz **0.0.0.0/0**.
7. W przypadku następnego typu przeskoku wybierz pozycję **Urządzenie wirtualne**.
8. W przypadku następnego adresu przeskoku wpisz prywatny adres IP zapory, który został wcześniej odnotowany.
9. Kliknij przycisk **OK**.

Teraz skojarzyć trasę z podsiecią.

1. Na stronie **UDR-DG - Routes (Trasy)** wybierz pozycję **Podsieci**.
2. Wybierz **pozycję Skojarz**.
3. Wybierz **pozycję Wybierz sieć wirtualną**.
4. Wybierz **vnet-szprycha**.
5. Wybierz **zadanie SN**.
6. Kliknij przycisk **OK**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Teraz utwórz maszyny wirtualne pakietu roboczego szprychy i sieci lokalnej, a następnie umieść je w odpowiednich podsieciach.

### <a name="create-the-workload-virtual-machine"></a>Tworzenie maszyny wirtualnej pakietu roboczego

Utwórz maszynę wirtualną w sieci wirtualnej szprychy, z uruchomionymi usługami IIS bez publicznego adresu IP.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W obszarze **Popularne**wybierz pozycję **Centrum danych systemu Windows Server 2016**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:
    - **Grupa zasobów** — wybierz **FW-Hybrid-Test**.
    - **Nazwa maszyny wirtualnej**: *VM-Spoke-01*.
    - **Region** — ten sam region, w który był używany wcześniej.
    - **Nazwa użytkownika**: *azureuser*.
    - **Hasło**: *Azure123456!*
4. Wybierz pozycję **Dalej: Dyski**.
5. Zaakceptuj ustawienia domyślne i wybierz **pozycję Dalej: Sieć**.
6. Wybierz **VNet-Spoke** dla sieci wirtualnej i podsieci jest **SN-Workload**.
7. W przypadku **adresu IP publicznego**wybierz pozycję **Brak**.
8. W przypadku **publicznych portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty**, a następnie wybierz **HTTP (80)** i **RDP (3389)**
9. Wybierz **dalej:Zarządzanie**.
10. W przypadku **diagnostyki rozruchu**wybierz **opcję Wyłącz**.
11. Wybierz **pozycję Recenzja+Utwórz**, przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.

### <a name="install-iis"></a>Instalowanie usług IIS

1. W witrynie Azure portal otwórz powłokę chmury i upewnij się, że jest ustawiona na **program PowerShell.**
2. Uruchom następujące polecenie, aby zainstalować usługi IIS na maszynie wirtualnej i w razie potrzeby zmienić lokalizację:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Tworzenie maszyny wirtualnej w środowisku lokalnym

Jest to maszyna wirtualna używana do łączenia się przy użyciu pulpitu zdalnego z publicznym adresem IP. Z tego miejsca nawiążesz następnie połączenie z serwerem lokalnym za pośrednictwem zapory.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W obszarze **Popularne**wybierz pozycję **Centrum danych systemu Windows Server 2016**.
3. Wprowadź poniższe wartości dla maszyny wirtualnej:
    - **Grupa zasobów** — wybierz istniejącą, a następnie wybierz **pozycję FW-Hybrid-Test**.
    - **Nazwa maszyny wirtualnej** - *VM-Onprem*.
    - **Region** — ten sam region, w który był używany wcześniej.
    - **Nazwa użytkownika**: *azureuser*.
    - **Hasło**: *Azure123456!*.
4. Wybierz pozycję **Dalej: Dyski**.
5. Zaakceptuj ustawienia domyślne i wybierz **pozycję Dalej:Sieć**.
6. Wybierz **sieć wirtualną-Onprem** dla sieci wirtualnej, a podsiecią jest **SN-Corp**.
7. W przypadku **publicznych portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty**, a następnie wybierz pozycję **RDP (3389)**
8. Wybierz **dalej:Zarządzanie**.
9. W przypadku **diagnostyki rozruchu**wybierz **opcję Wyłącz**.
10. Wybierz **pozycję Recenzja+Utwórz**, przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.

## <a name="test-the-firewall"></a>Testowanie zapory

1. Najpierw należy zwrócić uwagę na prywatny adres IP maszyny **wirtualnej VM-spoke-01.**

2. W witrynie Azure Portal połącz się z maszyną wirtualną **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Otwórz przeglądarkę internetową na maszynie wirtualnej **VM-Onprem**, a następnie przejdź do lokalizacji http://\<VM-spoke-01 private IP\>.

   Powinna zostać wyświetlona strona internetowa **VM-spoke-01:** ![VM-Spoke-01](media/tutorial-hybrid-portal/VM-Spoke-01-web.png)

4. Z maszyny wirtualnej **VM-Onprem** otwórz pulpit zdalny do **VM-spoke-01** pod prywatnym adresem IP.

   Połączenie powinno zakończyć się pomyślnie i powinno być możliwe zalogowanie się.

Więc teraz masz zweryfikowane, że reguły zapory działają:

<!---- You can ping the server on the spoke VNet.--->
- Możesz przeglądać serwer internetowy w sieci wirtualnej będącej szprychą.
- Możesz nawiązać połączenie z serwerem w sieci wirtualnej będącej szprychą, korzystając z protokołu RDP.

Następnie zmień ustawienie akcji kolekcji reguł sieci zapory na **Odmów**, aby sprawdzić, czy reguły zapory działają zgodnie z oczekiwaniami.

1. Wybierz zaporę **AzFW01.**
2. Wybierz **reguły**.
3. Wybierz kartę **Zbieranie reguł sieciowych** i wybierz kolekcję reguł **RCNet01.**
4. W **obszarze Akcja**wybierz pozycję **Odmów**.
5. Wybierz **pozycję Zapisz**.

Zamknij wszystkie zdalne pulpity, zanim zaczniesz testować zmienione zasady. Teraz ponownie uruchom testy. Tym razem wszystkie powinny zakończyć się niepowodzeniem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **FW-Hybrid-Test**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

Następnie możesz monitorować dzienniki usługi Azure Firewall.

> [!div class="nextstepaction"]
> [Samouczek: monitorowanie dzienników usługi Azure Firewall](./tutorial-diagnostics.md)
