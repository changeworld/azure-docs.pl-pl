---
title: 'Samouczek: Migrowanie wystąpienia zarządzanego programu SQL Server do programu SQL'
titleSuffix: Azure Database Migration Service
description: Dowiedz się, jak przeprowadzać migrację z lokalnego programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297725"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Samouczek: Migrowanie programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database w trybie offline przy użyciu usługi DMS

Usługi migracji bazy danych usługi Azure Database można korzystać z migracji baz danych z lokalnego wystąpienia programu SQL Server do [wystąpienia zarządzanego usługi Azure SQL Database.](../sql-database/sql-database-managed-instance.md) Dodatkowe metody, które mogą wymagać ręcznej pracy, są dostępne w artykule [Migracja wystąpienia programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database](../sql-database/sql-database-managed-instance-migrate.md).

W tym samouczku należy przeprowadzić migrację bazy danych **Adventureworks2012** z lokalnego wystąpienia programu SQL Server do wystąpienia zarządzanego bazy danych SQL przy użyciu usługi migracji bazy danych Azure.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
>
> - Utwórz wystąpienie usługi Azure Database Migration Service.
> - Utwórz projekt migracji przy użyciu usługi migracji bazy danych platformy Azure.
> - Uruchamianie migracji.
> - Monitorowanie migracji.
> - Pobieranie raportu migracji.

> [!IMPORTANT]
> W przypadku migracji w trybie offline z programu SQL Server do wystąpienia zarządzanego bazy danych SQL usługa migracji bazy danych azure może utworzyć pliki kopii zapasowej. Alternatywnie można podać najnowszą pełną kopię zapasową bazy danych w udziale sieciowym SMB, który usługa będzie używać do migracji baz danych. Nie należy dołączać wielu kopii zapasowych do jednego nośnika kopii zapasowej; każdej kopii zapasowej na osobnym pliku kopii zapasowej. Należy zauważyć, że można również użyć skompresowanych kopii zapasowych, aby zmniejszyć prawdopodobieństwo wystąpienia potencjalnych problemów z migracją dużych kopii zapasowych.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

W tym artykule opisano migrację w trybie offline z programu SQL Server do wystąpienia zarządzanego bazy danych SQL. Aby uzyskać migrację online, zobacz [Migrowanie programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database w trybie online przy użyciu usługi DMS](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

- Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Dowiedz się topologii sieci dla migracji wystąpień zarządzanych usługi Azure SQL Database przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi). Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej, zobacz [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/)a zwłaszcza artykuły przewodnika Szybki start ze szczegółami krok po kroku.

    > [!NOTE]
    > Podczas konfigurowania sieci wirtualnej, jeśli używasz usługi ExpressRoute z komunikacją równorzędną sieciową do firmy Microsoft, dodaj następujące [punkty końcowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) usługi do podsieci, w której usługa będzie aprowizowana:
    > - Docelowy punkt końcowy bazy danych (na przykład punkt końcowy SQL, punkt końcowy usługi Cosmos DB itd.)
    > - Punkt końcowy magazynu
    > - Punkt końcowy magistrali usług
    >
    > Ta konfiguracja jest konieczna, ponieważ usługa migracji bazy danych platformy Azure nie ma łączności z Internetem.

