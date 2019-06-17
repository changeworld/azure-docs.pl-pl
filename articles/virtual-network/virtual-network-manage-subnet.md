---
title: Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: 8a8feb0f12fbf5eadcddf239ff2e13c058a566fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693305"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej

Dowiedz się, jak dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej. Wszystkie zasoby platformy Azure wdrożonych w sieci wirtualnej są wdrażane w podsieci sieci wirtualnej. Jeśli dopiero zaczynasz pracę z sieciami wirtualnymi, możesz dowiedzieć się więcej o nich w [Omówienie usługi Virtual network](virtual-networks-overview.md) lub wykonując [samouczek](quick-create-portal.md). Aby utworzyć, zmienić, lub usunąć sieci wirtualnej, zobacz [zarządzanie siecią wirtualną](manage-virtual-network.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

## <a name="add-a-subnet"></a>Dodawanie podsieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, o których chcesz dodać podsieć, którą chcesz.
3. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**.
4. Wybierz **+ podsieć**.
5. Wprowadź wartości dla następujących parametrów:
   - **Nazwa**: Nazwa musi być unikatowa w obrębie sieci wirtualnej. Maksymalna zgodność z innymi usługami Azure zaleca się, za pomocą litery jako pierwszy znak nazwy. Na przykład usługi Azure Application Gateway nie zostaną wdrożone do podsieci, która ma nazwę, która rozpoczyna się od numeru.
   - **Zakres adresów**: Zakres musi być unikatowa w obrębie przestrzeni adresowej dla sieci wirtualnej. Zakres nie może nakładać się na inne zakresy adresów podsieci w sieci wirtualnej. Przestrzeń adresowa, należy określić przy użyciu notacji Bezklasowego routingu międzydomenowego (CIDR). Na przykład w sieci wirtualnej przy użyciu przestrzeni adresów 10.0.0.0/16, można zdefiniować przestrzeni adresów podsieci 10.0.0.0/24. Najmniejsza zakres, który można określić jest rozmiarze/29, zapewniającą osiem adresów IP w podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci na potrzeby zgodności protokołów. Trzy dodatkowe adresy są zarezerwowane do użycia usług platformy Azure. W rezultacie Definiowanie podsieci o rozmiarze/29 adresów zakresu wyniki w trzech można używać adresów IP w podsieci. Jeśli planujesz połączyć sieć wirtualną z bramą sieci VPN, należy utworzyć podsieć bramy. Dowiedz się więcej o [zakresu określonego adresu informacje dotyczące podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Można zmienić zakres adresów, po dodaniu tej podsieci, w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [zmienić ustawienia podsieci](#change-subnet-settings).
   - **Sieciowa grupa zabezpieczeń**: Możesz skojarzyć zero lub jeden istniejącej sieciowej grupy zabezpieczeń do podsieci do filtrowania ruchu przychodzącego i wychodzącego ruchu sieciowego dla podsieci. Sieciowa grupa zabezpieczeń muszą istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualną. Dowiedz się więcej o [sieciowe grupy zabezpieczeń](security-overview.md) i [tworzenie sieciowej grupy zabezpieczeń](tutorial-filter-network-traffic.md).
   - **Tabela tras**: Możesz skojarzyć zero lub jeden istniejącej tabeli tras do podsieci, aby sterować routingiem ruchu sieciowego z innymi sieciami. Tabela tras, musi istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualną. Dowiedz się więcej o [Azure routing](virtual-networks-udr-overview.md) i [sposób tworzenia tabeli tras](tutorial-create-route-table-portal.md)
   - **Punkty końcowe usługi:** Podsieć może mieć zero lub wielu włączonych punktów końcowych usługi dla niego. Aby włączyć punkt końcowy usługi dla usługi, wybierz usługę lub usługi, które chcesz włączyć punkty końcowe usługi z **usług** listy. Lokalizacja jest konfigurowana automatycznie dla punktu końcowego. Domyślnie punkty końcowe usługi są skonfigurowane do regionu sieci wirtualnej. Dla usługi Azure Storage na potrzeby scenariuszy rozwiązania regionalnej pracy awaryjnej, punkty końcowe są automatycznie konfigurowane do [sparowanych regionów platformy Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   - **Delegowanie podsieci:** Podsieć może mieć zero do wielu delegacji, które włączono dla niego. Delegowanie podsieci umożliwia jawne uprawnienia do usługi, aby utworzyć zasoby specyficzne dla usługi w podsieci za pomocą unikatowego identyfikatora podczas wdrażania usługi. Aby delegować do usługi, wybierz usługi ma działać delegowanie z **usług** listy.

       Aby usunąć punkt końcowy usługi, usuń zaznaczenie usługi, aby usunąć punkt końcowy usługi dla. Aby dowiedzieć się więcej na temat punktów końcowych usługi i usługi mogą być włączone dla, zobacz [Omówienie punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md). Po włączeniu punktu końcowego usługi dla usługi, musisz również włączyć dostęp do sieci dla podsieci dla zasobu utworzonych za pomocą usługi. Na przykład po włączeniu punktu końcowego usługi dla *Microsoft.Storage*, należy również włączyć dostęp sieciowy do wszystkich kont usługi Azure Storage, które chcesz przyznać dostęp do sieci. Aby uzyskać szczegółowe informacje o tym, jak włączyć dostęp sieciowy do podsieci, które włączono punkt końcowy usługi, zobacz dokumentację poszczególnych usług został włączony punkt końcowy usługi dla.

     Aby sprawdzić, czy punkt końcowy usługi jest włączona dla podsieci, Wyświetl [obowiązujące trasy](diagnose-network-routing-problem.md) dla dowolnego interfejsu sieciowego w podsieci. Po skonfigurowaniu punktu końcowego, zobacz *domyślne* trasy z prefiksami adresów usługi, a element nexthoptype o wartości **VirtualNetworkServiceEndpoint**. Aby dowiedzieć się więcej na temat routingu, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
6. Aby dodać podsieci do sieci wirtualnej, które wybrano, wybierz **OK**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [tworzenie az podsieci sieci wirtualnej](/cli/azure/network/vnet/subnet)
- Program PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Zmień ustawienia podsieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, która zawiera podsieci, aby zmienić ustawienia.
3. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**.
4. Na liście podsieci Wybierz podsieć, aby zmienić ustawienia. Można zmienić następujące ustawienia:

    - **Zakres adresów:** Brak zasobów w przypadku wdrożenia w obrębie podsieci, można zmienić zakresu adresów. Jeśli istnieje żadnych zasobów w podsieci, przeniesienie zasobów do innej podsieci, albo usuń je najpierw z podsieci. Kroki, które należy wykonać, aby przenieść lub usunąć zasób różnią się zależnie od zasobu. Aby dowiedzieć się, jak przenieść lub usunąć zasoby, które znajdują się w podsieci, przeczytaj dokumentację dla każdego typu zasobu, który chcesz przenieść lub usunąć. Zobacz ograniczenia **zakres adresów** w kroku nr 5 procedury [Dodaj podsieć](#add-a-subnet).
    - **Użytkownicy**: Możesz kontrolować dostęp do tej podsieci przy użyciu ról wbudowanych lub własnych ról niestandardowych. Aby dowiedzieć się więcej na temat przypisywania ról i użytkowników, aby uzyskać dostęp do podsieci, zobacz [zarządzanie dostępem do zasobów platformy Azure za pomocą przypisania roli](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Sieciowa grupa zabezpieczeń** i **tabeli tras**: Zobacz krok 5 z [Dodaj podsieć](#add-a-subnet).
    - **Punkty końcowe usługi**: Zobacz punktów końcowych usługi w kroku 5 [Dodaj podsieć](#add-a-subnet). Po włączeniu punktu końcowego usługi do istniejącej podsieci, upewnij się, że żadne krytyczne zadania nie są uruchomione na żaden zasób w podsieci. Punkty końcowe usługi Przełącz tras każdego interfejsu sieciowego w podsieci z przy użyciu trasy domyślnej z *0.0.0.0/0* prefiksu i typ następnego przeskoku *Internet*, za pomocą nowej trasy o prefiksy usługi i typ następnego przeskoku adresów *VirtualNetworkServiceEndpoint*. Podczas przełączania może zostać rozwiązana dowolnych otwartych połączeń TCP. Punkt końcowy usługi nie jest włączone, dopóki przepływów ruchu do usługi dla wszystkich interfejsów sieciowych są aktualizowane przy użyciu nowej trasy. Aby dowiedzieć się więcej na temat routingu, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
    - **Delegowanie podsieci:** Zobacz punktów końcowych usługi w kroku 5 [Dodaj podsieć](#add-a-subnet). Delegowanie podsieci można zmodyfikować w taki sposób, aby zero lub wielu delegacji włączyć dla niego. Jeśli zasób usługi została już wdrożona w podsieci, delegowanie podsieci nie można usunąć, dopóki nie zostaną usunięte wszystkie zasoby dla usługi. Aby delegować do innej usługi, wybierz usługę, o których ma działać delegowanie z **usług** listy.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacji podsieci sieci wirtualnej sieci az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- Program PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Tylko wtedy, gdy nie ma żadnych zasobów w podsieci, można usunąć podsieci. W przypadku zasobów w podsieci, należy usunąć zasoby, które znajdują się w podsieci, aby można było usunąć podsieć. Kroki, które należy wykonać w celu usuwania zasobu różnią się zależnie od zasobu. Aby dowiedzieć się, jak usunąć zasoby, które znajdują się w podsieci, przeczytaj dokumentację dla każdego typu zasobu, który chcesz usunąć.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, która zawiera podsieć, którą chcesz usunąć.
3. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**.
4. Na liście podsieci wybierz **...** , po prawej stronie podsieci chcesz usunąć
5. Wybierz **Usuń**, a następnie wybierz pozycję **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [Usuń az sieci w sieci wirtualnej](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- Program PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań w podsieciach, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie działania wymienione w poniższej tabeli:

|Akcja                                                                   |   Name (Nazwa)                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Przeczytaj podsieci sieci wirtualnej              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Utwórz lub zaktualizuj podsieci sieci wirtualnej  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Usuwanie podsieci sieci wirtualnej            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Dołącz do sieci wirtualnej                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Włączanie punktu końcowego usługi dla podsieci     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Pobierz maszyny wirtualne w podsieci       |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie sieci wirtualnej i podsieci za pomocą [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonów usługi Resource Manager](template-samples.md)
- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych
