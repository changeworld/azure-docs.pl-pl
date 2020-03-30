---
title: 'Łączenie klasycznych sieci wirtualnych z sieciami wirtualnymi usługi Azure Resource Manager: Portal | Dokumenty firmy Microsoft'
description: Kroki, aby połączyć klasyczne sieci wirtualne z sieciami wirtualnymi Menedżera zasobów przy użyciu bramy sieci VPN i portalu
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 5e64cb2db2bd16a881334779a1c6f1ef19296da2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152027"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Łączenie sieci wirtualnych z różnych modeli wdrażania za pomocą portalu

W tym artykule pokazano, jak połączyć klasyczne sieci wirtualne z sieciami wirtualnymi Menedżera zasobów, aby umożliwić komunikację z innymi zasobami znajdującymi się w oddzielnych modelach wdrażania. Kroki opisane w tym artykule należy przede wszystkim korzystać z witryny Azure portal, ale można również utworzyć tę konfigurację przy użyciu programu PowerShell, wybierając artykuł z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Powershell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Łączenie klasycznej sieci wirtualnej z siecią wirtualną Menedżera zasobów jest podobne do łączenia sieci wirtualnej z lokalną lokalizacją lokacji. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenie między sieciami wirtualnymi, które znajdują się w różnych subskrypcji i w różnych regionach. Można również połączyć sieci wirtualne, które mają już połączenia z sieciami lokalnymi, o ile brama, z którą zostały skonfigurowane, jest dynamiczna lub oparta na trasie. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz jeszcze bramy sieci wirtualnej i nie chcesz jej tworzyć, warto zamiast tego rozważyć połączenie sieci wirtualnych przy użyciu komunikacji równorzędnej sieci wirtualnej. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Przed rozpoczęciem



* Te kroki zakładają, że obie sieci wirtualne zostały już utworzone. Jeśli używasz tego artykułu jako ćwiczenie i nie masz sieci wirtualnych, istnieją łącza w krokach, które pomogą Ci je utworzyć.
* Sprawdź, czy zakresy adresów sieci wirtualnych nie nakładają się na siebie lub pokrywają się z żadnym z zakresów dla innych połączeń, z którymi mogą być połączone bramy.
* Zainstaluj najnowsze polecenia cmdlet programu PowerShell zarówno dla Menedżera zasobów, jak i zarządzania usługami (klasyczne). W tym artykule używamy zarówno witryny Azure portal i programu PowerShell. Program PowerShell jest wymagany do utworzenia połączenia z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). 

### <a name="example-settings"></a><a name="values"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Klasyczna vnet**

Nazwa sieci wirtualnej = ClassicVNet <br>
Przestrzeń adresowa = 10.0.0.0/24 <br>
Nazwa podsieci = Podsieć-1 <br>
Zakres adresów podsieci = 10,0,0,0/27 <br>
Subskrypcja = subskrypcja, której chcesz użyć <br>
Grupa zasobów = ClassicRG <br>
Lokalizacja = Zachodnie stany USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Strona lokalna = RMVNetLocal <br>

**W sieci wirtualnej Menedżera zasobów**

Nazwa sieci wirtualnej = RMVNet <br>
Przestrzeń adresowa = 192.168.0.0/16 <br>
Grupa zasobów = RG1 <br>
Lokalizacja = wschodnie stany USA <br>
Nazwa podsieci = Podsieć-1 <br>
Zakres adresów = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nazwa bramy sieci wirtualnej = RMGateway <br>
Typ bramy = SIEĆ VPN <br>
Typ sieci VPN = oparty na marszrutach <br>
SKU = VpnGw1 <br>
Lokalizacja = wschodnie stany USA <br>
Sieć wirtualna = RMVNet <br> (skojarz bramę sieci VPN z tą siecią wirtualną) Pierwsza konfiguracja IP = rmgwpip <br> (publiczny adres IP bramy) Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa połączenia = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Omówienie połączenia

W przypadku tej konfiguracji należy utworzyć połączenie bramy sieci VPN za pośrednictwem tunelu sieci VPN IPsec/IKE między sieciami wirtualnymi. Upewnij się, że żaden z zakresów sieci wirtualnej nie pokrywa się ze sobą lub z dowolną siecią lokalną, z którą się łączą.

