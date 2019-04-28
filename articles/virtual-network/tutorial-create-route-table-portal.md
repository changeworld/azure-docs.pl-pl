---
title: Kierowanie ruchu sieciowego — samouczek — Azure Portal
titlesuffix: Azure Virtual Network
description: W tym samouczku dowiesz się, jak kierować ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 855adccf036f731de12810fe0f5287186048ddb0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098645"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Samouczek: Kierowanie ruchem sieciowym za pomocą tabeli tras z użyciem witryny Azure Portal

Platforma Azure domyślnie kieruje ruchem między wszystkimi podsieciami w sieci wirtualnej. Możesz tworzyć własne trasy zastępujące domyślne trasy platformy Azure. Możliwość tworzenia niestandardowych tras jest przydatna, jeśli na przykład chcesz kierować ruchem między podsieciami za pomocą wirtualnego urządzenia sieciowego (NVA). Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

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

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-route-table"></a>Tworzenie tabeli tras

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Tabela tras**.

1. W obszarze **Tworzenie tabeli tras** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wpisz *myRouteTablePublic*. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk *OK*. |
    | Lokalizacja | Pozostaw wartość domyślną **Wschodnie stany USA**.
    | Propagowanie tras BGP | Pozostaw wartość domyślną **Włączone**. |

1. Wybierz pozycję **Utwórz**.

## <a name="create-a-route"></a>Tworzenie trasy

1. Na pasku wyszukiwania portalu wpisz *myRouteTablePublic*.

1. Gdy pozycja **myRouteTablePublic** pojawi się w wynikach wyszukiwania, wybierz ją.

1. W tabeli **myRouteTablePublic** w obszarze **Ustawienia** wybierz pozycję **Trasy** > **+ Dodaj**.

    ![Dodawanie trasy](./media/tutorial-create-route-table-portal/add-route.png)

1. W obszarze **Dodawanie trasy** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa trasy | Wpisz *ToPrivateSubnet*. |
    | Prefiks adresu | Wpisz *10.0.1.0/24*. |
    | Typ następnego skoku | Wybierz pozycję **Urządzenie wirtualne**. |
    | Adres następnego skoku | Wpisz *10.0.2.4*. |

1. Kliknij przycisk **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Kojarzenie tabeli tras z podsiecią

Zanim skojarzysz tabelę tras z podsiecią, musisz utworzyć sieć wirtualną i podsieć.

### <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wpisz *myVirtualNetwork*. |
    | Przestrzeń adresowa | Wpisz *10.0.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję ***Wybierz istniejącą*** > **myResourceGroup**. |
    | Lokalizacja | Pozostaw wartość domyślną **Wschodnie stany USA**. |
    | Podsieć — nazwa | Wpisz *Publiczna*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wpisz *10.0.0.0/24*. |

1. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Utwórz**.

### <a name="add-subnets-to-the-virtual-network"></a>Dodawanie podsieci do sieci wirtualnej

1. Na pasku wyszukiwania portalu wpisz *myVirtualNetwork*.

1. Gdy pozycja **myVirtualNetwork** pojawi się w wynikach wyszukiwania, wybierz ją.

1. W sieci **myVirtualNetwork** w obszarze **Ustawienia** wybierz pozycję **Podsieci** > **+ Podsieć**.

    ![Dodawanie podsieci](./media/tutorial-create-route-table-portal/add-subnet.png)

1. W obszarze **Dodawanie podsieci** wprowadź następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wpisz *Prywatna*. |
    | Przestrzeń adresowa | Wpisz *10.0.1.0/24*. |

1. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.

1. Wybierz ponownie pozycję **+ Podsieć**. Tym razem wprowadź następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wpisz *DMZ*. |
    | Przestrzeń adresowa | Wpisz *10.0.2.0/24*. |

1. Tak jak ostatnio, pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **OK**.

    Na platformie Azure są widoczne trzy podsieci: **Publiczna**, **Prywatna** i **DMZ**.

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>Kojarzenie tabeli myRouteTablePublic z podsiecią publiczną

