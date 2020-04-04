---
title: Szybki start — zarządzanie prywatnymi punktami końcowymi na platformie Azure
description: Dowiedz się, jak utworzyć prywatny punkt końcowy przy użyciu portalu Azure w tym przewodniku Szybki start
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 1bdc089bb89a732e329bf7d3ffd3d5b5c09ba408
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637241"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-portal"></a>Szybki start: tworzenie prywatnego punktu końcowego przy użyciu portalu Azure

Prywatny punkt końcowy jest podstawowym blokiem konstrukcyjnym dla łącza prywatnego na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne (maszyny wirtualne), do komunikowania się prywatnie z zasobami łączy prywatnych. W tym przewodniku Szybki start dowiesz się, jak utworzyć maszynę wirtualną w sieci wirtualnej platformy Azure, serwerze bazy danych SQL z prywatnym punktem końcowym platformy Azure przy użyciu witryny Azure portal. Następnie można bezpiecznie uzyskać dostęp do serwera bazy danych SQL z maszyny Wirtualnej.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do zasobu łącza prywatnego (serwer SQL na platformie Azure w tym przykładzie).

## <a name="virtual-network-and-parameters"></a>Sieć wirtualna i parametry

W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do zasobu łącza prywatnego.

W tej sekcji należy zastąpić następujące parametry w krokach poniższymi informacjami:

| Parametr                   | Wartość                |
|-----------------------------|----------------------|
| **\<>nazwa grupy zasobów**  | myResourceGroup |
| **\<>nazwa sieci wirtualnej** | myVirtualNetwork          |
| **\<nazwa regionu>**          | Zachodnio-środkowe stany USA    |
| **\<>przestrzeni adresowej IPv4**   | 10.1.0.0/16          |
| **\<>nazwa podsieci**          | mySubnet        |
| **\<>zakresu adresu podsieci** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w portalu Azure wybierz pozycję **Utwórz maszynę wirtualną** > **obliczeniową** > **Virtual Machine**zasobów .

1. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wpisz *myVm*. |
    | Region | Wybierz **WestCentralUS**. |
    | Opcje dostępności | Pozostaw wartość domyślną **Brak wymaganej nadmiarowości infrastruktury**. |
    | Image (Obraz) | Wybierz **pozycję Centrum danych systemu Windows Server 2019**. |
    | Rozmiar | Pozostaw wartość domyślną **Standardowy DS1, wersja 2**. |
    | **KONTO ADMINISTRATORA** |  |
    | Nazwa użytkownika | Wprowadź wybraną nazwę użytkownika. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Potwierdź hasło | Ponownie wprowadź hasło. |
    | **REGUŁY PORTÓW WEJŚCIOWYCH** |  |
    | Publiczne porty wejściowe | Pozostaw wartość domyślną **Brak**. |
    | **OSZCZĘDZAJ PIENIĄDZE** |  |
    | Masz już licencję systemu Windows? | Pozostaw wartość domyślną **Nie**. |
    |||

1. Wybierz **dalej: Dyski**.

1. W **obszarze Tworzenie maszyny wirtualnej — dyski**pozostaw ustawienia domyślne i wybierz pozycję **Dalej: Sieć**.

1. W obszarze **Tworzenie maszyny wirtualnej — sieć** wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Sieć wirtualna | Pozostaw domyślną **myvirtualnetwork**.  |
    | Przestrzeń adresowa | Pozostaw domyślny **10.1.0.0/24**.|
    | Podsieć | Pozostaw domyślną **mySubnet (10.1.0.0/24)**.|
    | Publiczny adres IP | Pozostaw wartość domyślną **(nowy) myVm-ip**. |
    | Publiczne porty wejściowe | Wybierz pozycję **Zezwalaj na wybrane porty**. |
    | Wybierz porty wejściowe | Wybierz pozycje **HTTP** i **RDP**.|
    |||


1. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji.

1. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**.

## <a name="create-a-sql-database-server"></a>Tworzenie serwera bazy danych SQL
W tej sekcji utworzysz serwer bazy danych SQL na platformie Azure. 

1. W lewym górnym rogu ekranu w portalu Azure wybierz pozycję **Utwórz** > **bazę danych SQL**baz danych**baz danych** > zasobów .

1. W **obszarze Tworzenie bazy danych SQL — podstawy**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły bazy danych** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa bazy danych  | Wprowadź *mydatabase*. Jeśli ta nazwa zostanie przejęta, utwórz unikatową nazwę. |
    |||
5. W **obszarze Serwer**wybierz pozycję **Utwórz nowy**. 
6. W **obszarze Nowy serwer**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Nazwa serwera  | Wpisz *myserver*. Jeśli ta nazwa zostanie przejęta, utwórz unikatową nazwę.|
    | Identyfikator logowania administratora serwera| Wprowadź wybraną nazwę administratora. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 8 znaków i spełniać określone wymagania. |
    | Lokalizacja | Wybierz region platformy Azure, w którym chcesz, aby serwer SQL Server miał się odnajdować. |
    
7. Kliknij przycisk **OK**. 
8. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji. 
9. Po wyświetleniu komunikatu O przekazaniu weryfikacji wybierz pozycję **Utwórz**. 
10. Po wyświetleniu komunikatu O przekazaniu weryfikacji wybierz pozycję Utwórz. 

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz serwer SQL i dodasz do niego prywatny punkt końcowy. 