W poniższej tabeli przedstawiono przykład sposobu definiowania przykładowych sieci wirtualnych i lokacji lokalnych:

| Virtual Network | Przestrzeń adresowa | Region | Łączy się z lokalną lokacją sieciową |
|:--- |:--- |:--- |:--- |
| ClassicVNet (Siecie) classicv |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |
| W sieci RMV | (192.168.0.0/16) |Wschodnie stany USA |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Sekcja 1 - Konfigurowanie klasycznych ustawień sieci wirtualnej

W tej sekcji utworzysz klasyczną sieć wirtualną, sieć lokalną (lokację lokalną) i bramę sieci wirtualnej. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj, aby zastąpić wartości własnymi lub użyć [przykładowych](#values) wartości.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"> </a>Tworzenie klasycznej sieci wirtualnej

Jeśli nie masz klasycznej sieci wirtualnej i uruchamiasz te kroki jako ćwiczenie, możesz utworzyć sieć wirtualną przy użyciu [tego artykułu](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) i wartości ustawień [przykładu](#values) z góry.

Jeśli masz już sieć wirtualną z bramą sieci VPN, sprawdź, czy brama jest dynamiczna. Jeśli jest statyczny, należy najpierw usunąć bramę sieci VPN przed przystąpieniem do [konfigurowania lokacji lokalnej](#local).

1. Otwórz witrynę [Azure Portal](https://ms.portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij **+ Utwórz zasób,** aby otworzyć stronę "Nowy".
3. W polu "Wyszukaj w rynku" wpisz "Sieć wirtualna". Jeśli zamiast tego wybierz pozycję Sieć -> sieć wirtualna, nie zostanie dostępna opcja utworzenia klasycznej sieci wirtualnej.
4. Znajdź "Sieć wirtualną" z listy zwracanych i kliknij ją, aby otworzyć stronę sieć wirtualna. 
5. Na stronie sieci wirtualnej wybierz opcję "Classic", aby utworzyć klasyczną sieć wirtualną. Jeśli wziąć domyślne w tym miejscu, będzie zakończyć się z sieci wirtualnej Menedżera zasobów zamiast.

### <a name="2-configure-the-local-site"></a>2. <a name="local"> </a>Konfigurowanie witryny lokalnej

1. Przejdź do **pozycji Wszystkie zasoby** i znajdź **ClassicVNet** na liście.
2. Kliknij **pozycję Brama** w sekcji **Ustawienia** menu, a następnie kliknij baner, aby utworzyć bramę.
  ![Konfigurowanie bramy sieci VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurowanie bramy VPN Gateway")
3. Na stronie **Nowe połączenie sieci VPN** dla typu **połączenia**wybierz pozycję **Lokacja lokacja**.
4. W przypadku **witryny lokalnej**kliknij pozycję **Konfiguruj wymagane ustawienia**. Spowoduje to otwarcie strony **Witryna lokalna.**
5. Na stronie **Local site** należy utworzyć nazwę, która ma być odwoływana do sieci wirtualnej Menedżera zasobów. Na przykład "RMVNetLocal".
6. Jeśli brama sieci VPN dla sieci wirtualnej Menedżera zasobów ma już publiczny adres IP, użyj tej wartości dla pola **adres IP bramy sieci VPN.** Jeśli wykonujesz te kroki jako ćwiczenie lub nie masz jeszcze bramy sieci wirtualnej dla sieci wirtualnej Menedżera zasobów, możesz uzupełnić zastępczy adres IP. Upewnij się, że zastępczy adres IP używa prawidłowego formatu. Później adres IP zastępczego zastępuje się publicznym adresem IP bramy sieci wirtualnej Menedżera zasobów.
7. W przypadku **przestrzeni adresowej klienta**należy użyć [wartości dla](#connectoverview) przestrzeni adresów IP sieci wirtualnej dla sieci wirtualnej Menedżera zasobów. To ustawienie służy do określania przestrzeni adresowych do kierowania do sieci wirtualnej Menedżera zasobów. W przykładzie używamy 192.168.0.0/16, zakres adresów dla RMVNet.
8. Kliknij **przycisk OK,** aby zapisać wartości i powrócić do strony **Nowe połączenie sieci VPN.**

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. Tworzenie bramy sieci wirtualnej

1. Na stronie **Nowe połączenie sieci VPN** zaznacz natychmiast pole wyboru **Utwórz bramę.**
2. Kliknij pozycję **Opcjonalna konfiguracja bramy**, aby otworzyć stronę **Konfiguracja bramy**.

   ![Otwórz stronę konfiguracji bramy](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Otwórz stronę konfiguracji bramy")
3. Kliknij **pozycję Podsieć — skonfiguruj wymagane ustawienia,** aby otworzyć stronę **Dodaj podsieci.** **Nazwa** jest już skonfigurowana z wymaganą wartością: **GatewaySubnet**.
4. **Zakres adresów** odnosi się do zakresu podsieci bramy. Chociaż można utworzyć podsieć bramy z zakresem adresów /29 (3 adresy), zalecamy utworzenie podsieci bramy zawierającej więcej adresów IP. Będzie to uwzględniać przyszłe konfiguracje, które mogą wymagać większej liczby dostępnych adresów IP. Jeśli to możliwe, użyj /27 lub /28. Jeśli te kroki są używane jako ćwiczenie, można odwołać się do [przykładowych wartości](#values). W tym przykładzie używamy "10.0.0.32/28". Kliknij **przycisk OK,** aby utworzyć podsieć bramy.
5. Na stronie **konfiguracji bramy** **rozmiar** odnosi się do jednostki SKU bramy. Wybierz jednostkę SKU bramy dla bramy sieci VPN.
6. Sprawdź, czy **typ routingu** jest **dynamiczny,** a następnie kliknij przycisk **OK,** aby powrócić do strony **Nowe połączenie sieci VPN.**
7. Na stronie **Nowe połączenie sieci VPN** kliknij przycisk **OK,** aby rozpocząć tworzenie bramy sieci VPN. Tworzenie bramy sieci VPN może potrwać do 45 minut.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. Skopiuj publiczny adres IP bramy sieci wirtualnej

Po utworzeniu bramy sieci wirtualnej można wyświetlić adres IP bramy. 

1. Przejdź do klasycznej sieci wirtualnej i kliknij pozycję **Przegląd**.
2. Kliknij **pozycję Połączenia sieci VPN,** aby otworzyć stronę połączenia sieci VPN. Na stronie Połączenia sieci VPN można wyświetlić publiczny adres IP. Jest to publiczny adres IP przypisany do bramy sieci wirtualnej. Zanotuj adres IP. Jest używany w późniejszych krokach podczas pracy z ustawieniami konfiguracji bramy sieci lokalnej Menedżera zasobów. 
3. Można wyświetlić stan połączeń bramy. Zwróć uwagę, że utworzona lokalna witryna sieciowa jest wyświetlana jako "Łączenie". Stan zmieni się po utworzeniu połączeń. Możesz zamknąć tę stronę po zakończeniu wyświetlania stanu.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Sekcja 2 — konfigurowanie ustawień sieci wirtualnej Menedżera zasobów

W tej sekcji utworzysz bramę sieci wirtualnej i bramę sieci lokalnej dla sieci wirtualnej Menedżera zasobów. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj, aby zastąpić wartości własnymi lub użyć [przykładowych](#values) wartości.

### <a name="1-create-a-virtual-network"></a>1. Tworzenie sieci wirtualnej

**Przykładowe wartości:**

* Nazwa sieci wirtualnej = RMVNet <br>
* Przestrzeń adresowa = 192.168.0.0/16 <br>
* Grupa zasobów = RG1 <br>
* Lokalizacja = wschodnie stany USA <br>
* Nazwa podsieci = Podsieć-1 <br>
* Zakres adresów = 192.168.1.0/24 <br>

Jeśli nie masz sieci wirtualnej Menedżera zasobów i uruchamiasz te kroki jako ćwiczenie, utwórz sieć wirtualną z krokami w [tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md), używając przykładowych wartości.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. Tworzenie bramy sieci wirtualnej

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Przykładowe wartości:**

* Nazwa bramy sieci wirtualnej = RMGateway <br>
* Typ bramy = SIEĆ VPN <br>
* Typ sieci VPN = oparty na marszrutach <br>
* SKU = VpnGw1 <br>
* Lokalizacja = wschodnie stany USA <br>
* Sieć wirtualna = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Pierwsza konfiguracja IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. Tworzenie bramy sieci lokalnej

**Przykładowe wartości:** Brama sieci lokalnej = ClassicVNetLocal

| Virtual Network | Przestrzeń adresowa | Region | Łączy się z lokalną lokacją sieciową |Publiczny adres IP bramy|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet (Siecie) classicv |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |Publiczny adres IP przypisany do bramy ClassicVNet|
| W sieci RMV | (192.168.0.0/16) |Wschodnie stany USA |ClassicVNetLocal (10.0.0.0/24) |Publiczny adres IP przypisany do bramy RMVNet.|

Brama sieci lokalnej określa zakres adresów i publiczny adres IP skojarzony z klasyczną siecią wirtualną i jej bramą sieci wirtualnej. Jeśli wykonujesz te kroki jako ćwiczenie, zapoznaj się z przykładowymi wartościami.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Sekcja 3 - Modyfikowanie klasycznych ustawień lokacji lokalnej sieci wirtualnej

W tej sekcji należy zastąpić zastępczy adres IP używany podczas określania ustawień lokacji lokalnej adresem IP bramy sieci VPN Menedżera zasobów. W tej sekcji używane są klasyczne polecenia cmdlet programu PowerShell.This section uses the classic (SM) PowerShell cmdlets.

1. W witrynie Azure portal przejdź do klasycznej sieci wirtualnej.
2. Na stronie sieci wirtualnej kliknij pozycję **Przegląd**.
3. W sekcji **Połączenia sieci VPN** kliknij nazwę witryny lokalnej na grafice.

   ![Połączenia VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Połączenia VPN")
4. Na stronie **Połączenia sieci VPN między lokacjami** kliknij nazwę witryny.

   ![Nazwa witryny](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nazwa witryny lokalnej")
5. Na stronie połączenia witryny lokalnej kliknij nazwę witryny lokalnej, aby otworzyć stronę **Witryna lokalna.**

   ![Strona otwarta lokalna](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Otwórz witrynę lokalną")
6. Na stronie **Lokacja lokalna** zastąp **adres IP bramy sieci VPN** adresem IP bramy Menedżera zasobów.

   ![Adres-adres IP bramy](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Adres IP bramy")
7. Kliknij **przycisk OK,** aby zaktualizować adres IP.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Sekcja 4 - Tworzenie Menedżera zasobów do połączenia klasycznego

W tych krokach można skonfigurować połączenie z sieci wirtualnej Menedżera zasobów do klasycznej sieci wirtualnej przy użyciu witryny Azure portal.

1. W **aplikacji Wszystkie zasoby**znajdź bramę sieci lokalnej. W naszym przykładzie bramą sieci lokalnej jest **ClassicVNetLocal**.
2. Kliknij **pozycję Konfiguracja** i sprawdź, czy wartość adresu IP jest bramą sieci VPN dla klasycznej sieci wirtualnej. W razie potrzeby kliknij pozycję **Zapisz**. Zamknij stronę.
3. W **aplikacji Wszystkie zasoby**kliknij bramę sieci lokalnej.
4. Kliknij **pozycję Połączenia,** aby otworzyć stronę Połączenia.
5. Na stronie **Połączenia** kliknij, **+** aby dodać połączenie.
6. Na stronie **Dodawanie połączenia** nazwij połączenie. Na przykład "RMtoClassic".
7. **Site-to-Site** jest już zaznaczona na tej stronie.
8. Wybierz bramę sieci wirtualnej, którą chcesz skojarzyć z tą witryną.
9. Utwórz **klucz udostępniony**. Ten klucz jest również używany w połączeniu utworzonym z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów. Można wygenerować klucz lub uzupełnić jeden. W naszym przykładzie używamy "abc123", ale możesz (i powinieneś) użyć czegoś bardziej złożonego.
10. Kliknij **przycisk OK,** aby utworzyć połączenie.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Sekcja 5 — tworzenie połączenia klasycznego z Menedżerem zasobów

W tych krokach można skonfigurować połączenie z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów. Te kroki wymagają programu PowerShell. Nie można utworzyć tego połączenia w portalu. Upewnij się, że pobrano i zainstalowano zarówno klasyczne (SM) i Resource Manager (RM) polecenia cmdlet programu PowerShell.

### <a name="1-connect-to-your-azure-account"></a>1. Połącz się ze swoim kontem platformy Azure

Otwórz konsolę programu PowerShell z podwyższonymi prawami i zaloguj się do swojego konta platformy Azure. Po zalogowaniu ustawienia konta są pobierane, dzięki czemu są one dostępne dla programu Azure PowerShell. Następujące polecenie cmdlet monituje o poświadczenia logowania dla konta platformy Azure dla modelu wdrażania usługi Resource Manager:

```powershell
Connect-AzAccount
```

Pobierz listę subskrypcji platformy Azure.

```powershell
Get-AzSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Następnie zaloguj się, aby użyć klasycznych poleceń cmdlet programu PowerShell (Zarządzanie usługami). Użyj następującego polecenia, aby dodać konto platformy Azure dla klasycznego modelu wdrażania:

```powershell
Add-AzureAccount
```

Pobierz listę swoich subskrypcji. Ten krok może być konieczne podczas dodawania poleceń cmdlet zarządzania usługami, w zależności od instalacji modułu platformy Azure.

```powershell
Get-AzureSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, której chcesz użyć.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Wyświetlanie wartości plików konfiguracji sieci

Podczas tworzenia sieci wirtualnej w witrynie Azure portal, pełna nazwa używana przez platformę Azure nie jest widoczna w witrynie Azure portal. Na przykład sieć wirtualna, która wydaje się być nazwany "ClassicVNet" w witrynie Azure portal może mieć znacznie dłuższą nazwę w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: "Group ClassicRG ClassicVNet". W tych krokach można pobrać plik konfiguracji sieci i wyświetlić wartości.

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik za pomocą edytora tekstu i wyświetl nazwę klasycznej sieci wirtualnej. Nazwy w pliku konfiguracji sieciowej należy używać podczas uruchamiania poleceń cmdlet programu PowerShell.

- Nazwy sieci wirtualnych są wyświetlane jako **nazwa VirtualNetworkSite =**
- Nazwy witryn są wyświetlane jako **nazwa LocalNetworkSite=**

### <a name="3-create-the-connection"></a>3. Tworzenie połączenia

Ustaw klucz udostępniony i utwórz połączenie z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów. Nie można ustawić klucza udostępnionego za pomocą portalu. Upewnij się, że te kroki są uruchamiane po zalogowaniu przy użyciu klasycznej wersji poleceń cmdlet programu PowerShell. Aby to zrobić, należy użyć **add-AzureAccount**. W przeciwnym razie nie będzie można ustawić "-AzureVNetGatewayKey".

- W tym przykładzie **-VNetName** jest nazwą klasycznej sieci wirtualnej, jak można znaleźć w pliku konfiguracji sieci. 
- **-LocalNetworkSiteName** to nazwa określona dla lokacji lokalnej, znaleziona w pliku konfiguracji sieci.
- **-SharedKey** jest wartością, którą generujesz i określasz. W tym przykładzie użyliśmy *abc123*, ale można wygenerować coś bardziej skomplikowanego. Ważne jest to, że wartość, którą określisz w tym miejscu, musi być tą samą wartością, którą określono podczas tworzenia Menedżera zasobów do połączenia klasycznego.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Sekcja 6 - Weryfikowanie połączeń

Połączenia można zweryfikować za pomocą witryny Azure portal lub programu PowerShell. Podczas weryfikacji może być konieczne odczekanie minuty lub dwóch podczas tworzenia połączenia. Gdy połączenie zakończy się pomyślnie, stan łączności zmienia się z "Łączenie" na "Połączone".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby zweryfikować połączenie z klasycznej sieci wirtualnej z siecią wirtualną Menedżera zasobów

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby zweryfikować połączenie z sieci wirtualnej Menedżera zasobów z klasyczną siecią wirtualną

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
