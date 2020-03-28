---
title: Kierowanie ruchu sieciowego — samouczek — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym samouczku dowiesz się, jak kierować ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: a565aba12f1b10f215d8f6cc7fc0b7247a0441d2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066366"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Samouczek: kierowanie ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal

Platforma Azure domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Trasy niestandardowe są przydatne, gdy na przykład chcesz kierować ruch między podsieciami za pośrednictwem sieciowego urządzenia wirtualnego (NVA). Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie urządzenia NVA, które kieruje ruchem
> * Tworzenie tabeli tras
> * Tworzenie trasy
> * Kojarzenie tabeli tras z podsiecią
> * Wdrażanie maszyn wirtualnych w różnych podsieciach
> * Kierowanie ruchem z jednej podsieci do drugiej za pomocą urządzenia NVA

Ten samouczek używa [portalu Azure](https://portal.azure.com). Można również użyć [interfejsu wiersza polecenia platformy Azure](tutorial-create-route-table-cli.md) lub programu Azure [PowerShell.](tutorial-create-route-table-powershell.md)

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Sieciowe urządzenia wirtualne to maszyny wirtualne, które pomagają w funkcjiach sieciowych, takich jak routing i optymalizacja zapory. W tym samouczku założono, że używasz systemu **Windows Server 2016 Datacenter**. Możesz wybrać inny system operacyjny.

1. W menu [portalu azure](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

1. Wybierz **pozycję Centrum** > danych**systemu Windows Server 2016**.

    ![Centrum danych systemu Windows Server 2016, tworzenie maszyny wirtualnej, witryna Azure portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Na stronie **Tworzenie maszyny wirtualnej** w obszarze **Podstawy**wprowadź lub wybierz te informacje:

    | Sekcja | Ustawienie | Akcja |
    | ------- | ------- | ----- |
    | **Szczegóły projektu** | Subskrypcja | Wybierz subskrypcję. |
    | | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | **Szczegóły wystąpienia** | Nazwa maszyny wirtualnej | Wpisz *myVmNva*. |
    | | Region | Wybierz **(USA) Wschodnie stany USA**. |
    | | Opcje dostępności | Wybierz **pozycję Nie jest wymagana nadmiarowość infrastruktury**. |
    | | Image (Obraz) | Wybierz **pozycję Centrum danych systemu Windows Server 2016**. |
    | | Rozmiar | Zachowaj domyślny **standard DS1 v2**. |
    | **Konto administratora** | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | | Hasło | Wprowadź wybrane hasło, które musi mieć co najmniej 12 znaków i spełniać [określone wymagania dotyczące złożoności.](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) |
    | | Potwierdź hasło | Wprowadź hasło ponownie. |
    | **Reguły portów przychodzących** | Publiczne porty wejściowe | Wybierz **opcję Brak**. |
    | **Oszczędzaj pieniądze** | Masz już licencję na system Windows Server? | Wybierz **nr**. |

    ![Podstawy, Tworzenie maszyny wirtualnej, witryna Azure portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Następnie wybierz przycisk **Dalej : Dyski >**.

1. W obszarze **Dyski**wybierz ustawienia odpowiednie dla Twoich potrzeb, a następnie wybierz pozycję **Dalej: >sieci **.

1. W obszarze **Sieć:**

    1. W obszarze **Sieć wirtualna**wybierz pozycję **Utwórz nowy**.
    
    1. W oknie dialogowym **Tworzenie sieci wirtualnej** w obszarze **Nazwa**wprowadź *pozycję mojaWirtualNetwork*.

    1. W **przestrzeni adresowej**zastąp istniejący zakres adresów *10.0.0.0/16*.

    1. W **podsieci wybierz**ikonę **Usuń,** aby usunąć istniejącą podsieć, a następnie wprowadź następujące kombinacje **nazwy podsieci** i **zakresu adresów**. Po wprowadzeniu prawidłowej nazwy i zakresu pod nim pojawi się nowy pusty wiersz.

        | Nazwa podsieci | Zakres adresów |
        | ----------- | ------------- |
        | *Publicznego* | *10.0.0.0/24* |
        | *Prywatny* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Wybierz **przycisk OK,** aby zakończyć okno dialogowe.

    1. W **podsieci**wybierz **DMZ (10.0.2.0/24)**.

    1. W **publicznej ip**, wybierz **Brak**, ponieważ ta maszyna wirtualna nie będzie łączyć się przez Internet.

    1. **Wybierz dalej : >zarządzania **.

1. W obszarze **zarządzanie:**

    1. W **obszarze Konto magazynu diagnostyki**wybierz pozycję **Utwórz nowy**.
    
    1. W oknie dialogowym **Tworzenie konta magazynu** wprowadź lub wybierz te informacje:

        | Ustawienie | Wartość |
        | ------- | ----- |
        | Nazwa | *mynvastoragekont* |
        | Rodzaj konta | **Magazynowanie (ogólnego przeznaczenia v1)** |
        | Wydajność | **Standardowa** |
        | Replikacja | **Magazyn lokalnie nadmiarowy (LRS)** |
    
    1. Wybierz **przycisk OK,** aby zakończyć okno dialogowe.

    1. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + create** strony i platformy Azure sprawdza poprawność konfiguracji.

1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

    W ciągu kilku minut zostanie utworzona maszyna wirtualna. Poczekaj, aż platforma Azure zakończy tworzenie maszyny Wirtualnej. Na stronie **Wdrażanie jest w toku** przedstawiono szczegóły wdrożenia.

1. Gdy maszyna wirtualna będzie gotowa, wybierz pozycję **Przejdź do zasobu**.

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

1. W menu [portalu azure](https://portal.azure.com) lub na stronie **głównej** wybierz pozycję **Utwórz zasób**.

2. W polu wyszukiwania wprowadź *tabelę Trasa*. Gdy w wynikach wyszukiwania pojawi się **tabela Trasa,** zaznacz ją.

3. Na stronie **Tabela Marszruta** wybierz pozycję **Utwórz**.

4. W obszarze **Tworzenie tabeli tras** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | *myRouteTablePublic* |
    | Subskrypcja | Twoja subskrypcja |
    | Grupa zasobów | **myResourceGroup** |
    | Lokalizacja | **(Stany Zjednoczone) Wschodnie stany USA** |
    | Propagacja trasy bramy sieci wirtualnej | **Enabled (Włączony)** |

    ![Tworzenie tabeli tras, witryna Azure portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Wybierz **pozycję Utwórz**.

## <a name="create-a-route"></a>Tworzenie trasy

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać tabelą tras. Wyszukaj i wybierz **tabele trasy**.

1. Wybierz nazwę tabeli tras (**myRouteTablePublic**).

1. Wybierz pozycję **Trasy** > **Dodaj**.

    ![Dodawanie trasy, tabeli tras, witryny Azure Portal](./media/tutorial-create-route-table-portal/add-route.png)

1. W obszarze **Dodawanie trasy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa trasy | *ToPrivateSubnet* |
    | Prefiks adresu | *10.0.1.0/24* (zakres adresów *podsieci Prywatnej* został utworzony wcześniej) |
    | Typ następnego skoku | **Urządzenie wirtualne** |
    | Adres następnego skoku | *10.0.2.4* (adres w zakresie adresów podsieci *DMZ)* |

1. Kliknij przycisk **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać siecią wirtualną. Wyszukaj i wybierz **sieć wirtualną**.

1. Wybierz nazwę swojej sieci wirtualnej (**myVirtualNetwork**).

1. Na pasku menu sieci wirtualnej wybierz pozycję **Podsieci**.

1. Na liście podsieci sieci wirtualnej wybierz pozycję **Public**.

1. W **tabeli Marszruta**wybierz utworzoną tabelę tras **(myRouteTablePublic),** a następnie wybierz pozycję **Zapisz,** aby skojarzyć tabelę marszruty z podsiecią *publicznej.*

    ![Kojarzenie tabeli tras, listy podsieci, sieci wirtualnej, witryny Azure portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Włączanie przekazywania dalej adresu IP

Następnie włącz przekazywanie adresów IP dla nowej maszyny wirtualnej NVA, *myVmNva*. Gdy platforma Azure wysyła ruch sieciowy do *myVmNva*, jeśli ruch jest przeznaczony dla innego adresu IP, przekazywanie adresów IP wysyła ruch do właściwej lokalizacji.

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać maszyną wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.

1. Wybierz nazwę maszyny Wirtualnej (**myVmNva**).

1. Na pasku menu maszyny wirtualnej NVA wybierz pozycję **Sieć**.

1. Wybierz pozycję **myvmnva123**. Jest to interfejs sieciowy utworzony przez platformę Azure dla maszyny wirtualnej. Platforma Azure dodaje numery, aby zapewnić unikatową nazwę.

    ![Maszyna wirtualna sieci, sieciowe urządzenie wirtualne (VM), witryna Azure portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Na pasku menu interfejsu sieciowego wybierz **pozycję Konfiguracje ADRESÓW IP**.

1. Na stronie **Konfiguracje IP** ustaw **przekazywanie adresów IP** na **Włączone**i wybierz pozycję **Zapisz**.

    ![Włącz przekazywanie adresów IP, konfiguracje IP, interfejs sieciowy, maszynę wirtualną sieciowego urządzenia wirtualnego (VM), portal Azure](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Tworzenie publicznych i prywatnych maszyn wirtualnych

Utwórz publiczną i prywatną maszynę wirtualną w sieci wirtualnej. Później użyjesz ich, aby sprawdzić, jak platforma Azure kieruje ruch podsieci *publicznej* do podsieci *prywatnej* za pośrednictwem urządzenia WUS.

Aby utworzyć publiczną maszynę wirtualną i prywatną maszynę wirtualną, wykonaj kroki [tworzenie urządzenia wuszędnego](#create-an-nva) wcześniej. Nie trzeba czekać na zakończenie wdrażania lub przejść do zasobu maszyny Wirtualnej. Użyjesz większości tych samych ustawień, z wyjątkiem opisanych poniżej.

Przed **wybraniem opcji Utwórz,** aby utworzyć publiczną lub prywatną maszynę wirtualną, przejdź do następujących dwóch podsekcji[(publiczna maszyna wirtualna](#public-vm) i [prywatna maszyna wirtualna),](#private-vm)które pokazują wartości, które muszą być różne. Po zakończeniu wdrażania obu maszyn wirtualnych może być kontynuowana do następnej sekcji[(Ruch trasy za pośrednictwem urządzenia WUS)](#route-traffic-through-an-nva)po zakończeniu wdrażania obu maszyn wirtualnych przez platformę Azure.

### <a name="public-vm"></a>Publiczna maszyna wirtualna

| Tab | Ustawienie | Wartość |
| --- | ------- | ----- |
| Podstawy | Grupa zasobów | **myResourceGroup** |
| | Nazwa maszyny wirtualnej | *myVmPublic* |
| | Publiczne porty wejściowe | **Zezwalaj na wybrane porty** |
| | Wybierz porty wejściowe | **HTTP** i **RDP** |
| Obsługa sieci | Sieć wirtualna | **myVirtualNetwork** |
| | Podsieć | **Publiczne (10.0.0.0/24)** |
| | Publiczny adres IP | Wartość domyślna |
| Zarządzanie | Konto magazynu diagnostyki | **mynvastoragekont** |

### <a name="private-vm"></a>Prywatna maszyna wirtualna

| Tab | Ustawienie | Wartość |
| --- | ------- | ----- |
| Podstawy | Grupa zasobów | **myResourceGroup** |
| | Nazwa maszyny wirtualnej | *myVmPrivate* |
| | Publiczne porty wejściowe | **Zezwalaj na wybrane porty** |
| | Wybierz porty wejściowe | **HTTP** i **RDP** |
| Obsługa sieci | Sieć wirtualna | **myVirtualNetwork** |
| | Podsieć | **Prywatne (10.0.1.0/24)** |
| | Publiczny adres IP | Wartość domyślna |
| Zarządzanie | Konto magazynu diagnostyki | **mynvastoragekont** |

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Logowanie się do maszyny wirtualnej myVmPrivate za pośrednictwem pulpitu zdalnego

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać prywatną maszyną wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.

1. Wybierz nazwę swojej prywatnej maszyny Wirtualnej **(myVmPrivate).**

1. Na pasku menu maszyny Wirtualnej wybierz pozycję **Połącz,** aby utworzyć połączenie pulpitu zdalnego z prywatną maszyną wirtualną.

1. Na stronie **Połącz z RDP** wybierz pozycję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik *rdp*. Po wyświetleniu monitu wybierz pozycję **Połącz**. Wybierz **pozycję Więcej opcji** > **Użyj innego konta,** a następnie wprowadź nazwę użytkownika i hasło określone podczas tworzenia prywatnej maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Jeśli podczas procesu logowania otrzymasz ostrzeżenie o certyfikacie, wybierz opcję **Tak,** aby połączyć się z maszyną wirtualną.

### <a name="enable-icmp-through-the-windows-firewall"></a>Włączanie komunikacji protokołu ICMP przez Zaporę systemu Windows

W kolejnym kroku przetestujesz routing przy użyciu narzędzia śledzenia trasy. Narzędzie śledzenia trasy używa protokołu ICMP (Internet Control Message Protocol), którego ruch jest domyślnie blokowany przez Zaporę systemu Windows. Włącz protokół ICMP za pomocą Zapory systemu Windows.

1. Na pulpicie zdalnym maszyny *myVmPrivate* otwórz program PowerShell.

1. Wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Będziesz używać trasy śledzenia do testowania routingu w tym samouczku. W środowiskach produkcyjnych nie zalecamy zezwalania na protokół ICMP przez Zaporę systemu Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Włączanie przekazywania dalej adresu na maszynie wirtualnej myVmNva

[Włączono przekazywanie adresu IP](#turn-on-ip-forwarding) dla interfejsu sieciowego maszyny wirtualnej przy użyciu platformy Azure. System operacyjny maszyny wirtualnej również musi przekazywać dalej ruch sieciowy. Włącz przekazywanie adresu IP dla systemu operacyjnego maszyny wirtualnej *myVmNva* przy użyciu poniższych poleceń.

1. W wierszu polecenia maszyny wirtualnej *myVmPrivate* otwórz pulpit zdalny na maszynie wirtualnej *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Z programu PowerShell na maszynie Wirtualnej *myVmNva* wprowadź to polecenie, aby włączyć przekazywanie adresów IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Uruchom ponownie *maszynę wirtualną myVmNva:* z paska zadań wybierz pozycję **Uruchom** > **zasilanie**, **Inne (planowane)** > **Kontynuuj**.

    Powoduje to również rozłączenie sesji pulpitu zdalnego.

1. Po ponownym uruchomieniu maszyny wirtualnej *myVmNva* utwórz sesję pulpitu zdalnego z maszyną wirtualną *myVmPublic*. Mając nadal połączenie z maszyną *myVmPrivate*, otwórz wiersz polecenia i uruchom następujące polecenie:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Na pulpicie zdalnym *myVmPublic*otwórz program PowerShell.

1. Włącz protokół ICMP za pomocą Zapory systemu Windows przez wprowadzenie tego polecenia:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>Testowanie routingu ruchu sieciowego

Najpierw przetestuj routing ruchu sieciowego z maszyny wirtualnej *myVmPublic* do maszyny wirtualnej *myVmPrivate*.

1. W programie PowerShell na maszynie wirtualnej *myVmPublic* wprowadź następujące polecenie:

    ```powershell
    tracert myVmPrivate
    ```

    Odpowiedź jest podobna do poniższego przykładu:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    Możesz zobaczyć pierwszy przeskok do 10.0.2.4, który jest prywatnym adresem IP urządzenia NVA. Drugi przeskok jest do prywatnego adresu IP maszyny wirtualnej *myVmPrivate:* 10.0.1.4. Wcześniej dodano trasę do tabeli tras *myRouteTablePublic* i skojarzono ją z podsiecią *publiczną*. W rezultacie platforma Azure wysyłała ruch przez urządzenie WUS, a nie bezpośrednio do podsieci *prywatnej*.

1. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*. Połączenie z maszyną wirtualną *myVmPrivate* pozostanie nadal aktywne.

1. W wierszu polecenia na maszynie wirtualnej *myVmPrivate* wprowadź następujące polecenie:

    ```cmd
    tracert myVmPublic
    ```

    To polecenie testuje routing ruchu sieciowego z maszyny wirtualnej *myVmPrivate* do maszyny Wirtualnej *myVmPublic.* Odpowiedź jest podobna do poniższego przykładu:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Widać, że platforma Azure kieruje ruch bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic.* Domyślnie platforma Azure kieruje ruch bezpośrednio między podsieciami.

1. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie jest już potrzebna, usuń *myResourceGroup* i wszystkie zasoby, które posiada:

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby zarządzać grupą zasobów. Wyszukaj i wybierz **grupę zasobów**.

1. Wybierz nazwę grupy zasobów (**myResourceGroup**).

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W oknie dialogowym potwierdzenia wprowadź *myResourceGroup* dla **TYPU NAZWA GRUPY ZASOBÓW,** a następnie wybierz pozycję **Usuń**. Platforma Azure usuwa *myResourceGroup* i wszystkie zasoby powiązane z tą grupą zasobów, w tym tabele tras, konta magazynu, sieci wirtualne, maszyny wirtualne, interfejsy sieciowe i publiczne adresy IP.

## <a name="next-steps"></a>Następne kroki

W tym samouczku została utworzona tabela tras, która została następnie skojarzona z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Teraz można wdrożyć różne wstępnie skonfigurowane usługi WDO z [usługi Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), które zapewniają wiele przydatnych funkcji sieciowych. Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, platforma Azure nie może wdrożyć zasobów dla niektórych usług PaaS w sieci wirtualnej. Jest możliwe, aby ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure, chociaż ograniczenie musi być tylko ruch z podsieci sieci wirtualnej. Aby dowiedzieć się, jak ograniczyć dostęp do sieci do zasobów usługi Azure PaaS, zobacz następny samouczek.

> [!div class="nextstepaction"]
> [Ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources.md)
