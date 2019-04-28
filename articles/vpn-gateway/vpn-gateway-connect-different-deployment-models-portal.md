---
title: 'Łączenie klasycznych sieci wirtualnych do sieci wirtualnych Menedżera zasobów platformy Azure: Portal | Microsoft Docs'
description: Kroki, aby połączyć z klasycznymi sieciami wirtualnymi z sieciami wirtualnymi usługi Resource Manager przy użyciu bramy sieci VPN i portalu
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/17/2018
ms.author: cherylmc
ms.openlocfilehash: bf7d80bbbe63204cda47719a7d7c019013ad800b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124033"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Łączenie sieci wirtualnych z różnych modeli wdrażania za pomocą portalu

W tym artykule przedstawiono sposób nawiązywania połączeń z sieciami wirtualnymi usługi Resource Manager, aby zezwolić na zasoby znajdujące się w oddzielnych modelach wdrażania do komunikowania się ze sobą klasycznych sieci wirtualnych. Kroki opisane w tym artykule przede wszystkim używasz witryny Azure portal, ale można również utworzyć tę konfigurację przy użyciu programu PowerShell, wybierając w artykule z tej listy.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Łączenie klasycznej sieci wirtualnej z siecią wirtualną usługi Resource Manager jest podobny do procesu łączenia sieci z lokacją lokalną. Oba typy połączeń wykorzystują bramę sieci VPN, aby zapewnić bezpieczny tunel z użyciem protokołu IPsec/IKE. Można utworzyć połączenia między sieciami wirtualnymi, które są w różnych subskrypcjach i w różnych regionach. Można też połączyć sieci wirtualnych, które mają już połączenia z sieciami lokalnymi, tak długo, jak bramy, które zostały skonfigurowane jest dynamiczny lub oparte na trasach. Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#faq) na końcu tego artykułu. 

Jeśli nie masz jeszcze bramy sieci wirtualnej, a nie chcesz utworzyć, warto zamiast tego warto rozważyć połączenie sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych. W przypadku komunikacji równorzędnej sieci wirtualnych nie jest używana brama sieci VPN. Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

### <a name="before"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Te czynności zakładają, że obie sieci wirtualne zostały już utworzone. Jeśli używasz w tym artykule w charakterze ćwiczenia, nie ma sieci wirtualne istnieją połączenia w krokach, które ułatwią Ci je utworzyć.
* Sprawdź, czy zakresy adresów dla sieci wirtualnych nie pokrywają się ze sobą lub pokrywać się z żadnym z zakresów dla innych połączeń, które mogą być połączone bramy z.
* Zainstaluj najnowsze polecenia cmdlet programu PowerShell dla usługi Resource Manager i Service Management (wersja klasyczna). W tym artykule używamy zarówno w witrynie Azure portal, jak i programu PowerShell. Wymagany jest program PowerShell, aby utworzyć połączenie z klasyczną siecią wirtualną z siecią wirtualną usługi Resource Manager. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). 

### <a name="values"></a>Przykładowe ustawienia

Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule.

**Klasyczna sieć wirtualna**

Nazwa sieci wirtualnej = ClassicVNet <br>
Przestrzeń adresowa = 10.0.0.0/24 <br>
Nazwa podsieci Subnet-1 = <br>
Zakres adresów podsieci = 10.0.0.0/27 <br>
Subskrypcja = subskrypcji, w której chcesz użyć <br>
Grupa zasobów = ClassicRG <br>
Lokalizacja = zachodnie stany USA <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokacja lokalna = RMVNetLocal <br>

**Sieć wirtualna Menedżera zasobów**

Nazwa sieci wirtualnej = RMVNet <br>
Przestrzeń adresowa = 192.168.0.0/16 <br>
Grupa zasobów = RG1 <br>
Lokalizacja = wschodnie stany USA <br>
Nazwa podsieci Subnet-1 = <br>
Zakres adresów = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nazwa bramy sieci wirtualnej = RMGateway <br>
Typ bramy sieci VPN = <br>
Typ sieci VPN = oparte na trasach <br>
SKU = VpnGw1 <br>
Lokalizacja = wschodnie stany USA <br>
Sieć wirtualna = RMVNet <br> (skojarzenia bramy sieci VPN w tej sieci wirtualnej) Konfiguracja pierwszego adresu IP = rmgwpip <br> (publiczny adres IP bramy) Brama sieci lokalnej = ClassicVNetLocal <br>
Nazwa połączenia = RMtoClassic

