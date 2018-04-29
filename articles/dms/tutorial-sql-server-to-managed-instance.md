---
title: Usługa DMS umożliwia migrację do wystąpienia zarządzane bazy danych SQL Azure | Dokumentacja firmy Microsoft
description: Dowiedz się przeprowadzić migrację z lokalnej instalacji programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure za pomocą usługi Azure migracji bazy danych.
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/10/2018
ms.openlocfilehash: 6628ea218c4c7a9aacc0c2899c1ea4e5b6169b51
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>Migrowanie serwera SQL do Azure wystąpienia bazy danych SQL zarządzane przy użyciu DMS
Usługa Azure bazy danych migracji umożliwia migrację bazy danych z lokalnego wystąpienia programu SQL Server do [wystąpienia zarządzane bazy danych SQL Azure](../sql-database/sql-database-managed-instance.md) przestojów bliskie zeru. Aby uzyskać dodatkowe metody, które wymagają pewien Przestój, zobacz [migracji wystąpienie programu SQL Server do wystąpienia zarządzane bazy danych SQL Azure](../sql-database/sql-database-managed-instance-migrate.md).

W tym samouczku, wykonywana jest migracja **Adventureworks2012** bazy danych z lokalnego wystąpienia programu SQL Server do bazy danych SQL Azure za pomocą usługi Azure migracji bazy danych.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Utwórz wystąpienie usługi migracji bazy danych Azure.
> * Utwórz projekt migracji za pomocą usługi Azure migracji bazy danych.
> * Uruchom migrację.
> * Monitoruj migrację.

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka, musisz:

