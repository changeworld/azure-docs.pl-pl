---
title: 'Samouczek: Usługa DMS umożliwia migrację do wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft'
description: Dowiedz się przeprowadzić migrację z lokalnego programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 78f19fcca51bb9c14fd05013834599c3e0f12779
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662112"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Samouczek: Migrowanie programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database w trybie offline przy użyciu usługi DMS

Usługa Azure Database Migration Service służy do migrowania baz danych z lokalnego wystąpienia programu SQL Server do [wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance.md). Dodatkowe metody, które mogą wymagać ręcznej pracy, są dostępne w artykule [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** z lokalnego wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database, używając usługi Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie wystąpienia usługi Azure Database Migration Service.
> - Tworzenie projektu migracji za pomocą usługi Azure Database Migration Service.
> - Uruchamianie migracji.
> - Monitorowanie migracji.
> - Pobieranie raportu migracji.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migracji w trybie offline z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Aby migracja online, zobacz [migracji programu SQL Server do usługi Azure SQL Database, wystąpienia zarządzanego online przy użyciu usługi DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Utworzenie usługi Azure Virtual Network (VNET) dla usługi Azure Database Migration Service przy użyciu modelu wdrożenia usługi Azure Resource Manager, która zapewnia łączność między lokacjami dla lokalnych serwerów źródłowych, z użyciem usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub sieci [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Poznaj topologie sieci na potrzeby migracji wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

    > [!NOTE]
    > Podczas konfigurowania sieci Wirtualnej, jeśli korzystasz z usługi ExpressRoute za pomocą komunikacji równorzędnej sieci do firmy Microsoft, Dodaj następujące usługi [punktów końcowych](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) do podsieci, w której będą udostępniane usługi:
    > - Docelowy punkt końcowy z bazy danych (na przykład SQL punktu końcowego, punktu końcowego usługi Cosmos DB i tak dalej)
    > - Punkt końcowy magazynu
    > - Punkt końcowy usługi Service bus
    >
    > Ta konfiguracja jest konieczne, ponieważ usługi Azure Database Migration Service nie ma łączności z Internetem.

- Upewnij się, że reguły sieci Wirtualnej, sieciowej grupy zabezpieczeń nie blokuje następujące porty komunikacyjne 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń usługi Azure VNET, zapoznaj się z artykułem [Filter network traffic with network security groups (Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu źródłowej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
- Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
- W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowych baz danych podczas migracji oraz do plików za pośrednictwem portu SMB 445.
- Utworzenie wystąpienia zarządzanego usługi Azure SQL Database — szczegółowe instrukcje znajdują się w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database w witrynie Azure Portal](https://aka.ms/sqldbmi).
- Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.
- Utworzenie udziału sieciowego umożliwiającego wykonanie kopii zapasowej źródłowej bazy danych za pomocą usługi Azure Database Migration Service.
- Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
- Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Usługa Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage na potrzeby operacji przywracania.
- Utworzenie kontenera obiektów blob i pobranie jego identyfikatora URI sygnatury dostępu współdzielonego przez wykonanie czynności opisanych w artykule [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Musisz wybrać wszystkie uprawnienia (odczyt, zapis, usuwanie, wyświetlanie listy) w oknie zasad podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego. Te informacje umożliwiają usłudze Azure Database Migration Service uzyskiwanie dostępu do kontenera konta magazynu w celu przekazywania plików kopii zapasowej używanych na potrzeby migracji baz danych do wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)        

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę **Azure Database Migration Service**, a następnie wybierz usługę **Azure Database Migration Service** z listy rozwijanej.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usłudze Azure Database Migration Service dostęp do źródłowego wystąpienia programu SQL Server i docelowego wystąpienia zarządzanego usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://aka.ms/DMSVnet).

    Aby uzyskać więcej informacji zobacz artykuł [Topologie sieci na potrzeby migracji wystąpienia zarządzanego usługi Azure SQL DB przy użyciu usługi Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie offline**.

   ![Tworzenie projektu usługi DMS](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Wybierz polecenie **Create** (Utwórz), aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Wybierz pozycję **Zapisz**.

4. Na ekranie **Wybierz źródłowe bazy danych** wybierz bazę danych **Adventureworks2012** na potrzeby migracji.

   ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Jeśli używasz usługi SQL Server Integration Services (SSIS), usługa DMS nie obsługuje obecnie migrowania bazy danych katalogu dla projektów/pakietów usługi SSIS (SSISDB) z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Możesz jednak aprowizować usługę SSIS w usłudze Azure Data Factory (ADF) i ponownie wdrożyć projekty/pakiety usługi SSIS w docelowej bazie danych SSISDB hostowanej przez wystąpienie zarządzane usługi Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla lokalizacji docelowej, czyli dla aprowizowanego wcześniej wystąpienia zarządzanego usługi Azure SQL Database, do którego jest migrowana baza danych **AdventureWorks2012**.

    Jeśli jeszcze nie aprowizowano wystąpienia zarządzanego usługi Azure SQL Database, wybierz ten [link](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started), aby aprowizować wystąpienie. Możesz kontynuować tworzenie projektu i gdy wystąpienia zarządzanego usługi Azure SQL Database jest gotowy, wróć do tego określonego projektu, aby wykonać migrację.

     ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Wybierz pozycję **Zapisz**.

## <a name="select-source-databases"></a>Wybieranie źródłowych baz danych

1. Na ekranie **Wybierz źródłowe bazy danych** wybierz źródłową bazę danych, którą chcesz zmigrować.

    ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Wybierz pozycję **Zapisz**.

## <a name="select-logins"></a>Wybieranie identyfikatorów logowania

1. Na ekranie **Wybierz identyfikatory logowania** wybierz identyfikatory logowania, które chcesz zmigrować.

    >[!NOTE]
    >Ta wersja obsługuje tylko migrację danych logowania SQL.

    ![Wybieranie identyfikatorów logowania](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Wybierz pozycję **Zapisz**.

## <a name="configure-migration-settings"></a>Konfigurowanie ustawień migracji

1. Na ekranie **Konfiguruj ustawienia migracji** podaj następujące informacje:

    | | |
    |--------|---------|
    |**Wybierz opcję tworzenia kopii zapasowej źródła** | Wybierz opcję **Dostarczę najnowsze pliki kopii zapasowej**, jeśli pełne pliki kopii zapasowej są już dostępne do użycia przez usługę DMS na potrzeby migracji bazy danych. Wybierz opcję **Umożliwię usłudze Azure Database Migration Service utworzenie plików kopii zapasowej**, jeśli usługa DMS ma pobrać pełną kopię zapasową źródłowej bazy danych i użyć jej na potrzeby migracji. |
    |**Udział lokalizacji sieciowej** | Lokalny udział sieciowy SMB, do którego usługa Azure Database Migration Service może pobrać kopie zapasowe źródłowych baz danych. Konto usługi uruchamiające źródłowe wystąpienie programu SQL Server musi mieć uprawnienia do zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresy IP serwera w udziale sieciowym, na przykład „\\\nazwa_serwera.nazwa_domeny.com\folder_kopii_zapasowych” lub „\\\adres_IP\folder_kopii_zapasowych”.|
    |**Nazwa użytkownika** | Upewnij się, że użytkownik systemu Windows ma uprawnienia pełnej kontroli w udziale sieciowym, który podano powyżej. Usługa Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage na potrzeby operacji przywracania. W przypadku wybrania baz danych obsługujących szyfrowanie TDE użytkownik systemu Windows musi korzystać z wbudowanego konta administratora, a [kontrola konta użytkownika](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) musi być wyłączona, aby usługa Azure Database Migration Service mogła przekazywać i usuwać pliki certyfikatów. |
    |**Hasło** | Hasło użytkownika. |
    |**Ustawienia konta magazynu** | Identyfikator URI sygnatury dostępu współdzielonego umożliwiający usłudze Azure Database Migration Service uzyskiwanie dostępu do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej i którego używa na potrzeby migracji baz danych do wystąpienia zarządzanego usługi Azure SQL Database. [Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego dla kontenera obiektów blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Ustawienia szyfrowania TDE** | W przypadku migrowania źródłowej bazy danych za pomocą przezroczystego szyfrowania danych (TDE) włączone, musisz mieć uprawnienia do zapisu w docelowym wystąpienia zarządzanego usługi Azure SQL Database.  Wybierz z menu rozwijanego subskrypcję, w której aprowizowano wystąpienie zarządzane usługi Azure SQL Database.  Wybierz docelowe **wystąpienie zarządzane usługi Azure SQL Database** z menu rozwijanego. |

    ![Konfigurowanie ustawień migracji](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Wybierz pozycję **Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. Rozwiń sekcję **Opcja weryfikacji**, aby wyświetlić ekran **Wybierz opcję weryfikacji**, określ, czy przeprowadzić weryfikację bazy danych po migracji pod kątem poprawności zapytań, a następnie wybierz przycisk **Zapisz**.

3. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

    ![Podsumowanie projektu migracji](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Wybierz pozycję **Zapisz**.

## <a name="run-the-migration"></a>Uruchamianie migracji

- Wybierz polecenie **Uruchom migrację**.

  Zostanie wyświetlone okno działania migracji, a stan działania będzie określony jako **Oczekujący**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybierz pozycję **Odśwież**, aby zaktualizować wyświetlane dane.
 
   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Możesz rozwinąć kategorie baz danych i danych logowania, aby monitorować stan migracji odpowiednich obiektów serwera.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Po zakończeniu migracji wybierz polecenie **Pobierz raport**, aby pobrać raport zawierający szczegółowe informacje o procesie migracji.

3. Upewnij się, że docelowa baza danych znajduje się w docelowym środowisku wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="next-steps"></a>Kolejne kroki

- Samouczek omawiający migrowanie bazy danych do wystąpienia zarządzanego przy użyciu polecenia RESTORE języka T-SQL, zobacz [Przywracanie kopii zapasowej do wystąpienia zarządzanego przy użyciu polecenia przywracania](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Aby uzyskać informacje na temat wystąpienia zarządzanego, zobacz [Co to jest wystąpienie zarządzane](../sql-database/sql-database-managed-instance.md).
- Aby uzyskać informacje na temat łączenia aplikacji z wystąpieniem zarządzanym, zobacz [Nawiązywanie połączenia z aplikacjami](../sql-database/sql-database-managed-instance-connect-app.md).
