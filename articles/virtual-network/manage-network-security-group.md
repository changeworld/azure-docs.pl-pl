---
title: Tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć, zmienić lub usunąć sieciową grupę zabezpieczeń.
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
ms.openlocfilehash: fa933b820d8677e4d080b54ce5e6a5d506ea38fc
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978925"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń

Reguły zabezpieczeń w sieciowych grupach zabezpieczeń umożliwiają filtrowanie typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej i interfejsów sieciowych. Jeśli nie znasz sieciowych grup zabezpieczeń, zobacz [Omówienie sieciowej grupy zabezpieczeń](security-overview.md) , aby dowiedzieć się więcej o nich i zakończyć samouczek [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md) w celu uzyskania pewnych doświadczeń z sieciowymi grupami zabezpieczeń.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz również uruchomić `az login`, aby utworzyć połączenie z platformą Azure.

Konto, do którego chcesz się zalogować, lub Połącz się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="work-with-network-security-groups"></a>Praca z sieciowymi grupami zabezpieczeń

Można tworzyć, [wyświetlać wszystkie](#view-all-network-security-groups), [wyświetlać szczegóły](#view-details-of-a-network-security-group), [zmieniać](#change-a-network-security-group)i [usuwać](#delete-a-network-security-group) sieciową grupę zabezpieczeń. Można także [skojarzyć lub usunąć skojarzenie](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) sieciowej grupy zabezpieczeń z interfejsu sieciowego lub podsieci.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Istnieje ograniczenie dotyczące liczby grup zabezpieczeń sieci, które można utworzyć na potrzeby lokalizacji i subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **sieciowa Grupa zabezpieczeń**.
3. Wprowadź **nazwę** sieciowej grupy zabezpieczeń, wybierz **subskrypcję**, Utwórz nową **grupę zasobów**lub wybierz istniejącą grupę zasobów, wybierz **lokalizację**, a następnie wybierz pozycję **Utwórz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Wyświetl wszystkie sieciowe grupy zabezpieczeń

W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je. Zostaną wyświetlone grupy zabezpieczeń sieci, które istnieją w ramach subskrypcji.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Wyświetlanie szczegółów sieciowej grupy zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy grupę zabezpieczeń sieci, dla której chcesz wyświetlić szczegóły. W obszarze **Ustawienia** można wyświetlić **reguły zabezpieczeń ruchu przychodzącego** i **reguły zabezpieczeń ruchu wychodzącego**, do **interfejsów sieciowych** i **podsieci** , z którymi jest skojarzona Grupa zabezpieczeń sieci. Możesz również włączyć lub wyłączyć **dzienniki diagnostyczne** i wyświetlić **obowiązujące reguły zabezpieczeń**. Aby dowiedzieć się więcej, zobacz [dzienniki diagnostyczne](virtual-network-nsg-manage-log.md) i [Wyświetlanie obowiązujących reguł zabezpieczeń](diagnose-network-traffic-filter-problem.md).
3. Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące artykuły:
    *   [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md)
    *   [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
    *   [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Zamki](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skrypt automatyzacji](../azure-resource-manager/templates/export-template-portal.md)

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Zmiana sieciowej grupy zabezpieczeń

1. W polu wyszukiwania w górnej części portalu w polu wyszukiwania wprowadź *sieciowe grupy zabezpieczeń* . Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz grupę zabezpieczeń sieci, którą chcesz zmienić. Najbardziej typowe zmiany to [Dodawanie](#create-a-security-rule) lub [usuwanie](#delete-a-security-rule) reguł zabezpieczeń oraz [kojarzenie lub skojarzenie grupy zabezpieczeń sieci z lub z podsiecią lub interfejsem sieciowym](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Kojarzenie lub usuwanie skojarzenia sieciowej grupy zabezpieczeń z lub z podsiecią lub interfejsem sieciowym

Aby skojarzyć sieciową grupę zabezpieczeń z interfejsem sieciowym lub usunąć skojarzenie sieciowej grupy zabezpieczeń, zobacz [kojarzenie sieciowej grupy zabezpieczeń lub usuń skojarzenie sieciowej grupy zabezpieczeń z interfejsu sieciowego](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Aby skojarzyć grupę zabezpieczeń sieci z lub usunąć skojarzenie sieciowej grupy zabezpieczeń z podsiecią, zobacz [Zmiana ustawień podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Usuń sieciową grupę zabezpieczeń

Jeśli grupa zabezpieczeń sieci jest skojarzona z dowolnymi podsieciami lub interfejsami sieciowymi, nie można jej usunąć. Przed podjęciem próby usunięcia Usuń skojarzenie sieciowej grupy zabezpieczeń ze wszystkimi podsieciami i interfejsami sieciowymi.

1. W polu wyszukiwania w górnej części portalu w polu wyszukiwania wprowadź *sieciowe grupy zabezpieczeń* . Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy grupę zabezpieczeń sieci, która ma zostać usunięta.
3. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **Tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Korzystanie z reguł zabezpieczeń

Sieciowa Grupa zabezpieczeń zawiera co najmniej zero reguł zabezpieczeń. Możesz tworzyć, [wyświetlać wszystkie](#view-all-security-rules), [wyświetlać szczegóły](#view-details-of-a-security-rule), [zmieniać](#change-a-security-rule)i [usuwać](#delete-a-security-rule) regułę zabezpieczeń.

### <a name="create-a-security-rule"></a>Tworzenie reguły zabezpieczeń

Istnieje limit liczby reguł dla każdej sieciowej grupy zabezpieczeń, które można utworzyć na potrzeby lokalizacji i subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W polu wyszukiwania w górnej części portalu w polu wyszukiwania wprowadź *sieciowe grupy zabezpieczeń* . Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy grupę zabezpieczeń sieci, do której chcesz dodać regułę zabezpieczeń.
3. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu przychodzącego** . Zostanie wyświetlona kilka istniejących reguł. Niektóre z reguł, które mogły nie zostać dodane. Po utworzeniu sieciowej grupy zabezpieczeń są w niej tworzone kilka domyślnych reguł zabezpieczeń. Aby dowiedzieć się więcej, zobacz [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules).  Nie można usunąć domyślnych reguł zabezpieczeń, ale można je zastąpić przy użyciu reguł mających wyższy priorytet.
4. <a name = "security-rule-settings"></a>Wybierz pozycję **+ Dodaj**.  Wybierz lub Dodaj wartości dla następujących ustawień, a następnie wybierz przycisk **OK**:
    
    |Ustawienie  |Wartość  |Szczegóły  |
    |---------|---------|---------|
    |Źródło     | Wybierz **dowolną**, **grupę zabezpieczeń aplikacji**, **adresy IP**lub **tag usługi** dla reguł zabezpieczeń dla ruchu przychodzącego. W przypadku tworzenia reguły zabezpieczeń dla ruchu wychodzącego opcje są takie same jak opcje wymienione dla **miejsca docelowego**.       | W przypadku wybrania **grupy zabezpieczeń aplikacji**wybierz co najmniej jedną istniejącą grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group). W przypadku wybrania **grupy zabezpieczeń aplikacji** zarówno dla **źródła** , jak i **docelowego**, interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą znajdować się w tej samej sieci wirtualnej. W przypadku wybrania **adresów IP**Określ **źródłowe adresy IP/zakresy CIDR**. Można określić pojedynczą wartość lub rozdzieloną przecinkami listę wielu wartości. Przykładem wielu wartości jest 10.0.0.0/16, 192.188.1.1. Istnieją limity liczby wartości, które można określić. Aby uzyskać szczegółowe informacje, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) . Jeśli wybierzesz **tag usługi**, a następnie wybierz jeden tag usługi. Tag usługi jest wstępnie zdefiniowanym identyfikatorem dla kategorii adresów IP. Aby dowiedzieć się więcej na temat dostępnych tagów usługi i informacje o tym, co reprezentuje każdy tag, zobacz [Tagi usług](security-overview.md#service-tags). Jeśli określony adres IP jest przypisany do maszyny wirtualnej platformy Azure, upewnij się, że został określony prywatny adres IP, a nie publicznego adresu IP przypisanego do maszyny wirtualnej. Reguły zabezpieczeń są przetwarzane, gdy platforma Azure przekształci publiczny adres IP na prywatny adres IP dla reguł zabezpieczeń dla ruchu przychodzącego, a zanim platforma Azure przetłumaczy prywatny adres IP na publiczny adres IP dla reguł wychodzących. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, zobacz [typy adresów IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Zakresy portów źródłowych     | Określ pojedynczy port, taki jak 80, zakres portów, taki jak 1024-65535, lub rozdzielana przecinkami lista pojedynczych portów i/lub zakresów portów, takich jak 80, 1024-65535. Wprowadź gwiazdkę, aby zezwolić na ruch na dowolnym porcie. | Porty i zakresy określają, które porty są dozwolone lub odrzucane przez tę regułę. Istnieją limity liczby portów, które można określić. Aby uzyskać szczegółowe informacje, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .  |
    |Cel     | Wybierz **dowolną**, **grupę zabezpieczeń aplikacji**, **adresy IP**lub **Virtual Network** dla reguł zabezpieczeń dla ruchu wychodzącego. W przypadku tworzenia reguły zabezpieczeń dla ruchu przychodzącego opcje są takie same jak opcje na liście **Źródło**.        | W przypadku wybrania **grupy zabezpieczeń aplikacji** należy wybrać co najmniej jedną istniejącą grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group). W przypadku wybrania **grupy zabezpieczeń aplikacji**wybierz jedną istniejącą grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. W przypadku wybrania opcji **adresy IP**Określ **docelowe adresy IP/zakresy CIDR**. Podobnie jak **źródłowe** i **źródłowe adresy IP/zakresy CIDR**, można określić pojedynczy lub wiele adresów lub zakresów, a liczba jest ograniczona do liczby, którą można określić. Wybranie opcji **Sieć wirtualna**, która jest tagiem usługi, oznacza, że ruch jest dozwolony dla wszystkich adresów IP w przestrzeni adresowej sieci wirtualnej. Jeśli określony adres IP jest przypisany do maszyny wirtualnej platformy Azure, upewnij się, że został określony prywatny adres IP, a nie publicznego adresu IP przypisanego do maszyny wirtualnej. Reguły zabezpieczeń są przetwarzane, gdy platforma Azure przekształci publiczny adres IP na prywatny adres IP dla reguł zabezpieczeń dla ruchu przychodzącego, a zanim platforma Azure przetłumaczy prywatny adres IP na publiczny adres IP dla reguł wychodzących. Aby dowiedzieć się więcej na temat publicznych i prywatnych adresów IP na platformie Azure, zobacz [typy adresów IP](virtual-network-ip-addresses-overview-arm.md).        |
    |Zakresy portów docelowych     | Określ pojedynczą wartość lub listę wartości rozdzielonych przecinkami. | Podobnie jak w przypadku **zakresów portów źródłowych**, można określić jeden lub wiele portów i zakresów, a liczba jest ograniczona do liczby, którą można określić. |
    |Protocol (Protokół)     | Wybierz **dowolny**, **TCP**, **UDP** lub **ICMP**.        |         |
    |Działanie     | Wybierz opcję **Zezwalaj** lub **Odmów**.        |         |
    |Priorytet     | Wprowadź wartość z zakresu od 100-4096, która jest unikatowa dla wszystkich reguł zabezpieczeń w sieciowej grupie zabezpieczeń. |Reguły są przetwarzane w kolejności priorytetów. Im niższa wartość, tym wyższy priorytet. Zaleca się pozostawienie przerwy między numerami priorytetu podczas tworzenia reguł, takich jak 100, 200, 300. Pozostawienie przerw w ułatwia dodawanie reguł w przyszłości, które mogą być konieczne do wprowadzenia wyższych lub niższych niż istniejące reguły.         |
    |Nazwa     | Unikatowa nazwa reguły w sieciowej grupie zabezpieczeń.        |  Nazwa może składać się z maksymalnie 80 znaków. Musi zaczynać się literą lub cyfrą, kończyć literą, cyfrą lub znakiem podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki.       |
    |Opis     | Opcjonalny opis.        |         |

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Wyświetl wszystkie reguły zabezpieczeń

Sieciowa Grupa zabezpieczeń zawiera zero lub wiele reguł. Aby dowiedzieć się więcej na temat informacji wyświetlanych podczas wyświetlania reguł, zobacz [Omówienie grup zabezpieczeń sieci](security-overview.md).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz z listy grupę zabezpieczeń sieci, dla której chcesz wyświetlić reguły.
3. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń ruchu przychodzącego** lub **reguły zabezpieczeń ruchu wychodzącego** .

Lista zawiera wszystkie utworzone reguły oraz [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules)sieciowej grupy zabezpieczeń.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Wyświetlanie szczegółów reguły zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowe grupy zabezpieczeń*. Gdy **sieciowe grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz je.
2. Wybierz sieciową grupę zabezpieczeń, dla której chcesz wyświetlić szczegóły reguły zabezpieczeń.
3. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń ruchu przychodzącego** lub **reguły zabezpieczeń ruchu wychodzącego** .
4. Wybierz regułę, dla której chcesz wyświetlić szczegóły. Aby uzyskać szczegółowy opis wszystkich ustawień, zobacz [Ustawienia reguł zabezpieczeń](#security-rule-settings).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Zmiana reguły zabezpieczeń

1. Wykonaj kroki opisane w temacie [Wyświetlanie szczegółów reguły zabezpieczeń](#view-details-of-a-security-rule).
2. Zmień ustawienia zgodnie z potrzebami, a następnie wybierz pozycję **Zapisz**. Aby uzyskać szczegółowy opis wszystkich ustawień, zobacz [Ustawienia reguł zabezpieczeń](#security-rule-settings).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Rule Update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Usuwanie reguły zabezpieczeń

1. Wykonaj kroki opisane w temacie [Wyświetlanie szczegółów reguły zabezpieczeń](#view-details-of-a-security-rule).
2. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **Tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network sieciowej grupy zabezpieczeń Rule Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Praca z grupami zabezpieczeń aplikacji

Grupa zabezpieczeń aplikacji zawiera co najmniej jeden interfejs sieciowy. Aby dowiedzieć się więcej, zobacz [grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups). Wszystkie interfejsy sieciowe w grupie zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Aby dowiedzieć się, jak dodać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz [Dodawanie interfejsu sieciowego do grupy zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Tworzenie grupy zabezpieczeń aplikacji

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. W polu **Wyszukaj w witrynie Marketplace** wpisz *Grupa zabezpieczeń aplikacji*. Gdy **Grupa zabezpieczeń aplikacji** pojawi się w wynikach wyszukiwania, wybierz ją, wybierz opcję **Grupa zabezpieczeń aplikacji** ponownie w pozycji **Wszystko**, a następnie wybierz opcję **Utwórz**.
3. Wprowadź lub wybierz następujące informacje, a następnie wybierz pozycję **Utwórz**:

    | Ustawienie        | Wartość                                                   |
    | ---            | ---                                                     |
    | Nazwa           | Nazwa musi być unikatowa w obrębie grupy zasobów.        |
    | Subskrypcja   | Wybierz subskrypcję.                               |
    | Grupa zasobów | Wybierz istniejącą grupę zasobów lub Utwórz nową. |
    | Lokalizacja       | Wybieranie lokalizacji                                       |

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Wyświetl wszystkie grupy zabezpieczeń aplikacji

1. W lewym górnym rogu Azure Portal zaznacz pozycję **wszystkie usługi** .
2. W polu **Filtr wszystkie usługi** wprowadź *grupy zabezpieczeń aplikacji* , a następnie wybierz pozycję **grupy zabezpieczeń aplikacji** , gdy zostanie ona wyświetlona w wynikach wyszukiwania.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network ASG list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Wyświetl szczegóły określonej grupy zabezpieczeń aplikacji

1. W lewym górnym rogu Azure Portal zaznacz pozycję **wszystkie usługi** .
2. W polu **Filtr wszystkie usługi** wprowadź *grupy zabezpieczeń aplikacji* , a następnie wybierz pozycję **grupy zabezpieczeń aplikacji** , gdy zostanie ona wyświetlona w wynikach wyszukiwania.
3. Wybierz grupę zabezpieczeń aplikacji, dla której chcesz wyświetlić szczegóły.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Zmień grupę zabezpieczeń aplikacji

1. W lewym górnym rogu Azure Portal zaznacz pozycję **wszystkie usługi** .
2. W polu **Filtr wszystkie usługi** wprowadź *grupy zabezpieczeń aplikacji* , a następnie wybierz pozycję **grupy zabezpieczeń aplikacji** , gdy zostanie ona wyświetlona w wynikach wyszukiwania.
3. Wybierz grupę zabezpieczeń aplikacji, dla której chcesz zmienić ustawienia. Możesz dodawać lub usuwać Tagi albo przypisywać lub usuwać uprawnienia do grupy zabezpieczeń aplikacji.

- Interfejs wiersza polecenia platformy Azure: [AZ Network ASG Update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Brak polecenia cmdlet programu PowerShell.

### <a name="delete-an-application-security-group"></a>Usuwanie grupy zabezpieczeń aplikacji

Nie można usunąć grupy zabezpieczeń aplikacji, jeśli zawiera ona interfejsy sieciowe. Usuń wszystkie interfejsy sieciowe z grupy zabezpieczeń aplikacji, zmieniając ustawienia interfejsu sieciowego lub usuwając interfejsy sieciowe. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie do lub usuwanie interfejsu sieciowego z grup zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) lub [Usuwanie interfejsu sieciowego](virtual-network-network-interface.md#delete-a-network-interface).

1. W lewym górnym rogu Azure Portal zaznacz pozycję **wszystkie usługi** .
2. W polu **Filtr wszystkie usługi** wprowadź *grupy zabezpieczeń aplikacji* , a następnie wybierz pozycję **grupy zabezpieczeń aplikacji** , gdy zostanie ona wyświetlona w wynikach wyszukiwania.
3. Wybierz grupę zabezpieczeń aplikacji, którą chcesz usunąć.
4. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak** , aby usunąć grupę zabezpieczeń aplikacji.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania dotyczące sieciowych grup zabezpieczeń, reguł zabezpieczeń i grup zabezpieczeń aplikacji, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie uprawnienia wymienione w poniższych tabelach:

### <a name="network-security-group"></a>Sieciowa grupa zabezpieczeń

| Działanie                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Pobierz sieciową grupę zabezpieczeń                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Utwórz lub Zaktualizuj grupę zabezpieczeń sieci                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Usuń sieciową grupę zabezpieczeń                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Kojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym 


### <a name="network-security-group-rule"></a>Reguła sieciowej grupy zabezpieczeń

| Działanie                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Pobierz regułę                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Utwórz lub zaktualizuj regułę                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Usuń regułę                                                         |

### <a name="application-security-group"></a>Grupa zabezpieczeń aplikacji

| Działanie                                                                     | Nazwa                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Przyłączanie konfiguracji adresu IP do grupy zabezpieczeń aplikacji|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Przyłączanie reguły zabezpieczeń do grupy zabezpieczeń aplikacji    |
| Microsoft.Network/applicationSecurityGroups/read                           | Pobierz grupę zabezpieczeń aplikacji                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Utwórz lub Zaktualizuj grupę zabezpieczeń aplikacji           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Usuwanie grupy zabezpieczeń aplikacji                     |

## <a name="next-steps"></a>Następne kroki

- Tworzenie grupy zabezpieczeń sieci lub aplikacji przy użyciu [programu PowerShell](powershell-samples.md) lub przykładowych skryptów [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub korzystanie z szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
