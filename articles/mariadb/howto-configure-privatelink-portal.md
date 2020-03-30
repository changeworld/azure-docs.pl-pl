---
title: Link prywatny — witryna Azure portal — usługa Azure Database for MariaDB
description: Dowiedz się, jak skonfigurować łącze prywatne dla usługi Azure Database dla bazy danych MariaDB z witryny Azure portal
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 3f421cad64caf91b898bb1ec13dc909b93b7f72d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370342"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mariadb-using-portal"></a>Tworzenie łącza prywatnego dla usługi Azure Database dla bazy danych dla usługi MariaDB i zarządzanie nimi przy użyciu portalu

Prywatny punkt końcowy jest podstawowym blokiem konstrukcyjnym dla łącza prywatnego na platformie Azure. Umożliwia zasoby platformy Azure, takie jak maszyny wirtualne (maszyny wirtualne), do komunikowania się prywatnie z zasobami łączy prywatnych.  W tym artykule dowiesz się, jak używać witryny Azure portal do tworzenia maszyny Wirtualnej w sieci wirtualnej platformy Azure i usługi Azure Database dla serwera MariaDB z prywatnym punktem końcowym platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, gdzie usługa Azure Database dla MariaDB obsługuje warstwy cenowe ogólnego przeznaczenia i zoptymalizowane pod kątem pamięci.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do [Portalu Azure](https://portal.azure.com).

## <a name="create-an-azure-vm"></a>Tworzenie maszyny Wirtualnej platformy Azure

W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do zasobu łącza prywatnego (serwera MariaDB na platformie Azure).

### <a name="create-the-virtual-network"></a>Tworzenie sieci wirtualnej
W tej sekcji utworzysz sieć wirtualną i podsieć do obsługi maszyny Wirtualnej, która jest używana do uzyskiwania dostępu do zasobu łącza prywatnego.

1. W lewym górnym rogu ekranu wybierz pozycję **Utwórz zasób** > **Sieć wirtualna sieci** > **Virtual network**.
2. W obszarze **Utwórz sieć wirtualną** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa | Wprowadź *MyVirtualNetwork*. |
    | Przestrzeń adresowa | Wprowadź adres *10.1.0.0/16*. |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nową**, wprowadź nazwę *myResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Wybierz pozycję **Europa Zachodnia**.|
    | Podsieć — nazwa | Wpisz *mySubnet*. |
    | Zakres adresów podsieci: 10.41.0.0/24 | Wprowadź *10.1.0.0/24*. |
    |||
3. Pozostaw resztę jako domyślną i wybierz pozycję **Utwórz**.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu ekranu w portalu Azure wybierz pozycję **Utwórz maszynę wirtualną** > **obliczeniową** > **Virtual Machine**zasobów .

2. W obszarze **Tworzenie maszyny wirtualnej — ustawienia podstawowe** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **SZCZEGÓŁY PROJEKTU** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.  |
    | **SZCZEGÓŁY WYSTĄPIENIA** |  |
    | Nazwa maszyny wirtualnej | Wpisz *myVm*. |
    | Region | Wybierz pozycję **Europa Zachodnia**. |
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

## <a name="create-an-azure-database-for-mariadb"></a>Tworzenie usługi Azure Database for MariaDB

W tej sekcji utworzysz usługę Azure Database dla serwera MariaDB na platformie Azure. 

1. W lewym górnym rogu ekranu w witrynie Azure portal wybierz pozycję **Utwórz bazę** > **danych** > zasobów**usługi Azure Database for MariaDB**.

1. W **usłudze Azure Database for MariaDB** podaj następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **Szczegóły serwera** |  |
    |Nazwa serwera  | Wpisz *myserver*. Jeśli ta nazwa zostanie przejęta, utwórz unikatową nazwę.|
    | Nazwa użytkownika administratora| Wprowadź wybraną nazwę administratora. |
    | Hasło | Wprowadź wybrane hasło. Hasło musi mieć co najmniej 8 znaków i spełniać określone wymagania. |
    | Lokalizacja | Wybierz region platformy Azure, w którym chcesz, aby serwer MariaDB miał się odnajdować. |
    |Wersja  | Wybierz wymaganą wersję bazy danych serwera MariaDB.|
    | Zasoby obliczeniowe + magazyn| Wybierz warstwę cenową, która jest potrzebna dla serwera na podstawie obciążenia. |
    |||
 
7. Kliknij przycisk **OK**. 
8. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji. 
9. Po wyświetleniu komunikatu O przekazaniu weryfikacji wybierz pozycję **Utwórz**. 
10. Po wyświetleniu komunikatu O przekazaniu weryfikacji wybierz pozycję Utwórz. 

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

W tej sekcji utworzysz prywatny punkt końcowy do serwera MariaDB do niego. 

1. W lewym górnym rogu ekranu w witrynie Azure portal wybierz pozycję **Utwórz** > **łącze prywatne****w sieci** > zasobów .
2. W **centrum łączy prywatnych — przegląd**, w opcji Tworzenie połączenia prywatnego z **usługą**wybierz pozycję **Start**.

    ![Omówienie łącza prywatnego](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. W **obszarze Tworzenie prywatnego punktu końcowego — podstawy**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | **Szczegóły projektu** | |
    | Subskrypcja | Wybierz subskrypcję. |
    | Grupa zasobów | Wybierz pozycję **myResourceGroup**. Utworzono to w poprzedniej sekcji.|
    | **Szczegóły wystąpienia** |  |
    | Nazwa | Wprowadź *myPrivateEndpoint*. Jeśli ta nazwa zostanie przejęta, utwórz unikatową nazwę. |
    |Region|Wybierz pozycję **Europa Zachodnia**.|
    |||
5. Wybierz **dalej: Zasób**.
6. W **obszarze Tworzenie prywatnego punktu końcowego — zasób**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |Metoda połączenia  | Wybierz opcję połącz się z zasobem platformy Azure w moim katalogu.|
    | Subskrypcja| Wybierz subskrypcję. |
    | Typ zasobu | Wybierz **microsoft.DBforMariaDB/servers**. |
    | Zasób |Wybierz *serwer myServer*|
    |Docelowy podsób |Wybierz *mariadbServer*|
    |||
7. Wybierz **dalej: Konfiguracja**.
8. W **obszarze Tworzenie prywatnego punktu końcowego — konfiguracja**, wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    |**Sieci**| |
    | Sieć wirtualna| Wybierz *MyVirtualNetwork*. |
    | Podsieć | Wybierz *mySubnet*. |
    |**PRYWATNA INTEGRACJA Z SYSTEMEM DNS**||
    |Integracja z prywatną strefą DNS |Wybierz **pozycję Tak**. |
    |Prywatna strefa DNS |Wybierz *(nowy)privatelink.mariadb.database.azure.com* |
    |||

1. Wybierz pozycję **Przegląd + utwórz**. Zostaniesz przesuń do **review + tworzenie** strony, gdzie platforma Azure sprawdza poprawność konfiguracji. 
2. Po wyświetleniu komunikatu **O przekazaniu weryfikacji** wybierz pozycję **Utwórz**. 

    ![Utworzony link prywatny](media/concepts-data-access-and-security-private-link/show-mariadb-private-link.png)

    > [!NOTE] 
    > Sieć FQDN w ustawieniach DNS klienta nie jest rozpoznawana jako prywatny adres IP skonfigurowany. Musisz skonfigurować strefę DNS dla skonfigurowanego FQDN, jak pokazano [tutaj](../dns/dns-operations-recordsets-portal.md).

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

## <a name="access-the-mariadb-server-privately-from-the-vm"></a>Dostęp do serwera MariaDB prywatnie z maszyny Wirtualnej

1. W pulpicie zdalnym *myVM*otwórz program PowerShell.

2. Wprowadź `nslookup mydemomserver.privatelink.mariadb.database.azure.com`. 

    Otrzymasz wiadomość podobną do tej:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemoMariaDBserver.privatelink.mariadb.database.azure.com
    Address:  10.1.3.4
    ```

3. Przetestuj połączenie łącza prywatnego dla serwera MariaDB przy użyciu dowolnego dostępnego klienta. W poniższym przykładzie użyłem [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) do wykonania operacji.


4. W **obszarze Nowe połączenie**wprowadź lub wybierz te informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Typ serwera| Wybierz **MariaDB**.|
    | Nazwa serwera| Wybierz *mydemoserver.privatelink.mariadb.database.azure.com* |
    | Nazwa użytkownika | Wprowadź nazwę username@servername użytkownika, która jest podana podczas tworzenia serwera MariaDB. |
    |Hasło |Wprowadź hasło podane podczas tworzenia serwera MariaDB. |
    |Protokół SSL|Wybierz **opcję Wymagane**.|
    ||

5. Wybierz **opcję Testuj połączenie** lub **OK**.

6. (Opcjonalnie) Przeglądanie baz danych z lewego menu i tworzenie lub wykonywanie zapytań z bazy danych MariaDB

7. Zamknij połączenie pulpitu zdalnego do myVm.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Po zakończeniu korzystania z prywatnego punktu końcowego, serwera MariaDB i maszyny Wirtualnej usuń grupę zasobów i wszystkie zasoby, które zawiera:

1. Wprowadź *myResourceGroup* w polu **wyszukiwania** u góry portalu i wybierz *myResourceGroup* z wyników wyszukiwania.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź myResourceGroup dla **TYPU NAZWA GRUPY ZASOBÓW** i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym instrukcje utworzono maszynę wirtualną w sieci wirtualnej, usługi Azure Database dla MariaDB i prywatny punkt końcowy dla dostępu prywatnego. Połączenie z jedną maszyną wirtualną z Internetu i bezpieczne komunikowanie się z serwerem MariaDB przy użyciu łącza prywatnego. Aby dowiedzieć się więcej o prywatnych punktach końcowych, zobacz [Co to jest prywatny punkt końcowy platformy Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
