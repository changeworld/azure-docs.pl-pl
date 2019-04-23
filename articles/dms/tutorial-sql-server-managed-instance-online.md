---
title: 'Samouczek: Azure Database Migration Service umożliwia wykonanie migracji online programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database | Dokumentacja firmy Microsoft'
description: Dowiedz się dokonać migracji online z lokalnego programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database, za pomocą usługi Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: d9d57df3ec8e859a1f3257cb54e423d0006286b1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880189"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Samouczek: Migrowanie programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database online przy użyciu usługi DMS

Usługa Azure Database Migration Service służy do migrowania baz danych z lokalnego wystąpienia programu SQL Server do [wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance.md) z minimalnym czasem przestoju. Dodatkowe metody, które mogą wymagać ręcznej pracy, są dostępne w artykule [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

W tym samouczku przeprowadzisz migrację bazy danych **Adventureworks2012** z lokalnego wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database z minimalnym czasem przestoju, używając usługi Azure Database Migration Service.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Tworzenie wystąpienia usługi Azure Database Migration Service.
> - Tworzenie projektu migracji i rozpoczynanie migracji online za pomocą usługi Azure Database Migration Service.
> - Monitorowanie migracji.
> - Przeprowadzanie migracji jednorazowej, gdy wszystko będzie gotowe.

> [!NOTE]
> Przeprowadzenie migracji online przy użyciu usługi Azure Database Migration Service wymaga utworzenia wystąpienia opartego na warstwie cenowej Premium.
> [!IMPORTANT]
> W celu uzyskania optymalnego środowiska migracji firma Microsoft zaleca utworzenie wystąpienia usługi Azure Database Migration Service w tym samym regionie platformy Azure, w którym znajduje się docelowa baza danych. Przenoszenie danych między regionami lub lokalizacjami geograficznymi może spowalniać proces migracji i powodować błędy.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano online migracji z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Dla migracji w trybie offline, zobacz [migracji programu SQL Server do usługi Azure SQL Database, wystąpienia zarządzanego w trybie offline przy użyciu usługi DMS](tutorial-sql-server-to-managed-instance.md).

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

- Upewnij się, że reguły sieci Wirtualnej, sieciowej grupy zabezpieczeń nie blokują następujących portów komunikacji przychodzącej do usługi Azure Database Migration Service: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej grupy zabezpieczeń usługi Azure VNET, zapoznaj się z artykułem [Filter network traffic with network security groups (Filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu źródłowej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
- Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
- W przypadku korzystania z urządzenia zapory przed źródłowymi bazami danych konieczne może być dodanie reguł zapory, aby zezwolić usłudze Azure Database Migration Service na dostęp do źródłowych baz danych podczas migracji oraz do plików za pośrednictwem portu SMB 445.
- Utworzenie wystąpienia zarządzanego usługi Azure SQL Database — szczegółowe instrukcje znajdują się w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database w witrynie Azure Portal](https://aka.ms/sqldbmi).
- Upewnij się, że dane logowania używany do łączenia źródła programu SQL Server i wystąpienia zarządzanego docelowy są członkami roli serwera sysadmin.
- Podanie udziału sieciowego protokołu SMB, który zawiera pliki kopii zapasowych całej bazy danych oraz kolejne pliki kopii zapasowych dzienników transakcji, których usługa Azure Database Migration Service potrzebuje do przeprowadzenia migracji bazy danych.
- Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
- Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Usługa Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage na potrzeby operacji przywracania.
- Utworzenie identyfikatora aplikacji usługi Azure Active Directory, który generuje klucz identyfikatora aplikacji używany przez usługę DMS do połączenia z docelowym wystąpieniem zarządzanym usługi Azure Database i kontenerem usługi Azure Storage. Aby uzyskać więcej informacji, zobacz artykuł [Use portal to create an Azure Active Directory application and service principal that can access resources (Tworzenie aplikacji usługi Azure Active Directory i jednostki usługi, które mogą uzyskać dostęp do zasobów, w portalu)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
- Utworzenie lub zanotowanie konta usługi Azure Storage w **standardowej warstwie wydajności**, które umożliwia usłudze DMS przekazanie plików kopii zapasowej bazy danych i użycie ich do migrowania bazy danych.  Sprawdzenie, czy utworzono konto usługi Azure Storage w tym samym regionie co utworzona usługa DMS.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)        
2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Wyszukaj „migration”, a następnie po prawej stronie pozycji **Microsoft.DataMigration** wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W witrynie Azure Portal wybierz pozycję + **Utwórz zasób**, wyszukaj usługę **Azure Database Migration Service**, a następnie wybierz usługę **Azure Database Migration Service** z listy rozwijanej.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz nową.

    Sieć wirtualna zapewnia usłudze Azure Database Migration Service dostęp do źródłowego wystąpienia programu SQL Server i docelowego wystąpienia zarządzanego usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure Portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu witryny Azure Portal](https://aka.ms/DMSVnet).

    Aby uzyskać więcej informacji, zobacz artykuł [Network topologies for Azure SQL Database Managed Instance migrations using the Azure Database Migration Service (Topologie sieci na potrzeby migracji wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi Azure Database Migration Service)](https://aka.ms/dmsnetworkformi).

6. Wybierz jednostkę SKU z warstwy cenowej Premium.

    > [!NOTE]
    > Migracje online są obsługiwane tylko w przypadku korzystania z warstwy Premium. 

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdowanie wszystkich wystąpień usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Na ekranie **Azure Database Migration Service** wyszukaj nazwę utworzonego wystąpienia, a następnie wybierz to wystąpienie.

3. Wybierz pozycję + **Nowy projekt migracji**.

4. Na ekranie **Nowy projekt migracji** wpisz nazwę projektu, w polu tekstowym **Typ serwera źródłowego** wybierz pozycję **SQL Server**, w polu tekstowym **Typ serwera docelowego** wybierz pozycję **Wystąpienie zarządzane usługi Azure SQL Database**, a następnie w polu **Wybierz typ działania** wybierz pozycję **Migracja danych w trybie online**.

   ![Tworzenie projektu usługi DMS](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Wybierz pozycję **Utwórz i uruchom działanie**, aby utworzyć projekt.

## <a name="specify-source-details"></a>Określanie szczegółów źródła

1. Na ekranie **Szczegóły źródła migracji** określ szczegóły połączenia dla źródłowego programu SQL Server.

2. Jeśli nie zainstalowano zaufanego certyfikatu na serwerze, zaznacz pole wyboru **Certyfikat serwera zaufania**.

    Jeśli zaufany certyfikat nie został zainstalowany, program SQL Server wygeneruje certyfikat z podpisem własnym po uruchomieniu wystąpienia. Ten certyfikat jest używany do szyfrowania poświadczeń połączeń klienta.

    > [!CAUTION]
    > Połączenia SSL, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na połączeniach SSL z użyciem certyfikatów z podpisem własnym w środowisku produkcyjnym ani na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Wybierz pozycję **Zapisz**.

4. Na ekranie **Wybierz źródłowe bazy danych** wybierz bazę danych **Adventureworks2012** na potrzeby migracji.

   ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Jeśli używasz usługi SQL Server Integration Services (SSIS), usługa DMS nie obsługuje obecnie migrowania bazy danych katalogu dla projektów/pakietów usługi SSIS (SSISDB) z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Możesz jednak aprowizować usługę SSIS w usłudze Azure Data Factory (ADF) i ponownie wdrożyć projekty/pakiety usługi SSIS w docelowej bazie danych SSISDB hostowanej przez wystąpienie zarządzane usługi Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Wybierz pozycję **Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły lokalizacji docelowej migracji** należy określić **identyfikator aplikacji** i **klucz**, których może użyć wystąpienie usługi DMS, aby nawiązać połączenie z docelowym wystąpieniem zarządzanym usługi Azure SQL Database i kontem usługi Azure Storage.

    Aby uzyskać więcej informacji, zobacz artykuł [Use portal to create an Azure Active Directory application and service principal that can access resources (Tworzenie aplikacji usługi Azure Active Directory i jednostki usługi, które mogą uzyskać dostęp do zasobów, w portalu)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Wybierz **subskrypcję** zawierającą docelowe wystąpienie zarządzane usługi Azure SQL Database, a następnie wybierz wystąpienie docelowe.

    Jeśli jeszcze nie aprowizowano wystąpienia zarządzanego usługi Azure SQL Database, wybierz ten [link](https://aka.ms/SQLDBMI), aby aprowizować wystąpienie. Gdy wystąpienie zarządzane usługi Azure SQL Database będzie gotowe, możesz wrócić do tego projektu, aby przeprowadzić migrację.

3. Podaj **nazwę użytkownika bazy danych SQL** i **hasło**, aby nawiązać połączenie z wystąpieniem zarządzanym usługi Azure SQL Database.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Wybierz pozycję **Zapisz**.

## <a name="select-source-databases"></a>Wybieranie źródłowych baz danych

1. Na ekranie **Wybierz źródłowe bazy danych** wybierz źródłową bazę danych, którą chcesz zmigrować.

    ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Wybierz pozycję **Zapisz**.

## <a name="configure-migration-settings"></a>Konfigurowanie ustawień migracji

1. Na ekranie **Konfiguruj ustawienia migracji** podaj następujące informacje:

    | | |
    |--------|---------|
    |**Udział lokalizacji sieciowej protokołu SMB** | Lokalny udział sieciowy protokołu SMB zawierający pliki kopii zapasowych całej bazy danych oraz pliki kopii zapasowych dzienników transakcji, których usługa Azure Database Migration Service może użyć podczas migracji. Konto usługi, na którym uruchomiono źródłowe wystąpienie programu SQL Server, musi mieć uprawnienia do odczytu/zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresy IP serwera w udziale sieciowym, na przykład „\\\nazwa_serwera.nazwa_domeny.com\folder_kopii_zapasowych” lub „\\\adres_IP\folder_kopii_zapasowych”.|
    |**Nazwa użytkownika** | Upewnij się, że użytkownik systemu Windows ma uprawnienia pełnej kontroli w udziale sieciowym, który podano powyżej. Usługa Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage na potrzeby operacji przywracania. |
    |**Hasło** | Hasło użytkownika. |
    |**Subskrypcja konta usługi Azure Storage** | Wybierz subskrypcję, która zawiera konto usługi Azure Storage. |
    |**Konto usługi Azure Storage** | Wybierz konto usługi Azure Storage, na które usługa DMS może przekazać pliki kopii zapasowej z udziału sieciowego protokołu SMB używane podczas migracji bazy danych.  Zalecamy wybranie konta usługi Storage w tym samym regionie co usługa DMS w celu uzyskania optymalnej wydajności przekazywania plików. |

    ![Konfigurowanie ustawień migracji](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

2. Wybierz pozycję **Zapisz**.

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

Po przywróceniu pełnej kopii zapasowej bazy danych w docelowym wystąpieniu zarządzanym usługi Azure SQL Database baza danych jest dostępna na potrzeby uruchomienia produkcyjnego migracji.

1. Gdy wszystko będzie gotowe do zakończenia migracji bazy danych online, wybierz pozycję **Rozpocznij migrację jednorazową**.

2. Zatrzymaj cały ruch przychodzący do źródłowych baz danych.

3. Weź kopię zapasową końcowego fragmentu dziennika, udostępnij plik kopii zapasowej w udziale sieciowym protokołu SMB i zaczekaj na przywrócenie ostatecznej wersji kopii zapasowej dziennika transakcji.

    W tym momencie wartość w polu **Oczekujące zmiany** będzie wynosić 0.

4. Wybierz polecenie **Potwierdź**, a następnie wybierz pozycję **Zastosuj**.

    ![Przygotowanie do zakończenia migracji jednorazowej](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Gdy stan migracji bazy danych zmieni się na **Ukończono**, połącz aplikacje z nowym docelowym wystąpieniem zarządzanym usługi Azure SQL Database.

    ![Ukończona migracja jednorazowa](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Kolejne kroki

- Samouczek omawiający migrowanie bazy danych do wystąpienia zarządzanego przy użyciu polecenia RESTORE języka T-SQL, zobacz [Przywracanie kopii zapasowej do wystąpienia zarządzanego przy użyciu polecenia przywracania](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Aby uzyskać informacje na temat wystąpienia zarządzanego, zobacz [Co to jest wystąpienie zarządzane](../sql-database/sql-database-managed-instance.md).
- Aby uzyskać informacje na temat łączenia aplikacji z wystąpieniem zarządzanym, zobacz [Nawiązywanie połączenia z aplikacjami](../sql-database/sql-database-managed-instance-connect-app.md).
