---
title: Tworzenie, zmienianie lub usuwanie interfejsu sieci platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się interfejs sieciowy oraz sposobu tworzenia, zmienić ustawienia i usunąć jeden.
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
ms.date: 07/24/2017
ms.author: jdial
ms.openlocfilehash: a6e3bb31886f1b682ef20404b536bfc4a0c07151
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656887"
---
# <a name="create-change-or-delete-a-network-interface"></a>Tworzenie, zmienianie lub usuwanie interfejsu sieciowego

Dowiedz się, jak utworzyć, Zmień ustawienia i usunąć interfejsu sieciowego. Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure do komunikowania się z Internetem, Azure i lokalnymi zasobami. Podczas tworzenia maszyny wirtualnej przy użyciu portalu Azure, portal tworzy jeden interfejs sieciowy z ustawieniami domyślnymi dla Ciebie. Można zamiast tego do tworzenia interfejsów sieciowych z użyciem ustawień niestandardowych i Dodaj jeden lub więcej interfejsów sieciowych do maszyny wirtualnej, po jego utworzeniu. Można również zmienić domyślne ustawienia interfejsu sieciowego dla istniejącego interfejsu sieciowego. W tym artykule wyjaśniono, jak utworzyć niestandardowe ustawienia interfejsu sieciowego, zmień istniejące ustawienia, takie jak przypisywanie (sieciowej grupy zabezpieczeń) filtru sieci, przypisanie podsieci, ustawienia serwera DNS i przesyłanie dalej IP i usunąć interfejsu sieciowego.

