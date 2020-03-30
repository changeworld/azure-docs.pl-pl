---
title: Tworzenie sieci wirtualnej — Szybki start — Azure Portal
titleSuffix: Azure Virtual Network
description: 'Szybki start: tworzenie sieci wirtualnej w witrynie Azure portal. Te sieci umożliwiają zasoby platformy Azure, takie jak maszyny wirtualne, bezpiecznie komunikują się ze sobą i Internetem.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: 1a1593566b8bdb72f322d64c1ee99c7018f49329
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240072"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Szybki start: tworzenie sieci wirtualnej przy użyciu witryny Azure Portal

W tym przewodniku Szybki start dowiesz się, jak utworzyć sieć wirtualną za pomocą witryny Azure portal. Można wdrożyć dwie maszyny wirtualne (maszyny wirtualne). Następnie bezpiecznie komunikujesz się między maszynami wirtualnymi i łączysz się z maszynami wirtualnymi z Internetu. Sieć wirtualna jest podstawowym elementem konstrukcyjnym sieci prywatnej na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne, aby bezpiecznie komunikować się ze sobą i z Internetem.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**. W portalu Azure Marketplace wybierz pozycję **Sieć wirtualna** > **Virtual network**.

1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Nazwa | Wpisz *myVirtualNetwork*. |
    | Lokalizacja | Wybierz pozycję **Wschodnie stany USA**.|

1. Wybierz **dalej: Adresy IP**, a dla **przestrzeni adresowej IPv4**wprowadź *10.1.0.0/16*.

1. Wybierz **pozycję Dodaj podsieć**, a następnie wprowadź *myVirtualSubnet* dla **nazwy podsieci** i *10.1.0.0/24* dla **zakresu adresów podsieci**.

1. Wybierz **pozycję Dodaj**, a następnie wybierz pozycję Recenzja + **utwórz**. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.

1. W **obszarze Tworzenie sieci wirtualnej**wybierz pozycję **Utwórz**.

## <a name="create-virtual-machines"></a>Tworzenie maszyn wirtualnych

W sieci wirtualnej utwórz dwie maszyny wirtualne:

### <a name="create-the-first-vm"></a>Tworzenie pierwszej maszyny wirtualnej

1. Z menu Portalu platformy Azure wybierz polecenie **Utwórz zasób**.

1. W portalu Azure Marketplace wybierz **pozycję Compute** > **Windows Server 2019 Datacenter**. Wybierz **pozycję Utwórz**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Ta grupa zasobów została utworzona w poprzedniej sekcji. |
    | **Szczegóły wystąpienia** |  |
    | Nazwa maszyny wirtualnej | Wprowadź nazwę *myVm1*. |
    | Region | Wybierz pozycję **Wschodnie stany USA**. |
    | Opcje dostępności | Domyślnie **nie jest wymagana nadmiarowość infrastruktury**. |
    | Image (Obraz) | Domyślnie **centrum danych systemu Windows Server 2019**. |
    | Rozmiar | Domyślnie **wartość Standardowa ds1 v2**. |
    | **Konto administratora** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Wprowadź ponownie hasło. |
    | **Reguły portów przychodzących** |  |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wprowadź *HTTP (80)* i *RDP (3389)*. |
    | **Oszczędzaj pieniądze** |  |
    | Masz już licencję systemu Windows? | Wartość domyślna **nie .** |

1. Wybierz **dalej: Dyski**.

1. W **obszarze Tworzenie maszyny wirtualnej — dyski**zachowaj wartości domyślne i wybierz pozycję **Dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Domyślnie **myVirtualNetwork**. |
    | Podsieć | Domyślnie **myVirtualSubnet (10.1.0.0/24)**. |
    | Publiczny adres IP | Domyślnie **(nowy) myVm-ip**. |
    | Grupa zabezpieczeń sieciowej karty sieciowej | Domyślnie **wartość Podstawowa**. |
    | Publiczne porty wejściowe | Domyślnie **zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Domyślnie **http** i **rdp**.

1. Wybierz **dalej: Zarządzanie**.

1. W obszarze **Tworzenie maszyny wirtualnej — zarządzanie** dla opcji **Konto magazynu diagnostyki** wybierz ustawienie **Utwórz nowe**.

