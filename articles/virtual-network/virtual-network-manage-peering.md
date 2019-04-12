---
title: Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej.
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
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: fdc3a0030859e97cb81b8b9f6a66de1901b6eb3b
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491291"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej

Dowiedz się, jak tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej. Komunikacja równorzędna sieci wirtualnych umożliwia łączenie sieci wirtualnych w tym samym regionie i w różnych regionach (znany także jako globalnych wirtualnych sieci równorzędnych) za pośrednictwem sieci szkieletowej platformy Azure. Po nawiązaniu komunikacji równorzędnej, sieci wirtualne są nadal zarządzane jako oddzielne zasoby. Jeśli dopiero zaczynasz pracę z wirtualnymi sieciami równorzędnymi, możesz dowiedzieć się więcej o nim w [Omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md) lub wykonując [samouczek](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zasubskrybować [konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta które ma [niezbędne uprawnienia](#permissions) do pracy za pomocą komunikacji równorzędnej.
- Jeśli za pomocą poleceń programu PowerShell w celu wykonania zadań w tym artykule, albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/powershell), lub korzystając z polecenia programu PowerShell na komputerze. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 1.0.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, trzeba będzie również uruchomić `Connect-AzAccount` za pomocą konta mającego [niezbędne uprawnienia](#permissions) do pracy za pomocą komunikacji równorzędnej, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń interfejsu wiersza polecenia platformy Azure (CLI) w celu wykonania zadań w tym artykule albo Uruchom polecenia [usługi Azure Cloud Shell](https://shell.azure.com/bash), lub korzystając z polecenia interfejsu wiersza polecenia na komputerze. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure lokalnie, trzeba będzie również uruchomić `az login` za pomocą konta mającego [niezbędne uprawnienia](#permissions) do pracy za pomocą komunikacji równorzędnej, aby utworzyć połączenie z platformą Azure.

Konta, zaloguj się do lub łączenie z platformą Azure za pomocą, muszą być przypisane do [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) roli lub [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany odpowiednie działania, które są wymienione w [uprawnień ](#permissions).

## <a name="create-a-peering"></a>Utworzyć połączenie równorzędne

Zanim utworzysz komunikację równorzędną, zapoznaj się z wymagań i ograniczeń i [niezbędne uprawnienia](#permissions).

1. W polu wyszukiwania w górnej części witryny Azure portal wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją. Nie należy wybierać **sieci wirtualne (klasyczne)** Jeśli pojawia się na liście, ponieważ nie można utworzyć komunikację równorzędną z sieci wirtualnej, która jest wdrażana za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, który chcesz utworzyć komunikację równorzędną.
3. W obszarze **ustawienia**, wybierz opcję **komunikacje równorzędne**.
4. Wybierz pozycję **+ Dodaj**. 
5. <a name="add-peering"></a>Wprowadź lub wybierz wartości dla następujących ustawień:
    - **Nazwa:** Nazwa dla komunikacji równorzędnej musi być unikatowa w obrębie sieci wirtualnej.
    - **Model wdrożenia sieci wirtualnej:** Wybierz model wdrażania, których wdrożono sieć wirtualną, którą chcesz nawiązać komunikację równorzędną za pośrednictwem.
    - **Wiem, identyfikator zasobu:** Jeśli masz dostęp do odczytu do sieci wirtualnej, którą chcesz nawiązać komunikację równorzędną, zaznaczaj tego pola wyboru. Jeśli nie masz dostęp do odczytu do sieci wirtualnej lub subskrypcji, którą chcesz nawiązać komunikację równorzędną, zaznacz to pole wyboru. Wprowadź pełen identyfikator zasobu sieci wirtualnej, o którym chcesz nawiązać komunikację równorzędną w **identyfikator zasobu** pole, które wystąpiły, gdy zaznaczono pole. Zasobów, możesz wprowadzić identyfikator musi być znajdującą się w tej samej sieci wirtualnej lub [obsługiwane różne](#requirements-and-constraints) Azure [region](https://azure.microsoft.com/regions) jako tej sieci wirtualnej. Pełny identyfikator zasobu jest podobny do /subscriptions/<Id>/providers/Microsoft.Network/virtualNetworks/ /resourceGroups/ < — nazwa grupy zasobów-> < nazwa wirtualnej sieci >. Możesz uzyskać identyfikator zasobu dla sieci wirtualnej, wyświetlając właściwości dla sieci wirtualnej. Aby dowiedzieć się, jak wyświetlić właściwości dla sieci wirtualnej, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md#view-virtual-networks-and-settings). Jeśli subskrypcja jest skojarzona z innej dzierżawy usługi Azure Active Directory niż subskrypcji z tworzenia komunikacji równorzędnej z siecią wirtualną, najpierw dodać użytkownika z każdej dzierżawy jako [użytkownik-Gość](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w przeciwny dzierżawy.
    - **Subskrypcja:** Wybierz [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) chcesz nawiązać komunikację równorzędną sieci wirtualnej. Co najmniej jedną subskrypcją są wyświetlane w zależności od tego, jak wiele subskrypcji Twoje konto ma uprawnienia do odczytu. Jeśli zaznaczono **identyfikator zasobu** pole wyboru, to ustawienie nie jest dostępna.
    - **Sieć wirtualna:** Wybierz sieć wirtualną, którą chcesz nawiązać komunikację równorzędną. Możesz wybrać sieci wirtualnej utworzonej za pomocą dowolnego modelu wdrażania na platformie Azure. Jeśli chcesz wybrać sieć wirtualną w innym regionie, musisz wybrać sieć wirtualną w [obsługiwany region](#cross-region). Musi mieć dostęp do odczytu do sieci wirtualnej, aby była widoczna na liście. Jeśli sieć wirtualna jest na liście, ale wygaszone, może to być, ponieważ przestrzeń adresowa sieci wirtualnej nakłada się na przestrzeń adresową dla tej sieci wirtualnej. Jeśli przestrzenie adresowe sieci wirtualnych nakładają się na siebie, nie mogą one być skomunikowane równorzędnie. Jeśli zaznaczono **identyfikator zasobu** pole wyboru, to ustawienie nie jest dostępna.
    - **Zezwalaj na dostęp do sieci wirtualnej:** Wybierz **włączone** (ustawienie domyślne), jeśli chcesz umożliwić komunikację między dwiema sieciami wirtualnymi. Włączenie komunikacji między sieciami wirtualnymi umożliwia zasobami połączonymi do dowolnej sieci wirtualnej do komunikowania się ze sobą przy użyciu tego samego przepustowości i opóźnienia, tak, jakby były one połączone z tej samej sieci wirtualnej. Cała komunikacja między zasobami w dwóch sieci wirtualnych jest za pośrednictwem sieci prywatnej platformy Azure. **VirtualNetwork** tag usługi dla sieciowych grup zabezpieczeń obejmuje sieć wirtualną i wirtualnej sieci równorzędnej. Aby dowiedzieć się więcej na temat tagów usługi grupy zabezpieczeń sieci, zobacz [omówienie sieciowych grup zabezpieczeń](security-overview.md#service-tags). Wybierz **wyłączone** Jeśli nie chcesz, aby przepływ ruchu do wirtualnej sieci równorzędnej. Możesz wybrać **wyłączone** Jeśli została równorzędnie sieci wirtualnej z inną siecią wirtualną, ale czasami chcesz wyłączyć przepływ ruchu między dwiema sieciami wirtualnymi. Może się okazać że Włączanie/wyłączanie jest bardziej wygodne niż usunięcie i ponowne utworzenie komunikacji równorzędnej. Gdy to ustawienie jest wyłączone, ruch nie przechodzi między równorzędnymi sieciami wirtualnymi.
    - **Zezwalaj na ruch przesłany dalej:** Zaznacz to pole, aby zezwolić na ruch *przekazywane* przez wirtualne urządzenie sieciowe w sieci wirtualnej (który nie pochodzi z sieci wirtualnej) do usługi flow z tą siecią wirtualną za pośrednictwem komunikacji równorzędnej. Na przykład należy wziąć pod uwagę trzy wirtualne sieci o nazwie szprychy 1, Spoke2 i koncentratora. Komunikację równorzędną między każdej szprysze sieci wirtualnej i sieci wirtualnej koncentratora istnieje, ale komunikacja równorzędna nie istnieją między sieciami wirtualnymi szprychy. Wirtualne urządzenie sieciowe jest wdrożony w centralnej sieci wirtualnej, a trasy zdefiniowane przez użytkownika są stosowane do każdej szprychy sieci wirtualnej, która kierować ruchem między podsieciami za pośrednictwem wirtualnego urządzenia sieciowego. Jeśli to pole wyboru nie zostanie zaznaczona dla komunikacji równorzędnej między każdej szprysze sieci wirtualnej i sieci wirtualnej koncentratora, ruch nie przechodzi między sieciami wirtualnymi szprychy, ponieważ Centrum nie przekazuje ruch między sieciami wirtualnymi. Podczas włączania tej funkcji zezwala na ruch przesłany dalej za pośrednictwem komunikacji równorzędnej, nie tworzy się wszystkie trasy zdefiniowane przez użytkownika lub wirtualnych urządzeń sieciowych. Trasy zdefiniowane przez użytkownika i wirtualnych urządzeń sieciowych są tworzone oddzielnie. Dowiedz się więcej o [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md#user-defined). Nie trzeba sprawdzić to ustawienie, jeśli ruch jest przesyłany dalej między sieciami wirtualnymi za pośrednictwem bramy sieci VPN platformy Azure.
    - **Zezwalaj na tranzyt bramy:** Zaznacz to pole wyboru, jeśli masz bramy sieci wirtualnej, dołączonych do tej sieci wirtualnej i chcesz zezwolić na ruch z równorzędnej sieci wirtualnej, które będą przepływać za pośrednictwem bramy. Na przykład ta sieć wirtualna może być podłączona do sieci lokalnej za pośrednictwem bramy sieci wirtualnej. Brama może być brama usługi ExpressRoute lub sieci VPN. Zaznaczenie tego pola wyboru zezwala na ruch z wirtualnej sieci równorzędnej przepływu za pośrednictwem bramy, dołączone do tej sieci wirtualnej do sieci lokalnej. Jeśli zaznaczysz to pole, równorzędnej sieci wirtualnej nie może mieć skonfigurowaną bramę. Musi mieć wirtualnej sieci równorzędnej **Użyj bram zdalnych** zaznaczone pole wyboru, podczas konfigurowania komunikacji równorzędnej z sieci wirtualnej z tą siecią wirtualną. Jeśli pozostawisz to niezaznaczone pole (ustawienie domyślne), ruch z przepływów nadal równorzędnej sieci wirtualnej z tą siecią wirtualną, ale nie można wykonać przepływu za pośrednictwem bramy sieci wirtualnej, dołączonych do tej sieci wirtualnej. W przypadku komunikacji równorzędnej między sieci wirtualnej (Resource Manager) i sieci wirtualnej (model klasyczny), brama musi być w sieci wirtualnej (Resource Manager). Nie można włączyć tę opcję, jeśli w przypadku komunikacji równorzędnej sieci wirtualnych w różnych regionach.

       Oprócz przekazywania ruchu w sieci lokalnej, bramy sieci VPN może przekazywać ruch sieciowy między sieciami wirtualnymi jest połączona z siecią wirtualną, w której znajduje się brama, bez konieczności zostać połączona ze sobą sieci wirtualnych. Przesyłanie ruchu sieciowego przy użyciu bramy sieci VPN jest przydatne, gdy chcesz używać bramy sieci VPN w z koncentratorem (Zobacz przykład gwiazdy opisane dla **Zezwalaj na przekazywany ruch**) sieci wirtualnej, aby kierować ruchem między sieciami wirtualnymi szprych, które nie są połączona ze sobą. Aby dowiedzieć się więcej na temat pozwalające na używanie bramy działało, zobacz [Konfigurowanie bramy sieci VPN działało w wirtualnej sieci równorzędnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten scenariusz wymaga wykonania trasy zdefiniowane przez użytkownika, które określają bramy sieci wirtualnej jako typ następnego przeskoku. Dowiedz się więcej o [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md#user-defined). Jako typ następnego przeskoku w trasie zdefiniowanej przez użytkownika można określić tylko bramy VPN gateway, brama usługi ExpressRoute nie można określić jako typu następnego przeskoku w trasie zdefiniowanej przez użytkownika. Nie można włączyć tę opcję, jeśli w przypadku komunikacji równorzędnej sieci wirtualnych w różnych regionach.

    - **Użyj bram zdalnych:** Zaznacz to pole, aby zezwolić na ruch z tej sieci wirtualnej mają przepływać za pośrednictwem bramy sieci wirtualnej podłączone do sieci wirtualnej jest zaglądanie z. Na przykład wybrana sieć wirtualna jest zaglądanie z ma dołączone bramy sieci VPN, która umożliwia komunikację z siecią lokalną.  Zaznaczenie tego pola wyboru zezwala na ruch z tej sieci wirtualnej mają przepływać za pośrednictwem bramy sieci VPN, dołączone do równorzędnej sieci wirtualnej. Jeśli zaznaczysz to pole, wirtualnej sieci równorzędnej musi mieć bramę sieci wirtualnej podłączone do niego i musi mieć **Zezwalaj na tranzyt bramy** zaznaczone pole wyboru. Jeśli pole to pozostanie nie zaznaczone (ustawienie domyślne), ruch z wirtualnej sieci równorzędnej nadal mogą przepływać z tą siecią wirtualną, ale nie flow za pośrednictwem bramy sieci wirtualnej dołączyć do tym wirtualnych sieci.
    Tylko jeden element równorzędny dla tej sieci wirtualnej może mieć to ustawienie jest włączone.

        Nie można użyć bram zdalnych, jeśli masz już skonfigurowaną bramę w sieci wirtualnej. Nie można włączyć tę opcję, jeśli w przypadku komunikacji równorzędnej sieci wirtualnych w różnych regionach. Aby dowiedzieć się więcej na temat za pomocą bramy działało, zobacz [skonfigurować bramy sieci VPN na tranzyt w komunikacji równorzędnej sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

6. Wybierz **OK** można dodać komunikacji równorzędnej w sieci wirtualnej, które zostały wybrane.

Instrukcje krok po kroku dotyczące implementowania komunikację równorzędną między sieciami wirtualnymi w różnych subskrypcjach i modele wdrażania, zobacz [następne kroki](#next-steps).

### <a name="commands"></a>Polecenia

- **Interfejs wiersza polecenia Azure**: [tworzenie az sieci wirtualne sieci równorzędne](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Wyświetl lub zmień ustawienia komunikacji równorzędnej

Przed zmianą, komunikację równorzędną, zapoznaj się z wymagań i ograniczeń i [niezbędne uprawnienia](#permissions).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją. Nie należy wybierać **sieci wirtualne (klasyczne)** Jeśli pojawia się na liście, ponieważ nie można utworzyć komunikację równorzędną z sieci wirtualnej, która jest wdrażana za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, który chcesz zmienić ustawienia komunikacji równorzędnej.
3. W obszarze **ustawienia**, wybierz opcję **komunikacje równorzędne**.
4. Wybierz, komunikację równorzędną, którą chcesz wyświetlić lub zmienić ustawienia.
5. Zmień odpowiednie ustawienia. Przeczytaj o opcje dla każdego ustawienia w [kroku 5](#add-peering) polecenia Create komunikację równorzędną.
6. Wybierz pozycję **Zapisz**.

**Polecenia**

- **Wiersza polecenia platformy Azure**: [az listę komunikacji równorzędnej sieci wirtualnej sieci](/cli/azure/network/vnet/peering) do listy połączeń komunikacji równorzędnej sieci wirtualnej, [az network show komunikacji równorzędnej sieci wirtualnej](/cli/azure/network/vnet/peering) wyświetlanie ustawień dla określonych komunikacji równorzędnej i [az sieci Aktualizacja komunikacji równorzędnej sieci wirtualnej](/cli/azure/network/vnet/peering) zmiany ustawień komunikacji równorzędnej. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) można pobrać ustawień komunikacji równorzędnej widoku i [AzVirtualNetworkPeering zestaw](/powershell/module/az.network/set-azvirtualnetworkpeering) Aby zmienić ustawienia.

## <a name="delete-a-peering"></a>Usuwanie komunikacji równorzędnej

Przed usunięciem, komunikację równorzędną, upewnij się, Twoje konto ma [niezbędne uprawnienia](#permissions).

Gdy komunikacja równorzędna zostanie usunięty, ruch z sieci wirtualnej są już przekazywane do równorzędnej sieci wirtualnej. Po równorzędnym połączeniu sieci wirtualnych wdrożonych przy użyciu usługi Resource Manager, każda sieć wirtualna ma komunikację równorzędną w sieci wirtualnej. Chociaż usuwanie komunikacji równorzędnej z jednej sieci wirtualnej spowoduje wyłączenie komunikacji między sieciami wirtualnymi, nie zostanie usunięta komunikacja równorzędna z innej sieci wirtualnej. Stan komunikacji równorzędnej dla komunikacji równorzędnej, który znajduje się w innej sieci wirtualnej jest **Disconnected**. Nie można odtworzyć komunikacji równorzędnej do momentu ponownego tworzenia komunikacji równorzędnej w pierwszej sieci wirtualnej i stan komunikacji równorzędnej dla zmiany w obu sieciach wirtualnych *połączono*.

Jeśli chcesz, aby sieci wirtualne do komunikowania się czasami, ale nie zawsze zamiast usuwanie komunikacji równorzędnej, możesz ustawić **zezwolić na dostęp do sieci wirtualnej** ustawienie **wyłączone** zamiast tego. Aby dowiedzieć się, jak to zrobić, przeczytaj kroku 6 procedury tworzenia komunikacji równorzędnej dalszej części tego artykułu. Może się okazać wyłączania i włączania dostępu do sieci łatwiejsze niż usunięcie i ponowne utworzenie komunikacji równorzędnej.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualne** są wyświetlane w wynikach wyszukiwania, wybierz ją. Nie należy wybierać **sieci wirtualne (klasyczne)** Jeśli pojawia się na liście, ponieważ nie można utworzyć komunikację równorzędną z sieci wirtualnej, która jest wdrażana za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, który chcesz usunąć komunikację równorzędną.
3. W obszarze **ustawienia**, wybierz opcję **komunikacje równorzędne**.
4. Po prawej stronie komunikacji równorzędnej, aby usunąć, wybierz **...** , wybierz opcję **Usuń**, a następnie wybierz **tak** można usunąć komunikacji równorzędnej z pierwszej sieci wirtualnej.
5. Wykonaj poprzednie kroki, aby usunąć komunikację równorzędną z innych sieci wirtualnych w komunikacji równorzędnej.

**Polecenia**

- **Interfejs wiersza polecenia Azure**: [usuwanie komunikacji równorzędnej sieci wirtualnej sieci az](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Wymagania i ograniczenia

- <a name="cross-region"></a>Można nawiązać komunikację równorzędną między sieciami wirtualnymi w tym samym regionie lub w różnych regionach. Komunikacja równorzędna sieci wirtualnych w różnych regionach jest również nazywany *globalnych wirtualnych sieci równorzędnych*. 
- Podczas tworzenia, globalnej komunikacji równorzędnej, wirtualne sieci równorzędne może znajdować się w regionie chmury publicznej platformy Azure lub regionach chmury w Chinach dowolnej regionów chmury dla instytucji rządowych. Nie można komunikacji równorzędnej między różnymi chmurami. Na przykład sieci wirtualnej w chmurze publicznej platformy Azure nie może być skomunikowane równorzędnie z siecią wirtualną w chmurze Azure (Chiny).
- Zasoby w jednej sieci wirtualnej nie może komunikować się z adresu IP frontonu podstawowe wewnętrznego modułu równoważenia obciążenia globalne równorzędne sieci wirtualnych. Obsługa podstawowego modułu równoważenia obciążenia istnieje tylko w obrębie tego samego regionu. Obsługa standardowego modułu równoważenia obciążenia dla istnieje, wirtualnych sieci równorzędnych i globalnych wirtualnych sieci równorzędnych.
- Można użyć bram zdalnych lub Zezwalaj na tranzyt bramy na globalne równorzędne sieci wirtualne i wirtualne sieci równorzędne lokalnie.
- Sieci wirtualne mogą być w tej samej lub innej subskrypcji. Gdy możesz nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych subskrypcjach, obie subskrypcje można skojarzyć z tej samej lub innej dzierżawy usługi Azure Active Directory. Jeśli nie masz już dzierżawę usługi AD, możesz to zrobić [utworzyć](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant). Obsługa komunikacja równorzędna między sieciami wirtualnymi o subskrypcji skojarzonych z różnych dzierżaw usługi Azure Active Directory nie jest dostępne w portalu. Można użyć interfejsu wiersza polecenia, programu PowerShell i szablonów.
- Sieci wirtualne, z którymi możesz komunikacji równorzędnej musi mieć nienakładające się przestrzenie adresów IP.
- Nie można dodać zakresy adresów do lub usuwanie zakresów adresów z przestrzeni adresowej sieci wirtualnej, gdy sieć wirtualna a jest połączona z inną siecią wirtualną. Aby dodać lub usunąć zakresów adresów, usuwanie komunikacji równorzędnej, dodać lub usunąć zakresów adresów, następnie ponownie utworzyć komunikację równorzędną. Aby zakresy adresów, aby dodać lub usunąć zakresów adresów z sieciami wirtualnymi, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md).
- Można nawiązać komunikację równorzędną między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem Menedżera zasobów i wirtualna sieć wdrożone za pomocą usługi Resource Manager z siecią wirtualną, która jest wdrażana za pośrednictwem klasycznego modelu wdrażania. Nie można nawiązać komunikację równorzędną między dwiema sieciami wirtualnymi utworzonymi za pomocą klasycznego modelu wdrażania. Jeśli nie znasz modele wdrażania Azure, zapoznaj się z [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu. Do połączenia dwóch sieci wirtualnych utworzonych za pomocą klasycznego modelu wdrażania można użyć usługi [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
- W przypadku łączenia dwóch sieci wirtualnych utworzonych w usłudze Resource Manager za pomocą komunikacji równorzędnej należy skonfigurować komunikację równorzędną dla każdej objętej nią sieci wirtualnej. Zostanie wyświetlony jeden z następujących typów dla stan komunikacji równorzędnej: 
  - *Zainicjowano:* Po utworzeniu komunikacji równorzędnej do drugiej sieci wirtualnej z pierwszej sieci wirtualnej stan komunikacji równorzędnej to *zainicjowano*. 
  - *Połączone:* Po utworzeniu komunikacji równorzędnej z drugą siecią wirtualną do pierwszej sieci wirtualnej stan komunikacji równorzędnej jest *połączono*. Jeśli wyświetlisz stan komunikacji równorzędnej dla pierwszej sieci wirtualnej, zobacz zmienił się z *zainicjowano* do *połączono*. Komunikacja równorzędna nie zostanie pomyślnie nawiązana do momentu stanem połączeń obydwu sieci wirtualnych *połączono*.
- Podczas komunikacji równorzędnej sieci wirtualnej utworzonej przy użyciu usługi Resource Manager przy użyciu sieci wirtualnej utworzonej za pomocą klasycznego modelu wdrażania, możesz skonfigurować tylko komunikacji równorzędnej sieci wirtualnej wdrożony za pomocą usługi Resource Manager. Nie można skonfigurować komunikację równorzędną sieci wirtualnej (model klasyczny) lub między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. Podczas tworzenia komunikacji równorzędnej z sieci wirtualnej (Resource Manager) do sieci wirtualnej (wersja klasyczna), stan komunikacji równorzędnej to *aktualizowanie*, wkrótce zmieni się *połączono*.
- Komunikacja równorzędna zostanie nawiązana między dwiema sieciami wirtualnymi. Komunikacja równorzędna nie są przechodnie. Jeśli utworzysz komunikację równorzędną między:
  - Virtualnetwork1 przy & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Nie ma żadnych równorzędne virtualnetwork1 przy i VirtualNetwork3 za pośrednictwem VirtualNetwork2. Jak utworzyć wirtualną sieć równorzędną między virtualnetwork1 przy i VirtualNetwork3, musisz utworzyć komunikację równorzędną między virtualnetwork1 przy i VirtualNetwork3.
- Nie można rozpoznać nazwy w wirtualnych sieciach równorzędnych przy użyciu rozpoznawania nazw platformy Azure w domyślnej. Rozpoznawanie nazw w innych sieciach wirtualnych, należy użyć [usługi Azure DNS dla domen prywatnych](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub niestandardowy serwer DNS. Aby dowiedzieć się, jak skonfigurować serwer DNS, zobacz [rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Zasoby w sieciach wirtualnych w tym samym regionie mogą komunikować się ze sobą przy użyciu tego samego przepustowości i opóźnienia tak, jakby znajdowały się w tej samej sieci wirtualnej. Każdego rozmiaru maszyny wirtualnej ma własną maksymalną przepustowość sieci, jednak. Aby dowiedzieć się więcej o maksymalnej przepustowości dla różnych rozmiarów maszyn wirtualnych, zobacz [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiarów maszyn wirtualnych.
- Sieć wirtualna może być połączona z inną siecią wirtualną, a także podłączyć do innej sieci wirtualnej z bramą sieci wirtualnej platformy Azure. Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej i bramy, ruch między sieciami wirtualnymi odbywa się za pośrednictwem bramy, a nie konfiguracji komunikacji równorzędnej.
- Musi zostać ponownie pobrana klientów sieci VPN typu punkt-lokacja, po komunikacji równorzędnej sieci wirtualnej został pomyślnie skonfigurowany, aby upewnić się, że nowe trasy są pobierane do klienta.
- Istnieje nominalna opłata za ruch przychodzący i wychodzący w wirtualnych sieciach równorzędnych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Uprawnienia

Konta, na których możesz użyć do pracy za pomocą komunikacji równorzędnej sieci wirtualnej muszą być przypisane do następujących ról:

- [Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Dla sieci wirtualnej wdrożony za pomocą usługi Resource Manager.
- [Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): Dla sieci wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania.

Jeśli Twoje konto nie jest przypisana do jednego z poprzednich ról, musi ona zostać przypisana do [roli niestandardowej](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) przypisany niezbędne czynności z poniższej tabeli:

| Akcja                                                          | Name (Nazwa) |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Wymagane do utworzenia komunikacji równorzędnej z siecią wirtualną A siecią wirtualną B. wirtualnych sieci A musi być sieci wirtualnej (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Wymagane do utworzenia komunikacji równorzędnej z siecią wirtualną B (Resource Manager) z siecią wirtualną A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer                   | Wymagane do utworzenia komunikacji równorzędnej z siecią wirtualną B (model klasyczny) z siecią wirtualną A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Przeczytaj, wirtualne sieci równorzędne   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Usuwanie komunikacji równorzędnej sieci wirtualnej |

## <a name="next-steps"></a>Kolejne kroki

- Równorzędne sieci wirtualne tworzy się między sieciami wirtualnymi utworzonymi za pomocą tych samych lub różnych modeli wdrażania istniejących w tej samej lub w różnych subskrypcjach. Ukończ samouczek dla jednego z następujących scenariuszy:

  |Model wdrażania platformy Azure             | Subskrypcja  |
  |---------                          |---------|
  |Resource Manager — w obu przypadkach              |[Ten sam](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Różne](create-peering-different-subscriptions.md)|
  |Jedna sieć — Resource Manager, druga — model klasyczny  |[Ten sam](create-peering-different-deployment-models.md)|
  |                                   |[Różne](create-peering-different-deployment-models-subscriptions.md)|

- Dowiedz się jak, utworzyć [topologię sieciową typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie wirtualnej sieci równorzędnej przy użyciu [PowerShell](powershell-samples.md) lub [wiersza polecenia platformy Azure](cli-samples.md) przykładowe skrypty lub korzystanie z platformy Azure [szablonów usługi Resource Manager](template-samples.md)
- Tworzenie i stosowanie [usługa Azure policy](policy-samples.md) dla sieci wirtualnych