1. Wybierz pozycję **Publiczna**.

1. W obszarze **Publiczna** wybierz pozycję **Tabela tras** > **MyRouteTablePublic** > **Zapisz**.

    ![Kojarzenie tabeli tras](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>Tworzenie urządzenia NVA

Urządzenia WUS to maszyny wirtualne, które pomagają w działaniu funkcji sieciowych, takich jak optymalizacja routingu i zapory. Możesz wybrać inny system operacyjny. W tym samouczku założono, że używasz systemu **Windows Server 2016 Datacenter**.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Compute** > **Windows Server 2016 Datacenter**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wpisz *myVmNva*. |
    | Region | Wybierz pozycję **Wschodnie stany USA**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image | Pozostaw wartość domyślną **Windows Server 2016 Datacenter**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**.
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |

1. Wybierz opcję **Dalej: Dyski**.

1. W obszarze **Tworzenie maszyny wirtualnej — dyski** wybierz odpowiednie ustawienia.

1. Wybierz opcję **Dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **myVirtualNetwork**. |
    | Podsieć | Wybierz pozycję **DMZ (10.0.2.0/24)**. |
    | Publiczny adres IP | Wybierz pozycję **Brak**. Nie potrzebujesz publicznego adresu IP. Maszyna wirtualna nie łączy się przez Internet.|

1. Pozostaw resztę ustawień domyślnych, a następnie wybierz pozycję **Dalej: Zarządzanie**.

1. W obszarze **Tworzenie maszyny wirtualnej — zarządzanie** dla opcji **Konto magazynu diagnostyki** wybierz ustawienie **Utwórz nowe**.

1. W obszarze **Tworzenie konta magazynu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name (Nazwa) | Wpisz *mynvastorageaccount*. |
    | Rodzaj konta | Pozostaw wartość domyślną **Magazyn (ogólnego przeznaczenia, wersja 1)**. |
    | Wydajność | Pozostaw wartość domyślną **Standardowa**. |
    | Replikacja | Pozostaw wartość domyślną **Magazyn lokalnie nadmiarowy (LRS)**.

1. Kliknij przycisk **OK**

1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przekierowanie do strony**Przeglądanie + tworzenie**, a platforma Azure zweryfikuje konfigurację.

1. Po wyświetleniu komunikatu **Weryfikacja pomyślna** wybierz opcję **Utwórz**.

    W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie kontynuuj, dopóki platforma Azure nie zakończy tworzenia maszyny wirtualnej. Szczegóły wdrożenia będą wyświetlane na stronie **Wdrożenie jest w toku**.

1. Gdy maszyna wirtualna będzie gotowa, wybierz pozycję **Przejdź do zasobu**.

## <a name="turn-on-ip-forwarding"></a>Włączanie przekazywania dalej adresu IP

Włącz przekazywanie dalej adresu IP dla maszyny wirtualnej *myVmNva*. Gdy platforma Azure wysyła ruch sieciowy do maszyny wirtualnej *myVmNva*, jeśli ruch jest przeznaczony dla innego adresu IP, przekazywanie dalej adresu IP spowoduje wysłanie ruchu do poprawnej lokalizacji.

1. Na maszynie wirtualnej **myVmNva** w obszarze **Ustawienia** wybierz pozycję **Sieć**.

1. Wybierz pozycję **myvmnva123**. Jest to interfejs sieciowy utworzony przez platformę Azure dla maszyny wirtualnej. Zawiera unikatowy ciąg liczb.

    ![Sieć maszyn wirtualnych](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. W obszarze **Ustawienia** wybierz pozycję **Konfiguracje adresów IP**.

1. Na stronie **myvmnva123 — konfiguracje adresów IP** dla ustawienia **Przekazywanie dalej adresu IP** wybierz pozycję **Włączone**, a następnie wybierz pozycję **Zapisz**.

    ![Włączanie przekazywania dalej adresu IP](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Tworzenie publicznych i prywatnych maszyn wirtualnych

Utwórz publiczną i prywatną maszynę wirtualną w sieci wirtualnej. Później użyjesz ich, aby sprawdzić, jak platforma Azure kieruje ruch podsieci *publicznej* do podsieci *prywatnej* za pośrednictwem urządzenia WUS.

Wykonaj kroki 1–12 z sekcji [Tworzenie urządzenia NVA](#create-an-nva). Użyj w większości tych samych ustawień. Te wartości muszą być różne:

| Ustawienie | Wartość |
| ------- | ----- |
| **PUBLICZNA MASZYNA WIRTUALNA** | |
| PODSTAWY |  |
| Nazwa maszyny wirtualnej | Wpisz *myVmPublic*. |
| SIEĆ | |
| Podsieć | Wybierz pozycję **Publiczna (10.0.0.0/24)**. |
| Publiczny adres IP | Zaakceptuj domyślną wartość. |
| Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
| Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**. |
| ZARZĄDZANIE | |
| Konto magazynu diagnostyki | Pozostaw wartość domyślną **mynvastorageaccount**. |
| **PRYWATNA MASZYNA WIRTUALNA** | |
| PODSTAWY |  |
| Nazwa maszyny wirtualnej | Wpisz *myVmPrivate*. |
| SIEĆ | |
| Podsieć | Wybierz pozycję **Prywatna (10.0.1.0/24)**. |
| Publiczny adres IP | Zaakceptuj domyślną wartość. |
| Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
| Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**. |
| ZARZĄDZANIE | |
| Konto magazynu diagnostyki | Pozostaw wartość domyślną **mynvastorageaccount**. |

Maszynę wirtualną *myVmPrivate* możesz utworzyć w czasie, gdy platforma Azure tworzy maszynę wirtualną *myVmPublic*. Nie należy przechodzić do następnych kroków, dopóki platforma Azure nie zakończy tworzenia obu maszyn wirtualnych.

## <a name="route-traffic-through-an-nva"></a>Kierowanie ruchem za pośrednictwem urządzenia NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Logowanie się do maszyny wirtualnej myVmPrivate za pośrednictwem pulpitu zdalnego

1. Na pasku wyszukiwania portalu wpisz *myVmPrivate*.

1. Gdy maszyna wirtualna **myVmPrivate** pojawi się w wynikach wyszukiwania, wybierz ją.

1. Wybierz pozycję **Połącz**, aby utworzyć połączenie pulpitu zdalnego z maszyną wirtualną *myVmPrivate*.

1. W polu **Połącz z maszyną wirtualną** wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz pobrany plik *rdp*.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia prywatnej maszyny wirtualnej.

    1. Musisz wybrać pozycję **Więcej opcji** > **Użyj innego konta**, aby użyć poświadczeń prywatnej maszyny wirtualnej.

1. Kliknij przycisk **OK**.

    Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie.

1. Wybierz pozycję **Tak**, aby nawiązać połączenie z maszyną wirtualną.

### <a name="enable-icmp-through-the-windows-firewall"></a>Włączanie komunikacji protokołu ICMP przez Zaporę systemu Windows

W kolejnym kroku przetestujesz routing przy użyciu narzędzia śledzenia trasy. Narzędzie śledzenia trasy używa protokołu ICMP (Internet Control Message Protocol), którego ruch jest domyślnie blokowany przez Zaporę systemu Windows. Włącz protokół ICMP za pomocą Zapory systemu Windows.

1. Na pulpicie zdalnym maszyny *myVmPrivate* otwórz program PowerShell.

1. Wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    Używasz narzędzia śledzenia trasy do testowania routingu w ramach tego samouczka. W środowiskach produkcyjnych nie zalecamy zezwalania na protokół ICMP przez Zaporę systemu Windows.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Włączanie przekazywania dalej adresu na maszynie wirtualnej myVmNva

[Włączono przekazywanie adresu IP](#turn-on-ip-forwarding) dla interfejsu sieciowego maszyny wirtualnej przy użyciu platformy Azure. System operacyjny maszyny wirtualnej również musi przekazywać dalej ruch sieciowy. Włącz przekazywanie adresu IP dla systemu operacyjnego maszyny wirtualnej *myVmNva* przy użyciu poniższych poleceń.

1. W wierszu polecenia maszyny wirtualnej *myVmPrivate* otwórz pulpit zdalny na maszynie wirtualnej *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. W programie PowerShell na maszynie *myVmNva* wpisz to polecenie, aby włączyć przekazywanie dalej adresu IP:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Uruchom ponownie maszynę wirtualną *myVmNva*. Na pasku zadań wybierz kolejno pozycje **Przycisk uruchamiania** > **Przycisk zasilania**, **Inne (planowane)** > **Kontynuuj**.

    Spowoduje to także odłączenie sesji pulpitu zdalnego.

1. Po ponownym uruchomieniu maszyny wirtualnej *myVmNva* utwórz sesję pulpitu zdalnego z maszyną wirtualną *myVmPublic*. Mając nadal połączenie z maszyną *myVmPrivate*, otwórz wiersz polecenia i uruchom następujące polecenie:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Na pulpicie zdalnym maszyny *myVmPublic* otwórz program PowerShell.

1. Włącz protokół ICMP za pomocą Zapory systemu Windows przez wprowadzenie tego polecenia:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

    Pierwszy przeskok to 10.0.2.4. Jest to prywatny adres IP urządzenia WUS. Drugim przeskokiem jest prywatny adres IP maszyny wirtualnej *myVmPrivate*: 10.0.1.4. Wcześniej dodano trasę do tabeli tras *myRouteTablePublic* i skojarzono ją z podsiecią *publiczną*. W rezultacie platforma Azure wysyłała ruch przez urządzenie WUS, a nie bezpośrednio do podsieci *prywatnej*.

1. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPublic*. Połączenie z maszyną wirtualną *myVmPrivate* pozostanie nadal aktywne.

1. W wierszu polecenia na maszynie wirtualnej *myVmPrivate* wprowadź następujące polecenie:

    ```cmd
    tracert myVmPublic
    ```

    Umożliwia ono przetestowanie routingu ruchu sieciowego z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Odpowiedź jest podobna do poniższego przykładu:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Widać, że platforma Azure kieruje ruch bezpośrednio z maszyny wirtualnej *myVmPrivate* do maszyny wirtualnej *myVmPublic*. Domyślnie platforma Azure kieruje ruch bezpośrednio między podsieciami.

1. Zamknij sesję pulpitu zdalnego dla maszyny wirtualnej *myVmPrivate*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów nie będzie już potrzebna, usuń ją wraz ze wszystkimi zasobami, które zawiera:

1. Na pasku wyszukiwania portalu wpisz *myResourceGroup*.

1. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku została utworzona tabela tras, która została następnie skojarzona z podsiecią. Utworzono proste urządzenie NVA, które kierowało ruch z podsieci publicznej do podsieci prywatnej. Wiesz już, jak to zrobić, więc możesz wdrożyć różne wstępnie skonfigurowane urządzenia WUS z witryny [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking). Pełnią one wiele przydatnych funkcji sieciowych. Aby dowiedzieć się więcej na temat routingu, zobacz [Routing overview (Omówienie routingu)](virtual-networks-udr-overview.md) i [Manage a route table (Zarządzanie tabelą tras)](manage-route-table.md).

Chociaż możesz wdrożyć wiele zasobów platformy Azure w ramach sieci wirtualnej, platforma Azure nie może wdrożyć zasobów dla niektórych usług PaaS w sieci wirtualnej. Możesz ograniczyć dostęp do zasobów niektórych usług PaaS platformy Azure. Ograniczenie musi być jednak dotyczyć ruchu z podsieci sieci wirtualnej. Aby dowiedzieć się, jak ograniczyć dostęp sieciowy do zasobów PaaS platformy Azure, przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Ograniczanie dostępu sieciowego do zasobów PaaS](tutorial-restrict-network-access-to-resources.md)
