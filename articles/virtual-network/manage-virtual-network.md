---
title: Tworzenie, zmienianie lub usuwanie sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie sieci wirtualnej na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 235a82c6bba4165790c370c2641ee6cd41f10840
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64700481"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Tworzenie, zmienianie lub usuwanie sieci wirtualnej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dowiedz się, jak utworzyć i usunąć sieci wirtualnej i zmienić ustawienia, takie jak serwery DNS i przestrzeni adresów IP, dla istniejącej sieci wirtualnej. Jeśli dopiero zaczynasz pracę z sieciami wirtualnymi, możesz dowiedzieć się więcej o nich w [Omówienie usługi Virtual network](virtual-networks-overview.md) lub wykonując [samouczek](quick-create-portal.md). Sieć wirtualna zawiera podsieci. Aby dowiedzieć się, jak tworzenie, zmienianie i usuwanie podsieci, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.
- Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz **+ Utwórz zasób** > **sieć** > **sieć wirtualna**.
2. Wprowadź lub wybierz wartości dla następujących ustawień, a następnie wybierz **Utwórz**:
   - **Nazwa**: Nazwa musi być unikatowa w [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) umożliwia tworzenie sieci wirtualnej w. Nie można zmienić nazwę, po utworzeniu sieci wirtualnej. Wraz z upływem czasu, można utworzyć wiele sieci wirtualnych. Nazewnictwa sugestii, zobacz [konwencje nazewnictwa](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Następujących konwencji nazewnictwa można łatwiej zarządzać wieloma sieciami wirtualnymi.
   - **Przestrzeń adresowa**: Przestrzeń adresowa sieci wirtualnej składa się z co najmniej jeden bez nakładających się zakresów adresów, które są określone w notacji CIDR. Zakres adresów, jaką zdefiniujesz może być publiczny lub prywatny (RFC 1918). Czy można zdefiniować zakres adresów jako publicznych lub prywatnych, zakres adresów jest dostępny tylko z w ramach sieci wirtualnej z połączonymi sieciami wirtualnymi i sieciami lokalnymi, żadnych podłączonych do sieci wirtualnej. Nie można dodać poniższe zakresy adresów:
     - 224.0.0.0/4 multiemisji)
     - 255.255.255.255/32 (emisji)
     - 127.0.0.0/8 (Loopback)
     - 169.254.0.0/16 (Link-local)
     - 168.63.129.16/32 (wewnętrznego serwera DNS, DHCP i usługi Azure Load Balancer [sondy kondycji](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     Chociaż można zdefiniować tylko jeden adres, zakres, po utworzeniu sieci wirtualnej, możesz dodać więcej zakresów adresów do przestrzeni adresowej, po utworzeniu sieci wirtualnej. Aby dowiedzieć się, jak dodać zakres adresów do istniejącej sieci wirtualnej, zobacz [Dodawanie lub usuwanie zakresu adresów](#add-or-remove-an-address-range).

     >[!WARNING]
     >Jeśli sieć wirtualna ma zakresów adresów, które pokrywają się z inną siecią wirtualną lub sieci lokalnej, nie można połączyć dwie sieci. Przed zdefiniowaniem zakres adresów, należy rozważyć, czy może chcesz połączyć sieć wirtualną do innych sieci wirtualnych lub sieciami lokalnymi w przyszłości.
     >
     >

     - **Nazwa podsieci**: Nazwa podsieci musi być unikatowa w obrębie sieci wirtualnej. Nazwa podsieci nie można zmienić po utworzeniu podsieci. Portal wymaga zdefiniowania jednej podsieci podczas tworzenia sieci wirtualnej, nawet jeśli sieć wirtualna nie jest wymagane do żadnych podsieci. W portalu można zdefiniować tylko jedną podsieć, po utworzeniu sieci wirtualnej. Możesz dodać więcej podsieci w sieci wirtualnej później, po utworzeniu sieci wirtualnej. Aby dodać podsieć do sieci wirtualnej, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md). Można utworzyć sieci wirtualnej, która ma wiele podsieci przy użyciu wiersza polecenia platformy Azure lub programu PowerShell.

       >[!TIP]
       >Czasami Administratorzy tworzą w różnych podsieciach, aby filtrować lub kontrolowanie kierowania ruchu między podsieciami. Przed zdefiniowaniem podsieci należy wziąć pod uwagę sposób można filtrować i kierować ruchem między podsieciami sieci. Aby uzyskać więcej informacji na temat filtrowania ruchu między podsieciami, zobacz [sieciowe grupy zabezpieczeń](security-overview.md). Usługa Azure automatycznie kieruje ruch pomiędzy podsieciami, ale można zastąpić trasy domyślne systemu Azure. Aby dowiedzieć się więcej na temat routingu ruchu w podsieci Azures domyślnego, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
       >

     - **Zakres adresów podsieci**: Zakres musi być w przestrzeni adresowej, wprowadzone w ramach sieci wirtualnej. Najmniejsza zakres, który można określić jest rozmiarze/29, zapewniającą osiem adresów IP w podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci na potrzeby zgodności protokołów. Trzy dodatkowe adresy są zarezerwowane do użycia usług platformy Azure. W rezultacie sieci wirtualnej o zakres adresów podsieci/29 zawiera tylko dla trzech można używać adresów IP. Jeśli planujesz połączyć sieć wirtualną z bramą sieci VPN, należy utworzyć podsieć bramy. Dowiedz się więcej o [zakresu określonego adresu informacje dotyczące podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Zakres adresów można zmienić po utworzeniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).
     - **Subskrypcja**: Wybierz [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Nie można użyć tej samej sieci wirtualnej w więcej niż jedną subskrypcję platformy Azure. Jednak można połączyć sieci wirtualnej w jednej subskrypcji z sieciami wirtualnymi w innych subskrypcjach, o [wirtualne sieci równorzędne](virtual-network-peering-overview.md). Dowolnych zasobów platformy Azure, w którym jest nawiązywane połączenie sieć wirtualna musi być w tej samej subskrypcji co sieć wirtualna.
     - **Grupa zasobów**: Wybierz istniejącą [grupy zasobów](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) lub utworzyć nowy. Zasób platformy Azure, w którym jest nawiązywane połączenie sieci wirtualnej może być w tej samej grupie zasobów co sieć wirtualna lub w innej grupie zasobów.
     - **Lokalizacja**: Wybierz platformę Azure [lokalizacji](https://azure.microsoft.com/regions/), znanego również jako regionu. Sieć wirtualna może być w tylko jednej lokalizacji platformy Azure. Jednak można połączyć sieci wirtualnej w jednej lokalizacji do sieci wirtualnej w innej lokalizacji za pomocą bramy sieci VPN. Dowolnych zasobów platformy Azure, w którym jest nawiązywane połączenie sieć wirtualna musi być w tej samej lokalizacji co sieć wirtualna.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [tworzenie sieci wirtualnej sieci az](/cli/azure/network/vnet)
- Program PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Widok sieci wirtualnych i ustawienia

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, którą chcesz wyświetlić ustawienia.
3. Następujące ustawienia są wyświetlane w ramach sieci wirtualnej, wybrane:
   - **Omówienie**: Zawiera informacje dotyczące sieci wirtualnej, w tym przestrzeń adresową i serwery DNS. Poniższy zrzut ekranu przedstawia Przegląd ustawień sieci wirtualnej o nazwie **MyVNet**:

     ![Omówienie interfejsu sieciowego](./media/manage-virtual-network/vnet-overview.png)

     Sieć wirtualną można przenieść do innej subskrypcji lub grupy zasobów, wybierając **zmiany** obok **grupy zasobów** lub **Nazwa subskrypcji**. Aby dowiedzieć się, jak przenieść sieci wirtualnej, zobacz [przenoszenie zasobów do innej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Artykuł zawiera listę wymagań wstępnych i sposobu przenoszenia zasobów przy użyciu witryny Azure portal, programu PowerShell i wiersza polecenia platformy Azure. Wszystkie zasoby, które są podłączone do sieci wirtualnej należy przenieść z siecią wirtualną.
   - **Przestrzeń adresowa**: Przestrzenie adresów, które są przypisane do sieci wirtualnej są wyświetlane. Aby dowiedzieć się, jak dodawanie i usuwanie zakresu adresów do przestrzeni adresowej, wykonaj kroki opisane w [Dodawanie lub usuwanie zakresu adresów](#add-or-remove-an-address-range).
   - **Połączone urządzenia**: Wyświetlane są wszystkie zasoby, które są podłączone do sieci wirtualnej. Na poprzednim zrzucie ekranu trzy interfejsy sieciowe i jeden moduł równoważenia obciążenia są podłączone do sieci wirtualnej. Są wyświetlane wszystkie nowe zasoby, które umożliwiają tworzenie i łączenie się z siecią wirtualną. Jeśli usuniesz zasób, który był połączony z siecią wirtualną, nie są już wyświetlane na liście.
   - **Podsieci**: Wyświetlana jest lista podsieci, które istnieją w ramach sieci wirtualnej. Aby dowiedzieć się, jak dodawanie i usuwanie podsieci, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).
   - **Serwery DNS**: Można określić, czy Azure wewnętrznego serwera DNS lub niestandardowy serwer DNS udostępnia rozpoznawanie nazw dla urządzeń, które są podłączone do sieci wirtualnej. Po utworzeniu sieci wirtualnej przy użyciu witryny Azure portal, serwery DNS platformy Azure są używane do rozpoznawania nazw w obrębie sieci wirtualnej, domyślnie. Aby zmodyfikować serwery DNS, wykonaj kroki opisane w [serwerów DNS zmiany](#change-dns-servers) w tym artykule.
   - **Komunikacja równorzędna**: W przypadku istniejącego komunikacji równorzędnej w ramach subskrypcji, są one wymienione w tym miejscu. Możesz wyświetlić ustawienia dla istniejącego komunikacji równorzędnej, lub utworzyć, zmienić lub usunąć komunikacji równorzędnej. Aby dowiedzieć się więcej na temat komunikacji równorzędnej, zobacz [komunikacja równorzędna sieci wirtualnych](virtual-network-peering-overview.md).
   - **Właściwości**: Wyświetla ustawienia dotyczące sieci wirtualnej, w tym identyfikator zasobu sieci wirtualnej i subskrypcji platformy Azure, w którym się.
   - **Diagram**: Na diagramie przedstawiono wizualna reprezentacja dla wszystkich urządzeń, które są podłączone do sieci wirtualnej. Diagram ma kilka kluczowych informacji o urządzeniach. Zarządzanie urządzeniem, w tym widoku na diagramie wybierz urządzenie.
   - **Typowe ustawienia Azure**: Aby dowiedzieć się więcej na temat typowych ustawień platformy Azure, zobacz następujące informacje:
     - [Dziennik aktywności](../azure-monitor/platform/activity-logs-overview.md)
     - [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
     - [Tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Blokady](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Skrypt automatyzacji](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci vnet show](/cli/azure/network/vnet)
- Program PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Dodawanie lub usuwanie zakresu adresów

Można dodawać i usuwać zakresów adresów sieci wirtualnej. Zakres adresów musi być określony w notacji CIDR, a nie może nakładać się na inne zakresy adresów w obrębie tej samej sieci wirtualnej. Zakresy adresów, jaką zdefiniujesz może być publiczny lub prywatny (RFC 1918). Czy można zdefiniować zakres adresów jako publicznych lub prywatnych, zakres adresów jest dostępny tylko z w ramach sieci wirtualnej z połączonymi sieciami wirtualnymi i sieciami lokalnymi, żadnych podłączonych do sieci wirtualnej. 

Można zmniejszyć zakres adresów sieci wirtualnej, jeśli nie masz żadnych podsieci skojarzonych z nim. W przeciwnym razie można rozszerzyć tylko zakres adresów, na przykład zmiana /16 do /8. Można rozpocząć z zakresem adresów małe, a następnie go później rozszerzyć lub Dodaj dodatkowe zakresy.

<!-- the last two sentences above are added per GitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Nie można dodać poniższe zakresy adresów:

- 224.0.0.0/4 multiemisji)
- 255.255.255.255/32 (emisji)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 (wewnętrznego serwera DNS, DHCP i usługi Azure Load Balancer [sondy kondycji](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Aby dodać lub usunąć zakres adresów:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych Wybieranie sieci wirtualnej, dla którego chcesz dodać lub usunąć zakresu adresów.
3. Wybierz **przestrzeń adresowa**w obszarze **ustawienia**.
4. Wykonaj jedną z następujących opcji:
    - **Dodaj zakres adresów**: Wprowadź nowy zakres adresów. Zakres adresów nie może nakładać się z zakresem adresów, która jest zdefiniowana dla sieci wirtualnej.
    - **Usuń zakres adresów**: Po prawej stronie zakres adresów, które chcesz usunąć, wybierz **...** , a następnie wybierz **Usuń**. Jeśli w zakresie adresów podsieci, nie można usunąć zakresu adresów. Aby usunąć zakres adresów, należy najpierw usunąć wszelkie podsieci (i wszystkie zasoby w podsieciach) znajdujące się w zakresie adresów.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci vnet update](/cli/azure/network/vnet)
- Program PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Zmień serwerów DNS

Wszystkie maszyny wirtualne, które są podłączone do sieci wirtualnej, zarejestruj się przy użyciu serwerów DNS, które określisz dla sieci wirtualnej. Używane także przez określony serwer DNS do rozpoznawania nazw. Każdy interfejs sieciowy (karta sieciowa) na maszynie wirtualnej może mieć własne ustawienia serwera DNS. Jeśli karta sieciowa ma swoje własne ustawienia serwera DNS, zastępują ustawienia serwera DNS dla sieci wirtualnej. Aby dowiedzieć się więcej na temat ustawień DNS interfejsu Sieciowego, zobacz [interfejsu zadań i ustawień sieci](virtual-network-network-interface.md#change-dns-servers). Aby dowiedzieć się więcej na temat rozpoznawania nazw dla maszyn wirtualnych i wystąpień roli w usługach Azure Cloud Services, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](virtual-networks-name-resolution-for-vms-and-role-instances.md). Aby dodać, zmienić lub usunąć serwer DNS:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz sieci wirtualnej, dla którego chcesz zmienić serwerów DNS.
3. Wybierz **serwerów DNS**w obszarze **ustawienia**.
4. Wybierz jedną z następujących opcji:
   - **Domyślne (dostarczone do platformy Azure)**: Wszystkie nazwy zasobów i prywatne adresy IP są automatycznie rejestrowane na serwerach DNS platformy Azure. Można rozwiązać nazwy między wszystkie zasoby, które są podłączone do tej samej sieci wirtualnej. Tej opcji nie można używać do rozpoznawania nazw w sieciach wirtualnych. Aby rozpoznawanie nazw w sieciach wirtualnych, należy użyć niestandardowego serwera DNS.
   - **Niestandardowy**: Możesz dodać jeden lub więcej serwerów w granicach platformy Azure dla sieci wirtualnej. Aby dowiedzieć się więcej na temat limity serwera DNS, zobacz [limity platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Istnieją następujące opcje:
   - **Dodaj adres**: Dodaje serwer do listy serwerów DNS sieci wirtualnej. Ta opcja również rejestruje serwer DNS z platformą Azure. Jeśli użytkownik jest już zarejestrowany serwer DNS z platformą Azure, możesz wybrać tego serwera DNS, na liście.
   - **Usuń adres**: Obok serwera, który chcesz usunąć, wybierz **...** , następnie **Usuń**. Usunięcie serwera usuwa serwer tylko z tej listy sieci wirtualnej. Serwer DNS nadal zarejestrowany na platformie Azure, aby użyć innych sieci wirtualnych.
   - **Zmień kolejność adresów serwerów DNS**: Jest to ważne, aby sprawdzić, czy listy serwerów DNS w odpowiedniej kolejności dla danego środowiska. Listy serwerów DNS są używane w kolejności, w jakiej zostały określone. Nie działają jako ustawienia działania okrężnego. Jeśli pierwszy serwer DNS na liście jest osiągalna, klient korzysta z tego serwera DNS, niezależnie od tego, czy serwer DNS działa prawidłowo. Usuń wszystkie serwery DNS, które są wyświetlane, a następnie dodaj je ponownie w kolejności, w którym chcesz.
   - **Zmień adres**: Wyróżnij serwer DNS, na liście, a następnie wprowadź nowy adres.
5. Wybierz pozycję **Zapisz**.
6. Ponowne uruchamianie maszyn wirtualnych, które są podłączone do sieci wirtualnej, dzięki czemu przypisaniu nowych ustawień serwera DNS. Maszyny wirtualne w dalszym ciągu używać ich bieżących ustawień DNS, dopóki nie zostaną ponownie uruchomione.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci vnet update](/cli/azure/network/vnet)
- Program PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Usuwanie sieci wirtualnej

Tylko wtedy, gdy nie ma żadnych zasobów dołączone do niego, można usunąć sieci wirtualnej. W przypadku zasobów podłączone do żadnej podsieci w sieci wirtualnej, należy najpierw usunąć zasoby, które są podłączone do wszystkich podsieci w sieci wirtualnej. Kroki, które należy wykonać w celu usuwania zasobu różnią się zależnie od zasobu. Aby dowiedzieć się, jak usunąć zasoby, które są podłączone do podsieci, przeczytaj dokumentację dla każdego typu zasobu, które chcesz usunąć. Można usunąć sieci wirtualnej:

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, które chcesz usunąć.
3. Upewnij się, że nie istnieją żadne urządzenia podłączone do sieci wirtualnej, wybierając **połączone urządzenia**w obszarze **ustawienia**. W przypadku połączonych urządzeń, możesz je usunąć przed usunięciem sieci wirtualnej. Brak połączonych urządzeń, wybierz opcję **Przegląd**.
4. Wybierz pozycję **Usuń**.
5. Aby potwierdzić usuwanie sieci wirtualnej, zaznacz **tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [usunąć sieci wirtualnej sieci platformy azure](/cli/azure/network/vnet)
- Program PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań w sieciach wirtualnych, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie działania wymienione w poniższej tabeli:

| Akcja                                  |   Name (Nazwa)                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Przeczytaj sieci wirtualnej              |
|Microsoft.Network/virtualNetworks/write  |   Tworzenie lub aktualizowanie sieci wirtualnej  |
|Microsoft.Network/virtualNetworks/delete |   Usuwanie sieci wirtualnej            |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie sieci wirtualnej przy użyciu [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonów usługi Resource Manager](template-samples.md)
- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych
