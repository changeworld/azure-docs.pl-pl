---
title: Zarządzanie prywatnymi punktami końcowymi na platformie Azure
description: Dowiedz się, jak utworzyć prywatny punkt końcowy przy użyciu Azure Portal
services: private-link
author: KumudD
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 603e9912d7154913d2cd9f08c3b2040db953fe29
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274655"
---
# <a name="create-a-private-endpoint-using-azure-portal"></a>Tworzenie prywatnego punktu końcowego przy użyciu Azure Portal

Prywatny punkt końcowy to podstawowy blok konstrukcyjny dla prywatnego linku na platformie Azure. Umożliwia ona korzystanie z zasobów platformy Azure, takich jak Virtual Machines (VM), w celu komunikacji z prywatnymi zasobami łączy prywatnych. W tym przewodniku szybki start dowiesz się, jak utworzyć maszynę wirtualną na platformie Azure Virtual Network, SQL Database serwerze z prywatnym punktem końcowym platformy Azure przy użyciu Azure PowerShell. Następnie można bezpiecznie uzyskać dostęp do serwera SQL Database z maszyny wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .


> [!NOTE]
> Prywatne punkty końcowe są niedozwolone w połączeniu z punktami końcowymi usługi w tej samej podsieci.

## <a name="sign-in-to-azure"></a>Zaloguj się do platformy Azure

Zaloguj się do Azure Portal przy https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć służącą do hostowania maszyny wirtualnej, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku (w tym przykładzie programu SQL Server na platformie Azure).

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej


W tej sekcji utworzysz Virtual Network i podsieć, która będzie hostować maszynę wirtualną, która jest używana do uzyskiwania dostępu do prywatnego zasobu linku.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Networking** > **Sieć wirtualna**.
1. W obszarze **Utwórz sieć wirtualną**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź *10.1.0.0/16*. |
    | Ramach | Wybierz swoją subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, wprowadź nazwę *zasobu*, a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Wybierz pozycję **WestCentralUS**.|
    | Nazwa podsieci | Wprowadź nazwę moja *podsieć*. |
    | Zakres adresów podsieci | Wprowadź *10.1.0.0/24*. |
    |||
1. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.


### <a name="create-virtual-machine"></a>Utwórz maszynę wirtualną

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **COMPUTE** > **maszynę wirtualną**.

1. W obszarze **Tworzenie maszyny wirtualnej — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Ramach | Wybierz swoją subskrypcję. |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wprowadź *myVm*. |
    | Okolicy | Wybierz pozycję **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Obraz | Wybierz pozycję **Windows Server 2019 Datacenter**. |
    | Rozmiar | Pozostaw domyślną wartość **Standard DS1 v2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Wprowadź ponownie hasło. |
    | **REGUŁY PORTÓW RUCHU PRZYCHODZĄCEGO** |  |
    | Publiczne porty przychodzące | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDNOŚĆ PIENIĘDZY** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **nie**. |
    |||

1. Wybierz pozycję **Dalej: dyski**.

1. W obszarze **Utwórz maszynę wirtualną**, pozostaw wartości domyślne, a następnie wybierz pozycję **Dalej: sieć**.

1. W obszarze **Utwórz maszynę wirtualną — sieć**wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw wartość domyślną **MyVirtualNetwork**.  |
    | Przestrzeń adresowa | Pozostaw wartość domyślną **10.1.0.0/24**.|
    | Podsieci | Pozostaw domyślną wartość moja **podsieć (10.1.0.0/24)** .|
    | Publiczny adres IP | Pozostaw wartość domyślną **(New) myVm-IP**. |
    | Publiczne porty przychodzące | Wybierz opcję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycję **http** i **RDP**.|
    |||


1. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przejście do strony **Recenzja i tworzenie** , w której platforma Azure weryfikuje konfigurację.

1. Gdy zobaczysz komunikat o **przekazaniu walidacji** , wybierz pozycję **Utwórz**.

## <a name="create-a-sql-database-server"></a>Tworzenie serwera bazy danych SQL
W tej sekcji utworzysz serwer bazy danych SQL na platformie Azure. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób**@no__t **-1 bazy danych @no__t**-3**SQL Database**.

1. W obszarze **Tworzenie bazy danych SQL — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły bazy danych** | |
    | Ramach | Wybierz swoją subskrypcję. |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa bazy danych  | Wprowadź *bazę danych*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    |||
5. W obszarze **serwer**wybierz pozycję **Utwórz nowy**. 
6. W obszarze **nowy serwer**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Nazwa serwera  | Wprowadź *tekst*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę.|
    | Identyfikator logowania administratora serwera| Wprowadź wybraną nazwę administratora. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 8 znaków i spełniać zdefiniowane wymagania. |
    | Lokalizacja | Wybierz region platformy Azure, w którym chcesz mieć SQL Server. |
    
7. Wybierz **przycisk OK**. 
8. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przejście do **przeglądu i tworzenia**@no__t 1page, gdzie platforma Azure weryfikuje konfigurację. 
9. Gdy zobaczysz komunikat o przekazaniu walidacji, wybierz pozycję **Utwórz**. 
10. Gdy zobaczysz komunikat o przekazaniu walidacji, wybierz pozycję Utwórz. 

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz program SQL Server i dodasz do niego prywatny punkt końcowy. 

