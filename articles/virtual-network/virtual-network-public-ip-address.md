---
title: Tworzenie, zmienianie lub usuwanie Azure publicznego adresu IP | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie publicznego adresu IP.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: c28d409bbdb7a4100f2bb9f00ff6f58a13855ea4
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="create-change-or-delete-a-public-ip-address"></a>Tworzenie, zmienianie lub usuwanie publicznego adresu IP

Więcej informacji na temat publiczny adres IP i tworzenie, zmienianie i usunąć jeden. Publiczny adres IP jest zasób o jego ustawienia można skonfigurować. Przypisywanie publicznego adresu IP do zasobów platformy Azure, która obsługuje publicznych adresów IP umożliwia:
- Komunikacji przychodzącej z Internetu do zasobów, takich jak Azure maszyn wirtualnych (VM), bramy aplikacji Azure, usługi równoważenia obciążenia Azure, bram sieci VPN platformy Azure i inne. Użytkownik może nadal komunikacji z niektórych zasobów, takich jak maszyny wirtualne z Internetu, jeśli maszyna wirtualna nie ma publicznego adresu IP przypisane do niego, pod warunkiem, maszyna wirtualna jest częścią puli zaplecza modułu równoważenia obciążenia, a moduł równoważenia obciążenia przypisano publiczny adres IP. Aby ustalić, czy można przypisać zasobów dla określonej usługi Azure publicznego adresu IP lub czy go może być komunikowały się z za pośrednictwem publicznego adresu IP innego zasobów platformy Azure, zobacz dokumentację dla usługi. 
- Łączność wychodząca z Internetem przy użyciu wartości prognozowanych adresu IP. Na przykład maszyny wirtualne mogą komunikować się ruch wychodzący do Internetu bez publicznego adresu IP przypisane do niego, ale jej adres jest adresem sieci przetłumaczony przez platformę Azure nieprzewidywalne adres publiczny, domyślnie. Przypisywanie publicznego adresu IP do zasobu umożliwia wiedzieć, który adres IP jest używana dla połączenia wychodzące. Chociaż przewidywalne, można zmienić adres, w zależności od wybranej metody przypisania. Aby uzyskać więcej informacji, zobacz [tworzenie publicznego adresu IP](#create-a-public-ip-address). Aby dowiedzieć się więcej na temat połączenia wychodzące z zasobów platformy Azure, zobacz [zrozumieć połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.7.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

Konta, zaloguj się do lub z usługą Azure, musi być przypisany do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowej roli zabezpieczeń](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania na liście [uprawnień ](#permissions).

Publiczne adresy IP ma nominalnego opłat. Aby wyświetlić ceny, przeczytaj [cennik adres IP](https://azure.microsoft.com/pricing/details/ip-addresses) strony. 

## <a name="create-a-public-ip-address"></a>Tworzenie publicznego adresu IP

1. W górnego, lewego rogu portalu, wybierz **+ Utwórz zasób**.
2. Wprowadź *publicznego adresu ip* w *wyszukiwania portalu Marketplace* pole. Gdy **publicznego adresu IP** pojawia się w wynikach wyszukiwania, wybierz go.
3. W obszarze **publicznego adresu IP**, wybierz pozycję **Utwórz**.
4. Wprowadź lub wybierz wartości dla następujących ustawień w obszarze **tworzenie publicznego adresu IP**, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wymagana?|Szczegóły|
    |---|---|---|
    |Name (Nazwa)|Yes|Nazwa musi być unikatowa w ramach grupy zasobów, którą wybierzesz.|
    |SKU|Yes|Wszystkie publiczne adresy IP utworzone przed wprowadzeniem jednostki SKU **podstawowe** SKU publicznych adresów IP.  Jednostka SKU nie można zmienić po utworzeniu publicznego adresu IP. Autonomicznej maszyny wirtualnej, maszyn wirtualnych w zestawie dostępności lub zestawy skalowania maszyny wirtualnej można użyć, Basic lub Standard jednostki SKU.  Mieszanie jednostki SKU między maszynami wirtualnymi w obrębie zestawów dostępności lub zestawy skalowania jest niedozwolone. **Podstawowe** SKU: w przypadku tworzenia publicznego adresu IP w region, który obsługuje stref dostępności **strefy dostępności** mają ustawioną wartość *Brak* domyślnie. Można wybrać strefy dostępności, aby zagwarantować dla określonej strefy dla publicznego adresu IP. **Standardowe** SKU: A standardowy SKU publicznego adresu IP może być skojarzona z maszyną wirtualną lub fronton usługi równoważenia obciążenia. Jeśli tworzysz publicznego adresu IP w region, który obsługuje stref dostępności **strefy dostępności** mają ustawioną wartość *Strefowo nadmiarowy* domyślnie. Aby uzyskać więcej informacji na temat stref dostępności, zobacz **strefy dostępności** ustawienie. Standardowy SKU jest wymagany, jeśli można skojarzyć adres usługi równoważenia obciążenia standardowego. Aby dowiedzieć się więcej na temat równoważenia obciążenia standardowego, zobacz [modułu równoważenia obciążenia Azure standardowy SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.|
    |Wersji protokołu IP|Yes| Wybierz IPv4 lub IPv6. Podczas publicznych adresów IPv4 mogą być przypisane do wielu zasobów platformy Azure, publiczny adres IP protokołu IPv6 można przypisać tylko do modułu równoważenia obciążenia internetowy. Moduł równoważenia obciążenia można zrównoważeniu ruch IPv6 do maszyn wirtualnych platformy Azure. Dowiedz się więcej o [IPv6 ruchu do maszyn wirtualnych równoważenia obciążenia](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wybrania **standardowy SKU**, nie trzeba wybrać opcję *IPv6*. Można tworzyć tylko adresu IPv4 przy użyciu **standardowy SKU**.|
    |Przypisywanie adresów IP|Yes|**Dynamiczne:** dynamicznych adresów są przypisane tylko po publiczny adres IP jest skojarzony z karty sieciowej podłączonej do maszyny wirtualnej i maszynie wirtualnej jest uruchomiona po raz pierwszy. Jeśli interfejs sieciowy jest dołączony do maszyny wirtualnej zostanie zatrzymana (cofnięciu przydziału), można zmienić adresów dynamicznych. Adres jest taka sama, jeśli maszyna wirtualna jest ponowny rozruch lub zatrzymana (ale nie alokację). **Statyczne:** przypisywania adresów statycznych po utworzeniu publicznego adresu IP. Statyczne adresy nie należy zmieniać, nawet wtedy, gdy maszyna wirtualna przechodzi w stan zatrzymania (cofnięciu przydziału). Adres zwolnieniu tylko po usunięciu interfejsu sieciowego. Metoda przydziału można zmienić po utworzeniu interfejsu sieciowego. W przypadku wybrania *IPv6* dla **IP w wersji**, Metoda przydziału jest *dynamiczne*. W przypadku wybrania *standardowe* dla **SKU**, Metoda przydziału jest *statycznych*.|
    |Limit czasu bezczynności (w minutach)|Nie|Ile minut, aby utrzymać otwarte połączenie TCP lub HTTP bez polegania na klientach, aby wysyłać komunikaty utrzymywania aktywności. W przypadku wybrania protokołu IPv6 dla **IP w wersji**, nie można zmienić tej wartości. |
    |Etykieta nazwy DNS|Nie|Muszą być unikatowe w lokalizacji platformy Azure, Utwórz nazwę w (za pośrednictwem wszystkie subskrypcje i wszystkich klientów). Azure automatycznie rejestruje nazwę i adres IP w jego systemie DNS, możesz nawiązać połączenie zasób o takiej nazwie. Azure dołącza podsieci domyślnej, takich jak *location.cloudapp.azure.com* (Jeśli lokalizacja jest lokalizacją wybrania) na nazwę podasz, do utworzenia w pełni kwalifikowanej nazwy DNS. Jeśli wybierzesz opcję utworzenia obie wersje adres, tą samą nazwą DNS jest przypisany do adresów IPv4 i IPv6. Domyślne Azure DNS zawiera zarówno IPv4 A i IPv6 AAAA rejestruje nazwę i odpowiada zarówno rekordy po wyszukiwana jest nazwa DNS. Klient będzie preferował adresów (IPv4 lub IPv6) do komunikacji z. Zamiast lub oprócz używania etykiety nazwy DNS z domyślnym sufiksem możesz użyć usługi Azure DNS do skonfigurowania nazwy DNS z sufiksem niestandardowym, który jest rozpoznawany jako publiczny adres IP. Aby uzyskać więcej informacji, zobacz [Use Azure DNS with an Azure public IP address (Używanie usługi Azure DNS z publicznym adresem IP platformy Azure)](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
    |Utwórz adres IPv6 (lub IPv4)|Nie| Określa, czy jest wyświetlany IPv6 lub protokół IPv4 jest zależna od wybierz dla **IP w wersji**. Na przykład w przypadku wybrania **IPv4** dla **IP w wersji**, **IPv6** jest wyświetlany w tym miejscu. W przypadku wybrania *standardowe* dla **SKU**, nie masz opcję, aby utworzyć adres IPv6.
    |Nazwa (tylko widoczne, gdy zaznaczono **utworzyć adres IPv6 (lub IPv4)** wyboru)|Tak, w przypadku wybrania **utworzyć IPv6** (lub IPv4) pole wyboru.|Nazwa musi być inna niż nazwa wprowadzona dla pierwszego **nazwa** na tej liście. Jeśli użytkownik chce utworzyć adres IPv6 i IPv4, portal tworzy dwa oddzielne zasoby publicznych adresów IP adres, z każdej wersji adresu IP przypisane do niej.|
    |Przypisywanie adresów IP (tylko widoczne, gdy zaznaczono **utworzyć adres IPv6 (lub IPv4)** wyboru)|Tak, w przypadku wybrania **utworzyć IPv6** (lub IPv4) pole wyboru.|Jeśli pole wyboru **utworzyć adres IPv4**, można wybrać metodę przypisania. Jeśli pole wyboru **utworzyć adres IPv6**, nie można wybrać metodę przypisania jako musi być **dynamiczne**.|
    |Subskrypcja|Yes|Musi istnieć w tym samym [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) jako zasób, aby skojarzyć publiczny adres IP.|
    |Grupa zasobów|Yes|W tym samym lub różnych, może istnieć [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) jako zasób, aby skojarzyć publiczny adres IP.|
    |Lokalizacja|Yes|Musi istnieć w tym samym [lokalizacji](https://azure.microsoft.com/regions), również określone jako region, jako zasób, którą chcesz skojarzyć z publicznym adresem IP adres.|
    |Strefa dostępności| Nie | To ustawienie jest wyświetlany tylko w przypadku wybrania obsługiwanych lokalizacji. Aby uzyskać listę obsługiwanych lokalizacji, zobacz [omówienie stref dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W przypadku wybrania **podstawowe** jednostka SKU, *Brak* jest automatycznie wybrana. Jeśli wolisz zagwarantować dla określonej strefy, można wybrać określonej strefy. Wybór albo nie jest strefowo nadmiarowy. W przypadku wybrania **standardowe** SKU: Strefowo nadmiarowy jest automatycznie wybrana i sprawia, że ścieżki danych odporność na awarie strefy. Jeśli wolisz zagwarantować dla określonej strefy, który nie był odporny na awarie strefy, można wybrać określonej strefy.

**Polecenia**

Chociaż portalu udostępnia opcję, aby utworzyć dwa zasoby publicznych adresów IP address (jeden adres IPv4 i IPv6 jeden), następujących poleceń programu PowerShell i interfejsu wiersza polecenia Utwórz jeden zasób z adresem dla jednej wersji adresu IP lub innych. Jeśli chcesz, aby dwa zasoby publicznych adresów IP address, jeden dla każdej wersji adresu IP musi Uruchom polecenie dwa razy, określając inne nazwy i wersje zasoby publicznych adresów IP.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#az_network_public_ip_create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>Wyświetlanie, zmieniać ustawienia lub usuwanie publicznego adresu IP

1. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *publicznego adresu ip*. Gdy **publicznego adresu IP, adresy** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz nazwę publicznego adresu IP wyświetlić, Zmień ustawienia lub usunąć z listy.
3. Wykonaj jedną z następujących opcji, w zależności od tego, czy chcesz wyświetlić, usunąć lub zmienić publicznego adresu IP.
    - **Widok**: **omówienie** sekcji przedstawiono ustawienia klucza publicznego adresu IP, takich jak interfejs sieciowy jest ona skojarzona (Jeśli adres jest skojarzony z karty sieciowej). Portalu nie wyświetla wersję adresu (IPv4 lub IPv6). Aby wyświetlić informacje o wersji, polecenie programu PowerShell lub interfejsu wiersza polecenia do wyświetlania publicznego adresu IP. Wersja adresów IP w przypadku protokołu IPv6, przypisany adres nie jest wyświetlana w portalu, programu PowerShell lub interfejsu wiersza polecenia.
    - **Usuń**: można usunąć publicznego adresu IP, wybierz **usunąć** w **omówienie** sekcji. Jeśli adres jest obecnie skojarzony z konfiguracją protokołu IP, nie można usunąć. Jeśli adres jest obecnie skojarzony z konfiguracją, wybierz **Utwórz skojarzenie** można usunąć skojarzenia adresu z konfiguracji protokołu IP.
    - **Zmień**: Wybierz **konfiguracji**. Zmień ustawienia, korzystając z informacji w kroku 4 [tworzenie publicznego adresu IP](#create-a-public-ip-address). Aby zmienić przypisanie adresu IPv4 ze statycznego na dynamiczny, musi najpierw skojarzenie publiczny adres IPv4, z którą skojarzona jest do konfiguracji adresu IP. Można modyfikować metody przypisywania do dynamicznego i wybierz **skojarzyć** można skojarzyć adres IP adres do tej samej konfiguracji IP, inną konfigurację lub pozostawić Usunięto skojarzenie. Aby usunąć skojarzenie publicznego adresu IP, w **omówienie** zaznacz **Utwórz skojarzenie**.

    >[!WARNING]
    >Zmiana metody przypisywania ze statycznego na dynamiczny, utracisz adres IP, który został przypisany do publicznego adresu IP. Gdy Azure publicznych serwerów DNS Obsługa mapowanie między statyczne lub dynamiczne adresy i wszelkie etykieta nazwy DNS (Jeśli zdefiniowany jest jeden), dynamicznego adresu IP, można zmienić po uruchomieniu maszyny wirtualnej po jest w stanie zatrzymania (cofnięciu przydziału). Aby uniemożliwić zmianę adresu, Przypisz statyczny adres IP.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ sieci publicznego adresu ip listy](/cli/azure/network/public-ip#az_network_public_ip_list) publiczne adresy IP, [az sieci publicznego adresu ip Pokazywanie](/cli/azure/network/public-ip#az_network_public_ip_show) pokazanie ustawień; [az sieci ip publicznego aktualizacji](/cli/azure/network/public-ip#az_network_public_ip_update) zaktualizować; [usunąć publicznej sieci az ip](/cli/azure/network/public-ip#az_network_public_ip_delete) do usunięcia|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) można pobrać obiektu publiczny adres IP i wyświetlić jej ustawienia [AzureRmPublicIpAddress zestaw](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress) można zaktualizować ustawień; [AzureRmPublicIpAddress Usuń](/powershell/module/azurerm.network/remove-azurermpublicipaddress) do usunięcia|

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań na publiczne adresy IP, Twoje konto musi mieć przypisaną do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rola przypisana odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                                             | Name (Nazwa)                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Przeczytaj publicznego adresu IP                                          |
| Microsoft.Network/publicIPAddresses/write                          | Utwórz lub zaktualizuj publiczny adres IP                           |
| Microsoft.Network/publicIPAddresses/delete                         | Usuwanie publicznego adresu IP                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Skojarzenie publicznego adresu IP do zasobu                    |

## <a name="next-steps"></a>Kolejne kroki

- Utwórz publiczny adres IP, za pomocą [PowerShell](powershell-samples.md) lub [interfejsu wiersza polecenia Azure](cli-samples.md) przykładowe skrypty lub przy użyciu usługi Azure [szablonów Resource Manager](template-samples.md)
- Tworzenie i stosowanie [Azure zasad](policy-samples.md) publicznego adresu IP adresów
- Przypisz publicznych adresów IP podczas tworzenia [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) maszyny wirtualnej platformy Azure, [modułu równoważenia obciążenia Azure Azure internetowych](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Brama aplikacji w](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [połączenia lokacja lokacja przy użyciu bramy sieci VPN Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), lub [zestawu skalowania maszyn wirtualnych Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
