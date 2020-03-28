---
title: 'Samouczek: Zabezpiecz sieć wirtualną centrum przy użyciu usługi Azure Firewall Manager w wersji zapoznawczej'
description: W tym samouczku dowiesz się, jak zabezpieczyć sieć wirtualną za pomocą usługi Azure Firewall Manager przy użyciu witryny Azure portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: cdd416bdb833e4784334a6847d724a7375e2ef8d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77459957"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager-preview"></a>Samouczek: Zabezpiecz sieć wirtualną centrum przy użyciu usługi Azure Firewall Manager w wersji zapoznawczej 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

W przypadku łączenia sieci lokalnej z siecią wirtualną platformy Azure w celu utworzenia sieci hybrydowej ważną częścią ogólnego planu zabezpieczeń jest możliwość kontrolowania dostępu do zasobów sieciowych platformy Azure.

Korzystając z usługi Azure Firewall Manager Preview, można utworzyć sieć wirtualną centrum w celu zabezpieczenia hybrydowego ruchu sieciowego przeznaczonego do prywatnych adresów IP, usługi Azure PaaS i Internetu. Za pomocą usługi Azure Firewall Manager można kontrolować dostęp do sieci w sieci hybrydowej przy użyciu zasad definiujących dozwolony i odrzucony ruch sieciowy.

Firewall Manager obsługuje również zabezpieczoną architekturę centrum wirtualnego. Aby porównać zabezpieczone typy architektury centrum wirtualnego i sieci wirtualnej, zobacz [Jakie są opcje architektury usługi Azure Firewall Manager?](vhubs-and-vnets.md)

W tym samouczku zostaną utworzone trzy sieci wirtualne:

- **VNet-Hub** — w tej sieci wirtualnej znajduje się zapora.
- **VNet-Spoke** — sieć wirtualna będąca szprychą reprezentuje pakiet roboczy na platformie Azure.
- **VNet-Onprem** — lokalna sieć wirtualna reprezentuje sieć lokalną. W rzeczywistym wdrożeniu może być połączony przez sieć VPN lub połączenie usługi ExpressRoute. Dla ułatwienia w tym samouczku zostanie wykorzystane połączenie za pośrednictwem bramy VPN Gateway, a do reprezentowania sieci lokalnej zostanie wykorzystana sieć wirtualna zlokalizowana na platformie Azure.

