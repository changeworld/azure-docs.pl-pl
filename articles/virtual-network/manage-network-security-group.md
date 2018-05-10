---
title: Tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci.
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
ms.date: 04/05/2018
ms.author: jdial
ms.openlocfilehash: 7a244a5dbb86b076f99975ad477d4062699270b5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="create-change-or-delete-a-network-security-group"></a>Tworzenie, zmienianie lub usuwanie grupy zabezpieczeń sieci

Reguły zabezpieczeń sieciowych grup zabezpieczeń umożliwiają filtrowanie typ ruchu sieciowego, który może przepływać do i podsieci sieci wirtualnej i interfejsów sieciowych. Jeśli nie masz doświadczenia w obsłudze grup zabezpieczeń sieci, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md) Aby dowiedzieć się więcej o nich i ukończyć [filtrowania ruchu sieciowego](tutorial-filter-network-traffic.md) samouczkiem, aby uzyskać pewne doświadczenie w zakresie sieci grupy zabezpieczeń.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.4.1 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

Konta, zaloguj się do lub z usługą Azure z musi być przypisany do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowej roli zabezpieczeń](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania na liście [uprawnień ](#permissions).

## <a name="work-with-network-security-groups"></a>Praca z sieciowymi grupami zabezpieczeń

Można utworzyć [Wyświetl wszystkie](#view-all-network-security-groups), [szczegóły](#view-details-of-a-network-security-group), [zmienić](#change-a-network-security-group), i [usunąć](#delete-a-network-security-group) grupy zabezpieczeń sieci. Możesz również [skojarzyć lub usunąć skojarzenie](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) sieciową grupę zabezpieczeń z interfejsu sieciowego lub podsieci.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Istnieje limit liczby sieciowe grupy zabezpieczeń, które można utworzyć dla każdej lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W lewym górnym rogu portalu, wybierz **+ Utwórz zasób**.
2. Wybierz **sieci**, a następnie wybierz pozycję **sieciowej grupy zabezpieczeń**.
3. Wprowadź **nazwa** sieciowej grupy zabezpieczeń, wybierz użytkownika **subskrypcji**, Utwórz nową **grupy zasobów**, lub wybierz istniejącą grupę zasobów, wybierz pozycję **Lokalizacji**, a następnie wybierz **Utwórz**.

**Polecenia**

- Azure CLI: [utworzyć az sieci nsg](/cli/azure/network/nsg#az-network-nsg-create)
- Środowiska PowerShell: [nowe AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Wyświetlanie wszystkich grup zabezpieczeń sieci

W polu wyszukiwania w górnej części portalu wprowadź *sieciowej grupy zabezpieczeń*. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją. Grupy zabezpieczeń sieci, które istnieją w Twojej subskrypcji są wyświetlane.

**Polecenia**

- Azure CLI: [az sieci nsg listy](/cli/azure/network/nsg#az-network-nsg-list)
- Środowiska PowerShell: [Get AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Wyświetl szczegóły grupy zabezpieczeń sieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowej grupy zabezpieczeń*. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci na liście, który chcesz wyświetlić szczegóły. W obszarze **ustawienia** można wyświetlić **reguły zabezpieczeń dla ruchu przychodzącego** i **reguł zabezpieczeń dla ruchu wychodzącego**, **interfejsy sieciowe** i  **Podsieci** sieciowej grupy zabezpieczeń jest skojarzony. Można również włączyć lub wyłączyć **dzienniki diagnostyczne** i widoku **reguły efektywnym elementem systemu zabezpieczeń**. Aby dowiedzieć się więcej, zobacz [dzienniki diagnostyczne](virtual-network-nsg-manage-log.md) i [wyświetlić reguły efektywnym elementem systemu zabezpieczeń](virtual-network-nsg-troubleshoot-portal.md).
3. Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure na liście, zobacz następujące artykuły:
    *   [Dziennik aktywności](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
    *   [Kontrola dostępu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
    *   [Tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Blokady](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Skrypt automatyzacji](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Polecenia**

- Azure CLI: [Pokaż nsg sieci az](/cli/azure/network/nsg#az-network-nsg-show)
- Środowiska PowerShell: [Get AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Zmienianie grupy zabezpieczeń sieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowej grupy zabezpieczeń* w polu wyszukiwania. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci, który chcesz zmienić. Najbardziej typowe zmiany są [Dodawanie](#create-a-security-rule) lub [usuwanie](#delete-a-security-rule) reguły zabezpieczeń i [Associating lub usunięciu grupy zabezpieczeń sieci do lub z interfejsem podsieci lub sieci](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Polecenia**

- Azure CLI: [az sieci nsg aktualizacji](/cli/azure/network/nsg#az-network-nsg-update)
- Środowiska PowerShell: [AzureRmNetworkSecurityGroup zestawu](/powershell/module/azurerm.network/set-azurermnetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Skojarz lub usunąć skojarzenie z interfejsem podsieci lub sieci lub grupy zabezpieczeń sieci

Aby skojarzyć grupy zabezpieczeń sieci do lub usunąć skojarzenie z karty sieciowej grupy zabezpieczeń sieci, zobacz [sieciową grupę zabezpieczeń do skojarzenia lub Usuń skojarzenie grupy zabezpieczeń sieci z interfejsem sieciowym](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Aby skojarzyć grupy zabezpieczeń sieci do lub usunąć skojarzenie grupy zabezpieczeń sieci z podsieci, zobacz [zmienić ustawienia podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Usuń sieciową grupę zabezpieczeń

Jeśli grupa zabezpieczeń sieci jest skojarzony z żadnych podsieci lub do interfejsów sieciowych, nie można usunąć. [Usuń skojarzenie](#associate-or-dissociate-a-network-security-group-to-or-from-a-resource) sieciowej grupy zabezpieczeń ze wszystkich podsieci i interfejsów sieciowych przed próbą usunięcia go.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowej grupy zabezpieczeń* w polu wyszukiwania. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci, które chcesz usunąć z listy.
3. Wybierz **usunąć**, a następnie wybierz **tak**.

**Polecenia**

- Azure CLI: [usunąć az sieci nsg](/cli/azure/network/nsg#az-network-nsg-delete)
- Środowiska PowerShell: [AzureRmNetworkSecurityGroup Usuń](/powershell/module/azurerm.network/remove-azurermnetworksecuritygroupp) 

## <a name="work-with-security-rules"></a>Praca z zasadami zabezpieczeń

Grupa zabezpieczeń sieci zawiera zero lub więcej reguł zabezpieczeń. Można utworzyć [Wyświetl wszystkie](#view-all-security-rules), [szczegóły](#view-details-of-a-security-rule), [zmienić](#change-a-security-rule), i [usunąć](#delete-a-security-rule) regułę zabezpieczeń.

### <a name="create-a-security-rule"></a>Utwórz regułę zabezpieczeń

Istnieje limit liczby reguł na grupę zabezpieczeń sieci można utworzyć dla każdej lokalizacji platformy Azure i subskrypcji. Aby uzyskać więcej informacji, zobacz [Azure limits (Ograniczenia platformy Azure)](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowej grupy zabezpieczeń* w polu wyszukiwania. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci z listy, który chcesz dodać regułę zabezpieczeń.
3. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** w obszarze **ustawienia**. Kilka istniejących reguł są wyświetlane. Niektóre reguły, które nie zostały dodane. Po utworzeniu grupy zabezpieczeń sieci kilka domyślnych reguł zabezpieczeń są tworzone w nim. Aby dowiedzieć się więcej, zobacz [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules).  Nie można usunąć domyślnej reguły zabezpieczeń, ale można je zastąpić przy użyciu reguł, które mają wyższy priorytet.
4. <a name = "security-rule-settings"></a>Wybierz **+ Dodaj**.  Wybierz lub Dodaj wartości dla następujących ustawień, a następnie wybierz **OK**:
    
    |Ustawienie  |Wartość  |Szczegóły  |
    |---------|---------|---------|
    |Element źródłowy     | Wybierz **żadnych**, **adresów IP**, lub **usługi tagu**.        | W przypadku wybrania **adresów IP**, wówczas musisz określić **źródłowy adres IP, zakresy adresów/CIDR**. Można określić pojedynczą wartością lub rozdzielaną przecinkami listę wielu wartości. Przykład wielu wartości to 10.0.0.0/16, 192.188.1.1. Brak ograniczeń dotyczących liczbę wartości, które można określić. Zobacz [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) szczegółowe informacje. W przypadku wybrania **numer seryjny**, następnie należy wybrać jeden numer seryjny. Numer seryjny usługi jest identyfikatorem wstępnie zdefiniowanych kategorii adresów IP. Aby dowiedzieć się więcej na temat znaczników dostępność usług i reprezentuje tagami, zobacz [usługi tagów](security-overview.md#service-tags)        |
    |Zakresy portów źródłowych     | Określ pojedynczym portem, np. 80, zakres portów, np. 1024 – 65535 lub rozdzielaną przecinkami listę pojedynczego portów i/lub zakresów portów, np. 80, 1024 – 65 535. Wprowadź znak gwiazdki, aby zezwolić na ruch na dowolnym porcie. | Porty i zakresy należy określić, jaki ruch portów jest dozwolony lub odrzucany przez tę zasadę. Brak ograniczeń dotyczących liczbę portów, które można określić. Zobacz [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) szczegółowe informacje.  |
    |Element docelowy     | Wybierz **żadnych**, **adresów IP**, lub **sieci wirtualnej**.        | W przypadku wybrania **adresów IP**, wówczas musisz określić **docelowy adres IP, zakresy adresów/CIDR**. Podobnie jak **źródła** i **źródłowy adres IP, zakresy adresów/CIDR**, można określić jeden lub wiele adresy lub zakresy i istnieją ograniczenia do liczby można określić. Wybieranie **sieci wirtualnej**oznacza, że ruch jest dozwolony dla wszystkich adresów IP w przestrzeni adresowej sieci wirtualnej, która jest numer seryjny usługi.        |
    |Zakresy portów docelowych     | Określ pojedynczą wartość lub listę wartości oddzielanych przecinkami. | Podobnie jak **źródła zakresy portów**, można określić jeden lub wiele porty i zakresy i istnieją ograniczenia do liczby można określić. |
    |Protokół     | Wybierz **żadnych**, **TCP**, lub **UDP**.        |         |
    |Akcja     | Wybierz **Zezwalaj** lub **odmowy**.        |         |
    |Priorytet     | Wprowadź wartość od 100-4096 unikatowa dla wszystkich reguł zabezpieczeń grupy zabezpieczeń sieci. |Reguły są przetwarzane w kolejności priorytetu. Im niższy numer, tym wyższy priorytet. Zaleca się pozostawienie odstęp między liczbami priorytet podczas tworzenia reguł, takich jak 100, 200, 300. Pozostawienie przerw ułatwia ułatwia dodawanie w przyszłości reguł, które może być konieczne dokonanie wyższe lub niższe od istniejących reguł.         |
    |Name (Nazwa)     | Unikatowa nazwa reguły w ramach grupy zabezpieczeń sieci.        |  Nazwa może zawierać maksymalnie 80 znaków. Musi zaczynać się literą lub cyfrą, kończyć się literą, cyfrą lub podkreślenia i może zawierać tylko litery, cyfry, podkreślenia, kropki i łączniki.       |
    |Opis     | Opcjonalny opis.        |         |

    Nie można określić [grupy zabezpieczeń aplikacji](#work-with-application-security-groups) dla **źródła** lub **docelowego** ustawień za pomocą portalu. Można jednak przy użyciu wiersza polecenia platformy Azure lub programu PowerShell. Ustawienia dla **reguł zabezpieczeń dla ruchu wychodzącego** są podobne, więc nie obejmują one oddzielnie.

**Polecenia**

- Azure CLI: [Utwórz reguły nsg sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- Środowiska PowerShell: [nowe AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Wyświetl wszystkie reguły zabezpieczeń

Grupa zabezpieczeń sieci zawiera zero lub wiele reguł. Aby dowiedzieć się więcej o informacje podane podczas wyświetlania reguły, zobacz [omówienie grupy zabezpieczeń sieci](security-overview.md).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowej grupy zabezpieczeń*. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci na liście mają dotyczyć zasady.
3. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguł zabezpieczeń dla ruchu wychodzącego** w obszarze **ustawienia**.

Lista zawiera wszystkie reguły zostały utworzone i grupy zabezpieczeń sieci [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules).

**Polecenia**

- Azure CLI: [listy reguł nsg sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- Środowiska PowerShell: [Get AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Wyświetlanie szczegółów reguły zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź *sieciowej grupy zabezpieczeń*. Gdy **sieciowej grupy zabezpieczeń** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz grupy zabezpieczeń sieci, aby wyświetlić szczegóły reguły zabezpieczeń dla.
3. Wybierz **reguły zabezpieczeń dla ruchu przychodzącego** lub **reguł zabezpieczeń dla ruchu wychodzącego** w obszarze **ustawienia**.
4. Wybierz regułę, którą chcesz wyświetlić szczegóły. Aby uzyskać szczegółowy opis wszystkich ustawień, zobacz [ustawienia reguły zabezpieczeń](#security-rule-settings).

**Polecenia**

- Azure CLI: [Pokaż reguły nsg sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- Środowiska PowerShell: [Get AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/get-azurermnetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Zmień reguły zabezpieczeń

1. Wykonaj kroki [wyświetlić szczegóły reguły zabezpieczeń](#view-details-of-a-security-rule).
2. Zmień odpowiednie ustawienia, a następnie wybierz **zapisać**. Aby uzyskać szczegółowy opis wszystkich ustawień, zobacz [ustawienia reguły zabezpieczeń](#security-rule-settings).

**Polecenia**

- Azure CLI: [zaktualizować reguły nsg sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- Środowiska PowerShell: [AzureRmSecurityRuleConfig zestawu](/powershell/module/azurerm.network/set-azurermnetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Usuń regułę zabezpieczeń

1. Wykonaj kroki [wyświetlić szczegóły reguły zabezpieczeń](#view-details-of-a-security-rule).
2. Wybierz **usunąć**, a następnie wybierz **tak**.

**Polecenia**

- Azure CLI: [usunięcia reguły nsg sieci az](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- Środowiska PowerShell: [AzureRmSecurityRuleConfig Usuń](/powershell/module/azurerm.network/remove-azurermnetworksecurityruleconfig)


## <a name="work-with-application-security-groups"></a>Praca z grupami zabezpieczeń aplikacji

Grupa zabezpieczeń aplikacji zawiera zero lub więcej interfejsów sieciowych. Aby dowiedzieć się więcej, zobacz [grup zabezpieczeń aplikacji](security-overview.md#application-security-groups). Nie można użyć grup zabezpieczeń aplikacji w portalu, ale możesz użyć programu PowerShell lub wiersza polecenia platformy Azure. Wszystkie interfejsy sieciowe grupy zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Pierwszy interfejs sieciowy dodane do grupy zabezpieczeń aplikacji określa sieci wirtualnej, który musi należeć do wszystkich interfejsów sieciowych kolejne. Aby dowiedzieć się, jak dodać interfejsu sieciowego do grupy zabezpieczeń aplikacji, zobacz [Dodaj interfejs sieciowy do grupy zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Tworzenie grupy zabezpieczeń aplikacji

- Azure CLI: [utworzyć az asg sieci](/cli/azure/network/asg#az-network-asg-create)
- Środowiska PowerShell: [nowe AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Wyświetlanie wszystkich grup zabezpieczeń aplikacji

- Azure CLI: [az sieci asg listy](/cli/azure/network/asg#az-network-asg-list)
- Środowiska PowerShell: [Get AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Wyświetl szczegóły grupy zabezpieczeń określonej aplikacji

- Azure CLI: [az sieci asg Pokaż](/cli/azure/network/asg#az-network-asg-show)
- Środowiska PowerShell: [Get AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/get-azurermapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Zmienianie grupy zabezpieczeń aplikacji

Podczas niektórych ustawień, takich jak tagi i uprawnienia do istniejącej grupy zabezpieczeń aplikacji można zmienić, nie można zmienić jego nazwę lub lokalizację.

- Azure CLI: [az sieci asg aktualizacji](/cli/azure/network/asg#az-network-asg-update)
- : Nie PowerShell polecenia cmdlet programu PowerShell.

### <a name="delete-an-application-security-group"></a>Usuwanie grupy zabezpieczeń aplikacji

Nie można usunąć grupy zabezpieczeń aplikacji, jeśli ma ona żadnych interfejsów sieciowych w nim. Usuń wszystkie interfejsy sieciowe z grupy zabezpieczeń aplikacji przez zmianę ustawienia interfejsu sieciowego lub usuwanie interfejsów sieciowych. Aby uzyskać więcej informacji, zobacz [dodania lub usunięcia interfejsu sieciowego z grup zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) lub [usunąć interfejsu sieciowego](virtual-network-network-interface.md#delete-a-network-interface).

**Polecenia**

- Azure CLI: [usunąć az asg sieci](/cli/azure/network/asg#az-network-asg-delete)
- Środowiska PowerShell: [AzureRmApplicationSecurityGroup Usuń](/powershell/module/azurerm.network/remove-azurermapplicationsecuritygroup)

## <a name="permissions"></a>Uprawnienia

Do wykonania zadań dotyczących sieciowych grup zabezpieczeń, zasady zabezpieczeń i grup zabezpieczeń aplikacji, Twoje konto musi mieć przypisaną do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowej roli zabezpieczeń](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie uprawnienia są wymienione w poniższych tabelach:

### <a name="network-security-groups"></a>Grupy zabezpieczeń sieci

| Akcja                                                        |   Name (Nazwa)                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/ruleTables/read                             |   Pobierz sieciową grupę zabezpieczeń                                          |
| Microsoft.Network/ruleTables/write                            |   Utwórz lub zaktualizuj sieciową grupę zabezpieczeń                             |
| Microsoft.Network/ruleTables/delete                           |   Usuń sieciową grupę zabezpieczeń                                       |
| Microsoft.Network/ruleTables/join/action                      |   Skojarzenia sieciowej grupy zabezpieczeń do podsieci lub sieci interfejsu |
| Microsoft.Network/ruleTables/rules/read                       |   Pobierz regułę                                                            |
| Microsoft.Network/ruleTables/rules/write                      |   Utwórz lub zaktualizuj regułę                                               |
| Microsoft.Network/ruleTables/rules/delete                     |   Usuń regułę                                                         |
| Microsoft.Network/networkInterfaces/effectiveruleTable/action |   Pobierz sieciową grupę zabezpieczeń skuteczne interfejsu sieciowego              |
| Microsoft.Network/networkWatchers/nextHop/action              |   Pobiera następnego przeskoku z maszyny Wirtualnej                                         |

### <a name="application-security-groups"></a>Grupy zabezpieczeń aplikacji

| Akcja                                                                     | Name (Nazwa)                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Dołącz konfiguracji IP do grupy zabezpieczeń aplikacji|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Dołącz regułę zabezpieczeń do grupy zabezpieczeń aplikacji    |
| Microsoft.Network/applicationSecurityGroups/read                           | Pobierz grupy zabezpieczeń aplikacji                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Utwórz lub zaktualizuj grupy zabezpieczeń aplikacji           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Usuwanie grupy zabezpieczeń aplikacji                     |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie sieci lub za pomocą grupy zabezpieczeń aplikacji [PowerShell](powershell-samples.md) lub [interfejsu wiersza polecenia Azure](cli-samples.md) przykładowe skrypty lub przy użyciu usługi Azure [szablonów Resource Manager](template-samples.md)
- Tworzenie i stosowanie [Azure zasad](policy-samples.md) dla sieci wirtualnych