Aby dodać, zmienić, lub usunąć adresy IP dla interfejsu sieciowego, zobacz [adresów IP zarządzanie](virtual-network-network-interface-addresses.md). Jeśli konieczne jest dodanie interfejsów sieciowych do lub usuwanie interfejsów sieciowych z maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta platformy Azure.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.4.1 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` można utworzyć połączenia z platformą Azure.

Konta, zaloguj się do lub z usługą Azure, musi być przypisany do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowej roli zabezpieczeń](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania na liście [uprawnień ](#permissions).

## <a name="create-a-network-interface"></a>Tworzenie interfejsu sieciowego

Podczas tworzenia maszyny wirtualnej przy użyciu portalu Azure, portal tworzy interfejs sieciowy z ustawieniami domyślnymi dla Ciebie. Jeśli wolisz określić wszystkie ustawienia interfejsu sieciowego, można utworzyć niestandardowe ustawienia interfejsu sieciowego i dołączyć interfejsu sieciowego z maszyną wirtualną, podczas tworzenia maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure). Można również utworzyć interfejsu sieciowego i dodać go do istniejącej maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia Azure). Aby dowiedzieć się, jak utworzyć maszynę wirtualną z istniejącego interfejsu sieciowego lub Dodaj do lub usuwanie interfejsów sieciowych z istniejących maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md). Przed utworzeniem karty sieciowej, musisz mieć istniejące [sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network) w tej samej subskrypcji i lokalizacji tworzenia interfejsu sieciowego w.

1. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz **+ Dodaj** w obszarze **interfejsy sieciowe**.
3. Wprowadź, lub wybierz wartości poniższych ustawień, a następnie wybierz **Utwórz**:

    |Ustawienie|Wymagana?|Szczegóły|
    |---|---|---|
    |Name (Nazwa)|Yes|Nazwa musi być unikatowa w ramach grupy zasobów, którą wybierzesz. Wraz z upływem czasu najprawdopodobniej będziesz mieć kilka interfejsów sieciowych w ramach subskrypcji platformy Azure. Wskazówki dotyczące zasobów podczas tworzenia konwencji nazewnictwa do kilku interfejsów sieciowych łatwiejsze zarządzanie, zobacz [konwencje nazewnictwa](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Nie można zmienić nazwę, po utworzeniu interfejsu sieciowego.|
    |Sieć wirtualna|Yes|Wybierz sieć wirtualną dla interfejsu sieciowego. Interfejs sieciowy można przypisać tylko do sieci wirtualnej, który istnieje w tej samej subskrypcji i lokalizacji co interfejsu sieciowego. Po utworzeniu karty sieciowej nie można zmienić sieci wirtualnej, który jest przypisany do. Dodaj interfejs sieciowy do maszyny wirtualnej muszą także istnieć w tej samej lokalizacji, a subskrypcja jako interfejs sieciowy.|
    |Podsieć|Yes|Wybierz podsieć w wybranej sieci wirtualnej. Można zmienić podsieci, w której interfejsu sieciowego jest przypisany do po jego utworzeniu.|
    |Przypisanie prywatnego adresu IP|Yes| W tym ustawieniu, czy wybierana metoda przydziału dla adresu IPv4. Wybierz jedną z następujących metod przypisania: **dynamicznej:** po wybraniu tej opcji Azure automatycznie przypisuje następnego dostępnego adresu z przestrzeni adresowej podsieci wybrane. **Statyczne:** po wybraniu tej opcji, należy ręcznie przypisać dostępne z adresu IP w przestrzeni adresowej podsieci wybrane. Nie należy zmieniać adresów statycznych i dynamicznych, aż do ich zmiany lub interfejsu sieciowego są usuwane. Metoda przydziału można zmienić po utworzeniu interfejsu sieciowego. Serwer Azure DHCP przypisuje ten adres interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej.|
    |Sieciowa grupa zabezpieczeń|Nie| Pozostaw ustawioną **Brak**, wybierz istniejący [sieciowej grupy zabezpieczeń](security-overview.md), lub [Utwórz grupę zabezpieczeń sieci](tutorial-filter-network-traffic.md). Grupy zabezpieczeń sieci włączyć do filtrowania ruchu sieciowego do i z karty sieciowej. Zero lub jeden sieciowej grupy zabezpieczeń można stosować do interfejsu sieciowego. Zero lub jedną grupę zabezpieczeń sieci można również będą stosowane do podsieci, w której przydzielono interfejsu sieciowego. Po zastosowaniu do karty sieciowej i podsieci, w której interfejsu sieciowego jest przypisany do grupy zabezpieczeń sieci wystąpić czasami nieoczekiwane wyniki. Aby rozwiązać grup zabezpieczeń sieci dotyczą interfejsy sieciowe i podsieci, zobacz [Rozwiązywanie problemów z grup zabezpieczeń sieci](diagnose-network-traffic-filter-problem.md).|
    |Subskrypcja|Yes|Wybierz jedną z platformy Azure [subskrypcje](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Maszyny wirtualnej, dołączenie do interfejsu sieciowego i sieci wirtualnej, gdy nawiązujesz połączenie, musi istnieć w tej samej subskrypcji.|
    |Prywatny adres IP (IPv6)|Nie| Jeśli zaznaczysz to pole wyboru, adres IPv6 jest przypisany do interfejsu sieciowego, oprócz adres IPv4 przypisany do interfejsu sieciowego. Zobacz [IPv6](#IPv6) sekcji tego artykułu, aby uzyskać ważne informacje na temat używania protokołu IPv6 z interfejsami sieciowymi. Nie można wybrać metodę przypisania dla adresu IPv6. Jeśli chcesz przypisać adres IPv6 jest przypisany za pomocą metody dynamicznej.
    |Nazwa IPv6 (tylko jest wyświetlane, gdy **prywatny adres IP (IPv6)** jest zaznaczone pole wyboru) |Tak, jeśli **prywatny adres IP (IPv6)** jest zaznaczone pole wyboru.| Ta nazwa jest przypisany do dodatkowej konfiguracji IP interfejsu sieciowego. Aby dowiedzieć się więcej na temat konfiguracji adresów IP, zobacz [wyświetlić ustawienia interfejsu sieciowego](#view-network-interface-settings).|
    |Grupa zasobów|Yes|Wybierz istniejący [grupy zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) lub utwórz taki pakiet. Interfejs sieciowy może istnieć w grupie zasobów tego samego lub innego niż maszynę wirtualną, którą należy dołączyć, lub sieci wirtualnej można je połączyć.|
    |Lokalizacja|Yes|Maszyny wirtualnej, możesz dołączyć do interfejsu sieciowego i połącz go z sieci wirtualnej, musi istnieć w tym samym [lokalizacji](https://azure.microsoft.com/regions), nazywany również regionu.|

Portalu nie zapewnia możliwość przypisania publicznego adresu IP do interfejsu sieciowego, podczas tworzenia, mimo że portalu tworzenie publicznego adresu IP i przypisz je do karty sieciowej, podczas tworzenia maszyny wirtualnej za pomocą portalu. Aby dowiedzieć się, jak dodać publicznego adresu IP do interfejsu sieciowego po jego utworzeniu, zobacz [adresów IP zarządzanie](virtual-network-network-interface-addresses.md). Jeśli chcesz utworzyć interfejsu sieciowego z publicznym adresem IP, musi użyć interfejsu wiersza polecenia lub programu PowerShell do tworzenia interfejsu sieciowego.

Portalu nie zapewnia możliwość przypisania interfejsu sieciowego dla grup zabezpieczeń aplikacji, ale nie wiersza polecenia platformy Azure i programu PowerShell. Aby dowiedzieć się więcej na temat grup zabezpieczeń aplikacji, zobacz [grup zabezpieczeń aplikacji](security-overview.md#application-security-groups).

>[!Note]
> Azure przypisuje adres MAC do interfejsu sieciowego tylko wtedy, gdy interfejs sieciowy jest dołączony do maszyny wirtualnej i uruchomieniu maszyny wirtualnej po raz pierwszy. Nie można określić adres MAC, który przypisuje Azure do interfejsu sieciowego. Adres MAC jest przypisana do interfejsu sieciowego, dopóki interfejs sieciowy została usunięta lub prywatnego adresu IP przypisanego do podstawowej konfiguracji IP podstawowy interfejs sieciowy zostanie zmieniona. Aby dowiedzieć się więcej na temat adresów IP i konfiguracje adresów IP, zobacz [adresów IP zarządzania](virtual-network-network-interface-addresses.md)

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic#az_network_nic_create)|
|PowerShell|[New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface#create)|

## <a name="view-network-interface-settings"></a>Wyświetl ustawienia interfejsu sieciowego

Możesz wyświetlić i zmienić większość ustawień interfejsu sieciowego po jego utworzeniu. Portalu nie są wyświetlane DNS sufiks lub aplikacji przynależności do grupy zabezpieczeń dla interfejsu sieciowego. Możesz użyć programu PowerShell lub interfejsu wiersza polecenia Azure [polecenia](#view-settings-commands) Aby wyświetlić DNS sufiks i aplikacji przynależności do grupy zabezpieczeń.

1. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz wyświetlić lub zmienić ustawienia z listy.
3. Następujące elementy są wyświetlane dla wybranego interfejsu sieciowego:
    - **Omówienie:** informacje na temat interfejsu sieciowego, takie jak adresy IP przypisane go, wirtualne sieci/podsieci interfejsu sieciowego jest przypisany do oraz interfejs sieciowy jest dołączony do (jeśli jest on dołączony do maszyny wirtualnej jeden). Na poniższej ilustracji przedstawiono omówienie ustawień karty sieciowej o nazwie **mywebserver256**: ![omówienie interfejsu sieciowego](./media/virtual-network-network-interface/nic-overview.png) interfejsu sieciowego można przenieść do innej grupie zasobów lub Subskrypcja, wybierając (**zmienić**) obok pozycji **grupy zasobów** lub **Nazwa subskrypcji**. Jeśli przenosisz interfejsu sieciowego, należy przenieść wszystkie zasoby związane z interfejsu sieciowego z nim. Jeśli interfejs sieciowy jest dołączony do maszyny wirtualnej, na przykład, musisz również przenieść maszynę wirtualną i innych zasobów związanych z maszyny wirtualnej. Aby przenieść interfejsu sieciowego, zobacz [przenieść zasobu do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Artykuł zawiera listę wymagań wstępnych i sposobu przenoszenia zasobów za pomocą portalu Azure, programu PowerShell i interfejsu wiersza polecenia Azure.
    - **Konfiguracje adresów IP:** prywatnych i publicznych adresów IPv4 i IPv6 przypisany do konfiguracji adresów IP są wyświetlane tutaj. Jeśli adres IPv6 jest przypisany do konfiguracji adresu IP, adres nie jest wyświetlana. Aby dowiedzieć się więcej na temat konfiguracji IP i dodawanie i usuwanie adresów IP, zobacz [adresy IP, skonfiguruj dla interfejsu sieci platformy Azure](virtual-network-network-interface-addresses.md). Przesyłanie dalej IP i przypisanie podsieci są również skonfigurowane w tej sekcji. Aby dowiedzieć się więcej o tych ustawieniach, zobacz [włączać lub wyłączać przesyłanie dalej IP](#enable-or-disable-ip-forwarding) i [zmienić przypisanie podsieci](#change-subnet-assignment).
    - **Serwery DNS:** można określić, który serwer DNS interfejsu sieciowego jest przypisany przez serwery Azure DHCP. Interfejs sieciowy można dziedziczy ustawienia sieci wirtualnej, który interfejsu sieciowego jest przypisany do lub mieć ustawienia niestandardowe, zastępuje ustawienie dla sieci wirtualnej, który jest przypisany do. Aby zmodyfikować, co jest wyświetlane, zobacz [serwerów DNS zmiany](#change-dns-servers).
    - **Grupy zabezpieczeń sieci (NSG):** Wyświetla co grupa NSG jest skojarzona z interfejsu sieciowego (jeśli istnieje). Grupy NSG zawiera reguły ruchu przychodzącego i wychodzącego do filtrowania ruchu sieciowego dla interfejsu sieciowego. Jeśli grupa NSG jest skojarzona z interfejsu sieciowego, jest wyświetlana nazwa skojarzone NSG. Aby zmodyfikować, co jest wyświetlane, zobacz [skojarzyć lub usunąć skojarzenie grupy zabezpieczeń sieci](#associate-or-dissociate-a-network-security-group).
    - **Właściwości:** Wyświetla klucz Ustawienia dotyczące interfejsu sieciowego, łącznie z jej adres MAC (pusty w przypadku interfejsu sieciowego nie jest dołączony do maszyny wirtualnej), a subskrypcja istnieje on w.
    - **Reguły efektywnym elementem systemu zabezpieczeń:** reguły zabezpieczeń są wyświetlane, jeśli interfejs sieciowy jest dołączony do uruchomionej maszyny wirtualnej, a grupa NSG jest skojarzona z interfejsu sieciowego i/lub jest przypisany do podsieci. Aby dowiedzieć się więcej o to, co jest wyświetlane, zobacz [wyświetlić reguły efektywnym elementem systemu zabezpieczeń](#view-effective-security-rules). Aby dowiedzieć się więcej na temat grup NSG, zobacz [sieciowej grupy zabezpieczeń](security-overview.md).
    - **Skuteczne tras:** wymienione są trasy, jeśli interfejs sieciowy jest dołączony do uruchomionej maszyny wirtualnej. Trasy są kombinacją trasy domyślne Azure, wszelkie trasy zdefiniowane przez użytkownika i żadnych trasy protokołu BGP, które mogą wystąpić dla podsieci, w której interfejsu sieciowego jest przypisany do. Aby dowiedzieć się więcej o to, co jest wyświetlane, zobacz [wyświetlić trasy skuteczne](#view-effective-routes). Aby dowiedzieć się więcej na temat trasy domyślne Azure i trasy zdefiniowane przez użytkownika, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
    - **Typowe ustawienia usługi Azure Resource Manager:** Aby dowiedzieć się więcej na temat typowych ustawień usługi Azure Resource Manager, zobacz [dziennik aktywności](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#activity-logs), [(IAM) kontroli dostępu](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#access-control), [tagi](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Blokuje](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), i [skryptu automatyzacji](../azure-resource-manager/resource-manager-export-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json#export-the-template-from-resource-group).

<a name="view-settings-commands"></a>**Polecenia**

Jeśli adres IPv6 jest przypisany do interfejsu sieciowego, dane wyjściowe programu PowerShell zwraca fakt, że adres, ale nie zwraca przypisany adres. Podobnie, interfejsu wiersza polecenia zwraca fakt, że ten adres jest przypisane, ale zwraca *null* w danych wyjściowych dla adresu.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Lista kart sieci az](/cli/azure/network/nic#az_network_nic_list) Aby wyświetlić interfejsów sieciowych w subskrypcji; [Pokaż kart sieciowych az](/cli/azure/network/nic#az_network_nic_show) Aby wyświetlić ustawienia interfejsu sieciowego|
|PowerShell|[Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface) Aby wyświetlić interfejsów sieciowych w subskrypcji lub Wyświetl ustawienia interfejsu sieciowego|

## <a name="change-dns-servers"></a>Zmień serwerów DNS

Serwer DNS jest przypisany przez serwer Azure DHCP do interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. Serwer DNS przypisany jest niezależnie od ustawienia serwera DNS dla karty sieciowej. Aby dowiedzieć się więcej na temat ustawień rozpoznawania nazwy dla interfejsu sieciowego, zobacz [rozpoznawanie nazw dla maszyn wirtualnych](virtual-networks-name-resolution-for-vms-and-role-instances.md). Interfejs sieciowy może dziedziczyć ustawień sieci wirtualnej lub użyj własne ustawienia unikatowe, które zastępują ustawienia dla sieci wirtualnej.

1. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który ma zostać zmieniony na serwerze DNS z listy.
3. Wybierz **serwerów DNS** w obszarze **ustawienia**.
4. Wybierz opcję:
    - **Dziedzicz sieci wirtualnej**: Wybierz tę opcję, aby odziedziczyć ustawienia serwera DNS, które są zdefiniowane dla interfejsu sieciowego jest przypisany do sieci wirtualnej. Na poziomie sieci wirtualnej jest zdefiniowany niestandardowy serwer DNS lub serwer DNS platformy Azure. Serwer DNS platformy Azure można rozpoznać nazwy hostów dla zasobów przypisanych do tej samej sieci wirtualnej. Nazwa FQDN musi być używany do rozpoznania zasobów przydzielonych do różnych sieciach wirtualnych.
    - **Niestandardowe**: można skonfigurować własny serwer DNS do rozpoznawania nazw między wieloma sieciami wirtualnymi. Wprowadź adres IP serwera, który ma być używany jako serwer DNS. Adres serwera DNS, które określisz jest przypisane tylko do tego interfejsu sieciowego i zastępuje wszelkie ustawienia DNS dla sieci wirtualnej przydzielonej do interfejsu sieciowego.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja kart sieciowych az](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Włączać lub wyłączać przesyłanie dalej IP

Przesyłanie dalej IP umożliwia interfejs sieciowy jest dołączony do maszyny wirtualnej:
- Odbieranie ruchu sieciowego, które nie są przeznaczone do jednego z adresów IP przypisany do żadnej konfiguracji adresów IP przypisanych do interfejsu sieciowego.
- Wyślij ruch sieciowy za pomocą adresu IP źródła innego niż ten, który został przypisany do jednej konfiguracji IP interfejsu sieciowego.

Ustawienie musi być włączony dla każdego interfejsu sieciowego, który jest dołączony do maszyny wirtualnej, która odbiera ruch, który musi maszyny wirtualnej do przesyłania dalej. Maszyny wirtualnej może przekazywać ruch, czy ma ona wiele interfejsów sieciowych lub do niego dołączony jednym interfejsem sieciowym. Przesyłanie dalej IP jest ustawienie platformy Azure, maszyny wirtualnej muszą także uruchamiania aplikacji możliwość przesyłania ruchu, takie jak zapora, Optymalizacja sieci WAN i aplikacje równoważenia obciążenia. Po uruchomieniu aplikacji sieciowych maszyny wirtualnej maszyny wirtualnej jest często określany jako urządzenie wirtualne sieci. Można wyświetlić listę gotowe do wdrożenia sieci wirtualnych urządzeń w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Przesyłanie dalej IP jest zwykle używany z tras zdefiniowanych przez użytkownika. Aby dowiedzieć się więcej na temat trasy zdefiniowane przez użytkownika, zobacz [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md).

1. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz włączyć lub wyłączyć przekazywanie IP.
3. Wybierz **konfiguracje adresów IP** w **ustawienia** sekcji.
4. Wybierz **włączone** lub **wyłączone** (ustawienie domyślne) Aby zmienić to ustawienie.
5. Wybierz pozycję **Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja kart sieciowych az](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="change-subnet-assignment"></a>Zmień przypisanie podsieci

Można zmienić podsieci, ale nie sieci wirtualnej, przypisane do karty sieciowej.

1. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz interfejs sieciowy, który chcesz zmienić przypisania podsieci.
3. Wybierz **konfiguracje adresów IP** w obszarze **ustawienia**. Jeśli prywatne adresy IP dla konfiguracji IP wyświetlane **(statyczny)** obok nich, należy zmienić metoda przypisywania adresów IP na dynamiczny, wykonując kroki, które należy wykonać. Wszystkich prywatnych adresów IP musi być przypisany za pomocą metody dynamiczne przydzielanie, aby zmienić przypisanie podsieci interfejsu sieciowego. Jeśli adresy są przypisane przy użyciu metody dynamicznej, przejdź do kroku 5. Adresy IPv4, są przypisane przy użyciu metody statyczne przypisania, należy wykonać następujące kroki, aby zmienić metodę przypisania do dynamicznego:
    - Wybierz konfigurację IP chcesz zmienić metody przypisywania adresów IPv4 dla z listy konfiguracje adresów IP.
    - Wybierz **dynamiczne** prywatnego adresu IP **przypisania** metody. Nie można przypisać adresów IPv6 za pomocą metody statyczne przypisania.
    - Wybierz pozycję **Zapisz**.
4. Wybierz podsieć, aby przenieść interfejsu sieciowego z **podsieci** listy rozwijanej.
5. Wybierz pozycję **Zapisz**. Nowe dynamiczne adresy przypisywane są zakres adresów podsieci dla nowej podsieci. Po przypisaniu interfejsu sieciowego do nowych podsieci, można przypisać statycznego adresu IPv4 z nowy zakres adresów podsieci po wybraniu. Aby dowiedzieć się więcej o dodawanie, zmienianie i usuwanie adresów IP dla interfejsu sieciowego, zobacz [adresów IP zarządzanie](virtual-network-network-interface-addresses.md).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja konfiguracji adresu ip karty sieciowej sieci az](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update)|
|PowerShell|[Set-AzureRmNetworkInterfaceIpConfig](/powershell/module/azurerm.network/set-azurermnetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Dodawanie do lub usuwanie z grup zabezpieczeń aplikacji

Portalu nie podaj opcję, aby przypisać do interfejsu sieciowego, lub Usuń interfejs sieciowy z grup zabezpieczeń aplikacji, ale czy wiersza polecenia platformy Azure i programu PowerShell. Aby dowiedzieć się więcej na temat grup zabezpieczeń aplikacji, zobacz [grup zabezpieczeń aplikacji](security-overview.md#application-security-groups) i [Tworzenie grupy zabezpieczeń aplikacji](#create-an-application-security-group).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Aktualizacja kart sieciowych az](/cli/azure/network/nic#az_network_nic_update)|
|PowerShell|[Set-AzureRmNetworkInterface](/powershell/module/azurerm.network/set-azurermnetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Skojarz lub usunąć skojarzenie grupy zabezpieczeń sieci

1. W polu wyszukiwania w górnej części portalu wprowadź *interfejsy sieciowe* w polu wyszukiwania. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz z listy, który ma zostać skojarzona z sieciową grupę zabezpieczeń do interfejsu sieciowego, lub usunąć skojarzenie z grupy zabezpieczeń sieci.
3. Wybierz **sieciowej grupy zabezpieczeń** w obszarze **ustawienia**.
4. Wybierz pozycję **Edit** (Edytuj).
5. Wybierz **sieciowej grupy zabezpieczeń** , a następnie wybierz grupę zabezpieczeń sieci, aby skojarzyć do interfejsu sieciowego, lub wybierz **Brak**, aby usunąć skojarzenie grupy zabezpieczeń sieci.
6. Wybierz pozycję **Zapisz**.

**Polecenia**

- Azure CLI: [aktualizacji kart sieciowych az](/cli/azure/network/nic#az-network-nic-update)
- Środowiska PowerShell: [AzureRmNetworkInterface zestawu](/powershell/module/azurerm.network/set-azurermnetworkinterface)

## <a name="delete-a-network-interface"></a>Usunąć interfejsu sieciowego

Można usunąć interfejsu sieciowego, dopóki nie jest dołączony do maszyny wirtualnej. Jeśli interfejs sieciowy jest dołączony do maszyny wirtualnej, należy najpierw umieścić maszyny wirtualnej w stanie zatrzymania (cofnięciu przydziału), a następnie odłączyć interfejsu sieciowego z maszyny wirtualnej. Aby odłączyć interfejsu sieciowego z maszyny wirtualnej, wykonaj kroki [odłączyć interfejsu sieciowego z maszyny wirtualnej](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Nie można odłączyć interfejsu sieciowego z maszyny wirtualnej, jeśli jest dołączony do maszyny wirtualnej, jednak tylko interfejsu sieciowego. Maszyna wirtualna zawsze musi mieć co najmniej jeden interfejs sieciowy do niego dołączony. Trwa usuwanie maszyny wirtualnej Odłącza wszystkie interfejsy sieciowe podłączone do niego, ale nie powoduje usunięcia interfejsów sieciowych.

1. W polu zawierająca tekst, który *wyszukiwania zasobów* w górnej części portalu Azure, wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** są wyświetlane w wynikach wyszukiwania, wybierz ją.
2. Wybierz **...**  po prawej stronie interfejsu sieciowego, aby usunąć z listy interfejsów sieciowych.
3. Wybierz pozycję **Usuń**.
4. Wybierz **tak** aby potwierdzić usunięcie interfejsu sieciowego.

Po usunięciu interfejsu sieciowego są wydawane adresy MAC lub adres IP przypisane do niej.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[Usuń kartę sieciową sieci az](/cli/azure/network/nic#az_network_nic_delete)|
|PowerShell|[Remove-AzureRmNetworkInterface](/powershell/module/azurerm.network/remove-azurermnetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Rozwiąż problemy z połączeniem

Jeśli nie można nawiązać komunikacji z maszyny wirtualnej, reguł zabezpieczeń grupy zabezpieczeń sieci lub trasy dla interfejsu sieciowego lub mogą być przyczyną problemu. Masz następujące opcje, aby pomóc w rozwiązaniu problemu:

### <a name="view-effective-security-rules"></a>Wyświetl reguły efektywnym elementem systemu zabezpieczeń

Reguły efektywnym elementem systemu zabezpieczeń dla każdego interfejsu sieciowego dołączony do maszyny wirtualnej są kombinacją reguł utworzonych na grupę zabezpieczeń sieci i [domyślne reguły zabezpieczeń](security-overview.md#default-security-rules). Opis reguł efektywnym elementem systemu zabezpieczeń dla interfejsu sieciowego może pomóc w określeniu, dlaczego nie możesz nawiązać połączenia z maszyną wirtualną lub. Można wyświetlić skuteczne reguły dla dowolnego interfejsu sieciowego, który jest podłączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, aby wyświetlić zasady efektywnym elementem systemu zabezpieczeń. Jeśli nie znasz nazwę maszyny wirtualnej, wprowadź *maszyn wirtualnych* w polu wyszukiwania. Gdy **maszyn wirtualnych** są wyświetlane w wynikach wyszukiwania, zaznacz go, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **sieci** w obszarze **ustawienia**.
3. Wybierz nazwę karty sieciowej.
4. Wybierz **reguły efektywnym elementem systemu zabezpieczeń** w obszarze **pomocy technicznej i rozwiązywania problemów**.
5. Zapoznaj się z listą zasady efektywnym elementem systemu zabezpieczeń w celu ustalenia, czy istnieją poprawne reguły dla komunikacji wymagane dla ruchu przychodzącego i wychodzącego. Dowiedz się więcej o tym, co widać na liście w [omówienie grupy zabezpieczeń sieci](security-overview.md).

Przepływ IP Sprawdź, czy funkcja Azure obserwatora sieciowego też pomóc Ci określić, jeśli zasady zabezpieczeń są uniemożliwia komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [Sprawdź przepływ IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Azure CLI: [az sieci karty sieciowej listy obowiązującej nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- Środowiska PowerShell: [Get AzureRmEffectiveNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermeffectivenetworksecuritygroup) 

### <a name="view-effective-routes"></a>Widok skuteczne tras

Skuteczne trasy dla interfejsów sieciowych podłączonych do maszyny wirtualnej są kombinacją trasy domyślne, wszystkie trasy, które zostały utworzone i wszelkie tras propagowane z lokalnymi sieciami za pomocą protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Opis wprowadzenia trasy dla interfejsu sieciowego mogą pomóc ustalić, dlaczego nie możesz nawiązać połączenia z maszyną wirtualną lub. Można wyświetlić skuteczne trasy dla dowolnego interfejsu sieciowego, który jest podłączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania w górnej części portalu wprowadź nazwę maszyny wirtualnej, aby wyświetlić zasady efektywnym elementem systemu zabezpieczeń. Jeśli nie znasz nazwę maszyny wirtualnej, wprowadź *maszyn wirtualnych* w polu wyszukiwania. Gdy **maszyn wirtualnych** są wyświetlane w wynikach wyszukiwania, zaznacz go, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **sieci** w obszarze **ustawienia**.
3. Wybierz nazwę karty sieciowej.
4. Wybierz **skuteczne tras** w obszarze **pomocy technicznej i rozwiązywania problemów**.
5. Zapoznaj się z listą skuteczne tras można stwierdzić, czy poprawne trasy dla komunikacji wymagane dla ruchu przychodzącego i wychodzącego. Dowiedz się więcej o tym, co widać na liście w [Omówienie routingu](virtual-networks-udr-overview.md).

Funkcję następnego przeskoku obserwatora sieci Azure może również pomóc w określeniu, jeśli trasy nie uniemożliwiają komunikacji między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [następnego przeskoku](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Azure CLI: [az sieci karty sieciowej Pokaż obowiązującej--tabeli tras](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable)

## <a name="permissions"></a>Uprawnienia

Do wykonywania zadań w interfejsach sieciowych, Twoje konto musi mieć przypisaną do [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [niestandardowych](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rola przypisana odpowiednie uprawnienia są wymienione w poniższej tabeli:

| Akcja                                                                     | Name (Nazwa)                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Pobierz interfejs sieciowy                                     |
| Microsoft.Network/networkInterfaces/write                                  | Utwórz lub zaktualizuj interfejs sieciowy                        |
| Microsoft.Network/networkInterfaces/join/action                            | Dołącz do interfejsu sieciowego z maszyną wirtualną           |
| Microsoft.Network/networkInterfaces/delete                                 | Usunąć interfejsu sieciowego                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Dołącz zasobu do interfejsu sieciowego za pośrednictwem servi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Pobierz tabelę tras skuteczne interfejsu sieciowego               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Pobierz grup zabezpieczeń skuteczne interfejsu sieciowego           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Pobierz moduły równoważenia obciążenia interfejsu sieciowego                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Uzyskaj skojarzenie usługi                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Utwórz lub zaktualizuj skojarzenie usługi                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Usuń skojarzenie usługi                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Sprawdź poprawność skojarzenie usługi                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Pobierz Konfiguracja IP interfejsu sieciowego                    |

## <a name="next-steps"></a>Kolejne kroki

- Utwórz maszynę Wirtualną z wieloma kartami sieciowymi przy użyciu [interfejsu wiersza polecenia Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [programu PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Utwórz pojedynczy adresów maszyny Wirtualnej karty Sieciowej z wielu IPv4 [interfejsu wiersza polecenia Azure](virtual-network-multiple-ip-addresses-cli.md) lub [programu PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Tworzenie jednej maszyny Wirtualnej karty Sieciowej z prywatnych adresów (za równoważenia obciążenia Azure) IPv6, za pomocą [interfejsu wiersza polecenia Azure](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), lub [szablonu usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
- Tworzenie przy użyciu interfejsu sieciowego [PowerShell](powershell-samples.md) lub [interfejsu wiersza polecenia Azure](cli-samples.md) przykładowe skrypty lub przy użyciu usługi Azure [szablony Menedżera zasobów](template-samples.md)
- Tworzenie i stosowanie [Azure zasad](policy-samples.md) dla sieci wirtualnych