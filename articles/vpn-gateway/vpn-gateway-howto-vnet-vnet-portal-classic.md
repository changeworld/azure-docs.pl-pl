---
title: 'Tworzenie połączenia między sieciami wirtualnymi: klasycznym: portalu Azure | Dokumentacja firmy Microsoft'
description: Połączyć sieci wirtualnych platformy Azure przy użyciu programu PowerShell i portalu Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: d9766afefa793baf66ea5218843f06031b1b364c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31601060"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurowanie połączenia do wirtualnymi (klasyczne)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ten artykuł pomaga utworzyć połączenie bramy sieci VPN między sieciami wirtualnymi. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji. Kroki opisane w tym artykule dotyczą w klasycznym modelu wdrażania i portalu Azure. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Łączenie różnych modeli wdrażania — witryna Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Łączenie różnych modeli wdrażania — program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Sieć wirtualną do diagramu łączność sieci wirtualnej](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Informacje o połączeniach między sieciami wirtualnymi

Połączenie wirtualnej sieci do innej sieci wirtualnej (aby wirtualnymi) w klasycznym modelu wdrażania przy użyciu bramy sieci VPN jest podobny do łączenia sieci wirtualnej do lokalizacji lokalnej. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE.

Sieci wirtualne, gdy nawiązujesz połączenie może być w różnych regionach i różnych subskrypcji. Można połączyć sieć wirtualną do komunikacji między sieciami wirtualnymi w przypadku konfiguracji z wieloma lokacjami. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi.