1. W obszarze **Tworzenie konta magazynu** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź nazwę *myvmstorageaccount*. Jeśli ta nazwa zostanie przejęta, utwórz unikatową nazwę.|
    | Rodzaj konta | Domyślnie **wartość Magazyn (ogólnego przeznaczenia v1)**. |
    | Wydajność | Domyślnie **wartość Standardowa**. |
    | Replikacja | Domyślnie **wartość magazynu lokalnie nadmiarowego (LRS)**. |

1. Wybierz **przycisk OK**, a następnie wybierz pozycję Recenzja + **utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji.

1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

### <a name="create-the-second-vm"></a>Tworzenie drugiej maszyny wirtualnej

Powtórz procedurę w poprzedniej sekcji, aby utworzyć inną maszynę wirtualną.

> [!IMPORTANT]
> Aby uzyskać **nazwę maszyny wirtualnej,** wprowadź *myVm2*.
>
> W przypadku **konta magazynu Diagnostyka**upewnij się, że wybrałeś **myvmstorageaccount**zamiast go utworzyć.

## <a name="connect-to-a-vm-from-the-internet"></a>Nawiązywanie połączenia z maszyną wirtualną z Internetu

Po utworzeniu *myVm1*połącz się z Internetem.

1. W witrynie Azure portal wyszukaj i wybierz *myVm1*.

1. Wybierz **opcję Połącz**, a następnie **RDP**.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/quick-create-portal/connect-to-virtual-machine.png)

    Zostanie otwarta strona **Połącz.**

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz plik RDP. Po wyświetleniu monitu wybierz pozycję **Połącz**.

1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

    > [!NOTE]
    > Może być konieczne **wybranie opcji Więcej opcji** > **Użyj innego konta,** aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.

## <a name="communicate-between-vms"></a>Nawiązywanie komunikacji między maszynami wirtualnymi

1. Na pulpicie zdalnym maszyny *myVm1* otwórz program PowerShell.

1. Wprowadź polecenie `ping myVm2`.

    Otrzymasz komunikat podobny do tego wyjścia:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    Polecenie `ping` kończy się niepowodzeniem, ponieważ polecenie `ping` używa protokołu ICMP. Domyślnie protokół ICMP jest blokowany przez zaporę Windows.

1. Aby umożliwić wykonanie polecenia ping na maszynie *myVm2* w celu komunikacji z maszyną *myVm1* w późniejszym kroku, wprowadź następujące polecenie:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    To polecenie umożliwia przychodzące ICMP przez zaporę systemu Windows:

1. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm1*.

1. Ponownie wykonaj kroki opisane w sekcji [Nawiązywanie połączenia z maszyną wirtualną z Internetu](#connect-to-a-vm-from-the-internet), ale tym razem nawiąż połączenie z maszyną wirtualną *myVm2*.

1. W wierszu polecenia wprowadź polecenie `ping myvm1`.

    Zostanie wyświetlony komunikat podobny do następującego:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    Otrzymasz odpowiedzi z *myVm1*, ponieważ dozwolone ICMP przez zaporę systemu Windows na maszynie wirtualnej *myVm1* w kroku 3.

1. Zamknij podłączanie pulpitu zdalnego z maszyną wirtualną *myVm2*.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym przewodniku Szybki start utworzono domyślną sieć wirtualną i dwie maszyny wirtualne. Połączenie z jedną maszyną wirtualną z Internetu i bezpieczne komunikowanie się między dwoma maszynami wirtualnymi.

Po zakończeniu korzystania z sieci wirtualnej i maszyn wirtualnych usuń grupę zasobów i wszystkie zasoby, które zawiera:

1. Wyszukaj i wybierz *myResourceGroup*.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę *myResourceGroup*, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o ustawieniach sieci [wirtualnej, zobacz Tworzenie, zmienianie lub usuwanie sieci wirtualnej](manage-virtual-network.md).

Domyślnie platforma Azure umożliwia bezpieczną komunikację między maszynami wirtualnymi. Platforma Azure zezwala tylko na przychodzące połączenia pulpitu zdalnego z maszynami wirtualnymi z systemem Windows z Internetu. Aby dowiedzieć się więcej o typach komunikacji sieciowej maszyn wirtualnych, zobacz [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md).