1. W lewym górnym rogu ekranu w witrynie Azure portal wybierz pozycję **Utwórz prywatny** > program**Networking Networking** > **Private Link Center (Preview)** wybierz pozycję Utwórz źródło zasobów .
2. W **centrum łączy prywatnych — przegląd**, w opcji Tworzenie połączenia prywatnego z **usługą**wybierz pozycję **Start**.
1. W **obszarze Tworzenie prywatnego punktu końcowego (Wersja zapoznawcza) — podstawy**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa | Wprowadź *myPrivateEndpoint*. Jeśli ta nazwa zostanie przejęta, utwórz unikatową nazwę. |
    |Region|Wybierz **WestCentralUS**.|
    |||
5. Wybierz **dalej: Zasób**.
6. W **obszarze Tworzenie prywatnego punktu końcowego — zasób**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz opcję połącz się z zasobem platformy Azure w moim katalogu.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz pozycję **Microsoft.Sql/servers**. |
    | Zasób |Wybierz *serwer myServer*|
    |Docelowy podsób |Wybierz *sqlServer*|
    |||
7. Wybierz **dalej: Konfiguracja**.
8. W **obszarze Tworzenie prywatnego punktu końcowego (wersja zapoznawcza) — konfiguracja**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieci**| |
    | Sieć wirtualna| Wybierz *MyVirtualNetwork*. |
    | Podsieć | Wybierz *mySubnet*. |
    |**PRYWATNA INTEGRACJA Z SYSTEMEM DNS**||
    |Integracja z prywatną strefą DNS |Wybierz **pozycję Tak**. |
    |Prywatna strefa DNS |Wybierz *(nowy)privatelink.database.windows.net* |
    |||

1. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji. 
2. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**. 
 
## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>Łączenie się z maszyną wirtualną przy użyciu pulpitu zdalnego (RDP)


Po utworzeniu **myVm**, połącz się z nim z Internetu w następujący sposób: 

1. W pasku wyszukiwania portalu wpisz *myVm*.

1. Wybierz przycisk **Połącz**. Po wybraniu przycisku **Połącz** zostanie otwarta strona **Łączenie z maszyną wirtualną**.

1. Wybierz opcję **Pobierz plik RDP**. Na platformie Azure zostanie utworzony plik Remote Desktop Protocol (*rdp*), który zostanie pobrany na komputer.

1. Otwórz *pobrany plik rdp.*

    1. Po wyświetleniu monitu wybierz pozycję **Połącz**.

    1. Wprowadź nazwę użytkownika i hasło określone podczas tworzenia maszyny Wirtualnej.

        > [!NOTE]
        > Może być konieczne **wybranie opcji Więcej opcji** > **Użyj innego konta,** aby określić poświadczenia wprowadzone podczas tworzenia maszyny Wirtualnej.

1. Kliknij przycisk **OK**.

1. Podczas procesu logowania może pojawić się ostrzeżenie o certyfikacie. Jeśli zostanie wyświetlone ostrzeżenie o certyfikacie, wybierz opcję **Tak** lub **Kontynuuj**.

1. Po wyświetleniu pulpitu maszyny wirtualnej zminimalizuj ją i wróć z powrotem do pulpitu lokalnego.  

## <a name="access-the-sql-database-server-privately-from-the-vm"></a>Dostęp do serwera bazy danych SQL prywatnie z maszyny Wirtualnej

1. W pulpicie zdalnym *myVM*otwórz program PowerShell.

2. Wprowadź polecenie `nslookup myserver.database.windows.net`. 

    Otrzymasz wiadomość podobną do tej:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. Zainstaluj [program SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

4. W **obszarze Połącz z serwerem**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera| Wybierz pozycję **Aparat bazy danych**.|
    | Nazwa serwera| Wybierz *myserver.database.windows.net* |
    | Nazwa użytkownika | Wprowadź nazwę username@servername użytkownika, która jest podana podczas tworzenia serwera SQL. |
    |Hasło |Wprowadź hasło podane podczas tworzenia serwera SQL. |
    |Zapamiętaj hasło|Wybierz **pozycję Tak**.|
    |||
1. Wybierz przycisk **Połącz**.
2. Przeglądaj bazy danych z lewego menu.
3. (Opcjonalnie) Tworzenie lub wykonywanie zapytań z mydatabase.
4. Zamknij połączenie pulpitu zdalnego do *myVm*. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów 
Po zakończeniu korzystania z prywatnego punktu końcowego, serwera SQL i maszyny Wirtualnej usuń grupę zasobów i wszystkie zasoby, które zawiera: 
1. Wprowadź *myResourceGroup* w polu **wyszukiwania** u góry portalu i wybierz *myResourceGroup* z wyników wyszukiwania. 
2. Wybierz pozycję **Usuń grupę zasobów**. 
3. W polu **WPISZ NAZWĘ GRUPY ZASOBÓW:** wprowadź nazwę myResourceGroup, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną w sieci wirtualnej, serwerze bazy danych SQL i prywatnym punkcie końcowym dla dostępu prywatnego. Połączenie z jedną maszyną wirtualną z Internetu i bezpieczne komunikowanie się z serwerem bazy danych SQL przy użyciu łącza prywatnego. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz [Co to jest prywatny punkt końcowy platformy Azure?](private-endpoint-overview.md)