![Sieć wirtualną do połączeń sieci wirtualnej](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Dlaczego łączy się sieci wirtualne?

Sieci wirtualne można łączyć z następujących powodów:

* **Niezależna od regionu nadmiarowość i obecność geograficzna**

  * Można tworzyć własne replikacje geograficzne lub przeprowadzać synchronizację z bezpiecznym połączeniem bez przechodzenia przez punkty końcowe dostępne z Internetu.
  * Z usługi równoważenia obciążenia Azure i firmy Microsoft lub innych technologii klastrowania można skonfigurować obciążenia wysokiej dostępności z nadmiarowość geograficzna w różnych regionach platformy Azure. Istotnym przykładem jest konfiguracja ustawienia SQL Zawsze włączone, w przypadku którego grupy dostępności rozciągają się na wiele regionów świadczenia usługi Azure.
* **Regionalne aplikacje wielowarstwowe z granicą silne izolacji**

  * W tym samym regionie można skonfigurować aplikacje wielowarstwowe z wielu sieci wirtualnych połączone razem z silną izolacji i zapewnienia bezpiecznej komunikacji między warstwy.
* **Krzyżowe subskrypcji i komunikacji między organizacji na platformie Azure**

  * Jeśli masz wiele subskrypcji Azure, możesz połączyć obciążeń z różnych subskrypcji razem bezpiecznie między sieciami wirtualnymi.
  * Dla przedsiębiorstw i dostawców usług można włączyć komunikację między organizacji bezpiecznego technologii sieci VPN w systemie Azure.

Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Zagadnienia dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem tego ćwiczenia, Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell Azure usługi zarządzania (ko). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Większość kroków możemy korzystać z portalu, ale trzeba użyć programu PowerShell, aby utworzyć połączeń między sieciami wirtualnymi. Nie można utworzyć połączenia przy użyciu portalu Azure.

## <a name="plan"></a>Krok 1 — Planowanie zakresów adresów IP

Należy określić zakresy, które będą używane do konfigurowania sieci wirtualne. W przypadku tej konfiguracji, należy się upewnić, że żaden z zakresów w Twojej sieci wirtualnej nakłada się ze sobą lub za pomocą dowolnego z sieci lokalnej, które łączą się z.

W poniższej tabeli przedstawiono przykład sposobu definiowania Twojej sieci wirtualnych. Użyj zakresy tylko przyjąć. Zapisz zakresy dla sieci wirtualnej. Te informacje są niezbędne do wykonania kolejnych kroków.

**Przykład**

| Virtual Network | Przestrzeń adresowa | Region | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Wschodnie stany USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Krok 2 — Tworzenie sieci wirtualnych

Utwórz dwie sieci wirtualne w [portalu Azure](https://portal.azure.com). Aby uzyskać instrukcje dotyczące tworzenia klasycznych sieci wirtualnych, zobacz [utworzyć sieć wirtualną klasycznego](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Tworzenie klasycznej sieci wirtualnej za pomocą portalu, musisz przejść do strony sieci wirtualnej przy użyciu następujących kroków, w przeciwnym razie nie ma możliwość utworzenia klasycznego sieci wirtualnej:

1. Kliknij przycisk "+", aby otworzyć stronę "New".
2. W polu "Wyszukiwania portalu marketplace" typu "Sieci wirtualnej". Jeśli zamiast tego należy wybrać sieć -> Sieć wirtualna, nie otrzyma możliwość utworzenia klasycznego sieci wirtualnej.
3. Znajdź "Sieci wirtualnej" na liście zwracanych i kliknij go, aby otworzyć stronę sieci wirtualnej. 
4. Na stronie sieci wirtualnej wybierz 'Klasycznym' Tworzenie klasycznej sieci wirtualnej. 

Korzystając z tego artykułu jako wykonywania, można użyć następujących przykładowe wartości:

**Wartości TestVNet1**

Nazwa: TestVNet1<br>
Przestrzeń adresowa: 10.11.0.0/16, 10.12.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.11.0.1/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: Wschodnie stany USA<br>
GatewaySubnet: 10.11.1.0/27

**Wartości TestVNet4**

Nazwa: TestVNet4<br>
Przestrzeń adresowa: 10.41.0.0/16, 10.42.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.41.0.1/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: West US<br>
GatewaySubnet: 10.41.1.0/27

**Podczas tworzenia Twojej sieci wirtualnych, należy przestrzegać następujących ustawień:**

* **Przestrzeniami adresów sieci wirtualnej** — na stronie przestrzeniami adresów sieci wirtualnej, określ zakres adresów, który ma być używany dla sieci wirtualnej. Są to dynamiczne adresy IP, które zostanie przypisane do maszyn wirtualnych i innych wystąpień roli, które wdrażasz do tej sieci wirtualnej.<br>Wybrane przestrzenie adresowe nie może nakładać się przestrzeni adresowych dla żadnej z innymi sieciami wirtualnymi lub lokalnymi lokalizacje, które ta sieć wirtualna będzie łączyć się.

* **Lokalizacja** — po utworzeniu sieci wirtualnej, należy ją skojarzyć z lokalizacji platformy Azure (regionu). Na przykład jeśli chcesz, aby sieci maszyn wirtualnych, które są wdrażane w Twojej sieci wirtualnej się znajdować w zachodnie stany USA, wybierz opcję tej lokalizacji. Nie można zmienić lokalizację skojarzone z sieci wirtualnej, po jego utworzeniu.

**Po utworzeniu Twojej sieci wirtualnych, można dodać następujące ustawienia:**

* **Przestrzeń adresowa** — dodatkową przestrzeń adresów nie jest wymagana dla tej konfiguracji, ale można dodać dodatkową przestrzeń adresów po utworzeniu sieci wirtualnej.

* **Podsieci** — dodatkowe podsieci nie są wymagane dla tej konfiguracji, ale warto mieć maszyn wirtualnych w podsieci, która jest oddzielona od innych wystąpień roli.

* **Serwery DNS** — wprowadź nazwę serwera DNS i adres IP. To ustawienie nie powoduje jednak utworzenia serwera DNS. Umożliwia natomiast określenie serwerów DNS, które mają być używane do rozpoznawania nazw dla tej sieci wirtualnej.

W tej sekcji Skonfiguruj typ połączenia lokacji lokalnej i utworzenia bramy.

## <a name="localsite"></a>Krok 3 — Konfigurowanie lokacji lokalnej

Azure używa ustawień określonych w każdej lokacji sieci lokalnej w celu określenia sposobu kierować ruchem między sieciami wirtualnymi. Chcesz kierować ruchem do odpowiednich sieci lokalnej musi wskazywać każdej sieci wirtualnej. Należy określić nazwę, który ma być używana do odwoływania się do poszczególnych lokacji sieci lokalnej. Najlepiej użyć ciągu opisowy.

Na przykład TestVNet1 łączy się z lokacją sieci lokalnej, który utworzono o nazwie "VNet4Local". Ustawienia dla VNet4Local zawiera prefiksy adresów TestVNet4.

Lokalnej lokacji dla każdej sieci wirtualnej jest innych sieci wirtualnej. Następujące przykładowe wartości są używane w naszych konfiguracji:

| Virtual Network | Przestrzeń adresowa | Region | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Wschodnie stany USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Znajdź TestVNet1 w portalu Azure. W **połączeń sieci VPN** części strony, kliknij przycisk **bramy**.

    ![Brak bramy](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na **nowego połączenia sieci VPN** wybierz pozycję **lokacja-lokacja**.
3. Kliknij przycisk **lokacji lokalnej** aby otworzyć stronę lokacji lokalnej i skonfiguruj ustawienia.
4. Na **lokacji lokalnej** pozycję nazwy lokalnej witrynie. W naszym przykładzie nazywamy "VNet4Local" w lokacji lokalnej.
5. Aby uzyskać **adres IP bramy sieci VPN**, można użyć dowolnego adresu IP, który ma, tak długo, jak jest w nieprawidłowym formacie. Zazwyczaj użyje rzeczywiste zewnętrzny adres IP urządzenia sieci VPN. Jednak klasycznego konfiguracji sieci wirtualnej do sieci wirtualnej, użyj publicznego adresu IP, który jest przypisany do bramy sieci wirtualnej. Biorąc pod uwagę, że nie została jeszcze utworzona bramy sieci wirtualnej, należy określić dowolnego prawidłowego publicznego adresu IP jako symbol zastępczy.<br>Nie wypełniaj tego pola to — nie jest opcjonalny w przypadku tej konfiguracji. W kolejnym kroku Przejdź wstecz do tych ustawień i je skonfigurować odpowiednie adresy IP bramy sieci wirtualnej, gdy platforma Azure generuje go.
6. Aby uzyskać **przestrzeni adresowej klienta**, użyj przestrzeni adresowej sieci wirtualnej. Zapoznaj się z przykład planowania. Kliknij przycisk **OK** Aby zapisać ustawienia i wrócić do **nowego połączenia sieci VPN** strony.

    ![lokacji lokalnej](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Krok 4 — Utwórz bramę sieci wirtualnej

Każda sieć wirtualna musi mieć bramy sieci wirtualnej. Brama sieci wirtualnej trasy i szyfruje ruch.

1. Na stronie **Nowe połączenie VPN** zaznacz pole wyboru **Utwórz bramę natychmiast**.
2. Kliknij przycisk **podsieć, rozmiar i typ routingu**. Na **konfiguracji bramy** kliknij przycisk **podsieci**.
3. Nazwa podsieci bramy jest wypełniane automatycznie z wymaganą nazwą "GatewaySubnet". **Zakres adresów** zawiera adresy IP, które są przydzielone do usługi bramy sieci VPN. Zezwalaj na niektóre konfiguracje podsieci bramy /29, ale najlepiej użyć /28 lub /27, aby zmieścił się w przyszłości konfiguracje, które mogą wymagać więcej adresów IP dla bramy usług. W naszym przykładzie ustawienia używamy 10.11.1.0/27. Dostosuj przestrzeni adresowej, a następnie kliknij przycisk **OK**.
4. Skonfiguruj **rozmiaru bramy**. To ustawienie oznacza [jednostka SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Skonfiguruj **typ routingu**. Routing typu dla tej konfiguracji musi być **dynamiczne**. Typ routingu nie można zmienić później, chyba że zerwanie bramy i Utwórz nową.
6. Kliknij przycisk **OK**.
7. Na **nowego połączenia sieci VPN** kliknij przycisk **OK** aby rozpocząć tworzenie bramy sieci wirtualnej. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

## <a name="vnet4settings"></a>Krok 5 — Konfigurowanie ustawień TestVNet4

Powtórz kroki, aby [utworzyć lokalnej witryny](#localsite) i [Utwórz bramę sieci wirtualnej](#gw) skonfigurować TestVNet4, zastępując wartości, gdy jest to konieczne. Jeśli robią to jako wykonywania, użyj [przykładowe wartości](#vnetvalues).

## <a name="updatelocal"></a>Krok 6 — aktualizacja lokalnych witryn

Po z bram sieci wirtualnej zostały utworzone dla obu sieci wirtualnych, należy dostosować lokalnych witryn **adres IP bramy sieci VPN** wartości.

|Nazwa sieci wirtualnej|Podłączonej lokacji|Adres IP bramy|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Adres IP bramy sieci VPN dla TestVNet4|
|TestVNet4|VNet1Local|Adres IP bramy sieci VPN dla TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Część 1 - Get bramy na publiczny adres IP sieci wirtualnej

1. Znajdź sieci wirtualnej w portalu Azure.
2. Kliknij, aby otworzyć sieci wirtualnej **omówienie** strony. Na stronie w **połączeń sieci VPN**, można wyświetlić adres IP dla bramy sieci wirtualnej.

  ![Publiczny adres IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Skopiuj adres IP. Będą używać go w następnej sekcji.
4. Powtórz te kroki dla TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Część 2 - modyfikowanie lokacji lokalnej

1. Znajdź sieci wirtualnej w portalu Azure.
2. W sieci wirtualnej **omówienie** kliknij przycisk lokacji lokalnej.

  ![Utworzony lokacji lokalnej](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na **połączeń VPN lokacja-lokacja** strony, kliknij nazwę lokacji lokalnej, który chcesz zmodyfikować.

  ![Otwórz witrynę lokalnego](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kliknij przycisk **lokacji lokalnej** , który chcesz zmodyfikować.

  ![modyfikowanie lokacji](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aktualizacja **adres IP bramy sieci VPN** i kliknij przycisk **OK** Aby zapisać ustawienia.

  ![brama IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zamknij inne strony.
7. Powtórz te kroki dla TestVNet4.

## <a name="getvalues"></a>Krok 7: pobieranie wartości z pliku konfiguracji sieci

Po utworzeniu klasyczne sieci wirtualne w portalu Azure, nazwę wyświetlania nie jest pełną nazwę, której użyjesz dla środowiska PowerShell. Na przykład sieci wirtualnej z nosić **TestVNet1** w portalu, może być znacznie dłuższe nazwę w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: **ClassicRG TestVNet1 grupy**. Podczas tworzenia połączenia, należy użyć wartości, które są wyświetlane w pliku konfiguracji sieci.

W poniższych krokach będą Połącz się z kontem platformy Azure oraz Pobierz i Wyświetl pliku konfiguracji sieci, można uzyskać wartości, które są wymagane dla połączenia.

1. Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell Azure usługi zarządzania (ko). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

  ```powershell
  Connect-AzureRmAccount
  ```

  Sprawdź subskrypcje dostępne na koncie.

  ```powershell
  Get-AzureRmSubscription
  ```

  Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  Następnie użyj następującego polecenia cmdlet można dodać subskrypcji platformy Azure do środowiska PowerShell dla klasycznym modelu wdrażania.

  ```powershell
  Add-AzureAccount
  ```
3. Eksportowanie i Wyświetl plik konfiguracji sieci. Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie wyeksportowany do pliku konfiguracji sieci **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Otwórz plik w edytorze tekstów i wyświetlanie nazw sieci wirtualnych i witryny. Będą one nazwę użytą podczas tworzenia połączenia.<br>Nazwy sieci wirtualnej są wyświetlane jako **VirtualNetworkSite name =**<br>Nazwy lokacji są wyświetlane jako **nazwy elementu LocalNetworkSiteRef =**

## <a name="createconnections"></a>Krok 8 - Tworzenie połączenia bramy sieci VPN

Po wykonaniu poprzednich krokach można ustawić klucze wstępnego IPsec i IKE i utworzyć połączenie. Ten zestaw kroków korzysta z programu PowerShell. Nie można skonfigurować połączenia do wirtualnymi klasycznym modelu wdrażania w portalu Azure.

W przykładach Zwróć uwagę, że klucz Współdzielony są dokładnie takie same. Klucz udostępniony musi zawsze odpowiadać. Pamiętaj zastąpić wartości w tych przykładach dokładnej nazwy sieci wirtualnych i lokalnych witryn sieci.

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
3. Poczekaj, aż do zainicjowania połączenia. Brama została zainicjowana, stan po "Powodzenie".

  ```
  Error          :
  HttpStatusCode : OK
  Id             :
  Status         : Successful
  RequestId      :
  StatusCode     : OK
  ```

## <a name="faq"></a>Aby wirtualnymi zagadnienia dotyczące klasyczne sieci wirtualne
* Sieci wirtualne mogą być w tej samej lub różnych subskrypcji.
* Sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (lokalizacjach) świadczenia usługi Azure.
* Usługa w chmurze ani punkt końcowy z równoważeniem obciążenia nie mogą rozciągać się na sieci wirtualne, nawet jeśli są one ze sobą połączone.
* Łączenie wielu sieci wirtualnych nie wymaga żadnych urządzeń sieci VPN.
* Aby wirtualnymi obsługuje łączenia sieci wirtualnej platformy Azure. Nie obsługuje łączenia maszyn wirtualnych lub usług, które nie są wdrażane do sieci wirtualnej w chmurze.
* Aby wirtualnymi wymaga dynamicznymi bramami routingu. Azure statycznymi bramami routingu nie są obsługiwane.
* Połączenie sieci wirtualnej może być używane równocześnie z sieciami VPN obejmującymi wiele lokacji. Brak maksymalnie 10 tuneli VPN dla bramy sieci VPN sieci wirtualnej nawiązywania połączenia z innymi sieciami wirtualnymi, albo lokacjami lokalnymi.
* Przestrzenie adresów sieci wirtualnych i lokalnych witryn sieci obejmujących lokalizacje w obrębie organizacji nie mogą się nakładać. Nakładających się przestrzeni adresowych spowoduje, że do tworzenia sieci wirtualnych i przekazywanie plików konfiguracyjnych netcfg się niepowodzeniem.
* Nadmiarowe tunele między dwiema sieciami wirtualnymi nie są obsługiwane.
* Wszystkie tuneli VPN do sieci wirtualnej, P2S sieci VPN, w tym udostępnianie dostępnej przepustowości dla bramy sieci VPN, a tym samym przestojów bramy sieci VPN umowy SLA platformy Azure.
* Wirtualnymi do ruchu przechodzącego przez Azure sieci szkieletowej.

## <a name="next-steps"></a>Kolejne kroki
Sprawdź połączenia. Zobacz [sprawdzić połączenie bramy sieci VPN](vpn-gateway-verify-connection-resource-manager.md).
