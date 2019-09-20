---
title: Zarządzanie prywatnymi punktami końcowymi na platformie Azure
description: Dowiedz się, jak utworzyć prywatny punkt końcowy przy użyciu Azure Portal
services: private-link
author: KumudD
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 15b4d3208be693a5b8d858d30b663347515f5a68
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130288"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal

Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych. W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną na platformie Azure Virtual Network, SQL Database serwerze z prywatnym punktem końcowym platformy Azure przy użyciu Azure PowerShell. Następnie można bezpiecznie uzyskać dostęp do serwera SQL Database z maszyny wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku (w tym przykładzie programu SQL Server na platformie Azure).

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej


W tej sekcji utworzysz Virtual Network i podsieć, która będzie hostować maszynę wirtualną, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć** > **Sieć wirtualna**.
1. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Name | Wprowadź *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subscription | Wybierz subskrypcję.|
    | Resource group | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Location | Wybierz pozycję **WestCentralUS**.|
    | Podsieć — nazwa | Wprowadź nazwę moja *podsieć*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
    |||
1. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.


### <a name="create-virtual-machine"></a>Utwórz maszynę wirtualną

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **obliczeniowy** > **maszyny wirtualnej**.

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subscription | Wybierz subskrypcję. |
    | Resource group | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Region | Wybierz pozycję **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image | Wybierz pozycję **Windows Server 2019 Datacenter**. |
    | Size | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Wybierz opcję **Dalej: Dyski**.

1. W obszarze **Tworzenie maszyny wirtualnej — dyski**pozostaw wartości domyślne i wybierz pozycję **dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Subnet | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)** .|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    |||


1. Wybierz pozycję **Przegląd + utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

## <a name="create-a-sql-database-server"></a>Tworzenie serwera bazy danych SQL
W tej sekcji utworzysz serwer bazy danych SQL na platformie Azure. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** >  > **bazy danych SQL Database**.

1. W obszarze **Tworzenie bazy danych SQL — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    | **Szczegóły bazy danych** | |
    | Subscription | Wybierz subskrypcję. |
    | Resource group | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa bazy danych  | Wprowadź *bazę danych*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    |||
5. W obszarze **serwer**wybierz pozycję **Utwórz nowy**. 
6. W obszarze **nowy serwer**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    |Nazwa serwera  | Wprowadź *tekst*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
    | Identyfikator logowania administratora serwera| Wprowadź wybraną nazwę administratora. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 8 znaków i spełniać zdefiniowane wymagania. |
    | Location | Wybierz region platformy Azure, w którym chcesz mieć SQL Server. |
    
7. Wybierz **przycisk OK**. 
8. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację. 
9. Gdy zobaczysz komunikat o przekazaniu walidacji, wybierz pozycję **Utwórz**. 
10. Gdy zobaczysz komunikat o przekazaniu walidacji, wybierz pozycję Utwórz. 

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz program SQL Server i dodasz do niego prywatny punkt końcowy. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **Sieć** > **prywatne centrum linków (wersja zapoznawcza)** .
2. W **centrum linków prywatnych — Omówienie**opcji **tworzenia połączenia prywatnego z usługą**wybierz pozycję **Rozpocznij**.
1. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subscription | Wybierz subskrypcję. |
    | Resource group | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Name | *Wprowadź myPrivateEndpoint*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    |Region|Wybierz pozycję **WestCentralUS**.|
    |||
5. Wybierz **pozycję Dalej: Zasób**.
6. W obszarze **Utwórz prywatny punkt końcowy zasobu**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu.|
    | Subscription| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. SQL/Server**. |
    | Resource |Wybierz *pozycję* Wyznacz|
    |Docelowy zasób podrzędny |Wybierz *sqlServer*|
    |||
7. Wybierz **pozycję Dalej: Konfiguracja**.
8. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — Konfiguracja**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Value |
    | ------- | ----- |
    |**SIECI**| |
    | Sieć wirtualna| Wybierz pozycję *MyVirtualNetwork*. |
    | Subnet | Wybierz pozycję Moja *podsieć*. |
    |**PRYWATNA INTEGRACJA Z USŁUGĄ DNS**||
    |Integracja z prywatną strefą DNS |Wybierz pozycję **tak**. |
    |Strefa Prywatna strefa DNS |SELECT *(New) privatelink. Database. Windows. NET* |
    |||

1. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację. 
2. Gdy zobaczysz komunikat o  **przekazaniu walidacji**, wybierz pozycję **Utwórz**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu Pulpit zdalny (RDP)


Po utworzeniu*myVm** Połącz się z nim za pośrednictwem Internetu w następujący sposób: 

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Plik Remote Desktop Protocol ( *.rdp*) zostanie utworzony na platformie Azure, a następnie pobrany na komputer.

1. Otwórz pobrany plik RDP *.

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może okazać się konieczne wybranie pozycji **Więcej opcji** > **Użyj innego konta**, aby podać poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera SQL Database z maszyny wirtualnej

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.

2. Wprowadź `nslookup myserver.database.windows.net`. 

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
3. Install [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. In **Connect to server**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **Database Engine**.|
    | Server name| Select *myserver.database.windows.net* |
    | User name | Enter a password provided during the SQL server creation. |
    |Password |Enter a password provided during the SQL server creation. |
    |Remember password|Select **Yes**.|
    |||
1. Select **Connect**.
2. Browse databases from left menu.
3. (Optionally) Create or query information from mydatabase.
4. Close the remote desktop connection to *myVm*. 

## Clean up resources 
When you're done using the private endpoint, SQL server, and the VM, delete the resource group and all of the resources it contains: 
1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results. 
2. Select **Delete resource group**. 
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this quickstart, you created a VM on a virtual network, a SQL database server, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the SQL database server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint?](private-endpoint-overview.md).

