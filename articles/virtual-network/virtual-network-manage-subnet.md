---
title: Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak dodać, zmienić lub usunąć podsieć sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: e5b78969f6b4315bb02b3f4152c6eeab94adddb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246946"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Dodawanie, zmienianie i usuwanie podsieci sieci wirtualnej

Dowiedz się, jak dodać, zmienić lub usunąć podsieć sieci wirtualnej. Wszystkie zasoby platformy Azure wdrożone do sieci wirtualnej są wdrażane w podsieci w ramach sieci wirtualnej. Jeśli jesteś nowym użytkownikiem sieci wirtualnych, możesz dowiedzieć się więcej o nich w [przeglądzie sieci wirtualnej](virtual-networks-overview.md) lub wypełniając [przewodnik Szybki start](quick-create-portal.md). Aby dowiedzieć się więcej o zarządzaniu siecią wirtualną, zobacz [Tworzenie, zmienianie lub usuwanie sieci wirtualnej](manage-virtual-network.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Następnie wykonaj jedno z tych zadań przed rozpoczęciem kroków w dowolnej sekcji tego artykułu: 

- **Użytkownicy portalu:** Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

- **Użytkownicy programu PowerShell:** Uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Przeglądarka usługi Azure Cloud Shell znajdź listę rozwijana **Wybierz środowisko,** a następnie wybierz pozycję **PowerShell,** jeśli nie jest jeszcze zaznaczona.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom `Connect-AzAccount` również, aby utworzyć połączenie z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI):** uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchom interfejs wiersza polecenia z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom `az login` również, aby utworzyć połączenie z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do roli [roli współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

## <a name="add-a-subnet"></a>Dodawanie podsieci

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby wyświetlić sieci wirtualne. Wyszukaj i wybierz **sieć wirtualną**.

2. Wybierz nazwę sieci wirtualnej, do której chcesz dodać podsieć.

3. W **obszarze Ustawienia**wybierz pozycję **Podsieć podsieci** > **Subnet**.

4. W oknie dialogowym **Dodawanie podsieci** wprowadź wartości następujących ustawień:

    | Ustawienie | Opis |
    | --- | --- |
    | **Nazwa** | Nazwa musi być unikatowa w obrębie sieci wirtualnej. Aby uzyskać maksymalną zgodność z innymi usługami platformy Azure, zaleca się używanie litery jako pierwszego znaku nazwy. Na przykład brama aplikacji platformy Azure nie zostanie wdrożona w podsieci, która ma nazwę, która rozpoczyna się od liczby. |
    | **Zakres adresów** | <p>Zakres musi być unikatowy w przestrzeni adresowej dla sieci wirtualnej. Zakres nie może pokrywać się z innymi zakresami adresów podsieci w sieci wirtualnej. Przestrzeń adresowa musi być określona przy użyciu notacji CIDR (Classless Inter-Domain Routing).</p><p>Na przykład w sieci wirtualnej z przestrzenią adresową *10.0.0.0/16*można zdefiniować przestrzeń adresową podsieci *10.0.0.0/22*. Najmniejszy zakres, który można określić, to */29*, który zawiera osiem adresów IP dla podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci dla zgodności protokołu. Trzy dodatkowe adresy są zarezerwowane dla użycia usługi platformy Azure. W rezultacie zdefiniowanie podsieci z zakresem *adresów /29* powoduje trzy użyteczne adresy IP w podsieci.</p><p>Jeśli planujesz połączyć sieć wirtualną z bramą sieci VPN, musisz utworzyć podsieć bramy. Dowiedz się więcej o [określonych zagadnieniach dotyczących zakresu adresów dla podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Zakres adresów można zmienić po dodaniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zmienianie ustawień podsieci](#change-subnet-settings).</p> |
    | **Grupa zabezpieczeń sieci** | Aby filtrować przychodzący i wychodzący ruch sieciowy dla podsieci, można skojarzyć istniejącą grupę zabezpieczeń sieci z podsiecią. Sieciowa grupa zabezpieczeń musi istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej o [sieciowych grupach zabezpieczeń](security-overview.md) i [o tym, jak utworzyć grupę zabezpieczeń sieci .](tutorial-filter-network-traffic.md) |
    | **Tabela tras** | Aby kontrolować routing ruchu sieciowego do innych sieci, można opcjonalnie skojarzyć istniejącą tabelę tras z podsiecią. Tabela trasy musi istnieć w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej o [routingu platformy Azure](virtual-networks-udr-overview.md) i [o tym, jak utworzyć tabelę tras](tutorial-create-route-table-portal.md). |
    | **Punkty końcowe usługi** | <p>Podsieć może opcjonalnie mieć jeden lub więcej punktów końcowych usługi włączone dla niego. Aby włączyć punkt końcowy usługi dla usługi, wybierz usługę lub usługi, które chcesz włączyć punkty końcowe usługi z listy **Usługi.** Platforma Azure automatycznie konfiguruje lokalizację dla punktu końcowego. Domyślnie platforma Azure konfiguruje punkty końcowe usługi dla regionu sieci wirtualnej. Aby obsługiwać regionalne scenariusze pracy awaryjnej, platforma Azure automatycznie konfiguruje punkty końcowe w [sparowanych regionach platformy Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) dla usługi Azure Storage.</p><p>Aby usunąć punkt końcowy usługi, usuń zaznaczenie usługi, dla której chcesz usunąć punkt końcowy usługi. Aby dowiedzieć się więcej o punktach końcowych usługi i usługach, dla których można je włączyć, zobacz [Punkty końcowe usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md). Po włączeniu punktu końcowego usługi dla usługi, należy również włączyć dostęp do sieci dla podsieci dla zasobu utworzonego za pomocą usługi. Na przykład jeśli włączysz punkt końcowy usługi dla **microsoft.storage,** należy również włączyć dostęp do sieci do wszystkich kont usługi Azure Storage, do których chcesz udzielić dostępu do sieci. Aby włączyć dostęp do sieci do podsieci, dla których jest włączony punkt końcowy usługi, zobacz dokumentację dla poszczególnych usług, dla których włączono punkt końcowy usługi.</p><p>Aby sprawdzić, czy punkt końcowy usługi jest włączony dla podsieci, wyświetl [efektywne trasy](diagnose-network-routing-problem.md) dla dowolnego interfejsu sieciowego w podsieci. Podczas konfigurowania punktu końcowego zostanie *wyświetlonych domyślna* trasa z prefiksami adresu usługi i następnym typem przeskoku **VirtualNetworkServiceEndpoint**. Aby dowiedzieć się więcej o routingu, zobacz [Routing ruchu sieci wirtualnej](virtual-networks-udr-overview.md).</p> |
    | **Delegowanie podsieci** | Podsieć może opcjonalnie mieć jedną lub więcej delegacji włączone dla niego. Delegowanie podsieci daje jawne uprawnienia do usługi do tworzenia zasobów specyficznych dla usługi w podsieci przy użyciu unikatowego identyfikatora podczas wdrażania usługi. Aby delegować usługę, wybierz usługę, do której chcesz delegować, z listy **Usługi.** |

5. Aby dodać podsieć do wybranej sieci wirtualnej, wybierz przycisk **OK**.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Zmienianie ustawień podsieci

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby wyświetlić sieci wirtualne. Wyszukaj i wybierz **sieć wirtualną**.

2. Wybierz nazwę sieci wirtualnej zawierającej podsieć, którą chcesz zmienić.

3. W **obszarze Ustawienia**wybierz pozycję **Podsieci**.

4. Na liście podsieci wybierz podsieć, dla której chcesz zmienić ustawienia.

5. Na stronie podsieci zmień dowolne z następujących ustawień:

    | Ustawienie | Opis |
    | --- | --- |
    | **Zakres adresów** | Jeśli w podsieci nie zostaną wdrożone żadne zasoby, można zmienić zakres adresów. Jeśli w podsieci istnieją jakieś zasoby, należy najpierw przenieść zasoby do innej podsieci lub najpierw usunąć je z podsieci. Kroki, które należy wykonać, aby przenieść lub usunąć zasób różnią się w zależności od zasobu. Aby dowiedzieć się, jak przenosić lub usuwać zasoby, które znajdują się w podsieci, przeczytaj dokumentację dla każdego z tych typów zasobów. Zobacz ograniczenia **dotyczące zakresu adresów** w kroku 4 [dodaj podsieć](#add-a-subnet). |
    | **Użytkownicy** | Dostęp do podsieci można kontrolować za pomocą wbudowanych ról lub własnych ról niestandardowych. Aby dowiedzieć się więcej o przypisywaniu ról i użytkowników do podsieci, zobacz [Dodawanie przypisania roli](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Sieciowa grupa zabezpieczeń** i **Tabela tras** | Zobacz krok 4 [dodawania podsieci](#add-a-subnet). |
    | **Punkty końcowe usługi** | <p>Zobacz punkty końcowe usługi w kroku 4 [Dodaj podsieć](#add-a-subnet). Podczas włączania punktu końcowego usługi dla istniejącej podsieci upewnij się, że żadne krytyczne zadania nie są uruchomione na żadnym zasobie w podsieci. Punkty końcowe usługi przełączają trasy na każdym interfejsie sieciowym w podsieci. Punkty końcowe usługi przejść od korzystania z trasy domyślnej z prefiksem adresu *0.0.0.0/0* i następny typ przeskoku *Internet*, do korzystania z nowej trasy z prefiksami adresu usługi i następny typ przeskoku *VirtualNetworkServiceEndpoint*.</p><p>Podczas przełączania wszystkie otwarte połączenia TCP mogą zostać zakończone. Punkt końcowy usługi nie jest włączona, dopóki przepływy ruchu do usługi dla wszystkich interfejsów sieciowych są aktualizowane o nową trasę. Aby dowiedzieć się więcej o routingu, zobacz [Routing ruchu sieci wirtualnej](virtual-networks-udr-overview.md).</p> |
    | **Delegowanie podsieci** | Zobacz punkty końcowe usługi w kroku 4 [Dodaj podsieć](#add-a-subnet). Delegowanie podsieci można zmodyfikować do zera lub włączone jest wiele delegacji. Jeśli zasób dla usługi jest już wdrożony w podsieci, delegowania podsieci nie można dodać ani usunąć, dopóki nie zostaną usunięte wszystkie zasoby usługi. Aby delegować inną usługę, wybierz usługę, do której chcesz delegować, z listy **Usługi.** |

6. Wybierz **pozycję Zapisz**.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Podsieci można usunąć tylko wtedy, gdy w podsieci nie ma żadnych zasobów. Jeśli zasoby znajdują się w podsieci, należy usunąć te zasoby, zanim będzie można usunąć podsieć. Kroki podejmowane w celu usunięcia zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak usunąć zasoby, które znajdują się w podsieci, przeczytaj dokumentację dla każdego z tych typów zasobów.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby wyświetlić sieci wirtualne. Wyszukaj i wybierz **sieć wirtualną**.

2. Wybierz nazwę sieci wirtualnej zawierającej podsieć, którą chcesz usunąć.

3. W **obszarze Ustawienia**wybierz pozycję **Podsieci**.

4. Na liście podsieci wybierz podsieć, którą chcesz usunąć.

5. Wybierz **pozycję Usuń**, a następnie wybierz pozycję **Tak** w oknie dialogowym potwierdzenia.

### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [Usuwanie podsieci sieci az](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Usuń-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w podsieciach, konto musi być przypisane do [roli współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która ma przypisane odpowiednie akcje w poniższej tabeli:

|Akcja                                                                   |   Nazwa                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Odczytywanie podsieci sieci wirtualnej              |
|Microsoft.Network/virtualNetworks/podsieci/zapis                          |   Tworzenie lub aktualizowanie podsieci sieci wirtualnej  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Usuwanie podsieci sieci wirtualnej            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Dołącz do sieci wirtualnej                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Włączanie punktu końcowego usługi dla podsieci     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Pobierz maszyny wirtualne w podsieci       |

## <a name="next-steps"></a>Następne kroki

- Tworzenie sieci wirtualnej i podsieci przy użyciu przykładowych skryptów interfejsu [wiersza](cli-samples.md) polecenia [programu PowerShell](powershell-samples.md) lub platformy Azure lub przy użyciu [szablonów usługi](template-samples.md) Azure Resource Manager
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
