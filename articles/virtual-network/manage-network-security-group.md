---
title: Tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: f1353165954021cd949d6e46357d10514ee26b3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65560941"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci

Reguły zabezpieczeń sieciowych grup zabezpieczeń pozwalają do filtrowania typu ruchu sieciowego, które mogą być przenoszone do podsieci sieci wirtualnej i interfejsów sieciowych. Jeśli nie znasz z sieciowymi grupami zabezpieczeń, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md) Aby dowiedzieć się więcej o nich i ukończyć [filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md) samouczka, aby można było uzyskać pewne doświadczenie w zakresie sieci grupy zabezpieczeń.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

## <a name="work-with-network-security-groups"></a>Praca z sieciowymi grupami zabezpieczeń

Można utworzyć [Wyświetl wszystkie](#view-all-network-security-groups), [służy do wyświetlania szczegółów](#view-details-of-a-network-security-group), [zmienić](#change-a-network-security-group), i [Usuń](#delete-a-network-security-group) sieciowej grupy zabezpieczeń. Możesz również [skojarzyć lub usunąć skojarzenie](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) sieciową grupę zabezpieczeń z interfejsem sieciowym lub podsieci.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Istnieje limit liczby sieciowe grupy zabezpieczeń, utworzonej lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W lewym górnym rogu portalu, wybierz **+ Utwórz zasób**.
2. Wybierz **sieć**, a następnie wybierz **sieciowej grupy zabezpieczeń**.
3. Wprowadź **nazwa** dla sieciowej grupy zabezpieczeń, wybierz swoje **subskrypcji**, Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę zasobów, wybierz pozycję **Lokalizacji**, a następnie wybierz pozycję **Utwórz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [tworzenie az sieciowej](/cli/azure/network/nsg#az-network-nsg-create)
- Program PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Wyświetlanie wszystkich grup zabezpieczeń sieci

W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją. Sieciowe grupy zabezpieczeń, które istnieją w Twojej subskrypcji są wyświetlane.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [listę sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg#az-network-nsg-list)
- Program PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Wyświetl szczegóły sieciowej grupy zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci na liście, który chcesz wyświetlić szczegóły. W obszarze **ustawienia** można wyświetlić **reguły zabezpieczeń dla ruchu przychodzącego** i **reguły zabezpieczeń dla ruchu wychodzącego**, **interfejsy sieciowe** i  **Podsieci** sieciowa grupa zabezpieczeń jest skojarzona z. Można również włączyć lub wyłączyć **dzienniki diagnostyczne** i widok **obowiązujących reguł zabezpieczeń**. Aby dowiedzieć się więcej, zobacz [dzienniki diagnostyczne](virtual-network-nsg-manage-log.md) i [wyświetlanie obowiązujących reguł zabezpieczeń](diagnose-network-traffic-filter-problem.md).
3. Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, na liście, zobacz następujące artykuły:
    *   [Dziennik aktywności](../azure-monitor/platform/activity-logs-overview.md)
    *   [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
    *   [Tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Blokady](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skrypt automatyzacji](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- Program PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Zmień grupę zabezpieczeń sieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń* w polu wyszukiwania. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieciową grupę zabezpieczeń, które chcesz zmienić. Najbardziej typowe zmiany są [Dodawanie](#create-a-security-rule) lub [usuwanie](#delete-a-security-rule) reguły zabezpieczeń i [Associating lub skojarzenia sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym lub](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacji sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg#az-network-nsg-update)
- Program PowerShell: [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Skojarzyć lub usunąć skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym lub

Aby skojarzyć sieciową grupę zabezpieczeń do lub usuwanie skojarzenia sieciowej grupy zabezpieczeń z interfejsem sieciowym, zobacz [skojarzyć sieciową grupę zabezpieczeń do lub Anuluj skojarzenie sieciowej grupy zabezpieczeń z interfejsem sieciowym](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Aby skojarzyć sieciową grupę zabezpieczeń do lub usuwanie skojarzenia sieciowej grupy zabezpieczeń z podsiecią, zobacz [zmienić ustawienia podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Usuń sieciową grupę zabezpieczeń

Jeśli sieciowa grupa zabezpieczeń jest skojarzona podsieci lub interfejsy sieciowe, nie można usunąć. Skojarzenie sieciowej grupy zabezpieczeń ze wszystkich podsieci i interfejsów sieciowych, przed próbą usunięcia go.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń* w polu wyszukiwania. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieciową grupę zabezpieczeń, które chcesz usunąć z listy.
3. Wybierz **Usuń**, a następnie wybierz pozycję **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [Usuń az sieciowej](/cli/azure/network/nsg#az-network-nsg-delete)
- Program PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Praca z reguły zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera zero lub więcej reguł zabezpieczeń. Można utworzyć [Wyświetl wszystkie](#view-all-security-rules), [służy do wyświetlania szczegółów](#view-details-of-a-security-rule), [zmienić](#change-a-security-rule), i [Usuń](#delete-a-security-rule) regułę zabezpieczeń.

### <a name="create-a-security-rule"></a>Utwórz regułę zabezpieczeń

Istnieje limit liczby reguł na sieciową grupę zabezpieczeń można utworzyć dla lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń* w polu wyszukiwania. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieciową grupę zabezpieczeń z listy, który chcesz dodać regułę zabezpieczeń.
3. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** w obszarze **ustawienia**. Kilka istniejących reguł są wyświetlane. Niektóre z reguł, które nie mogły zostać dodane. Po utworzeniu sieciowej grupy zabezpieczeń, kilka domyślnych reguł zabezpieczeń są tworzone w nim. Aby dowiedzieć się więcej, zobacz [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules).  Nie można usunąć domyślnych reguł zabezpieczeń, ale można je zastąpić, reguły, które mają wyższy priorytet.
4. <a name = "security-rule-settings"></a>Wybierz **+ Dodaj**.  Wybierz lub Dodaj wartości dla następujących ustawień, a następnie wybierz **OK**:
    
    |Ustawienie  |Wartość  |Szczegóły  |
    |---------|---------|---------|
    |source     | Wybierz **wszelkie**, **grupy zabezpieczeń aplikacji**, **adresów IP**, lub **Tag usługi** dla reguł zabezpieczeń dla ruchu przychodzącego. Jeśli tworzysz regułę zabezpieczeń dla ruchu wychodzącego, opcje są takie same jak dla opcji **docelowy**.       | Jeśli wybierzesz **grupy zabezpieczeń aplikacji**, następnie wybierz jedno lub większe bezpieczeństwo aplikacji istniejących grup, które istnieją w tym samym regionie, co interfejs sieciowy. Dowiedz się, jak [Tworzenie grupy zabezpieczeń aplikacji](#create-an-application-security-group). Jeśli wybierzesz **grupy zabezpieczeń aplikacji** dla obu **źródła** i **docelowego**, interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą mieć ten sam sieć wirtualna. Jeśli wybierzesz **adresów IP**, następnie określ **źródła zakresy adresów IP/CIDR**. Można określić pojedynczej wartości lub rozdzielaną przecinkami listę wielu wartości. Przykład wielu wartości to 10.0.0.0/16, 192.188.1.1. Istnieją limity liczby wartości, które można określić. Zobacz [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) Aby uzyskać szczegółowe informacje. Jeśli wybierzesz **Tag usługi**, następnie wybierz jeden tag usługi. Tag usługi jest predefiniowany identyfikator kategorii adresów IP. Aby dowiedzieć się więcej na temat tagi usługi dostępne i co reprezentuje każdy tag, zobacz [tagów usług](security-overview.md#service-tags). Jeśli adres IP, które określisz jest przypisany do maszyny wirtualnej platformy Azure, upewnij się, że należy określić prywatny adres IP, nie publiczny adres IP przypisany do maszyny wirtualnej. Reguły zabezpieczeń są przetwarzane po Azure tłumaczy publiczny adres IP na prywatny adres IP dla reguły zabezpieczeń ruchu przychodzącego i przed Azure tłumaczy prywatny adres IP na publiczny adres IP dla reguły ruchu wychodzącego. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, zobacz [typy adresów IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Zakresy portów źródłowych     | Podaj pojedynczy port, np. 80, zakres portów, np. 1024 – 65535, lub rozdzielaną przecinkami listę pojedynczych portów i/lub zakresów portów, np. 80, 1024 – 65 535. Wprowadź znak gwiazdki, aby zezwolić na ruch na dowolnym porcie. | Porty i zakresy należy określić, które porty ruch jest dozwolony lub zablokowany przez regułę. Istnieją ograniczenia liczby portów, które możesz określić. Zobacz [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) Aby uzyskać szczegółowe informacje.  |
    |Miejsce docelowe     | Wybierz **wszelkie**, **grupy zabezpieczeń aplikacji**, **adresów IP**, lub **sieci wirtualnej** dla reguł zabezpieczeń dla ruchu wychodzącego. Jeśli tworzysz regułę zabezpieczeń dla ruchu przychodzącego opcje są takie same jak dla opcji **źródła**.        | Jeśli wybierzesz **grupy zabezpieczeń aplikacji** następnie należy wybrać co najmniej jeden istniejącej grupy zabezpieczeń aplikacji, które istnieją w tym samym regionie, co interfejs sieciowy. Dowiedz się, jak [Tworzenie grupy zabezpieczeń aplikacji](#create-an-application-security-group). Jeśli wybierzesz **grupy zabezpieczeń aplikacji**, następnie wybierz jeden istniejących aplikacji grupy zabezpieczeń, która istnieje w tym samym regionie, co interfejs sieciowy. Jeśli wybierzesz **adresów IP**, następnie określ **miejsce docelowe zakresy adresów IP/CIDR**. Podobnie jak **źródła** i **źródła zakresy adresów IP/CIDR**, można określić jeden lub wiele adresów lub zakresy i istnieją limity numeru można określić. Wybieranie **sieć wirtualna**, który jest tag usługi, oznacza to, że ruch jest dozwolony na wszystkie adresy IP w przestrzeni adresowej sieci wirtualnej. Jeśli adres IP, które określisz jest przypisany do maszyny wirtualnej platformy Azure, upewnij się, że należy określić prywatny adres IP, nie publiczny adres IP przypisany do maszyny wirtualnej. Reguły zabezpieczeń są przetwarzane po Azure tłumaczy publiczny adres IP na prywatny adres IP dla reguły zabezpieczeń ruchu przychodzącego i przed Azure tłumaczy prywatny adres IP na publiczny adres IP dla reguły ruchu wychodzącego. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, zobacz [typy adresów IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Docelowe zakresy portów     | Określ pojedynczą wartość lub listę wartości oddzielanych przecinkami. | Podobnie jak **zakresów portów źródłowych**, można określić jeden lub wiele portów i zakresów i istnieją limity numeru można określić. |
    |Protocol     | Wybierz **wszelkie**, **TCP**, lub **UDP**.        |         |
    |Akcja     | Wybierz **Zezwalaj** lub **Odmów**.        |         |
    |Priorytet     | Wprowadź wartość zawierającą od 100-4096 unikatowe dla wszystkich reguł zabezpieczeń w sieciowej grupie zabezpieczeń. |Reguły są przetwarzane w kolejności priorytetów. Im mniejsza liczba, tym wyższy priorytet. Zaleca się pozostawienie przerwę między liczbami priorytet podczas tworzenia reguły, takie jak 100, 200, 300. Pozostawienie przerw sprawia, że ułatwia dodawanie reguły w przyszłości może zajść potrzeba wprowadzenia w wyższej lub mniejsza niż istniejące reguły.         |
    |Name (Nazwa)     | Unikatowa nazwa dla tej reguły w ramach grupy zabezpieczeń sieci.        |  Nazwa może zawierać maksymalnie 80 znaków. Musi rozpoczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki.       |
    |Opis     | Opcjonalny opis.        |         |

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [Tworzenie reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- Program PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Wyświetlanie wszystkich reguł zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera zero lub wiele reguł. Aby dowiedzieć się więcej o informacje podane podczas wyświetlania reguły, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz sieciową grupę zabezpieczeń z listy, który chcesz wyświetlić zasady.
3. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń dla ruchu wychodzącego** w obszarze **ustawienia**.

Lista zawiera wszystkie reguły, które zostały utworzone i sieciowa grupa zabezpieczeń [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [listę reguł sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- Program PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Wyświetlanie szczegółów reguły zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci, aby wyświetlić szczegóły reguły zabezpieczeń dla.
3. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguły zabezpieczeń dla ruchu wychodzącego** w obszarze **ustawienia**.
4. Wybierz regułę, którą chcesz wyświetlić szczegóły. Aby uzyskać szczegółowy opis wszystkich ustawień, zobacz [ustawienia reguły zabezpieczeń](#security-rule-settings).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [Pokaż reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- Program PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Zmień reguły zabezpieczeń

1. Wykonaj kroki [wyświetlić szczegóły reguły zabezpieczeń](#view-details-of-a-security-rule).
2. Zmień ustawienia zgodnie z potrzebami, a następnie wybierz **Zapisz**. Aby uzyskać szczegółowy opis wszystkich ustawień, zobacz [ustawienia reguły zabezpieczeń](#security-rule-settings).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacja reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- Program PowerShell: [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Usuwanie reguły zabezpieczeń

1. Wykonaj kroki [wyświetlić szczegóły reguły zabezpieczeń](#view-details-of-a-security-rule).
2. Wybierz **Usuń**, a następnie wybierz pozycję **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [usunięcia reguły sieciowej grupy zabezpieczeń sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- Program PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Praca z grupami zabezpieczeń aplikacji

Grupy zabezpieczeń aplikacji zawiera zero lub więcej interfejsów sieciowych. Aby dowiedzieć się więcej, zobacz [grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups). Wszystkie interfejsy sieciowe grupy zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Aby dowiedzieć się, jak dodać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz [Dodawanie karty sieciowej do grupy zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Tworzenie grupy zabezpieczeń aplikacji

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. W polu **Wyszukaj w witrynie Marketplace** wpisz *Grupa zabezpieczeń aplikacji*. Gdy **Grupa zabezpieczeń aplikacji** pojawi się w wynikach wyszukiwania, wybierz ją, wybierz opcję **Grupa zabezpieczeń aplikacji** ponownie w pozycji **Wszystko**, a następnie wybierz opcję **Utwórz**.
3. Wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie        | Wartość                                                   |
    | ---            | ---                                                     |
    | Name (Nazwa)           | Nazwa musi być unikatowa w obrębie grupy zasobów.        |
    | Subskrypcja   | Wybierz subskrypcję.                               |
    | Grupa zasobów | Wybierz istniejącą grupę zasobów lub Utwórz nową. |
    | Lokalizacja       | Wybierz lokalizację                                       |

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [tworzenie asg sieci az](/cli/azure/network/asg#az-network-asg-create)
- Program PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Wyświetlanie wszystkich grup zabezpieczeń aplikacji

1. Wybierz **wszystkich usług** w górnym lewym rogu witryny Azure portal.
2. Wprowadź *grupy zabezpieczeń aplikacji* w **wszystkich usług filtru** , a następnie wybierz **grupy zabezpieczeń aplikacji** , gdy pojawia się w wynikach wyszukiwania.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [listę asg sieci az](/cli/azure/network/asg#az-network-asg-list)
- Program PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Wyświetl szczegóły grupy zabezpieczeń aplikacji

1. Wybierz **wszystkich usług** w górnym lewym rogu witryny Azure portal.
2. Wprowadź *grupy zabezpieczeń aplikacji* w **wszystkich usług filtru** , a następnie wybierz **grupy zabezpieczeń aplikacji** , gdy pojawia się w wynikach wyszukiwania.
3. Wybierz grupy zabezpieczeń aplikacji, którą chcesz wyświetlić szczegóły.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci asg show](/cli/azure/network/asg#az-network-asg-show)
- Program PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Zmienianie grupy zabezpieczeń aplikacji

1. Wybierz **wszystkich usług** w górnym lewym rogu witryny Azure portal.
2. Wprowadź *grupy zabezpieczeń aplikacji* w **wszystkich usług filtru** , a następnie wybierz **grupy zabezpieczeń aplikacji** , gdy pojawia się w wynikach wyszukiwania.
3. Wybierz grupy zabezpieczeń aplikacji, którą chcesz zmienić ustawienia. Można dodać lub usunąć tagów, lub przypisać lub usunąć uprawnienia do grupy zabezpieczeń aplikacji.

- Interfejs wiersza polecenia platformy Azure: [az sieci asg aktualizacji](/cli/azure/network/asg#az-network-asg-update)
- Program PowerShell: Nie polecenia cmdlet programu PowerShell.

### <a name="delete-an-application-security-group"></a>Usuwanie grupy zabezpieczeń aplikacji

Nie można usunąć grupy zabezpieczeń aplikacji, jeśli ma żadnych interfejsów sieci w nim. Usuń wszystkie interfejsy sieciowe z grupy zabezpieczeń aplikacji przez zmianę ustawienia interfejsu sieciowego lub usuwanie interfejsów sieciowych. Aby uzyskać więcej informacji, zobacz [dodania lub usunięcia interfejsu sieciowego z grup zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) lub [usunąć interfejsu sieciowego](virtual-network-network-interface.md#delete-a-network-interface).

1. Wybierz **wszystkich usług** w górnym lewym rogu witryny Azure portal.
2. Wprowadź *grupy zabezpieczeń aplikacji* w **wszystkich usług filtru** , a następnie wybierz **grupy zabezpieczeń aplikacji** , gdy pojawia się w wynikach wyszukiwania.
3. Wybierz grupy zabezpieczeń aplikacji, którą chcesz usunąć.
4. Wybierz **Usuń**, a następnie wybierz pozycję **tak** można usunąć grupy zabezpieczeń aplikacji.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci w grupie asg Usuń](/cli/azure/network/asg#az-network-asg-delete)
- Program PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań na sieciowych grup zabezpieczeń, regułami zabezpieczeń i grup zabezpieczeń aplikacji, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie uprawnienia wymienione w poniższych tabelach:

### <a name="network-security-group"></a>Sieciowa grupa zabezpieczeń

| Akcja                                                        |   Name (Nazwa)                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Pobierz sieciową grupę zabezpieczeń                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Utwórz lub zaktualizuj sieciową grupę zabezpieczeń                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Usuń sieciową grupę zabezpieczeń                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Skojarz sieciową grupę zabezpieczeń do podsieci lub sieci interfejsu 


### <a name="network-security-group-rule"></a>Reguły sieciowej grupy zabezpieczeń

| Akcja                                                        |   Name (Nazwa)                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Pobierz regułę                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Utwórz lub zaktualizuj regułę                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Usuń regułę                                                         |

### <a name="application-security-group"></a>Grupy zabezpieczeń aplikacji

| Akcja                                                                     | Name (Nazwa)                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Dołącz do konfiguracji adresu IP do grupy zabezpieczeń aplikacji|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Dołącz do reguły zabezpieczeń do grupy zabezpieczeń aplikacji    |
| Microsoft.Network/applicationSecurityGroups/read                           | Pobieranie grupy zabezpieczeń aplikacji                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Utwórz lub zaktualizuj grupę zabezpieczeń aplikacji           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Usuwanie grupy zabezpieczeń aplikacji                     |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie sieci lub przy użyciu grupy zabezpieczeń aplikacji [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonów usługi Resource Manager](template-samples.md)
- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych
