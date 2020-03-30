---
title: 'Samouczek: Migrowanie programu SQL Server w trybie online do wystąpienia zarządzanego sql'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzić migrację online z lokalnego programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298904"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Samouczek: Migrowanie programu SQL Server do wystąpienia zarządzanego bazy danych SQL usługi Azure w trybie online przy użyciu usługi DMS

Usługi migracji bazy danych usługi Azure Database można korzystać z migracji baz danych z lokalnego wystąpienia programu SQL Server do [wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance.md) przy minimalnym przestoju. Dodatkowe metody, które mogą wymagać ręcznej pracy, są dostępne w artykule [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

W tym samouczku należy przeprowadzić migrację bazy danych **Adventureworks2012** z lokalnego wystąpienia programu SQL Server do wystąpienia zarządzanego bazy danych SQL z minimalnym przestojem przy użyciu usługi migracji bazy danych Azure Database.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> * Utwórz wystąpienie usługi Azure Database Migration Service.
> * Utwórz projekt migracji i rozpocznij migrację online przy użyciu usługi migracji usługi Azure Database.
> * Monitorowanie migracji.
> * Wykonaj cutover migracji, gdy jesteś gotowy.

> [!IMPORTANT]
> W przypadku migracji online z programu SQL Server do wystąpienia zarządzanego bazy danych SQL przy użyciu usługi migracji bazy danych Azure, należy podać pełną kopię zapasową bazy danych i kolejne kopie zapasowe dziennika w udziale sieciowym SMB, których usługa może używać do migracji baz danych. Usługa migracji bazy danych Platformy Azure nie inicjuje żadnych kopii zapasowych, a zamiast tego używa istniejących kopii zapasowych, które mogą już mieć w ramach planu odzyskiwania po awarii, dla migracji.
> Upewnij się, że kopie zapasowe są wykonywanie [kopii zapasowych za pomocą opcji WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Upewnij się również, że nie należy dołączać wielu kopii zapasowych (tj. pełnych i t-log) do jednego nośnika kopii zapasowej; każdej kopii zapasowej na osobnym pliku kopii zapasowej. Na koniec można użyć skompresowanych kopii zapasowych, aby zmniejszyć prawdopodobieństwo wystąpienia potencjalnych problemów związanych z migracją dużych kopii zapasowych.

> [!NOTE]
> Korzystanie z usługi migracji bazy danych Azure do wykonywania migracji online wymaga utworzenia wystąpienia na podstawie warstwy cenowej Premium.

> [!IMPORTANT]
> Aby zapewnić optymalne środowisko migracji, firma Microsoft zaleca utworzenie wystąpienia usługi migracji bazy danych platformy Azure w tym samym regionie platformy Azure co docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

> [!IMPORTANT]
> Zmniejsz czas trwania procesu migracji online tak bardzo, jak to możliwe, aby zminimalizować ryzyko przerwania spowodowane przez rekonfigurację wystąpienia lub planowaną konserwację. W przypadku takiego zdarzenia proces migracji rozpocznie się od początku. W przypadku planowanej konserwacji istnieje okres prolongaty 36 godzin przed ponownym uruchomieniem procesu migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację online z programu SQL Server do wystąpienia zarządzanego bazy danych SQL. W przypadku migracji w trybie offline zobacz [Migrowanie programu SQL Server do wystąpienia zarządzanego bazy danych SQL w trybie offline przy użyciu usługi DMS](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Dowiedz się topologii sieci dla migracji wystąpień zarządzanych usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.

    > [!NOTE]
    > Podczas konfigurowania sieci wirtualnej, jeśli używasz usługi ExpressRoute z komunikacją równorzędną sieciową do firmy Microsoft, dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usługi do podsieci, w której usługa będzie aprowizowana:
    >
    > * Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, punkt końcowy usługi Cosmos DB itd.)
    > * Punkt końcowy magazynu
    > * Punkt końcowy magistrali usług
    >
    > Ta konfiguracja jest konieczna, ponieważ usługa migracji bazy danych platformy Azure nie ma łączności z Internetem.
    >
    >Jeśli nie masz łączności lokacja-lokacja między siecią lokalną a platformą Azure lub jeśli przepustowość łączności między lokacjami a lokacjami jest ograniczona, należy rozważyć użycie usługi migracji bazy danych Azure w trybie hybrydowym (Wersja zapoznawcza). Tryb hybrydowy wykorzystuje lokalny proces migracji wraz z wystąpieniem usługi migracji bazy danych platformy Azure uruchomionej w chmurze. Aby utworzyć wystąpienie usługi migracji bazy danych Platformy Azure w trybie hybrydowym, zobacz artykuł [Tworzenie wystąpienia usługi migracji bazy danych Azure w trybie hybrydowym przy użyciu portalu Azure.](https://aka.ms/dms-hybrid-create)

    > [!IMPORTANT]
    > W odniesieniu do konta magazynu używanego w ramach migracji należy:
    > * Wybierz, aby zezwolić całej sieci na dostęp do konta magazynu.
    > * Włącz [delegowanie podsieci](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) w podsieci MI i zaktualizuj reguły zapory konta magazynu, aby zezwolić na tę podsieć.

* Upewnij się, że reguły sieciowej grupy zabezpieczeń sieciowej sieci nie blokują następujących portów komunikacji przychodzącej do usługi migracji bazy danych Azure: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu źródłowej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Otwórz Zaporę systemu Windows, aby zezwolić usłudze migracji bazy danych platformy Azure na dostęp do źródłowego programu SQL Server, który domyślnie jest portem TCP 1433.
* Jeśli używasz wielu wystąpień programu SQL Server przy użyciu portów dynamicznych, możesz włączyć usługę przeglądarki SQL i zezwolić na dostęp do portu UDP 1434 za pośrednictwem zapór, aby usługa migracji bazy danych azure mogła łączyć się z nazwanym wystąpieniem w źródle Serwera.
* Jeśli używasz urządzenia zapory przed źródłowymi bazami danych, może być konieczne dodanie reguł zapory, aby umożliwić usłudze migracji bazy danych platformy Azure dostęp do źródłowych baz danych w celu migracji, a także pliki za pośrednictwem portu SMB 445.
* Utwórz wystąpienie zarządzanego bazy danych SQL, postępając zgodnie ze szczegółami w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database w portalu Azure.](https://aka.ms/sqldbmi)
* Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.
* Udostępnij udział sieciowy SMB, który zawiera wszystkie pliki kopii zapasowej pełnej bazy danych bazy danych i kolejne pliki kopii zapasowej dziennika transakcji, których usługa migracji bazy danych azure może używać do migracji bazy danych.
* Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
* Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Usługa migracji bazy danych Azure personifikuje poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera usługi Azure Storage w celu przywrócenia operacji.
* Utwórz identyfikator aplikacji usługi Azure Active Directory, który generuje klucz identyfikatora aplikacji, którego usługa migracji bazy danych azure może używać do łączenia się z docelowym wystąpieniem zarządzanym usługi Azure Database i kontenerem usługi Azure Storage Container. Aby uzyskać więcej informacji, zobacz artykuł [Use portal to create an Azure Active Directory application and service principal that can access resources (Tworzenie aplikacji usługi Azure Active Directory i jednostki usługi, które mogą uzyskać dostęp do zasobów, w portalu)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Usługa migracji bazy danych Azure wymaga uprawnienia współautora w ramach subskrypcji dla określonego identyfikatora aplikacji. Alternatywnie można utworzyć role niestandardowe, które przyznają określone uprawnienia, których wymaga usługa migracji bazy danych Azure. Aby uzyskać wskazówki krok po kroku dotyczące korzystania z ról niestandardowych, zobacz artykuł [Niestandardowe role dla migracji online z zarządzanych wystąpień sql server do bazy danych SQL](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Utworzenie lub zanotowanie konta usługi Azure Storage w **standardowej warstwie wydajności**, które umożliwia usłudze DMS przekazanie plików kopii zapasowej bazy danych i użycie ich do migrowania bazy danych.  Upewnij się, że utworzono konto usługi Azure Storage w tym samym regionie, w jakim jest tworzone wystąpienie usługi migracji bazy danych platformy Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W portalu Azure wybierz + **Utwórz zasób**, wyszukaj **usługę migracji usługi Azure Database**, a następnie wybierz pozycję Usługa migracji bazy danych Platformy **Azure** z listy rozwijanej.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz jedną.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego programu SQL Server i docelowego wystąpienia zarządzanego bazy danych SQL.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

    Aby uzyskać więcej informacji, zobacz artykuł [Topologie sieci dla migracji wystąpień zarządzanych usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi).

6. Wybierz jednostkę SKU z warstwy cenowej Premium.

    > [!NOTE]
    > Migracje online są obsługiwane tylko w przypadku korzystania z warstwy Premium.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdź wszystkie wystąpienia usługi migracji bazy danych platformy Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

   ![Tworzenie projektu usługi migracji bazy danych platformy Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Wybierz pozycję **Utwórz i uruchom działanie**, aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia TLS, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Wybierz **pozycję Zapisz**.

4. Na ekranie **Wybierz źródłowe bazy danych** wybierz bazę danych **Adventureworks2012** na potrzeby migracji.

   ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Jeśli używasz usługi SQL Server Integration Services (SSIS), usługa DMS nie obsługuje obecnie migrowania bazy danych katalogu dla projektów/pakietów usługi SSIS (SSISDB) z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Możesz jednak aprowizować usługę SSIS w usłudze Azure Data Factory (ADF) i ponownie wdrożyć projekty/pakiety usługi SSIS w docelowej bazie danych SSISDB hostowanej przez wystąpienie zarządzane usługi Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Wybierz **pozycję Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **szczegóły docelowe migracji** określ **identyfikator aplikacji** i **klucz,** których wystąpienie DMS może używać do łączenia się z wystąpieniem docelowym wystąpienia zarządzanego usługi Azure SQL Database i kontem usługi Azure Storage.

    Aby uzyskać więcej informacji, zobacz artykuł [Use portal to create an Azure Active Directory application and service principal that can access resources (Tworzenie aplikacji usługi Azure Active Directory i jednostki usługi, które mogą uzyskać dostęp do zasobów, w portalu)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Wybierz **subskrypcję** zawierającą wystąpienie docelowe wystąpienia zarządzanego usługi Azure SQL Database, a następnie wybierz wystąpienie docelowe.

    Jeśli nie zostały jeszcze zainicjowane inicjowane wystąpienie zarządzanego usługi Azure SQL Database, wybierz [łącze](https://aka.ms/SQLDBMI) ułatwiające aprowizycję wystąpienia. Gdy wystąpienie zarządzane usługi Azure SQL Database będzie gotowe, możesz wrócić do tego projektu, aby przeprowadzić migrację.

3. Podaj **nazwę użytkownika bazy danych SQL** i **hasło**, aby nawiązać połączenie z wystąpieniem zarządzanym usługi Azure SQL Database.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Wybierz **pozycję Zapisz**.

## <a name="select-source-databases"></a>Wybieranie źródłowych baz danych

1. Na ekranie **Wybierz źródłowe bazy danych** wybierz źródłową bazę danych, którą chcesz zmigrować.

    ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Wybierz **pozycję Zapisz**.

## <a name="configure-migration-settings"></a>Konfigurowanie ustawień migracji

1. Na ekranie **Konfiguruj ustawienia migracji** podaj następujące informacje:

    | | |
    |--------|---------|
    |**Udział lokalizacji sieciowej protokołu SMB** | Lokalny udział sieciowy SMB lub udział plików platformy Azure, który zawiera pliki kopii zapasowej pełnej bazy danych i pliki kopii zapasowej dziennika transakcji, których usługa migracji bazy danych azure może używać do migracji. Konto usługi, na którym uruchomiono źródłowe wystąpienie programu SQL Server, musi mieć uprawnienia do odczytu/zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresy IP serwera w udziale sieciowym, na przykład „\\\nazwa_serwera.nazwa_domeny.com\folder_kopii_zapasowych” lub „\\\adres_IP\folder_kopii_zapasowych”. Aby zwiększyć wydajność, zaleca się użycie oddzielnego folderu dla każdej bazy danych do migracji. Ścieżkę udziału plików na poziomie bazy danych można podać za pomocą opcji **Ustawienia zaawansowane.** |
    |**Nazwa użytkownika** | Upewnij się, że użytkownik systemu Windows ma uprawnienia pełnej kontroli w udziale sieciowym, który podano powyżej. Usługa migracji bazy danych Azure będzie personifikować poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera usługi Azure Storage w celu przywrócenia operacji. Jeśli używasz udziału plików platformy Azure, użyj nazwy konta magazynu wstępnie zaaskowane z azure\ jako nazwę użytkownika. |
    |**Hasło** | Hasło użytkownika. Jeśli używasz udziału plików platformy Azure, użyj klucza konta magazynu jako hasła. |
    |**Subskrypcja konta usługi Azure Storage** | Wybierz subskrypcję, która zawiera konto usługi Azure Storage. |
    |**Konto usługi Azure Storage** | Wybierz konto usługi Azure Storage, na które usługa DMS może przekazać pliki kopii zapasowej z udziału sieciowego protokołu SMB używane podczas migracji bazy danych.  Zalecamy wybranie konta usługi Storage w tym samym regionie co usługa DMS w celu uzyskania optymalnej wydajności przekazywania plików. |

    ![Konfigurowanie ustawień migracji](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Jeśli usługa migracji bazy danych platformy Azure pokazuje błąd "Błąd systemu 53" lub "Błąd systemu 57", przyczyną może być niemożność dostępu usługi migracji bazy danych platformy Azure do uzyskania dostępu do udziału plików platformy Azure. Jeśli napotkasz jeden z tych błędów, udziel dostępu do konta magazynu z sieci wirtualnej, korzystając z instrukcji [tutaj](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Jeśli funkcja sprawdzania sprzężenia zwrotnego jest włączona, a źródłowy program SQL Server i udział plików znajdują się na tym samym komputerze, źródło nie będzie mogło uzyskać dostępu do plików zająca przy użyciu nazwy FQDN. Aby rozwiązać ten problem, wyłącz funkcję sprawdzania sprzężenia zwrotnego, korzystając z instrukcji [tutaj](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Wybierz **pozycję Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

    ![Podsumowanie projektu migracji](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Uruchamianie i monitorowanie migracji

1. Wybierz polecenie **Uruchom migrację**.

2. Na ekranie działania migracji wybierz pozycję **Odśwież**, aby zaktualizować wyświetlane dane.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Możesz rozwinąć kategorie baz danych i danych logowania, aby monitorować stan migracji odpowiednich obiektów serwera.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Przeprowadzanie migracji jednorazowej

Po przywróceniu pełnej kopii zapasowej bazy danych w wystąpieniu docelowym wystąpienia zarządzanego bazy danych SQL baza danych jest dostępna do wykonywania migracji.

1. Gdy wszystko będzie gotowe do zakończenia migracji bazy danych online, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Zatrzymaj cały ruch przychodzący do źródłowych baz danych.

3. Weź kopię zapasową końcowego fragmentu dziennika, udostępnij plik kopii zapasowej w udziale sieciowym protokołu SMB i zaczekaj na przywrócenie ostatecznej wersji kopii zapasowej dziennika transakcji.

    W tym momencie wartość w polu **Oczekujące zmiany** będzie wynosić 0.

4. Wybierz polecenie **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.

    ![Przygotowanie do zakończenia migracji jednorazowej](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Gdy stan migracji bazy danych pokazuje **zakończone,** połącz aplikacje z nowym wystąpieniem docelowym wystąpienia zarządzanego usługi Azure SQL Database.

    ![Ukończona migracja jednorazowa](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z samouczkiem przedstawiającym sposób migracji bazy danych do wystąpienia zarządzanego za pomocą polecenia T-SQL RESTORE, zobacz [Przywracanie kopii zapasowej do wystąpienia zarządzanego za pomocą polecenia restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Aby uzyskać informacje o [wystąpieniu zarządzanym,](../sql-database/sql-database-managed-instance.md)zobacz Co to jest wystąpienie zarządzane .
* Aby uzyskać informacje dotyczące łączenia aplikacji z wystąpieniem zarządzanym, zobacz [Łączenie aplikacji](../sql-database/sql-database-managed-instance-connect-app.md).
