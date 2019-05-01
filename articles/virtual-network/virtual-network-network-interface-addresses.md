---
title: Skonfiguruj adresy IP dla interfejsu sieci platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodawanie, zmienianie i usuwanie prywatnych i publicznych adresów IP dla interfejsu sieciowego.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: a6635b811dfa9c46facfffee1c57b2871cb4c738
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64719707"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Dodawanie, zmienianie lub usuwanie adresów IP dla interfejsu sieci platformy Azure

Dowiedz się, jak dodawanie, zmienianie i usuwanie prywatnych i publicznych adresów IP dla interfejsu sieciowego. Prywatne adresy IP przypisane do karty sieciowej, Włącz maszynę wirtualną do komunikowania się z innymi zasobami w sieci wirtualnej platformy Azure i połączonych sieciach. Prywatny adres IP umożliwia także komunikacji wychodzącej w Internecie przy użyciu adresu IP nieprzewidywalne. A [publiczny adres IP](virtual-network-public-ip-address.md) przypisane do sieci interfejs umożliwia komunikacji przychodzącej na maszynę wirtualną z Internetu. Adres umożliwia także komunikacji wychodzącej z maszyny wirtualnej w Internecie przy użyciu adresu IP przewidywalne. Aby uzyskać więcej informacji, zobacz [informacje o połączeniach wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Jeśli potrzebujesz do tworzenia, zmienić lub usunąć interfejsu sieciowego, zapoznaj się z [zarządzania interfejsem sieciowym](virtual-network-network-interface.md) artykułu. Jeśli chcesz dodać interfejsy sieciowe do lub usuwanie interfejsów sieciowych z maszyny wirtualnej, przeczytaj [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md) artykułu.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [sieci Interfejs uprawnienia](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Dodaj adresy IP

Można dodać tyle [prywatnej](#private) i [publicznych](#public) [IPv4](#ipv4) adresy zgodnie z potrzebami do interfejsu sieciowego, w ramach limitów na liście [limitówplatformyAzure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artykułu. Nie można użyć portalu, aby dodać adres IPv6 do istniejącego interfejsu sieciowego (chociaż można dodać z prywatnym adresem IPv6 interfejsu sieciowego, podczas tworzenia interfejsu sieciowego, można użyć portalu). Można użyć programu PowerShell lub interfejsu wiersza polecenia, można dodać z prywatnym adresem IPv6 do jednego [konfiguracji pomocniczego adresu IP](#secondary) (o ile nie ma żadnych istniejących konfiguracje pomocniczych adresów IP) dla istniejącego interfejsu sieciowego, który nie jest dołączony do maszyny wirtualnej. Nie można użyć dowolnego narzędzia, aby dodać publiczny adres IPv6 do interfejsu sieciowego. Zobacz [IPv6](#ipv6) szczegółowe informacje na temat przy użyciu adresów IPv6.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, aby dodać adres IPv4 z listy.
3. W obszarze **ustawienia**, wybierz opcję **konfiguracje adresów IP**.
4. W obszarze **konfiguracje adresów IP**, wybierz opcję **+ Dodaj**.
5. Podaj następujące informacje, a następnie wybierz **OK**:

   |Ustawienie|Wymagana?|Szczegóły|
   |---|---|---|
   |Name (Nazwa)|Yes|Musi być unikatowa dla interfejsu sieciowego|
   |Type|Yes|Ponieważ dodajesz konfigurację adresu IP do istniejącego interfejsu sieciowego, a każdy interfejs sieciowy musi mieć [podstawowego](#primary) jest jedynym rozwiązaniem Konfiguracja protokołu IP **dodatkowej**.|
   |Metoda przypisania adresu prywatnego adresu IP|Yes|[**Dynamic**](#dynamic): Platforma Azure przypisuje następny dostępny adres dla zakresu adresów podsieci, wdrożonej w interfejsie sieciowym. [**Statyczne**](#static): Możesz przypisać nieużywany adres dla zakresu adresów podsieci, wdrożonej w interfejsie sieciowym.|
   |Publiczny adres IP|Nie|**Wyłączone:** Nie zasobu publicznego adresu IP jest obecnie skojarzony z konfiguracją adresów IP. **Włączone:** Wybierz istniejący adres IPv4 publiczny adres IP lub Utwórz nową. Aby dowiedzieć się, jak utworzyć publiczny adres IP, przeczytaj [publiczne adresy IP](virtual-network-public-ip-address.md#create-a-public-ip-address) artykułu.|
6. Ręcznie dodaj pomocnicze prywatne adresy IP do systemu operacyjnego maszyny wirtualnej, wykonując instrukcje [przypisać wiele adresów IP do maszyn wirtualnych systemów operacyjnych](virtual-network-multiple-ip-addresses-portal.md#os-config) artykułu. Zobacz [prywatnej](#private) adresów IP dla szczególnych kwestii przed ręcznego dodawania adresów IP do systemu operacyjnego maszyny wirtualnej. Nie należy dodawać żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Tool|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Zmień ustawienia adresu IP

Może potrzebujesz zmienić metodę przypisywania adresu IPv4 zmień statyczny adres IPv4 lub zmienić publiczny adres IP przypisany do interfejsu sieciowego. Jeśli zmieniasz prywatny adres IPv4 w pomocniczej konfiguracji adresu IP skojarzone z pomocniczego interfejsu sieciowego w maszynie wirtualnej (Dowiedz się więcej o [interfejsów sieciowych podstawowych i pomocniczych](virtual-network-network-interface-vm.md)), umieść maszynę wirtualną do zatrzymania (przydział zostanie cofnięty) stan przed wykonaniem następujących czynności:

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz wyświetlić lub zmienić ustawienia adresu IP z listy.
3. W obszarze **ustawienia**, wybierz opcję **konfiguracje adresów IP**.
4. Wybierz konfigurację adresu IP, który chcesz zmodyfikować z listy.
5. Zmienić ustawienia, zgodnie z potrzebami, korzystając z informacji o ustawieniach w kroku 5 [Dodaj konfigurację adresu IP](#add-ip-addresses).
6. Wybierz pozycję **Zapisz**.

>[!NOTE]
>Jeśli podstawowy interfejs sieciowy ma wiele konfiguracji adresów IP i zmienić prywatnego adresu IP w konfiguracji podstawowego adresu IP, należy ręcznie ponownie przypisać podstawowych i pomocniczych adresów IP do interfejsu sieciowego w ramach Windows (niewymagane w przypadku systemu Linux) . Ręczne ich przypisywanie adresów IP do interfejsu sieciowego w ramach systemu operacyjnego, zobacz [przypisać wiele adresów IP do maszyn wirtualnych](virtual-network-multiple-ip-addresses-portal.md#os-config). Specjalne uwagi przed ręcznego dodawania adresów IP do systemu operacyjnego maszyny wirtualnej można znaleźć [prywatnej](#private) adresów IP. Nie należy dodawać żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Tool|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ sieci nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Usuń adresy IP

Możesz usunąć [prywatnej](#private) i [publicznych](#public) adresy IP z interfejsem sieciowym, ale interfejs sieciowy zawsze musi mieć co najmniej jeden prywatny adres IPv4 przypisany do niego.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz usunąć adresy IP z listy.
3. W obszarze **ustawienia**, wybierz opcję **konfiguracje adresów IP**.
4. Po prawej stronie wybierz [dodatkowej](#secondary) konfigurację adresu IP (nie można usunąć [podstawowego](#primary) konfiguracji), którą chcesz usunąć, wybierz pozycję **Usuń**, a następnie wybierz  **Tak**, aby potwierdzić usunięcie. Jeśli konfiguracja była publicznego zasobu adresu IP, powiązany, zasobu jest oddzielona od konfigurację adresu IP, ale zasób nie zostanie usunięta.

**Polecenia**

|Tool|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ sieci nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Konfiguracje adresów IP

[Prywatne](#private) i (opcjonalnie) [publicznych](#public) adresy IP są przypisywane do co najmniej jedna konfiguracja adresu IP, przypisany do interfejsu sieciowego. Istnieją dwa rodzaje konfiguracji adresu IP:

### <a name="primary"></a>Podstawowy

Każdy interfejs sieciowy jest przypisany jeden podstawowa konfiguracja adresu IP. Podstawowa konfiguracja adresu IP:

- Ma [prywatnej](#private) [IPv4](#ipv4) adres przypisany do niego. Nie można przydzielić prywatnej [IPv6](#ipv6) adres, aby podstawowa konfiguracja adresu IP.
- Mogą także mieć [publicznych](#public) adres IPv4 przypisany do niego. Nie można przypisać publiczny adres IPv6 w podstawowej lub pomocniczej konfiguracji adresu IP. Jednak możesz przypisać publiczny adres IPv6 do usługi Azure load balancer, która może równoważyć obciążenie na maszynie wirtualnej prywatny adres IPv6. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje i ograniczenia dotyczące IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Pomocniczy

Oprócz podstawowa konfiguracja adresu IP interfejsu sieciowego może mieć zero lub więcej konfiguracje pomocniczych adresów IP przypisanych do niego. Konfiguracji pomocniczego adresu IP:

- Musi mieć prywatny adres IPv4 lub IPv6 przypisany do niego. Jeśli adres IPv6, interfejs sieciowy może mieć tylko jedną konfigurację adresu IP pomocniczego. Jeśli adres IPv4, interfejs sieciowy może mieć wielu konfiguracji adresu IP pomocniczego do niej przypisany. Aby dowiedzieć się więcej o ile prywatnych i publicznych adresów IPv4, które można przypisać do interfejsu sieciowego, zobacz [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artykułu.
- Może mieć publiczny adres IPv4 przypisany do, jeśli prywatnego adresu IP protokołu IPv4. Prywatny adres IP w przypadku protokołu IPv6, nie można przypisać publiczny adres IPv4 lub IPv6 z konfiguracją adresów IP. Przypisywanie wielu adresów IP do interfejsu sieciowego jest, przydatne w scenariuszach takich jak:
  - Hostowanie wielu witryn sieci Web lub usług z różnymi adresami IP i certyfikatami SSL na jednym serwerze.
  - Maszyna wirtualna, służąc jako wirtualne urządzenie sieciowe, takie jak Zapora lub moduł równoważenia obciążenia.
  - Możliwość dodawania dowolnego z prywatnych adresów IPv4 dla wszystkich interfejsów sieciowych do puli zaplecza modułu równoważenia obciążenia platformy Azure. W przeszłości tylko podstawowy adres IPv4 dla interfejsu sieci podstawowej można dodać do puli zaplecza. Aby dowiedzieć się więcej na temat sposobu równoważenia obciążenia wielu konfiguracji protokołu IPv4, zobacz [wielu konfiguracji adresu IP Równoważenie obciążenia](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu. 
  - Możliwość ładowania saldo jeden adres IPv6 przypisane do karty sieciowej. Aby dowiedzieć się więcej o tym, jak załadować saldo z prywatnym adresem IPv6, zobacz [adresy IPv6 Równoważenie obciążenia](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu.

## <a name="address-types"></a>Typy adresów

Można przypisać następujące typy adresów IP, aby [konfigurację adresu IP](#ip-configurations):

### <a name="private"></a>Private

Prywatne [IPv4](#ipv4) adresy Włącz maszynę wirtualną do komunikowania się z innymi zasobami w sieci wirtualnej lub innych połączonych sieciach. Maszyna wirtualna nie może być przekazywane przychodzący do, ani można maszyny wirtualnej komunikują się ruchu wychodzącego z prywatnej [IPv6](#ipv6) adres, z jednym wyjątkiem. Maszyny wirtualne mogą komunikować się z modułem równoważenia obciążenia platformy Azure przy użyciu adresu IPv6. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje i ograniczenia dotyczące IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

Domyślnie, serwery Azure DHCP przypisać prywatny adres IPv4 dla [podstawowa konfiguracja adresu IP](#primary) interfejsu sieci platformy Azure z interfejsem sieciowym, w ramach systemu operacyjnego maszyny wirtualnej. Jeśli to konieczne, należy nigdy ręcznie ustawić adres IP interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej.

> [!WARNING]
> Jeśli adres IPv4 jest ustawiony jako podstawowy adres IP interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej kiedykolwiek różni się od prywatny adres IPv4 przypisany do podstawowa konfiguracja adresów IP głównego interfejsu sieciowego dołączonego do maszyny wirtualnej w ramach platformy Azure możesz utracić łączność z maszyną wirtualną.

Istnieją scenariusze, w którym należy ręcznie ustawić adres IP interfejsu sieciowego w ramach systemu operacyjnego maszyny wirtualnej. Ręcznie należy na przykład ustawić adresy IP podstawowych i pomocniczych, systemu operacyjnego Windows, podczas dodawania wielu adresów IP do maszyny wirtualnej platformy Azure. Dla maszyny wirtualnej systemu Linux mogą tylko należy ręcznie ustawić pomocniczych adresów IP. Zobacz [adresy IP Dodaj do systemu operacyjnego maszyny Wirtualnej](virtual-network-multiple-ip-addresses-portal.md#os-config) Aby uzyskać szczegółowe informacje. Jeśli trzeba będzie zmienić adres przypisany do konfiguracji adresu IP, zalecane jest, możesz:

1. Upewnij się, że maszyna wirtualna otrzymuje adres z serwerów DHCP w usłudze Azure. Po utworzeniu, zmienić przypisanie adresu IP protokołu DHCP w systemie operacyjnym i uruchom ponownie maszynę wirtualną.
2. Zatrzymaj (Cofnij ich przydział) maszyny wirtualnej.
3. Zmień adres IP dla konfiguracji IP w obrębie platformy Azure.
4. Uruchamia maszynę wirtualną.
5. [Ręczne konfigurowanie](virtual-network-multiple-ip-addresses-portal.md#os-config) pomocniczych adresów IP w ramach systemu operacyjnego (a także podstawowego adresu IP w obrębie Windows) do dopasowania, ustaw w obrębie platformy Azure.

Postępując zgodnie z poprzednich kroków, prywatny adres IP przypisany do interfejsu sieciowego w systemie Azure i w ramach systemu operacyjnego maszyny wirtualnej, pozostają takie same. Aby śledzić maszyn wirtualnych w ramach subskrypcji, który został ręcznie ustawić adresy IP w ramach systemu operacyjnego, należy rozważyć dodanie platformy Azure [tag](../azure-resource-manager/resource-group-using-tags.md) do maszyn wirtualnych. Można na przykład "przypisywanie adresów IP: Statyczne", na przykład. Dzięki temu możesz łatwo odnaleźć maszyn wirtualnych w ramach subskrypcji, który został ręcznie ustawić adres IP w ramach systemu operacyjnego.

Oprócz włączenia maszyny wirtualnej do komunikowania się z innymi zasobami w ramach tej samej lub połączone sieci wirtualne, prywatny adres IP umożliwia również maszynę wirtualną do komunikacji z Internetem. Połączenia wychodzące są tłumaczone przez usługę Azure nieprzewidywalne publiczny adres IP adres sieci źródłowej. Aby dowiedzieć się więcej na temat platformy Azure wychodzące połączenie z Internetem, przeczytaj [Azure wychodzące połączenie z Internetem](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu. Użytkownik nie może komunikować się ruchu przychodzącego na prywatny adres IP maszyny wirtualnej z Internetu. Jeśli połączeń wychodzących wymagają przewidywalne publiczny adres IP, należy skojarzyć zasób publicznego adresu IP do interfejsu sieciowego.

### <a name="public"></a>Public

Publiczne adresy IP przypisane do zasobu publicznego adresu IP włączyć łączność ruchu przychodzącego do maszyny wirtualnej z Internetu. Połączenia wychodzące z Internetem Użyj przewidywalne adresu IP. Zobacz [informacje o połączeniach wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Aby uzyskać szczegółowe informacje. Może przypisać publiczny adres IP do konfiguracji adresu IP, ale nie są wymagane. Jeśli nie przypiszesz publicznego adresu IP do maszyny wirtualnej przez skojarzenie zasobu publicznego adresu IP, maszyna wirtualna może nadal komunikować się ruch wychodzący do Internetu. W tym przypadku prywatny adres IP jest adres sieciowy źródła tłumaczyć Azure nieprzewidywalne publiczny adres IP. Aby dowiedzieć się więcej na temat zasobów publicznych adresów IP, zobacz [zasób publicznego adresu IP](virtual-network-public-ip-address.md).

Istnieją limity liczby prywatnych i publicznych adresów IP, które można przypisać do interfejsu sieciowego. Aby uzyskać więcej informacji, przeczytaj [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artykułu.

> [!NOTE]
> Azure tłumaczy maszyny wirtualnej prywatny adres IP na publiczny adres IP. W rezultacie system operacyjny maszyny wirtualnej nie rozpoznaje wszelkie publiczny adres IP, które są przypisane do niego, dzięki czemu nie ma potrzeby kiedykolwiek ręcznie przypisać publiczny adres IP w ramach systemu operacyjnego.

## <a name="assignment-methods"></a>Metody przydziału

Publiczne i prywatne adresy IP są przypisywane, przy użyciu jednej z następujących metod przypisania:

### <a name="dynamic"></a>Dynamiczny

Dynamiczny prywatny protokołów IPv4 i IPv6 (opcjonalnie) adresy są domyślnie przypisane.

- **Tylko publiczne**: Platforma Azure przypisuje adres z zakresu unikatowego do każdego regionu platformy Azure. Aby dowiedzieć się, zakresy, które są przypisane do każdego regionu, zobacz [zakresów IP centrum danych Azure Microsoft](https://www.microsoft.com/download/details.aspx?id=41653). Adres można zmienić, gdy maszyna wirtualna jest zatrzymana (cofnięto przydział), następnie uruchomić go ponownie. Nie można przypisać publiczny adres IPv6 w konfiguracji adresu IP przy użyciu jednej z metod przypisania.
- **Prywatne tylko**: Azure rezerwuje pierwsze cztery adresy w każdym zakresie adresów podsieci, a nie przydziela adresów. Platforma Azure przypisuje następny dostępny adres do zasobu z zakresu adresów podsieci. Jeśli na przykład zakres adresów podsieci to 10.0.0.0/16, a adresy 10.0.0.0.4–10.0.0.14 zostały już przypisane (.0–.3 są zarezerwowane), platforma Azure przypisuje do zasobu adres 10.0.0.15. Metoda dynamiczna to domyślna metoda alokacji. Po przypisaniu dynamiczne adresy IP są zwalniane, tylko jeśli interfejs sieciowy zostanie usunięty, przypisany do innej podsieci w tej samej sieci wirtualnej, lub metoda alokacji zostanie zmieniona na Statyczna i zostanie podany inny adres IP. Domyślnie platforma Azure przypisuje poprzedni adres dynamicznie przypisany jako adres statyczny po zmianie metody alokacji z dynamicznej na statyczną. Można przypisać tylko z prywatnym adresem IPv6 przy użyciu metody dynamiczne przypisania.

### <a name="static"></a>Statyczny

(Opcjonalnie) można przypisać publiczny lub prywatny statyczny adres IPv4 do konfiguracji adresu IP. Nie można przypisać statyczny adres IPv6 publicznych lub prywatnych w konfiguracji adresu IP. Aby dowiedzieć się, jak platforma Azure przypisuje statyczne publiczne adresy IPv4, zobacz [publiczne adresy IP](virtual-network-public-ip-address.md).

- **Tylko publiczne**: Platforma Azure przypisuje adres z zakresu unikatowego do każdego regionu platformy Azure. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064). Adres nie zmienia się aż do zasobu publicznego adresu IP, który jest przypisany do zostanie usunięty lub metoda przydziału zostanie zmieniona na dynamiczny. Jeśli zasób publicznego adresu IP jest skojarzony z konfiguracją adresów IP, musi być oddzielona od konfiguracji IP, zanim zmienisz jego metodę przypisywania.
- **Prywatne tylko**: Wybierz i przypisujesz adres z zakresu adresów podsieci. Możesz przypisać dowolny adres z zakresu adresów podsieci, który nie jest jednym z pierwszych czterech adresów w zakresie adresów podsieci i nie jest aktualnie przypisany do żadnego innego zasobu w podsieci. Adresy statyczne są zwalniane tylko w przypadku usunięcia interfejsu sieciowego. Jeśli zmienisz metodę alokacji na statyczną, platforma Azure dynamicznie przypisze wcześniej przypisany statyczny adres IP jako adres dynamiczny, nawet jeśli adres nie jest następnym dostępnym adresem w zakresie adresów podsieci. Adres zmieni się także, jeśli interfejs sieciowy zostanie przypisany do innej podsieci w tej samej sieci wirtualnej, ale aby przypisać interfejs sieciowy do innej podsieci, musisz najpierw zmienić metodę alokacji ze statycznej na dynamiczną. Po przypisaniu interfejsu sieciowego do innej podsieci możesz zmienić metodę alokacji z powrotem na statyczną i przypisać adres IP z zakresu adresów nowej podsieci.

## <a name="ip-address-versions"></a>Wersje adresu IP

Podczas przypisywania adresów, należy określić następujące wersje:

### <a name="ipv4"></a>Protokół IPv4

Każdy interfejs sieciowy musi mieć jeden [podstawowego](#primary) konfigurację adresu IP z przypisanym [prywatnej](#private) [IPv4](#ipv4) adresu. Można dodać co najmniej jeden [dodatkowej](#secondary) konfiguracje adresów IP, w których każdy może mieć prywatnego adresu IPv4 i (opcjonalnie) IPv4 [publicznych](#public) adresu IP.

### <a name="ipv6"></a>Protokół IPv6

Możesz przypisać zero lub jeden prywatny [IPv6](#ipv6) adres jedną konfigurację adresu IP pomocniczego interfejsu sieciowego. Interfejs sieciowy nie może mieć żadnych istniejących konfiguracje pomocniczych adresów IP. Nie można dodać konfiguracji adresu IP przy użyciu adresu IPv6 przy użyciu portalu. Użyj programu PowerShell lub interfejsu wiersza polecenia, aby dodać konfigurację adresu IP za pomocą prywatnego adresu IPv6 do istniejącego interfejsu sieciowego. Nie można dołączyć interfejsu sieciowego do istniejącej maszyny Wirtualnej.

> [!NOTE]
> Chociaż można utworzyć interfejsu sieciowego przy użyciu adresu IPv6 przy użyciu portalu, nie możesz dodać istniejący interfejs sieciowy do nowej lub istniejącej maszyny wirtualnej przy użyciu portalu. Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć interfejs sieciowy z prywatnym adresem IPv6, a następnie dołączyć interfejsu sieciowego, podczas tworzenia maszyny wirtualnej. Nie można dołączyć interfejsu sieciowego za pomocą prywatnego adresu IPv6 przypisany do istniejącej maszyny wirtualnej. Nie można dodać z prywatnym adresem IPv6 w konfiguracji adresu IP dla każdy interfejs sieciowy dołączony do maszyny wirtualnej przy użyciu dowolnych narzędzi (portal, interfejsu wiersza polecenia lub programu PowerShell).

Nie można przypisać publiczny adres IPv6 w podstawowej lub pomocniczej konfiguracji adresu IP.

## <a name="skus"></a>Jednostki SKU

Publiczny adres IP jest tworzony z jednostką SKU podstawowa lub standardowa. Aby uzyskać więcej informacji o różnicach jednostki SKU, zobacz [Zarządzanie publiczne adresy IP](virtual-network-public-ip-address.md).

> [!NOTE]
> Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć maszynę wirtualną z różnymi konfiguracjami adresów IP, przeczytaj następujące artykuły:

|Zadanie|Tool|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma interfejsami sieciowymi|[Interfejs wiersza polecenia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [programu PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny Wirtualnej karty Sieciowej z wieloma adresami IPv4|[Interfejs wiersza polecenia](virtual-network-multiple-ip-addresses-cli.md), [programu PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Utwórz na jednej maszynie Wirtualnej karty Sieciowej z prywatnym adresem IPv6 (za modułem równoważenia obciążenia platformy Azure)|[Interfejs wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [szablonu usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
