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
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: 4af8c6dbbcbb877351018eb881f4d3b0b447bbce
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098977"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej

Dowiedz się, jak dodać, zmienić lub usunąć podsieć sieci wirtualnej. Wszystkie zasoby platformy Azure wdrożone w sieci wirtualnej są wdrażane w podsieci w sieci wirtualnej. Jeśli dopiero zaczynasz korzystanie z sieci wirtualnych, możesz dowiedzieć się więcej o nich w [sieci wirtualnej](virtual-networks-overview.md) lub za pomocą [samouczka](quick-create-portal.md). Aby utworzyć, zmienić lub usunąć sieć wirtualną, zobacz [Zarządzanie siecią wirtualną](manage-virtual-network.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków opisanych w sekcji tego artykułu wykonaj następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, Utwórz [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli używasz portalu, Otwórz https://portal.azure.com program i zaloguj się przy użyciu konta platformy Azure.
- W przypadku wykonywania zadań w tym artykule przy użyciu poleceń programu PowerShell uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/powershell)lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- W przypadku korzystania z poleceń interfejsu wiersza polecenia (CLI) platformy Azure w celu wykonania zadań w tym artykule Uruchom polecenia w [Azure Cloud Shell](https://shell.azure.com/bash)lub przez uruchomienie interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, musisz też uruchomić `az login` polecenie, aby utworzyć połączenie z platformą Azure.

Konto, do którego należy się zalogować lub połączyć się z platformą Azure za pomocą programu, musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie akcje wymienione w obszarze [uprawnienia](#permissions).

## <a name="add-a-subnet"></a>Dodawanie podsieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, do której chcesz dodać podsieć.
3. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**.
4. Wybierz pozycję **+ podsieć**.
5. Wprowadź wartości dla następujących parametrów:
   - **Nazwa**: Nazwa musi być unikatowa w obrębie sieci wirtualnej. Aby zapewnić maksymalną zgodność z innymi usługami platformy Azure, zalecamy użycie litery jako pierwszego znaku nazwy. Na przykład platforma Azure Application Gateway nie zostanie wdrożona w podsieci o nazwie rozpoczynającej się od cyfry.
   - **Zakres adresów**: Zakres musi być unikatowy w obrębie przestrzeni adresowej sieci wirtualnej. Zakres nie może nakładać się na inne zakresy adresów podsieci w obrębie sieci wirtualnej. Przestrzeń adresową należy określić przy użyciu notacji CIDR (Inter-Domain Routing). Na przykład w sieci wirtualnej z przestrzenią adresową 10.0.0.0/16 można zdefiniować przestrzeń adresową podsieci 10.0.0.0/24. Najmniejszy zakres, który można określić, to/29, który zapewnia osiem adresów IP dla podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci w celu zapewnienia zgodności z protokołem. Trzy dodatkowe adresy są zastrzeżone dla użycia usługi platformy Azure. W związku z tym Definiowanie podsieci z zakresem adresów/29 powoduje trzy użyteczne adresy IP w podsieci. Jeśli planujesz połączenie sieci wirtualnej z bramą sieci VPN, należy utworzyć podsieć bramy. Dowiedz się więcej [na temat konkretnych zagadnień dotyczących zakresów adresów dla podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Można zmienić zakres adresów po dodaniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zmiana ustawień podsieci](#change-subnet-settings).
   - **Sieciowa Grupa zabezpieczeń**: Można skojarzyć zero lub jedną istniejącą grupę zabezpieczeń sieci z podsiecią w celu filtrowania przychodzącego i wychodzącego ruchu sieciowego dla podsieci. Grupa zabezpieczeń sieci musi znajdować się w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej o [grupach zabezpieczeń sieci](security-overview.md) i [sposobach tworzenia sieciowej grupy zabezpieczeń](tutorial-filter-network-traffic.md).
   - **Tabela tras**: Można skojarzyć zero lub jedną istniejącą tabelę tras z podsiecią w celu kontrolowania routingu ruchu sieciowego do innych sieci. Tabela tras musi znajdować się w tej samej subskrypcji i lokalizacji co sieć wirtualna. Dowiedz się więcej na temat [routingu platformy Azure](virtual-networks-udr-overview.md) i [sposobu tworzenia tabeli tras](tutorial-create-route-table-portal.md)
   - **Punkty końcowe usługi:** Dla podsieci może być włączonych zero lub wiele punktów końcowych usługi. Aby włączyć punkt końcowy usługi dla usługi, wybierz usługę lub usługi, dla których chcesz włączyć punkty końcowe usługi z listy **usług** . Lokalizacja jest konfigurowana automatycznie dla punktu końcowego. Domyślnie punkty końcowe usługi są konfigurowane dla regionu sieci wirtualnej. W przypadku usługi Azure Storage w celu obsługi regionalnych scenariuszy trybu failover punkty końcowe są automatycznie konfigurowane do [sparowanych regionów platformy Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   - **Delegowanie podsieci:** Podsieć może mieć zero dla wielu delegowanych uprawnień. Delegowanie podsieci daje jawne uprawnienia do usługi w celu tworzenia zasobów specyficznych dla usługi w podsieci przy użyciu unikatowego identyfikatora podczas wdrażania usługi. Aby delegować do usługi, wybierz usługę, z której chcesz delegować listę **usług** .

       Aby usunąć punkt końcowy usługi, usuń zaznaczenie usługi, dla której chcesz usunąć punkt końcowy usługi. Aby dowiedzieć się więcej na temat punktów końcowych usługi i usług, dla których można je włączyć, zobacz [Omówienie punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md). Po włączeniu punktu końcowego usługi dla usługi należy również włączyć dostęp do sieci dla podsieci dla zasobu utworzonego za pomocą usługi. Jeśli na przykład zostanie włączony punkt końcowy usługi *Microsoft. Storage*, należy również włączyć dostęp do sieci do wszystkich kont usługi Azure Storage, do których chcesz udzielić dostępu do sieci. Aby uzyskać szczegółowe informacje o sposobie włączania dostępu do sieci w podsieciach, dla których włączono punkt końcowy usługi, zobacz dokumentację dla poszczególnych usług, dla których włączono punkt końcowy usługi.

     Aby sprawdzić, czy punkt końcowy usługi jest włączony dla podsieci, należy wyświetlić [obowiązujące trasy](diagnose-network-routing-problem.md) dla dowolnego interfejsu sieciowego w podsieci. Gdy punkt końcowy jest skonfigurowany, zostanie wyświetlona trasa *Domyślna* z prefiksami adresów usługi i nextHopType **VirtualNetworkServiceEndpoint**. Aby dowiedzieć się więcej na temat routingu, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
6. Aby dodać podsieć do wybranej sieci wirtualnej, wybierz **przycisk OK**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Subnet Create](/cli/azure/network/vnet/subnet)
- Program PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Zmień ustawienia podsieci

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną zawierającą podsieć, dla której chcesz zmienić ustawienia.
3. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**.
4. Na liście podsieci wybierz podsieć, dla której chcesz zmienić ustawienia. Można zmienić następujące ustawienia:

    - **Zakres adresów:** Jeśli w podsieci nie wdrożono żadnych zasobów, można zmienić zakres adresów. Jeśli jakieś zasoby istnieją w podsieci, należy przenieść zasoby do innej podsieci lub najpierw usunąć je z podsieci. Kroki wykonywane w celu przeniesienia lub usunięcia zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak przenosić lub usuwać zasoby znajdujące się w podsieciach, zapoznaj się z dokumentacją dla każdego typu zasobu, który ma zostać przeniesiony lub usunięty. Zobacz ograniczenia dotyczące **zakresu adresów** w kroku 5 [Dodaj podsieć](#add-a-subnet).
    - **Użytkownicy**: Dostęp do podsieci można kontrolować za pomocą wbudowanych ról lub własnych ról niestandardowych. Aby dowiedzieć się więcej na temat przypisywania ról i użytkowników w celu uzyskania dostępu do podsieci, zobacz [Korzystanie z przypisania roli w celu zarządzania dostępem do zasobów platformy Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Sieciowa Grupa zabezpieczeń** i **tabela tras**: Zobacz Krok 5 [dodawania podsieci](#add-a-subnet).
    - **Punkty końcowe usługi**: Zobacz punkty końcowe usługi w kroku 5 [Dodaj podsieć](#add-a-subnet). Podczas włączania punktu końcowego usługi dla istniejącej podsieci upewnij się, że żadne krytyczne zadania nie są uruchomione w żadnym z zasobów podsieci. Punkty końcowe usługi przełączają trasy na każdy interfejs sieciowy w podsieci z używania trasy domyślnej z prefiksem adresu *0.0.0.0/0* i typem następnego przeskoku w *Internecie*, aby użyć nowej trasy z prefiksami adresów usługi i typu następnego przeskoku z *VirtualNetworkServiceEndpoint*. W trakcie przełączania można zakończyć wszystkie otwarte połączenia TCP. Punkt końcowy usługi nie jest włączony, dopóki nie zostaną zaktualizowane przepływy ruchu do usługi dla wszystkich interfejsów sieciowych przy użyciu nowej trasy. Aby dowiedzieć się więcej na temat routingu, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
    - **Delegowanie podsieci:** Zobacz punkty końcowe usługi w kroku 5 [Dodaj podsieć](#add-a-subnet). Delegowanie podsieci można zmodyfikować na zero lub dla niego włączono wiele delegowania. Jeśli zasób dla usługi został już wdrożony w podsieci, delegowanie podsieci nie może zostać dodane lub usunięte, dopóki nie zostaną usunięte wszystkie zasoby usługi. Aby delegować dla innej usługi, wybierz usługę, z którą chcesz delegować z listy **usług** .
5. Wybierz pozycję **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- Program PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Usuwanie podsieci

Podsieć można usunąć tylko wtedy, gdy nie ma żadnych zasobów w podsieci. Jeśli w podsieci znajdują się zasoby, przed usunięciem podsieci należy usunąć zasoby znajdujące się w podsieci. Kroki wykonywane w celu usunięcia zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak usunąć zasoby znajdujące się w podsieciach, zapoznaj się z dokumentacją dla każdego typu zasobu, który chcesz usunąć.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną zawierającą podsieć, którą chcesz usunąć.
3. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**.
4. Na liście podsieci wybierz pozycję **...** po prawej stronie dla podsieci, którą chcesz usunąć.
5. Wybierz pozycję **Usuń**, a następnie wybierz pozycję **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [AZ Network VNET Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- Program PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Uprawnienia

Aby wykonać zadania w podsieciach, Twoje konto musi być przypisane do roli [współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , do której przypisano odpowiednie działania wymienione w poniższej tabeli:

|Action                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Odczytaj podsieć sieci wirtualnej              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Utwórz lub zaktualizuj podsieć sieci wirtualnej  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Usuń podsieć sieci wirtualnej            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Dołącz do sieci wirtualnej                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Włączanie punktu końcowego usługi dla podsieci     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Pobieranie maszyn wirtualnych w podsieci       |

## <a name="next-steps"></a>Następne kroki

- Tworzenie sieci wirtualnej i podsieci przy użyciu [programu PowerShell](powershell-samples.md) lub przykładów skryptów [interfejsu wiersza polecenia platformy Azure](cli-samples.md) lub korzystanie z szablonów usługi Azure [Menedżer zasobów](template-samples.md)
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
