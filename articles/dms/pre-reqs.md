---
title: Przegląd wymagań wstępnych dotyczących używania usługi Azure Database Migration Service | Dokumentacja firmy Microsoft
description: Zapoznaj się z omówieniem wymagania wstępne dotyczące korzystania z usługi Azure Database Migration Service do przeprowadzania migracji bazy danych.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 05/29/2019
ms.openlocfilehash: 4e21014f7b4ed86846a100ed9a2b1cd4b0400974
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66304254"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Przegląd wymagań wstępnych dotyczących używania usługi Azure Database Migration Service

Istnieje kilka wymagań wstępnych dotyczących upewnij się, że usługa Azure Database Migration Service działa sprawnie podczas przeprowadzania migracji bazy danych. Pewne warunki wstępne mają zastosowanie we wszystkich scenariuszach (par źródło cel) obsługiwane przez usługę, podczas gdy inne wymagania wstępne są unikatowe dla konkretnego scenariusza.

W poniższych sekcjach wymieniono wymagań wstępnych związanych z użyciem usługi Azure Database Migration Service.

## <a name="prerequisites-common-across-migration-scenarios"></a>Wymagania wstępne dotyczące typowych scenariuszy migracji

Azure Database Migration Service wstępnie wymagane składniki, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji obejmują konieczność:

* Tworzenie usługi Azure Virtual Network (VNet) dla usługi Azure Database Migration Service przy użyciu modelu wdrażania usługi Azure Resource Manager, co zapewnia łączność lokacja lokacja z serwerów źródłowych w środowisku lokalnym przy użyciu [usługiExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Upewnij się, że reguły sieciowej grupy zabezpieczeń (NSG) sieci wirtualnej nie blokują następujących komunikacji porty 443, 53, 9354, 445, 12000. Aby uzyskać więcej szczegółowych informacji dotyczących filtrowania ruchu sieciowej grupy zabezpieczeń w sieci wirtualnej platformy Azure, zobacz artykuł [filtrowanie ruchu sieciowego przy użyciu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Podczas korzystania z urządzenia zapory przed źródłowych baz danych, użytkownik może być konieczne dodanie reguły zapory zezwalające usługi Azure Database Migration Service dostęp źródłowych baz danych do migracji do.
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Tworzenie wystąpienia usługi Azure Database Migration Service wymaga dostępu do ustawień sieci wirtualnej, które zwykle nie znajdują się w tej samej grupie zasobów. W rezultacie użytkownik tworzący wystąpienie usługi DMS wymaga uprawnień na poziomie subskrypcji. Aby utworzyć wymagane role, które można przypisać zgodnie z potrzebami, uruchom następujący skrypt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.DataMigration/services/*/read", `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action"
    >
    > $writerActions += $readerActions
    >
    > # TODO: replace with actual subscription IDs
    > $subScopes = ,"/subscriptions/00000000-0000-0000-0000-000000000000/","/subscriptions/11111111-1111-1111-1111-111111111111/"
    >
    > function New-DmsReaderRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Reader"
    > $aRole.Description = "Lets you perform read only actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    >
    > $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    >
    > function New-DmsContributorRole() {
    > $aRole = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
    > $aRole.Name = "Azure Database Migration Contributor"
    > $aRole.Description = "Lets you perform CRUD actions on DMS service/project/tasks."
    > $aRole.IsCustom = $true
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    >
    >   $aRole.AssignableScopes = $subScopes
    > #Create the role
    > New-AzRoleDefinition -Role $aRole
    > }
    > 
    > function Update-DmsReaderRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Reader"
    > $aRole.Actions = $readerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > function Update-DmsConributorRole() {
    > $aRole = Get-AzRoleDefinition "Azure Database Migration Contributor"
    > $aRole.Actions = $writerActions
    > $aRole.NotActions = @()
    > Set-AzRoleDefinition -Role $aRole
    > }
    >
    > # Invoke above functions
    > New-DmsReaderRole
    > New-DmsContributorRole
    > Update-DmsReaderRole
    > Update-DmsConributorRole
    > ```

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Wymagania wstępne dotyczące migracji programu SQL Server do usługi Azure SQL Database

Oprócz wymagań wstępnych usługi Azure Database Migration Service, które są wspólne dla wszystkich scenariuszy migracji są również wymagania wstępne, które mają zastosowanie szczególnie w jeden scenariusz lub innej.

Korzystając z usługi Azure Database Migration Service do wykonania programu SQL Server do migracji usługi Azure SQL Database, oprócz wymagań wstępnych, które są wspólne dla wszystkich scenariuszy migracji, należy rozwiązać następujące dodatkowe wymagania wstępne:

* Utwórz wystąpienie obiektu wystąpienia usługi Azure SQL Database, co możesz zrobić, klikając następujące szczegółowo w artykule C[Utwórz bazę danych Azure SQL database w witrynie Azure portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla serwera usługi Azure SQL Database, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi Azure Database Migration Service.
* Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Sprawdź, czy poświadczenia użyte do nawiązania połączenia z docelowym wystąpieniem usługi Azure SQL Database mają uprawnienie CONTROL DATABASE do docelowych baz danych Azure SQL Database.

   > [!NOTE]
   > Aby uzyskać pełną listę wymagań wstępnych dotyczących wykonania migracji z programu SQL Server do usługi Azure SQL Database przy użyciu usługi Azure Database Migration Service, zapoznaj się z samouczkiem [migracji programu SQL Server do usługi Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Wymagania wstępne dotyczące migracji programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database

* Tworzenie wystąpienia zarządzanego usługi Azure SQL Database, postępując zgodnie z szczegółowo w artykule [Tworzenie wystąpienia usługi Azure SQL Database zarządzane w witrynie Azure portal](https://aka.ms/sqldbmi).
* Otwarcie zapory zezwalającą na ruch protokołu SMB na porcie 445 dla zakresu adresów lub podsieci IP usługi migracji bazy danych Azure.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.
* Utworzenie udziału sieciowego umożliwiającego wykonanie kopii zapasowej źródłowej bazy danych za pomocą usługi Azure Database Migration Service.
* Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
* Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Usługa Azure Database Migration Service personifikuje poświadczenia użytkownika w celu przekazania plików kopii zapasowej do kontenera usługi Azure Storage na potrzeby operacji przywracania.
* Utwórz kontener obiektów blob i pobrać jego identyfikatora URI sygnatury dostępu Współdzielonego wykonując kroki opisane w artykule [zasoby zarządzania usługi Azure Blob Storage za pomocą Eksploratora usługi Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Pamiętaj zaznaczyć wszystkie uprawnienia (Odczyt, zapis, usuwanie, lista) w oknie zasad podczas tworzenia identyfikatora URI sygnatury dostępu Współdzielonego.

   > [!NOTE]
   > Aby uzyskać pełną listę wymagań wstępnych dotyczących wykonania migracji z programu SQL Server do wystąpienia zarządzanego Azure SQL Database przy użyciu usługi Azure Database Migration Service, zapoznaj się z samouczkiem [migracji programu SQL Server do wystąpienia zarządzanego Azure SQL Database ](https://aka.ms/migratetomiusingdms).

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać omówienie usługi Azure Database Migration Service i dostępności regionalnej, zobacz artykuł [co to jest Azure Database Migration Service](dms-overview.md).
