---
title: Tworzenie, zmienianie lub usuwanie Azure publiczny adres IP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie publicznego adresu IP.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 98810af66b0be6d925229b7e05dc01f62106e7cd
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2019
ms.locfileid: "64728734"
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Tworzenie, zmienianie lub usuwanie publicznego adresu IP

Więcej informacji na temat publiczny adres IP i sposobu tworzenia, Zmień i Usuń jeden. Publiczny adres IP stanowi zasób o swoje własne konfigurowalne ustawienia. Przypisanie publicznego adresu IP do zasobu platformy Azure, który obsługuje publicznych adresów IP umożliwia:
- Komunikacji przychodzącej z Internetu do zasobu, takich jak Azure Virtual Machines (VM), bramy aplikacji platformy Azure, usługi równoważenia obciążenia platformy Azure, bram Azure VPN Gateway i inne. Użytkownik może nadal komunikować się z niektórych zasobów, takich jak maszyny wirtualne z Internetu, jeśli maszyna wirtualna nie ma publiczny adres IP przypisany do niego, tak długo, jak maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia i równoważenia obciążenia jest przypisany publiczny adres IP. Aby ustalić, czy zasób dla określonej usługi platformy Azure można przypisać publiczny adres IP lub czy go może być skomunikował się z za pośrednictwem publicznego adresu IP innego zasobu platformy Azure, zobacz dokumentację dla usługi.
- Łączność wychodząca z Internetem przy użyciu adresu IP przewidywalne. Na przykład maszyny wirtualnej mogą komunikować się ruch wychodzący do Internetu bez publicznego adresu IP do niej przypisany, lecz jego adres sieci adresu tłumaczone przez usługę Azure nieprzewidywalne adres publiczny, domyślnie. Przypisanie publicznego adresu IP do zasobu umożliwia wiedzieć, których adres IP jest używany dla połączeń wychodzących. Chociaż przewidywalne, można zmienić adres, w zależności od wybranej metody przydziału. Aby uzyskać więcej informacji, zobacz [tworzenie publicznego adresu IP](#create-a-public-ip-address). Aby dowiedzieć się więcej na temat połączeń wychodzących z zasobów platformy Azure, zobacz [informacje o połączeniach wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

Publiczne adresy IP mają za symboliczną cenę. Aby wyświetlić ceny, przeczytaj [cennik adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony.

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

1. Górnego, lewego rogu portalu, wybierz **+ Utwórz zasób**.
2. Wprowadź *publiczny adres ip* w *Przeszukaj witrynę Marketplace* pole. Gdy **publiczny adres IP** pojawi się w wynikach wyszukiwania, wybierz ją.
3. W obszarze **publiczny adres IP**, wybierz opcję **Utwórz**.
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **tworzenie publicznego adresu IP**, a następnie wybierz **Utwórz**:

   |Ustawienie|Wymagana?|Szczegóły|
   |---|---|---|
   |Name (Nazwa)|Yes|Nazwa musi być unikatowa w obrębie grupy zasobów, którą wybierzesz.|
   |SKU|Yes|Wszystkie publiczne adresy IP utworzone przed wprowadzeniem jednostek SKU są **podstawowe** publiczne adresy IP jednostki SKU. Jednostka SKU nie można zmienić po utworzeniu publicznego adresu IP. Autonomiczna maszyna wirtualna, maszyn wirtualnych w zestawie dostępności lub zestawach skalowania maszyn wirtualnych, można użyć podstawowa lub standardowej jednostki SKU. Mieszanie jednostek SKU maszyn wirtualnych w ramach zestawów dostępności lub zestawach skalowania jest niedozwolona. **Podstawowe** jednostki SKU: W przypadku tworzenia publicznego adresu IP w regionie, który obsługuje stref dostępności **strefy dostępności** jest ustawiana *Brak* domyślnie. Można wybrać strefie dostępności w celu zagwarantowania określonej strefy dla publicznego adresu IP. **Standardowa** jednostki SKU: Publiczny adres IP standardowej jednostki SKU może być skojarzona z maszyną wirtualną lub frontonu modułu równoważenia obciążenia. Jeśli tworzysz publiczny adres IP w regionie, który obsługuje stref dostępności **strefy dostępności** jest ustawiana *strefowo nadmiarowe* domyślnie. Aby uzyskać więcej informacji o strefach dostępności, zobacz **strefy dostępności** ustawienie. Standardowa jednostka SKU jest wymagany, jeśli można skojarzyć adres do standardowego modułu równoważenia obciążenia. Aby dowiedzieć się więcej na temat modułów równoważenia obciążenia standardowego, zobacz [usługa Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.|
   |Wersja protokołu IP|Tak| Wybierz IPv4 lub IPv6. Natomiast do wielu zasobów platformy Azure można przypisywać publiczne adresy IPv4, IPv6 publiczny adres IP można przypisać tylko do modułu równoważenia obciążenia dostępnego z Internetu. Moduł równoważenia obciążenia będzie mogła równoważyć obciążenie ruchem IPv6 na maszynach wirtualnych platformy Azure. Dowiedz się więcej o [Równoważenie obciążenia ruchu IPv6 na maszynach wirtualnych](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wybrania **standardowej jednostki SKU**, nie masz możliwość dokonania wyboru *IPv6*. Adres IPv4 można tworzyć tylko, gdy za pomocą **standardowej jednostki SKU**.|
   |Przypisywanie adresów IP|Tak|**Dynamiczne:** Dynamiczne adresy są przypisywane tylko wtedy, gdy publiczny adres IP jest skojarzony z zasobem platformy Azure, a zasób zostanie uruchomiony po raz pierwszy. Adresy dynamiczne mogą ulec zmianie, jeśli są przypisani do zasobu, np. w przypadku maszyny wirtualnej, a maszyna wirtualna jest zatrzymana (cofnięto przydział), a następnie uruchomiona ponownie. Adres nie zmienia się maszyna wirtualna jest ponownie uruchomiona lub zatrzymana (ale nie cofnięto przydziału). Dynamiczne adresy są zwalniane, gdy zasób publicznego adresu IP jest oddzielona od zasobu, który jest skojarzony. **Statyczna:** Statyczne adresy są przypisywane po utworzeniu publicznego adresu IP. Adresy statyczne nie są zwalniane, aż do usunięcia zasobu publicznego adresu IP. Jeśli adres nie jest skojarzony z zasobem, możesz zmienić metodę przypisywania po utworzeniu adres. Jeśli adres jest skojarzony z zasobem, nie można zmienić metodę przypisywania. Jeśli wybierzesz *IPv6* dla **wersja protokołu IP**, Metoda przydziału jest *dynamiczne*. Jeśli wybierzesz *standardowa* dla **jednostki SKU**, Metoda przydziału jest *statyczne*.|
   |Limit czasu bezczynności (minuty)|Nie|Ile minut nie zamykaj połączenie TCP lub HTTP bez polegania na klientach, aby wysyłać komunikaty utrzymywania aktywności. Jeśli wybierzesz protokół IPv6 dla **wersja protokołu IP**, ta wartość nie może zostać zmieniona. |
   |Etykieta nazwy DNS|Nie|Musi być unikatowa w lokalizacji platformy Azure, Utwórz nazwę w (za pośrednictwem wszystkich subskrypcji i wszystkich klientów). Azure powoduje automatyczne zarejestrowanie nazwy i adresu IP w jej systemie DNS, dzięki czemu możesz nawiązać połączenie zasób o nazwie. Azure dołącza podsieć domyślna, takich jak *location.cloudapp.azure.com* (tam, gdzie lokalizacja to lokalizacja, możesz wybrać) do nazwy zostaną podane, można utworzyć w pełni kwalifikowana nazwa DNS. Jeśli zdecydujesz się utworzyć obie wersje adres, tą samą nazwą DNS zostanie przypisany do adresów IPv4 i IPv6. Domyślne platformy Azure DNS zawiera rekordy nazw IPv6 AAAA i IPv4, A i odpowiada za pomocą obu rekordów, podczas wyszukiwania nazwy DNS. Klient wybiera adresów (IPv4 lub IPv6) do komunikacji z. Zamiast lub oprócz używania etykiety nazwy DNS z domyślnym sufiksem możesz użyć usługi Azure DNS do skonfigurowania nazwy DNS z sufiksem niestandardowym, który jest rozpoznawany jako publiczny adres IP. Aby uzyskać więcej informacji, zobacz [Use Azure DNS with an Azure public IP address (Używanie usługi Azure DNS z publicznym adresem IP platformy Azure)](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Utwórz adres IPv6 (lub IPv4)|Nie| Czy jest wyświetlany IPv6 i IPv4 jest zależny od wybrania dla **wersja protokołu IP**. Na przykład w przypadku wybrania **IPv4** dla **wersja protokołu IP**, **IPv6** wyświetlane w tym miejscu. Jeśli wybierzesz *standardowa* dla **jednostki SKU**, masz możliwość utworzenia adresu IPv6.
   |Nazwa (tylko widoczne, gdy zaznaczono **tworzenia adresu IPv6 (lub IPv4)** wyboru)|Tak, jeśli wybierzesz **tworzenie IPv6** (lub IPv4) zaznacz pole wyboru.|Nazwa musi być inna niż nazwa wprowadzona dla pierwszego **nazwa** na tej liście. Jeśli zdecydujesz się utworzyć adres IPv6 i IPv4, portal tworzy dwie oddzielne zasoby publicznych adresów IP adresu, jeden z każdą wersją adres IP przypisany do niego.|
   |Przypisywanie adresów IP (tylko widoczne, gdy zaznaczono **tworzenia adresu IPv6 (lub IPv4)** wyboru)|Tak, jeśli wybierzesz **tworzenie IPv6** (lub IPv4) zaznacz pole wyboru.|Jeśli pole wyboru jest w stanie **Utwórz adres IPv4**, można wybrać metodę przypisania. Jeśli pole wyboru jest w stanie **Utwórz adres IPv6**, nie można wybrać metodę przypisania, ponieważ należy ją **dynamiczne**.|
   |Subskrypcja|Tak|Musi istnieć w tym samym [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako zasób, którą chcesz skojarzyć publiczny adres IP.|
   |Grupa zasobów|Tak|Może znajdować się w tej samej lub różnych [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako zasób, którą chcesz skojarzyć publiczny adres IP.|
   |Lokalizacja|Tak|Musi istnieć w tym samym [lokalizacji](https://azure.microsoft.com/regions), również w określonym regionem, co zasób, którą chcesz skojarzyć z publicznym adresem IP adres.|
   |Strefa dostępności| Nie | To ustawienie jest wyświetlany tylko w przypadku wybrania obsługiwanej lokalizacji. Aby uzyskać listę obsługiwanych lokalizacji, zobacz [Przegląd stref dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wybrania **podstawowe** jednostki SKU i *Brak* jest automatycznie wybrana. Jeśli wolisz zagwarantować określonej strefy, można wybrać określonej strefy. Wybór albo nie jest strefowo nadmiarowe. W przypadku wybrania **standardowa** jednostki SKU: Strefowo nadmiarowy jest automatycznie wybrana i sprawia, że ścieżka danych odporne na awarie strefy. Jeśli wolisz gwarantuje określonej strefy, który nie jest odporna na awarie strefy, można wybrać określonej strefy.

**Polecenia**

Chociaż portal zapewnia możliwość utworzenia dwóch publicznych adresów IP adresowania zasobów (jednej dla protokołu IPv4 i jednej dla protokołu IPv6), następujące polecenia programu PowerShell i interfejsu wiersza polecenia utworzyć jeden zasób za pomocą adresu dla jednej wersji adresu IP lub innych. Jeśli chcesz, aby dwa zasoby publicznych adresów IP adresu, jeden dla każdej wersji adresu IP należy uruchomić polecenie, określając różne nazwy i wersji zasoby publicznych adresów IP.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create)|
|PowerShell|[New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Wyświetlić, zmienić ustawienia lub usunąć publicznego adresu IP

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *publiczny adres ip*. Gdy **publiczne adresy IP** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz nazwę publicznego adresu IP wyświetlić, zmienić ustawienia lub usunąć z listy.
3. Wykonaj jedną z następujących opcji, w zależności od tego, czy chcesz wyświetlić, usunąć lub zmienić publiczny adres IP.
   - **Widok**: **Przegląd** sekcji przedstawiono ustawienia kluczy dla publicznego adresu IP, takich jak interfejs sieciowy, jest ona skojarzona (Jeśli adres jest skojarzona z interfejsem sieciowym). Portal nie wyświetla wersję adresu (IPv4 lub IPv6). Aby wyświetlić informacje o wersji, polecenie programu PowerShell lub interfejsu wiersza polecenia, aby wyświetlić publiczny adres IP. Wersja adresów IP w przypadku protokołu IPv6, przypisany adres nie jest wyświetlana w portalu, programu PowerShell lub interfejsu wiersza polecenia.
   - **Usuń**: Aby usunąć publiczny adres IP, wybierz **Usuń** w **Przegląd** sekcji. Jeśli adres jest obecnie skojarzony z konfiguracją adresów IP, nie można usunąć. Jeśli adres jest aktualnie skojarzona z konfiguracją, wybierz opcję **Usuń skojarzenie** usunąć skojarzenia adresu z konfiguracji adresu IP.
   - **Zmiana**: Wybierz **konfiguracji**. Zmień ustawienia, korzystając z informacji w kroku 4 [tworzenie publicznego adresu IP](#create-a-public-ip-address). Aby zmienić przypisanie dla adresu IPv4 ze statycznego na dynamiczny, należy najpierw skojarzenie publiczny adres IPv4 z konfiguracji IP, który jest skojarzony. Można później zmienić metodę przypisywania na dynamicznych i wybierz **skojarzyć** skojarzyć adres IP adres taką samą konfigurację adresu IP, w innej konfiguracji, lub pozostawić je usunięto skojarzenie. Aby usunąć skojarzenie publicznego adresu IP w **Przegląd** zaznacz **Usuń skojarzenie**.

   >[!WARNING]
   >Po zmianie metody przypisania ze statycznego na dynamiczny, tracisz adres IP, który został przypisany do publicznego adresu IP. Gdy usługa Azure publiczne serwery DNS Obsługa mapowanie między adresy statyczne lub dynamiczne i wszelkie etykiety nazwy DNS (Jeśli zdefiniowany jest jeden), dynamiczny adres IP można zmienić, gdy maszyna wirtualna zostanie uruchomiona po znajdujące się w stanie zatrzymania (przydział zostanie cofnięty). Aby uniemożliwić zmianę adresu, Przypisz statyczny adres IP.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Lista public-ip sieci az](/cli/azure/network/public-ip#az-network-public-ip-list) publiczne adresy IP, [az sieci public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) wyświetlanie ustawień; [az sieci public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) można zaktualizować; [usuwanie sieci az public-ip](/cli/azure/network/public-ip#az-network-public-ip-delete) do usunięcia|
|PowerShell|[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) pobierania obiektu publicznego adresu IP i wyświetlić jej ustawienia [AzPublicIpAddress zestaw](/powershell/module/az.network/set-azpublicipaddress) można zaktualizować ustawień; [AzPublicIpAddress Usuń](/powershell/module/az.network/remove-azpublicipaddress) do usunięcia|

## <a name="assign-a-public-ip-address"></a>Przypisz publiczny adres IP

Dowiedz się, jak przypisać publiczny adres IP do następujących zasobów:

- A [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny Wirtualnej (tworząc) lub [istniejącej maszyny Wirtualnej](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Moduł równoważenia obciążenia dostępnego z Internetu](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Połączenia lokacja lokacja przy użyciu usługi Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zestaw skalowania maszyn wirtualnych platformy Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań na publiczne adresy IP, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                                             | Name (Nazwa)                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Przeczytaj publiczny adres IP                                          |
| Microsoft.Network/publicIPAddresses/write                          | Utwórz lub zaktualizuj publiczny adres IP                           |
| Microsoft.Network/publicIPAddresses/delete                         | Usuwanie publicznego adresu IP                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Skojarz publiczny adres IP do zasobu                    |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie publicznego adresu IP, za pomocą [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonów usługi Resource Manager](template-samples.md)
- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) publiczny adres IP na adresy