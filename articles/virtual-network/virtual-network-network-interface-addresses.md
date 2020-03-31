---
title: Konfigurowanie adresów IP dla interfejsu sieciowego platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak dodawać, zmieniać i usuwać prywatne i publiczne adresy IP interfejsu sieciowego.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: a2a85d98bf29e78d58bf0c578ce79943bae21fc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244968"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Dodawanie, zmienianie i usuwanie adresów IP dla interfejsów sieciowych platformy Azure

Dowiedz się, jak dodawać, zmieniać i usuwać publiczne i prywatne adresy IP interfejsu sieciowego. Prywatne adresy IP przypisane do interfejsu sieciowego umożliwiają maszynie wirtualnej komunikowanie się z innymi zasobami w sieci wirtualnej platformy Azure i połączonych sieciach. Prywatny adres IP umożliwia również komunikację wychodzącą z Internetem przy użyciu nieprzewidywalnego adresu IP. [Publiczny adres IP](virtual-network-public-ip-address.md) przypisany do interfejsu sieciowego umożliwia komunikację przychodzącą do maszyny wirtualnej z Internetu. Adres umożliwia również komunikację wychodzącą z maszyny wirtualnej do Internetu przy użyciu przewidywalnego adresu IP. Aby uzyskać szczegółowe informacje, zobacz [Opis połączeń wychodzących na platformie Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Aby utworzyć, zmienić lub usunąć interfejs sieciowy, przeczytaj artykuł [Zarządzanie interfejsem sieciowym.](virtual-network-network-interface.md) Jeśli chcesz dodać interfejsy sieciowe do lub usunąć interfejsy sieciowe z maszyny wirtualnej, przeczytaj artykuł [Dodaj lub usuń interfejsy sieciowe.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wykonaj następujące zadania przed wykonaniem kroków w dowolnej sekcji tego artykułu:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać [bezpłatne konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z https://portal.azure.comportalu, otwórz program i zaloguj się za pomocą konta platformy Azure.
- Jeśli do wykonywania zadań w tym artykule są używane polecenia programu PowerShell, należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchomić program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzAccount`, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, należy również uruchomić, aby utworzyć połączenie z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w obszarze Uprawnienia [interfejsu sieciowego](virtual-network-network-interface.md#permissions).

## <a name="add-ip-addresses"></a>Dodawanie adresów IP

Można dodać dowolną [liczbę prywatnych](#private) i [publicznych](#public) adresów [IPv4,](#ipv4) jeśli jest to konieczne do interfejsu sieciowego, w granicach wymienionych w artykule [Limity platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) Można dodać prywatny adres IPv6 do jednej [pomocniczej konfiguracji IP](#secondary) (o ile nie ma istniejących dodatkowych konfiguracji IP) dla istniejącego interfejsu sieciowego. Każdy interfejs sieciowy może mieć co najwyżej jeden adres prywatny IPv6. Opcjonalnie można dodać publiczny adres IPv6 do konfiguracji interfejsu sieciowego IPv6. Szczegółowe informacje na temat używania adresów IPv6 można znaleźć w aplikacji [IPv6.](#ipv6)

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, dla którego chcesz dodać adres IPv4 z listy.
3. W obszarze **USTAWIENIA**wybierz pozycję **Konfiguracje IP**.
4. W obszarze **Konfiguracje IP**wybierz **pozycję + Dodaj**.
5. Określ następujące elementy, a następnie wybierz przycisk **OK**:

   |Ustawienie|Wymagana?|Szczegóły|
   |---|---|---|
   |Nazwa|Tak|Musi być unikatowa dla interfejsu sieciowego|
   |Typ|Tak|Ponieważ dodajesz konfigurację IP do istniejącego interfejsu sieciowego, a każdy interfejs sieciowy musi mieć [podstawową](#primary) konfigurację IP, jedyną opcją jest **pomocnicza**.|
   |Metoda przypisywania prywatnych adresów IP|Tak|[**Dynamiczne:**](#dynamic)Platforma Azure przypisuje następny dostępny adres dla zakresu adresów podsieci, w który jest wdrażany interfejs sieciowy. [**Statyczne:**](#static)Należy przypisać nieużyny adres dla zakresu adresów podsieci, w której jest wdrażany interfejs sieciowy.|
   |Publiczny adres IP|Nie|**Wyłączone:** Z konfiguracją adresu IP nie jest obecnie skojarzony żaden publiczny zasób adresu IP. **Włączono:** Wybierz istniejący publiczny adres IP protokołu IPv4 lub utwórz nowy. Aby dowiedzieć się, jak utworzyć publiczny adres IP, przeczytaj artykuł [Publiczne adresy IP.](virtual-network-public-ip-address.md#create-a-public-ip-address)|
6. Ręcznie dodaj pomocnicze prywatne adresy IP do systemu operacyjnego maszyny wirtualnej, wykonując instrukcje w artykule [Przypisywanie wielu adresów IP do systemów operacyjnych maszyn wirtualnych.](virtual-network-multiple-ip-addresses-portal.md#os-config) Zobacz [prywatne](#private) adresy IP dla specjalnych zagadnień przed ręcznym dodaniem adresów IP do systemu operacyjnego maszyny wirtualnej. Nie należy dodawać żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Dodatek-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Zmienianie ustawień adresu IP

Może być konieczna zmiana metody przypisywania adresu IPv4, zmiana statycznego adresu IPv4 lub zmiana publicznego adresu IP przypisanego do interfejsu sieciowego. Jeśli zmieniasz prywatny adres IPv4 pomocniczej konfiguracji IP skojarzonej z pomocniczym interfejsem sieciowym na maszynie wirtualnej (dowiedz się więcej o [podstawowych i pomocniczych interfejsach sieciowych),](virtual-network-network-interface-vm.md)umieść maszynę wirtualną w stanie zatrzymany (cofnięto przydział) przed wykonaniem następujących kroków:

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, który chcesz wyświetlić lub zmienić ustawienia adresu IP dla z listy.
3. W obszarze **USTAWIENIA**wybierz pozycję **Konfiguracje IP**.
4. Wybierz konfigurację IP, którą chcesz zmodyfikować z listy.
5. Zmień ustawienia, zgodnie z potrzebami, używając informacji o ustawieniach w kroku 5 [Dodaj konfigurację IP](#add-ip-addresses).
6. Wybierz **pozycję Zapisz**.

>[!NOTE]
>Jeśli podstawowy interfejs sieciowy ma wiele konfiguracji IP i zmieniasz prywatny adres IP podstawowej konfiguracji IP, należy ręcznie ponownie przypisać podstawowe i pomocnicze adresy IP do interfejsu sieciowego w systemie Windows (nie jest wymagane dla systemu Linux) . Aby ręcznie przypisać adresy IP do interfejsu sieciowego w systemie operacyjnym, zobacz [Przypisywanie wielu adresów IP do maszyn wirtualnych](virtual-network-multiple-ip-addresses-portal.md#os-config). Aby uzyskać szczególne uwagi przed ręcznym dodaniem adresów IP do systemu operacyjnego maszyny wirtualnej, zobacz [prywatne](#private) adresy IP. Nie należy dodawać żadnych publicznych adresów IP do systemu operacyjnego maszyny wirtualnej.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az sieć nic ip-config aktualizacja](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Usuwanie adresów IP

Można usunąć [prywatne](#private) i [publiczne](#public) adresy IP z interfejsu sieciowego, ale interfejs sieciowy musi zawsze mieć przypisany co najmniej jeden prywatny adres IPv4.

1. W polu zawierającym tekst *Zasoby wyszukiwania* u góry witryny Azure Portal wpisz *interfejsy sieciowe*. Gdy **interfejsy sieciowe** pojawiają się w wynikach wyszukiwania, wybierz je.
2. Wybierz interfejs sieciowy, który chcesz usunąć adresy IP z listy.
3. W obszarze **USTAWIENIA**wybierz pozycję **Konfiguracje IP**.
4. Wybierz prawym przyciskiem [wyboru pomocniczą](#secondary) konfigurację IP (nie można usunąć konfiguracji [podstawowej),](#primary) którą chcesz usunąć, wybierz pozycję **Usuń**, a następnie wybierz pozycję **Tak**, aby potwierdzić usunięcie. Jeśli konfiguracja miała skojarzony z nim publiczny zasób adresu IP, zasób jest odłączony od konfiguracji IP, ale zasób nie jest usuwany.

**Polecenia**

|Narzędzie|Polecenie|
|---|---|
|Interfejs wiersza polecenia|[az sieć nic ip-config usunąć](/cli/azure/network/nic/ip-config)|
|PowerShell|[Usuń-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>Konfiguracje adresów IP

[Prywatne](#private) i (opcjonalnie) [publiczne](#public) adresy IP są przypisywane do jednej lub więcej konfiguracji IP przypisanych do interfejsu sieciowego. Istnieją dwa typy konfiguracji adresów IP:

### <a name="primary"></a>Podstawowy

Każdemu interfejsowi sieciowego jest przypisana jedna podstawowa konfiguracja IP. Podstawowa konfiguracja ip:

- Ma przypisany [prywatny](#private) adres [IPv4.](#ipv4) Nie można przypisać prywatnego adresu [IPv6](#ipv6) do podstawowej konfiguracji IP.
- Może również mieć [przypisany publiczny](#public) adres IPv4. Nie można przypisać publicznego adresu IPv6 do podstawowej konfiguracji IP (IPv4). 

### <a name="secondary"></a>Pomocniczy

Oprócz podstawowej konfiguracji ip interfejs sieciowy może mieć przypisane do niego zero lub więcej pomocniczych konfiguracji IP. Pomocnicza konfiguracja IP:

- Musi mieć przypisany prywatny adres IPv4 lub IPv6. Jeśli adres jest IPv6, interfejs sieciowy może mieć tylko jedną pomocniczą konfigurację IP. Jeśli adres jest IPv4, interfejs sieciowy może mieć wiele dodatkowych konfiguracji IP przypisane do niego. Aby dowiedzieć się więcej o liczbie prywatnych i publicznych adresów IPv4 można przypisać do interfejsu sieciowego, zobacz artykuł [Limity platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)
- Może również mieć publiczny adres IPv4 lub IPv6 przypisany do niego. Przypisywanie wielu adresów IPv4 do interfejsu sieciowego jest przydatne w scenariuszach, takich jak:
  - Hostowanie wielu witryn sieci Web lub usług z różnymi adresami IP i certyfikatami SSL na jednym serwerze.
  - Maszyna wirtualna służąca jako wirtualne urządzenie sieciowe, takie jak zapora lub moduł równoważenia obciążenia.
  - Możliwość dodawania dowolnych prywatnych adresów IPv4 dla dowolnego interfejsu sieciowego do puli zaplecza modułu równoważenia obciążenia platformy Azure. W przeszłości tylko podstawowy adres IPv4 dla podstawowego interfejsu sieciowego można było dodać do puli zaplecza. Aby dowiedzieć się więcej o tym, jak równoważyć wiele konfiguracji IPv4, zobacz [bilansowanie obciążenia wielu konfiguracji IP.](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
  - Możliwość równoważenia obciążenia jednego adresu IPv6 przypisanego do interfejsu sieciowego. Aby dowiedzieć się więcej o tym, jak równoważyć obciążenie prywatnego adresu IPv6, zobacz artykuł [Adresy IPv6 równoważenia obciążenia.](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="address-types"></a>Typy adresów

Do konfiguracji IP można przypisać następujące typy adresów [IP:](#ip-configurations)

### <a name="private"></a>Private

Prywatne [adresy IPv4](#ipv4) lub IPv6 umożliwiają maszynie wirtualnej komunikowanie się z innymi zasobami w sieci wirtualnej lub innych połączonych sieciach. 

Domyślnie serwery DHCP platformy Azure przypisują prywatny adres IPv4 dla [podstawowej konfiguracji IP](#primary) interfejsu sieciowego platformy Azure do interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. O ile nie jest to konieczne, nigdy nie należy ręcznie ustawiać adresu IP interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej.

> [!WARNING]
> Jeśli adres IPv4 ustawiony jako podstawowy adres IP interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej jest zawsze inny niż prywatny adres IPv4 przypisany do podstawowej konfiguracji IP podstawowego interfejsu sieciowego podłączonego do maszyny wirtualnej na platformie Azure, tracisz łączność z maszyną wirtualną.

Istnieją scenariusze, w których konieczne jest ręczne ustawienie adresu IP interfejsu sieciowego w systemie operacyjnym maszyny wirtualnej. Na przykład należy ręcznie ustawić podstawowe i pomocnicze adresy IP systemu operacyjnego Windows podczas dodawania wielu adresów IP do maszyny wirtualnej platformy Azure. W przypadku maszyny wirtualnej systemu Linux może być konieczne ręczne ustawienie pomocniczych adresów IP. Szczegółowe informacje można znaleźć [w aplikacji Dodawanie adresów IP do systemu operacyjnego maszyny Wirtualnej.](virtual-network-multiple-ip-addresses-portal.md#os-config) Jeśli kiedykolwiek chcesz zmienić adres przypisany do konfiguracji IP, zaleca się:

1. Upewnij się, że maszyna wirtualna odbiera adres z serwerów DHCP platformy Azure. Po zakończeniu programu zmień przypisanie adresu IP z powrotem na usługę DHCP w systemie operacyjnym i uruchom ponownie maszynę wirtualną.
2. Zatrzymaj (cofnięto przydział) maszyny wirtualnej.
3. Zmień adres IP dla konfiguracji IP na platformie Azure.
4. Uruchom maszynę wirtualną.
5. [Ręcznie skonfiguruj pomocnicze](virtual-network-multiple-ip-addresses-portal.md#os-config) adresy IP w systemie operacyjnym (a także podstawowy adres IP w systemie Windows), aby dopasować je do tego, co ustawisz na platformie Azure.

Wykonując poprzednie kroki, prywatny adres IP przypisany do interfejsu sieciowego na platformie Azure i w systemie operacyjnym maszyny wirtualnej pozostaje taki sam. Aby śledzić, które maszyny wirtualne w ramach subskrypcji, które zostały ręcznie ustawić adresy IP w systemie operacyjnym dla, należy rozważyć dodanie [tagu](../azure-resource-manager/management/tag-resources.md) platformy Azure do maszyn wirtualnych. Możesz użyć na przykład "Przypisanie adresu IP: Statyczne". W ten sposób można łatwo znaleźć maszyny wirtualne w ramach subskrypcji, który został ręcznie ustawiony adres IP w systemie operacyjnym.

Oprócz włączania maszyny wirtualnej do komunikowania się z innymi zasobami w ramach tych samych lub połączonych sieci wirtualnych, prywatny adres IP umożliwia również maszynie wirtualnej komunikowanie się wychodzących do Internetu. Połączenia wychodzące są źródłowym adresem sieciowym przetłumaczonym przez platformę Azure na nieprzewidywalny publiczny adres IP. Aby dowiedzieć się więcej o wychodzącej łączności internetowej platformy Azure, przeczytaj artykuł [o wychodzącej łączności internetowej platformy Azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Nie można komunikować się przychodzące do prywatnego adresu IP maszyny wirtualnej z Internetu. Jeśli połączenia wychodzące wymagają przewidywalnego publicznego adresu IP, skojarz zasób publicznego adresu IP z interfejsem sieciowym.

### <a name="public"></a>Public

Publiczne adresy IP przypisane za pośrednictwem zasobu publicznego adresu IP umożliwiają łączność przychodzącą z maszyną wirtualną z Internetu. Połączenia wychodzące z Internetem używają przewidywalnego adresu IP. Aby uzyskać szczegółowe informacje, zobacz Opis połączeń wychodzących na [platformie Azure.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Można przypisać publiczny adres IP do konfiguracji IP, ale nie są wymagane. Jeśli nie przypiszesz publicznego adresu IP do maszyny wirtualnej, kojarząc publiczny zasób adresu IP, maszyna wirtualna nadal może komunikować się wychodzące z Internetem. W takim przypadku prywatny adres IP jest źródłowym adresem sieciowym przetłumaczonym przez platformę Azure na nieprzewidywalny publiczny adres IP. Aby dowiedzieć się więcej o zasobach publicznych adresów IP, zobacz [Zasób publicznego adresu IP](virtual-network-public-ip-address.md).

Istnieją ograniczenia liczby prywatnych i publicznych adresów IP, które można przypisać do interfejsu sieciowego. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [o limitach platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

> [!NOTE]
> Platforma Azure tłumaczy prywatny adres IP maszyny wirtualnej na publiczny adres IP. W rezultacie system operacyjny maszyny wirtualnej nie jest świadomy żadnego publicznego adresu IP przypisanego do niej, więc nie ma potrzeby ręcznego przypisywania publicznego adresu IP w systemie operacyjnym.

## <a name="assignment-methods"></a>Metody przypisywania

Publiczne i prywatne adresy IP są przypisywane przy użyciu jednej z następujących metod przypisywania:

### <a name="dynamic"></a>Dynamiczny

Domyślnie są przypisywane dynamiczne prywatne adresy IPv4 i IPv6 (opcjonalnie).

- **Tylko publiczne:** Platforma Azure przypisuje adres z zakresu unikatowego dla każdego regionu platformy Azure. Aby dowiedzieć się, które zakresy są przypisane do każdego regionu, zobacz [Zakresy adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adres można zmienić, gdy maszyna wirtualna jest zatrzymana (cofnięto alokację), a następnie uruchomiony ponownie. Nie można przypisać publicznego adresu IPv6 do konfiguracji IP przy użyciu żadnej z metod przypisania.
- **Tylko prywatne:** Platforma Azure rezerwuje pierwsze cztery adresy w każdym zakresie adresów podsieci i nie przypisuje adresów. Platforma Azure przypisuje następny dostępny adres do zasobu z zakresu adresów podsieci. Jeśli na przykład zakres adresów podsieci to 10.0.0.0/16, a adresy 10.0.0.0.4–10.0.0.14 zostały już przypisane (.0–.3 są zarezerwowane), platforma Azure przypisuje do zasobu adres 10.0.0.15. Metoda dynamiczna to domyślna metoda alokacji. Po przypisaniu dynamiczne adresy IP są zwalniane, tylko jeśli interfejs sieciowy zostanie usunięty, przypisany do innej podsieci w tej samej sieci wirtualnej, lub metoda alokacji zostanie zmieniona na Statyczna i zostanie podany inny adres IP. Domyślnie platforma Azure przypisuje poprzedni adres dynamicznie przypisany jako adres statyczny po zmianie metody alokacji z dynamicznej na statyczną. 

### <a name="static"></a>Statyczny

Można (opcjonalnie) przypisać publiczny lub prywatny statyczny adres IPv4 lub IPv6 do konfiguracji IP. Aby dowiedzieć się więcej o przypisywaniu przez platformę Azure statycznych publicznych adresów IPv4, zobacz [Publiczne adresy IP](virtual-network-public-ip-address.md).

- **Tylko publiczne:** Platforma Azure przypisuje adres z zakresu unikatowego dla każdego regionu platformy Azure. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064). Adres nie zmienia się, dopóki nie zostanie usunięty publiczny zasób adresu IP, do który jest przypisany, lub metoda przypisania zostanie zmieniona na dynamiczną. Jeśli publiczny zasób adresu IP jest skojarzony z konfiguracją IP, przed zmianą metody przypisania musi zostać odłączony od konfiguracji IP.
- **Tylko prywatne**: Wybierasz i przypisujesz adres z zakresu adresów podsieci. Możesz przypisać dowolny adres z zakresu adresów podsieci, który nie jest jednym z pierwszych czterech adresów w zakresie adresów podsieci i nie jest aktualnie przypisany do żadnego innego zasobu w podsieci. Adresy statyczne są zwalniane tylko w przypadku usunięcia interfejsu sieciowego. Jeśli zmienisz metodę alokacji na statyczną, platforma Azure dynamicznie przypisuje wcześniej przypisany dynamiczny adres IP jako adres statyczny, nawet jeśli adres nie jest następnym dostępnym adresem w zakresie adresów podsieci. Adres zmieni się także, jeśli interfejs sieciowy zostanie przypisany do innej podsieci w tej samej sieci wirtualnej, ale aby przypisać interfejs sieciowy do innej podsieci, musisz najpierw zmienić metodę alokacji ze statycznej na dynamiczną. Po przypisaniu interfejsu sieciowego do innej podsieci możesz zmienić metodę alokacji z powrotem na statyczną i przypisać adres IP z zakresu adresów nowej podsieci.

## <a name="ip-address-versions"></a>Wersje adresów IP

Podczas przypisywania adresów można określić następujące wersje:

### <a name="ipv4"></a>Protokół IPv4

Każdy interfejs sieciowy musi mieć jedną [podstawową](#primary) konfigurację IP z przypisanym [prywatnym](#private) adresem [IPv4.](#ipv4) Można dodać jedną lub więcej [dodatkowych](#secondary) konfiguracji IP, z których każda ma prywatny iPv4 i (opcjonalnie) [publiczny](#public) adres IP IPv4.

### <a name="ipv6"></a>Protokół IPv6

Do jednej pomocniczej konfiguracji ip interfejsu sieciowego można przypisać zero lub jeden prywatny adres [IPv6.](#ipv6) Interfejs sieciowy nie może mieć żadnych istniejących dodatkowych konfiguracji IP. Każdy interfejs sieciowy może mieć co najwyżej jeden adres prywatny IPv6. Opcjonalnie można dodać publiczny adres IPv6 do konfiguracji interfejsu sieciowego IPv6.

> [!NOTE]
> Chociaż można utworzyć interfejs sieciowy z adresem IPv6 za pomocą portalu, nie można dodać istniejącego interfejsu sieciowego do nowej lub istniejącej maszyny wirtualnej przy użyciu portalu. Użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby utworzyć interfejs sieciowy z prywatnym adresem IPv6, a następnie dołączyć interfejs sieciowy podczas tworzenia maszyny wirtualnej. Nie można dołączyć interfejsu sieciowego z prywatnym adresem IPv6 przypisanym do istniejącej maszyny wirtualnej. Nie można dodać prywatnego adresu IPv6 do konfiguracji IP dla dowolnego interfejsu sieciowego podłączonego do maszyny wirtualnej przy użyciu dowolnych narzędzi (portal, interfejs wiersza polecenia lub programu PowerShell).

Nie można przypisać publicznego adresu IPv6 do podstawowej lub pomocniczej konfiguracji IP.

## <a name="skus"></a>Jednostki SKU

Publiczny adres IP jest tworzony przy podstawowych lub standardowych jednostek SKU. Aby uzyskać więcej informacji na temat różnic jednostek SKU, zobacz [Zarządzanie publicznymi adresami IP](virtual-network-public-ip-address.md).

> [!NOTE]
> Po przypisaniu standardowego publicznego adresu IP jednostki SKU do interfejsu sieciowego maszyny wirtualnej musisz jawnie zezwolić na ruch do miejsca przeznaczenia przy użyciu [sieciowej grupy zabezpieczeń](security-overview.md#network-security-groups). Próba komunikacji z zasobem będzie kończyć się niepowodzeniem do momentu utworzenia i skojarzenia sieciowej grupy zabezpieczeń, a następnie jawnego zezwolenia na żądany ruch.

## <a name="next-steps"></a>Następne kroki
Aby utworzyć maszynę wirtualną z różnymi konfiguracjami adresów IP, przeczytaj następujące artykuły:

|Zadanie|Narzędzie|
|---|---|
|Tworzenie maszyny wirtualnej z wieloma interfejsami sieciowymi|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Program PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Tworzenie pojedynczej maszyny wirtualnej karty sieciowej z wieloma adresami IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [Program PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Tworzenie pojedynczej maszyny Wirtualnej karty sieciowej z prywatnym adresem IPv6 (za modułem równoważenia obciążenia platformy Azure)|[Szablon interfejsu wiersza polecenia](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [programu PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [usługi Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
