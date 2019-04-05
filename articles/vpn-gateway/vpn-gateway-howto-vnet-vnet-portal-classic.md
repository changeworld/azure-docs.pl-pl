---
title: 'Utwórz połączenie między sieciami wirtualnymi: klasycznym: Witryna Azure portal | Dokumentacja firmy Microsoft'
description: Łączenie sieci wirtualnych platformy Azure ze sobą przy użyciu programu PowerShell i witryny Azure portal.
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
ms.openlocfilehash: e323a8d71bbffd1d29ad793dff7b5b4a072b6979
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59046126"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Konfigurowanie połączenia sieć wirtualna-sieć wirtualna (klasyczna)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Ten artykuł pomaga utworzyć połączenie bramy sieci VPN między sieciami wirtualnymi. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji. Te kroki w tym artykule mają zastosowanie do klasycznego modelu wdrażania i witryny Azure portal. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Witryna Azure portal (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Łączenie różnych modeli wdrażania — witryna Azure portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Łączenie różnych modeli wdrażania — PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Sieć wirtualna do diagramu łączność w sieci wirtualnej](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-vnet-to-vnet-connections"></a>Informacje o połączeniach między sieciami wirtualnymi

Łączenie sieci wirtualnej z inną siecią wirtualną (VNet między sieciami wirtualnymi) w klasycznym modelu wdrażania przy użyciu bramy sieci VPN jest podobny do procesu łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE.

Sieci wirtualne, z którymi się łączysz, może być w różnych regionach i różnych subskrypcji. Możesz połączyć sieć wirtualną do sieci wirtualnej komunikacji z konfiguracjami obejmującymi wiele lokacji. Pozwala to tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi.

