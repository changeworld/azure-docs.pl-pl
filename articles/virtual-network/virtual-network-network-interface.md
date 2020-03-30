---
title: Tworzenie, zmienianie lub usuwanie interfejsu sieciowego platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, czym jest interfejs sieciowy i jak go tworzyć, zmieniać ustawienia i usuwać.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 11e6285ef70ffde5344add951801997f8541eaad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244942"
---
# <a name="create-change-or-delete-a-network-interface"></a>Tworzenie, zmienianie lub usuwanie interfejsu sieciowego

Dowiedz się, jak tworzyć, zmieniać ustawienia i usuwać interfejs sieciowy. Interfejs sieciowy umożliwia maszynie wirtualnej platformy Azure komunikowanie się z zasobami internetowymi, platformy Azure i lokalnymi. Podczas tworzenia maszyny wirtualnej przy użyciu portalu Azure portal tworzy jeden interfejs sieciowy z ustawieniami domyślnymi dla Ciebie. Zamiast tego można utworzyć interfejsy sieciowe z ustawieniami niestandardowymi i dodać jeden lub więcej interfejsów sieciowych do maszyny wirtualnej podczas jej tworzenia. Można również zmienić domyślne ustawienia interfejsu sieciowego dla istniejącego interfejsu sieciowego. W tym artykule wyjaśniono, jak utworzyć interfejs sieciowy z ustawieniami niestandardowymi, zmienić istniejące ustawienia, takie jak przypisanie filtru sieciowego (grupa zabezpieczeń sieci), przypisanie podsieci, ustawienia serwera DNS i przekierowanie IP, a także usunąć interfejs sieciowy.

