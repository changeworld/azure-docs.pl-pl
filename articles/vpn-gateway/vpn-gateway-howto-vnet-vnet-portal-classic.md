---
title: 'Tworzenie połączenia między sieciami wirtualnymi: klasyczne: Witryna Azure portal'
description: Łączenie sieci wirtualnych platformy Azure przy użyciu programu PowerShell i witryny Azure portal.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/12/2020
ms.author: cherylmc
ms.openlocfilehash: 63c6329ad62289cd127902c1438073b28fc8683e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201853"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną (klasyczna)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ten artykuł ułatwia tworzenie połączenia bramy sieci VPN między sieciami wirtualnymi. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji. Kroki opisane w tym artykule dotyczą klasycznego modelu wdrażania i witryny Azure Portal. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Portal Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Powershell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Łączenie różnych modeli wdrażania — witryna Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Łączenie różnych modeli wdrażania — program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagram łączności sieci wirtualnej do sieci wirtualnej](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)



## <a name="about-vnet-to-vnet-connections"></a>Informacje o połączeniach między sieciami wirtualnymi

Łączenie sieci wirtualnej z inną siecią wirtualną (sieć wirtualna do sieci wirtualnej) w klasycznym modelu wdrażania przy użyciu bramy sieci VPN jest podobne do łączenia sieci wirtualnej z lokalną lokalizacją lokacji. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE.

Sieci wirtualne, które łączysz może być w różnych subskrypcji i różnych regionach. Można połączyć komunikację sieci wirtualnej z siecią wirtualną z konfiguracjami wielu lokacji. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi.

