---
title: Kierowanie ruchu sieciowego — samouczek — Azure Portal | Microsoft Docs
description: W tym samouczku dowiesz się, jak kierować ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 81478ace72a538f4970e114cd704fd64ceb94aa6
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344901"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Samouczek: kierowanie ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal

Platforma Azure automatycznie domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie tabeli tras
> * Tworzenie trasy
> * Tworzenie sieci wirtualnej z wieloma podsieciami
> * Kojarzenie tabeli tras z podsiecią
> * Tworzenie urządzenia NVA, które kieruje ruchem
> * Wdrażanie maszyn wirtualnych w różnych podsieciach
> * Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Jeśli chcesz, możesz wykonać ten samouczek przy użyciu [interfejsu wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md) lub [programu Azure PowerShell](tutorial-create-route-table-powershell.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure. 

Zaloguj się do witryny Azure Portal na stronie http://portal.azure.com.

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Tabela tras**.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myRouteTablePublic|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów | Wybierz pozycję **Utwórz nową**, a następnie wprowadź nazwę *myResourceGroup*.|
    |Lokalizacja|Wschodnie stany USA|
 
    ![Tworzenie tabeli tras](./media/tutorial-create-route-table-portal/create-route-table.png) 

## <a name="create-a-route"></a>Tworzenie trasy

1. W polu *Szukaj zasobów, usług i dokumentów* w górnej części portalu rozpocznij wpisywanie ciągu *myRouteTablePublic*. Gdy pozycja **myRouteTablePublic** pojawi się w wynikach wyszukiwania, wybierz ją.
2. W obszarze **USTAWIENIA** wybierz pozycję **Trasy**, a następnie wybierz pozycję **+ Dodaj**, jak pokazano na poniższym obrazie:

    ![Dodawanie trasy](./media/tutorial-create-route-table-portal/add-route.png) 
 
3. W obszarze **Dodaj trasę** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa trasy|ToPrivateSubnet|
    |Prefiks adresu| 10.0.1.0/24|
    |Typ następnego skoku | Wybierz pozycję **Urządzenie wirtualne**.|
    |Adres następnego skoku| 10.0.2.4|

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć:

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Sieć**, a następnie wybierz pozycję **Sieć wirtualna**.
3. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz pozycję **Utwórz**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVirtualNetwork|
    |Przestrzeń adresowa| 10.0.0.0/16|
    |Subskrypcja | Wybierz subskrypcję.|
    |Grupa zasobów|Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Lokalizacja|Wybierz pozycję *Wschodnie stany USA*|
    |Nazwa podsieci|Public|
    |Zakres adresów|10.0.0.0/24|
    
4. W polu **Szukaj zasobów, usług i dokumentów** w górnej części portalu rozpocznij wpisywanie ciągu *myVirtualNetwork*. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją.
5. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**, a następnie wybierz pozycję **+ Podsieć**, jak pokazano na poniższym obrazie:

    ![Dodawanie podsieci](./media/tutorial-create-route-table-portal/add-subnet.png) 

6. Wybierz lub wprowadź następujące informacje, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|Private|
    |Przestrzeń adresowa| 10.0.1.0/24|

7. Ponownie wykonaj kroki 5 i 6, podając następujące informacje:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|DMZ|
    |Przestrzeń adresowa| 10.0.2.0/24|

8. Pole **myVirtualNetwork — podsieci** zostanie wyświetlone po zakończeniu poprzedniego kroku. W obszarze **USTAWIENIA** wybierz pozycję **Podsieci**, a następnie wybierz pozycję **Public**.
9. Wybierz pozycję **Tabela tras**, wybierz pozycję **MyRouteTablePublic**, a następnie wybierz pozycję **Zapisz**, jak pokazano na poniższym obrazie:

    ![Kojarzenie tabeli tras](./media/tutorial-create-route-table-portal/associate-route-table.png) 

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenie NVA jest maszyną wirtualną, która realizuje funkcje sieci, takie jak routing, zapora lub optymalizacja sieci WAN.

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **Wystąpienia obliczeniowe**, a następnie wybierz pozycję **Windows Server 2016 Datacenter**. Możesz wybrać inny system operacyjny, ale w pozostałych krokach założono, że wybierzesz system **Windows Server 2016 Datacenter**. 
3. Wybierz lub wprowadź następujące informacje w obszarze **Podstawy**, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Name (Nazwa)|myVmNva|
    |Nazwa użytkownika|Wprowadź wybraną nazwę użytkownika.|
    |Hasło|Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę *myResourceGroup*.|
    |Lokalizacja|Wybierz pozycję **Wschodnie stany USA**.|
4. Wybierz rozmiar maszyny wirtualnej w obszarze **Wybierz rozmiar**.
5. Wybierz lub wprowadź następujące informacje w obszarze **Ustawienia**, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Sieć wirtualna|myVirtualNetwork — jeśli nie jest zaznaczona, wybierz pozycję **Sieć wirtualna**, a następnie wybierz pozycję **myVirtualNetwork** w obszarze **Wybierz sieć wirtualną**.|
    |Podsieć|Wybierz pozycję **Podsieć**, a następnie wybierz pozycję **DMZ** w obszarze **Wybierz podsieć**. |
    |Publiczny adres IP| Wybierz pozycję **Publiczny adres IP**, a następnie wybierz pozycję **Brak** w obszarze **Wybierz publiczny adres IP**. Żaden publiczny adres IP nie zostanie przypisany do tej maszyny wirtualnej, ponieważ nie można będzie się z nią połączyć z Internetu.
6. W obszarze **Utwórz** w sekcji **Podsumowanie** wybierz pozycję **Utwórz**, aby rozpocząć wdrażanie maszyny wirtualnej.

    W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie przechodź do następnego kroku, dopóki platforma Azure nie zakończy tworzenia maszyny wirtualnej i nie otworzy okna z informacjami o maszynie wirtualnej.

7. W polu, które zostało otwarte dla maszyny wirtualnej po jej utworzeniu, w obszarze **USTAWIENIA** wybierz pozycję **Sieć**, a następnie wybierz pozycję **myvmnva158** (interfejs sieciowy utworzony przez platformę Azure dla Twojej maszyny wirtualnej ma inny numer po ciągu **myvmnva**), jak pokazano na poniższym obrazie:

    ![Sieć maszyn wirtualnych](./media/tutorial-create-route-table-portal/virtual-machine-networking.png) 

8. Aby karta sieciowa mogła przekazywać dalej wysyłany do niej ruch sieciowy, który nie jest przeznaczony dla jej własnego adresu IP, musi być włączone przekazywanie adresu IP dla interfejsu sieciowego. W obszarze **USTAWIENIA** wybierz pozycję **Konfiguracje adresów IP**, wybierz wartość **Włączone** dla pozycji **Przekazywanie dalej adresu IP**, a następnie wybierz pozycję **Zapisz**, jak pokazano na poniższym obrazie:

    ![Włączanie przekazywania dalej adresu IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png) 

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

Utwórz dwie maszyny wirtualne w sieci wirtualnej, aby w późniejszym kroku można było zweryfikować, czy ruch z podsieci *Public* jest kierowany do podsieci *Private* za pośrednictwem urządzenia NVA. Wykonaj kroki 1–6 z sekcji [Tworzenie urządzenia NVA](#create-a-network-virtual-appliance). Użyj tych samych ustawień w kroku 3 i 5, z wyjątkiem następujących zmian:

|Nazwa maszyny wirtualnej      |Podsieć      | Publiczny adres IP     |
|--------- | -----------|---------              |
| myVmPublic  | Public     | Zaakceptuj wartość domyślną portalu |
| myVmPrivate | Private    | Zaakceptuj wartość domyślną portalu |

Maszynę wirtualną *myVmPrivate* możesz utworzyć w czasie, gdy platforma Azure tworzy maszynę wirtualną *myVmPublic*. Nie należy przechodzić do następnych kroków, dopóki platforma Azure nie zakończy tworzenia obu maszyn wirtualnych.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

1. W polu *Szukaj* w górnej części portalu rozpocznij wpisywanie ciągu *myVmPrivate*. Gdy maszyna wirtualna **myVmPrivate** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Utwórz połączenie pulpitu zdalnego z maszyną wirtualną *myVmPrivate*, wybierając pozycję **Połącz**, jak pokazano na poniższym obrazie:

    ![Łączenie z maszyną wirtualną ](./media/tutorial-create-route-table-portal/connect-to-virtual-machine.png)  

3. Aby nawiązać połączenie z maszyną wirtualną, otwórz pobrany plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.
4. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej (może okazać się konieczne wybranie pozycji **Więcej opcji** oraz **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej), a następnie wybierz przycisk **OK**.
5. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Wybierz pozycję **Tak**, aby nawiązać połączenie.
6. W kolejnym kroku narzędzie śledzenia trasy jest używane do testowania routingu. Narzędzie śledzenia trasy używa protokołu ICMP (Internet Control Message Protocol), którego ruch jest blokowany przez zaporę systemu Windows. Wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPrivate*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Chociaż w tym samouczku do testowania routingu jest używane narzędzie śledzenia trasy, nie zaleca się zezwalania na komunikację za pomocą protokołu ICMP w zaporze systemu Windows we wdrożeniach produkcyjnych.
7. Przekazywanie dalej adresu IP w obrębie platformy Azure dla interfejsu sieciowego maszyny wirtualnej zostało włączone w sekcji [Włączanie przekazywania dalej adresu IP](#enable-ip-forwarding). W ramach maszyny wirtualnej system operacyjny lub aplikacja działająca na maszynie wirtualnej musi także móc przekazywać dalej ruch sieciowy. Włącz przekazywanie dalej adresu IP w ramach systemu operacyjnego maszyny wirtualnej *myVmNva*:

    W wierszu polecenia maszyny wirtualnej *myVmPrivate* połącz się za pomocą pulpitu zdalnego z maszyną wirtualną *myVmNva*:

    ``` 
    mstsc /v:myvmnva
    ```
    
    Aby włączyć przekazywanie dalej adresu IP w ramach systemu operacyjnego, wprowadź następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmNva*:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```
    
    Uruchom ponownie maszynę wirtualną *myVmNva*, co spowoduje także odłączenie sesji pulpitu zdalnego.
8. Mając nadal połączenie z maszyną wirtualną *myVmPrivate*, utwórz sesję pulpitu zdalnego z maszyną wirtualną *myVmPublic* po ponownym uruchomieniu maszyny wirtualnej *myVmNva*:

    ``` 
    mstsc /v:myVmPublic
    ```
    
    Wyłącz blokowanie protokołu ICMP przez zaporę systemu Windows, wprowadzając następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPublic*:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

9. Aby przetestować routing ruchu sieciowego do maszyny wirtualnej *myVmPrivate* z maszyny wirtualnej *myVmPublic*, wprowadź następujące polecenie w programie PowerShell na maszynie wirtualnej *myVmPublic*:

    ```
    tracert myVmPrivate
    ```

    Odpowiedź jest podobna do poniższego przykładu:
    
    ```
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:
        
    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4
        
    Trace complete.
    ```
      
    Teraz możesz zobaczyć, że pierwszym przeskokiem jest 10.0.2.4, czyli prywatny adres IP urządzenia NVA. Drugim przeskokiem jest 10.0.1.4, prywatny adres IP maszyny wirtualnej *myVmPrivate*. Trasa dodana do tabeli tras *myRouteTablePublic* i powiązana z podsiecią *Public* spowodowała, że platforma Azure skierowała ruch przez urządzenie NVA, a nie bezpośrednio do podsieci *Private*.
10.  Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*. Połączenie z maszyną wirtualną *myVmPrivate* pozostanie nadal aktywne.
11. Aby przetestować routing ruchu sieciowego do maszyny wirtualnej *myVmPublic* z maszyny wirtualnej *myVmPrivate*, wprowadź następujące polecenie w wierszu polecenia na maszynie wirtualnej *myVmPrivate*:

    ```
    tracert myVmPublic
    ```

    Odpowiedź jest podobna do poniższego przykładu:

    ```
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:
    
    1     1 ms     1 ms     1 ms  10.0.0.4
    
    Trace complete.
    ```

    Widać, że ruch jest kierowany bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Domyślnie platforma Azure kieruje ruch bezpośrednio między podsieciami.
12. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją wraz ze wszystkimi zasobami, które zawiera: 

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku została utworzona tabela tras, która została następnie skojarzona z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wdrożono różne wstępnie skonfigurowane urządzenia NVA wykonujące funkcje sieciowe, takie jak zapora i optymalizacja sieci WAN, z witryny [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).


Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, zasobów dla niektórych usług PaaS platformy Azure nie można wdrożyć w sieci wirtualnej. Nadal możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure tylko do ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się, jak ograniczyć dostęp sieciowy do zasobów PaaS platformy Azure, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources.md)
