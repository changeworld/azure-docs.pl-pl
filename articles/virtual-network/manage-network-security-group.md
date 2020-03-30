---
title: Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń platformy Azure
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
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: a22adef5510e24c2dc07ffb39c9687d500644f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066435"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Tworzenie, zmienianie i usuwanie sieciowej grupy zabezpieczeń

Reguły zabezpieczeń w sieciowych grupach zabezpieczeń umożliwiają filtrowanie typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej i interfejsów sieciowych. Aby dowiedzieć się więcej o sieciowych grupach zabezpieczeń, zobacz [Omówienie sieciowej grupy zabezpieczeń](security-overview.md). Następnie wykonaj samouczek [Filtrowanie ruchu sieciowego,](tutorial-filter-network-traffic.md) aby uzyskać pewne doświadczenie z grupami zabezpieczeń sieci.

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Jeśli go nie masz, skonfiguruj konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Wykonaj jedno z następujących zadań przed rozpoczęciem pozostałej części tego artykułu:

- **Użytkownicy portalu:** Zaloguj się do [witryny Azure portal](https://portal.azure.com) za pomocą konta platformy Azure.

- **Użytkownicy programu PowerShell:** Uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchom program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Na karcie Przeglądarka usługi Azure Cloud Shell znajdź listę rozwijana **Wybierz środowisko,** a następnie wybierz program **PowerShell,** jeśli nie jest jeszcze zaznaczona.

    Jeśli używasz programu PowerShell lokalnie, użyj modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az.Network`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Uruchom polecenie `Connect-AzAccount`, aby utworzyć połączenia z platformą Azure.

- **Użytkownicy interfejsu wiersza polecenia platformy Azure (CLI):** uruchom polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchom interfejs wiersza polecenia z komputera. Użyj interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej, jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Uruchom polecenie `az login`, aby utworzyć połączenia z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do [roli współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

## <a name="work-with-network-security-groups"></a>Praca z sieciowymi grupami zabezpieczeń

Można tworzyć, [wyświetlać wszystkie](#view-all-network-security-groups), [wyświetlać szczegóły](#view-details-of-a-network-security-group), [zmieniać](#change-a-network-security-group)i [usuwać](#delete-a-network-security-group) sieciową grupę zabezpieczeń. Można również [skojarzyć lub odłączyć](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) sieciową grupę zabezpieczeń od interfejsu sieciowego lub podsieci.

### <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

Istnieje limit liczby grup zabezpieczeń sieciowych, które można utworzyć dla każdej lokalizacji i subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. W menu [portalu azure](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

2. Wybierz **pozycję Sieć**, a następnie wybierz pozycję Grupa zabezpieczeń **sieć**.

3. Na stronie **Tworzenie sieciowej grupy zabezpieczeń** na karcie **Podstawy** ustaw wartości dla następujących ustawień:

    | Ustawienie | Akcja |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę zasobów. |
    | **Nazwa** | Wprowadź unikatowy ciąg tekstowy w grupie zasobów. |
    | **Region** | Wybierz odpowiednią lokalizację. |

4. Wybierz pozycję **Przegląd + utwórz**.

5. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Wyświetlanie wszystkich grup zabezpieczeń sieci

Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**. Lista grup zabezpieczeń sieciowych pojawi się dla subskrypcji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg lista](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Grupa bezpieczeństwa Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Wyświetlanie szczegółów sieciowej grupy zabezpieczeń

1. Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń.

Na pasku menu sieciowej grupy zabezpieczeń w obszarze **Ustawienia**można wyświetlić **reguły zabezpieczeń przychodzących**, **Reguły zabezpieczeń** **wychodzących, Interfejsy sieciowe**i **Podsieci,** z którymi jest skojarzona grupa zabezpieczeń sieci.

W obszarze **Monitorowanie**można włączyć lub wyłączyć **ustawienia diagnostyczne**. W obszarze **Pomoc techniczna + rozwiązywanie problemów**można wyświetlić **reguły efektywnego bezpieczeństwa**. Aby dowiedzieć się więcej, zobacz [Rejestrowanie diagnostyczne dla sieciowej grupy zabezpieczeń](virtual-network-nsg-manage-log.md) i [Diagnozowanie problemu z filtrem ruchu sieciowego maszyny Wirtualnej](diagnose-network-traffic-filter-problem.md).

Aby dowiedzieć się więcej o typowych ustawieniach platformy Azure na liście, zobacz następujące artykuły:

- [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md)
- [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
- [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Skrypt usługi Automation](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg pokaż](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Grupa bezpieczeństwa Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Zmienianie sieciowej grupy zabezpieczeń

1. Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, którą chcesz zmienić.

Najczęstszymi zmianami jest [dodanie reguły zabezpieczeń](#create-a-security-rule), [usunięcie reguły](#delete-a-security-rule)oraz [skojarzenie lub odłączenie sieciowej grupy zabezpieczeń z lub z podsieci lub interfejsu sieciowego.](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć nsg aktualizacja](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Grupa bezpieczeństwa Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Kojarzenie lub odłączanie sieciowej grupy zabezpieczeń z lub z podsieci lub interfejsu sieciowego

Aby skojarzyć sieciową grupę zabezpieczeń z sieciową grupą zabezpieczeń lub odłączyć ją od interfejsu [sieciowego, zobacz Kojarzenie sieciowej grupy zabezpieczeń z sieciową grupą zabezpieczeń lub odłączanie sieciowej grupy zabezpieczeń od interfejsu sieciowego](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Aby skojarzyć grupę zabezpieczeń sieci lub odłączyć grupę zabezpieczeń sieci z podsieci, zobacz [Zmienianie ustawień podsieci](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Usuwanie sieciowej grupy zabezpieczeń

Jeśli sieciowa grupa zabezpieczeń jest skojarzona z dowolnymi podsieciami lub interfejsami sieciowymi, nie można jej usunąć. Przed podjęciem próby jej usunięcia należy odłączyć grupę zabezpieczeń sieci od wszystkich podsieci i interfejsów sieciowych.

1. Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, którą chcesz usunąć.

3. Na pasku narzędzi sieciowej grupy zabezpieczeń wybierz pozycję **Usuń**. Następnie wybierz **pozycję Tak** w oknie dialogowym potwierdzenia.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć nsg usunąć](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Usuń grupę zabezpieczeń sieciowych](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Praca z regułami zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera zero lub więcej reguł zabezpieczeń. Można tworzyć, [wyświetlać wszystkie](#view-all-security-rules), [wyświetlać szczegóły](#view-details-of-a-security-rule), [zmieniać](#change-a-security-rule)i [usuwać](#delete-a-security-rule) regułę zabezpieczeń.

### <a name="create-a-security-rule"></a>Tworzenie reguły zabezpieczeń

Istnieje limit liczby reguł na grupę zabezpieczeń sieci, które można utworzyć dla każdej lokalizacji i subskrypcji platformy Azure. Aby dowiedzieć się więcej, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić sieciowe grupy zabezpieczeń. Wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, do której chcesz dodać regułę zabezpieczeń.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz pozycję **Reguły zabezpieczeń przychodzących** lub **Reguły zabezpieczeń wychodzących**.

    Na liście znajduje się kilka istniejących reguł, w tym niektóre, których nie zostały dodane. Podczas tworzenia sieciowej grupy zabezpieczeń jest w niej tworzonych kilka domyślnych reguł zabezpieczeń. Aby dowiedzieć się więcej, zobacz [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules).  Nie można usunąć domyślnych reguł zabezpieczeń, ale można je zastąpić regułami o wyższym priorytecie.

4. <a name="security-rule-settings"></a>Wybierz **pozycję Dodaj**. Zaznacz lub dodaj wartości dla następujących ustawień, a następnie wybierz **przycisk OK:**

    | Ustawienie | Wartość | Szczegóły |
    | ------- | ----- | ------- |
    | **Źródła** | Jeden z:<ul><li>**Dowolne**</li><li>**Adresy IP**</li><li>**Tag usługi** (reguła zabezpieczeń przychodzących) lub **VirtualNetwork** (reguła zabezpieczeń wychodzących)</li><li>**Grupa&nbsp;&nbsp;zabezpieczeń aplikacji**</li></ul> | <p>W przypadku **wybrania opcji Adresy IP**należy również określić **źródłowe adresy IP/zakresy CIDR**.</p><p>Jeśli wybierzesz **tag usługi,** możesz również wybrać **tag usługi Źródłowej**.</p><p>W przypadku **wybrania grupy zabezpieczeń aplikacji**należy również wybrać istniejącą grupę zabezpieczeń aplikacji. W przypadku **wybrania grupy zabezpieczeń aplikacji** dla **źródła** i **miejsca docelowego**interfejsy sieciowe w obu grupach zabezpieczeń aplikacji muszą znajdować się w tej samej sieci wirtualnej.</p> |
    | **Źródłowe adresy IP/zakresy CIDR** | Lista adresów IP i zakresów cidr (Classless Interdomain Routing) rozdzielanych przecinkami | <p>To ustawienie jest wyświetlane po zmianie **funkcji Źródło** na **Adresy IP**. Należy określić pojedynczą wartość lub oddzieloną przecinkami listę wielu wartości. Przykładem wielu wartości `10.0.0.0/16, 192.188.1.1`jest . Istnieją ograniczenia liczby wartości, które można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Jeśli określony adres IP jest przypisany do maszyny Wirtualnej platformy Azure, określ jej prywatny adres IP, a nie publiczny adres IP. Platforma Azure przetwarza reguły zabezpieczeń po przetłumaczeniu publicznego adresu IP na prywatny adres IP dla przychodzących reguł zabezpieczeń, ale zanim przetłumaczy prywatny adres IP na publiczny adres IP dla reguł wychodzących. Aby dowiedzieć się więcej o publicznych i prywatnych adresach IP na platformie Azure, zobacz [typy adresów IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Tag usługi źródłowej** | Tag usługi z listy rozwijanej | To opcjonalne ustawienie jest wyświetlane po ustawieniu **funkcji Źródło** na **Znacznik usługi** dla reguły zabezpieczeń przychodzących. Tag usługi jest wstępnie zdefiniowanym identyfikatorem kategorii adresów IP. Aby dowiedzieć się więcej o dostępnych tagach usług i o tym, co reprezentuje każdy tag, zobacz [Tagi usługi](security-overview.md#service-tags). |
    | **Grupa zabezpieczeń aplikacji źródłowej** | Istniejąca grupa zabezpieczeń aplikacji | To ustawienie jest wyświetlane po ustawieniu grupy zabezpieczeń **Źródło** **na Application**. Wybierz grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group). |
    | **Zakresy portów źródłowych** | Jeden z:<ul><li>Pojedynczy port, taki jak`80`</li><li>Szereg portów, takich jak`1024-65535`</li><li>Oddzielona przecinkami lista pojedynczych portów i/lub zakresów portów, takich jak`80, 1024-65535`</li><li>Gwiazdka (`*`) umożliwiająca ruch na dowolnym porcie</li></ul> | To ustawienie określa porty, na których reguła zezwala lub odrzuca ruch. Istnieją ograniczenia liczby portów, które można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Docelowy** | Jeden z:<ul><li>**Dowolne**</li><li>**Adresy IP**</li><li>**Tag usługi** (reguła zabezpieczeń wychodzących) lub **VirtualNetwork** (reguła zabezpieczeń przychodzących)</li><li>**Grupa&nbsp;&nbsp;zabezpieczeń aplikacji**</li></ul> | <p>Jeśli wybierzesz **adresy IP,** należy również określić **docelowe adresy IP/zakresy CIDR**.</p><p>Jeśli wybierzesz **VirtualNetwork,** ruch jest dozwolony dla wszystkich adresów IP w przestrzeni adresowej sieci wirtualnej. **VirtualNetwork** jest tagiem usługi.</p><p>W przypadku **wybrania grupy zabezpieczeń aplikacji**należy wybrać istniejącą grupę zabezpieczeń aplikacji. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group).</p> |
    | **Docelowe adresy IP/zakresy CIDR** | Lista adresów IP i zakresów CIDR rozdzielanych przecinkami | <p>To ustawienie jest wyświetlane po zmianie **adresu docelowego** na **adresy IP**. Podobnie jak **w przypadku źródłowych** i **źródłowych adresów IP/zakresów CIDR,** można określić pojedyncze lub wiele adresów lub zakresów. Istnieją limity liczby, którą można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).</p><p>Jeśli określony adres IP jest przypisany do maszyny Wirtualnej platformy Azure, upewnij się, że określisz jej prywatny adres IP, a nie jego publiczny adres IP. Platforma Azure przetwarza reguły zabezpieczeń po przetłumaczeniu publicznego adresu IP na prywatny adres IP dla przychodzących reguł zabezpieczeń, ale zanim platforma Azure przetłumaczy prywatny adres IP na publiczny adres IP dla reguł wychodzących. Aby dowiedzieć się więcej o publicznych i prywatnych adresach IP na platformie Azure, zobacz [typy adresów IP](virtual-network-ip-addresses-overview-arm.md).</p> |
    | **Docelowy tag usługi** | Tag usługi z listy rozwijanej | To opcjonalne ustawienie jest wyświetlane po zmianie **funkcji Miejsce docelowe** na **Znacznik usługi** dla reguły zabezpieczeń wychodzących. Tag usługi jest wstępnie zdefiniowanym identyfikatorem kategorii adresów IP. Aby dowiedzieć się więcej o dostępnych tagach usług i o tym, co reprezentuje każdy tag, zobacz [Tagi usługi](security-overview.md#service-tags). |
    | **Grupa zabezpieczeń aplikacji docelowej** | Istniejąca grupa zabezpieczeń aplikacji | To ustawienie jest wyświetlane po ustawieniu grupy zabezpieczeń **Miejsce docelowe** na **Application**. Wybierz grupę zabezpieczeń aplikacji, która istnieje w tym samym regionie co interfejs sieciowy. Dowiedz się, jak [utworzyć grupę zabezpieczeń aplikacji](#create-an-application-security-group). |
    | **Zakresy portów docelowych** | Jeden z:<ul><li>Pojedynczy port, taki jak`80`</li><li>Szereg portów, takich jak`1024-65535`</li><li>Oddzielona przecinkami lista pojedynczych portów i/lub zakresów portów, takich jak`80, 1024-65535`</li><li>Gwiazdka (`*`) umożliwiająca ruch na dowolnym porcie</li></ul> | Podobnie jak **w przypadku zakresów portów źródłowych,** można określić pojedyncze lub wiele portów i zakresów. Istnieją limity liczby, którą można określić. Aby uzyskać więcej informacji, zobacz [limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). |
    | **Protokół** | **Dowolny**, **TCP,** **UDP**lub **ICMP** | Regułę można ograniczyć do protokołu TCP( Transmission Control Protocol), Protokołu UDP (User Datagram Protocol) lub Internet Control Message Protocol (ICMP). Domyślnie reguła ma zastosowanie do wszystkich protokołów. |
    | **Akcja** | **Zezwalaj** lub **odmów** | To ustawienie określa, czy ta reguła zezwala na dostęp dla dostarczonej konfiguracji źródła i miejsca docelowego, czy też odmawia jej dostępu. |
    | **Priorytet** | Wartość od 100 do 4096 unikatowa dla wszystkich reguł zabezpieczeń w sieciowej grupie zabezpieczeń | Platforma Azure przetwarza reguły zabezpieczeń w kolejności priorytetów. Im mniejsza liczba, tym wyższy priorytet. Podczas tworzenia reguł zaleca się pozostawienie odstępu między liczbami priorytetów, takimi jak 100, 200 i 300. Pozostawienie luk ułatwia dodawanie reguł w przyszłości, dzięki czemu można nadać im wyższy lub niższy priorytet niż istniejące przepisy. |
    | **Nazwa** | Unikatowa nazwa reguły w sieciowej grupie zabezpieczeń | Nazwa może mieć maksymalnie 80 znaków. Musi zaczynać się od litery lub cyfry, a musi kończyć się literą, cyfrą lub podkreśleniem. Nazwa może zawierać tylko litery, cyfry, podkreślenia, kropki lub łączniki. |
    | **Opis** | Opis tekstowy | Opcjonalnie można określić opis tekstowy reguły zabezpieczeń. |

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Wyświetlanie wszystkich reguł zabezpieczeń

Sieciowa grupa zabezpieczeń zawiera zero lub więcej reguł. Aby dowiedzieć się więcej o informacjach wymienionych podczas wyświetlania reguł, zobacz [Omówienie sieciowej grupy zabezpieczeń](security-overview.md).

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby wyświetlić reguły sieciowej grupy zabezpieczeń. Wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, dla której chcesz wyświetlić reguły.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz pozycję **Reguły zabezpieczeń przychodzących** lub **Reguły zabezpieczeń wychodzących**.

Lista zawiera wszystkie utworzone reguły oraz [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules)sieciowej grupy zabezpieczeń .

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Wyświetlanie szczegółów reguły zabezpieczeń

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby wyświetlić reguły sieciowej grupy zabezpieczeń. Wyszukaj i wybierz **pozycję Sieciowe grupy zabezpieczeń**.

2. Wybierz nazwę sieciowej grupy zabezpieczeń, dla której chcesz wyświetlić szczegóły reguły.

3. Na pasku menu sieciowej grupy zabezpieczeń wybierz pozycję **Reguły zabezpieczeń przychodzących** lub **Reguły zabezpieczeń wychodzących**.

4. Wybierz regułę, dla której chcesz wyświetlić szczegóły. Aby uzyskać wyjaśnienie wszystkich ustawień, zobacz [Ustawienia reguł zabezpieczeń](#security-rule-settings).

    > [!NOTE]
    > Ta procedura dotyczy tylko niestandardowej reguły zabezpieczeń. To nie działa, jeśli wybierzesz domyślną regułę zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg reguły pokaż](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Zmienianie reguły zabezpieczeń

1. Wykonaj czynności opisane w [widoku szczegóły reguły zabezpieczeń](#view-details-of-a-security-rule).

2. W razie potrzeby zmień ustawienia, a następnie wybierz pozycję **Zapisz**. Aby uzyskać wyjaśnienie wszystkich ustawień, zobacz [Ustawienia reguł zabezpieczeń](#security-rule-settings).

    > [!NOTE]
    > Ta procedura dotyczy tylko niestandardowej reguły zabezpieczeń. Nie można zmienić domyślnej reguły zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Usuwanie reguły zabezpieczeń

1. Wykonaj czynności opisane w [widoku szczegóły reguły zabezpieczeń](#view-details-of-a-security-rule).

2. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **Tak**.

    > [!NOTE]
    > Ta procedura dotyczy tylko niestandardowej reguły zabezpieczeń. Nie można usunąć domyślnej reguły zabezpieczeń.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az network nsg reguła usunąć](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Usuń-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Praca z grupami zabezpieczeń aplikacji

Grupa zabezpieczeń aplikacji zawiera zero lub więcej interfejsów sieciowych. Aby dowiedzieć się więcej, zobacz [grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups). Wszystkie interfejsy sieciowe w grupie zabezpieczeń aplikacji muszą istnieć w tej samej sieci wirtualnej. Aby dowiedzieć się, jak dodać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz [Dodawanie interfejsu sieciowego do grupy zabezpieczeń aplikacji](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Tworzenie grupy zabezpieczeń aplikacji

1. W menu [portalu azure](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wprowadź *grupę zabezpieczeń aplikacji*.

3. Na stronie **Grupa zabezpieczeń aplikacji** wybierz pozycję **Utwórz**.

4. Na stronie **Tworzenie grupy zabezpieczeń aplikacji** na karcie **Podstawy** ustaw wartości dla następujących ustawień:

    | Ustawienie | Akcja |
    | --- | --- |
    | **Subskrypcja** | Wybierz subskrypcję. |
    | **Grupa zasobów** | Wybierz istniejącą grupę zasobów lub wybierz pozycję **Utwórz nowy,** aby utworzyć nową grupę zasobów. |
    | **Nazwa** | Wprowadź unikatowy ciąg tekstowy w grupie zasobów. |
    | **Region** | Wybierz odpowiednią lokalizację. |

5. Wybierz pozycję **Przegląd + utwórz**.

6. Na karcie **Recenzja + utwórz** po wyświetleniu komunikatu **O przekazaniu sprawdzania poprawności** wybierz pozycję **Utwórz**.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć asg tworzenie](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [Nowa grupa AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Wyświetlanie wszystkich grup zabezpieczeń aplikacji

Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić grupy zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji**. Portal platformy Azure wyświetla listę grup zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć asg lista](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Grupa Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Wyświetlanie szczegółów określonej grupy zabezpieczeń aplikacji

1. Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić grupę zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji**.

2. Wybierz nazwę grupy zabezpieczeń aplikacji, której szczegóły chcesz wyświetlić.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć asg pokaż](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Grupa Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Zmienianie grupy zabezpieczeń aplikacji

1. Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby wyświetlić grupę zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji**.

2. Wybierz nazwę grupy zabezpieczeń aplikacji, którą chcesz zmienić.

3. Wybierz **zmień** obok ustawienia, które chcesz zmodyfikować. Można na przykład dodać lub usunąć **znaczniki**lub zmienić **grupę Zasobów** lub **Subskrypcję**.

    > [!NOTE]
    > Nie można zmienić lokalizacji.

    Na pasku menu można również wybrać **opcję Kontrola dostępu (IAM)**. Na stronie **Kontrola dostępu (IAM)** można przypisać lub usunąć uprawnienia do grupy zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć asg aktualizacja](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Brak polecenia cmdlet programu PowerShell |

### <a name="delete-an-application-security-group"></a>Usuwanie grupy zabezpieczeń aplikacji

Nie można usunąć grupy zabezpieczeń aplikacji, jeśli zawiera ona interfejsy sieciowe. Aby usunąć wszystkie interfejsy sieciowe z grupy zabezpieczeń aplikacji, zmień ustawienia interfejsu sieciowego lub usuń interfejsy sieciowe. Aby dowiedzieć się więcej, zobacz [Dodawanie do grup zabezpieczeń aplikacji lub usuwanie z niej](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) lub Usuwanie interfejsu [sieciowego](virtual-network-network-interface.md#delete-a-network-interface).

1. Przejdź do [witryny Azure Portal,](https://portal.azure.com) aby zarządzać grupami zabezpieczeń aplikacji. Wyszukaj i wybierz **pozycję Grupy zabezpieczeń aplikacji**.

2. Wybierz nazwę grupy zabezpieczeń aplikacji, którą chcesz usunąć.

3. Wybierz **pozycję Usuń**, a następnie wybierz pozycję **Tak,** aby usunąć grupę zabezpieczeń aplikacji.

#### <a name="commands"></a>Polecenia

| Narzędzie | Polecenie |
| ---- | ------- |
| Interfejs wiersza polecenia platformy Azure | [az sieć asg usunąć](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Usuń-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w sieciowych grupach zabezpieczeń, regułach zabezpieczeń i grupach zabezpieczeń aplikacji, konto musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która ma przypisane odpowiednie uprawnienia wymienione w poniższych tabelach:

### <a name="network-security-group"></a>Sieciowa grupa zabezpieczeń

| Akcja                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Pobierz grupę zabezpieczeń sieci                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Tworzenie lub aktualizowanie sieciowej grupy zabezpieczeń                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Usuwanie sieciowej grupy zabezpieczeń                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Kojarzenie sieciowej grupy zabezpieczeń z podsiecią lub interfejsem sieciowym 

### <a name="network-security-group-rule"></a>Reguła sieciowej grupy zabezpieczeń

| Akcja                                                        |   Nazwa                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Pobierz regułę                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Tworzenie lub aktualizowanie reguły                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Usuwanie reguły                                                         |

### <a name="application-security-group"></a>Grupa zabezpieczeń aplikacji

| Akcja                                                                     | Nazwa                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Dołączanie konfiguracji IP do grupy zabezpieczeń aplikacji|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Dołączanie reguły zabezpieczeń do grupy zabezpieczeń aplikacji    |
| Microsoft.Network/applicationSecurityGroups/read                           | Pobierz grupę zabezpieczeń aplikacji                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Tworzenie lub aktualizowanie grupy zabezpieczeń aplikacji           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Usuwanie grupy zabezpieczeń aplikacji                     |

## <a name="next-steps"></a>Następne kroki

- Tworzenie grupy zabezpieczeń sieci lub aplikacji przy użyciu przykładowych skryptów interfejsu [wiersza polecenia](cli-samples.md) [programu PowerShell](powershell-samples.md) lub platformy Azure lub [szablonów usługi](template-samples.md) Azure Resource Manager
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