![Połączenia sieci wirtualnej z siecią wirtualną](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Dlaczego łączy się sieci wirtualne?

Sieci wirtualne można łączyć z następujących powodów:

* **Niezależna od regionu nadmiarowość i obecność geograficzna**

  * Można tworzyć własne replikacje geograficzne lub przeprowadzać synchronizację z bezpiecznym połączeniem bez przechodzenia przez punkty końcowe dostępne z Internetu.
  * Dzięki modułowi równoważenia obciążenia platformy Azure i technologii klastrowania firmy Microsoft lub innej firmy można skonfigurować wysoce dostępne obciążenie z nadmiarowością geograficzną w wielu regionach platformy Azure. Istotnym przykładem jest konfiguracja ustawienia SQL Zawsze włączone, w przypadku którego grupy dostępności rozciągają się na wiele regionów świadczenia usługi Azure.
* **Regionalne aplikacje wielopoziomowe z silną granicą izolacji**

  * W tym samym regionie można skonfigurować aplikacje wielowarstwowe z wieloma sieciami wirtualnymi połączonymi z silną izolacją i bezpieczną komunikacją międzywarstwową.
* **Subskrypcja krzyżowa, komunikacja między organizacjami na platformie Azure**

  * Jeśli masz wiele subskrypcji platformy Azure, można połączyć obciążeń z różnych subskrypcji razem bezpiecznie między sieciami wirtualnymi.
  * W przypadku przedsiębiorstw lub usługodawców można włączyć komunikację międzyorganizacjową za pomocą bezpiecznej technologii sieci VPN na platformie Azure.

Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Zagadnienia dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu.

### <a name="working-with-azure-powershell"></a><a name="powershell"></a>Praca z programem Azure PowerShell

Używamy portalu dla większości kroków, ale należy użyć programu PowerShell do tworzenia połączeń między sieciami wirtualnymi. Nie można utworzyć połączeń przy użyciu witryny Azure portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="step-1---plan-your-ip-address-ranges"></a><a name="plan"></a>Krok 1 — planowanie zakresów adresów IP

Ważne jest, aby zdecydować zakresy, które będą używane do konfigurowania sieci wirtualnych. W przypadku tej konfiguracji należy się upewnić, że żaden z zakresów sieci wirtualnej nie pokrywa się ze sobą ani z dowolną siecią lokalną, z którą się łączą.

W poniższej tabeli przedstawiono przykład definiowania sieci wirtualnych. Używaj zakresów tylko jako wytycznych. Zapisz zakresy dla sieci wirtualnych. Te informacje są potrzebne do późniejszych kroków.

**Przykład**

| Virtual Network | Przestrzeń adresowa | Region | Łączy się z lokalną lokacją sieciową |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Wschodnie stany USA |VNet4Local (Wirtualna 4Local)<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local (Wirtualna 1lokal)<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="step-2---create-the-virtual-networks"></a><a name="vnetvalues"></a>Krok 2 - Tworzenie sieci wirtualnych

Utwórz dwie sieci wirtualne w [witrynie Azure portal](https://portal.azure.com). Aby zapoznać się z instrukcjami tworzenia klasycznych sieci wirtualnych, zobacz [Tworzenie klasycznej sieci wirtualnej](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Podczas tworzenia klasycznej sieci wirtualnej za pomocą portalu należy przejść do strony sieci wirtualnej, wykonując następujące kroki, w przeciwnym razie opcja utworzenia klasycznej sieci wirtualnej nie jest wyświetlana:

1. Kliknij "+", aby otworzyć stronę "Nowy".
2. W polu "Wyszukaj w rynku" wpisz "Sieć wirtualna". Jeśli zamiast tego wybierz pozycję Sieć -> sieć wirtualna, nie zostanie dostępna opcja utworzenia klasycznej sieci wirtualnej.
3. Znajdź "Sieć wirtualną" z listy zwracanych i kliknij ją, aby otworzyć stronę sieć wirtualna. 
4. Na stronie sieci wirtualnej wybierz opcję "Classic", aby utworzyć klasyczną sieć wirtualną. 

Jeśli używasz tego artykułu jako ćwiczenia, możesz użyć następujących przykładowych wartości:

**Wartości dla TestVNet1**

Nazwa: TestVNet1<br>
Przestrzeń adresowa: 10.11.0.0/16, 10.12.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.11.0.1/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: Wschodnie stany USA<br>
GatewaySubnet: 10.11.1.0/27

**Wartości dla TestVNet4**

Nazwa: TestVNet4<br>
Przestrzeń adresowa: 10.41.0.0/16, 10.42.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.41.0.1/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: West US<br>
GatewaySubnet: 10.41.1.0/27

**Podczas tworzenia sieci wirtualnych należy pamiętać o następujących ustawieniach:**

* **Wirtualne przestrzenie adresowe sieci** — na stronie Wirtualne przestrzenie adresowe sieci określ zakres adresów, który ma być używany dla sieci wirtualnej. Są to dynamiczne adresy IP, które zostaną przypisane do maszyn wirtualnych i innych wystąpień roli, które można wdrożyć w tej sieci wirtualnej.<br>Wybrane przestrzenie adresowe nie mogą pokrywać się z przestrzeniami adresowymi dla innych sieci wirtualnych lub lokalizacji lokalnych, z którymi będzie się łączyć ta sieci wirtualnej.

* **Lokalizacja** — podczas tworzenia sieci wirtualnej, należy skojarzyć go z lokalizacji platformy Azure (regionu). Na przykład jeśli chcesz, aby maszyny wirtualne, które są wdrażane w sieci wirtualnej, były fizycznie zlokalizowane w zachodnie stany USA, wybierz tę lokalizację. Po utworzeniu nie można zmienić lokalizacji skojarzonej z siecią wirtualną.

**Po utworzeniu sieci wirtualnych można dodać następujące ustawienia:**

* **Przestrzeń adresowa** — dodatkowa przestrzeń adresowa nie jest wymagana dla tej konfiguracji, ale można dodać dodatkową przestrzeń adresową po utworzeniu sieci wirtualnej.

* **Podsieci** — dodatkowe podsieci nie są wymagane dla tej konfiguracji, ale można mieć maszyny wirtualne w podsieci, która jest oddzielona od innych wystąpień roli.

* **Serwery DNS** — wprowadź nazwę serwera DNS i adres IP. To ustawienie nie powoduje jednak utworzenia serwera DNS. Umożliwia natomiast określenie serwerów DNS, które mają być używane do rozpoznawania nazw dla tej sieci wirtualnej.

W tej sekcji można skonfigurować typ połączenia, lokację lokalną i utworzyć bramę.

## <a name="step-3---configure-the-local-site"></a><a name="localsite"></a>Krok 3 - Konfigurowanie witryny lokalnej

Platforma Azure używa ustawień określonych w każdej lokacji sieci lokalnej, aby określić sposób kierowania ruchu między sieciami wirtualnymi. Każda sieć wirtualna musi wskazywać odpowiednią sieć lokalną, do której chcesz kierować ruch. Można określić nazwę, której chcesz użyć do odwoływania się do każdej lokacji sieci lokalnej. Najlepiej użyć czegoś opisowego.

Na przykład TestVNet1 łączy się z lokacją sieci lokalnej, która została utworzona o nazwie "VNet4Local". Ustawienia VNet4Local zawierają prefiksy adresu dla TestVNet4.

Lokacja lokalna dla każdej sieci wirtualnej jest drugą siecią wirtualną. W naszej konfiguracji używane są następujące przykładowe wartości:

| Virtual Network | Przestrzeń adresowa | Region | Łączy się z lokalną lokacją sieciową |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Wschodnie stany USA |VNet4Local (Wirtualna 4Local)<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local (Wirtualna 1lokal)<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Zlokalizuj TestVNet1 w witrynie Azure portal. W sekcji **Połączenia sieci VPN** na stronie kliknij pozycję **Brama**.

    ![Brak bramy](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na stronie **Nowe połączenie sieci VPN** wybierz pozycję **Lokacja lokacja**.
3. Kliknij **pozycję Witryna lokalna,** aby otworzyć stronę Witryna lokalna i skonfigurować ustawienia.
4. Na stronie **Witryna lokalna** nazwij witrynę lokalną. W naszym przykładzie nazwa lokalnego witryny "VNet4Local".
5. W przypadku **adresu IP bramy sieci VPN**można użyć dowolnego adresu IP, o ile jest on w prawidłowym formacie. Zazwyczaj należy użyć rzeczywistego zewnętrznego adresu IP dla urządzenia sieci VPN. Jednak w przypadku klasycznej konfiguracji sieci wirtualnej do sieci wirtualnej należy użyć publicznego adresu IP przypisanego do bramy sieci wirtualnej. Biorąc pod uwagę, że brama sieci wirtualnej nie została jeszcze utworzona, należy określić dowolny prawidłowy publiczny adres IP jako symbol zastępczy.<br>Nie zostawiaj tego pustego pola — nie jest to opcjonalne dla tej konfiguracji. W późniejszym kroku należy wrócić do tych ustawień i skonfigurować je z odpowiednimi adresami IP bramy sieci wirtualnej po wygenerowaniu przez platformę Azure.
6. W przypadku **przestrzeni adresowej klienta**należy użyć przestrzeni adresowej drugiej sieci wirtualnej. Zapoznaj się z przykładem planowania. Kliknij **przycisk OK,** aby zapisać ustawienia i powrócić do strony **Nowe połączenie sieci VPN.**

    ![strona lokalna](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="step-4---create-the-virtual-network-gateway"></a><a name="gw"></a>Krok 4 - Tworzenie bramy sieci wirtualnej

Każda sieć wirtualna musi mieć bramę sieci wirtualnej. Brama sieci wirtualnej kieruje i szyfruje ruch.

1. Na stronie **Nowe połączenie VPN** zaznacz pole wyboru **Utwórz bramę natychmiast**.
2. Kliknij **pozycję Podsieć, rozmiar i typ marszruty**. Na stronie **Konfiguracja bramy** kliknij pozycję **Podsieć**.
3. Nazwa podsieci bramy jest wypełniana automatycznie o wymaganej nazwie "GatewaySubnet". **Zakres adresów** zawiera adresy IP przydzielone do usług bramy sieci VPN. Niektóre konfiguracje zezwalają na podsieć bramy /29, ale najlepiej użyć /28 lub /27, aby pomieścić przyszłe konfiguracje, które mogą wymagać większej liczby adresów IP dla usług bramy. W naszych ustawieniach przykładu używamy 10.11.1.0/27. Dostosuj przestrzeń adresową, a następnie kliknij przycisk **OK**.
4. Skonfiguruj **rozmiar bramy**. To ustawienie odnosi się do jednostki [SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Skonfiguruj **typ marszruty**. Typ routingu dla tej konfiguracji musi być **dynamiczny**. Nie można później zmienić typu routingu, chyba że zburzysz bramę i utworzysz nową.
6. Kliknij przycisk **OK**.
7. Na stronie **Nowe połączenie sieci VPN** kliknij przycisk **OK,** aby rozpocząć tworzenie bramy sieci wirtualnej. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

## <a name="step-5---configure-testvnet4-settings"></a><a name="vnet4settings"></a>Krok 5 - Konfigurowanie ustawień TestVNet4

Powtórz kroki, aby [utworzyć lokację lokalną](#localsite) i [utworzyć bramę sieci wirtualnej,](#gw) aby skonfigurować TestVNet4, zastępując wartości, gdy jest to konieczne. Jeśli robisz to jako ćwiczenie, użyj [przykładowych wartości](#vnetvalues).

## <a name="step-6---update-the-local-sites"></a><a name="updatelocal"></a>Krok 6 - Aktualizacja lokalnych witryn

Po utworzeniu bram sieci wirtualnej dla obu sieci wirtualnych należy dostosować wartości **adresów IP bramy sieci VPN** lokacji lokalnych.

|Nazwa sieci wirtualnej|Połączona witryna|Adres IP bramy|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local (Wirtualna 4Local)|Adres IP bramy sieci VPN dla TestVNet4|
|TestVNet4|VNet1Local (Wirtualna 1lokal)|Adres IP bramy sieci VPN dla TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Część 1 - Pobierz publiczny adres IP bramy sieci wirtualnej

1. Znajdź swoją sieć wirtualną w witrynie Azure portal.
2. Kliknij, aby otworzyć stronę **Przegląd sieci wirtualnej.** Na stronie w **połączeniach sieci VPN**można wyświetlić adres IP bramy sieci wirtualnej.

   ![Publiczny adres IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Skopiuj adres IP. Użyjesz go w następnej sekcji.
4. Powtórz te kroki dla TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Część 2 - Modyfikowanie lokalnych witryn

1. Znajdź swoją sieć wirtualną w witrynie Azure portal.
2. Na stronie **Omówienie sieci** wirtualnej kliknij witrynę lokalną.

   ![Utworzono witrynę lokalną](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na stronie **Połączenia sieci VPN między lokacjami** kliknij nazwę witryny lokalnej, którą chcesz zmodyfikować.

   ![Otwórz witrynę lokalną](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kliknij **witrynę lokalną,** którą chcesz zmodyfikować.

   ![modyfikowanie witryny](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Zaktualizuj **adres IP bramy sieci VPN** i kliknij przycisk **OK,** aby zapisać ustawienia.

   ![adres IP bramy](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zamknij pozostałe strony.
7. Powtórz te kroki dla TestVNet4.

## <a name="step-7---retrieve-values-from-the-network-configuration-file"></a><a name="getvalues"></a>Krok 7 - Pobieranie wartości z pliku konfiguracji sieci

Podczas tworzenia klasycznych sieci wirtualnych w witrynie Azure portal, nazwa, która jest wyświetlane nie jest pełna nazwa, która jest używana dla programu PowerShell. Na przykład sieć wirtualna, która wydaje się być nazywana **TestVNet1** w portalu, może mieć znacznie dłuższą nazwę w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: **Group ClassicRG TestVNet1**. Podczas tworzenia połączeń należy używać wartości widocznych w pliku konfiguracji sieci.

W poniższych krokach połączysz się z kontem platformy Azure i pobierzesz i wyświetlisz plik konfiguracji sieci, aby uzyskać wartości, które są wymagane dla połączeń.

1. Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Service Management (SM). Aby uzyskać więcej informacji, zobacz [Praca z programem Azure PowerShell](#powershell).

2. Otwórz konsolę programu PowerShell z podwyższonymi prawami. Skorzystaj z poniższych przykładów, aby połączyć się. Polecenia te należy uruchomić lokalnie przy użyciu modułu zarządzania usługami programu PowerShell. Aby przełączyć się do zarządzania usługami, użyj tego polecenia:

   ```powershell
   azure config mode asm
   ```
3. Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```powershell
   Add-AzureAccount
   ```
4. Sprawdź subskrypcje dostępne na koncie.

   ```powershell
   Get-AzureSubscription
   ```
5. Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```
6. Eksportuj i wyświetlaj plik konfiguracji sieci. Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie plik konfiguracji sieci jest eksportowany do **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
7. Otwórz plik za pomocą edytora tekstu i wyświetl nazwy sieci wirtualnych i witryn. Te nazwy będą nazwami używanymi podczas tworzenia połączeń.<br>Nazwy sieci wirtualnych są wyświetlane jako **nazwa VirtualNetworkSite =**<br>Nazwy witryn są wyświetlane jako **Nazwa LocalNetworkSiteRef =**

## <a name="step-8---create-the-vpn-gateway-connections"></a><a name="createconnections"></a>Krok 8 - Tworzenie połączeń bramy sieci VPN

Po wykonaniu wszystkich poprzednich kroków można ustawić klucze wstępnie udostępnione IPsec/IKE i utworzyć połączenie. Ten zestaw kroków używa programu PowerShell. Nie można skonfigurować połączeń sieci wirtualnej do sieci wirtualnej dla klasycznego modelu wdrażania w witrynie Azure Portal.

W przykładach należy zauważyć, że klucz udostępniony jest dokładnie taki sam. Klucz udostępniony musi zawsze być zgodny. Pamiętaj, aby zastąpić wartości w tych przykładach dokładnymi nazwami sieci wirtualnych i witryn sieci lokalnych.

1. Utwórz połączenie z sieci wirtualnej TestVNet1 do sieci wirtualnej TestVNet4.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
   ```
2. Utwórz połączenie z sieci wirtualnej TestVNet4 do sieci wirtualnej TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
   ```
3. Poczekaj na zainicjowanie połączeń. Po zainicjowaniu bramy stan jest "Pomyślny".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="vnet-to-vnet-considerations-for-classic-vnets"></a><a name="faq"></a>Zagadnienia dotyczące sieci wirtualnej dla klasycznych sieci wirtualnych
* Sieci wirtualne mogą być w tej samej lub różnych subskrypcji.
* Sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (lokalizacjach) świadczenia usługi Azure.
* Usługa w chmurze lub punkt końcowy równoważenia obciążenia nie może obejmować sieci wirtualnych, nawet jeśli są one połączone ze sobą.
* Łączenie wielu sieci wirtualnych nie wymaga żadnych urządzeń sieci VPN.
* Sieć wirtualna do sieci wirtualnej obsługuje łączenie sieci wirtualnych platformy Azure. Nie obsługuje łączenia maszyn wirtualnych lub usług w chmurze, które nie są wdrażane w sieci wirtualnej.
* Sieci wirtualnej do sieci wirtualnej wymaga dynamicznych bram routingu. Bramy routingu statycznego platformy Azure nie są obsługiwane.
* Połączenie sieci wirtualnej może być używane równocześnie z sieciami VPN obejmującymi wiele lokacji. Istnieje maksymalnie 10 tuneli sieci VPN dla bramy sieci vpn sieci wirtualnej łączącej się z innymi sieciami wirtualnymi lub lokacjami lokalnymi.
* Przestrzenie adresów sieci wirtualnych i lokalnych witryn sieci obejmujących lokalizacje w obrębie organizacji nie mogą się nakładać. Nakładające się przestrzenie adresowe spowoduje, że tworzenie sieci wirtualnych lub przekazywanie plików konfiguracyjnych netcfg zakończy się niepowodzeniem.
* Nadmiarowe tunele między dwiema sieciami wirtualnymi nie są obsługiwane.
* Wszystkie tunele sieci VPN dla sieci wirtualnej, w tym sieci VPN P2S, współużytkują dostępną przepustowość bramy sieci VPN i tę samą bramę sieci VPN na platformie Azure.
* Ruch sieci wirtualnej do sieci wirtualnej przemieszcza się przez szkielet platformy Azure.

## <a name="next-steps"></a>Następne kroki
Sprawdź połączenia. Zobacz [Sprawdzanie połączenia bramy sieci VPN](vpn-gateway-verify-connection-resource-manager.md).
