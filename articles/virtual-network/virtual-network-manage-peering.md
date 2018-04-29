---
title: Tworzenie, zmienianie lub usuwanie równorzędna sieci wirtualnej platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć, zmienić lub usunąć sieci wirtualnej komunikacji równorzędnej.
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
ms.date: 02/09/2018
ms.author: jdial;anavin
ms.openlocfilehash: 52c910609930bbeecd21b75549c71ee9ed4e1e3b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Tworzenie, zmienianie lub usunąć sieci wirtualnej komunikacji równorzędnej

Dowiedz się, jak utworzyć, zmienić lub usunąć sieci wirtualnej komunikacji równorzędnej. Sieci wirtualnej komunikacji równorzędnej umożliwia łączenie z sieciami wirtualnymi w sieci Azure sieci szkieletowej. Po połączyć za pomocą, sieci wirtualne są nadal zarządzane jako oddzielne zasoby. Jeśli nie masz doświadczenia w obsłudze sieci wirtualnej komunikacji równorzędnej, zaleca się odczytu [komunikacji równorzędnej omówienie sieci wirtualnej](virtual-network-peering-overview.md) i wykonując [tworzenie sieci wirtualnej komunikacji równorzędnej samouczek](tutorial-connect-virtual-networks-portal.md), przed wykonaniem zadania w tym artykule.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed wykonaniem kroków w żadnej sekcji tego artykułu, należy wykonać następujące zadania:

