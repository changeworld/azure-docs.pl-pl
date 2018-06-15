---
title: 'Połącz klasycznych sieci wirtualnych do sieci wirtualnych Menedżera zasobów Azure: Portal | Dokumentacja firmy Microsoft'
description: Kroki, aby połączyć klasycznych sieci wirtualnych do sieci wirtualnych Menedżera zasobów przy użyciu bramy sieci VPN i portalu
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2018
ms.author: cherylmc
ms.openlocfilehash: 5e51027455da1f8be34d99c79bc79bc37df57d14
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603381"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Nawiązywanie połączenia sieci wirtualnych z różne modele wdrażania przy użyciu portalu

W tym artykule przedstawiono sposób nawiązywania klasyczne sieci wirtualne sieci wirtualnych Menedżera zasobów umożliwia zasobów znajdujących się w modelach wdrażania oddzielne do komunikowania się ze sobą. Kroki opisane w tym artykule przede wszystkim użyj portalu Azure, ale można również utworzyć tę konfigurację za pomocą programu PowerShell, wybierając artykułu z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Łączenie klasycznej sieci wirtualnej z Menedżera zasobów sieci wirtualnej jest podobny do łączenia sieci wirtualnej do lokalizacji lokalnej. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenia między sieciami wirtualnymi, które są w różnych subskrypcji i w różnych regionach. Można również połączyć sieci wirtualnych, które mają już połączenia z sieciami lokalnymi tak długo, jak jest bramy, które zostały skonfigurowane z dynamicznego lub oparte na trasach. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz już bramy sieci wirtualnej i nie chcesz utworzyć, możesz zamiast tego należy rozważyć połączenie Twojej sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnej. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Przed rozpoczęciem

* Tych krokach przyjęto założenie, że obie sieci wirtualne zostały już utworzone. Jeśli jest używany w tym artykule jako wykonywania i nie ma sieci wirtualnych, Brak łącza w kroki, aby ułatwić ich utworzenia.
* Sprawdź, czy zakresy adresów dla sieci wirtualne nie pokrywają się ze sobą lub pokrywają się z jednym z zakresów dla innych połączeń, które może być połączone bramy.
* Zainstaluj najnowsze poleceń cmdlet programu PowerShell dla usługi Resource Manager i usługi zarządzania (klasyczny). W tym artykule używamy Azure portal i programu PowerShell. Środowisko PowerShell jest wymagany do utworzenia połączenia z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). 

### <a name="values"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Klasyczne sieci wirtualnej**

Nazwa sieci wirtualnej = ClassicVNet <br>
Przestrzeń adresowa = 10.0.0.0/24 <br>
Nazwa podsieci = 1 podsieci <br>
Zakres adresów podsieci = 10.0.0.0/27 <br>
Subskrypcja = subskrypcji, którego chcesz użyć <br>
Grupa zasobów = ClassicRG <br>
Lokalizacja = zachodnie stany USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokalna witryna = RMVNetLocal <br>

**Menedżer zasobów w sieci wirtualnej**

Nazwa sieci wirtualnej = RMVNet <br>
Przestrzeń adresowa = 192.168.0.0/16 <br>
Grupa zasobów = RG1 <br>
Lokalizacja = wschodnie stany USA <br>
Nazwa podsieci = 1 podsieci <br>
Zakres adresów = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nazwa bramy sieci wirtualnej = RMGateway <br>
Typ bramy sieci VPN = <br>
Typ sieci VPN = opartej na trasach <br>
SKU = VpnGw1 <br>
Lokalizacja = wschodnie stany USA <br>
Sieć wirtualna = RMVNet <br> (skojarzyć bramy sieci VPN w tej sieci wirtualnej) Pierwszy konfiguracji IP = rmgwpip <br> (bramy publiczny adres IP) Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa połączenia = RMtoClassic

### <a name="connectoverview"></a>Omówienie połączenia

Dla tej konfiguracji tworzenia połączenia bramy sieci VPN za pośrednictwem tunelu IPsec i IKE sieci VPN między sieciami wirtualnymi. Upewnij się, że żaden z zakresów w Twojej sieci wirtualnej nakłada się ze sobą lub za pomocą dowolnego z sieci lokalnej, które łączą się z.

