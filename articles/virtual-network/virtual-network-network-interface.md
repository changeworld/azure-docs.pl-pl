---
title: Tworzenie, zmienianie lub usuwanie interfejsu sieci platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się interfejs sieciowy oraz sposób tworzenia, zmienić ustawienia i usunąć jeden.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: f25840c21ec64ca8d8e9e17eb39637cff7524c76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755246"
---
# <a name="create-change-or-delete-a-network-interface"></a>Tworzenie, zmienianie lub usuwanie interfejsu sieciowego

Dowiedz się, jak tworzenie, zmienianie ustawień i usuwanie interfejsu sieciowego. Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure, nawiązać połączenia z Internetem, Azure i zasobami lokalnymi. Podczas tworzenia maszyny wirtualnej przy użyciu witryny Azure portal, portal tworzy jeden interfejs sieciowy przy użyciu ustawień domyślnych dla Ciebie. Zamiast tego można utworzyć interfejsów sieciowych z ustawieniami niestandardowymi i Dodaj jeden lub więcej interfejsów sieciowych do maszyny wirtualnej, podczas jego tworzenia. Można również zmienić domyślne ustawienia interfejsu sieciowego dla istniejącego interfejsu sieciowego. W tym artykule wyjaśniono, jak utworzyć interfejsu sieciowego z użyciem ustawień niestandardowych, zmienić istniejące ustawienia, takie jak przypisywanie (sieciowej grupy zabezpieczeń) filtru sieci, przypisanie podsieci, ustawienia serwera DNS i przekazywanie adresów IP i usunąć interfejsu sieciowego.