![Sieć hybrydowa](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zasad zapory
> * Tworzenie sieci wirtualnych
> * Konfigurowanie i wdrażanie zapory
> * Tworzenie i łączenie bram sieci VPN
> * Komunikacja równorzędna pomiędzy sieciami wirtualnymi koncentratora i szprychy
> * Tworzenie tras
> * Tworzenie maszyn wirtualnych
> * Testowanie zapory


## <a name="prerequisites"></a>Wymagania wstępne

Sieć hybrydowa używa modelu architektury koncentratora i szprychy do kierowania ruchu między sieciami wirtualnymi platformy Azure i sieciami lokalnymi. Architektura piasty i szprychy ma następujące wymagania:

- Ustaw **AllowGatewayTransit** podczas równania usługi VNet-Hub do sieci wirtualnej-Spoke. W architekturze sieci koncentratora i szprychy tranzyt bramy umożliwia sieciom wirtualnym szprychy udostępnianie bramy sieci VPN w centrum zamiast wdrażania bram sieci VPN w każdej sieci wirtualnej szprychy. 

   Ponadto trasy do sieci wirtualnych połączonych z bramą lub sieci lokalnych będą automatycznie propagowane do tabel routingu dla równorzędnych sieci wirtualnych przy użyciu przesyłania bramy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie przesyłania bramy sieci VPN do komunikacji równorzędnej w sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Ustaw **UseRemoteGateways** podczas równorzędnej sieci wirtualnej-spoke do sieci wirtualnej-Hub. Jeśli **UseRemoteGateways** jest ustawiona i **AllowGatewayTransit** na zdalnej komunikacji równorzędnej jest również ustawiona, szprychowa sieć wirtualna używa bram zdalnej sieci wirtualnej do przesyłania.
- Aby rozsyłać ruch podsieci szprychowej przez zaporę koncentratora, potrzebna jest trasa zdefiniowana przez użytkownika (UDR), która wskazuje zaporę z wyłączonym ustawieniem **propagacji bramy bramy sieci wirtualnej.** Ta opcja zapobiega dystrybucji trasy do podsieci szprychy. Zapobiega to konfliktowi wyuczonych tras z twoim UDR.
- Skonfiguruj UDR w podsieci bramy koncentratora, który wskazuje adres IP zapory jako następny przeskok do sieci szprych. W podsieci usługi Azure Firewall nie jest wymagana trasa zdefiniowana przez użytkownika, ponieważ uzyskuje ona informacje o trasach na podstawie protokołu BGP.

Zobacz sekcję [Tworzenie tras](#create-the-routes) w tym samouczku, aby zobaczyć, jak tworzone są te trasy.

>[!NOTE]
>Zapora platformy Azure musi mieć bezpośrednią łączność z Internetem. Jeśli azurefirewallsubnet uczy się domyślnej trasy do sieci lokalnej za pośrednictwem protokołu BGP, należy zastąpić to z 0.0.0.0/0 UDR z **NextHopType** wartość ustawiona jako **Internet,** aby utrzymać bezpośrednią łączność z Internetem.
>
>Zaporę platformy Azure można skonfigurować do obsługi wymuszonego tunelowania. Aby uzyskać więcej informacji, zobacz [Azure Firewall wymuszone tunelowanie](../firewall/forced-tunneling.md).

>[!NOTE]
>Ruch między wirtualnymi sieciami równorzędnymi połączonymi bezpośrednio jest kierowany bezpośrednio nawet wtedy, gdy trasa zdefiniowana przez użytkownika wskazuje usługę Azure Firewall jako bramę domyślną. Aby w tym scenariuszu wysyłać ruch między podsieciami do zapory, trasa zdefiniowana przez użytkownika musi jawnie zawierać prefiks podsieci docelowej w obu podsieciach.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-firewall-policy"></a>Tworzenie zasad zapory

1. Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).
2. Na pasku wyszukiwania portalu Azure wpisz **Menedżera zapór i** naciśnij klawisz **Enter**.
3. Na stronie Usługi Azure Firewall Manager wybierz pozycję **Wyświetl zasady zapory platformy Azure**.

   ![Zasady zapory sieciowej](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Wybierz **pozycję Utwórz zasady zapory platformy Azure**.
1. Wybierz subskrypcję, a dla grupy zasobów wybierz pozycję **Utwórz nowy** i utwórz grupę zasobów o nazwie **FW-Hybrid-Test**.
2. W przypadku nazwy zasad wpisz **Pol-Net01**.
3. W obszarze Region wybierz pozycję **Wschodnie stany USA**.
4. Wybierz **dalej:Reguły**.
5. Wybierz **pozycję Dodaj kolekcję reguł**.
6. Dla **Name**, wpisz **RCNet01**.
7. W obszarze **Typ kolekcji reguł**wybierz pozycję **Sieć**.
8. Dla **priorytetu**, typ **100**.
9. W polu **Akcja** wybierz opcję **Zezwalaj**.
10. W obszarze **Reguły**, dla **Name**, wpisz **AllowWeb**.
11. W przypadku **adresów źródłowych**wpisz **192.168.1.0/24**.
12. W polu **Protokół** wybierz **TCP**.
13. W przypadku **portów docelowych**wpisz **80**.
14. W przypadku **opcji Typ miejsca docelowego**wybierz pozycję **Adres IP**.
15. Dla **miejsca docelowego**wpisz **10.6.0.0/16**.
16. W następnym wierszu reguły wprowadź następujące informacje:
 
    Nazwa: typ **AllowRDP**<br>
    Źródłowy adres IP: typ **192.168.1.0/24**.<br>
    Protokół, wybierz **TCP**<br>
    Porty docelowe, typ **3389**<br>
    Typ miejsca docelowego, wybierz **adres IP**<br>
    Dla miejsca docelowego wpisz **10.6.0.0/16**

1. Wybierz pozycję **Dodaj**.
2. Wybierz **pozycję Recenzja + Utwórz**.
3. Przejrzyj szczegóły, a następnie wybierz pozycję **Utwórz**.

## <a name="create-the-firewall-hub-virtual-network"></a>Tworzenie sieci wirtualnej koncentratora zapory

> [!NOTE]
> Rozmiar podsieci AzureFirewallSubnet to /26. Aby uzyskać więcej informacji na temat rozmiaru podsieci, zobacz [Często zadawane pytania dotyczące zapory platformy Azure](../firewall/firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

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

Po wdrożeniu sieci wirtualnej utwórz drugą podsieć dla bramy.

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
6. W obszarze **Lokalizacja**wybierz pozycję **Wschodnie stany USA**.
7. Zaakceptuj inne ustawienia domyślne, a następnie wybierz pozycję **Utwórz**.

## <a name="configure-and-deploy-the-firewall"></a>Konfigurowanie i wdrażanie zapory

Gdy zasady zabezpieczeń są skojarzone z koncentratorem, jest on określany jako *sieć wirtualna koncentratora*.

Przekonwertuj sieć **wirtualną VNet-Hub** na *sieć wirtualną centrum* i zabezpiecz ją za pomocą zapory azure.

1. Na pasku wyszukiwania portalu Azure wpisz **Menedżera zapór i** naciśnij klawisz **Enter**.
3. Na stronie Azure Firewall Manager w obszarze **Dodawanie zabezpieczeń do sieci wirtualnych**wybierz pozycję Wyświetl sieci **wirtualne centrum**.
4. Wybierz **opcję Konwertuj sieci wirtualne**.
5. Wybierz **centrum sieci wirtualnej,** a następnie wybierz pozycję **Dalej: Zapora azure**.
6. W przypadku **zasad zapory**wybierz **Pol-Net01**.
7. Wybierz **przycisk Dalej " Recenzja + potwierdź**
8. Przejrzyj szczegóły, a następnie wybierz pozycję **Potwierdź**.


   Trwa to kilka minut, aby wdrożyć.
7. Po zakończeniu wdrażania przejdź do grupy zasobów **FW-Hybrid-Test** i wybierz zaporę.
9. Zanotuj prywatny adres **IP zapory** na stronie **Przegląd.** Użyjesz go później podczas tworzenia trasy domyślnej.

## <a name="create-and-connect-the-vpn-gateways"></a>Tworzenie i łączenie bram sieci VPN

Sieć wirtualna koncentratora i lokalna sieć wirtualna są połączone za pośrednictwem bram sieci VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Tworzenie bramy sieci VPN dla sieci wirtualnej koncentratora

Teraz utwórz bramę sieci VPN dla sieci wirtualnej koncentratora. Konfiguracje połączeń między sieciami wymagają zastosowania wartości RouteBased obiektu VpnType. Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy sieci VPN.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym wyszukiwania wpisz **bramę sieci wirtualnej** i naciśnij klawisz **Enter**.
3. Wybierz **wirtualną bramę sieciową**i wybierz pozycję **Utwórz**.
4. W przypadku **nazwy**wpisz **GW-hub**.
5. W obszarze **Region**wybierz **(USA) wschodnie stany USA**.
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
5. W obszarze **Region**wybierz **(USA) wschodnie stany USA**.
6. W przypadku **typu bramy**wybierz pozycję **VPN**.
7. W przypadku **typu VPN**wybierz opcję Oparte **na marszrutach**.
8. W przypadku **jednostki SKU**wybierz opcję **Podstawowe**.
9. W przypadku **sieci wirtualnej**wybierz pozycję **VNet-Onprem**.
10. W przypadku **publicznego adresu IP**wybierz **Użyj istniejącego*i wybierz **vNet-Onprem-GW-pip** dla nazwy.
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

![Połączenia bramy](media/secure-hybrid-network/gateway-connections.png)

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
8. W obszarze **Lokalizacja**wybierz **(USA) wschodnie stany USA).**
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
4. W **obszarze Sieć wirtualna**wybierz **koncentrator sieci wirtualnej**.
5. W **obszarze Podsieć**wybierz pozycję **GatewaySubnet**.
6. Kliknij przycisk **OK**.

Teraz utwórz trasę domyślną z podsieci szprychowej.

1. Na stronie głównej portalu platformy Azure wybierz pozycję **Utwórz zasób**.
2. W polu tekstowym wyszukiwania wpisz **tabelę tras** i naciśnij klawisz **Enter**.
3. Wybierz **pozycję Tabela Marszruta**.
5. Wybierz **pozycję Utwórz**.
6. Aby uzyskać nazwę, wpisz **UDR-DG**.
7. Wybierz **FW-Hybrid-Test** dla grupy zasobów.
8. W obszarze **Lokalizacja**wybierz **(USA) wschodnie stany USA).**
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
4. W **obszarze Sieć wirtualna**wybierz opcję **VNet-spoke**.
5. W **obszarze Podsieć**wybierz pozycję **Obciążenie SN**.
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
    - **(USA)** - *Wschodnie stany USA).*
    - **Nazwa użytkownika**: *azureuser*.
    - **Hasło:** wpisz hasło

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
    - **(USA)** - *Wschodnie stany USA).*
    - **Nazwa użytkownika**: *azureuser*.
    - **Hasło:** wpisz hasło.