Jeśli chcesz dodać, zmienić lub usunąć adresy IP interfejsu sieciowego, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md). Jeśli chcesz dodać interfejsy sieciowe lub usunąć interfejsy sieciowe z maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wykonaj następujące zadania przed wykonaniem kroków w dowolnej sekcji tego artykułu:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać [bezpłatne konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z https://portal.azure.comportalu, otwórz program i zaloguj się za pomocą konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule są używane polecenia programu PowerShell, należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchomić program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, należy również uruchomić, aby utworzyć połączenie z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

## <a name="create-a-network-interface"></a>Tworzenie interfejsu sieciowego

Podczas tworzenia maszyny wirtualnej przy użyciu portalu Azure portal tworzy interfejs sieciowy z ustawieniami domyślnymi dla Ciebie. Jeśli wolisz określić wszystkie ustawienia interfejsu sieciowego, można utworzyć interfejs sieciowy z ustawieniami niestandardowymi i dołączyć interfejs sieciowy do maszyny wirtualnej podczas tworzenia maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Można również utworzyć interfejs sieciowy i dodać go do istniejącej maszyny wirtualnej (przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure). Aby dowiedzieć się, jak utworzyć maszynę wirtualną z istniejącym interfejsem sieciowym lub dodać lub usunąć interfejsy sieciowe z istniejących maszyn wirtualnych, zobacz [Dodawanie lub usuwanie interfejsów sieciowych](virtual-network-network-interface-vm.md). Przed utworzeniem interfejsu sieciowego musi istnieć istniejąca [sieć wirtualna](manage-virtual-network.md) w tej samej lokalizacji i subskrypcji, w której utworzysz interfejs sieciowy.

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz **+ Dodaj** w obszarze **Interfejsy sieciowe**.
3. Wprowadź lub wybierz wartości dla następujących ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wymagana?|Szczegóły|
    |---|---|---|
    |Nazwa|Tak|Nazwa musi być unikatowa w wybranej grupie zasobów. Z biegiem czasu prawdopodobnie będziesz mieć kilka interfejsów sieciowych w subskrypcji platformy Azure. Aby uzyskać sugestie podczas tworzenia konwencji nazewnictwa, aby ułatwić zarządzanie kilkoma interfejsami sieciowymi, zobacz [Konwencje nazewnictwa](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). Nie można zmienić nazwy po utworzeniu interfejsu sieciowego.|
    |Sieć wirtualna|Tak|Wybierz sieć wirtualną dla interfejsu sieciowego. Interfejs sieciowy można przypisać tylko do sieci wirtualnej, która istnieje w tej samej subskrypcji i lokalizacji co interfejs sieciowy. Po utworzeniu interfejsu sieciowego nie można zmienić sieci wirtualnej, do której jest przypisana. Maszyna wirtualna, do której dodajesz interfejs sieciowy, musi również istnieć w tej samej lokalizacji i subskrypcji co interfejs sieciowy.|
    |Podsieć|Tak|Wybierz podsieć w wybranej sieci wirtualnej. Można zmienić podsieć, do której interfejs sieciowy jest przypisany po jego utworzeniu.|
    |Przypisanie prywatnego adresu IP|Tak| W tym ustawieniu wybierasz metodę przypisania adresu IPv4. Wybierz jedną z następujących metod przypisywania: **Dynamiczne:** Po wybraniu tej opcji platforma Azure automatycznie przypisuje następny dostępny adres z przestrzeni adresowej wybranej podsieci. **Statyczne:** Wybierając tę opcję, należy ręcznie przypisać dostępny adres IP z przestrzeni adresowej wybranej podsieci. Adresy statyczne i dynamiczne nie zmieniają się, dopóki nie zostaną one zmienić lub interfejs sieciowy zostanie usunięty. Metodę przypisania można zmienić po utworzeniu interfejsu sieciowego. Serwer DHCP platformy Azure przypisuje ten adres do interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej.|
    |Sieciowa grupa zabezpieczeń|Nie| Pozostaw ustawioną na **Brak,** wybierz istniejącą [grupę zabezpieczeń sieci](security-overview.md)lub [utwórz sieciową grupę zabezpieczeń](tutorial-filter-network-traffic.md). Sieciowe grupy zabezpieczeń umożliwiają filtrowanie ruchu sieciowego do i z interfejsu sieciowego. Do interfejsu sieciowego można zastosować zero lub jedną grupę zabezpieczeń sieci. Do podsieci, do jakiej jest przypisany interfejs sieciowy, można również zastosować zero lub jedną grupę zabezpieczeń sieci. Gdy sieciowa grupa zabezpieczeń jest stosowana do interfejsu sieciowego i podsieci, do niej przypisany jest interfejs sieciowy, czasami występują nieoczekiwane wyniki. Aby rozwiązywać problemy z grupami zabezpieczeń sieciowych zastosowanymi do interfejsów sieciowych i podsieci, zobacz [Rozwiązywanie problemów z grupami zabezpieczeń sieci](diagnose-network-traffic-filter-problem.md).|
    |Subskrypcja|Tak|Wybierz jedną z [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)platformy Azure . Maszyna wirtualna, do której dołączasz interfejs sieciowy i sieć wirtualna, z którą go łączysz, musi istnieć w tej samej subskrypcji.|
    |Prywatny adres IP (IPv6)|Nie| Jeśli to pole wyboru zostanie zaznaczone, adres IPv6 jest przypisany do interfejsu sieciowego, oprócz adresu IPv4 przypisanego do interfejsu sieciowego. Zobacz sekcję IPv6 tego artykułu, aby uzyskać ważne informacje na temat korzystania z IPv6 z interfejsami sieciowymi. Nie można wybrać metody przypisania adresu IPv6. Jeśli zdecydujesz się przypisać adres IPv6, zostanie on przypisany do metody dynamicznej.
    |Nazwa protokołu IPv6 (pojawia się tylko po zaznaczeniu pola wyboru **Prywatny adres IP (IPv6)IPv6** name (only appears when the Private IP address (IPv6) checkbox) |Tak, jeśli zaznaczone jest pole wyboru **Prywatny adres IP (IPv6).**| Ta nazwa jest przypisana do pomocniczej konfiguracji IP dla interfejsu sieciowego. Aby dowiedzieć się więcej o konfiguracjach adresów IP, zobacz [Wyświetlanie ustawień interfejsu sieciowego](#view-network-interface-settings).|
    |Grupa zasobów|Tak|Wybierz istniejącą [grupę zasobów](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) lub utwórz jedną. Interfejs sieciowy może istnieć w tej samej lub innej grupie zasobów niż maszyna wirtualna, do której jest do niego dołączona, lub sieć wirtualna, z którą jest do niego nawiązywać połączenie.|
    |Lokalizacja|Tak|Maszyna wirtualna, do której dołączasz interfejs sieciowy, oraz sieć wirtualna, z którą go łączysz, muszą istnieć w tej samej [lokalizacji](https://azure.microsoft.com/regions), nazywane również regionem.|

Portal nie udostępnia opcji przypisywania publicznego adresu IP do interfejsu sieciowego podczas jego tworzenia, chociaż portal tworzy publiczny adres IP i przypisuje go do interfejsu sieciowego podczas tworzenia maszyny wirtualnej za pomocą portalu. Aby dowiedzieć się, jak dodać publiczny adres IP do interfejsu sieciowego po jego utworzeniu, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md). Aby utworzyć interfejs sieciowy z publicznym adresem IP, należy użyć interfejsu wiersza polecenia lub programu PowerShell do utworzenia interfejsu sieciowego.

Portal nie udostępnia opcji przypisywania interfejsu sieciowego do grup zabezpieczeń aplikacji podczas tworzenia interfejsu sieciowego, ale działają interfejsy wiersza polecenia platformy Azure i program PowerShell. Istniejący interfejs sieciowy można jednak przypisać do grupy zabezpieczeń aplikacji za pomocą portalu, o ile interfejs sieciowy jest podłączony do maszyny wirtualnej. Aby dowiedzieć się, jak przypisać interfejs sieciowy do grupy zabezpieczeń aplikacji, zobacz [Dodawanie do grup zabezpieczeń aplikacji lub usuwanie z niego](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Platforma Azure przypisuje adres MAC do interfejsu sieciowego dopiero po dołączeniu interfejsu sieciowego do maszyny wirtualnej i uruchomieniu maszyny wirtualnej po raz pierwszy. Nie można określić adresu MAC, który platforma Azure przypisuje do interfejsu sieciowego. Adres MAC pozostaje przypisany do interfejsu sieciowego do czasu usunięcia interfejsu sieciowego lub zmiany prywatnego adresu IP przypisanego do podstawowej konfiguracji IP podstawowego interfejsu sieciowego. Aby dowiedzieć się więcej o adresach IP i konfiguracjach IP, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md)

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Wyświetlanie ustawień interfejsu sieciowego

Po jego utworzeniu można wyświetlać i zmieniać większość ustawień interfejsu sieciowego. Portal nie wyświetla sufiksu DNS ani członkostwa w grupie zabezpieczeń aplikacji dla interfejsu sieciowego. Za pomocą poleceń interfejsu [wiersza](#view-settings-commands) polecenia programu PowerShell lub platformy Azure można wyświetlić sufiks DNS i członkostwo w grupie zabezpieczeń aplikacji.

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, który chcesz wyświetlić lub zmienić ustawienia z listy.
3. Dla wybranego interfejsu sieciowego są wyświetlane następujące elementy:
   - **Przegląd:** Zawiera informacje o interfejsie sieciowym, takie jak przypisane do niego adresy IP, sieć wirtualna/podsieć, do jakiej jest przypisany interfejs sieciowy, oraz maszyna wirtualna, do na którą jest podłączony interfejs sieciowy (jeśli jest dołączona do jednej). Na poniższej ilustracji przedstawiono ustawienia przeglądu interfejsu sieciowego ![o nazwie **mywebserver256**: Omówienie interfejsu sieciowego](./media/virtual-network-network-interface/nic-overview.png)

     Interfejs sieciowy można przenieść do innej grupy zasobów lub subskrypcji, wybierając **(zmień)** obok **nazwy grupy zasobów** lub **subskrypcji**. W przypadku przenoszenia interfejsu sieciowego należy przenieść wszystkie zasoby związane z interfejsem sieciowym. Jeśli interfejs sieciowy jest dołączony do maszyny wirtualnej, na przykład należy również przenieść maszynę wirtualną i inne zasoby związane z maszyną wirtualną. Aby przenieść interfejs sieciowy, zobacz [Przenoszenie zasobu do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal). W tym artykule wymieniono wymagania wstępne i sposób przenoszenia zasobów przy użyciu witryny Azure portal, powershell i interfejsu wiersza polecenia platformy Azure.
   - **Konfiguracje adresów IP:** W tym miejscu wymieniono publiczne i prywatne adresy IPv4 i IPv6 przypisane do konfiguracji IP. Jeśli adres IPv6 jest przypisany do konfiguracji IP, adres nie jest wyświetlany. Aby dowiedzieć się więcej o konfiguracjach adresów IP oraz o tym, jak dodawać i usuwać adresy IP, zobacz [Konfigurowanie adresów IP dla interfejsu sieciowego platformy Azure](virtual-network-network-interface-addresses.md). Przekazywanie adresów IP i przypisanie podsieci są również skonfigurowane w tej sekcji. Aby dowiedzieć się więcej o tych ustawieniach, zobacz [Włączanie lub wyłączanie przekazywania adresów IP](#enable-or-disable-ip-forwarding) i [zmienianie przypisania podsieci](#change-subnet-assignment).
   - **Serwery DNS:** Można określić, który serwer DNS jest przypisany przez serwery DHCP platformy Azure. Interfejs sieciowy może dziedziczyć ustawienie z sieci wirtualnej, do których jest przypisany interfejs sieciowy, lub mieć ustawienie niestandardowe, które zastępuje ustawienie sieci wirtualnej, do których jest przypisana. Aby zmodyfikować wyświetlane wyświetlane, zobacz [Zmienianie serwerów DNS](#change-dns-servers).
   - **Grupa zabezpieczeń sieci (NSG):** Wyświetla, która grupa sieciowa jest skojarzona z interfejsem sieciowym (jeśli istnieje). Grupa sieciowa sieciowej zawiera reguły przychodzące i wychodzące do filtrowania ruchu sieciowego dla interfejsu sieciowego. Jeśli sieciowej sieciowej sieciowej jest skojarzony interfejs sieciowy, wyświetlana jest nazwa skojarzonej sieciowej sieciowej. Aby zmodyfikować wyświetlane wyświetlane, zobacz [Kojarzenie lub odłączenie sieciowej grupy zabezpieczeń](#associate-or-dissociate-a-network-security-group).
   - **Właściwości:** Wyświetla kluczowe ustawienia interfejsu sieciowego, w tym jego adres MAC (pusty, jeśli interfejs sieciowy nie jest podłączony do maszyny wirtualnej) i subskrypcję, w jakiej istnieje.
   - **Skuteczne zasady bezpieczeństwa:**  Reguły zabezpieczeń są wyświetlane, jeśli interfejs sieciowy jest dołączony do uruchomionej maszyny wirtualnej, a sieciowej listy zabezpieczeń jest skojarzony z interfejsem sieciowym, podsiecią, do których jest przypisany lub do obu tych powiązanych. Aby dowiedzieć się więcej o wyświetlanych komunikatach, zobacz [Wyświetlanie skutecznych reguł zabezpieczeń](#view-effective-security-rules). Aby dowiedzieć się więcej o sieciach SGS, zobacz [Sieciowe grupy zabezpieczeń](security-overview.md).
   - **Efektywne trasy:** Trasy są wyświetlane, jeśli interfejs sieciowy jest podłączony do uruchomionej maszyny wirtualnej. Trasy są kombinacją tras domyślnych platformy Azure, wszystkie trasy zdefiniowane przez użytkownika i wszelkie trasy BGP, które mogą istnieć dla podsieci, do którego jest przypisany interfejs sieciowy. Aby dowiedzieć się więcej o wyświetlanych, zobacz [Wyświetlanie tras efektywnych](#view-effective-routes). Aby dowiedzieć się więcej o trasach domyślnych platformy Azure i trasach zdefiniowanych przez użytkownika, zobacz [Omówienie routingu](virtual-networks-udr-overview.md).
Typowe ustawienia usługi Azure Resource Manager: aby dowiedzieć się więcej o typowych ustawieniach usługi Azure Resource Manager, zobacz [Dziennik aktywności](../azure-monitor/platform/platform-logs-overview.md), Kontrola [dostępu (IAM),](../role-based-access-control/overview.md) [Znaczniki,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Blokady](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)i [Skrypt automatyzacji](../azure-resource-manager/templates/export-template-portal.md).

<a name="view-settings-commands"></a>**Polecenia**

Jeśli adres IPv6 jest przypisany do interfejsu sieciowego, wyjście programu PowerShell zwraca fakt, że adres jest przypisany, ale nie zwraca przypisanego adresu. Podobnie cli zwraca fakt, że adres jest przypisany, ale zwraca *wartość null* w jego danych wyjściowych dla adresu.

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic lista](/cli/azure/network/nic) do przeglądania interfejsów sieciowych w subskrypcji; [az network nic show,](/cli/azure/network/nic) aby wyświetlić ustawienia interfejsu sieciowego|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) do wyświetlania interfejsów sieciowych w ustawieniach subskrypcji lub widoku interfejsu sieciowego|

## <a name="change-dns-servers"></a>Zmienianie serwerów DNS

Serwer DNS jest przypisywany przez serwer DHCP platformy Azure do interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. Przypisany serwer DNS jest niezależnie od ustawienia serwera DNS dla interfejsu sieciowego. Aby dowiedzieć się więcej o ustawieniach rozpoznawania nazw dla interfejsu sieciowego, zobacz [Rozpoznawanie nazw maszyn wirtualnych](virtual-networks-name-resolution-for-vms-and-role-instances.md). Interfejs sieciowy może dziedziczyć ustawienia z sieci wirtualnej lub używać własnych unikatowych ustawień, które zastępują ustawienie dla sieci wirtualnej.

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz zmienić serwer DNS, wybierz z listy.
3. Wybierz **serwery DNS** w obszarze **USTAWIENIA**.
4. Wybierz jedną z opcji:
   - **Dziedzicz z sieci wirtualnej**: Wybierz tę opcję, aby dziedziczyć ustawienie serwera DNS zdefiniowane dla sieci wirtualnej, do niej przypisany jest interfejs sieciowy. Na poziomie sieci wirtualnej jest definiowany niestandardowy serwer DNS lub serwer DNS dostarczony przez platformę Azure. Serwer DNS dostarczony przez platformę Azure może rozpoznawać nazwy hostów dla zasobów przypisanych do tej samej sieci wirtualnej. Nazwa FQDN musi być używana do rozpoznawania zasobów przypisanych do różnych sieci wirtualnych.
   - **Niestandardowe:** Można skonfigurować własny serwer DNS, aby rozpoznawać nazwy w wielu sieciach wirtualnych. Wprowadź adres IP serwera, którego chcesz używać jako serwera DNS. Określony adres serwera DNS jest przypisany tylko do tego interfejsu sieciowego i zastępuje wszelkie ustawienia DNS dla sieci wirtualnej, do której jest przypisany interfejs sieciowy.
     >[!Note]
     >Jeśli maszyna wirtualna używa karty sieciowej, która jest częścią zestawu dostępności, wszystkie serwery DNS, które są określone dla każdej z maszyn wirtualnych ze wszystkich kart sieciowych, które są częścią zestawu dostępności, zostaną odziedziczone.
5. Wybierz **pozycję Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az aktualizacja sieci nic](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Włączanie lub wyłączanie przekazywania adresów IP

Przekazywanie adresów IP umożliwia maszynie wirtualnej, do na które jest dołączony interfejs sieciowy:
- Odbieranie ruchu sieciowego nie jest przeznaczony dla jednego z adresów IP przypisanych do żadnej z konfiguracji IP przypisanych do interfejsu sieciowego.
- Wysyłaj ruch sieciowy z innym źródłowym adresem IP niż ten przypisany do jednej z konfiguracji IP interfejsu sieciowego.

Ustawienie musi być włączone dla każdego interfejsu sieciowego, który jest dołączony do maszyny wirtualnej, która odbiera ruch, który maszyna wirtualna musi do przekazania. Maszyna wirtualna może przesyłać dalej ruch, niezależnie od tego, czy ma wiele interfejsów sieciowych, czy jeden interfejs sieciowy. Podczas przekazywania adresów IP jest ustawienie platformy Azure, maszyna wirtualna musi również uruchomić aplikację zdolną do przesyłania dalej ruchu, takich jak zapora, optymalizacja WAN i równoważenia obciążenia aplikacji. Gdy maszyna wirtualna jest uruchomiona aplikacje sieciowe, maszyna wirtualna jest często określane jako wirtualne urządzenie sieciowe. Listę gotowych do wdrożenia wirtualnych urządzeń sieciowych można wyświetlić w portalu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Przekazywanie adresów IP jest zwykle używane z trasami zdefiniowanymi przez użytkownika. Aby dowiedzieć się więcej o trasach zdefiniowanych przez użytkownika, zobacz [Trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md).

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz włączyć lub wyłączyć przekazywanie adresów IP.
3. Wybierz **konfiguracje IP** w sekcji **USTAWIENIA.**
4. Wybierz **opcję Włączone** lub **Wyłączone** (ustawienie domyślne), aby zmienić to ustawienie.
5. Wybierz **pozycję Zapisz**.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az aktualizacja sieci nic](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Zmienianie przydziału podsieci

Można zmienić podsieć, ale nie sieć wirtualną, do której jest przypisany interfejs sieciowy.

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz zmienić przypisanie podsieci.
3. Wybierz **konfiguracje IP** w obszarze **USTAWIENIA**. Jeśli obok nich znajdują się prywatne adresy IP dla wszystkich wymienionych konfiguracji IP **(Statyczne),** należy zmienić metodę przypisywania adresów IP na dynamiczną, wykonując następujące kroki. Wszystkie prywatne adresy IP muszą być przypisane za pomocą metody przypisania dynamicznego, aby zmienić przypisanie podsieci dla interfejsu sieciowego. Jeśli adresy są przypisane z metodą dynamiczną, kontynuuj krok piąty. Jeśli dowolne adresy IPv4 są przypisane z metodą przypisania statycznego, wykonaj następujące kroki, aby zmienić metodę przypisania na dynamiczną:
   - Wybierz konfigurację IP, dla której chcesz zmienić metodę przypisywania adresów IPv4, z listy konfiguracji adresów IP.
   - Wybierz **opcję Dynamiczny** dla prywatnej metody **przypisywania** adresów IP. Nie można przypisać adresu IPv6 za pomocą metody przypisania statycznego.
   - Wybierz **pozycję Zapisz**.
4. Wybierz podsieć, do której chcesz **Subnet** przenieść interfejs sieciowy, z listy rozwijanej Podsieć.
5. Wybierz **pozycję Zapisz**. Nowe adresy dynamiczne są przypisywane z zakresu adresów podsieci dla nowej podsieci. Po przypisaniu interfejsu sieciowego do nowej podsieci można przypisać statyczny adres IPv4 z nowego zakresu adresów podsieci, jeśli wybierzesz. Aby dowiedzieć się więcej o dodawaniu, zmienianiu i usuwaniu adresów IP interfejsu sieciowego, zobacz [Zarządzanie adresami IP](virtual-network-network-interface-addresses.md).

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az sieć nic ip-config aktualizacja](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Dodawanie do grup zabezpieczeń aplikacji lub ich usuwanie

Do interfejsu sieciowego można dodać lub usunąć interfejs sieciowy z grupy zabezpieczeń aplikacji za pomocą portalu tylko wtedy, gdy interfejs sieciowy jest podłączony do maszyny wirtualnej. Za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure można dodać interfejs sieciowy lub usunąć interfejs sieciowy z grupy zabezpieczeń aplikacji, niezależnie od tego, czy interfejs sieciowy jest podłączony do maszyny wirtualnej, czy nie. Dowiedz się więcej o [grupach zabezpieczeń aplikacji](security-overview.md#application-security-groups) i o tym, jak [utworzyć grupę zabezpieczeń aplikacji](manage-network-security-group.md).

1. W polu *Wyszukaj zasoby, usługi i dokumenty* u góry portalu zacznij wpisywać nazwę maszyny wirtualnej z interfejsem sieciowym, który chcesz dodać lub usunąć z grupy zabezpieczeń aplikacji. Gdy w wynikach wyszukiwania pojawi się nazwa maszyny Wirtualnej, wybierz ją.
2. W obszarze **USTAWIENIA** wybierz pozycję **Sieć**.  Wybierz **pozycję Grupy zabezpieczeń aplikacji,** a następnie **skonfiguruj grupy zabezpieczeń aplikacji,** wybierz grupy zabezpieczeń aplikacji, do których chcesz dodać interfejs sieciowy, lub usuń zaznaczenie grup zabezpieczeń aplikacji, z których chcesz usunąć interfejs sieciowy, a następnie wybierz pozycję **Zapisz**. Do tej samej grupy zabezpieczeń aplikacji można dodać tylko interfejsy sieciowe istniejące w tej samej sieci wirtualnej. Grupa zabezpieczeń aplikacji musi istnieć w tej samej lokalizacji co interfejs sieciowy.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az aktualizacja sieci nic](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Kojarzenie lub odłączanie sieciowej grupy zabezpieczeń

1. W polu wyszukiwania u góry portalu wprowadź *interfejsy sieciowe* w polu wyszukiwania. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy na liście, z którą chcesz skojarzyć grupę zabezpieczeń sieci, lub odłączyć sieciową grupę zabezpieczeń.
3. Wybierz **grupę zabezpieczeń Sieć** w obszarze **USTAWIENIA**.
4. Wybierz pozycję **Edit** (Edytuj).
5. Wybierz **opcję Sieć grupy zabezpieczeń,** a następnie wybierz grupę zabezpieczeń sieci, którą chcesz skojarzyć z interfejsem sieciowym, lub wybierz pozycję **Brak**, aby odłączyć sieciową grupę zabezpieczeń.
6. Wybierz **pozycję Zapisz**.

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [aktualizacja sieci AZ](/cli/azure/network/nic#az-network-nic-update)
- Program PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Usuwanie interfejsu sieciowego

Można usunąć interfejs sieciowy, o ile nie jest dołączony do maszyny wirtualnej. Jeśli interfejs sieciowy jest dołączony do maszyny wirtualnej, należy najpierw umieścić maszynę wirtualną w stanie zatrzymany (cofnięto alokację), a następnie odłączyć interfejs sieciowy od maszyny wirtualnej. Aby odłączyć interfejs sieciowy od maszyny wirtualnej, wykonaj czynności opisane w [odłączeniu interfejsu sieciowego od maszyny wirtualnej.](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm) Nie można jednak odłączyć interfejsu sieciowego od maszyny wirtualnej, jeśli jest to jednak jedyny interfejs sieciowy dołączony do maszyny wirtualnej. Maszyna wirtualna musi zawsze mieć co najmniej jeden interfejs sieciowy podłączony do niego. Usunięcie maszyny wirtualnej odłącza wszystkie interfejsy sieciowe podłączone do niej, ale nie powoduje usunięcia interfejsów sieciowych.

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy na liście, którą chcesz usunąć.
3. W **obszarze Przegląd** wybierz pozycję **Usuń**.
4. Wybierz **opcję Tak,** aby potwierdzić usunięcie interfejsu sieciowego.

Po usunięciu interfejsu sieciowego wszystkie przypisane do niego adresy MAC lub IP są zwalniane.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az sieć nic usunąć](/cli/azure/network/nic)|
|PowerShell|[Usuń-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Rozwiązywanie problemów z łącznością

Jeśli nie można komunikować się z lub z maszyny wirtualnej, reguły zabezpieczeń sieciowej grupy zabezpieczeń lub trasy skuteczne dla interfejsu sieciowego, może być przyczyną problemu. Dostępne są następujące opcje, które pomogą rozwiązać ten problem:

### <a name="view-effective-security-rules"></a>Wyświetlanie skutecznych reguł zabezpieczeń

Skuteczne reguły zabezpieczeń dla każdego interfejsu sieciowego podłączonego do maszyny wirtualnej to kombinacja reguł utworzonych w sieciowej grupie zabezpieczeń i [domyślnych reguł zabezpieczeń](security-overview.md#default-security-rules). Zrozumienie skutecznych reguł zabezpieczeń dla interfejsu sieciowego może pomóc w określeniu, dlaczego nie można komunikować się z maszyną wirtualną lub z niej. Można wyświetlić skuteczne reguły dla dowolnego interfejsu sieciowego, który jest dołączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania u góry portalu wprowadź nazwę maszyny wirtualnej, dla której chcesz wyświetlić skuteczne reguły zabezpieczeń. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyny wirtualne* w polu wyszukiwania. Gdy **maszyny wirtualne** pojawiają się w wynikach wyszukiwania, wybierz go, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **pozycję Sieć w** obszarze **USTAWIENIA**.
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz **pozycję Skuteczne reguły zabezpieczeń** w obszarze POMOC / **ROZWIĄZYWANIE PROBLEMÓW**.
5. Przejrzyj listę skutecznych reguł zabezpieczeń, aby ustalić, czy istnieją odpowiednie reguły dla wymaganej komunikacji przychodzącej i wychodzącej. Dowiedz się więcej o tym, co widzisz na liście w [omówienie grupy zabezpieczeń sieci](security-overview.md).

Funkcja weryfikacji przepływu ip usługi Azure Network Watcher może również pomóc w określeniu, czy reguły zabezpieczeń uniemożliwiają komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [Weryfikacja przepływu ip](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [lista sieci az-efektywna-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Wyświetlanie efektywnych tras

Efektywne trasy interfejsów sieciowych dołączonych do maszyny wirtualnej to kombinacja tras domyślnych, utworzonych tras i wszelkich tras propagowanych z sieci lokalnych za pośrednictwem protokołu BGP za pośrednictwem bramy sieci wirtualnej platformy Azure. Zrozumienie skutecznych tras dla interfejsu sieciowego może pomóc w określeniu, dlaczego nie można komunikować się z maszyną wirtualną lub z niej. Można wyświetlić efektywne trasy dla dowolnego interfejsu sieciowego, który jest dołączony do uruchomionej maszyny wirtualnej.

1. W polu wyszukiwania u góry portalu wprowadź nazwę maszyny wirtualnej, dla której chcesz wyświetlić skuteczne reguły zabezpieczeń. Jeśli nie znasz nazwy maszyny wirtualnej, wprowadź *maszyny wirtualne* w polu wyszukiwania. Gdy **maszyny wirtualne** pojawiają się w wynikach wyszukiwania, wybierz go, a następnie wybierz maszynę wirtualną z listy.
2. Wybierz **pozycję Sieć w** obszarze **USTAWIENIA**.
3. Wybierz nazwę interfejsu sieciowego.
4. Wybierz **opcję Efektywne trasy** w obszarze OBSŁUGA + **ROZWIĄZYWANIE PROBLEMÓW**.
5. Przejrzyj listę skutecznych tras, aby ustalić, czy istnieją poprawne trasy dla wymaganej komunikacji przychodzącej i wychodzącej. Dowiedz się więcej o tym, co widzisz na liście w [omówieniu routingu](virtual-networks-udr-overview.md).

Następna funkcja przeskoku usługi Azure Network Watcher może również pomóc w określeniu, czy trasy uniemożliwiają komunikację między maszyną wirtualną a punktem końcowym. Aby dowiedzieć się więcej, zobacz [Następny przeskok](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Polecenia**

- Interfejs wiersza polecenia platformy Azure: [tabela az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Uprawnienia

Aby wykonywać zadania w interfejsach sieciowych, konto musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do roli [niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która ma przypisane odpowiednie uprawnienia wymienione w poniższej tabeli:

| Akcja                                                                     | Nazwa                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Pobierz interfejs sieciowy                                     |
| Microsoft.Network/networkInterfaces/write                                  | Tworzenie lub aktualizowanie interfejsu sieciowego                        |
| Sieć Microsoft.Network/networkInterfaces/join/action                            | Dołączanie interfejsu sieciowego do maszyny wirtualnej           |
| Microsoft.Network/networkInterfaces/delete                                 | Usuwanie interfejsu sieciowego                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Dołącz do zasobu do interfejsu sieciowego za pośrednictwem usługi...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Uzyskaj efektywną tabelę tras interfejsu sieciowego               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Uzyskaj skuteczne grupy zabezpieczeń interfejsu sieciowego           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Pobierz moduły równoważenia obciążenia interfejsu sieciowego                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Pobierz skojarzenie usług                                   |
| Microsoft.Network/networkInterfaces/serviceNawiązania/zapis              | Tworzenie lub aktualizowanie skojarzenia usługi                    |
| Microsoft.Network/networkInterfaces/serviceNałączenia/usuwanie             | Usuwanie skojarzenia usługi                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Sprawdzanie poprawności skojarzenia usług                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | Pobierz konfigurację IP interfejsu sieciowego                    |

## <a name="next-steps"></a>Następne kroki

- Tworzenie maszyny wirtualnej z wieloma kartami sieciowymi przy użyciu [interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub programu [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie pojedynczej maszyny Wirtualnej karty sieciowej z wieloma adresami IPv4 przy użyciu [interfejsu wiersza polecenia platformy Azure](virtual-network-multiple-ip-addresses-cli.md) lub programu [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Tworzenie pojedynczej maszyny Wirtualnej karty sieciowej z prywatnym adresem IPv6 (za modułem równoważenia obciążenia platformy Azure) przy użyciu [szablonu](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [interfejsu wiersza polecenia platformy Azure,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json)programu [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)lub usługi Azure Resource Manager
- Tworzenie interfejsu sieciowego przy użyciu przykładowych skryptów interfejsu [wiersza polecenia programu](cli-samples.md) [PowerShell](powershell-samples.md) lub platformy Azure lub przy użyciu [szablonu usługi](template-samples.md) Azure Resource Manager
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