- Tworzenie sieci Wirtualnej dla usługi Azure migracji bazy danych przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia połączenie lokacja lokacja z serwerami lokalnymi źródła przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Dowiedz się topologii sieci migracji wystąpienia zarządzane bazy danych SQL Azure przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi).
- Upewnij się, że Twoje Azure (VNET) sieciowej grupy zabezpieczeń sieci wirtualnej reguły blok następujący komunikat porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółów na filtrowanie ruchu NSG sieci Wirtualnej Azure, zobacz artykuł [filtrowania ruchu sieciowego z grup zabezpieczeń sieci](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Konfigurowanie sieci [zapory systemu Windows dla dostępu aparatu bazy danych źródła](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Otwórz Zaporę systemu Windows, aby umożliwić usłudze migracji bazy danych Azure dostępu do źródła SQL Server, która domyślnie jest TCP port 1433.
- Jeśli używasz wielu wystąpień programu SQL Server o nazwie przy użyciu portów dynamicznych, możesz włączyć usługę Przeglądarka SQL i umożliwiają dostęp do portu UDP 1434 na zaporach, dzięki czemu usługa migracji bazy danych Azure mogą łączyć się nazwanego wystąpienia w źródle serwer.
- Jeśli korzystasz z urządzenia zapory przed źródła baz danych, może być konieczne dodanie reguły zapory, aby umożliwić dostęp do baz danych źródła dla plików za pośrednictwem portu SMB 445, a także migracji z usługi migracji bazy danych Azure.
- Utwórz wystąpienie zarządzane bazy danych SQL Azure, wykonując szczegółowo w artykule [utworzyć wystąpienia zarządzanego bazy danych SQL Azure w portalu Azure](https://aka.ms/sqldbmi).
- Sprawdź, czy dane logowania umożliwiający połączenie źródła programu SQL Server i zarządzane wystąpienia docelowego są członkowie roli serwera sysadmin.
- Utwórz udział sieciowy, który usługa migracji bazy danych Azure umożliwia tworzenie kopii zapasowej źródłowej bazy danych.
- Upewnij się, że konto usługi uruchomione wystąpienie programu SQL Server źródła ma uprawnienia zapisu w udziale sieciowym, który został utworzony.
- Zwróć uwagę użytkownika systemu Windows (i hasło), które ma uprawnienia pełnej kontroli w udziale sieciowym, utworzoną wcześniej. Usługa Azure bazy danych migracji personifikuje poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure dla operacji przywracania.
- Tworzenie kontenera obiektu blob i pobrać jego identyfikatora URI, wykonując kroki opisane w artykule [zasobów zarządzania magazynu obiektów Blob Azure Eksplorator magazynu](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container), pamiętaj zaznaczyć wszystkie uprawnienia (Odczyt, zapis, usuwanie, listy) w oknie zasad podczas Tworzenie Identyfikatora URI. Zapewnia to usługa migracji bazy danych Azure dostęp do Twojego kontenera konta magazynu o przekazywaniu używany do migracji plików kopii zapasowej baz danych do wystąpienia zarządzane bazy danych SQL Azure

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Rejestrowanie dostawcy zasobów Microsoft.DataMigration

1.  Zaloguj się do portalu Azure, wybierz opcję **wszystkie usługi**, a następnie wybierz **subskrypcje**.
![Pokaż portalu subskrypcji](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Wybierz subskrypcję, w której chcesz utworzyć wystąpienie usługi Azure Database Migration Service, a następnie wybierz pozycję **Dostawcy zasobów**.
![Pokaż dostawców zasobów](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  Wyszukiwanie do migracji, a następnie po prawej stronie **Microsoft.DataMigration**, wybierz pozycję **zarejestrować**.
![Rejestrowanie dostawcy zasobów](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-azure-database-migration-service-instance"></a>Utwórz wystąpienie usługi migracji bazy danych Azure

1.  W portalu Azure wybierz **+ Utwórz zasób**, wyszukaj **usługi migracji bazy danych Azure**, a następnie wybierz **usługi migracji bazy danych Azure** z listy rozwijanej Lista.

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  Na **usługi migracji bazy danych Azure (wersja zapoznawcza)** ekranu wybierz **Utwórz**.

    ![Utwórz wystąpienie usługi migracji bazy danych Azure](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  Na **bazy danych migracji usługi** ekranu, określ nazwę usługi, subskrypcji, grupy zasobów, sieć wirtualną i warstwę cenową.

    Aby uzyskać więcej informacji na kosztów i warstwy cenowej, zobacz [cennikiem](https://aka.ms/dms-pricing). *Usługa Azure migracji bazy danych jest obecnie w wersji zapoznawczej i nie zostanie naliczona.*

    **Sieć:** wybierz istniejącą lub Utwórz nową sieć Wirtualną, która zapewnia usługi migracji bazy danych Azure dostęp do źródła programu SQL Server i docelowego wystąpienia zarządzane bazy danych SQL Azure. [Dowiedz się topologii sieci migracji wystąpienia zarządzane bazy danych SQL Azure przy użyciu usługi migracji bazy danych Azure](https://aka.ms/dmsnetworkformi).

    Aby uzyskać więcej informacji dotyczących sposobu tworzenia sieci Wirtualnej w portalu Azure, zobacz [tworzenie sieci wirtualnej z wieloma podsieciami przy użyciu portalu Azure](https://aka.ms/DMSVnet).

    ![Tworzenie usługi DMS](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  Wybierz **Utwórz** można utworzyć usługi.


## <a name="create-a-migration-project"></a>Tworzenie projektu migracji

Po utworzeniu usługi znalezienie go w portalu Azure, a następnie otwórz go.

1.  Wybierz **+ nowy projekt migracji**.

1.  Na **nowy projekt migracji** ekranu, należy określić nazwę projektu, w **typ serwera źródła** pole tekstowe, wybierz opcję **programu SQL Server**, a następnie w **docelowego Typ serwera** pole tekstowe, wybierz opcję **wystąpienia zarządzane bazy danych SQL Azure**.

    ![Utwórz projekt DMS](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  Wybierz **Utwórz** Aby utworzyć projekt.

## <a name="specify-source-details"></a>Określ szczegóły źródła

1.  Na **źródła szczegóły** ekranu, określ szczegóły połączenia dla źródła SQL Server.

    ![Szczegóły źródła](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  Wybierz **zapisać**, a następnie wybierz **Adventureworks2012** bazy danych do migracji.

    ![Wybierz źródło baz danych](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>Określ szczegóły docelowego

1.  Wybierz **zapisać**, a następnie na **docelowe szczegóły** ekranu, określ szczegóły połączenia dla obiektu docelowego, który jest wstępnie przygotowany Azure wystąpienia bazy danych SQL zarządzanych do której  **AdventureWorks2012** bazy danych zostaną poddane migracji.

    ![Wybierz element docelowy](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  Wybierz pozycję **Zapisz**.

1.  Na **podsumowanie projektu** ekranu, przejrzyj i sprawdź szczegóły skojarzone z projektem migracji.

## <a name="run-the-migration"></a>Uruchom migrację

1.  Wybierz ostatnio zapisany projekt, wybierz pozycję **+ nowe działanie**, a następnie wybierz **uruchamiania migracji**.

    ![Utwórz nowe działanie](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  Po wyświetleniu monitu wprowadź poświadczenia z serwerem źródłowym a docelowym, a następnie wybierz **zapisać**.

1.  Na **mapy w celu docelowymi bazami danych** wybierz źródło baz danych, które chcesz migrować.

    ![Wybierz źródło baz danych](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  Wybierz **zapisać**na **skonfigurować ustawień migracji** ekranu, podaj następujące szczegóły:

    | | |
    |--------|---------|
    |**Lokalizacja kopii zapasowej serwera** | Sieci lokalnej udostępnić, czy usługa migracji bazy danych Azure może potrwać źródło kopii zapasowych bazy danych. Konto usługi uruchomione wystąpienie programu SQL Server źródło musi mieć uprawnienia do zapisu w tym udziale sieciowym. |
    |**Nazwa użytkownika** | Nazwa użytkownika systemu windows Azure usługa migracji bazy danych może spersonifikować i przekazać pliki kopii zapasowej do kontenera magazynu systemu Azure dla operacji przywracania. |
    |**Hasło** | Hasło dla użytkownika powyżej. |
    |**Identyfikator URI SAS magazynu** | Identyfikator URI sygnatury dostępu Współdzielonego, który udostępnia usługi migracji bazy danych Azure dostęp do Twojego kontenera konta magazynu, do której usługa przekazywania plików kopii zapasowych i które jest używane w przypadku migracji baz danych do wystąpienia zarządzane bazy danych SQL Azure. [Dowiedz się, jak uzyskać identyfikator URI sygnatury dostępu Współdzielonego dla kontenera obiektów blob](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    
    ![Skonfiguruj ustawienia migracji](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  Wybierz **zapisać**na ekranie migracji podsumowania, **Nazwa działania** tekst Określ nazwę działania migracji.

    ![Podsumowanie migracji](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>Monitoruj migrację

1.  Wybierz działanie migracji, aby sprawdzić stan działania.

1.  Po zakończeniu migracji Sprawdź z docelowymi bazami danych w celu wystąpienia zarządzane bazy danych SQL Azure.

    ![Monitoruj migrację](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

## <a name="next-steps"></a>Kolejne kroki

- Samouczek pokazuje, jak przeprowadzić migrację bazy danych do wystąpienia zarządzane przy użyciu polecenia Przywróć T-SQL, zobacz [przywrócenia kopii zapasowej do wystąpienia zarządzane za pomocą polecenia restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Uzyskać informacji dotyczących importowania z pliku pliku BACPAC bazy danych, zobacz [Importowanie pliku pliku BACPAC do nowej bazy danych SQL Azure](../sql-database/sql-database-import.md).
- Informacji o zarządzanych wystąpienie znajduje się w temacie [co to jest wystąpieniem zarządzane](../sql-database/sql-database-managed-instance.md).
- Informacji o podłączaniu aplikacje do wystąpienia zarządzane, zobacz [łączyć aplikacje](../sql-database/sql-database-managed-instance-connect-app.md).
