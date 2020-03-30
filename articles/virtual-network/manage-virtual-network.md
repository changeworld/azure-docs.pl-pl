---
title: Tworzenie, zmienianie lub usuwanie sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć, zmienić lub usunąć sieć wirtualną na platformie Azure.
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
ms.openlocfilehash: 70523dc12f3f20362fcf4a2c3cb456a182038e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280237"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Tworzenie, zmienianie i usuwanie sieci wirtualnej

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Dowiedz się, jak utworzyć i usunąć sieć wirtualną oraz zmienić ustawienia, takie jak serwery DNS i przestrzenie adresów IP, dla istniejącej sieci wirtualnej. Jeśli jesteś nowy w sieciach wirtualnych, możesz dowiedzieć się więcej o nich w [przeglądzie sieci wirtualnej](virtual-networks-overview.md) lub wykonując [samouczek](quick-create-portal.md). Sieć wirtualna zawiera podsieci. Aby dowiedzieć się, jak tworzyć, zmieniać i usuwać podsieci, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Wykonaj następujące zadania przed wykonaniem kroków w dowolnej sekcji tego artykułu:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać [bezpłatne konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z https://portal.azure.comportalu, otwórz program i zaloguj się za pomocą konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule są używane polecenia programu PowerShell, należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchomić program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, należy również uruchomić, aby utworzyć połączenie z platformą Azure.
- Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Wybierz **+ Utwórz zasób** > **Sieć wirtualna** > **Virtual network**.
2. Wprowadź lub wybierz wartości dla następujących ustawień, a następnie wybierz pozycję **Utwórz**:
   - **Nazwa**: Nazwa musi być unikatowa w [grupie zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) wybranej do utworzenia sieci wirtualnej. Nie można zmienić nazwy po utworzeniu sieci wirtualnej. W czasie można utworzyć wiele sieci wirtualnych. Aby zapoznać się z sugestiami dotyczących nazewnictwa, zobacz [Konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Przestrzeganie konwencji nazewnictwa może ułatwić zarządzanie wieloma sieciami wirtualnymi.
   - **Przestrzeń**adresowa: Przestrzeń adresowa sieci wirtualnej składa się z co najmniej jednego nienakładających się zakresów adresów określonych w notacji CIDR. Zdefiniowany zakres adresów może być publiczny lub prywatny (RFC 1918). Niezależnie od tego, czy zakres adresów jest definiowany jako publiczny czy prywatny, zakres adresów jest osiągalny tylko z poziomu sieci wirtualnej, z połączonych sieci wirtualnych i z wszystkich sieci lokalnych połączonych z siecią wirtualną. Nie można dodać następujących zakresów adresów:
     - 224.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (Transmisja)
     - 127.0.0.0/8 (Sprzężenie zwrotne)
     - 169.254.0.0/16 (Link-local)
     - 168.63.129.16/32 [(sonda kondycji](../load-balancer/load-balancer-custom-probe-overview.md#probesource)Wewnętrzny dns, DHCP i Azure Load Balancer)

     Chociaż podczas tworzenia sieci wirtualnej w portalu można zdefiniować tylko jeden zakres adresów, po utworzeniu sieci wirtualnej można dodać więcej zakresów adresów do przestrzeni adresowej. Aby dowiedzieć się, jak dodać zakres adresów do istniejącej sieci [wirtualnej,](#add-or-remove-an-address-range)zobacz Dodawanie lub usuwanie zakresu adresów .

     >[!WARNING]
     >Jeśli sieć wirtualna ma zakresy adresów, które nakładają się na inną sieć wirtualną lub sieć lokalną, nie można połączyć obu sieci. Przed zdefiniowaniem zakresu adresów należy rozważyć, czy w przyszłości można połączyć sieć wirtualną z innymi sieciami wirtualnymi lub sieciami lokalnymi.
     >
     >

     - **Nazwa podsieci**: Nazwa podsieci musi być unikatowa w sieci wirtualnej. Nie można zmienić nazwy podsieci po utworzeniu podsieci. Portal wymaga zdefiniowania jednej podsieci podczas tworzenia sieci wirtualnej, nawet jeśli sieć wirtualna nie musi mieć żadnych podsieci. W portalu można zdefiniować tylko jedną podsieć podczas tworzenia sieci wirtualnej. Po utworzeniu sieci wirtualnej można dodać więcej podsieci do sieci wirtualnej później. Aby dodać podsieć do sieci wirtualnej, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md). Można utworzyć sieć wirtualną, która ma wiele podsieci przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

       >[!TIP]
       >Czasami administratorzy tworzą różne podsieci, aby filtrować lub kontrolować routing ruchu między podsieciami. Przed zdefiniowaniem podsieci należy wziąć podjęcie zastanów się, jak można filtrować i kierować ruch między podsieciami. Aby dowiedzieć się więcej o filtrowaniu ruchu między podsieciami, zobacz [Sieciowe grupy zabezpieczeń](security-overview.md). Platforma Azure automatycznie kieruje ruch między podsieciami, ale można zastąpić trasy domyślne platformy Azure. Aby dowiedzieć się więcej o domyślnym routingu ruchu podsieci platformy Azure, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
       >

     - **Zakres adresów podsieci:** Zakres musi znajdować się w przestrzeni adresowej wprowadzonej dla sieci wirtualnej. Najmniejszy zakres, który można określić, to /29, który zawiera osiem adresów IP dla podsieci. Platforma Azure rezerwuje pierwszy i ostatni adres w każdej podsieci dla zgodności protokołu. Trzy dodatkowe adresy są zarezerwowane dla użycia usługi platformy Azure. W rezultacie sieć wirtualna z zakresem adresów podsieci /29 ma tylko trzy użyteczne adresy IP. Jeśli planujesz połączyć sieć wirtualną z bramą sieci VPN, musisz utworzyć podsieć bramy. Dowiedz się więcej o [określonych zagadnieniach dotyczących zakresu adresów dla podsieci bramy](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Zakres adresów można zmienić po utworzeniu podsieci w określonych warunkach. Aby dowiedzieć się, jak zmienić zakres adresów podsieci, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).
     - **Subskrypcja**: Wybierz [subskrypcję](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Nie można używać tej samej sieci wirtualnej w więcej niż jednej subskrypcji platformy Azure. Można jednak połączyć sieć wirtualną w jednej subskrypcji z sieciami wirtualnymi w innych subskrypcjach za pomocą [komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md). Każdy zasób platformy Azure, który łączysz się z siecią wirtualną, musi znajdować się w tej samej subskrypcji co sieć wirtualna.
     - **Grupa zasobów**: Wybierz istniejącą [grupę zasobów](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) lub utwórz nową. Zasób platformy Azure, który łączysz się z siecią wirtualną, może znajdować się w tej samej grupie zasobów co sieć wirtualna lub w innej grupie zasobów.
     - **Lokalizacja:** Wybierz [lokalizację](https://azure.microsoft.com/regions/)platformy Azure , znany również jako region. Sieć wirtualna może znajdować się tylko w jednej lokalizacji platformy Azure. Można jednak połączyć sieć wirtualną w jednej lokalizacji z siecią wirtualną w innej lokalizacji przy użyciu bramy sieci VPN. Każdy zasób platformy Azure, który łączysz się z siecią wirtualną, musi znajdować się w tej samej lokalizacji co sieć wirtualna.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [tworzenie sieci wirtualnej az](/cli/azure/network/vnet)
- PowerShell: [Nowa sieci AzVirtual](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Wyświetlanie sieci wirtualnych i ustawień

1. W polu wyszukiwania u góry portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, dla której chcesz wyświetlić ustawienia.
3. Dla wybranej sieci wirtualnej są wyświetlane następujące ustawienia:
   - **Omówienie**: Zawiera informacje o sieci wirtualnej, w tym przestrzeni adresowej i serwerach DNS. Poniższy zrzut ekranu przedstawia ustawienia przeglądu sieci wirtualnej o nazwie **MyVNet**:

     ![Omówienie interfejsu sieciowego](./media/manage-virtual-network/vnet-overview.png)

     Sieć wirtualną można przenieść do innej grupy subskrypcji lub zasobów, wybierając pozycję **Zmień** obok **pozycji Nazwa grupy zasobów** lub **Subskrypcja**. Aby dowiedzieć się, jak przenieść sieć wirtualną, zobacz [Przenoszenie zasobów do innej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W tym artykule wymieniono wymagania wstępne i sposób przenoszenia zasobów przy użyciu witryny Azure portal, powershell i interfejsu wiersza polecenia platformy Azure. Wszystkie zasoby, które są podłączone do sieci wirtualnej musi przenieść z sieci wirtualnej.
   - **Przestrzeń adresowa:** wymienione są przestrzenie adresowe przypisane do sieci wirtualnej. Aby dowiedzieć się, jak dodać i usunąć zakres adresów do przestrzeni adresowej, wykonaj czynności opisane w [aplikacji Dodaj lub usuń zakres adresów](#add-or-remove-an-address-range).
   - **Podłączone urządzenia:** wszystkie zasoby podłączone do sieci wirtualnej są wymienione. Na poprzednim zrzucie ekranu trzy interfejsy sieciowe i jeden moduł równoważenia obciążenia są podłączone do sieci wirtualnej. Zostaną wyświetlone wszystkie nowe zasoby utworzone i łączące się z siecią wirtualną. Jeśli usuniesz zasób, który był połączony z siecią wirtualną, nie będzie już wyświetlany na liście.
   - **Podsieci:** Wyświetlana jest lista podsieci, które istnieją w sieci wirtualnej. Aby dowiedzieć się, jak dodać i usunąć podsieć, zobacz [Zarządzanie podsieciami](virtual-network-manage-subnet.md).
   - **Serwery DNS:** Można określić, czy wewnętrzny serwer DNS platformy Azure lub niestandardowy serwer DNS zapewniają rozpoznawanie nazw dla urządzeń podłączonych do sieci wirtualnej. Podczas tworzenia sieci wirtualnej przy użyciu witryny Azure portal serwery DNS platformy Azure są domyślnie używane do rozpoznawania nazw w sieci wirtualnej. Aby zmodyfikować serwery DNS, wykonaj kroki opisane w [artykule Zmień serwery DNS](#change-dns-servers) w tym artykule.
   - **Komunikacja równorzędna:** Jeśli istnieją istniejące komunikacji równorzędnej w subskrypcji, są one wymienione w tym miejscu. Można wyświetlać ustawienia istniejących komunikacji równorzędnej lub tworzyć, zmieniać lub usuwać komunikacji równorzędnej. Aby dowiedzieć się więcej o komunikacji równorzędnej, zobacz [Komunikacja równorzędna w sieci wirtualnej](virtual-network-peering-overview.md).
   - **Właściwości:** Wyświetla ustawienia dotyczące sieci wirtualnej, w tym identyfikator zasobu sieci wirtualnej i subskrypcję platformy Azure, w jakiej się znajduje.
   - **Diagram**: Diagram zapewnia wizualną reprezentację wszystkich urządzeń podłączonych do sieci wirtualnej. Diagram ma kilka kluczowych informacji o urządzeniach. Aby zarządzać urządzeniem w tym widoku, na diagramie wybierz urządzenie.
   - **Typowe ustawienia platformy Azure**: Aby dowiedzieć się więcej o typowych ustawieniach platformy Azure, zobacz następujące informacje:
     - [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md)
     - [Kontrola dostępu (IAM)](../role-based-access-control/overview.md)
     - [Tagi](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Skrypt usługi Automation](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [wyświetlanie sieci wirtualnej az](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Dodawanie lub usuwanie zakresu adresów

Zakresy adresów dla sieci wirtualnej można dodawać i usuwać. Zakres adresów musi być określony w notacji CIDR i nie może pokrywać się z innymi zakresami adresów w tej samej sieci wirtualnej. Zdefiniowane zakresy adresów mogą być publiczne lub prywatne (RFC 1918). Niezależnie od tego, czy zakres adresów jest definiowany jako publiczny czy prywatny, zakres adresów jest osiągalny tylko z poziomu sieci wirtualnej, z połączonych sieci wirtualnych i z wszystkich sieci lokalnych połączonych z siecią wirtualną. 

Zakres adresów sieci wirtualnej można zmniejszyć, o ile nadal obejmuje zakresy skojarzonych podsieci. Ponadto można rozszerzyć zakres adresów, na przykład zmieniając /16 do /8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Nie można dodać następujących zakresów adresów:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Transmisja)
- 127.0.0.0/8 (Sprzężenie zwrotne)
- 169.254.0.0/16 (Link-local)
- 168.63.129.16/32 [(sonda kondycji](../load-balancer/load-balancer-custom-probe-overview.md#probesource)Wewnętrzny dns, DHCP i Azure Load Balancer)

Aby dodać lub usunąć zakres adresów:

1. W polu wyszukiwania u góry portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, dla której chcesz dodać lub usunąć zakres adresów.
3. Wybierz **miejsce na adres**, w obszarze **USTAWIENIA**.
4. Wykonaj jedną z następujących opcji:
    - **Dodaj zakres adresów**: Wprowadź nowy zakres adresów. Zakres adresów nie może pokrywać się z istniejącym zakresem adresów zdefiniowanym dla sieci wirtualnej.
    - **Usuń zakres adresów**: Po prawej stronie zakresu adresów, który chcesz usunąć, wybierz **...**, a następnie wybierz pozycję **Usuń**. Jeśli podsieć istnieje w zakresie adresów, nie można usunąć zakresu adresów. Aby usunąć zakres adresów, należy najpierw usunąć wszystkie podsieci (i wszystkie zasoby w podsieci), które istnieją w zakresie adresów.
5. Wybierz **pozycję Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacja sieci az w sieci wirtualnej](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Zmienianie serwerów DNS

Wszystkie maszyny wirtualne, które są podłączone do sieci wirtualnej, rejestrują się z serwerami DNS określonymi dla sieci wirtualnej. Używają również określonego serwera DNS do rozpoznawania nazw. Każdy interfejs sieciowy (NIC) na maszynie wirtualnej może mieć własne ustawienia serwera DNS. Jeśli karta sieciowa ma własne ustawienia serwera DNS, zastępuje ustawienia serwera DNS dla sieci wirtualnej. Aby dowiedzieć się więcej o ustawieniach systemu DNS karty sieciowej, zobacz [Zadania i ustawienia interfejsu sieciowego](virtual-network-network-interface.md#change-dns-servers). Aby dowiedzieć się więcej na temat rozpoznawania nazw maszyn wirtualnych i wystąpień ról w usługach Azure Cloud Services, zobacz [Rozpoznawanie nazw maszyn wirtualnych i wystąpień ról.](virtual-networks-name-resolution-for-vms-and-role-instances.md) Aby dodać, zmienić lub usunąć serwer DNS:

1. W polu wyszukiwania u góry portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, dla której chcesz zmienić serwery DNS.
3. Wybierz **serwery DNS**w obszarze **USTAWIENIA**.
4. Wybierz jedną z następujących opcji:
   - **Domyślnie (pod warunkiem platformy Azure)**: Wszystkie nazwy zasobów i prywatne adresy IP są automatycznie rejestrowane na serwerach DNS platformy Azure. Można rozpoznać nazwy między zasobami, które są podłączone do tej samej sieci wirtualnej. Tej opcji nie można używać do rozpoznawania nazw w sieciach wirtualnych. Aby rozpoznawać nazwy w sieciach wirtualnych, należy użyć niestandardowego serwera DNS.
   - **Niestandardowe:** Można dodać jeden lub więcej serwerów, do limitu platformy Azure dla sieci wirtualnej. Aby dowiedzieć się więcej o limitach serwera DNS, zobacz [Limity platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Istnieją następujące opcje:
   - **Dodaj adres:** Dodaje serwer do listy serwerów DNS sieci wirtualnej. Ta opcja rejestruje również serwer DNS na platformie Azure. Jeśli serwer DNS został już zarejestrowany na platformie Azure, możesz wybrać ten serwer DNS na liście.
   - **Usuń adres**: Obok serwera, który chcesz usunąć, wybierz **...**, a następnie **usuń**. Usunięcie serwera powoduje usunięcie serwera tylko z tej listy sieci wirtualnej. Serwer DNS pozostaje zarejestrowany na platformie Azure dla innych sieci wirtualnych do użycia.
   - **Ponowne zamawianie adresów serwera DNS:** Ważne jest, aby sprawdzić, czy serwery DNS są wystawiane we właściwej kolejności dla środowiska. Listy serwerów DNS są używane w określonej kolejności. Nie działają one jako instalacja okrężna. Jeśli można uzyskać kontakt z pierwszym serwerem DNS na liście, klient używa tego serwera DNS, niezależnie od tego, czy serwer DNS działa poprawnie. Usuń wszystkie wymienione serwery DNS, a następnie dodaj je z powrotem w żądanej kolejności.
   - **Zmień adres**: Wyróżnij serwer DNS na liście, a następnie wprowadź nowy adres.
5. Wybierz **pozycję Zapisz**.
6. Uruchom ponownie maszyny wirtualne podłączone do sieci wirtualnej, aby przypisano im nowe ustawienia serwera DNS. Maszyny wirtualne nadal używają swoich bieżących ustawień DNS, dopóki nie zostaną ponownie uruchomione.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacja sieci az w sieci wirtualnej](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Usuwanie sieci wirtualnej

Sieć wirtualną można usunąć tylko wtedy, gdy nie ma z nią żadnych zasobów. Jeśli istnieją zasoby połączone z dowolną podsiecią w sieci wirtualnej, należy najpierw usunąć zasoby, które są połączone ze wszystkimi podsieciami w sieci wirtualnej. Kroki podejmowane w celu usunięcia zasobu różnią się w zależności od zasobu. Aby dowiedzieć się, jak usunąć zasoby połączone z podsieciami, przeczytaj dokumentację dla każdego typu zasobu, który chcesz usunąć. Aby usunąć sieć wirtualną:

1. W polu wyszukiwania u góry portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Z listy sieci wirtualnych wybierz sieć wirtualną, którą chcesz usunąć.
3. Upewnij się, że nie ma urządzeń podłączonych do sieci wirtualnej, wybierając pozycję **Podłączone urządzenia**w obszarze **USTAWIENIA**. Jeśli istnieją podłączone urządzenia, należy je usunąć, aby można było usunąć sieć wirtualną. Jeśli nie ma podłączonych urządzeń, wybierz **opcję Przegląd**.
4. Wybierz pozycję **Usuń**.
5. Aby potwierdzić usunięcie sieci wirtualnej, wybierz opcję **Tak**.

**Polecenia**

- Interfejs wiersza polecenia platformy [Azure: usuwanie sieci azure vnet](/cli/azure/network/vnet)
- PowerShell: [Usuń-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w sieciach wirtualnych, konto musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która jest przypisana odpowiednie akcje wymienione w poniższej tabeli:

| Akcja                                  |   Nazwa                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Czytanie sieci wirtualnej              |
|Microsoft.Network/virtualNetworks/write  |   Tworzenie lub aktualizowanie sieci wirtualnej  |
|Microsoft.Network/virtualNetworks/delete |   Usuwanie sieci wirtualnej            |

## <a name="next-steps"></a>Następne kroki

- Tworzenie sieci wirtualnej przy użyciu przykładowych skryptów interfejsu [wiersza polecenia programu](cli-samples.md) [PowerShell](powershell-samples.md) lub platformy Azure lub przy użyciu [szablonów usługi](template-samples.md) Azure Resource Manager
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