- Jeśli nie masz jeszcze konta platformy Azure, należy zarejestrować się w celu [bezpłatnego konta wersji próbnej](https://azure.microsoft.com/free).
- Jeśli przy użyciu portalu, otwórz https://portal.azure.comi zaloguj się przy użyciu konta, które ma [niezbędne uprawnienia](#permissions) do pracy z komunikacji równorzędnych.
- Jeśli za pomocą poleceń programu PowerShell do wykonywania zadań w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/powershell), lub przez uruchomienie programu PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga programu Azure PowerShell w wersji modułu 5.5.0 lub nowszym. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). Jeśli używasz programu PowerShell lokalnie, należy uruchomić `Connect-AzureRmAccount` przy użyciu konta, które ma [niezbędne uprawnienia](#permissions) do pracy z komunikacji równorzędnej, aby utworzyć połączenie z platformą Azure.
- Jeśli za pomocą poleceń Azure interfejsu wiersza polecenia (CLI), aby wykonać zadania w tym artykule, albo Uruchom polecenia w [powłoki chmury Azure](https://shell.azure.com/bash), lub za pomocą interfejsu wiersza polecenia z tego komputera. Ten samouczek wymaga wiersza polecenia platformy Azure w wersji 2.0.29 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia Azure lokalnie, należy uruchomić `az login` przy użyciu konta, które ma [niezbędne uprawnienia](#permissions) do pracy z komunikacji równorzędnej, aby utworzyć połączenie z platformą Azure.

## <a name="create-a-peering"></a>Utwórz element równorzędny

Przed utworzeniem komunikacji równorzędnej, warto zapoznać się z [wymagań i ograniczeń](#requirements-and-contstraints) i [niezbędne uprawnienia](#permissions).

1. W polu wyszukiwania w górnej części portalu Azure, wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go. Nie zaznaczaj **sieci wirtualnych (klasyczne)** Jeśli wygląda na to, na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz z listy, który chcesz utworzyć komunikacji równorzędnej dla sieci wirtualnej.
3. Z listy sieci wirtualnych wybierz chcesz utworzyć komunikacji równorzędnej dla sieci wirtualnej.
4. W obszarze **ustawienia**, wybierz pozycję **komunikacji równorzędnych**.
5. Wybierz pozycję **+ Dodaj**. 
6. <a name="add-peering"></a>Wprowadź lub wybierz wartości poniższych ustawień:
    - **Nazwa:** nazwa dla komunikacji równorzędnej musi być unikatowa w ramach sieci wirtualnej.
    - **Model wdrażania sieci wirtualnej:** wybierz sieci wirtualnej, aby równorzędny model wdrożenia, które zostało wdrożone za pośrednictwem.
    - **Sprawdzić mój identyfikator zasobu:** Jeśli masz dostęp do odczytu do sieci wirtualnej, aby równorzędny, pozostaw to pole wyboru niezaznaczone. Jeśli nie masz dostęp do odczytu do sieci wirtualnej lub subskrypcji, który chcesz równorzędny, zaznacz to pole wyboru. Wprowadź pełny identyfikator zasobu sieci wirtualnej, aby równorzędny w **identyfikator zasobu** pola, które wystąpiły, gdy zaznaczono pole. Zasób, możesz wprowadzić identyfikator musi być sieci wirtualnej, który istnieje w tej samej lub [obsługiwane różne](#requirements-and-constraints) Azure [region](https://azure.microsoft.com/regions) jako tej sieci wirtualnej. Pełny identyfikator zasobu wygląda podobnie do /subscriptions/<Id>/providers/Microsoft.Network/virtualNetworks/ <-nazwa grupy zasobów-> /resourceGroups/ < nazwa wirtualnej sieci >. Identyfikator zasobu można uzyskać sieci wirtualnej, wyświetlając właściwości dla sieci wirtualnej. Aby dowiedzieć się wyświetlić właściwości dla sieci wirtualnej, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md#view-virtual-networks-and-settings).
    - **Subskrypcja:** wybierz [subskrypcji](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) chcesz elementu równorzędnego z sieci wirtualnej. Co najmniej jedna subskrypcja znajdują się w zależności od tego, jak wiele subskrypcji Twoje konto ma uprawnienia do odczytu. Jeśli zaznaczono **identyfikator zasobu** pole wyboru, to ustawienie jest niedostępne.
    - **Sieć wirtualna:** wybierz sieć wirtualną, aby równorzędny. Możesz wybrać sieć wirtualną, która została utworzona za pośrednictwem albo modelu wdrożenia usługi Azure. Jeśli chcesz wybrać sieć wirtualną w innym regionie, należy wybrać sieć wirtualną w [obsługiwany region](#cross-region). Musi mieć dostęp do odczytu do sieci wirtualnej, aby były widoczne na liście. Jeśli sieć wirtualna jest na liście, ale wygaszone, prawdopodobnie przestrzeni adresowej dla sieci wirtualnej nakłada się przestrzeni adresowej tej sieci wirtualnej. Jeśli nakładania się przestrzenie adresowe sieci wirtualnej, ich nie można połączyć za pomocą. Jeśli zaznaczono **identyfikator zasobu** pole wyboru, to ustawienie jest niedostępne.
    - **Zezwalaj na dostęp do sieci wirtualnej:** wybierz **włączone** (ustawienie domyślne), jeśli chcesz umożliwić komunikację między dwiema sieciami wirtualnymi. Włączenie komunikacji między sieciami wirtualnymi umożliwia zasoby podłączone do jednej sieci wirtualnej do komunikowania się ze sobą przy tym samym przepustowości i opóźnień, jakby były one podłączone do tej samej sieci wirtualnej. Cała komunikacja między zasobami w dwóch sieci wirtualnych jest za pośrednictwem sieci prywatnej platformy Azure. **VirtualNetwork** domyślne znaczniki dla grup zabezpieczeń sieci obejmuje sieci wirtualnej i połączyć za pomocą sieci wirtualnej. Aby dowiedzieć się więcej na temat znaczników domyślnych grup zabezpieczeń sieci, przeczytaj [Przegląd grup zabezpieczeń sieci](virtual-networks-nsg.md#default-tags) artykułu.  Wybierz **wyłączone** Jeśli nie chcesz, aby przepływ ruchu do peered sieci wirtualnej. Możesz wybrać pozycję **wyłączone** już połączyć za pomocą sieci wirtualnej z inną siecią wirtualną, ale czasami chcesz wyłączyć przepływu ruchu między dwiema sieciami wirtualnymi. Może się okazać się, że włączenie/wyłączenie funkcji jest wygodniejsze niż usunięcia i ponownego utworzenia komunikacji równorzędnych. Gdy to ustawienie jest wyłączone, ruch nie przepływa między sieciami wirtualnymi peered.
    - **Zezwalaj na związanego z przekazywaniem ruchu:** zaznacz to pole, aby zezwolić na ruch *przekazywane* przez urządzenie wirtualne sieci w sieci wirtualnej (który nie pochodzi z sieci wirtualnej) przepływem z tą siecią wirtualną za pomocą komunikacji równorzędnej . Rozważmy na przykład trzech sieci wirtualne o nazwach Spoke1, Spoke2 i koncentratora. Komunikacji równorzędnej między każdym gwiazdy sieci wirtualnej i sieci wirtualnej w Centrum istnieje, ale komunikacji równorzędnych nie istnieją między sieciami wirtualnymi gwiazdy. Urządzenie wirtualne sieci zostało wdrożone w Centrum sieci wirtualnej, a trasy zdefiniowane przez użytkownika są stosowane do każdej sieci wirtualnej gwiazdy, który kierować ruchem między podsieciami przez urządzenie wirtualne sieci. Jeśli to pole wyboru nie zostanie zaznaczona dla komunikacji równorzędnej między każdym gwiazdy sieci wirtualnej i siecią wirtualną koncentratora, ruch nie przepływa między sieciami wirtualnymi gwiazdy ponieważ koncentratora jest przesyłanie ruchu między sieciami wirtualnymi. Chociaż ruchu przekazywane przez komunikację równorzędną pozwala na włączenie tej funkcji, nie powoduje utworzenia trasy zdefiniowane przez użytkownika ani wirtualnych urządzeń sieciowych. Trasy zdefiniowane przez użytkownika i wirtualnych urządzeń sieciowych są tworzone oddzielnie. Dowiedz się więcej o [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md#user-defined). Nie należy zaznaczać tego ustawienia, jeśli ruch jest przesyłany dalej między sieciami wirtualnymi za pośrednictwem bramy sieci VPN platformy Azure.
    - **Zezwalaj na bramy przesyłania:** zaznacz to pole wyboru, jeśli masz bramy sieci wirtualnej, dołączony do tej sieci wirtualnej i chcesz zezwolić na ruch z sieci wirtualnej peered przepływają przez bramę. Na przykład ta sieć wirtualna może zostać dołączony do sieci lokalnej za pośrednictwem bramy sieci wirtualnej. Brama może być bramę usługi ExpressRoute lub sieci VPN. Zaznaczenie tego pola wyboru zezwala na ruch z sieci wirtualnej peered przepływ za pośrednictwem bramy dołączony do tej sieci wirtualnej do sieci lokalnej. Jeśli zaznaczysz to pole peered sieci wirtualnej nie może mieć skonfigurowaną bramę. Peered sieć wirtualna musi mieć **użycie bram zdalnego** zaznaczone pole wyboru, podczas konfigurowania komunikacji równorzędnej z innych sieci wirtualnej z tą siecią wirtualną. Pozostawienie to niezaznaczone pole (ustawienie domyślne), ruch z przepływem wciąż połączyć za pomocą sieci wirtualnej z tą siecią wirtualną, ale nie można wykonać przepływu za pośrednictwem bramy sieci wirtualnej dołączony do tej sieci wirtualnej. 
    
        Oprócz przesyłania ruchu do sieci lokalnej, Brama sieci VPN może przekazywać ruch sieciowy między sieciami wirtualnymi, które są połączyć za pomocą z siecią wirtualną, której bramy, bez konieczności można połączyć ze sobą za pomocą sieci wirtualnych. Jest to przydatne, jeśli chcesz użyć bramy sieci VPN w koncentratora (zapoznaj się z przykładem gwiazdy opisane dla **Zezwalaj przekazywane ruchu**) sieci wirtualnej można kierować ruchem między sieciami wirtualnymi gwiazdy, które nie są połączyć ze sobą za pomocą. Dowiedz się więcej o [bram sieci wirtualnej](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti). Ten scenariusz wymaga wykonania trasy zdefiniowane przez użytkownika, określające bramy sieci wirtualnej jako typ następnego przeskoku. Dowiedz się więcej o [trasy zdefiniowane przez użytkownika](virtual-networks-udr-overview.md#user-defined). Brama sieci VPN można określić tylko jako typ następnego przeskoku w trasy zdefiniowane przez użytkownika, bramę usługi ExpressRoute nie można określić jako typ następnego przeskoku w trasy zdefiniowane przez użytkownika.

        Nie można włączyć tę opcję, jeśli w przypadku komunikacji równorzędnej sieć wirtualną (Resource Manager) z siecią wirtualną (klasyczny). Chociaż ruch przepływa między dwiema sieciami wirtualnymi, (klasyczne) ruchu w sieci wirtualnej nie może przepływać przez bramę sieci dołączony do sieci wirtualnej (Resource Manager). Nie można włączyć tę opcję, jeśli w przypadku komunikacji równorzędnej sieci wirtualnych w różnych regionach.

    - **Użyj bramy zdalnego:** zaznacz to pole, aby zezwolić na ruch z tą siecią wirtualną przepływają przez bramę sieci wirtualnej dołączony do sieci wirtualnej jest równorzędna z. Na przykład sieci wirtualnej, który jest równorzędna z ma bramy sieci VPN dołączony umożliwiający komunikację z siecią lokalną.  Zaznaczenie tego pola wyboru zezwala na ruch z tą siecią wirtualną przepływ za pośrednictwem bramy sieci VPN, dołączony do peered sieci wirtualnej. Jeśli zaznaczysz to pole musi mieć do niego dołączony bramę sieci wirtualnej peered sieci wirtualnej i musi mieć **Zezwalaj bramy przesyłania** zaznaczono element checkbox. Jeśli pole to unchecked (ustawienie domyślne), ruch z sieci wirtualnej peered nadal mogą przepływać do tej sieci wirtualnej, ale nie może przepływu za pośrednictwem bramy sieci wirtualnej dołączony do tego wirtualnego sieci. 
    Tylko jeden komunikacji równorzędnej dla tej sieci wirtualnej może mieć to ustawienie jest włączone.

        Nie można używać zdalnego bramy, jeśli masz już skonfigurowano w Twojej sieci wirtualnej bramę.
        Nie można włączyć tę opcję, jeśli w przypadku komunikacji równorzędnej sieć wirtualną (Resource Manager) z siecią wirtualną (klasyczny). Chociaż ruch przepływa między dwiema sieciami wirtualnymi, wirtualnych ruchu sieciowego (Resource Manager) nie można wykonać przepływu przez bramę sieci dołączony do sieci wirtualnej (wdrożenia klasyczne). Nie można włączyć tę opcję, jeśli w przypadku komunikacji równorzędnej sieci wirtualnych w różnych regionach.

7. Wybierz **OK** można dodać równorzędna do wybrania sieci wirtualnej.

Aby uzyskać instrukcje krok po kroku dotyczące implementowania komunikacji równorzędnej między sieciami wirtualnymi w różnych subskrypcji i modeli wdrażania, zobacz [następne kroki](#next-steps). 


### <a name="commands"></a>Polecenia

- **Azure CLI**: [równorzędna az sieci wirtualne sieci równorzędne — tworzenie](/cli/azure/network/vnet/peering#create)
- **PowerShell**: [Add-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/add-azurermvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Wyświetl lub zmień ustawienia komunikacji równorzędnej

Przed wprowadzeniem zmian komunikacji równorzędnej, warto zapoznać się z [wymagań i ograniczeń](#requirements-and-contstraints) i [niezbędne uprawnienia](#permissions).

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go. Nie zaznaczaj **sieci wirtualnych (klasyczne)** Jeśli wygląda na to, na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, który chcesz zmienić ustawienia komunikacji równorzędnej.
3. Wybierz sieci wirtualnej, aby zmienić ustawienia komunikacji równorzędnej z listy sieci wirtualnych.
4. W obszarze **ustawienia**, wybierz pozycję **komunikacji równorzędnych**.
5. Wybierz komunikacji równorzędnej, który chcesz wyświetlić lub zmienić ustawienia.
6. Zmień odpowiednie ustawienia. Przeczytaj informacje o opcje dla każdego ustawienia w [krok 6](#add-peering) Utwórz komunikacji równorzędnej. 
7. Wybierz pozycję **Zapisz**.

**Polecenia**

- **Azure CLI**: [az sieci wirtualnej komunikacji równorzędnej listy](/cli/azure/network/vnet/peering#az_network_vnet_peering_list) do komunikacji równorzędnych listy sieci wirtualnej [az sieci vnet show komunikacji równorzędnej](/cli/azure/network/vnet/peering#az_network_vnet_peering_show) do wyświetlenia określonej komunikacji równorzędnej, ustawienia i [az sieci sieci wirtualnej komunikacji równorzędnej aktualizacji](/cli/azure/network/vnet/peering#az_network_vnet_peering_update) Aby zmienić ustawienia komunikacji równorzędnej. |
- **PowerShell**: [Get-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering) można pobrać ustawień komunikacji równorzędnej widoku i [AzureRmVirtualNetworkPeering zestaw](/powershell/module/azurerm.network/set-azurermvirtualnetworkpeering) Aby zmienić ustawienia.

## <a name="delete-a-peering"></a>Usuń element równorzędny

Przed usunięciem komunikacji równorzędnej, upewnij się, konto użytkownika ma [niezbędne uprawnienia](#permissions).

Po usunięciu komunikacji równorzędnej, ruch z sieci wirtualnej jest już przepływ peered sieci wirtualnej. Sieci wirtualne są wdrażane za pomocą Menedżera zasobów są połączyć za pomocą, każdej sieci wirtualnej ma równorzędna do innych sieci wirtualnej. Chociaż usuwanie komunikacji równorzędnej z jedną sieć wirtualną wyłączenie komunikacji między sieciami wirtualnymi, nie są usuwane komunikacji równorzędnej z innych sieci wirtualnej. Jest w stanie komunikacji równorzędnej dla komunikacji równorzędnej, który istnieje w sieci wirtualnej **Rozłączono**. Nie można odtworzyć komunikację równorzędną dopóki nie zostaną utworzone ponownie komunikację równorzędną w pierwszej sieci wirtualnej i stanu komunikacji równorzędnej dla obu zmiany sieci wirtualnych *połączony*. 

Jeśli chcesz sieci wirtualne do komunikowania się czasami, ale nie zawsze, zamiast usuwania komunikacji równorzędnej, można ustawić **Zezwalaj na dostęp do sieci wirtualnej** ustawienie **wyłączone** zamiast tego. Aby dowiedzieć się, jak to zrobić, przeczytaj kroku 6 procedury [utworzyć komunikacji równorzędnej](#create-peering) sekcji tego artykułu. Może się okazać wyłączania i włączania łatwiejsze niż usunięcie i ponowne utworzenie komunikacji równorzędnych dostępu do sieci.

1. W polu wyszukiwania w górnej części portalu wprowadź *sieci wirtualnych* w polu wyszukiwania. Gdy **sieci wirtualnych** pojawia się w wynikach wyszukiwania, wybierz go. Nie zaznaczaj **sieci wirtualnych (klasyczne)** Jeśli wygląda na to, na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz z listy, który chcesz usunąć komunikacji równorzędnej dla sieci wirtualnej.
3. Z listy sieci wirtualnych wybierz chcesz usunąć komunikacji równorzędnej dla sieci wirtualnej.
4. W obszarze **ustawienia**, wybierz pozycję **komunikacji równorzędnych**.
5. Po prawej stronie w komunikacji równorzędnej, aby usunąć wybierz **...** , wybierz pozycję **usunąć**, a następnie wybierz pozycję **tak** można usunąć komunikacji równorzędnej z pierwszej sieci wirtualnej.
6. Wykonaj poprzednie kroki, aby usunąć komunikacji równorzędnej z innych sieci wirtualnej w komunikacji równorzędnej.

**Polecenia**

- **Azure CLI**: [az sieci wirtualnej komunikacji równorzędnej usunięcie](/cli/azure/network/vnet/peering#az_network_vnet_peering_delete)
- **PowerShell**: [Remove-AzureRmVirtualNetworkPeering](/powershell/module/azurerm.network/remove-azurermvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Wymagania i ograniczenia 

- Sieci wirtualne, które możesz elementu równorzędnego musi mieć-nakładającymi się obszarami adresów IP.
- Nie można dodać zakresów adresów, lub Usuń zakresy adresów z przestrzeń adresową sieci wirtualnej, po sieci wirtualnej jest połączyć z inną siecią wirtualną za pomocą. Aby dodać lub usunąć zakresy adresów, usunąć komunikację równorzędną, dodać lub usunąć zakresów adresów, następnie utworzyć je ponownie komunikację równorzędną. Aby zakresów adresów, aby dodać lub usunąć zakresy adresów sieci wirtualnej, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md).
- Można równorzędne dwie sieci wirtualne wdrożone za pośrednictwem Menedżera zasobów lub sieci wirtualnej wdrożone za pomocą Menedżera zasobów z siecią wirtualną wdrożone za pośrednictwem klasycznego modelu wdrażania. Nie można równorzędne dwie sieci wirtualne utworzone za pośrednictwem klasycznego modelu wdrażania. Jeśli nie masz doświadczenia w obsłudze modele wdrażania platformy Azure, przeczytaj [modele wdrażania zrozumieć Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artykułu. Do połączenia dwóch sieci wirtualnych utworzonych za pomocą klasycznego modelu wdrażania można użyć usługi [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
- W przypadku łączenia dwóch sieci wirtualnych utworzonych w usłudze Resource Manager za pomocą komunikacji równorzędnej należy skonfigurować komunikację równorzędną dla każdej objętej nią sieci wirtualnej. Zostanie wyświetlony jeden z następujących typów komunikacji równorzędnej stanu: 
    - *Zainicjowano:* podczas tworzenia równorzędna do drugiej sieci wirtualnej z pierwszej sieci wirtualnej, stan komunikacji równorzędnej jest *zainicjowano*. 
    - *Połączony:* podczas tworzenia komunikacji równorzędnej z drugiej sieci wirtualnej do pierwszej sieci wirtualnej, jego stan komunikacji równorzędnej jest *połączony*. Jeśli możesz wyświetlić stan komunikacji równorzędnej dla pierwszej sieci wirtualnej, zobacz jego stan zmienił się z *zainicjowano* do *połączony*. Komunikację równorzędną nie zostanie pomyślnie nawiązane momentu komunikacji równorzędnej stan dla obu komunikacji równorzędnych sieci wirtualnych *połączony*.
- Podczas komunikacji równorzędnej sieci wirtualnej utworzonej za pomocą Menedżera zasobów z siecią wirtualną, która została utworzona za pośrednictwem klasycznego modelu wdrażania, można skonfigurować tylko komunikacji równorzędnej dla sieci wirtualnej wdrożone za pomocą Menedżera zasobów. Nie można skonfigurować komunikację równorzędną sieci wirtualnej (wdrożenia klasyczne) lub między dwiema sieciami wirtualnej wdrożone za pośrednictwem klasycznego modelu wdrażania. Podczas tworzenia komunikacji równorzędnej z sieci wirtualnej (Resource Manager) do sieci wirtualnej (klasyczne), stan komunikacji równorzędnej jest *aktualizacji*, wkrótce zmienia się *połączony*.
- Komunikacja równorzędna została ustanowiona między dwiema sieciami wirtualnymi. Komunikacji równorzędnych nie są przechodnie. Jeśli utworzysz komunikacji równorzędnych między:
    - VirtualNetwork1 & VirtualNetwork2
    - VirtualNetwork2 & VirtualNetwork3

  Nie ma żadnych komunikacji równorzędnej między VirtualNetwork1 i VirtualNetwork3 za pośrednictwem VirtualNetwork2. Jeśli chcesz utworzyć sieć wirtualną komunikacji równorzędnej między VirtualNetwork1 i VirtualNetwork3, należy utworzyć komunikacji równorzędnej między VirtualNetwork1 i VirtualNetwork3.
- Nie można rozpoznać nazwy w połączyć za pomocą sieci wirtualnych za pomocą domyślnego rozwiązania nazwa platformy Azure. Aby rozpoznawanie nazw w innych sieciach wirtualnych, należy użyć [usługi Azure DNS dla domen prywatnej](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub niestandardowy serwer DNS. Aby dowiedzieć się, jak skonfigurować serwer DNS, zobacz [rozpoznawanie nazw przy użyciu serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Zasoby w połączyć za pomocą sieci wirtualne w tym samym regionie może komunikować się ze sobą przy tym samym przepustowości i opóźnień tak, jakby były w tej samej sieci wirtualnej. Rozmiar każdej maszyny wirtualnej ma jednak własną maksymalną przepustowość sieci. Aby dowiedzieć się więcej na temat maksymalną przepustowość sieci dla maszyny wirtualnej różnych rozmiarów, zobacz [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiarów maszyn wirtualnych.
- Subskrypcje, które są obie sieci wirtualne, które mają być elementów równorzędnych, musi być skojarzony z tej samej dzierżawy usługi Azure Active Directory. Jeśli nie masz już dzierżawę AD, możesz szybko [utworzyć](../active-directory/develop/active-directory-howto-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json#create-a-new-azure-ad-tenant). Można użyć [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) Aby połączyć dwie sieci wirtualne, które istnieją w ramach różnych subskrypcji skojarzonych z różnych dzierżawców usługi Active Directory.
- Sieć wirtualną można połączyć za pomocą do innej sieci wirtualnej, a także być podłączony do innej sieci wirtualnej z bramą sieci wirtualnej platformy Azure. Jeśli sieci wirtualne są połączone za pośrednictwem komunikacji równorzędnej i bramy, ruchu między sieciami wirtualnymi przechodzi przez konfiguracji komunikacji równorzędnej, a nie bramy.
- Istnieje nominalna opłata za ruch przychodzący i wychodzący w wirtualnych sieciach równorzędnych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/virtual-network).
* <a name="cross-region"></a>Można elementów równorzędnych sieci wirtualnych w tym samym regionie lub różnych regionach. Następujące ograniczenia nie są stosowane, gdy obie sieci wirtualne są *tego samego* region, ale są stosowane w przypadku sieci wirtualnych globalnie są połączyć za pomocą: 
    - Sieci wirtualne mogą istnieć tylko w następujących regionach: zachodnie centralnej nam (Wyoming), zachodnie stany USA 2 (Waszyngton) środkowe stany USA (Iowa), nam wschodnie 2 (Virginia), centralnej Kanada (naszej), Wschodnia Kanada (Quebec miejscowości), Azja południowo-wschodnia (Singapur) Południowej Korei (Buscan), południe Indie (Chennai), (Pune) Indie środkowe, Indie Zachodnie (Bombaj), UK południe (Londynie), UK zachód (Cardiff), Europa Zachodnia (Holandia)
    - Zasoby w jednej sieci wirtualnej nie może komunikować się z adresem IP Azure wewnętrznego modułu równoważenia obciążenia w sieci wirtualnej peered. Moduł równoważenia obciążenia i zasobów, które komunikują się z nim muszą być w tej samej sieci wirtualnej.
    - Nie można użyć bramy zdalnego lub zezwolić przesyłania bramy. Aby użyć bramy zdalnego lub zezwolić przesyłania bramy, obie sieci wirtualne w komunikacji równorzędnej musi istnieć w tym samym regionie. 

## <a name="permissions"></a>Uprawnienia

Konta, które służy do tworzenia sieci wirtualnej komunikacji równorzędnej musi mieć ról lub uprawnień. Na przykład, jeśli są równorzędna dwie sieci wirtualnej o nazwie *myVnetA* i *myVnetB*, konto musi mieć przypisaną następującą minimalną rolę lub uprawnienia dla każdej sieci wirtualnej:
    
|Sieć wirtualna|Model wdrażania|Rola|Uprawnienia|
|---|---|---|---|
|myVnetA|Resource Manager|[Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
| |Wdrożenie klasyczne|[Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|ND|
|myVnetB|Resource Manager|[Współautor sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|
||Wdrożenie klasyczne|[Współautor klasycznej sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)|Microsoft.ClassicNetwork/virtualNetworks/peer|

Dowiedz się więcej o [wbudowane role](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) i przypisywanie określonych uprawnień do [role niestandardowe](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (tylko Resource Manager).

## <a name="next-steps"></a>Kolejne kroki

* Równorzędne sieci wirtualne tworzy się między sieciami wirtualnymi utworzonymi za pomocą tych samych lub różnych modeli wdrażania istniejących w tej samej lub w różnych subskrypcjach. Ukończ samouczek dla jednego z następujących scenariuszy:

    |Model wdrażania platformy Azure             | Subskrypcja  |
    |---------                          |---------|
    |Resource Manager — w obu przypadkach              |[Ta sama](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Różne](create-peering-different-subscriptions.md)|
    |Jedna sieć — Resource Manager, druga — model klasyczny  |[Ta sama](create-peering-different-deployment-models.md)|
    |                                   |[Różne](create-peering-different-deployment-models-subscriptions.md)|

* Dowiedz się jak, utworzyć [topologię sieciową typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual network-peering).