- Upewnij się, że reguły sieciowej grupy zabezpieczeń sieciowej sieci nie blokują następujących portów komunikacji przychodzącej do usługi migracji bazy danych Azure: 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu źródłowej bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz Zaporę systemu Windows, aby zezwolić usłudze migracji bazy danych platformy Azure na dostęp do źródłowego programu SQL Server, który domyślnie jest portem TCP 1433.
- Jeśli używasz wielu wystąpień programu SQL Server przy użyciu portów dynamicznych, możesz włączyć usługę przeglądarki SQL i zezwolić na dostęp do portu UDP 1434 za pośrednictwem zapór, aby usługa migracji bazy danych azure mogła łączyć się z nazwanym wystąpieniem w źródle Serwera.
- Jeśli używasz urządzenia zapory przed źródłowymi bazami danych, może być konieczne dodanie reguł zapory, aby umożliwić usłudze migracji bazy danych platformy Azure dostęp do źródłowych baz danych w celu migracji, a także pliki za pośrednictwem portu SMB 445.
- Utworzenie wystąpienia zarządzanego usługi Azure SQL Database — szczegółowe instrukcje znajdują się w artykule [Tworzenie wystąpienia zarządzanego usługi Azure SQL Database w witrynie Azure Portal](https://aka.ms/sqldbmi).
- Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.

    >[!NOTE]
    >Domyślnie usługa migracji bazy danych platformy Azure obsługuje tylko migrację logowania SQL. Można jednak włączyć możliwość migracji logowania systemu Windows przez:
    >
    >- Zapewnienie, że docelowe wystąpienie zarządzanej bazy danych SQL ma dostęp do odczytu usługi AAD, który może być skonfigurowany za pośrednictwem portalu Azure przez użytkownika z **administratorem firmy**lub administratorem **globalnym**" roli.
    >- Konfigurowanie wystąpienia usługi migracji bazy danych Azure, aby włączyć migracje logowania użytkownika/grupy systemu Windows, który jest skonfigurowany za pośrednictwem portalu Azure, na stronie Konfiguracja. Po włączeniu tego ustawienia uruchom ponownie usługę, aby zmiany zostały wprowadzone.
    >
    > Po ponownym uruchomieniu usługi logowania użytkowników/grup systemu Windows są wyświetlane na liście loginów dostępnych do migracji. W przypadku wszystkich migrowanych loginów użytkowników/grup systemu Windows zostanie wyświetlony monit o podanie skojarzonej nazwy domeny. Konta użytkowników usługi (konto o nazwie domeny NT AUTHORITY) i konta użytkowników wirtualnych (nazwa konta o nazwie domeny NT SERVICE) nie są obsługiwane.

- Utwórz udział sieciowy, którego usługa migracji bazy danych platformy Azure może używać do tworzenia kopii zapasowej źródłowej bazy danych.
- Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
- Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Usługa migracji bazy danych Azure personifikuje poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera usługi Azure Storage w celu przywrócenia operacji.
- Utworzenie kontenera obiektów blob i pobranie jego identyfikatora URI sygnatury dostępu współdzielonego przez wykonanie czynności opisanych w artykule [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Musisz wybrać wszystkie uprawnienia (odczyt, zapis, usuwanie, wyświetlanie listy) w oknie zasad podczas tworzenia identyfikatora URI sygnatury dostępu współdzielonego. Ten szczegół zapewnia usługę migracji bazy danych Azure z dostępem do kontenera konta magazynu do przekazywania plików kopii zapasowej używanych do migracji baz danych do wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1. Zaloguj się w witrynie Azure Portal, wybierz pozycję **Wszystkie usługi**, a następnie wybierz pozycję **Subskrypcje**.

    ![Wyświetlanie subskrypcji w portalu](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi migracji bazy danych platformy Azure, a następnie wybierz pozycję **Dostawcy zasobów**.

    ![Wyświetlanie dostawców zasobów](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Wyszukaj migrację, a następnie po prawej stronie **microsoft.DataMigration**, wybierz pozycję **Zarejestruj**.

    ![Rejestrowanie dostawcy zasobów](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Tworzenie wystąpienia usługi Azure Database Migration Service

1. W portalu Azure wybierz + **Utwórz zasób**, wyszukaj **usługę migracji usługi Azure Database**, a następnie wybierz pozycję Usługa migracji bazy danych Platformy **Azure** z listy rozwijanej.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Na ekranie **Azure Database Migration Service** wybierz polecenie **Utwórz**.

    ![Tworzenie wystąpienia usługi Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Na ekranie **Tworzenie usługi migracji** określ nazwę usługi, subskrypcję oraz nową lub istniejącą grupę zasobów.

4. Wybierz lokalizację, w której chcesz utworzyć wystąpienie usługi DMS.

5. Wybierz istniejącą sieć wirtualną lub utwórz jedną.

    Sieć wirtualna zapewnia usługę migracji bazy danych platformy Azure z dostępem do źródłowego programu SQL Server i docelowego wystąpienia zarządzanego usługi Azure SQL Database.

    Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnej w witrynie Azure portal, zobacz artykuł [Tworzenie sieci wirtualnej przy użyciu portalu Azure](https://aka.ms/DMSVnet).

    Aby uzyskać więcej informacji, zobacz artykuł [Topologie sieci dla migracji wystąpień zarządzanych usługi Azure SQL DB przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi).

6. Wybierz warstwę cenową.

    Aby uzyskać więcej informacji na temat kosztów i warstw cenowych, zobacz [stronę z cennikiem](https://aka.ms/dms-pricing).

    ![Tworzenie usługi DMS](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Wybierz pozycję **Utwórz**, aby utworzyć usługę.

## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu wystąpienia usługi znajdź je w witrynie Azure Portal, otwórz je, a następnie utwórz nowy projekt migracji.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**, wyszukaj usługę Azure Database Migration Service, a następnie wybierz pozycję **Azure Database Migration Services**.

    ![Znajdź wszystkie wystąpienia usługi migracji bazy danych platformy Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

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
    > Połączenia TLS, które są szyfrowane przy użyciu certyfikatu z podpisem własnym, nie zapewniają silnego zabezpieczenia. Są one podatne na ataki typu „man-in-the-middle”. Nie należy polegać na TLS przy użyciu certyfikatów z podpisem własnym w środowisku produkcyjnym lub na serwerach, które są połączone z Internetem.

   ![Szczegóły źródła](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Wybierz **pozycję Zapisz**.

4. Na ekranie **Wybierz źródłowe bazy danych** wybierz bazę danych **Adventureworks2012** na potrzeby migracji.

   ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Jeśli używasz usługi SQL Server Integration Services (SSIS), usługa DMS nie obsługuje obecnie migrowania bazy danych katalogu dla projektów/pakietów usługi SSIS (SSISDB) z programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database. Możesz jednak aprowizować usługę SSIS w usłudze Azure Data Factory (ADF) i ponownie wdrożyć projekty/pakiety usługi SSIS w docelowej bazie danych SSISDB hostowanej przez wystąpienie zarządzane usługi Azure SQL Database. Aby uzyskać więcej informacji na temat migracji pakietów SSIS, zobacz artykuł [Migrowanie pakietów usług SQL Server Integration Services na platformę Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Wybierz **pozycję Zapisz**.

## <a name="specify-target-details"></a>Określanie szczegółów elementu docelowego

1. Na ekranie **Szczegóły lokalizacji docelowej migracji** określ szczegóły połączenia dla lokalizacji docelowej, czyli dla aprowizowanego wcześniej wystąpienia zarządzanego usługi Azure SQL Database, do którego jest migrowana baza danych **AdventureWorks2012**.

    Jeśli nie zostały jeszcze zainicjowane inicjowane wystąpienie zarządzane bazy danych SQL, wybierz [łącze,](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) aby ułatwić aprowizjanie wystąpienia. Nadal można kontynuować tworzenie projektu, a następnie, gdy wystąpienie zarządzanej usługi Azure SQL Database jest gotowe, wróć do tego konkretnego projektu, aby wykonać migrację.

    ![Wybieranie obiektu docelowego](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Wybierz **pozycję Zapisz**.

## <a name="select-source-databases"></a>Wybieranie źródłowych baz danych

1. Na ekranie **Wybierz źródłowe bazy danych** wybierz źródłową bazę danych, którą chcesz zmigrować.

    ![Wybieranie źródłowych baz danych](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Wybierz **pozycję Zapisz**.

## <a name="select-logins"></a>Wybieranie identyfikatorów logowania

1. Na ekranie **Wybierz identyfikatory logowania** wybierz identyfikatory logowania, które chcesz zmigrować.

    >[!NOTE]
    >Domyślnie usługa migracji bazy danych platformy Azure obsługuje tylko migrację logowania SQL. Aby włączyć obsługę migracji logowania systemu Windows, zobacz sekcję **Wymagania wstępne** tego samouczka.

    ![Wybieranie identyfikatorów logowania](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Wybierz **pozycję Zapisz**.

## <a name="configure-migration-settings"></a>Konfigurowanie ustawień migracji

1. Na ekranie **Konfiguruj ustawienia migracji** podaj następujące informacje:

    | | |
    |--------|---------|
    |**Wybierz opcję tworzenia kopii zapasowej źródła** | Wybierz opcję **Dostarczę najnowsze pliki kopii zapasowej**, jeśli pełne pliki kopii zapasowej są już dostępne do użycia przez usługę DMS na potrzeby migracji bazy danych. Wybierz opcję **Umożliwię usłudze Azure Database Migration Service utworzenie plików kopii zapasowej**, jeśli usługa DMS ma pobrać pełną kopię zapasową źródłowej bazy danych i użyć jej na potrzeby migracji. |
    |**Udział lokalizacji sieciowej** | Lokalny udział sieciowy SMB, do który usługa migracji bazy danych platformy Azure może wykonać źródłowe kopie zapasowe bazy danych. Konto usługi uruchamiające źródłowe wystąpienie programu SQL Server musi mieć uprawnienia do zapisu w tym udziale sieciowym. Podaj nazwę FQDN lub adresy IP serwera w udziale sieciowym, na przykład „\\\nazwa_serwera.nazwa_domeny.com\folder_kopii_zapasowych” lub „\\\adres_IP\folder_kopii_zapasowych”.|
    |**Nazwa użytkownika** | Upewnij się, że użytkownik systemu Windows ma uprawnienia pełnej kontroli w udziale sieciowym, który podano powyżej. Usługa migracji bazy danych Azure będzie personifikować poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera usługi Azure Storage w celu przywrócenia operacji. W przypadku wybrania baz danych obsługujących szyfrowanie TDE użytkownik systemu Windows musi korzystać z wbudowanego konta administratora, a [kontrola konta użytkownika](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) musi być wyłączona, aby usługa Azure Database Migration Service mogła przekazywać i usuwać pliki certyfikatów. |
    |**Hasło** | Hasło użytkownika. |
    |**Ustawienia konta magazynu** | Identyfikator URI sygnatury dostępu współdzielonego, który zapewnia usłudze migracji bazy danych Azure dostęp do kontenera konta magazynu, do którego usługa przekazuje pliki kopii zapasowej i który jest używany do migracji baz danych do wystąpienia zarządzanego usługi Azure SQL Database. [Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu współdzielonego dla kontenera obiektów blob](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Ustawienia szyfrowania TDE** | Jeśli przeprowadzasz migrację źródłowych baz danych z włączoną funkcją przezroczystego szyfrowania danych (TDE), musisz mieć uprawnienia do zapisu w docelowym wystąpieniu zarządzanym usługi Azure SQL Database.  Wybierz z menu rozwijanego subskrypcję, w której aprowizowano wystąpienie zarządzane usługi Azure SQL Database.  Wybierz docelowe **wystąpienie zarządzane usługi Azure SQL Database** z menu rozwijanego. |

    ![Konfigurowanie ustawień migracji](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Wybierz **pozycję Zapisz**.

## <a name="review-the-migration-summary"></a>Przeglądanie podsumowania migracji

1. Na ekranie **Podsumowanie migracji** w polu tekstowym **Nazwa działania** określ nazwę działania migracji.

2. Rozwiń sekcję **Opcja weryfikacji**, aby wyświetlić ekran **Wybierz opcję weryfikacji**, określ, czy przeprowadzić weryfikację bazy danych po migracji pod kątem poprawności zapytań, a następnie wybierz przycisk **Zapisz**.

3. Przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

    ![Podsumowanie projektu migracji](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Wybierz **pozycję Zapisz**.

## <a name="run-the-migration"></a>Uruchamianie migracji

- Wybierz polecenie **Uruchom migrację**.

  Zostanie wyświetle okno aktywności migracji, a stan działania to **Oczekujące**.

## <a name="monitor-the-migration"></a>Monitorowanie migracji

1. Na ekranie działania migracji wybierz pozycję **Odśwież**, aby zaktualizować wyświetlane dane.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    Możesz rozwinąć kategorie baz danych i danych logowania, aby monitorować stan migracji odpowiednich obiektów serwera.

   ![Działanie migracji w toku](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Po zakończeniu migracji wybierz polecenie **Pobierz raport**, aby pobrać raport zawierający szczegółowe informacje o procesie migracji.

3. Upewnij się, że docelowa baza danych znajduje się w docelowym środowisku wystąpienia zarządzanego usługi Azure SQL Database.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z samouczkiem przedstawiającym sposób migracji bazy danych do wystąpienia zarządzanego za pomocą polecenia T-SQL RESTORE, zobacz [Przywracanie kopii zapasowej do wystąpienia zarządzanego za pomocą polecenia restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Aby uzyskać informacje o [wystąpieniu zarządzanym,](../sql-database/sql-database-managed-instance.md)zobacz Co to jest wystąpienie zarządzane .
- Aby uzyskać informacje dotyczące łączenia aplikacji z wystąpieniem zarządzanym, zobacz [Łączenie aplikacji](../sql-database/sql-database-managed-instance-connect-app.md).