Jeśli potrzebujesz dodać, zmienić, lub usunąć adresy IP dla interfejsu sieciowego, zobacz [adresy IP zarządzania](virtual-network-network-interface-addresses.md). Jeśli trzeba dodać interfejsy sieciowe do lub usuwanie interfejsów sieciowych z maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.com i zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` do utworzenia połączenia z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

## <a name="create-a-network-interface"></a>Utwórz interfejs sieciowy

Podczas tworzenia maszyny wirtualnej przy użyciu witryny Azure portal, w portalu jest tworzony interfejs sieciowy przy użyciu ustawień domyślnych dla Ciebie. Jeśli wolisz określić wszystkie ustawienia interfejsu sieciowego, można utworzyć interfejsu sieciowego z użyciem ustawień niestandardowych i dołączyć interfejsu sieciowego do maszyny wirtualnej, podczas tworzenia maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Można również utworzyć interfejsu sieciowego i dodać go do istniejącej maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Aby dowiedzieć się, jak utworzyć maszynę wirtualną przy użyciu istniejącego interfejsu sieciowego lub dodać do lub usuwanie interfejsów sieciowych z istniejących maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md). Przed utworzeniem interfejsu sieciowego, konieczne jest posiadanie istniejące [sieci wirtualnej](manage-virtual-network.md) w tej samej lokalizacji i subskrypcji można utworzyć interfejsu sieciowego w.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz **+ Dodaj** w obszarze **interfejsy sieciowe**.
3. Wprowadź lub wybierz wartości dla następujących ustawień, a następnie wybierz **Utwórz**:

    |Ustawienie|Wymagana?|Szczegóły|
    |---|---|---|
    |Name (Nazwa)|Tak|Nazwa musi być unikatowa w obrębie grupy zasobów, którą wybierzesz. Wraz z upływem czasu prawdopodobnie masz kilka interfejsów sieciowych w ramach subskrypcji platformy Azure. Podczas tworzenia konwencji nazewnictwa sugestii w zakresie Zarządzanie łatwiejsze kilka interfejsów sieciowych, można zobaczyć [konwencje nazewnictwa](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Nie można zmienić nazwę, po utworzeniu interfejsu sieciowego.|
    |Sieć wirtualna|Yes|Wybierz sieć wirtualną dla interfejsu sieciowego. Interfejs sieciowy można przypisać tylko do sieci wirtualnej, która znajduje się w tej samej subskrypcji i lokalizacji co interfejs sieciowy. Po utworzeniu karty sieciowej nie można zmienić sieci wirtualnej, z którego jest przypisany. Maszynę wirtualną, którą możesz dodać interfejsu sieciowego musi także istnieć w tej samej lokalizacji i subskrypcji co interfejs sieciowy.|
    |Podsieć|Tak|Wybierz podsieć w sieci wirtualnej, który wybrano. Można zmienić podsieci, w której interfejs sieciowy jest przypisany do, po jego utworzeniu.|
    |Przypisanie prywatnego adresu IP|Tak| W tym ustawieniu wybrać metodę przypisywania dla adresu IPv4. Wybierz jedną z następujących metod przypisania: **Dynamiczne:** Po wybraniu tej opcji, Azure automatycznie przypisuje następny dostępny adres z przestrzeni adresowej podsieci, w której wybrano. **Statyczna:** Po wybraniu tej opcji, należy ręcznie przypisać dostępne z adresu IP w przestrzeni adresowej podsieci, w której wybrano. Nie należy zmieniać adresów statycznych i dynamicznych, aż do ich zmiany lub usunięcia interfejsu sieciowego. Można zmienić metodę przypisywania po utworzeniu interfejsu sieciowego. Serwer Azure DHCP przypisuje ten adres z interfejsem sieciowym, w ramach systemu operacyjnego maszyny wirtualnej.|
    |Sieciowa grupa zabezpieczeń|Nie| Pozostaw wartość **Brak**, wybierz istniejącą [sieciowej grupy zabezpieczeń](security-overview.md), lub [Utwórz sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md). Sieciowe grupy zabezpieczeń umożliwiają do filtrowania ruchu sieciowego do i z interfejsem sieciowym. Zero lub jeden sieciowej grupy zabezpieczeń można stosować do interfejsu sieciowego. Zero lub jedna grupa zabezpieczeń sieci można również będą stosowane do podsieci, w której interfejs sieciowy jest przypisany do. Gdy sieciowa grupa zabezpieczeń jest stosowana do karty sieciowej i podsieci, w której interfejs sieciowy jest przypisany do, występuje czasami nieoczekiwane wyniki. Aby rozwiązać problemy sieciowe grupy zabezpieczeń stosowane do interfejsów sieciowych i podsieci, zobacz [Rozwiązywanie problemów z sieciowymi grupami zabezpieczeń](diagnose-network-traffic-filter-problem.md).|
    |Subskrypcja|Tak|Wybierz jedną z subskrypcji platformy Azure [subskrypcje](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Maszyny wirtualnej, dołączenie do interfejsu sieciowego i sieci wirtualnej, z którymi są nawiązywane połączenia muszą istnieć w tej samej subskrypcji.|
    |Prywatny adres IP (IPv6)|Nie| Jeśli zaznaczysz to pole wyboru, adres IPv6 jest przypisany do interfejsu sieciowego, oprócz adres IPv4 przypisany do interfejsu sieciowego. Zobacz sekcję IPv6 w tym artykule, aby uzyskać ważne informacje na temat używania protokołu IPv6 z interfejsami sieciowymi. Nie można wybrać metodę przypisywania adresów IPv6. Jeśli chcesz przypisać adresu IPv6 jest przypisana metoda dynamiczna.
    |Nazwa IPv6 (pojawia się wtedy kiedy **prywatny adres IP (IPv6)** pole wyboru jest zaznaczone) |Tak, jeśli **prywatny adres IP (IPv6)** po zaznaczeniu pola wyboru.| Ta nazwa jest przypisany do pomocniczej konfiguracji adresów IP dla interfejsu sieciowego. Aby dowiedzieć się więcej na temat konfiguracji adresu IP, zobacz [wyświetlić ustawienia interfejsu sieciowego](#view-network-interface-settings).|
    |Grupa zasobów|Tak|Wybierz istniejącą [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) lub utworzyć nowe. Interfejs sieciowy może znajdować się w grupie zasobów w tej samej lub innej niż maszyna wirtualna, które można dołączyć, lub sieć wirtualną można ją połączyć.|
    |Lokalizacja|Yes|Maszyna wirtualna, możesz dołączyć do interfejsu sieciowego i sieci wirtualnej, połącz go musi istnieć w tym samym [lokalizacji](https://azure.microsoft.com/regions), nazywany również region.|

Portal nie udostępnia opcję, aby przypisać publiczny adres IP do interfejsu sieciowego, tworząc, mimo że portalu tworzenie publicznego adresu IP i przypisać je do interfejsu sieciowego podczas tworzenia maszyny wirtualnej przy użyciu portalu. Aby dowiedzieć się, jak dodać publiczny adres IP do interfejsu sieciowego po jej utworzeniu, zobacz [adresy IP zarządzania](virtual-network-network-interface-addresses.md). Jeśli chcesz utworzyć interfejsu sieciowego z publicznym adresem IP, należy użyć interfejsu wiersza polecenia lub programu PowerShell można utworzyć interfejsu sieciowego.

Portal nie udostępnia opcję, aby przypisać interfejs sieciowy do grup zabezpieczeń aplikacji, podczas tworzenia interfejsu sieciowego, ale wiersza polecenia platformy Azure i programu PowerShell. Istniejący interfejs sieciowy można przypisać do grupy zabezpieczeń aplikacji przy użyciu portalu, jednak tak długo, jak interfejs sieciowy jest podłączony do maszyny wirtualnej. Aby dowiedzieć się, jak przypisać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz [dodania lub usunięcia z grupy zabezpieczeń aplikacji](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Platforma Azure przypisuje adres MAC z interfejsem sieciowym, tylko wtedy, gdy interfejs sieciowy jest podłączony do maszyny wirtualnej, a maszyna wirtualna zostanie uruchomiona po raz pierwszy. Nie można określić adres MAC, który platforma Azure przypisuje do interfejsu sieciowego. Adres MAC jest przypisana do interfejsu sieciowego, do momentu usunięcia interfejsu sieciowego lub prywatny adres IP przypisany do podstawowa konfiguracja adresów IP podstawowy interfejs sieciowy zostanie zmieniony. Aby dowiedzieć się więcej na temat adresów IP i konfiguracje adresów IP, zobacz [adresy IP zarządzania](virtual-network-network-interface-addresses.md)

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Wyświetl ustawienia interfejsu sieciowego

Można wyświetlić i zmienić większość ustawień dla interfejsu sieciowego po jego utworzeniu. Portal nie wyświetla DNS sufiksu lub aplikacji przynależności do grupy zabezpieczeń dla interfejsu sieciowego. Możesz użyć programu PowerShell lub wiersza polecenia platformy Azure [polecenia](#view-settings-commands) do wyświetlania DNS sufiksu i aplikacji przynależności do grupy zabezpieczeń.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz wyświetlić lub zmienić ustawienia z listy.
3. Następujące elementy są wyświetlane dla interfejsu sieciowego, wybrane:
   - **Omówienie:** Zawiera informacje dotyczące interfejsu sieciowego, takie jak adresy IP przypisane do jego sieci wirtualnej/podsieci interfejsu sieciowego jest przypisany do i maszyny wirtualnej, do której jest dołączona interfejsu sieciowego (jeśli jest on dołączony do jednego). Na poniższej ilustracji przedstawiono omówienie ustawienia karty sieciowej o nazwie **mywebserver256**: ![Omówienie interfejsu sieciowego](./media/virtual-network-network-interface/nic-overview.png)

     Interfejs sieciowy można przenieść do innej grupy zasobów lub subskrypcji, wybierając (**zmienić**) obok pozycji **grupy zasobów** lub **Nazwa subskrypcji**. Jeśli przenosisz interfejsu sieciowego, należy przenieść wszystkie zasoby związane z interfejsem sieciowym z nim. Jeśli interfejs sieciowy jest podłączony do maszyny wirtualnej, na przykład, należy również przenieść maszynę wirtualną i inne zasoby związane z maszyny wirtualnej. Aby przenieść interfejsu sieciowego, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Artykuł zawiera listę wymagań wstępnych i sposobu przenoszenia zasobów przy użyciu witryny Azure portal, programu PowerShell i wiersza polecenia platformy Azure.
   - **Konfiguracje adresów IP:** Poniżej przedstawiono prywatnych i publicznych adresów IPv4 i IPv6 przypisane do konfiguracji adresu IP. Jeśli adres IPv6 jest przypisany do konfiguracji adresu IP, adres nie jest wyświetlana. Aby dowiedzieć się więcej na temat konfiguracji adresów IP i jak dodawać i usuwać adresy IP, zobacz [adresy IP, konfigurowanie dla interfejsu sieci platformy Azure](virtual-network-network-interface-addresses.md). Przekazywanie dalej adresu IP i przypisanie podsieci również są skonfigurowane w tej sekcji. Aby dowiedzieć się więcej o tych ustawieniach, zobacz [włączać lub wyłączać przesyłanie dalej IP](#enable-or-disable-ip-forwarding) i [zmienić przypisanie podsieci](#change-subnet-assignment).
   - **Serwery DNS:** Można określić, który serwer DNS interfejsu sieciowego jest przypisany przez serwery DHCP platformy Azure. Interfejs sieciowy można dziedziczyć ustawienia z sieci wirtualnej, które interfejs sieciowy jest przypisany do lub mieć ustawienia niestandardowego, który zastępuje ustawienie dla sieci wirtualnej, który jest przypisany do. Aby zmodyfikować, co jest wyświetlane, zobacz [serwerów DNS zmiany](#change-dns-servers).
   - **Sieciowa grupa zabezpieczeń (NSG):** Wyświetla, co sieciowa grupa zabezpieczeń jest skojarzona z interfejsem sieciowym (jeśli istnieje). Sieciowa grupa zabezpieczeń zawiera reguły ruchu przychodzącego i wychodzącego do filtrowania ruchu sieciowego dla interfejsu sieciowego. Jeśli sieciowa grupa zabezpieczeń jest skojarzona z interfejsem sieciowym, jest wyświetlana nazwa skojarzonej sieciowej grupy zabezpieczeń. Aby zmodyfikować, co jest wyświetlane, zobacz [skojarzyć lub usunąć skojarzenie sieciowej grupy zabezpieczeń](#associate-or-dissociate-a-network-security-group).
   - **Właściwości:** Wyświetla ustawienia klucza o interfejsu sieciowego, łącznie z jej adres MAC (puste, jeśli interfejs sieciowy nie jest dołączony do maszyny wirtualnej), a subskrypcja istnieje on w.
   - **Efektywne reguły zabezpieczeń:**  Reguły zabezpieczeń są wyświetlane, jeśli interfejs sieciowy jest podłączony do uruchomionej maszyny wirtualnej i sieciowa grupa zabezpieczeń jest skojarzona z interfejsu sieciowego i/lub podsieci, w której jest przypisany do. Aby dowiedzieć się, co jest wyświetlane, zobacz [wyświetlanie obowiązujących reguł zabezpieczeń](#view-effective-security-rules). Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, zobacz [sieciowe grupy zabezpieczeń](security-overview.md).
   - **Skuteczne trasy:** Trasy są wyświetlane, jeśli interfejs sieciowy jest podłączony do uruchomionej maszyny wirtualnej. Trasy są kombinacją trasy domyślne systemu Azure, wszystkie trasy zdefiniowane przez użytkownika i wszystkie trasy protokołu BGP, które mogą istnieć dla podsieci, w której interfejs sieciowy jest przypisany do. Aby dowiedzieć się, co jest wyświetlane, zobacz [wyświetlić obowiązujące trasy](#view-effective-routes). Aby dowiedzieć się więcej na temat domyślnych tras i trasy zdefiniowane przez użytkownika, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
   - **Typowe ustawienia usługi Azure Resource Manager:**  Aby dowiedzieć się więcej na temat typowych ustawień usługi Azure Resource Manager, zobacz [dziennika aktywności](../azure-monitor/platform/activity-logs-overview.md), [kontrola dostępu (IAM)](../role-based-access-control/overview.md), [tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [blokuje](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i [ Skrypt automatyzacji](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

<a name="view-settings-commands"></a>**Polecenia**

Jeśli adres IPv6 jest przypisany do interfejsu sieciowego, dane wyjściowe programu PowerShell zwraca fakt, że przypisany adres, ale nie zwraca przypisany adres. Podobnie, interfejsu wiersza polecenia zwraca fakt, że adres jest przypisane, ale zwraca *null* w danych wyjściowych dla adresu.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Lista kart sieciowych sieci az](/cli/azure/network/nic) wyświetlić interfejsy sieciowe w ramach subskrypcji; [az sieci nic show](/cli/azure/network/nic) Aby wyświetlić ustawienia interfejsu sieciowego|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) do wyświetlania interfejsów sieciowych w ustawieniach subskrypcji lub widoku dla interfejsu sieciowego|

## <a name="change-dns-servers"></a>Zmień serwerów DNS

Serwer DNS jest przypisany przez serwer DHCP platformy Azure z interfejsem sieciowym, w ramach systemu operacyjnego maszyny wirtualnej. Przypisany serwer DNS jest niezależnie od ustawienia serwera DNS dla karty sieciowej. Aby dowiedzieć się więcej na temat ustawień rozpoznawania nazwy dla interfejsu sieciowego, zobacz [rozpoznawanie nazw dla maszyn wirtualnych](virtual-networks-name-resolution-for-vms-and-role-instances.md). Interfejs sieciowy można dziedziczyć ustawienia z sieci wirtualnej, lub użyć swoje własne unikatowe ustawienia, które zastępują ustawienia dla sieci wirtualnej.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który ma zostać zmieniony na serwerze DNS z listy.
3. Wybierz **serwerów DNS** w obszarze **ustawienia**.
4. Wybierz opcję:
   - **Dziedzicz z sieci wirtualnej**: Wybierz tę opcję, aby dziedziczyć ustawienia serwera DNS, które są zdefiniowane dla sieci wirtualnej, przypisanej do interfejsu sieciowego. Na poziomie sieci wirtualnej niestandardowego serwera DNS lub serwer DNS platformy Azure jest zdefiniowana. Serwer DNS platformy Azure może rozpoznać nazwy hostów dla zasobów przydzielonych do tej samej sieci wirtualnej. Nazwy FQDN musi być używana do rozpoznawania dla zasobów przydzielonych do różnych sieci wirtualnych.
   - **Niestandardowy**: Można skonfigurować własnego serwera DNS do rozpoznawania nazw między wieloma sieciami wirtualnymi. Wprowadź adres IP serwera, który ma być używany jako serwer DNS. Adres serwera DNS, które określisz są przypisane tylko do tego interfejsu sieciowego i zastępuje wszelkie ustawienia DNS dla sieci wirtualnej, przypisanej do interfejsu sieciowego.
     >[!Note]
     >Jeśli maszyna wirtualna używa interfejsu Sieciowego, który jest częścią zestawu dostępności, wszystkie serwery DNS, które są określone dla poszczególnych maszyn wirtualnych na podstawie wszystkich kart sieciowych, które są częścią zestawu dostępności będą dziedziczone.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja interfejsu sieciowego sieci az](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Włączać lub wyłączać przesyłanie dalej IP

Przekazywanie adresów IP umożliwia maszyny wirtualnej, której jest dołączona do interfejsu sieciowego:
- Odbieranie ruchu sieciowego nie są przeznaczone do jednego z adresów IP, przypisany do jednej z konfiguracji adresów IP, przypisany do interfejsu sieciowego.
- Wysyłania ruchu sieciowego przy użyciu adresu IP źródła innego niż ten, który został przypisany do jednej z konfiguracji adresu IP interfejsu sieciowego.

Ustawienie musi być włączona dla każdego interfejsu sieciowego, który jest dołączony do maszyny wirtualnej, która odbiera ruch, że maszyna wirtualna musi przejść do przesyłania dalej. Maszyny wirtualnej może przekazywać ruch, czy ma wiele interfejsów sieciowych lub pojedynczym interfejsem sieciowym podłączone do niego. Przekazywanie dalej adresu IP jest to ustawienie platformy Azure, maszyny wirtualnej należy również uruchomić aplikacji możliwość przesyłania ruchu, takie jak zapora, Optymalizacja sieci WAN i równoważy obciążenia aplikacji. Gdy maszyna wirtualna jest uruchomiona aplikacji sieciowych, maszyna wirtualna często nazywa się sieciowe urządzenie wirtualne. Można wyświetlić listę rozpocząć wdrażanie wirtualnych urządzeń sieciowych w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Przekazywanie dalej adresu IP jest zazwyczaj używany przy użyciu tras zdefiniowanych przez użytkownika. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md).

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz włączyć lub wyłączyć przekazywania dla adresu IP.
3. Wybierz **konfiguracje adresów IP** w **ustawienia** sekcji.
4. Wybierz **włączone** lub **wyłączone** (ustawienie domyślne) Aby zmienić to ustawienie.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja interfejsu sieciowego sieci az](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Zmień przypisanie podsieci

Można zmienić podsieci, ale nie sieci wirtualnej, przypisana do karty sieciowej.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz zmienić przypisania podsieci.
3. Wybierz **konfiguracje adresów IP** w obszarze **ustawienia**. Jeśli na liście żadnych prywatnych adresów IP dla konfiguracji IP **(statyczne)** obok nich, należy zmienić metodę przypisywania adresów IP na dynamiczne, wykonując poniższe kroki. Wszystkie prywatne adresy IP, należy przypisać przy użyciu metody dynamiczne przydzielanie zmienić przypisanie podsieci dla interfejsu sieciowego. Jeśli adresy są przydzielane przy użyciu metody dynamiczne, przejdź do kroku 5. Jeśli żadnych adresów IPv4 przypisanych przy użyciu metody statyczne przypisania, wykonaj następujące kroki, aby zmienić metodę przypisywania do dynamicznego:
   - Wybierz konfigurację adresu IP, aby zmienić metodę przypisywania adresów IPv4 dla z listy konfiguracji adresu IP.
   - Wybierz **dynamiczne** prywatnego adresu IP **przypisania** metody. Nie można przypisać adresu IPv6 przy użyciu metody przypisania statycznego.
   - Wybierz pozycję **Zapisz**.
4. Wybierz podsieć, którą chcesz przenieść do interfejsu sieciowego, od **podsieci** listy rozwijanej.
5. Wybierz pozycję **Zapisz**. Nowe adresy dynamiczne są przypisywane z zakresu adresów podsieci dla nowej podsieci. Po przypisaniu interfejsu sieciowego do nowej podsieci, można przypisać statyczny adres IPv4 w nowy zakres adresów podsieci, jeśli wybierzesz. Aby dowiedzieć się więcej o dodawanie, zmienianie i usuwanie adresów IP dla interfejsu sieciowego, zobacz [adresy IP zarządzania](virtual-network-network-interface-addresses.md).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[AZ sieci nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Dodawanie do lub usuwanie z grup zabezpieczeń aplikacji

Możesz tylko dodawać do interfejsu sieciowego lub usuwania karty sieciowej grupy zabezpieczeń aplikacji przy użyciu portalu, jeśli interfejs sieciowy jest podłączony do maszyny wirtualnej. Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby dodać do interfejsu sieciowego lub usuwania karty sieciowej grupy zabezpieczeń aplikacji, czy interfejs sieciowy jest podłączony do maszyny wirtualnej, czy nie. Dowiedz się więcej o [grupy zabezpieczeń aplikacji](security-overview.md#application-security-groups) oraz sposób [Tworzenie grupy zabezpieczeń aplikacji](manage-network-security-group.md).

1. W *Szukaj zasobów, usług i dokumentów* w górnej części portalu, zacznij pisać nazwę maszyny wirtualnej, która ma interfejs sieciowy, który chcesz dodać do lub usuwanie z grupy zabezpieczeń aplikacji. Gdy nazwa maszyny Wirtualnej pojawi się w wynikach wyszukiwania, wybierz ją.
2. W obszarze **USTAWIENIA** wybierz pozycję **Sieć**.  Wybierz **. Konfigurowanie grup zabezpieczeń aplikacji**, wybierz grup zabezpieczeń aplikacji, które chcesz dodać interfejsu sieciowego lub usuń zaznaczenie grupy zabezpieczeń aplikacji, które chcesz usunąć interfejs sieciowy, a następnie wybierz **Zapisz**. Tylko te interfejsy sieciowe, które istnieją w tej samej sieci wirtualnej można dodać do tej samej grupy zabezpieczeń aplikacji. Grupy zabezpieczeń aplikacji muszą istnieć w tej samej lokalizacji co interfejs sieciowy.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja interfejsu sieciowego sieci az](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Skojarzyć lub usunąć skojarzenie sieciowej grupy zabezpieczeń

1. W polu wyszukiwania w górnej części portalu wprowadź *interfejsy sieciowe* w polu wyszukiwania. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy na liście, które chcesz skojarzyć sieciową grupę zabezpieczeń do lub usuwanie skojarzenia sieciowej grupy zabezpieczeń z.
3. Wybierz **sieciowej grupy zabezpieczeń** w obszarze **ustawienia**.
4. Wybierz pozycję **Edit** (Edytuj).
5. Wybierz **sieciowej grupy zabezpieczeń** a następnie wybierz grupę zabezpieczeń sieci chcesz skojarzyć z interfejsem sieciowym, lub wybierz **Brak**, aby usunąć skojarzenie sieciowej grupy zabezpieczeń.
6. Wybierz pozycję **Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az sieci nic update](/cli/azure/network/nic#az-network-nic-update)
- Program PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Usuwanie interfejsu sieciowego

Tak długo, jak nie jest dołączony do maszyny wirtualnej, można usunąć interfejsu sieciowego. Jeśli interfejs sieciowy jest podłączony do maszyny wirtualnej, należy najpierw umieścić maszyny wirtualnej w stanie zatrzymania (przydział zostanie cofnięty), a następnie odłączyć interfejsu sieciowego z maszyny wirtualnej. Aby odłączyć interfejsu sieciowego z maszyny wirtualnej, wykonaj kroki [odłączanie interfejsu sieciowego z maszyną wirtualną](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Nie można odłączyć interfejsu sieciowego z maszyny wirtualnej, jeśli jest tylko interfejs sieciowy dołączony do maszyny wirtualnej, jednak. Maszyna wirtualna zawsze musi mieć co najmniej jeden interfejs sieciowy dołączony do niego. Usuwanie maszyny wirtualnej Odłącza wszystkie interfejsy sieciowe podłączone do niego, ale nie powoduje usunięcia interfejsów sieciowych.

1. W polu zawierającym tekst *Wyszukaj zasoby* w górnej części witryny Azure portal, wpisz polecenie *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz **...**  po prawej stronie interfejsu sieciowego, aby usunąć z listy interfejsów sieciowych.
3. Wybierz pozycję **Usuń**.
4. Wybierz **tak** o potwierdzenie usunięcia interfejsu sieciowego.

Podczas usuwania interfejsu sieciowego, wszystkie adresy MAC lub adres IP przypisany do niego są zwalniane.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Usuń kartę sieciową sieci az](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Jeśli nie można nawiązać komunikacji z maszyną wirtualną, reguły zabezpieczeń sieciowej grupy zabezpieczeń lub tras dla interfejsu sieciowego, może być przyczyną problemu. Masz następujące opcje, aby pomóc w rozwiązaniu problemu:

### <a name="view-effective-security-rules"></a>Wyświetlanie obowiązujących reguł zabezpieczeń

Efektywne reguły zabezpieczeń dla każdego interfejsu sieciowego dołączonych do maszyny wirtualnej są kombinacją reguł został utworzony w sieciowej grupie zabezpieczeń a [domyślnych regułach zabezpieczeń](security-overview.md#default-security-rules). Informacje o obowiązujących reguł zabezpieczeń dla interfejsu sieciowego może pomóc w określeniu, dlaczego nie możesz nawiązać połączenia z maszyną wirtualną lub. Możesz wyświetlić aktywne reguły dla dowolnego interfejsu sieciowego, który jest dołączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, którą chcesz wyświetlić obowiązujące reguły zabezpieczeń dla. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyn wirtualnych* w polu wyszukiwania. Gdy **maszyn wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **sieć** w obszarze **ustawienia**.
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz **obowiązujących reguł zabezpieczeń** w obszarze **pomoc techniczna i rozwiązywanie problemów**.
5. Przejrzyj listę reguł efektywnym elementem systemu zabezpieczeń, aby określić, jeśli poprawne reguły znajdują się na wymaganą komunikację ruchu przychodzącego i wychodzącego. Dowiedz się więcej o tym, co widać na liście w [omówienie grupy zabezpieczeń sieci](security-overview.md).

Przepływ IP Sprawdź, czy funkcja usługi Azure Network Watcher ułatwiają także określić, jeśli reguły zabezpieczeń są uniemożliwia komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [weryfikowanie przepływu protokołu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az network nic list — zacznie obowiązywać od — sieciowej grupy zabezpieczeń](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- Program PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Wyświetlanie obowiązujących tras

Skuteczne trasy dla interfejsów sieciowych dołączonych do maszyny wirtualnej są kombinacją trasy domyślne, wszystkie trasy, który został utworzony i wszystkie trasy propagowane z sieciami lokalnymi za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Opis obowiązujące trasy dla interfejsu sieciowego może pomóc w określeniu, dlaczego nie możesz nawiązać połączenia z maszyną wirtualną lub. Można wyświetlić obowiązujące trasy dla dowolnego interfejsu sieciowego, który jest dołączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, którą chcesz wyświetlić obowiązujące reguły zabezpieczeń dla. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyn wirtualnych* w polu wyszukiwania. Gdy **maszyn wirtualnych** są wyświetlane w wynikach wyszukiwania, wybierz ją, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **sieć** w obszarze **ustawienia**.
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz **obowiązujące trasy** w obszarze **pomoc techniczna i rozwiązywanie problemów**.
5. Przejrzyj listę obowiązujące trasy, aby stwierdzić, jeśli poprawne tras dla komunikacji wymagane dla ruchu przychodzącego i wychodzącego. Dowiedz się więcej o tym, co widać na liście w [Omówienie routingu](virtual-networks-udr-overview.md).

Funkcja następnego przeskoku usługi Azure Network Watcher może również pomóc w określeniu, jeśli trasy nie uniemożliwiają komunikacji między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [następnego przeskoku](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [az network nic show obowiązywać route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- Program PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań w interfejsach sieciowych, Twoje konto musi mieć przypisaną do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) roli, którą przypisano odpowiednie uprawnienia wymienione w poniższej tabeli:

| Akcja                                                                     | Name (Nazwa)                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Pobierz interfejs sieciowy                                     |
| Microsoft.Network/networkInterfaces/write                                  | Utwórz lub zaktualizuj interfejs sieciowy                        |
| Microsoft.Network/networkInterfaces/join/action                            | Dołączanie interfejsu sieciowego do maszyny wirtualnej           |
| Microsoft.Network/networkInterfaces/delete                                 | Usuwanie interfejsu sieciowego                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Dołącz zasób do interfejsu sieciowego za pomocą dodatku servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Pobierz tabelę skuteczną trasę interfejsu sieciowego               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Pobieranie grup zabezpieczeń obowiązujące interfejsu sieciowego           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Pobierz moduły równoważenia obciążenia interfejsu sieciowego                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Uzyskaj skojarzenie usługi                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Utwórz lub zaktualizuj skojarzenie usługi                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Usuń skojarzenie usługi                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Sprawdź poprawność skojarzenie usługi                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Pobierz konfigurację adresu IP interfejsu sieciowego                    |

## <a name="next-steps"></a>Kolejne kroki

- Tworzenie maszyny Wirtualnej z wieloma kartami sieciowymi przy użyciu [wiersza polecenia platformy Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [programu PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie pojedynczej maszyny Wirtualnej karty Sieciowej, za pomocą wielu IPv4 adresy przy użyciu [wiersza polecenia platformy Azure](virtual-network-multiple-ip-addresses-cli.md) lub [programu PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Tworzenie pojedynczej maszyny Wirtualnej karty Sieciowej za pomocą prywatnego adresu (znajdującego się za usługą Azure Load Balancer) protokołu IPv6, za pomocą [wiersza polecenia platformy Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), lub [szablonu usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie interfejsu sieciowego przy użyciu [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonu usługi Resource Manager](template-samples.md)
- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych
