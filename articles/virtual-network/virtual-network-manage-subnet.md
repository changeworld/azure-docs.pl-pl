---
title: Dodawanie, zmienianie lub usuwanie podsieć sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać, zmienić lub usunąć podsieć sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: jdial
ms.openlocfilehash: 550fe16c5997947b528d284b7afdce9af0b7a56b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Dodawanie, zmienianie lub usuwanie podsieć sieci wirtualnej

Dowiedz się, jak dodać, zmienić lub usunąć podsieć sieci wirtualnej. Jeśli nie znasz z sieciami wirtualnymi, aby dodać, zmienić lub usunąć podsieć, zaleca się przeczytanie [Omówienie usługi Azure Virtual Network](virtual-networks-overview.md) i [tworzenie, zmienianie lub usuwanie sieci wirtualnej](manage-virtual-network.md). Wszystkie zasoby Azure wdrożony w sieci wirtualnej są wdrażane w podsieci sieci wirtualnej.
 
## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.2.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

## <a name="add-a-subnet"></a>Dodaj podsieć

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz chcesz dodać podsieci do sieci wirtualnej.
3. W obszarze **ustawienia**, wybierz pozycję **podsieci**.
4. Wybierz **+ podsieci**.
5. Wprowadź wartości dla następujących parametrów:
    - **Nazwa**: Nazwa musi być unikatowa w ramach sieci wirtualnej.
    - **Zakres adresów**: zakres musi być unikatowa w ramach przestrzeni adresowej dla sieci wirtualnej. Zakres nie może nakładać się na inne zakresy adresów podsieci w sieci wirtualnej. Przestrzeń adresowa należy określić przy użyciu notacji Classless Inter-Domain Routing (CIDR). Na przykład w sieci wirtualnej z 10.0.0.0/16 przestrzeni adresów, można zdefiniować przestrzeni adresowej podsieci 10.0.0.0/24. Najmniejszą zakres, który można określić jest /29, co umożliwia osiem adresów IP podsieci. Azure rezerwuje pierwszy i ostatni adres w każdej podsieci dla zgodności protokołu. Trzy dodatkowe adresy są zarezerwowane do użycia usługi Azure. W związku z tym Definiowanie podsieci z /29 adresów zakresu wyników w trzech można używać adresów IP w podsieci. Jeśli planowane jest połączenie wirtualnej sieci do bramy sieci VPN, należy utworzyć podsieć bramy. Dowiedz się więcej o [zagadnienia dotyczące zakresu określonego adresu podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Po dodaniu podsieci w określonych warunkach, można zmienić zakresu adresów. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [zmienić ustawienia podsieci](#change-subnet-settings).
    - **Grupy zabezpieczeń sieci**: można skojarzyć zero lub jedną istniejącą sieciową grupę zabezpieczeń do podsieci do filtrowania ruchu sieciowego przychodzącego i wychodzącego do podsieci. Grupy zabezpieczeń sieci musi istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej o [sieciowej grupy zabezpieczeń](security-overview.md) i [jak utworzyć grupę zabezpieczeń sieci](tutorial-filter-network-traffic.md).
    - **Tabela tras**: można skojarzyć zero lub jedną istniejącą tabelę tras do podsieci, aby kontrolować routingu ruchu sieciowego z innymi sieciami. Tabela tras musi istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej o [Azure routingu](virtual-networks-udr-overview.md) i [jak utworzyć tabelę tras](tutorial-create-route-table-portal.md)
    - **Punkty końcowe usługi:** podsieci może być zerowy lub ma wiele punktów końcowych usługi włączyć dla niego. Aby włączyć punkt końcowy usługi dla usługi, wybierz usługę lub usługi, które chcesz włączyć usługi punktów końcowych z **usług** listy. Aby usunąć punkt końcowy usługi, usuń zaznaczenie ma zostać usunięty punkt końcowy usługi dla usługi. Aby dowiedzieć się więcej na temat punktów końcowych usług, zobacz [Omówienie punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md). Po włączeniu punktu końcowego usługi, musisz również włączyć dostęp do sieci dla podsieci zasobu utworzone za pomocą usługi. Na przykład po włączeniu punktu końcowego usługi *Microsoft.Storage*, należy również włączyć dostęp do sieci do wszystkich kont usługi Azure Storage chcesz przyznać dostęp do sieci. Aby uzyskać więcej informacji na temat dostępu do sieci z podsieciami, które punkt końcowy usługi jest włączone, aby włączyć, można znaleźć w dokumentacji dla poszczególnych usług są włączone dla punktu końcowego usługi.
6. Aby dodać podsieci do wybranej sieci wirtualnej, wybierz **OK**.

**Polecenia**

- Azure CLI: [utworzyć az podsieci sieci wirtualnej](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)
- PowerShell: [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Zmień ustawienia podsieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, która zawiera podsieci, aby zmienić ustawienia.
3. W obszarze **ustawienia**, wybierz pozycję **podsieci**.
4. Na liście podsieci Wybierz podsieć, aby zmienić ustawienia. Można zmienić następujące ustawienia:

    - **Zakres adresów:** zasobów nie są wdrażane w podsieci, można zmienić zakresu adresów. Jeśli istnieje wszystkie zasoby w tej podsieci, przeniesienie zasobów do innej podsieci, albo usuń je najpierw z podsieci. Czynności, które należy wykonać, aby przenieść lub usunąć zasób zależy od zasobu. Aby dowiedzieć się, jak przenieść lub usunąć zasoby, które znajdują się w podsieci, przeczytaj dokumentację dla każdego typu zasobu, który chcesz przenieść lub usunąć. Zobacz ograniczenia **zakres adresów** w kroku 5 [Dodaj podsieć](#add-a-subnet).
    - **Użytkownicy**: dostęp do tej podsieci można kontrolować za pomocą wbudowanych ról lub własne niestandardowe role. Aby dowiedzieć się więcej na temat Przypisywanie ról i użytkownikom uzyskiwanie dostępu do tej podsieci, zobacz [umożliwia zarządzanie dostępem do zasobów platformy Azure przypisania roli](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
    - Aby uzyskać informacje na temat zmiany **sieciowej grupy zabezpieczeń**, **tabeli tras**, **użytkowników**, i **punkty końcowe usługi**, zobacz krok 5 w [ Dodaj podsieć](#add-a-subnet).
5. Wybierz pozycję **Zapisz**.

**Polecenia**

- Azure CLI: [zaktualizować podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Usuń podsieć

Tylko wtedy, gdy nie ma żadnych zasobów w podsieci, można usunąć podsieci. Jeśli dostępne są zasoby w tej podsieci, należy usunąć zasoby, które znajdują się w podsieci, aby można było usunąć podsieci. Czynności, które należy wykonać, aby usunąć zasób zależy od zasobu. Aby dowiedzieć się, jak usunąć zasoby, które znajdują się w podsieci, przeczytaj dokumentację dla każdego typu zasobu, który chcesz usunąć.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieć wirtualną, która zawiera podsieć, którą chcesz usunąć z listy sieci wirtualnych.
3. W obszarze **ustawienia**, wybierz pozycję **podsieci**.
4. Na liście podsieci, wybierz **...** , po prawej stronie podsieci chcesz usunąć
5. Wybierz **usunąć**, a następnie wybierz **tak**.

**Polecenia**

- Azure CLI: [usunąć az sieci wirtualnej](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań w podsieciach, Twoje konto musi mieć przypisaną do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rola przypisana odpowiednie uprawnienia są wymienione w poniższej tabeli:

|Operacja                                                                |   Nazwa operacji                               |
|-----------------------------------------------------------------------  |   -------------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Pobierz podsieć sieci wirtualnej                   |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Utwórz lub zaktualizuj podsieć sieci wirtualnej      |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Usuń podsieć sieci wirtualnej                |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Dołącz do sieci wirtualnej                         |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Dołącz do usługi do podsieci                     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Pobierz maszyny wirtualne podsieci sieci wirtualnej  |