W poniższej tabeli przedstawiono przykładowy sposób definiowania przykład sieci wirtualnych i witryn lokalnych:

| Virtual Network | Przestrzeń adresowa | Region | Nawiązuje połączenie z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Wschodnie stany USA |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Sekcja 1 — Konfigurowanie klasycznych ustawień sieci wirtualnej

W tej sekcji Tworzenie klasycznej sieci wirtualnej lokalnej sieci (lokalnej lokacji) i bramy sieci wirtualnej. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj zastąpić wartości własnym lub użyć [przykład](#values) wartości.

### 1. <a name="classicvnet"></a>Tworzenie klasycznej sieci wirtualnej

Jeśli nie masz klasycznej sieci wirtualnej i są uruchomione te kroki jako wykonywania, można utworzyć sieci wirtualnej przy użyciu [w tym artykule](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) i [przykład](#values) wartości ustawienia z powyżej.

Jeśli masz już sieć wirtualną z bramą sieci VPN, sprawdź, czy brama jest dynamiczny. Jeśli jest statyczna, należy najpierw usunąć bramy sieci VPN przed przejściem do [konfigurowania lokacji lokalnej](#local).

1. Otwórz witrynę [Azure Portal](https://ms.portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij przycisk **+ Utwórz zasób** aby otworzyć stronę "New".
3. W polu "Wyszukiwania portalu marketplace" typu "Sieci wirtualnej". Jeśli zamiast tego należy wybrać sieć -> Sieć wirtualna, nie otrzyma możliwość utworzenia klasycznego sieci wirtualnej.
4. Znajdź "Sieci wirtualnej" na liście zwracanych i kliknij go, aby otworzyć stronę sieci wirtualnej. 
5. Na stronie sieci wirtualnej wybierz 'Klasycznym' Tworzenie klasycznej sieci wirtualnej. Jeśli nie podejmiesz tutaj wartość domyślna, użytkownik będzie likwidacji z sieci wirtualnej Menedżera zasobów zamiast tego.

### 2. <a name="local"></a>Konfigurowanie lokacji lokalnej

1. Przejdź do **wszystkie zasoby** i Znajdź **ClassicVNet** na liście.
2. Na **omówienie** strony w **połączeń sieci VPN** kliknij **bramy** Aby utworzyć bramę.
  ![Konfigurowanie bramy sieci VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Brama sieci VPN")
3. Na **nowego połączenia sieci VPN** strony, dla **typ połączenia**, wybierz pozycję **lokacja lokacja**.
4. Aby uzyskać **lokacji lokalnej**, kliknij przycisk **Skonfiguruj wymagane ustawienia**. Spowoduje to otwarcie **lokacji lokalnej** strony.
5. Na **lokacji lokalnej** pozycję Utwórz nazwę umożliwiającą odwoływanie się do sieci wirtualnej Menedżera zasobów. Na przykład "RMVNetLocal".
6. Jeśli brama sieci VPN dla Menedżera zasobów sieci wirtualnej ma już do publicznego adresu IP, użyj wartości dla **adres IP bramy sieci VPN** pola. Jeśli wykonanie tych czynności jako wykonywania, lub nie masz jeszcze bramy sieci wirtualnej sieci wirtualnej Menedżera zasobów, można uzupełnić adres IP — symbol zastępczy. Upewnij się, że adres IP — symbol zastępczy używa prawidłowy format. Później Zastąp symbol zastępczy adres IP adres publiczny adres IP bramy sieci wirtualnej Menedżera zasobów.
7. Dla **przestrzeni adresowej klienta**, użyj [wartości](#connectoverview) dla adresu IP sieci wirtualnej obszary adresów sieci wirtualnej Menedżera zasobów. To ustawienie służy do określania przestrzeni adresowych przekierowujący do Menedżera zasobów sieci wirtualnej. W tym przykładzie używamy 192.168.0.0/16, zakres adresów dla RMVNet.
8. Kliknij przycisk **OK** zapisać wartości, a następnie wróć do **nowego połączenia sieci VPN** strony.

### <a name="classicgw"></a>3. Tworzenie bramy sieci wirtualnej

1. Na **nowego połączenia sieci VPN** wybierz pozycję **Utwórz bramę natychmiast** wyboru.
2. Kliknij pozycję **Opcjonalna konfiguracja bramy**, aby otworzyć stronę **Konfiguracja bramy**.

  ![Na stronie konfiguracji bramy Otwórz](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "strony konfiguracji bramy Otwórz")
3. Kliknij przycisk **podsieci — Skonfiguruj wymagane ustawienia** otworzyć **Dodaj podsieć** strony. **Nazwa** jest już skonfigurowana z wartością wymagane: **GatewaySubnet**.
4. **Zakres adresów** odwołuje się do zakresu podsieci bramy. Mimo że można utworzyć podsieci bramy z /29 (adresy 3), zakres adresów zaleca się utworzenie podsieć bramy, który zawiera więcej adresów IP. Pomieszczenie tylu przyszłych konfiguracje, które mogą wymagać więcej dostępnych adresów IP. Jeśli to możliwe Użyj /27 lub /28. Jeśli używasz tych kroków jako wykonywania, może się odwoływać do [przykładowe wartości](#values). W tym przykładzie używamy "10.0.0.32/28". Kliknij przycisk **OK** można utworzyć podsieci bramy.
5. Na **konfiguracji bramy** strony, **rozmiar** odwołuje się do jednostka SKU bramy. Wybierz jednostki SKU bramy dla bramy sieci VPN.
6. Sprawdź **typ routingu** jest **dynamiczne**, następnie kliknij przycisk **OK** aby powrócić do **nowego połączenia sieci VPN** strony.
7. Na **nowego połączenia sieci VPN** kliknij przycisk **OK** aby rozpocząć tworzenie bramy sieci VPN. Tworzenie bramy sieci VPN może potrwać do 45 minut.

### <a name="ip"></a>4. Skopiuj publiczny adres IP bramy sieci wirtualnej

Po utworzeniu bramy sieci wirtualnej, można wyświetlić adres IP bramy. 

1. Przejdź do klasycznej sieci wirtualnej, a następnie kliknij przycisk **omówienie**.
2. Kliknij przycisk **połączeń sieci VPN** aby otworzyć stronę połączenia sieci VPN. Na stronie połączeń sieci VPN można wyświetlić adres publiczny adres IP. Jest to adres publiczny adres IP przypisany do bramy sieci wirtualnej. Zanotuj adres IP. Można użyć go w kolejnych krokach podczas pracy z ustawieniami konfiguracji bramy sieci lokalnej usługi Resource Manager. 
3. Można wyświetlić stan połączenia bramy. Należy zauważyć, że lokacja sieci lokalnej, który został utworzony, jest wymienione jako "Łączenie". Stan zmieni się po utworzeniu połączenia. Możesz zamknąć tę stronę po zakończeniu przeglądania stanu.

## <a name="rmvnet"></a>Sekcja 2 — Konfigurowanie ustawień sieci wirtualnej Resource Manager

W tej sekcji utworzysz bramy sieci wirtualnej i Brama sieci lokalnej sieci wirtualnej Menedżera zasobów. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj zastąpić wartości własnym lub użyć [przykład](#values) wartości.

### <a name="1-create-a-virtual-network"></a>1. Tworzenie sieci wirtualnej

**Przykładowe wartości:**

* Nazwa sieci wirtualnej = RMVNet <br>
* Przestrzeń adresowa = 192.168.0.0/16 <br>
* Grupa zasobów = RG1 <br>
* Lokalizacja = wschodnie stany USA <br>
* Nazwa podsieci = 1 podsieci <br>
* Zakres adresów = 192.168.1.0/24 <br>


Jeśli nie masz Menedżera zasobów sieci wirtualnej i są uruchomione te kroki jako wykonywania, Utwórz sieć wirtualną kroków w [utworzyć sieć wirtualną](../virtual-network/quick-create-portal.md), przy użyciu przykładowe wartości.

### <a name="2-create-a-gateway-subnet"></a>2. Tworzenie podsieci bramy

**Przykładowa wartość:** GatewaySubnet = 192.168.0.0/26

Przed utworzeniem bramy sieci wirtualnej, należy najpierw utworzyć podsieć bramy. Utwórz podsieć bramy z licznikiem CIDR /28 i większych (/ 27, / 26, itp.). Jeśli tworzysz podczas wykonywania służy przykładowe wartości.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3. Tworzenie bramy sieci wirtualnej

**Przykładowe wartości:**

* Nazwa bramy sieci wirtualnej = RMGateway <br>
* Typ bramy sieci VPN = <br>
* Typ sieci VPN = opartej na trasach <br>
* SKU = VpnGw1 <br>
* Lokalizacja = wschodnie stany USA <br>
* Sieć wirtualna = RMVNet <br>
* Pierwszy konfiguracji IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Tworzenie bramy sieci lokalnej

**Przykładowe wartości:** bramy sieci lokalnej = ClassicVNetLocal

| Virtual Network | Przestrzeń adresowa | Region | Nawiązuje połączenie z lokacją sieci lokalnej |Adres publiczny adres IP bramy|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |Adres publiczny adres IP, który jest przypisany do bramy ClassicVNet|
| RMVNet | (192.168.0.0/16) |Wschodnie stany USA |ClassicVNetLocal (10.0.0.0/24) |Adres publiczny adres IP, który jest przypisany do bramy RMVNet.|

Brama sieci lokalnej określa zakres adresów, a adres publiczny adres IP skojarzony z klasycznej sieci wirtualnej i jej bramy sieci wirtualnej. Czynności te kroki jako wykonywania dotyczą przykładowe wartości.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Sekcja 3 - zmodyfikuj ustawienia klasycznego lokalnej lokacji sieci wirtualnej

W tej sekcji musisz zastąpić adres IP symbolu zastępczego, używanego podczas określania ustawień lokacji lokalnej przy użyciu adresu IP bramy sieci VPN usługi Resource Manager. Ta sekcja używa klasycznego poleceń cmdlet programu PowerShell (ko).

1. W portalu Azure przejdź do klasycznej sieci wirtualnej.
2. Na stronie dla sieci wirtualnej, kliknij przycisk **omówienie**.
3. W **połączeń sieci VPN** sekcji, kliknij nazwę witryny lokalnej na rysunku.

  ![Połączenia sieci VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "połączeń sieci VPN")
4. Na **połączenia sieci VPN typu lokacja lokacja** kliknij nazwę witryny.

  ![Nazwa witryny](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "nazwy lokacji lokalnych")
5. Na stronie połączenia dla witryny sieci lokalnej, kliknij nazwę lokacji lokalnej, aby otworzyć **lokacji lokalnej** strony.

  ![Otwórz lokalnej lokacji](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Otwórz lokacji lokalnej")
6. Na **lokacji lokalnej** strony, Zastąp **adres IP bramy sieci VPN** przy użyciu adresu IP bramy Menedżera zasobów.

  ![Adres ip bramy](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "adresu IP bramy")
7. Kliknij przycisk **OK** można zaktualizować adresu IP.

## <a name="RMtoclassic"></a>Sekcja 4 - tworzenie Menedżera zasobów klasycznych połączenia

W tych kroków Konfigurowanie połączenia z sieciami wirtualnymi Menedżera zasobów do klasycznej sieci wirtualnej przy użyciu portalu Azure.

1. W **wszystkie zasoby**, Znajdź bramę sieci lokalnej. W naszym przykładzie Brama sieci lokalnej jest **ClassicVNetLocal**.
2. Kliknij przycisk **konfiguracji** i sprawdź, czy wartość adresu IP bramy sieci VPN w klasycznej sieci wirtualnej. Aktualizacja, w razie potrzeby, a następnie kliknij przycisk **zapisać**. Zamknij stronę.
3. W **wszystkie zasoby**, kliknij przycisk Brama sieci lokalnej.
4. Kliknij przycisk **połączeń** aby otworzyć stronę połączenia.
5. Na **połączeń** kliknij przycisk **+** Aby dodać połączenie.
6. Na **Dodaj połączenie** pozycję nazwę połączenia. Na przykład "RMtoClassic".
7. **Lokacja-lokacja** jest już zaznaczone na tej stronie.
8. Wybierz bramę sieci wirtualnej, które chcesz skojarzyć z tą lokacją.
9. Utwórz **klucza wspólnego**. Ten klucz jest również używany w połączeniu utworzonego z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów. Można wygenerować klucz lub go utworzyć. W tym przykładzie używamy "abc123", ale można i powinien używasz bardziej złożonych.
10. Kliknij przycisk **OK** do utworzenia połączenia.

## <a name="classictoRM"></a>Sekcja 5 - Utwórz klasycznego do połączenia usługi Resource Manager

W tych kroków Konfigurowanie połączenia z klasycznej sieci wirtualnej z Menedżera zasobów sieci wirtualnej. Kroki te wymagają programu PowerShell. Nie można utworzyć tego połączenia w portalu. Upewnij się, że zostały pobrane i zainstalowane zarówno klasyczny (ko), jak i poleceń cmdlet programu PowerShell Menedżera zasobów (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Nawiąż połączenie z kontem platformy Azure

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i logowania do konta platformy Azure. Po zalogowaniu ustawienia konta są pobierane, tak aby były dostępne dla programu Azure PowerShell. Następujące polecenie cmdlet monituje o poświadczenia logowania dla konta platformy Azure dla modelu wdrażania usługi Resource Manager:

```powershell
Connect-AzureRmAccount
```

Pobierz listę subskrypcji platformy Azure.

```powershell
Get-AzureRmSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, która ma być używany.

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

Następnie zaloguj się przy użyciu poleceń cmdlet programu PowerShell klasyczny (Service Management). Aby dodać konta dla klasycznym modelu wdrażania platformy Azure, użyj następującego polecenia:

```powershell
Add-AzureAccount
```

Pobierz listę subskrypcji. Ten krok może być konieczne dodanie polecenia cmdlet usługi zarządzania, w zależności od modułu Azure instalowania.

```powershell
Get-AzureSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, która ma być używany.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Wyświetl wartości w pliku konfiguracji sieci

Po utworzeniu sieci wirtualnej w portalu Azure, pełna nazwa, która używa usługi Azure nie jest widoczne w portalu Azure. Na przykład sieci wirtualnej, który wydaje się być o nazwie "ClassicVNet" w portalu Azure może mieć wiele nazwę dłużej w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: "ClassicRG ClassicVNet grupy". W tych kroków Pobierz plik konfiguracji sieci i służy do wyświetlania wartości.

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik w edytorze tekstów i wyświetlić nazwę klasycznej sieci wirtualnej. Podczas uruchamiania polecenia cmdlet programu PowerShell, użyj nazw w pliku konfiguracji sieci.

- Nazwy sieci wirtualnej są wyświetlane jako **VirtualNetworkSite name =**
- Nazwy lokacji są wyświetlane jako **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. Tworzenie połączenia

Ustaw klucz udostępniony i Utwórz połączenie z klasycznej sieci wirtualnej do sieci wirtualnej Menedżera zasobów. Nie można ustawić klucza wspólnego za pomocą portalu. Upewnij się, że uruchomienie tych kroków, logując się przy użyciu poleceń cmdlet programu PowerShell za pośrednictwem klasycznego. Aby to zrobić, użyj **Add-AzureAccount**. W przeciwnym razie nie będzie możliwe ustawienie "-AzureVNetGatewayKey".

- W tym przykładzie **- VNetName** jest nazwą klasycznej sieci wirtualnej tak jak w pliku konfiguracji sieci. 
- **- LocalNetworkSiteName** nazwa określona dla lokacji lokalnej jako znajduje się w pliku konfiguracji sieci.
- **- SharedKey** to Generowanie i określ wartość. W tym przykładzie użyliśmy *abc123*, ale można generować bardziej złożonych. Ważne jest, że wartość określone w tym miejscu musi być taka sama jak wartość określone podczas tworzenia Menedżera zasobów klasycznych połączenia.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

##<a name="verify"></a>Sekcja 6 - Sprawdź połączenia

Połączenia można sprawdzić za pomocą portalu Azure lub programu PowerShell. Podczas sprawdzania poprawności, konieczne może być Zaczekaj minutę lub dwie co połączenie, jest tworzona. Gdy połączenie zostanie nawiązane, stan łączności zmieniona z "Łączenie" na "Połączony".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby sprawdzić połączenie z klasycznej sieci wirtualnej do sieci wirtualnej Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby sprawdzić połączenie z sieci wirtualnej Menedżera zasobów klasycznych sieci wirtualnej

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
