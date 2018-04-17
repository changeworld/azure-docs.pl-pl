---
title: Tworzenie, zmienianie lub usuwanie sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie sieci wirtualnej na platformie Azure.
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
ms.openlocfilehash: c5f3dbd1aa26cbe656b2ff89106819d228cf447d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="create-change-or-delete-a-virtual-network"></a>Tworzenie, zmienianie lub usuwanie sieci wirtualnej

Dowiedz się, jak utworzyć i usunąć sieci wirtualnej i zmienić ustawienia, takie jak serwery DNS i przestrzeni adresów IP, do istniejącej sieci wirtualnej.

Sieć wirtualna jest odzwierciedla w chmurze Twoją sieć. Sieć wirtualna jest logiczną izolacją chmury Azure, przeznaczona dla Twojej subskrypcji platformy Azure. Dla każdej sieci wirtualnej, utworzony można:
- Wybierz przestrzeń adresową można przypisać. Przestrzeń adresową składa się z jednego lub więcej zakresów adresów, które są zdefiniowane przy użyciu notacji Classless Inter-Domain Routing (CIDR), takich jak 10.0.0.0/16.
- Wybierz użyć serwera DNS platformy Azure lub serwera DNS. Wszystkie zasoby, które są podłączone do sieci wirtualnej są przypisywane tego serwera DNS do rozpoznawania nazw w sieci wirtualnej.
- Segment sieci wirtualnej do podsieci, każda z własną zakres adresów w przestrzeni adresowej sieci wirtualnej. Aby dowiedzieć się, jak tworzenie, zmienianie i usuwanie podsieci, zobacz [Dodawanie, zmienianie lub usuń podsieci](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.2.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.26 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz **+ Utwórz zasób** > **sieci** > **sieci wirtualnej**.
2. Wprowadź lub wybierz wartości poniższych ustawień, a następnie wybierz **Utwórz**:
    - **Nazwa**: Nazwa musi być unikatowa w [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) umożliwia tworzenie sieci wirtualnej w. Nie można zmienić nazwy po utworzeniu sieci wirtualnej. Wraz z upływem czasu, można utworzyć wiele sieci wirtualnych. Nazewnictwa sugestii, zobacz [konwencje nazewnictwa](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Po konwencji nazewnictwa może pomóc ułatwiają zarządzanie wieloma sieciami wirtualnymi.
    - **Przestrzeń adresowa**: przestrzeni adresowej dla sieci wirtualnej składa się z co najmniej jeden-nakładających się zakresów adresów, które są określone w notacji CIDR. Zakres adresów, które należy zdefiniować można publicznych lub prywatnych (RFC 1918). Czy można zdefiniować zakres adresów jako publicznych lub prywatnych, zakres adresów jest dostępny tylko w ramach sieci wirtualnej z połączonych sieci wirtualnych i sieciami lokalnymi podłączonych do sieci wirtualnej. Nie można dodać następujących zakresów adresów:
        - 224.0.0.0/4 (multiemisji)
        - 255.255.255.255/32 (emisji)
        - 127.0.0.0/8 (sprzężenie zwrotne)
        - 169.254.0.0/16 (Link-local)
        - 168.63.129.16/32 (wewnętrzny serwer DNS)

      Chociaż podczas tworzenia sieci wirtualnej, można zdefiniować tylko jeden adres zakresu, można dodać więcej zakresów adresów do przestrzeni adresowej po utworzeniu sieci wirtualnej. Aby dowiedzieć się, jak dodać zakres adresów do istniejącej sieci wirtualnej, zobacz [Dodaj lub Usuń zakres adresów](#add-or-remove-an-address-range).

      >[!WARNING]
      >Jeśli sieć wirtualna ma zakresów adresów, które nakładają się innej sieci wirtualnej lub lokalnej sieci, nie można połączyć dwie sieci. Przed zdefiniuj zakres adresów, należy rozważyć, czy można połączyć sieć wirtualną do innych sieci wirtualnych lub sieciami lokalnymi w przyszłości.
      >
      >

    - **Nazwa podsieci**: nazwy podsieci muszą być unikatowe w ramach sieci wirtualnej. Nie można zmienić nazwy podsieci, po utworzeniu podsieci. Portal wymaga zdefiniowania w jednej podsieci po utworzeniu sieci wirtualnej, nawet jeśli sieć wirtualna nie jest wymagane do ma żadnych podsieci. W portalu można zdefiniować tylko jedną podsieć, podczas tworzenia sieci wirtualnej. Można dodać więcej podsieci do sieci wirtualnej później, po utworzeniu sieci wirtualnej. Aby dodać podsieci do sieci wirtualnej, zobacz [Zarządzanie podsieci](virtual-network-manage-subnet.md). Można utworzyć sieci wirtualnej, który ma wiele podsieci przy użyciu wiersza polecenia platformy Azure lub programu PowerShell.

      >[!TIP]
      >Czasami Administratorzy utworzyć różne podsieci filtrowanie lub kontrolować ruch routing między podsieciami. Przed zdefiniowaniem podsieci, należy rozważyć sposób można filtrować i kierować ruchem między podsieci. Aby dowiedzieć się więcej na temat filtrowania ruchu między podsieciami, zobacz [sieciowej grupy zabezpieczeń](security-overview.md). Azure automatycznie trasy ruch między podsieciami, ale można zastąpić trasy domyślne platformy Azure. Aby dowiedzieć się więcej o routingu ruchu podsieci domyślne Azures, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
      >

    - **Zakres adresów podsieci**: zakres musi być w przestrzeni adresowej dla sieci wirtualnej. Najmniejszą zakres, który można określić jest /29, co umożliwia osiem adresów IP podsieci. Azure rezerwuje pierwszy i ostatni adres w każdej podsieci dla zgodności protokołu. Trzy dodatkowe adresy są zarezerwowane do użycia usługi Azure. W związku z tym sieci wirtualnej z zakresem adresów podsieci /29 zawiera tylko trzy można używać adresów IP. Jeśli planowane jest połączenie wirtualnej sieci do bramy sieci VPN, należy utworzyć podsieć bramy. Dowiedz się więcej o [zagadnienia dotyczące zakresu określonego adresu podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Zakres adresów można zmienić po utworzeniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zarządzanie podsieci](virtual-network-manage-subnet.md).
    - **Subskrypcja**: Wybierz [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Nie można użyć tej samej sieci wirtualnej w więcej niż jedną subskrypcją platformy Azure. Jednak możesz nawiązać połączenie sieci wirtualnej w ramach jednej subskrypcji sieci wirtualnych w inne subskrypcje z [sieci wirtualnej komunikacji równorzędnej](virtual-network-peering-overview.md). Zasobów platformy Azure, podłączoną do sieci wirtualnej muszą być w tej samej subskrypcji co sieć wirtualna.
    - **Grupa zasobów**: Wybierz istniejący [grupy zasobów](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) lub Utwórz nową. Zasobów platformy Azure, podłączoną do sieci wirtualnej może być w tej samej grupie zasobów co sieć wirtualna lub w innej grupie zasobów.
    - **Lokalizacja**: wybierz pozycję Azure [lokalizacji](https://azure.microsoft.com/regions/), znanej również jako regionu. Sieć wirtualna może być w tylko jednej lokalizacji platformy Azure. Można jednak połączyć sieć wirtualną w jednej lokalizacji do sieci wirtualnej w innej lokalizacji przy użyciu bramy sieci VPN. Zasobów platformy Azure, podłączoną do sieci wirtualnej musi być w tej samej lokalizacji co sieć wirtualna.

**Polecenia**

- Azure CLI: [utworzyć az sieci wirtualnej](/cli/azure/network/vnet)
- PowerShell: [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Widok sieci wirtualnych i ustawień

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go.
2. Wybierz sieci wirtualnej, który chcesz wyświetlić ustawienia z listy sieci wirtualnych.
3. Następujące ustawienia są wyświetlane dla wybranej sieci wirtualnej:
    - **Omówienie**: zawiera informacje o sieci wirtualnej, w tym przestrzeń adresową i serwery DNS. Poniższy zrzut ekranu przedstawia Przegląd ustawień sieci wirtualnej o nazwie **MyVNet**:

        ![Omówienie interfejsu sieciowego](./media/manage-virtual-network/vnet-overview.png)

      Sieć wirtualną można przenieść do innej grupy zasobów lub subskrypcji, wybierając **zmiany** obok **grupy zasobów** lub **Nazwa subskrypcji**. Aby dowiedzieć się, jak przenieść sieć wirtualną, zobacz [przenoszenia zasobów w innej grupie zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Artykuł zawiera listę wymagań wstępnych i sposobu przenoszenia zasobów przy użyciu portalu Azure, programu PowerShell i interfejsu wiersza polecenia Azure. Wszystkie zasoby, które są podłączone do sieci wirtualnej, należy przenieść z siecią wirtualną.
    - **Przestrzeń adresowa**: znajdują się przestrzeni adresowych, które są przypisane do sieci wirtualnej. Aby dowiedzieć się, jak dodać i usunąć zakres adresów do przestrzeni adresowej, wykonaj kroki [Dodaj lub Usuń zakres adresów](#add-or-remove-an-address-range).
    - **Połączone urządzenia**: wymienione są wszystkie zasoby, które są podłączone do sieci wirtualnej. Na poprzednim zrzucie ekranu pokazano trzy interfejsy sieciowe i jeden moduł równoważenia obciążenia są podłączone do sieci wirtualnej. Wyświetlane są nowe zasoby, które tworzą i połączyć sieć wirtualną. Jeśli usuniesz z zasobem, który został podłączony do sieci wirtualnej, nie będzie widoczny na liście.
    - **Podsieci**: przedstawiono listę podsieci, które istnieją w sieci wirtualnej. Aby dowiedzieć się, jak dodać i usunąć podsieć, zobacz [Zarządzanie podsieci](virtual-network-manage-subnet.md).
    - **Serwery DNS**: można określić, czy Azure wewnętrznego serwera DNS lub niestandardowy serwer DNS udostępnia rozpoznawanie nazw dla urządzeń, które są podłączone do sieci wirtualnej. Po utworzeniu sieci wirtualnej przy użyciu portalu Azure, serwery DNS platformy Azure są używane do rozpoznawania nazw w sieci wirtualnej domyślnie. Aby zmodyfikować serwery DNS, wykonaj kroki [serwerów DNS zmiany](#change-dns-servers) w tym artykule.
    - **Komunikacji równorzędnych**: Jeśli istnieją istniejącego komunikacji równorzędnych w subskrypcji, są one wyświetlane tutaj. Można wyświetlać ustawienia dla istniejącego komunikacji równorzędnych, lub utworzyć, zmienić lub usunąć komunikacji równorzędnych. Aby dowiedzieć się więcej na temat komunikacji równorzędnych, zobacz [równorzędna sieci wirtualnej](virtual-network-peering-overview.md).
    - **Właściwości**: Wyświetla ustawienia dotyczące sieci wirtualnej, w tym identyfikator zasobu sieci wirtualnej i subskrypcji platformy Azure w.
    - **Diagram**: na diagramie przedstawiono wizualną reprezentację wszystkich urządzeń, które są podłączone do sieci wirtualnej. Diagram zawiera niektóre najważniejsze informacje na temat urządzeń. Do zarządzania urządzeniem, w tym widoku diagramu, wybierz urządzenie.
    - **Typowe ustawienia Azure**: Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące informacje:
        *   [Dziennik aktywności](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs)
        *   [Kontrola dostępu (IAM)](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control)
        *   [Tagi](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#tags)
        *   [Blokady](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        *   [Skrypt automatyzacji](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group)

**Polecenia**

- Azure CLI: [az sieci vnet show](/cli/azure/network/vnet#az_network_vnet_show)
- PowerShell: [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Dodawanie lub usuwanie zakres adresów

Można dodawać i usuwać zakresy adresów sieci wirtualnej. Zakres adresów musi być określony w notacji CIDR, a nie może nakładać się na inne zakresy adresów w obrębie tej samej sieci wirtualnej. Zakresów adresów, które należy zdefiniować można publicznych lub prywatnych (RFC 1918). Czy można zdefiniować zakres adresów jako publicznych lub prywatnych, zakres adresów jest dostępny tylko w ramach sieci wirtualnej z połączonych sieci wirtualnych i sieciami lokalnymi podłączonych do sieci wirtualnej. Nie można dodać następujących zakresów adresów:

- 224.0.0.0/4 (multiemisji)
- 255.255.255.255/32 (emisji)
- 127.0.0.0/8 (sprzężenie zwrotne)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (wewnętrzny serwer DNS)

Aby dodać lub usunąć zakres adresów:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go.
2. Z listy sieci wirtualnych wybierz sieć wirtualna, dla której chcesz dodać lub usunąć zakresu adresów.
3. Wybierz **przestrzeni adresów**w obszarze **ustawienia**.
4. Wykonaj jedną z następujących opcji:
    - **Dodaj zakres adresów**: Wprowadź nowy zakres adresów. Zakres adresów nie może nakładać się z zakresem adresów, która jest zdefiniowana dla sieci wirtualnej.
    - **Usuń zakres adresów**: po prawej stronie zakres adresów, które chcesz usunąć, wybierz **...** , a następnie wybierz pozycję **Usuń**. Jeśli istnieje zakres adresów podsieci, nie można usunąć zakresu adresów. Aby usunąć zakres adresów, należy najpierw usunąć wszystkie podsieci (i wszystkie zasoby w podsieciach) istniejące w zakresie adresów.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

- Azure CLI: [zaktualizować sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="change-dns-servers"></a>Zmień serwerów DNS

Wszystkie maszyny wirtualne, które są podłączone do sieci wirtualnej rejestr przy użyciu serwerów DNS, które określają dla sieci wirtualnej. Określony serwer DNS są również używany do rozpoznawania nazw. Każdego interfejsu sieciowego (NIC) na maszynie wirtualnej może mieć własne ustawienia serwera DNS. Jeśli karta sieciowa ma swoje własne ustawienia serwera DNS, zastępują one ustawienia serwera DNS dla sieci wirtualnej. Aby dowiedzieć się więcej o ustawieniach DNS kart interfejsu Sieciowego, zobacz [interfejsu zadań i ustawień sieci](virtual-network-network-interface.md#change-dns-servers). Aby dowiedzieć się więcej na temat rozpoznawania nazw dla maszyn wirtualnych i wystąpień roli w usług Azure Cloud Services, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](virtual-networks-name-resolution-for-vms-and-role-instances.md). Aby dodać, zmienić lub usunąć serwer DNS:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go.
2. Z listy sieci wirtualnych wybierz sieci wirtualnej, dla którego chcesz zmienić serwerów DNS.
3.  Wybierz **serwerów DNS**w obszarze **ustawienia**.
4. Wybierz jedną z następujących opcji:
    - **Domyślne (zakładając Azure)**: wszystkie nazwy zasobów i prywatnych adresów IP są automatycznie rejestrowane na serwerach DNS platformy Azure. Można rozwiązać nazwy między wszystkie zasoby, które są podłączone do tej samej sieci wirtualnej. Nie można użyć tej opcji do rozpoznawania nazw w sieciach wirtualnych. Rozpoznawanie nazw w sieciach wirtualnych, należy użyć niestandardowego serwera DNS.
    - **Niestandardowe**: można dodać jeden lub więcej serwerów w granicach Azure dla sieci wirtualnej. Aby dowiedzieć się więcej na temat limity serwera DNS, zobacz [limity Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Istnieją następujące opcje:
        - **Dodaj adres**: dodaje serwer do listy serwerów DNS w sieci wirtualnej. Ta opcja również rejestruje serwer DNS z platformy Azure. Jeśli serwer DNS został już zarejestrowany przy użyciu platformy Azure, można wybrać tego serwera DNS na liście.
        - **Usuń adres**: obok serwera, który chcesz usunąć, wybierz **...** , następnie **Usuń**. Usunięcie serwera spowoduje usunięcie serwera tylko z tej listy sieci wirtualnej. Serwer DNS pozostaje zarejestrowane na platformie Azure do innych sieci wirtualne do użycia.
        - **Zmień kolejność adresów serwerów DNS**: ważne jest, aby sprawdzić, wyświetlić listę serwerów DNS w odpowiedniej kolejności dla danego środowiska. Listy serwera DNS są używane w kolejności, w jakiej zostały określone. Nie działają jako Instalator okrężnego. Jeśli pierwszy serwer DNS na liście jest osiągalna, klient korzysta z tego serwera DNS, niezależnie od tego, czy serwer DNS działa prawidłowo. Usuń wszystkie serwery DNS, które są wyświetlane, a następnie dodaj je ponownie w kolejności, która ma.
        - **Zmień adres**: zaznacz serwer DNS na liście, a następnie wprowadź nowy adres.
5. Wybierz pozycję **Zapisz**.
6. Ponowne uruchomienie maszyn wirtualnych, które są podłączone do sieci wirtualnej, aby nowe ustawienia serwera DNS są przypisane. Maszyny wirtualne w dalszym ciągu używać ich bieżące ustawienia DNS, dopóki nie zostaną ponownie uruchomione.

**Polecenia**

- Azure CLI: [zaktualizować sieci wirtualnej sieci az](/cli/azure/network/vnet#az_network_vnet_update)
- PowerShell: [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Usunąć sieci wirtualnej

Tylko wtedy, gdy nie ma żadnych zasobów dołączone do niego, można usunąć sieci wirtualnej. Jeśli istnieją zasoby podłączone do żadnej podsieci w sieci wirtualnej, należy najpierw usunąć zasoby, które są podłączone do wszystkich podsieci w sieci wirtualnej. Czynności, które należy wykonać, aby usunąć zasób zależy od zasobu. Aby dowiedzieć się, jak usunąć zasoby, które są podłączone do podsieci, przeczytaj dokumentację dla każdego typu zasobu, który chcesz usunąć. Aby usunąć sieć wirtualną:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go.
2. Wybierz sieć wirtualną, której chcesz usunąć z listy sieci wirtualnych.
3. Upewnij się, że nie istnieją żadne urządzenia podłączone do sieci wirtualnej, wybierając **urządzeń podłączonych**w obszarze **ustawienia**. W przypadku połączonych urządzeń, należy je usunąć przed usunięciem sieci wirtualnej. Jeśli nie ma żadnych podłączonych urządzeń, wybierz **omówienie**.
4. Wybierz pozycję **Usuń**.
5. Aby potwierdzić usunięcie sieci wirtualnej, wybierz **tak**.

**Polecenia**

- Azure CLI: [usunąć sieci wirtualnej sieci platformy azure](/cli/azure/network/vnet#az_network_vnet_delete)
- PowerShell: [Remove-AzureRmVirtualNetwork](/powershell/module/azurerm.network/remove-azurermvirtualnetwork)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań w sieciach wirtualnych, Twoje konto musi mieć przypisaną do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rola przypisana odpowiednie uprawnienia są wymienione w poniższej tabeli:

|Operacja                                    |   Nazwa operacji                    |
|-------------------------------------------  |   --------------------------------  |
|Microsoft.Network/virtualNetworks/read       |   Pobierz sieć wirtualną               |
|Microsoft.Network/virtualNetworks/write      |   Utwórz lub zaktualizuj sieć wirtualną  |
|Microsoft.Network/virtualNetworks/delete     |   Usunąć sieci wirtualnej            |

## <a name="next-steps"></a>Kolejne kroki

- Aby utworzyć Maszynę wirtualną, a następnie podłącz je do sieci wirtualnej, zobacz [utworzyć sieć wirtualną i połączyć maszyny wirtualne](quick-create-portal.md#create-virtual-machines).
- Aby filtrować ruch sieciowy między podsieciami sieci wirtualnej, zobacz [Utwórz grupy zabezpieczeń sieci](virtual-networks-create-nsg-arm-pportal.md).
- -To-peer sieci wirtualnej do innej sieci wirtualnej, zobacz [tworzenie sieci wirtualnej komunikacji równorzędnej](tutorial-connect-virtual-networks-portal.md).
- Aby dowiedzieć się więcej o opcjach sieci wirtualnej nawiązywania połączenia z siecią lokalną, zobacz [o bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).