### <a name="connectoverview"></a>Przegląd połączenia

Dla tej konfiguracji tworzenia połączenia bramy sieci VPN nawiązywane za pośrednictwem tunelu VPN IPsec/IKE między sieciami wirtualnymi. Upewnij się, że żaden z zakresów w Twojej sieci wirtualnej nakłada się ze sobą lub z żadną z sieci lokalnej, które łączą się z.

W poniższej tabeli przedstawiono przykładowy sposób definiowania przykład sieci wirtualnych i lokacji lokalnych:

| Virtual Network | Przestrzeń adresowa | Region | Nawiązanie połączenia z lokacją sieci lokalnej |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Wschodnie stany USA |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Sekcja 1 — Konfigurowanie ustawień klasycznej sieci wirtualnej

W tej sekcji utworzysz klasycznej sieci wirtualnej, lokalnej sieci (lokalnej witryny) i bramy sieci wirtualnej. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj zastąpić wartości własnymi, lub użyj [przykład](#values) wartości.

### 1. <a name="classicvnet"></a>Tworzenie klasycznej sieci wirtualnej

Jeśli nie masz klasyczną sieć wirtualną i działają tych kroków w charakterze ćwiczenia, można utworzyć sieć wirtualną przy użyciu [w tym artykule](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) i [przykład](#values) wartości ustawień z góry.

Jeśli masz już sieć wirtualną z bramą sieci VPN, sprawdź, czy brama jest dynamiczny. Jeśli jest statyczna, należy najpierw usunąć bramę sieci VPN przed przystąpieniem do [Konfigurowanie lokacji lokalnej](#local).

1. Otwórz witrynę [Azure Portal](https://ms.portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.
2. Kliknij przycisk **+ Utwórz zasób** o otwarcie strony "New".
3. W polu "Wyszukaj w witrynie marketplace" wpisz "Sieć wirtualna". Jeśli zamiast tego należy wybrać sieć -> Sieć wirtualna, nie otrzymasz opcję, aby utworzyć klasyczną sieć wirtualną.
4. Znajdź "Sieci wirtualnej" na liście zwrócone i kliknij go, aby otworzyć stronę sieci wirtualnej. 
5. Na stronie sieci wirtualnej wybierz pozycję "Classic", aby utworzyć klasyczną sieć wirtualną. Jeśli nie podejmiesz tutaj domyślne, użytkownik będzie likwidacji z siecią wirtualną Menedżera zasobów zamiast tego.

### 2. <a name="local"></a>Konfigurowanie lokacji lokalnej

1. Przejdź do **wszystkie zasoby** i Znajdź **ClassicVNet** na liście.
2. Na **Przegląd** strony w **połączeń sieci VPN** kliknij **bramy** utworzyć bramę.
  ![Konfigurowanie bramy sieci VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Konfigurowanie bramy sieci VPN")
3. Na **nowe połączenie VPN** strony dla **typu połączenia**, wybierz opcję **Site-to-site**.
4. Aby uzyskać **lokacja lokalna**, kliknij przycisk **Skonfiguruj wymagane ustawienia**. Spowoduje to otwarcie **lokacja lokalna** strony.
5. Na **lokacja lokalna** strony, należy utworzyć nazwy do odwoływania się do sieci wirtualnej usługi Resource Manager. Na przykład "RMVNetLocal".
6. Jeśli brama sieci VPN dla sieci wirtualnej usługi Resource Manager jest już publicznego adresu IP, użyj wartości dla **adres IP bramy sieci VPN** pola. Jeśli podczas wykonywania tych kroków w charakterze ćwiczenia lub jeszcze nie masz bramy sieci wirtualnej dla sieci wirtualnej usługi Resource Manager, można uzupełnić zastępczego adresu IP. Upewnij się, że zastępczego adresu IP używa prawidłowy format. Później należy zamienić zastępczego adresu IP publiczny adres IP bramy sieci wirtualnej usługi Resource Manager.
7. Aby uzyskać **przestrzeń adresowa klienta**, użyj [wartości](#connectoverview) dla adresu IP sieci wirtualnej przestrzenie adresowe sieci wirtualnej usługi Resource Manager. To ustawienie służy do określania przestrzeni adresów, aby skierować do sieci wirtualnej usługi Resource Manager. W tym przykładzie używamy 192.168.0.0/16, zakres adresów dla RMVNet.
8. Kliknij przycisk **OK** można zapisać wartości i powrócić do **nowe połączenie VPN** strony.

### <a name="classicgw"></a>3. Tworzenie bramy sieci wirtualnej

1. Na **nowe połączenie VPN** wybierz opcję **Utwórz bramę natychmiast** pola wyboru.
2. Kliknij pozycję **Opcjonalna konfiguracja bramy**, aby otworzyć stronę **Konfiguracja bramy**.

   ![Strona konfiguracji bramy Otwórz](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "stronie konfiguracji bramy Otwórz")
3. Kliknij przycisk **podsieć — Skonfiguruj wymagane ustawienia** otworzyć **Dodaj podsieć** strony. **Nazwa** ma już skonfigurowaną wymagana wartość: **GatewaySubnet**.
4. **Zakres adresów** odwołuje się do zakresu podsieci bramy. Chociaż można utworzyć mniejszą podsieć bramy o rozmiarze/29 (adres 3), zakres adresów zaleca się utworzenie podsieci bramy, która zawiera więcej adresów IP. Będzie to dostosowana przyszłe konfiguracje, które mogą wymagać więcej dostępnych adresów IP. Jeśli jest to możliwe używaj wartość/27 lub/28. Jeśli używasz tych kroków w charakterze ćwiczenia, można zajrzeć do [przykładowe wartości](#values). W tym przykładzie używamy "10.0.0.32/28". Kliknij przycisk **OK** utworzyć podsieć bramy.
5. Na **konfiguracji bramy** stronie **rozmiar** odwołuje się do jednostki SKU bramy. Wybierz jednostkę SKU bramy dla bramy sieci VPN.
6. Sprawdź **typu routingu** jest **dynamiczne**, następnie kliknij przycisk **OK** aby powrócić do **nowe połączenie VPN** strony.
7. Na **nowe połączenie VPN** kliknij **OK** aby rozpocząć tworzenie bramy sieci VPN. Tworzenie bramy sieci VPN może potrwać do 45 minut.

### <a name="ip"></a>4. Skopiuj publiczny adres IP bramy sieci wirtualnej

Po utworzeniu bramy sieci wirtualnej można wyświetlić adres IP bramy. 

1. Przejdź do klasycznej sieci wirtualnej, a następnie kliknij przycisk **Przegląd**.
2. Kliknij przycisk **połączeń sieci VPN** aby otworzyć stronę połączenia sieci VPN. Na stronie połączenia sieci VPN można wyświetlić publiczny adres IP. Jest to publiczny adres IP przypisany do bramy sieci wirtualnej. Zanotuj adres IP. Możesz użyć go w kolejnych krokach podczas pracy z ustawień konfiguracji bramy sieci lokalnej usługi Resource Manager. 
3. Można wyświetlić stan połączenia bramy. Zwróć uwagę, że lokacja sieci lokalnej, który został utworzony, jest wyświetlany jako "Łączenie". Stan zmieni się po utworzeniu połączenia. Możesz zamknąć tę stronę po zakończeniu, wyświetlając stan.

## <a name="rmvnet"></a>Sekcja 2 — Konfigurowanie ustawień sieci wirtualnej usługi Resource Manager

W tej sekcji utworzysz bramy sieci wirtualnej i bramy sieci lokalnej sieci wirtualnej usługi Resource Manager. Zamieszczone zrzuty ekranu są przykładowe. Pamiętaj zastąpić wartości własnymi, lub użyj [przykład](#values) wartości.

### <a name="1-create-a-virtual-network"></a>1. Tworzenie sieci wirtualnej

**Przykładowe wartości:**

* Nazwa sieci wirtualnej = RMVNet <br>
* Przestrzeń adresowa = 192.168.0.0/16 <br>
* Grupa zasobów = RG1 <br>
* Lokalizacja = wschodnie stany USA <br>
* Nazwa podsieci Subnet-1 = <br>
* Zakres adresów = 192.168.1.0/24 <br>


Jeśli nie masz sieci wirtualnej usługi Resource Manager i systemem tych kroków w charakterze ćwiczenia, Utwórz sieć wirtualną z krokami w [tworzenie sieci wirtualnej](../virtual-network/quick-create-portal.md), przy użyciu przykładowych wartości.

### <a name="2-create-a-gateway-subnet"></a>2. Tworzenie podsieci bramy

**Przykładowa wartość:** GatewaySubnet = 192.168.0.0/26

Przed utworzeniem bramy sieci wirtualnej, należy najpierw utworzyć podsieć bramy. Tworzenie podsieci bramy z liczbą CIDR/28 i większych (/ 27, / 26, itp.). Jeśli tworzysz jej w ramach ćwiczenia, można użyć przykładowych wartości.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="creategw"></a>3. Tworzenie bramy sieci wirtualnej

**Przykładowe wartości:**

* Nazwa bramy sieci wirtualnej = RMGateway <br>
* Typ bramy sieci VPN = <br>
* Typ sieci VPN = oparte na trasach <br>
* SKU = VpnGw1 <br>
* Lokalizacja = wschodnie stany USA <br>
* Sieć wirtualna = RMVNet <br>
* Konfiguracja pierwszego adresu IP = rmgwpip <br>

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>4. Tworzenie bramy sieci lokalnej

**Przykładowe wartości:** Brama sieci lokalnej = ClassicVNetLocal

| Virtual Network | Przestrzeń adresowa | Region | Nawiązanie połączenia z lokacją sieci lokalnej |Adres publiczny adres IP bramy|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Zachodnie stany USA | RMVNetLocal (192.168.0.0/16) |Publiczny adres IP przypisany do bramy ClassicVNet|
| RMVNet | (192.168.0.0/16) |Wschodnie stany USA |ClassicVNetLocal (10.0.0.0/24) |Publiczny adres IP przypisany do bramy RMVNet.|

Brama sieci lokalnej określa zakres adresów i publiczny adres IP skojarzony z klasycznej sieci wirtualnej i bramy sieci wirtualnej. Jeśli przeprowadzasz tych kroków w charakterze ćwiczenia, można znaleźć przykładowe wartości.

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Sekcja 3 — modyfikowanie klasyczne ustawienia sieci wirtualnej w lokacji lokalnej

W tej sekcji możesz zamiana zastępczego adresu IP, która została użyta podczas określania ustawień lokacji lokalnej, za pomocą adresu IP bramy sieci VPN usługi Resource Manager. Ta sekcja używa klasycznego poleceń cmdlet programu PowerShell (SM).

1. W witrynie Azure portal przejdź do klasycznej sieci wirtualnej.
2. Na stronie Twojej sieci wirtualnej, kliknij przycisk **Przegląd**.
3. W **połączeń sieci VPN** kliknij nazwę lokacji lokalnej do grafiki.

   ![Połączenia sieci VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "połączeń sieci VPN")
4. Na **połączeń sieci VPN typu lokacja lokacja** kliknij nazwę witryny.

   ![Nazwa witryny](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "nazwy lokacji lokalnej")
5. Na stronie połączenia lokacji lokalnej, kliknij nazwę lokacji lokalnej, aby otworzyć **lokacja lokalna** strony.

   ![Otwórz lokalnej lokacji](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Otwórz lokacji lokalnej")
6. Na **lokacja lokalna** strony, Zastąp **adres IP bramy sieci VPN** przy użyciu adresu IP bramy usługi Resource Manager.

   ![Adres ip bramy](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "adres IP bramy")
7. Kliknij przycisk **OK** można zaktualizować adresu IP.

## <a name="RMtoclassic"></a>Sekcja 4 — Tworzenie usługi Resource Manager do klasycznego połączenia

W ramach tej procedury skonfigurujesz połączenie z sieci wirtualnej usługi Resource Manager do klasycznej sieci wirtualnej, przy użyciu witryny Azure portal.

1. W **wszystkie zasoby**, Znajdź bramę sieci lokalnej. W tym przykładzie Brama sieci lokalnej jest **ClassicVNetLocal**.
2. Kliknij przycisk **konfiguracji** i sprawdź, czy wartość adresu IP bramy sieci VPN dla klasycznej sieci wirtualnej. Aktualizuj, jeśli to konieczne, a następnie kliknij przycisk **Zapisz**. Zamknij stronę.
3. W **wszystkie zasoby**, kliknij bramę sieci lokalnej.
4. Kliknij przycisk **połączeń** aby otworzyć stronę połączenia.
5. Na **połączeń** kliknij **+** Dodaj połączenie.
6. Na **Dodaj połączenie** strony, określ nazwę połączenia. Na przykład "RMtoClassic".
7. **Site-to-Site** na tej stronie jest zaznaczona.
8. Wybierz bramę sieci wirtualnej, który chcesz skojarzyć z tą lokacją.
9. Tworzenie **klucz współużytkowany**. Ten klucz jest również używany w połączeniu utworzonym z klasyczną siecią wirtualną z siecią wirtualną usługi Resource Manager. Można wygenerować klucz lub utworzyć. W naszym przykładzie używamy wartości "abc123", ale można i należy użyć bardziej złożonej.
10. Kliknij przycisk **OK** do utworzenia połączenia.

## <a name="classictoRM"></a>Sekcja 5 — Tworzenie klasycznego połączenia usługi Resource Manager

W ramach tej procedury skonfigurujesz połączenie z klasyczną siecią wirtualną z siecią wirtualną usługi Resource Manager. Te kroki wymagają programu PowerShell. Nie można utworzyć tego połączenia w portalu. Upewnij się, że zostały pobrane i zainstalowane zarówno klasyczny (SM), jak i poleceń cmdlet programu PowerShell Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Nawiąż połączenie z kontem platformy Azure

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i zaloguj się do konta platformy Azure. Po zalogowaniu się w ustawieniach konta zostaną pobrane tak, aby były dostępne dla programu Azure PowerShell. Następujące polecenie cmdlet wyświetli monit o podanie poświadczeń logowania do konta platformy Azure dla modelu wdrażania usługi Resource Manager:

```powershell
Connect-AzAccount
```

Pobierz listę subskrypcji platformy Azure.

```powershell
Get-AzSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, dla której chcesz użyć.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Następnie zaloguj się za pomocą klasycznego poleceń cmdlet programu PowerShell (Zarządzanie usługami). Aby dodać swoje konto platformy Azure dla klasycznego modelu wdrażania, użyj następującego polecenia:

```powershell
Add-AzureAccount
```

Zostanie wyświetlona lista subskrypcji. Ten krok może być konieczne dodanie polecenia cmdlet zarządzania usługami, w zależności od platformy Azure modułu instalowania.

```powershell
Get-AzureSubscription
```

Jeśli masz więcej niż jedną subskrypcję, określ subskrypcję, dla której chcesz użyć.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Wyświetl wartości w pliku konfiguracji sieci

Po utworzeniu sieci wirtualnej w witrynie Azure portal, pełna nazwa, która używa usługi Azure nie jest widoczna w witrynie Azure portal. Na przykład sieci wirtualnej, który wydaje się być o nazwie "ClassicVNet" w witrynie Azure portal może mieć wiele dłuższej nazwy w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: "Group ClassicRG ClassicVNet". W tych krokach, Pobierz plik konfiguracji sieci i służy do wyświetlania wartości.

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik w edytorze tekstu i Wyświetl nazwę klasycznej sieci wirtualnej. Użyj nazw w pliku konfiguracji sieci, podczas uruchamiania poleceń cmdlet programu PowerShell.

- Nazwy sieci wirtualnej są wyświetlane jako **VirtualNetworkSite name =**
- Nazwy lokacji są wyświetlane jako **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. Tworzenie połączenia

Ustaw klucz współużytkowany i Utwórz połączenie z klasyczną siecią wirtualną z siecią wirtualną usługi Resource Manager. Nie można ustawić klucza wstępnego za pomocą portalu. Upewnij się, że uruchomieniu tych kroków po zalogowaniu się przy użyciu klasycznej wersji poleceń cmdlet programu PowerShell. Aby to zrobić, należy użyć **Add-AzureAccount**. W przeciwnym razie nie będzie możliwe ustawienie "-AzureVNetGatewayKey".

- W tym przykładzie **- VNetName** to nazwa klasycznej sieci wirtualnej jako znalezionych w pliku konfiguracji sieci. 
- **- LocalNetworkSiteName** nazwa określona dla witryny lokalnej, co znajduje się w pliku konfiguracji sieci.
- **- SharedKey** jest wartością, która jest generowana i określana. W tym przykładzie użyliśmy *abc123*, ale możesz generować bardziej złożonych. Ważne jest, że wartość podana w tym miejscu musi być taka sama jak wartość określone podczas tworzenia usługi Resource Manager do klasycznego połączenia.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="verify"></a>Sekcja 6 — Sprawdź połączenia

Połączenia można sprawdzić za pomocą witryny Azure portal lub programu PowerShell. Podczas sprawdzania, może być konieczne odczekanie minutę lub dwie połączenie jest tworzona. Gdy połączenie zostanie nawiązane, stan łączności zmienia się z "Łączenie" na "Połączone".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Aby sprawdzić połączenie z klasycznej sieci wirtualnej do sieci wirtualnej usługi Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Aby sprawdzić połączenie z siecią wirtualną usługi Resource Manager do klasycznej sieci wirtualnej

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