4. Wybierz pozycję **Dalej: Dyski**.
5. Zaakceptuj ustawienia domyślne i wybierz **pozycję Dalej:Sieć**.
6. Wybierz **sieć wirtualną-Onprem** dla sieci wirtualnej i sprawdź, czy podsieć jest **SN-Corp**.
7. W przypadku **publicznych portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty**, a następnie wybierz pozycję **RDP (3389)**
8. Wybierz **dalej:Zarządzanie**.
9. W przypadku **diagnostyki rozruchu**wybierz opcję **Wył.**
10. Wybierz **pozycję Recenzja+Utwórz**, przejrzyj ustawienia na stronie podsumowania, a następnie wybierz pozycję **Utwórz**.

## <a name="test-the-firewall"></a>Testowanie zapory

1. Najpierw należy zwrócić uwagę na prywatny adres IP maszyny wirtualnej VM-Spoke-01 na stronie Omówienie maszyny VM-Spoke-01.

2. W witrynie Azure Portal połącz się z maszyną wirtualną **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Otwórz przeglądarkę internetową na maszynie wirtualnej **VM-Onprem**, a następnie przejdź do lokalizacji http://\<VM-spoke-01 private IP\>.

   Powinna zostać wyświetlona strona internetowa **VM-spoke-01:** ![VM-Spoke-01](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Z maszyny wirtualnej **VM-Onprem** otwórz pulpit zdalny do **VM-spoke-01** pod prywatnym adresem IP.

   Połączenie powinno zakończyć się pomyślnie i powinno być możliwe zalogowanie się.

Więc teraz masz zweryfikowane, że reguły zapory działają:

<!---- You can ping the server on the spoke VNet.--->
- Możesz przeglądać serwer internetowy w sieci wirtualnej będącej szprychą.
- Możesz nawiązać połączenie z serwerem w sieci wirtualnej będącej szprychą, korzystając z protokołu RDP.

Następnie zmień ustawienie akcji kolekcji reguł sieci zapory na **Odmów**, aby sprawdzić, czy reguły zapory działają zgodnie z oczekiwaniami.

1. Otwórz grupę zasobów **FW-Hybrid-Test** i wybierz zasadę zapory **Pol-Net01.**
2. W obszarze **Ustawienia**wybierz pozycję **Reguły**.
3. W obszarze **Reguły sieci**wybierz kolekcję reguł **RCNet01,** wybierz elipsy (...), a następnie wybierz pozycję **Edytuj**.
4. W przypadku **akcji Zbieranie reguł**wybierz pozycję **Odmów**.
5. Wybierz **pozycję Zapisz**.

Zamknij wszystkie istniejące pulpity zdalne i przeglądarki w **VM-Onprem** przed przetestowaniem zmienionych reguł. Po zakończeniu aktualizacji kolekcji reguł uruchom testy ponownie. Tym razem wszystkie powinny zakończyć się niepowodzeniem.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz zachować zasoby zapory na potrzeby kolejnego samouczka, a jeśli nie będą już potrzebne, możesz usunąć grupę zasobów **FW-Hybrid-Test**, aby usunąć wszystkie zasoby związane z zaporą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Zabezpiecz wirtualną sieć WAN przy użyciu podglądu usługi Azure Firewall Manager](secure-cloud-network.md)