1. W lewym górnym rogu ekranu w Azure Portal wybierz pozycję **Utwórz zasób** > **Sieć** > **prywatne centrum linków (wersja zapoznawcza)** .
2. W **centrum linków prywatnych — Omówienie**opcji **tworzenia połączenia prywatnego z usługą**wybierz pozycję **Rozpocznij**.
1. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — podstawy**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Ramach | Wybierz swoją subskrypcję. |
    | Grupa zasobów | Wybierz pozycję Moja **resourceName**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa | Wprowadź * myPrivateEndpoint*. Jeśli ta nazwa jest wykonywana, utwórz unikatową nazwę. |
    |Okolicy|Wybierz pozycję **WestCentralUS**.|
    |||
5. Wybierz pozycję **Dalej: zasób**.
6. W obszarze **Utwórz prywatny punkt końcowy zasobu**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz pozycję Połącz z zasobem platformy Azure w moim katalogu.|
    | Ramach| Wybierz swoją subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft. SQL/Server**. |
    | Zasób |Wybierz *pozycję* Wyznacz|
    |Docelowy zasób podrzędny |Wybierz *sqlServer*|
    |||
7. Wybierz pozycję **Dalej: Konfiguracja**.
8. W obszarze **Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — Konfiguracja**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**SIECI**| |
    | Sieć wirtualna| Wybierz pozycję *MyVirtualNetwork*. |
    | Podsieci | Wybierz pozycję Moja *podsieć*. |
    |**PRYWATNA INTEGRACJA Z USŁUGĄ DNS**||
    |Integracja z prywatną strefą DNS |Wybierz pozycję **tak**. |
    |Strefa Prywatna strefa DNS |SELECT *(New) privatelink. Database. Windows. NET* |
    |||

1. Wybierz pozycję **Recenzja + Utwórz**. Nastąpi przejście do **przeglądu i tworzenia**@no__t 1page, gdzie platforma Azure weryfikuje konfigurację. 
2. Gdy zobaczysz **weryfikację przekazaną** message, wybierz pozycję **Utwórz**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Nawiązywanie połączenia z maszyną wirtualną przy użyciu Pulpit zdalny (RDP)


Po utworzeniu*myVm** Połącz się z nim za pośrednictwem Internetu w następujący sposób: 

1. Na pasku wyszukiwania portalu wprowadź *myVm*.

1. Wybierz przycisk **Połącz** . Po wybraniu przycisku **Połącz** zostanie otwarte okno **łączenie z maszyną wirtualną** .

1. Wybierz pozycję **Pobierz plik RDP**. Platforma Azure tworzy plik Remote Desktop Protocol (*RDP*) i pobiera go na komputer.

1. Otwórz pobrany plik RDP *.

    1. Jeśli zostanie wyświetlony monit, wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny wirtualnej.

        > [!NOTE]
        > Może być konieczne wybranie **pozycji więcej opcji** > **Użyj innego konta**, aby określić poświadczenia wprowadzone podczas tworzenia maszyny wirtualnej.

1. Wybierz **przycisk OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie dotyczące certyfikatu, wybierz pozycję **tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej Zminimalizuj go, aby wrócić do pulpitu lokalnego.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera SQL Database z maszyny wirtualnej

1. W Pulpit zdalny *myVM*Otwórz program PowerShell.

2. Wprowadź wartość @ no__t-0. 

    Zostanie wyświetlony komunikat podobny do tego:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Zainstaluj [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. W obszarze **Połącz z serwerem**wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera| Wybierz pozycję **aparat bazy danych**.|
    | Nazwa serwera| Wybierz *MyServer.Database.Windows.NET* |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika jako username@servername, która jest dostępna podczas tworzenia programu SQL Server. |
    |Hasło |Wprowadź hasło podane podczas tworzenia programu SQL Server. |
    |Zapamiętaj hasło|Wybierz pozycję **tak**.|
    |||
1. Wybierz pozycję **Połącz**.
2. Przeglądaj bazy danych z menu po lewej stronie.
3. Zdefiniować Utwórz lub zapytaj informacje z bazy danych.
4. Zamknij połączenie pulpitu zdalnego z *myVm*. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów 
Gdy skończysz korzystać z prywatnego punktu końcowego, programu SQL Server i maszyny wirtualnej, Usuń grupę zasobów i wszystkie zawarte w niej zasoby: 
1. Wprowadź wartość *Webresourcename* in **Wyszukiwanie** box w górnej części portalu i wybierz pozycję Moja *zasobów*@no__t — 5from wyniki wyszukiwania. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. Wprowadź dla elementu webresourcename **Typ Nazwa grupy zasobów** and wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono MASZYNę wirtualną w sieci wirtualnej, na serwerze bazy danych SQL i prywatnym punkcie końcowym dostępu prywatnego. Nawiązano połączenie z jedną maszyną wirtualną z Internetu i bezpieczną komunikację z serwerem usługi SQL Database przy użyciu linku prywatnego. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz [co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md).

