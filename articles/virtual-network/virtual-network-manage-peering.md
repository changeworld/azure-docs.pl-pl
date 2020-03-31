---
title: Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć, zmienić lub usunąć komunikację równorzędną sieci wirtualnej.
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
ms.date: 04/01/2019
ms.author: anavin
ms.openlocfilehash: 97acac61d0397a4e13fb64d39a6aba92e4de2afd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80123306"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej

Dowiedz się, jak utworzyć, zmienić lub usunąć komunikację równorzędną sieci wirtualnej. Komunikacja równorzędna sieci wirtualnej umożliwia łączenie sieci wirtualnych w tym samym regionie i w różnych regionach (nazywanych również globalną komunikacją równorzędną sieci wirtualnej) za pośrednictwem sieci szkieletowej platformy Azure. Po równorzędnym sieciach wirtualnych nadal zarządza się jako oddzielne zasoby. Jeśli jesteś nowy w komunikacji równorzędnej sieci wirtualnej, możesz dowiedzieć się więcej na ten temat w [przeglądzie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md) lub wypełniając [samouczek](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wykonaj następujące zadania przed wykonaniem kroków w dowolnej sekcji tego artykułu:

- Jeśli nie masz jeszcze konta platformy Azure, zarejestruj się, aby uzyskać [bezpłatne konto próbne.](https://azure.microsoft.com/free)
- Jeśli korzystasz z https://portal.azure.comportalu, otwórz i zaloguj się przy użyciu konta, które ma [uprawnienia niezbędne](#permissions) do pracy z komunikacją równorzędną.
- Jeśli do wykonywania zadań w tym artykule są używane polecenia programu PowerShell, należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/powershell)lub uruchomić program PowerShell z komputera. Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. Ten samouczek wymaga modułu programu Azure PowerShell w wersji 1.0.0 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps). Jeśli używasz programu PowerShell lokalnie, `Connect-AzAccount` należy również uruchomić z kontem, które ma [uprawnienia niezbędne](#permissions) do pracy z komunikacją równorzędną, aby utworzyć połączenie z platformą Azure.
- Jeśli do wykonywania zadań w tym artykule przy użyciu poleceń interfejsu wiersza polecenia platformy Azure należy uruchomić polecenia w [usłudze Azure Cloud Shell](https://shell.azure.com/bash)lub uruchomić interfejs wiersza polecenia z komputera. Ten samouczek wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest zainstalowana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli korzystasz z interfejsu wiersza polecenia platformy `az login` Azure lokalnie, musisz również uruchomić z kontem, które ma [uprawnienia niezbędne](#permissions) do pracy z komunikacją równorzędną, aby utworzyć połączenie z platformą Azure.

Konto, do którego się logujesz lub z którą łączysz się z platformą Azure, musi być przypisane do roli [współautora sieci](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) lub do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) której przypisano odpowiednie akcje wymienione w [sekcji Uprawnienia](#permissions).

## <a name="create-a-peering"></a>Tworzenie komunikacji równorzędnej

Przed utworzeniem komunikacji równorzędnej zapoznaj się z wymaganiami i ograniczeniami oraz [niezbędnymi uprawnieniami](#permissions).

1. W polu wyszukiwania u góry witryny Azure portal wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je. Nie należy **wybierać sieci wirtualnych (klasyczne),** jeśli są wyświetlane na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożonej za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, dla której chcesz utworzyć komunikację równorzędną.
3. W obszarze **USTAWIENIA**wybierz **pozycję Peerings**.
4. Wybierz pozycję **+ Dodaj**. 
5. <a name="add-peering"></a>Wprowadź lub wybierz wartości dla następujących ustawień:
    - **Nazwa:** Nazwa komunikacji równorzędnej musi być unikatowa w sieci wirtualnej.
    - **Model wdrażania sieci wirtualnej:** Wybierz model wdrażania, za pomocą którego sieci wirtualnej chcesz równorzędnie.
    - **Znam identyfikator zasobu:** Jeśli masz dostęp do odczytu do sieci wirtualnej, z którą chcesz się równorzędnie, pozostaw to pole wyboru niezaznaczone. Jeśli nie masz dostępu do odczytu do sieci wirtualnej lub subskrypcji, z którą chcesz się równorzędnie, zaznacz to pole wyboru. Wprowadź pełny identyfikator zasobu sieci wirtualnej, z którą chcesz się równorzędnie, w polu **Identyfikator zasobu,** który pojawił się po zaznaczeniu tego pola. Wprowadzony identyfikator zasobu musi być dla sieci wirtualnej, która istnieje w tym samym lub [obsługiwane w innym](#requirements-and-constraints) [regionie](https://azure.microsoft.com/regions) platformy Azure jako tej sieci wirtualnej. Pełny identyfikator zasobu wygląda `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`podobnie do . Identyfikator zasobu dla sieci wirtualnej można uzyskać, wyświetlając właściwości sieci wirtualnej. Aby dowiedzieć się, jak wyświetlić właściwości sieci wirtualnej, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md#view-virtual-networks-and-settings). Jeśli subskrypcja jest skojarzona z inną dzierżawą usługi Azure Active Directory niż subskrypcja z siecią wirtualną, z której tworzysz komunikację równorzędną, najpierw dodaj użytkownika z każdej dzierżawy jako [użytkownika-gościa](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) w przeciwnej dzierżawie.
    - **Subskrypcja:** Wybierz [subskrypcję](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) sieci wirtualnej, z którą chcesz się równorzędnie. W zależności od liczby subskrypcji, do których twoje konto ma dostęp do odczytu, wyświetlana jest co najmniej jedna subskrypcja. Jeśli zaznaczyliśmy pole wyboru **Identyfikator zasobu,** to ustawienie jest niedostępne.
    - **Sieć wirtualna:** Wybierz sieć wirtualną, z którą chcesz się równorzędnie. Można wybrać sieć wirtualną utworzoną za pomocą modelu wdrażania platformy Azure. Aby wybrać sieć wirtualną w innym regionie, należy wybrać sieć wirtualną w [obsługiwanym regionie](#cross-region). Musisz mieć dostęp do odczytu do sieci wirtualnej, aby była widoczna na liście. Jeśli sieć wirtualna jest wymieniona, ale wyszarzone, może to być spowodowane tym, że przestrzeń adresowa sieci wirtualnej pokrywa się z przestrzenią adresową dla tej sieci wirtualnej. Jeśli przestrzenie adresowe sieci wirtualnej nakładają się na siebie, nie można ich równorzędnie. Jeśli zaznaczyliśmy pole wyboru **Identyfikator zasobu,** to ustawienie jest niedostępne.
    - **Zezwalaj na dostęp do sieci wirtualnej:** Wybierz **enabled** (default), jeśli chcesz włączyć komunikację między dwiema sieciami wirtualnymi. Włączenie komunikacji między sieciami wirtualnymi umożliwia zasobom podłączonym do jednej z sieci wirtualnych komunikowanie się ze sobą o takiej samej przepustowości i opóźnieniu, jak gdyby były połączone z tą samą siecią wirtualną. Cała komunikacja między zasobami w dwóch sieciach wirtualnych odbywa się za pośrednictwem sieci prywatnej platformy Azure. Tag usługi **VirtualNetwork** dla grup zabezpieczeń sieci obejmuje sieć wirtualną i sieć wirtualną. Aby dowiedzieć się więcej o tagach usług sieciowej grupy zabezpieczeń, zobacz [Omówienie sieciowych grup zabezpieczeń](security-overview.md#service-tags). Wybierz **opcję Wyłączone,** jeśli nie chcesz, aby ruch przepływał do sieci wirtualnej równorzędnej. Możesz wybrać **Wyłączone,** jeśli masz równorzędną sieć wirtualną z inną siecią wirtualną, ale od czasu do czasu chcesz wyłączyć przepływ ruchu między dwiema sieciami wirtualnymi. Włączenie/wyłączenie może okazać się wygodniejsze niż usuwanie i ponowne tworzenie komunikacji równorzędnej. Gdy to ustawienie jest wyłączone, ruch nie przepływa między sieciami wirtualnymi równorzędnych.
    - **Zezwalaj na ruch przesyłany dalej:** Zaznacz to pole, aby umożliwić przepływ ruchu *przesyłanego* dalej przez wirtualne urządzenie sieciowe w sieci wirtualnej (która nie pochodzi z sieci wirtualnej) do tej sieci wirtualnej za pośrednictwem komunikacji równorzędnej. Rozważmy na przykład trzy sieci wirtualne o nazwie Spoke1, Spoke2 i Hub. Komunikacja równorzędna istnieje między każdą siecią wirtualną szprychy a siecią wirtualną centrum, ale komunikacja równorzędna nie istnieje między sieciami wirtualnymi szprych. Wirtualne urządzenie sieciowe jest wdrażane w sieci wirtualnej Centrum, a trasy zdefiniowane przez użytkownika są stosowane do każdej sieci wirtualnej szprychy, która kieruje ruch między podsieciami za pośrednictwem wirtualnego urządzenia sieciowego. Jeśli to pole wyboru nie jest zaznaczone pod kątem komunikacji równorzędnej między każdą siecią wirtualną szprychy a siecią wirtualną koncentratora, ruch nie przepływa między sieciami wirtualnymi szprychowymi, ponieważ koncentrator nie przekazuje ruchu między sieciami wirtualnymi. Podczas włączania tej funkcji umożliwia ruch przesyłany dalej za pośrednictwem komunikacji równorzędnej, nie tworzy żadnych tras zdefiniowanych przez użytkownika lub wirtualnych urządzeń sieciowych. Trasy zdefiniowane przez użytkownika i wirtualne urządzenia sieciowe są tworzone oddzielnie. Dowiedz się więcej o [trasach zdefiniowanych przez użytkownika](virtual-networks-udr-overview.md#user-defined). Nie trzeba sprawdzać tego ustawienia, jeśli ruch jest przekazywał dalej między sieciami wirtualnymi za pośrednictwem bramy sieci VPN platformy Azure.
    - **Zezwalaj na przesyłanie bramy:** Zaznacz to pole wyboru, jeśli do tej sieci wirtualnej jest dołączona brama sieci wirtualnej i chcesz zezwolić na przepływ ruchu z sieci wirtualnej równorzędnej przez bramę. Na przykład ta sieć wirtualna może być dołączona do sieci lokalnej za pośrednictwem bramy sieci wirtualnej. Brama może być bramą usługi ExpressRoute lub VPN. Zaznaczenie tego pola umożliwia przepływ ruchu z sieci wirtualnej równorzędnej przez bramę podłączoną do tej sieci wirtualnej do sieci lokalnej. Jeśli zaznaczysz to pole wyboru, sieć wirtualna równorzędna nie może mieć skonfigurowanej bramy. Podczas konfigurowania komunikacji równorzędnej z innej sieci wirtualnej do tej sieci wirtualnej należy zaznaczyć pole wyboru **Użyj bram zdalnych.** Jeśli to pole nie jest zaznaczone (domyślnie), ruch z sieci wirtualnej równorzędnej nadal przepływa do tej sieci wirtualnej, ale nie może przepływać przez bramę sieci wirtualnej podłączoną do tej sieci wirtualnej. Jeśli komunikacja równorzędna znajduje się między siecią wirtualną (Menedżer zasobów) a siecią wirtualną (klasyczna), brama musi znajdować się w sieci wirtualnej (Menedżer zasobów).

       Oprócz przekazywania ruchu do sieci lokalnej brama sieci VPN może przesyłać dalej ruch sieciowy między sieciami wirtualnymi, które są równorzędne z siecią wirtualną, w której znajduje się brama, bez konieczności równorzędnego korzystania z sieci wirtualnych. Używanie bramy sieci VPN do przekazywania ruchu jest przydatne, gdy chcesz użyć bramy sieci VPN w centrum (zobacz przykład koncentratora i argumentów opisany dla Zezwalaj na **ruch przesyłany dalej)** w celu kierowania ruchu między sieciami wirtualnymi, które nie są ze sobą równorzędne. Aby dowiedzieć się więcej na temat zezwalania na korzystanie z bramy do przesyłania, zobacz [Konfigurowanie bramy sieci VPN do przesyłania w komunikacji równorzędnej sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). W tym scenariuszu wymaga implementacji tras zdefiniowanych przez użytkownika, które określają bramę sieci wirtualnej jako następny typ przeskoku. Dowiedz się więcej o [trasach zdefiniowanych przez użytkownika](virtual-networks-udr-overview.md#user-defined). Bramę sieci VPN można określić tylko jako następny typ przeskoku w marszruty zdefiniowanej przez użytkownika, nie można określić bramy usługi ExpressRoute jako następnego typu przeskoku w marszruty zdefiniowanej przez użytkownika.

    - **Użyj bram zdalnych:** Zaznacz to pole wyboru, aby umożliwić przepływ ruchu z tej sieci wirtualnej przez bramę sieci wirtualnej podłączoną do sieci wirtualnej, z którą się równorzędnie. Na przykład sieć wirtualna, z którą współwłasno-komunikacyjna jest dołączona brama sieci VPN, która umożliwia komunikację z siecią lokalną.  Zaznaczenie tego pola umożliwia przepływ ruchu z tej sieci wirtualnej przez bramę sieci VPN dołączoną do sieci wirtualnej równorzędnej. Jeśli zaznaczysz to pole wyboru, sieć wirtualna w trybie równorzędnym musi być do niej dołączona i musi mieć zaznaczone pole wyboru **Zezwalaj na przesyłanie bramy.** Jeśli to pole nie jest zaznaczone (domyślnie), ruch z sieci wirtualnej równorzędnej może nadal przepływać do tej sieci wirtualnej, ale nie może przepływać przez bramę sieci wirtualnej podłączoną do tej sieci wirtualnej.
    
      Tylko jedna komunikacja równorzędna dla tej sieci wirtualnej może mieć włączone to ustawienie.

      Nie można używać bram zdalnych, jeśli masz już skonfigurowaną bramę w sieci wirtualnej. Aby dowiedzieć się więcej na temat korzystania z bramy do przesyłania, zobacz [Konfigurowanie bramy sieci VPN do przesyłania w komunikacji równorzędnej w sieci wirtualnej](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Jeśli brama sieci wirtualnej jest używana do wysyłania ruchu lokalnego przechodnie do sieci wirtualnej równorzędnej, równorzędny zakres adresów IP sieci wirtualnej dla lokalnego urządzenia sieci VPN musi być ustawiony na ruch "interesujący". W przeciwnym razie zasoby lokalne nie będą mogły komunikować się z zasobami w sieci wirtualnej równorzędnej.

6. Wybierz **przycisk OK,** aby dodać komunikację równorzędnej do wybranej sieci wirtualnej.

Aby uzyskać instrukcje krok po kroku dotyczące implementowania komunikacji równorzędnej między sieciami wirtualnymi w różnych subskrypcjach i modelach wdrażania, zobacz [następne kroki](#next-steps).

### <a name="commands"></a>Polecenia

- **Interfejs wiersza polecenia platformy Azure:** [tworzenie komunikacji równorzędnej sieci az sieci wirtualnej](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Wyświetlanie lub zmienianie ustawień komunikacji równorzędnej

Przed zmianą komunikacji równorzędnej zapoznaj się z wymaganiami i ograniczeniami oraz [niezbędnymi uprawnieniami](#permissions).

1. W polu wyszukiwania u góry portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je. Nie należy **wybierać sieci wirtualnych (klasyczne),** jeśli są wyświetlane na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożonej za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, dla której chcesz zmienić ustawienia komunikacji równorzędnej.
3. W obszarze **USTAWIENIA**wybierz **pozycję Peerings**.
4. Wybierz komunikację równorzędnej, dla której chcesz wyświetlić lub zmienić ustawienia.
5. Zmień odpowiednie ustawienie. Przeczytaj o opcjach dla każdego ustawienia w [kroku 5](#add-peering) Tworzenie komunikacji równorzędnej.
6. Wybierz **pozycję Zapisz**.

**Polecenia**

- **Interfejs wiersza polecenia platformy Azure:** [lista komunikacji równorzędnej sieci az](/cli/azure/network/vnet/peering) do listy równorzędnych dla sieci wirtualnej, [wyświetlanie komunikacji równorzędnej sieci az,](/cli/azure/network/vnet/peering) aby wyświetlić ustawienia dla określonej komunikacji równorzędnej, oraz [aktualizację komunikacji równorzędnej sieci az sieci,](/cli/azure/network/vnet/peering) aby zmienić ustawienia komunikacji równorzędnej.|
- **Program PowerShell:** [Get-AzVirtualNetworkPeering,](/powershell/module/az.network/get-azvirtualnetworkpeering) aby pobrać ustawienia komunikacji równorzędnej widoku i [Set-AzVirtualNetworkPeering,](/powershell/module/az.network/set-azvirtualnetworkpeering) aby zmienić ustawienia.

## <a name="delete-a-peering"></a>Usuwanie komunikacji równorzędnej

Przed usunięciem komunikacji równorzędnej upewnij się, że twoje konto ma [niezbędne uprawnienia](#permissions).

Po usunięciu komunikacji równorzędnej ruch z sieci wirtualnej nie jest już przepływany do sieci wirtualnej równorzędnej. Gdy sieci wirtualne wdrożone za pośrednictwem Menedżera zasobów są równorzędne, każda sieć wirtualna ma komunikację równorzędną z inną siecią wirtualną. Chociaż usunięcie komunikacji równorzędnej z jednej sieci wirtualnej wyłącza komunikację między sieciami wirtualnymi, nie powoduje usunięcia komunikacji równorzędnej z innej sieci wirtualnej. Stan komunikacji równorzędnej dla komunikacji równorzędnej, który istnieje w innej sieci wirtualnej, to **Disconnected**. Nie można ponownie utworzyć komunikacji równorzędnej, dopóki nie ponownie utworzyć komunikacji równorzędnej w pierwszej sieci wirtualnej, a stan komunikacji równorzędnej dla obu sieci wirtualnych zmieni się na *Połączony*.

Jeśli chcesz, aby sieci wirtualne komunikowały się czasami, ale nie zawsze, zamiast usuwać komunikację równorzędną, możesz zamiast tego ustawić ustawienie **Zezwalaj na dostęp do sieci wirtualnej** na **Wyłączone.** Aby dowiedzieć się, jak to zrobić, przeczytaj krok 6 sekcji Tworzenie komunikacji równorzędnej w tym artykule. Wyłączenie i włączenie dostępu do sieci może być łatwiejsze niż usuwanie i odtworzenie komunikacji równorzędnej.

1. W polu wyszukiwania u góry portalu wprowadź *sieci wirtualne* w polu wyszukiwania. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, wybierz je. Nie należy **wybierać sieci wirtualnych (klasyczne),** jeśli są wyświetlane na liście, ponieważ nie można utworzyć komunikacji równorzędnej z sieci wirtualnej wdrożonej za pośrednictwem klasycznego modelu wdrażania.
2. Wybierz sieć wirtualną na liście, dla której chcesz usunąć komunikację równorzędną.
3. W obszarze **USTAWIENIA**wybierz **pozycję Peerings**.
4. Po prawej stronie komunikacji równorzędnej, którą chcesz usunąć, wybierz **...**, wybierz **pozycję Usuń**, a następnie wybierz pozycję **Tak,** aby usunąć komunikację równorzędnej z pierwszej sieci wirtualnej.
5. Wykonaj poprzednie kroki, aby usunąć komunikację równorzędnej z innej sieci wirtualnej w komunikacji równorzędnej.

**Polecenia**

- **Interfejs wiersza polecenia platformy Azure:** [usuwanie komunikacji równorzędnej sieci az](/cli/azure/network/vnet/peering)
- **PowerShell**: [Usuń-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Wymagania i ograniczenia

- <a name="cross-region"></a>Można równorzędne sieci wirtualne w tym samym regionie lub w różnych regionach. Komunikacja między sieciami wirtualnymi w różnych regionach jest również określana jako *globalna komunikacja równorzędna sieci wirtualnej.* 
- Podczas tworzenia globalnej komunikacji równorzędnej równorzędne sieci wirtualne mogą istnieć w dowolnym regionie chmury publicznej platformy Azure lub w regionach chmury chińskiej lub w chmurze dla instytucji rządowych. Nie można równorzędnie w chmurach. Na przykład sieci wirtualnej w chmurze publicznej platformy Azure nie można równorzędnie z siecią wirtualną w chmurze Platformy Azure w Chinach.
- Zasoby w jednej sieci wirtualnej nie mogą komunikować się z adresem IP frontonu wewnętrznego modułu równoważenia obciążenia w warstwie Podstawowa w sieci wirtualnej z globalną komunikacją równorzędną. Pomoc techniczna dla usługi Load Balancer w warstwie Podstawowa jest dostępna tylko w obrębie tego samego regionu. Pomoc techniczna dla usługi Load Balancer w warstwie Standardowa jest dostępna dla wirtualnych sieci równorzędnych i globalnych wirtualnych sieci równorzędnych. Usługi, które używają podstawowego modułu równoważenia obciążenia, który nie będzie działać za pomocą globalnej komunikacji równorzędnej sieci wirtualnej, są tutaj [udokumentowane.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Można użyć bram zdalnych lub zezwolić na przesyłanie bramy w globalnie równorzędnych sieciach wirtualnych i lokalnie równorzędnych sieciach wirtualnych.
- Sieci wirtualne mogą być w tej samej lub różnych subskrypcji. Podczas równorzędnych sieci wirtualnych w różnych subskrypcjach obie subskrypcje mogą być skojarzone z tą samą lub inną dzierżawą usługi Azure Active Directory. Jeśli nie masz jeszcze dzierżawy usługi AD, możesz [ją utworzyć.](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant) Obsługa komunikacji równorzędnej w sieciach wirtualnych z subskrypcji skojarzonych z różnymi dzierżawami usługi Azure Active Directory nie jest dostępna w portalu. Można użyć interfejsu WIERSZA POLECENIA, programu PowerShell lub szablonów.
- Sieci wirtualne, które są równorzędne, muszą mieć nienakładające się przestrzenie adresów IP.
- Nie można dodawać zakresów adresów ani usuwać zakresów adresów z przestrzeni adresowej sieci wirtualnej, gdy sieć wirtualna jest równorzędna z inną siecią wirtualną. Aby dodać lub usunąć zakresy adresów, usuń komunikację równorzędną, dodaj lub usuń zakresy adresów, a następnie ponownie utwórz komunikację równorzędną. Aby dodać zakresy adresów do sieci wirtualnych lub usunąć je z sieci wirtualnych, zobacz [Zarządzanie sieciami wirtualnymi](manage-virtual-network.md).
- Można równorzędnie dwóch sieci wirtualnych wdrożonych za pośrednictwem Menedżera zasobów lub sieci wirtualnej wdrożonej za pośrednictwem Menedżera zasobów z siecią wirtualną wdrożoną za pośrednictwem klasycznego modelu wdrażania. Nie można utworzyć elementu równorzędnych dwóch sieci wirtualnych utworzonych za pośrednictwem klasycznego modelu wdrażania. Jeśli nie znasz modeli wdrażania platformy Azure, przeczytaj artykuł [Poznaj modele wdrażania platformy Azure.](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Do połączenia dwóch sieci wirtualnych utworzonych za pomocą klasycznego modelu wdrażania można użyć usługi [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V).
- W przypadku łączenia dwóch sieci wirtualnych utworzonych w usłudze Resource Manager za pomocą komunikacji równorzędnej należy skonfigurować komunikację równorzędną dla każdej objętej nią sieci wirtualnej. Zobaczysz jeden z następujących typów stanu komunikacji równorzędnej: 
  - *Zainicjowane:* Podczas tworzenia komunikacji równorzędnej do drugiej sieci wirtualnej z pierwszej sieci wirtualnej, stan komunikacji równorzędnej jest *inicjowany*. 
  - *Podłączony:* Podczas tworzenia komunikacji równorzędnej z drugiej sieci wirtualnej do pierwszej sieci wirtualnej, jej stan komunikacji równorzędnej jest *Połączony*. Jeśli wyświetlasz stan komunikacji równorzędnej dla pierwszej sieci wirtualnej, zostanie wyświetlony jego stan zmieniony z *Zainicjowane* na *Połączone*. Komunikacja równorzędna nie zostanie pomyślnie ustanowiona, dopóki stan komunikacji równorzędnej dla obu komunikacji równorzędnej sieci wirtualnej nie zostanie *połączony*.
- Podczas komunikacji równorzędnej sieci wirtualnej utworzonej za pośrednictwem Menedżera zasobów za pomocą sieci wirtualnej utworzonej za pośrednictwem klasycznego modelu wdrażania można skonfigurować tylko komunikację równorzędną dla sieci wirtualnej wdrożonej za pośrednictwem Menedżera zasobów. Nie można skonfigurować komunikacji równorzędnej dla sieci wirtualnej (klasycznej) lub między dwiema sieciami wirtualnymi wdrożonymi za pośrednictwem klasycznego modelu wdrażania. Podczas tworzenia komunikacji równorzędnej z sieci wirtualnej (Menedżer zasobów) do sieci wirtualnej (Classic), stan komunikacji równorzędnej jest *Aktualizowanie,* a następnie wkrótce zmienia się *na Połączony*.
- Komunikacja równorzędna jest ustanawiana między dwiema sieciami wirtualnymi. Komunikacja równorzędna nie jest przechodnia. Jeśli tworzysz komunikacji równorzędnej między:
  - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3

  Nie ma komunikacji równorzędnej między VirtualNetwork1 i VirtualNetwork3 za pośrednictwem VirtualNetwork2. Jeśli chcesz utworzyć komunikację równorzędną sieci wirtualnej między VirtualNetwork1 i VirtualNetwork3, musisz utworzyć komunikację równorzędną między VirtualNetwork1 i VirtualNetwork3.
- Nie można rozpoznać nazw w sieciach wirtualnych równorzędnych przy użyciu domyślnego rozpoznawania nazw platformy Azure. Aby rozpoznawać nazwy w innych sieciach wirtualnych, należy użyć [usługi Azure DNS dla domen prywatnych](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub niestandardowego serwera DNS. Aby dowiedzieć się, jak skonfigurować własny serwer DNS, zobacz [Rozpoznawanie nazw przy użyciu własnego serwera DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Zasoby w sieciach wirtualnych równorzędnych w tym samym regionie mogą komunikować się ze sobą z taką samą przepustowością i opóźnieniem, jak w tej samej sieci wirtualnej. Każdy rozmiar maszyny wirtualnej ma jednak własną maksymalną przepustowość sieci. Aby dowiedzieć się więcej o maksymalnej przepustowości sieci dla różnych rozmiarów maszyn wirtualnych, zobacz Rozmiary maszyn wirtualnych [systemu Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux.](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Sieć wirtualna może być równorzędna z inną siecią wirtualną, a także być połączona z inną siecią wirtualną za pomocą bramy sieci wirtualnej platformy Azure. Gdy sieci wirtualne są połączone zarówno za pośrednictwem komunikacji równorzędnej, jak i bramy, ruch między sieciami wirtualnymi przepływa przez konfigurację komunikacji równorzędnej, a nie przez bramę.
- Klienci sieci VPN typu point-to-site muszą zostać pobrani ponownie po pomyślnym skonfigurowaniu komunikacji równorzędnej w sieci wirtualnej, aby upewnić się, że nowe trasy są pobierane do klienta.
- Istnieje nominalna opłata za ruch przychodzący i wychodzący w wirtualnych sieciach równorzędnych. Aby uzyskać więcej informacji, odwiedź [stronę cennika](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Uprawnienia

Konta używane do pracy z komunikacją równorzędkową w sieci wirtualnej muszą być przypisane do następujących ról:

- [Współautor sieci:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)Dla sieci wirtualnej wdrożonej za pośrednictwem Menedżera zasobów.
- [Klasyczny współautor sieci:](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor)Dla sieci wirtualnej wdrożonej za pośrednictwem klasycznego modelu wdrażania.

Jeśli twoje konto nie jest przypisane do jednej z poprzednich ról, musi być przypisane do [roli niestandardowej,](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) która jest przypisana niezbędne akcje z poniższej tabeli:

| Akcja                                                          | Nazwa |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Wymagane do utworzenia komunikacji równorzędnej z sieci wirtualnej A do sieci wirtualnej B. Sieć wirtualna A musi być siecią wirtualną (Menedżer zasobów)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Wymagane do utworzenia komunikacji równorzędnej z sieci wirtualnej B (Menedżer zasobów) do sieci wirtualnej A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Wymagane do utworzenia komunikacji równorzędnej z sieci wirtualnej B (klasycznej) do sieci wirtualnej A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Odczytywanie komunikacji równorzędnej w sieci wirtualnej   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Usuwanie komunikacji równorzędnej sieci wirtualnej |

## <a name="next-steps"></a>Następne kroki

- Równorzędne sieci wirtualne tworzy się między sieciami wirtualnymi utworzonymi za pomocą tych samych lub różnych modeli wdrażania istniejących w tej samej lub w różnych subskrypcjach. Ukończ samouczek dla jednego z następujących scenariuszy:

  |Model wdrażania platformy Azure             | Subskrypcja  |
  |---------                          |---------|
  |Resource Manager — w obu przypadkach              |[Ta sama](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Różne](create-peering-different-subscriptions.md)|
  |Jedna sieć — Resource Manager, druga — model klasyczny  |[Ta sama](create-peering-different-deployment-models.md)|
  |                                   |[Różne](create-peering-different-deployment-models-subscriptions.md)|

- Dowiedz się jak, utworzyć [topologię sieciową typu gwiazda](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Tworzenie komunikacji równorzędnej sieci wirtualnej przy użyciu przykładowych skryptów interfejsu [wiersza polecenia](cli-samples.md) [programu PowerShell](powershell-samples.md) lub platformy Azure lub przy użyciu [szablonów usługi](template-samples.md) Azure Resource Manager
- Tworzenie i stosowanie [zasad platformy Azure](policy-samples.md) dla sieci wirtualnych