![Połączenia między sieciami wirtualnymi](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Dlaczego łączy się sieci wirtualne?

Sieci wirtualne można łączyć z następujących powodów:

* **Niezależna od regionu nadmiarowość i obecność geograficzna**

  * Można tworzyć własne replikacje geograficzne lub przeprowadzać synchronizację z bezpiecznym połączeniem bez przechodzenia przez punkty końcowe dostępne z Internetu.
  * Za pomocą usługi Azure Load Balancer i firmy Microsoft lub innych technologii klastrowania można skonfigurować obciążenie o wysokiej dostępności z nadmiarowością geograficzną w wielu regionach platformy Azure. Istotnym przykładem jest konfiguracja ustawienia SQL Zawsze włączone, w przypadku którego grupy dostępności rozciągają się na wiele regionów świadczenia usługi Azure.
* **Regionalne aplikacje wielowarstwowe z granicą silną izolację**

  * W ramach tego samego regionu można skonfigurować aplikacje wielowarstwowe z wielu sieci wirtualnych połączonych z silną izolację i bezpiecznej komunikacji między warstwy.
* **Obejmujące wiele subskrypcji i komunikacji między organizacji na platformie Azure**

  * Jeśli masz wiele subskrypcji platformy Azure, możesz połączyć obciążeń z różnych subskrypcji jednocześnie bezpiecznie między sieciami wirtualnymi.
  * Dla przedsiębiorstw i dostawców usług można włączyć dla wielu organizacji komunikację z bezpiecznego technologii sieci VPN na platformie Azure.

Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Zagadnienia dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem tego ćwiczenia, Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Service Management (SM). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Będziemy korzystać z portalu potrzeby większość czynności, ale należy użyć programu PowerShell do tworzenia połączeń między sieciami wirtualnymi. Nie można utworzyć połączenia przy użyciu witryny Azure portal.

## <a name="plan"></a>Krok 1 — Planowanie zakresów adresów IP

Należy określić zakresy, które zostaną użyte do ustawienia sieci wirtualnych. W przypadku tej konfiguracji należy upewnić się, że żaden z zakresów w Twojej sieci wirtualnej nakłada się ze sobą lub z żadną z sieci lokalnej, które łączą się z.

W poniższej tabeli przedstawiono przykład sposobu definiowania sieci wirtualne. Używaj zakresów tylko wskazówką. Zanotuj zakresów sieci wirtualnych. Ta informacja będzie potrzebna do wykonania kolejnych kroków.

**Przykład**

| Virtual Network | Przestrzeń adresowa | Region | Nawiązanie połączenia z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Wschodnie stany USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Krok 2 — Tworzenie sieci wirtualnych

Utworzymy dwie sieci wirtualne w [witryny Azure portal](https://portal.azure.com). Aby uzyskać instrukcje dotyczące tworzenia klasycznych sieci wirtualnych, zobacz [utworzyć klasyczną sieć wirtualną](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Tworzenie klasycznej sieci wirtualnej za pomocą portalu, musisz przejść do strony sieć wirtualna za pomocą poniższej procedury, w przeciwnym razie nie ma opcję, aby utworzyć klasyczną sieć wirtualną:

1. Kliknij przycisk "+', aby otworzyć stronę"New".
2. W polu "Wyszukaj w witrynie marketplace" wpisz "Sieć wirtualna". Jeśli zamiast tego należy wybrać sieć -> Sieć wirtualna, nie otrzymasz opcję, aby utworzyć klasyczną sieć wirtualną.
3. Znajdź "Sieci wirtualnej" na liście zwrócone i kliknij go, aby otworzyć stronę sieci wirtualnej. 
4. Na stronie sieci wirtualnej wybierz pozycję "Classic", aby utworzyć klasyczną sieć wirtualną. 

Jeśli używasz w tym artykule w charakterze ćwiczenia, można użyć następujących wartości przykładowych:

**Wartości dla sieci TestVNet1**

Nazwa: TestVNet1<br>
Przestrzeń adresowa: 10.11.0.0/16, 10.12.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.11.0.1/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: Wschodnie stany USA<br>
GatewaySubnet: 10.11.1.0/27

**Wartości dla sieci TestVNet4**

Nazwa: TestVNet4<br>
Przestrzeń adresowa: 10.41.0.0/16, 10.42.0.0/16 (opcjonalnie)<br>
Nazwa podsieci: domyślna<br>
Zakres adresów podsieci: 10.41.0.1/24<br>
Grupa zasobów: ClassicRG<br>
Lokalizacja: Zachodnie stany USA<br>
GatewaySubnet: 10.41.1.0/27

**Podczas tworzenia sieci wirtualnych należy pamiętać o następujących ustawień:**

* **Przestrzenie adresowe sieci wirtualnej** — na stronie przestrzenie adresów sieci wirtualnej Określ zakres adresów, który chcesz używać dla sieci wirtualnej. Są to dynamiczne adresy IP, które zostaną przypisane maszynom wirtualnym i innych wystąpień roli, które wdrażasz z tą siecią wirtualną.<br>Przestrzenie adresów, którą wybierzesz nie może nakładać się z przestrzeniami adresowymi dla każdego z innymi sieciami wirtualnymi lub lokalnymi lokalizacji łączących się z tą siecią wirtualną.

* **Lokalizacja** — po utworzeniu sieci wirtualnej, należy ją skojarzyć z lokalizacji platformy Azure (regionu). Na przykład jeśli chcesz, aby maszyny wirtualne, które zostały wdrożone w sieci wirtualnej mają znajdować się w regionie zachodnie stany USA, należy wybrać tę właśnie lokalizację. Nie można zmienić lokalizację skojarzone z sieci wirtualnej, po jego utworzeniu.

**Po utworzeniu sieci wirtualne można dodać następujące ustawienia:**

* **Przestrzeń adresowa** — dodatkową przestrzeń adresową nie jest wymagana dla tej konfiguracji, ale możesz dodać dodatkową przestrzeń adresową, po utworzeniu sieci wirtualnej.

* **Podsieci** — dodatkowe podsieci nie są wymagane dla tej konfiguracji, ale warto mieć maszyn wirtualnych w podsieci, który jest oddzielony od innych wystąpień roli.

* **Serwery DNS** — wprowadź nazwę serwera DNS i adres IP. To ustawienie nie powoduje jednak utworzenia serwera DNS. Umożliwia natomiast określenie serwerów DNS, które mają być używane do rozpoznawania nazw dla tej sieci wirtualnej.

W tej sekcji, skonfiguruj typ połączenia, lokacja lokalna i utworzyć bramę.

## <a name="localsite"></a>Krok 3 — Konfigurowanie lokacji lokalnej

Platforma Azure używa ustawień określonych w każdej lokacji sieci lokalnej, aby określić, jak kierować ruchem między sieciami wirtualnymi. Każda sieć wirtualna musi wskazywać na odpowiednich sieci lokalnej, która ma być kierowany ruch do. Należy określić nazwę, który ma być używana do odwoływania się do poszczególnych lokacji sieci lokalnej. Najlepiej użyć opisową.

Na przykład sieci TestVNet1 łączy się z lokacją sieci lokalnej, którą tworzysz o nazwie "VNet4Local". Ustawienia dla VNet4Local zawierają prefiksy adresów dla sieci TestVNet4.

Lokalna witryna dla każdej sieci wirtualnej jest w innej sieci wirtualnej. Następujące przykładowe wartości są używane dla naszej konfiguracji:

| Virtual Network | Przestrzeń adresowa | Region | Nawiązanie połączenia z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Wschodnie stany USA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Zachodnie stany USA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Znajdź TestVNet1 w witrynie Azure portal. W **połączeń sieci VPN** części strony, kliknij przycisk **bramy**.

    ![Brak bramy](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na **nowe połączenie VPN** wybierz opcję **Site-to-Site**.
3. Kliknij przycisk **lokacja lokalna** aby otworzyć stronę lokacji lokalnej i skonfiguruj ustawienia.
4. Na **lokacja lokalna** strony, określ nazwę lokacji lokalnej. W tym przykładzie użyjemy nazwy lokacji lokalnej "VNet4Local".
5. Aby uzyskać **adres IP bramy sieci VPN**, można użyć dowolnego adresu IP, który ma, tak długo, jak jest w prawidłowym formacie. Zazwyczaj należy użyć rzeczywistego zewnętrznego adresu IP dla urządzenia sieci VPN. Jednak w przypadku klasycznych konfiguracji sieci wirtualnej między sieciami wirtualnymi Użyj publiczny adres IP, który jest przypisany do bramy sieci wirtualnej. Biorąc pod uwagę, że jeszcze nie utworzono bramę sieci wirtualnej, można określić dowolny prawidłowy publiczny adres IP jako symbol zastępczy.<br>Nie pozostaw to pole puste — nie jest opcjonalny w przypadku tej konfiguracji. Na późniejszym etapie wróć do tych ustawień i je skonfigurować za pomocą odpowiednich adresów IP bramy sieci wirtualnej po Azure generuje go.
6. Aby uzyskać **przestrzeń adresowa klienta**, korzystanie z przestrzeni adresowej sieci wirtualnej. Zapoznaj się z planowania przykładu. Kliknij przycisk **OK** Aby zapisać ustawienia i wrócić do **nowe połączenie VPN** strony.

    ![lokacja lokalna](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Krok 4 — Tworzenie bramy sieci wirtualnej

Każda sieć wirtualna musi mieć bramę sieci wirtualnej. Brama sieci wirtualnej trasy i szyfruje ruch.

1. Na stronie **Nowe połączenie VPN** zaznacz pole wyboru **Utwórz bramę natychmiast**.
2. Kliknij przycisk **podsieć, rozmiar i typ routingu**. Na **konfiguracji bramy** kliknij **podsieci**.
3. Nazwa podsieci bramy są wypełniane automatycznie z wymaganą nazwą "GatewaySubnet". **Zakres adresów** zawiera adresy IP, które są przydzielone do usługi bramy sieci VPN. Zezwalaj na niektórych konfiguracji podsieci bramy/29, ale najlepiej użyć o rozmiarze/28 lub/27, aby uwzględnić przyszłe konfiguracje, które mogą wymagać większej liczby adresów IP dla usługi bramy. W ustawieniach naszym przykładzie używamy 10.11.1.0/27. Dostosuj przestrzeni adresowej, a następnie kliknij przycisk **OK**.
4. Konfigurowanie **rozmiar bramy**. To ustawienie oznacza [jednostkę SKU bramy](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Konfigurowanie **typu routingu**. Routing typu dla tej konfiguracji musi być **dynamiczne**. Typ routingu nie można zmienić później, chyba że zatrzymywania bramę i Utwórz nową.
6. Kliknij przycisk **OK**.
7. Na **nowe połączenie VPN** kliknij **OK** aby rozpocząć tworzenie bramy sieci wirtualnej. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

## <a name="vnet4settings"></a>Krok 5 — Konfigurowanie ustawień sieci TestVNet4

Powtórz kroki, aby [Utwórz lokację lokalną](#localsite) i [Tworzenie bramy sieci wirtualnej](#gw) na konfigurowanie sieci TestVNet4, zastępując wartości, gdy jest to konieczne. Jeśli robisz to w charakterze ćwiczenia, użyj [przykładowe wartości](#vnetvalues).

## <a name="updatelocal"></a>Krok 6 — aktualizowanie lokalnych witryn

Po swojej bramy sieci wirtualnej zostały utworzone dla obu sieciach wirtualnych, należy dostosować lokalnych witryn **adres IP bramy sieci VPN** wartości.

|Nazwa sieci wirtualnej|Podłączonej lokacji|Adres IP bramy|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Adres IP bramy sieci VPN dla sieci TestVNet4|
|TestVNet4|VNet1Local|Adres IP bramy sieci VPN dla sieci TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Część 1 — bramy na publiczny adres IP sieci wirtualnej

1. Znajdź sieci wirtualnej w witrynie Azure portal.
2. Kliknij, aby otworzyć sieci wirtualnej **Przegląd** strony. Na stronie w **połączeń sieci VPN**, można wyświetlić adres IP bramy sieci wirtualnej.

   ![Publiczny adres IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Skopiuj adres IP. Zostanie użyty w następnej sekcji.
4. Powtórz te czynności dla sieci TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Część 2 — zmodyfikować Lokacje lokalne

1. Znajdź sieci wirtualnej w witrynie Azure portal.
2. W sieci wirtualnej **Przegląd** kliknij lokacji lokalnej.

   ![Lokacja lokalna utworzone](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na **połączeń sieci VPN typu lokacja-lokacja** kliknij nazwę lokacji lokalnej, który chcesz zmodyfikować.

   ![Otwórz witrynę lokalne](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Kliknij przycisk **lokacja lokalna** , którą chcesz zmodyfikować.

   ![modyfikowanie lokacji](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Aktualizacja **adres IP bramy sieci VPN** i kliknij przycisk **OK** Aby zapisać ustawienia.

   ![Brama IP](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Zamknij inne strony.
7. Powtórz te czynności dla sieci TestVNet4.

## <a name="getvalues"></a>Krok 7 — pobieranie wartości z pliku konfiguracji sieci

Po utworzeniu klasycznych sieci wirtualnych w witrynie Azure portal, nazwa, którą można wyświetlić nie jest pełną nazwę, której użyjesz dla programu PowerShell. Na przykład sieci wirtualnej, która pojawia się miała nazwę **TestVNet1** w portalu może być znacznie dłuższe nazwy w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: **Grupa sieci ClassicRG TestVNet1**. Podczas tworzenia połączenia należy użyć wartości, które są wyświetlane w pliku konfiguracji sieci.

W poniższych krokach możesz nawiązać połączenie z kontem platformy Azure i pobrać i wyświetlić plik konfiguracji sieci, aby uzyskać wartości, które są wymagane dla połączenia.

1. Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Service Management (SM). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

2. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```powershell
   Connect-AzAccount
   ```

   Sprawdź subskrypcje dostępne na koncie.

   ```powershell
   Get-AzSubscription
   ```

   Jeśli masz więcej niż jedną subskrypcję, wybierz tę, której chcesz użyć.

   ```powershell
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```

   Następnie użyj następującego polecenia cmdlet, aby dodać subskrypcję platformy Azure do programu PowerShell dla klasycznego modelu wdrażania.

   ```powershell
   Add-AzureAccount
   ```
3. Eksportowanie i Wyświetl plik konfiguracji sieci. Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do **C:\AzureNet**.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
4. Otwórz plik w edytorze tekstu i wyświetlić nazwy sieci wirtualnych i witryn. Będą one nazwę użytą podczas tworzenia połączenia.<br>Nazwy sieci wirtualnej są wyświetlane jako **VirtualNetworkSite name =**<br>Nazwy lokacji są wyświetlane jako **nazwy elementu LocalNetworkSiteRef =**

## <a name="createconnections"></a>Krok 8 — tworzenie połączeń bramy sieci VPN

Gdy wszystkie wcześniejsze kroki zostały ukończone, możesz ustawić klucze wstępne protokołu IPsec/IKE i utworzyć połączenie. Ten zestaw kroków korzysta z programu PowerShell. Nie można skonfigurować połączenia sieci wirtualnej między sieciami wirtualnymi dla klasycznego modelu wdrażania w witrynie Azure portal.

W przykładach Zwróć uwagę, że klucz współużytkowany są dokładnie takie same. Klucz współużytkowany musi zawsze odpowiadać. Pamiętaj zastąpić wartości w tych przykładach dokładnej nazwy sieci wirtualnych i lokalne Lokacje sieciowe.

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
3. Poczekaj, aż połączeń do zainicjowania. Po zainicjowaniu ma bramę, stan to "Powodzenie".

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq"></a>Sieć wirtualna-sieć wirtualna uwagi dla klasycznych sieci wirtualnych
* Sieci wirtualne mogą być w tej samej lub różnych subskrypcji.
* Sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (lokalizacjach) świadczenia usługi Azure.
* Usługa w chmurze ani punkt końcowy z równoważeniem obciążenia nie mogą rozciągać się na sieci wirtualne, nawet jeśli są one ze sobą połączone.
* Łączenie wielu sieci wirtualnych nie wymaga żadnych urządzeń sieci VPN.
* Sieć wirtualna-sieć wirtualna obsługuje łączenie sieci wirtualnych platformy Azure. Nie obsługuje połączeń maszyn wirtualnych lub usług, które nie zostały wdrożone w sieci wirtualnej w chmurze.
* Sieć wirtualna-sieć wirtualna wymaga dynamicznymi bramami routingu. Usługa Azure bramy o statycznym routingu nie są obsługiwane.
* Połączenie sieci wirtualnej może być używane równocześnie z sieciami VPN obejmującymi wiele lokacji. Istnieje więcej niż 10 tuneli sieci VPN dla bramy sieci VPN sieci wirtualnej nawiązywanie połączeń z innymi sieciami wirtualnymi albo lokacjami lokalnymi.
* Przestrzenie adresów sieci wirtualnych i lokalnych witryn sieci obejmujących lokalizacje w obrębie organizacji nie mogą się nakładać. Nakładające się przestrzenie adresowe spowodują, do tworzenia sieci wirtualnych i przekazywanie plików konfiguracyjnych netcfg nie powiedzie się.
* Nadmiarowe tunele między dwiema sieciami wirtualnymi nie są obsługiwane.
* Wszystkie tunele VPN sieci wirtualnej, w tym sieci VPN typu P2S, współużytkują dostępną przepustowość bramy sieci VPN i tej samej sieci VPN bramy umowę SLA na platformie Azure.
* Sieć wirtualna-sieć wirtualna ruchu przechodzącego przez sieć szkieletową platformy Azure.

## <a name="next-steps"></a>Kolejne kroki
Sprawdź połączenia. Zobacz [sprawdzanie połączenia bramy sieci VPN](vpn-gateway-verify-connection-resource-manager.md).
