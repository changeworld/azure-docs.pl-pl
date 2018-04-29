---
title: Skonfiguruj adresy IP dla interfejsu sieci platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodawanie, zmienianie i usuwanie prywatnych i publicznych adresów IP dla karty sieciowej.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: 79b84e3231886f62bf5978195562339d5c3275b6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Dodawanie, zmienianie lub usuwanie adresów IP dla interfejsu sieci platformy Azure

Dowiedz się, jak dodawanie, zmienianie i usuwanie prywatnych i publicznych adresów IP dla karty sieciowej. Prywatne adresy IP przypisane do interfejsu sieciowego umożliwiają maszynie wirtualnej do komunikowania się z innymi zasobami w sieci wirtualnej platformy Azure i sieci połączonych. Prywatny adres IP umożliwia także komunikacji wychodzącej z Internetem przy użyciu adresu IP nieprzewidywalne. A [publicznego adresu IP](virtual-network-public-ip-address.md) przypisany do interfejsu umożliwia przychodzącej komunikacji sieciowej do maszyny wirtualnej z Internetu. Adres umożliwia także komunikacji wychodzącej z maszyny wirtualnej z Internetem przy użyciu wartości prognozowanych adresu IP. Aby uzyskać więcej informacji, zobacz [Opis połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Jeśli użytkownik należy do tworzenia, zmienić lub usunąć interfejsu sieciowego, przeczytaj [Zarządzanie interfejsu sieciowego](virtual-network-network-interface.md) artykułu. Jeśli trzeba dodać interfejsów sieciowych lub usunąć interfejsów sieciowych z maszyny wirtualnej, przeczytaj [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md) artykułu. 


## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.2.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

## <a name="add-ip-addresses"></a>Dodaj adresy IP

Można dodać jako wiele [prywatnej](#private) i [publicznego](#public) [IPv4](#ipv4) adresy odpowiednio do interfejsu sieciowego, w ramach limitów na liście [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artykułu. Nie można użyć portalu, aby dodać adres IPv6 do istniejącego interfejsu sieciowego (chociaż portalu umożliwia dodawanie prywatnego adresu IPv6 karty sieciowej, podczas tworzenia interfejsu sieciowego). Można użyć programu PowerShell lub interfejsu wiersza polecenia do dodania do jednego prywatnego adresu IPv6 [dodatkowej konfiguracji IP](#secondary) (o ile istnieją żadnych istniejących dodatkowej konfiguracji IP) dla istniejącego interfejsu sieciowego, który nie jest dołączony do maszyny wirtualnej. Nie można użyć dowolnego narzędzia można dodać publiczny adres IPv6 do interfejsu sieciowego. Zobacz [IPv6](#ipv6) szczegółowe informacje o przy użyciu adresów IPv6. 

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, który jest przypisane (co najmniej) uprawnienia roli współautora sieci dla Twojej subskrypcji. Odczyt [wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artykuł, aby dowiedzieć się więcej o przypisywanie ról i uprawnień do kont.
2. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawia się w wynikach wyszukiwania kliknij ją.
3. W **interfejsy sieciowe** bloku, który jest wyświetlany, kliknij przycisk ma zostać dodany adres IPv4 dla interfejsu sieciowego.
4. Kliknij przycisk **konfiguracje adresów IP** w **ustawienia** bloku dla wybranego interfejsu sieciowego.
5. Kliknij przycisk **+ Dodaj** w otwartym bloku dla konfiguracji adresów IP.
6. Podaj następujące informacje, a następnie kliknij przycisk **OK** zamknąć **konfiguracji IP dodać** bloku:

    |Ustawienie|Wymagana?|Szczegóły|
    |---|---|---|
    |Name (Nazwa)|Yes|Musi być unikatowa dla interfejsu sieciowego|
    |Typ|Yes|Ponieważ dodajesz konfiguracji IP do istniejącego interfejsu sieciowego, a każdy interfejs sieciowy musi mieć [głównej](#primary) jest jedyną opcją konfiguracji adresów IP, **dodatkowej**.|
    |Metoda przypisywania adresu prywatnego adresu IP|Yes|[**Dynamiczne**](#dynamic): Azure przypisuje następnego dostępnego adresu dla interfejsu sieciowego jest wdrażany w zakres adresów podsieci. [**Statyczne**](#static): nieużywane adres dla zakresu adresów podsieci interfejsu sieciowego jest wdrażany w.|
    |Publiczny adres IP|Nie|**Wyłączone:** zasobu bez publicznego adresu IP jest obecnie skojarzony z konfiguracją protokołu IP. **Włączone:** wybierz istniejący adres IPv4 publicznego adresu IP lub Utwórz nową. Aby dowiedzieć się, jak utworzyć publicznego adresu IP, przeczytaj [publicznego adresu IP, adresy](virtual-network-public-ip-address.md#create-a-public-ip-address) artykułu.|
7. Ręcznie Dodaj dodatkowej prywatnych adresów IP do systemu operacyjnego maszyny wirtualnej, wykonując instrukcje [przypisać wiele adresów IP do maszyny wirtualnej systemów operacyjnych](virtual-network-multiple-ip-addresses-portal.md#os-config) artykułu. Zobacz [prywatnej](#private) adresów IP dla uwagi przed ręcznie dodać adresy IP do systemu operacyjnego maszyny wirtualnej. Nie dodawaj żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Tworzenie kart sieciowych az ip-config](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-address-settings"></a>Zmień ustawienia adresu IP

Może zmienić metodę przypisanie adresu IPv4 zmień statyczny adres IPv4, lub zmień publiczny adres IP przypisany do interfejsu sieciowego. Jeśli chcesz zmienić prywatny adres IPv4 dodatkowej konfiguracji adresu IP skojarzonego z dodatkowy interfejs sieciowy na maszynie wirtualnej (Dowiedz się więcej o [interfejsów sieciowych podstawowych i pomocniczych](virtual-network-network-interface-vm.md)), umieść maszynę wirtualną do (cofnięciu przydziału) zatrzymana przed wykonaniem poniższych kroków: 

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, który jest przypisane (co najmniej) uprawnienia roli współautora sieci dla Twojej subskrypcji. Odczyt [wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artykuł, aby dowiedzieć się więcej o przypisywanie ról i uprawnień do kont.
2. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawia się w wynikach wyszukiwania kliknij ją.
3. W **interfejsy sieciowe** bloku, który jest wyświetlany, kliknij przycisk chcesz przejrzeć lub zmienić ustawienia adresu IP dla interfejsu sieciowego.
4. Kliknij przycisk **konfiguracje adresów IP** w **ustawienia** bloku dla wybranego interfejsu sieciowego.
5. Kliknij pozycję konfiguracji IP, który chcesz zmodyfikować na liście w bloku, który zostanie otwarty w przypadku konfiguracji adresu IP.
6. Zmień ustawienia, zgodnie z potrzebami, korzystając z informacji o ustawieniach w kroku 6 [Dodaj konfigurację IP](#create-ip-config) sekcji tego artykułu. Kliknij przycisk **zapisać** zamknąć bloku do konfiguracji protokołu IP, można zmienić.

>[!NOTE]
>Podstawowy interfejs sieciowy ma wielu konfiguracji adresów IP, można zmienić prywatnego adresu IP w konfiguracji podstawowego adresu IP należy ręcznie ponownie przypisać głównych i dodatkowych adresów IP do interfejsu sieciowego w systemie Windows (nie wymaga Linux) . Aby ręcznie przypisywać adresy IP do karty sieciowej w systemie operacyjnym, przeczytaj [przypisać wiele adresów IP maszynom wirtualnym](virtual-network-multiple-ip-addresses-portal.md#os-config) artykułu. Zobacz [prywatnej](#private) adresów IP dla uwagi przed ręcznie dodać adresy IP do systemu operacyjnego maszyny wirtualnej. Nie dodawaj żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja konfiguracji adresu ip karty sieciowej sieci az](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="remove-ip-addresses"></a>Usuń adresy IP

Możesz usunąć [prywatnej](#private) i [publicznego](#public) adresów IP z karty sieciowej, ale interfejs sieciowy musi zawsze mieć przypisane do niego co najmniej jeden prywatny adres IPv4.

1. Zaloguj się do [portalu Azure](https://portal.azure.com) przy użyciu konta, który jest przypisane (co najmniej) uprawnienia roli współautora sieci dla Twojej subskrypcji. Odczyt [wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) artykuł, aby dowiedzieć się więcej o przypisywanie ról i uprawnień do kont.
2. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawia się w wynikach wyszukiwania kliknij ją.
3. W **interfejsy sieciowe** wyświetlonym bloku, kliknij interfejsu sieciowego, aby usunąć IP adresów z.
4. Kliknij przycisk **konfiguracje adresów IP** w **ustawienia** bloku dla wybranego interfejsu sieciowego.
5. Kliknij prawym przyciskiem myszy [dodatkowej](#secondary) konfiguracji protokołu IP (nie można usunąć [głównej](#primary) konfiguracji) chcesz usunąć, kliknij przycisk **usunąć**, następnie kliknij przycisk **tak** aby potwierdzić usunięcie. Konfiguracja gdyby publicznego zasobu adres IP skojarzony zasobu jest oddzielona od konfiguracji IP, ale zasób nie zostanie usunięta.
6. Zamknij **konfiguracje adresów IP** bloku.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Usuwanie konfiguracji adresu ip karty sieciowej sieci az](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_nic_ip_config_delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="ip-configurations"></a>Konfiguracje adresów IP

[Prywatne](#private) i (opcjonalnie) [publicznego](#public) adresy IP są przypisywane do co najmniej jednej konfiguracji IP przypisane do karty sieciowej. Istnieją dwa typy konfiguracji adresu IP:

### <a name="primary"></a>Podstawowy

Każdy interfejs sieciowy jest przypisany jedną podstawową konfigurację protokołu IP. Podstawową konfigurację protokołu IP:

- Ma [prywatnej](#private) [IPv4](#ipv4) adres przypisany do niej. Nie można przydzielić prywatnej [IPv6](#ipv6) adres podstawową konfigurację protokołu IP.
- Może być również [publicznego](#public) przypisane do niej adres IPv4. Nie można przypisać publiczny adres IPv6 do podstawowej lub dodatkowej konfiguracji adresu IP. Jednak możesz przypisać publiczny adres IPv6 do modułu równoważenia obciążenia Azure, które można równoważenia obciążenia ruchu maszyny wirtualnej prywatny adres IPv6. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje i ograniczenia dotyczące IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Pomocniczy

Oprócz konfiguracji podstawowego adresu IP karty sieciowej może być zero lub więcej dodatkowej konfiguracji IP przypisanych do niego. Dodatkowej konfiguracji adresu IP:

- Musi mieć prywatny adres IPv4 lub IPv6 przypisany do niej. Jeśli adres IPv6, interfejsu sieciowego może mieć tylko jedną konfigurację adresu IP dodatkowej. Jeśli adres IPv4, interfejsu sieciowego może mieć wielu dodatkowej konfiguracji IP przypisane do niej. Aby dowiedzieć się więcej na temat liczby prywatnych i publicznych adresów IPv4 można przypisać do interfejsu sieciowego, zobacz [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artykułu.  
- Może również mieć publiczny adres IPv4 przypisane, jeśli protokół IPv4 jest prywatny adres IP. Prywatny adres IP w przypadku protokołu IPv6, nie można przypisać publiczny adres IPv4 lub IPv6 z konfiguracją protokołu IP. Przypisywanie wielu adresów IP do interfejsu sieciowego jest takie jak przydatne w scenariuszach:
    - Hostowanie wielu witryn sieci Web lub usług z różnymi adresami IP i certyfikatami SSL na jednym serwerze.
    - Maszyna wirtualna, służąc jako urządzenie wirtualne sieci, takie jak Zapora lub Usługa równoważenia obciążenia.
    - Możliwość dodawania żadnego prywatne adresy IPv4 dla wszystkich interfejsów sieciowych do puli zaplecza modułu równoważenia obciążenia Azure. W przeszłości tylko podstawowy adres IPv4 dla interfejsu sieci podstawowej można można dodać do puli zaplecza. Aby dowiedzieć się więcej na temat sposobu równoważenia obciążenia wielu konfiguracji IPv4, zobacz [wielu konfiguracji adresów IP Równoważenie obciążenia](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu. 
    - Możliwość ładowania równoważenie jeden adres IPv6 przypisany do interfejsu sieciowego. Aby dowiedzieć się więcej na temat zrównoważeniu prywatny adres IPv6, zobacz [adresy IPv6 równoważenia obciążenia](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu.


## <a name="address-types"></a>Typy adresów

Następujące adresy IP można przypisać [konfiguracji IP](#ip-configurations):

### <a name="private"></a>Private

Prywatne [IPv4](#ipv4) adresy umożliwiają maszynie wirtualnej do komunikowania się z innych zasobów w sieci wirtualnej lub innych połączonych sieci. Maszyny wirtualnej nie może być przekazywane ruchu przychodzącego, ani można maszyny wirtualnej komunikowania się wychodzące z prywatnej [IPv6](#ipv6) adres, z jednym wyjątkiem. Maszyny wirtualnej mogą komunikować się z usługą równoważenia obciążenia Azure przy użyciu adresu IPv6. Aby uzyskać więcej informacji, zobacz [szczegółowe informacje i ograniczenia dotyczące IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations). 

Domyślnie serwery Azure DHCP przypisać prywatny adres IPv4 dla [podstawową konfigurację protokołu IP](#primary) interfejsu sieci platformy Azure do interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. O ile to konieczne, należy nigdy nie ręcznie ustawić adres IP interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. 

> [!WARNING]
> Jeśli adres IPv4 jest ustawiony jako podstawowy adres IP interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej kiedykolwiek różni się od prywatny adres IPv4, przypisane do podstawowej konfiguracji IP podstawowy interfejs sieciowy dołączonego do maszyny wirtualnej w obrębie platformy Azure możesz utracić łączność z maszyną wirtualną.

Istnieją scenariusze, w których konieczne jest ręczne ustawienie adresu IP interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. Na przykład należy ręcznie ustawić głównych i dodatkowych adresów IP w systemie operacyjnym Windows podczas dodawania wielu adresów IP do maszyny wirtualnej platformy Azure. Dla maszyny wirtualnej systemu Linux może tylko należy ręcznie ustawić dodatkowych adresów IP. Zobacz [adresów IP Dodaj do systemu operacyjnego maszyny Wirtualnej](virtual-network-multiple-ip-addresses-portal.md#os-config) szczegółowe informacje. Jeśli trzeba zmienić adres przypisany do konfiguracji adresu IP, jest zalecane możesz:

1. Upewnij się, że maszyna wirtualna jest uzyskiwania adresu z serwerów Azure DHCP. Po utworzeniu, Zmień przypisanie adresów IP protokołu DHCP w ramach systemu operacyjnego i uruchomić ponownie maszynę wirtualną.
2. Zatrzymaj (deallocate) maszyny wirtualnej.
3. Zmienianie adresu IP dla konfiguracji IP w obrębie platformy Azure.
4. Uruchamia maszynę wirtualną.
5. [Ręczne konfigurowanie](virtual-network-multiple-ip-addresses-portal.md#os-config) dodatkowych adresów IP w ramach systemu operacyjnego (a także podstawowego adresu IP w systemie Windows) do dopasowania, ustaw w obrębie platformy Azure.
 
Poprzednie kroki, prywatnego adresu IP przypisanego do interfejsu sieciowego w systemie Azure i w systemie operacyjnym maszyny wirtualnej, wykonując pozostają takie same. Aby śledzić maszyn wirtualnych w ramach subskrypcji ręcznie ustawionych adresów IP w ramach systemu operacyjnego, należy rozważyć dodanie Azure [tag](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags) do maszyn wirtualnych. Można na przykład "przypisywanie adresów IP: statyczny", na przykład. W ten sposób maszyn wirtualnych można łatwo znaleźć w ramach subskrypcji ręcznie ustawionych adres IP w ramach systemu operacyjnego.

Oprócz włączenia maszyny wirtualnej do komunikowania się z innych zasobów w ramach tego samego lub połączonych sieci wirtualnych, prywatnego adresu IP umożliwia także maszyny wirtualnej do komunikowania się ruch wychodzący do Internetu. Połączenia wychodzące są źródłowego adresu sieciowego przetłumaczony przez platformę Azure nieprzewidywalne publicznego adresu IP. Aby dowiedzieć się więcej na temat usługi Azure wychodzące połączenie z Internetem, przeczytaj [Azure wychodzące połączenie z Internetem](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu. Użytkownik nie może komunikować się przychodzących prywatny adres IP maszyny wirtualnej z Internetu. Jeśli połączeń wychodzących wymagają przewidywalnej publicznego adresu IP, należy skojarzyć publicznego zasobu adresu IP do karty sieciowej.

### <a name="public"></a>Public

Publiczne adresy IP przypisane przez zasób publiczny adres IP Włącz połączenia przychodzące do maszyny wirtualnej z Internetu. Połączenia wychodzące z Internetem użyć przewidywalną adresu IP. Zobacz [Opis połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szczegółowe informacje. Może przypisać publicznego adresu IP do konfiguracji adresu IP, ale nie są wymagane. Nie przypisuj publicznego adresu IP do maszyny wirtualnej, kojarząc zasób publiczny adres IP, maszyna wirtualna może nadal komunikacji w ruch wychodzący do Internetu. W takim przypadku prywatnego adresu IP jest adres sieciowy źródła przetłumaczony przez platformę Azure nieprzewidywalne publicznego adresu IP. Aby dowiedzieć się więcej na temat zasobów publicznych adresów IP, zobacz [publicznego adresu IP zasobu adresu](virtual-network-public-ip-address.md).

Istnieją ograniczenia liczby prywatnych i publicznych adresów IP, które można przypisać do interfejsu sieciowego. Aby uzyskać więcej informacji, przeczytaj [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) artykułu.

> [!NOTE]
> Azure tłumaczy prywatnego adresu IP maszyny wirtualnej do publicznego adresu IP. W związku z tym system operacyjny maszyny wirtualnej nie rozpoznaje wszystkie publiczny adres IP przypisany, więc nie trzeba kiedykolwiek ręcznie przypisać publicznego adresu IP w ramach systemu operacyjnego.

## <a name="assignment-methods"></a>Metody przydziału

Publiczne i prywatne adresy IP są przypisywane, przy użyciu jednej z następujących metod przypisania:

### <a name="dynamic"></a>Dynamiczny

Prywatne IPv4 i IPv6 (opcjonalnie) adresy są przypisywane domyślnie. 

- **Tylko publiczne**: Azure przypisuje adresem z zakresu unikatowy do każdego regionu Azure. Aby dowiedzieć się, które zakresy są przypisane do każdego regionu, zobacz [zakresy IP centrum danych Azure Microsoft](https://www.microsoft.com/download/details.aspx?id=41653). Po zatrzymaniu maszyny wirtualnej (cofnięciu przydziału), następnie uruchomić ponownie, można zmienić adres. Nie można przypisać publiczny adres IPv6 do konfiguracji adresu IP za pomocą jednej z metod przypisania.
- **Tylko prywatnego**: Azure rezerwuje pierwsze cztery adresów w każdym zakres adresów podsieci, a nie przypisywanie adresów. Platforma Azure przypisuje następny dostępny adres do zasobu z zakresu adresów podsieci. Jeśli na przykład zakres adresów podsieci to 10.0.0.0/16, a adresy 10.0.0.0.4–10.0.0.14 zostały już przypisane (.0–.3 są zarezerwowane), platforma Azure przypisuje do zasobu adres 10.0.0.15. Metoda dynamiczna to domyślna metoda alokacji. Po przypisaniu dynamiczne adresy IP są zwalniane, tylko jeśli interfejs sieciowy zostanie usunięty, przypisany do innej podsieci w tej samej sieci wirtualnej, lub metoda alokacji zostanie zmieniona na Statyczna i zostanie podany inny adres IP. Domyślnie platforma Azure przypisuje poprzedni adres dynamicznie przypisany jako adres statyczny po zmianie metody alokacji z dynamicznej na statyczną. Można przypisać tylko prywatnego adresu IPv6 przy użyciu metody dynamicznej przypisania.

### <a name="static"></a>Statyczny

(Opcjonalnie) można przypisać publicznych lub prywatnych statyczny adres IPv4 do konfiguracji adresu IP. Nie można przypisać statycznego adresu IPv6 publicznych lub prywatnych do konfiguracji adresu IP. Aby dowiedzieć się więcej na temat sposobu Azure przypisuje statyczne publiczne adresy IPv4, zobacz [publicznego adresu IP](virtual-network-public-ip-address.md) artykułu.

- **Tylko publiczne**: Azure przypisuje adresem z zakresu unikatowy do każdego regionu Azure. Aby dowiedzieć się, które zakresy są przypisane do każdego regionu, zobacz [zakresy IP centrum danych Azure Microsoft](https://www.microsoft.com/download/details.aspx?id=41653). Adres nie ulega zmianie, dopóki zasób publicznego adresu IP, który jest przypisany do została usunięta, lub metoda przydziału jest zmieniana na dynamiczny. Jeśli zasób publicznego adresu IP jest skojarzony z konfiguracją protokołu IP, musi być oddzielona od konfiguracji IP przed zmianą jego metody przypisania.
- **Tylko prywatnego**: Wybierz i przypisać adres z zakresu adresów w podsieci. Możesz przypisać dowolny adres z zakresu adresów podsieci, który nie jest jednym z pierwszych czterech adresów w zakresie adresów podsieci i nie jest aktualnie przypisany do żadnego innego zasobu w podsieci. Adresy statyczne są zwalniane tylko w przypadku usunięcia interfejsu sieciowego. Jeśli zmienisz metodę alokacji na statyczną, platforma Azure dynamicznie przypisze wcześniej przypisany statyczny adres IP jako adres dynamiczny, nawet jeśli adres nie jest następnym dostępnym adresem w zakresie adresów podsieci. Adres zmieni się także, jeśli interfejs sieciowy zostanie przypisany do innej podsieci w tej samej sieci wirtualnej, ale aby przypisać interfejs sieciowy do innej podsieci, musisz najpierw zmienić metodę alokacji ze statycznej na dynamiczną. Po przypisaniu interfejsu sieciowego do innej podsieci możesz zmienić metodę alokacji z powrotem na statyczną i przypisać adres IP z zakresu adresów nowej podsieci.

## <a name="ip-address-versions"></a>Wersji adresu IP

Podczas przypisywania adresów, można określić następujące wersje:

### <a name="ipv4"></a>Protokół IPv4

Każdy interfejs sieciowy musi mieć jeden [głównej](#primary) konfiguracji adresów IP z przypisanym [prywatnej](#private) [IPv4](#ipv4) adres. Można dodać jeden lub więcej [dodatkowej](#secondary) konfiguracje adresów IP, każdy mieć IPv4 prywatnych i (opcjonalnie) IPv4 [publicznego](#public) adresu IP.

### <a name="ipv6"></a>Protokół IPv6

Można przypisać prywatnego zero lub jeden [IPv6](#ipv6) adres do jednej dodatkowej konfiguracji IP interfejsu sieciowego. Interfejs sieciowy nie może mieć żadnych istniejących dodatkowej konfiguracji adresu IP. Nie można dodać konfiguracji IP przy użyciu adresu IPv6 przy użyciu portalu. Użyj programu PowerShell lub interfejsu wiersza polecenia, aby dodać konfigurację protokołu IP za pomocą prywatnego adresu IPv6 do istniejącego interfejsu sieciowego. Nie można dołączyć interfejsu sieciowego do istniejącej maszyny Wirtualnej.

> [!NOTE]
> Chociaż można utworzyć interfejs sieciowy z adresów IPv6 za pomocą portalu, nie można dodać istniejącego interfejsu sieciowego do nowej lub istniejącej maszyny wirtualnej za pomocą portalu. Użyj programu PowerShell lub 2.0 interfejsu wiersza polecenia platformy Azure można utworzyć za pomocą prywatnego adresu IPv6 karty sieciowej, a następnie dołączyć interfejsu sieciowego, podczas tworzenia maszyny wirtualnej. Nie można dołączyć karty sieciowej, za pomocą prywatnego adresu IPv6 przypisany do istniejącej maszyny wirtualnej. Nie można dodać prywatny adres IPv6 do konfiguracji adresu IP dla interfejsu sieciowego, wszystkie dołączony do maszyny wirtualnej przy użyciu dowolnego narzędzia (portal, interfejsu wiersza polecenia lub środowiska PowerShell).

Nie można przypisać publiczny adres IPv6 do podstawowej lub dodatkowej konfiguracji adresu IP.

## <a name="skus"></a>Jednostki SKU

Publiczny adres IP jest tworzony z SKU planu basic lub standard.  Aby uzyskać więcej informacji na temat różnic SKU zobacz [Zarządzanie publiczne adresy IP](virtual-network-public-ip-address.md).

> [!NOTE]
> Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć maszynę wirtualną za pomocą różnych konfiguracji adresu IP, przeczytaj następujące artykuły:

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi|[Interfejs wiersza polecenia](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [środowiska PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie jednej maszyny Wirtualnej karty Sieciowej z wielu adresów IPv4|[Interfejs wiersza polecenia](virtual-network-multiple-ip-addresses-cli.md), [środowiska PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Tworzenie jednej maszyny Wirtualnej karty Sieciowej za pomocą prywatnego adresu IPv6 (za równoważenia obciążenia Azure)|[Interfejs wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [szablonu usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
