---
title: Wymagania wstępne dla usługi migracji bazy danych platformy Azure
description: Dowiedz się więcej o omówie wymagań wstępnych dotyczących korzystania z usługi migracji bazy danych Azure do przeprowadzania migracji bazy danych.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 89cb63630e3dbe953ed3f4fd8796d01ba0d36067
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651495"
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Omówienie wymagań wstępnych dotyczących używania usługi Azure Database Migration Service

Istnieje kilka wymagań wstępnych wymaganych, aby upewnić się, że usługa migracji bazy danych Platformy Azure działa płynnie podczas przeprowadzania migracji bazy danych. Niektóre wymagania wstępne mają zastosowanie we wszystkich scenariuszach (pary źródłowe docelowe) obsługiwane przez usługę, podczas gdy inne wymagania wstępne są unikatowe dla określonego scenariusza.

Wymagania wstępne skojarzone z korzystaniem z usługi migracji bazy danych Azure są wymienione w poniższych sekcjach.

## <a name="prerequisites-common-across-migration-scenarios"></a>Wymagania wstępne typowe dla scenariuszy migracji

Wymagania wstępne usługi migracji bazy danych platformy Azure, które są wspólne dla wszystkich obsługiwanych scenariuszy migracji obejmują potrzebę:

* Utwórz usługę migracji bazy danych usługi Azure Database dla platformy Azure przy użyciu modelu wdrażania usługi Azure Resource Manager, który zapewnia łączność między lokacjami lokalnymi serwerami źródłowymi przy użyciu usługi [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) lub [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* Upewnij się, że reguły sieciowej sieciowej sieci zabezpieczeń sieci nie blokują następujących portów komunikacyjnych 443, 53, 9354, 445, 12000. Aby uzyskać więcej informacji na temat filtrowania ruchu sieciowej sieciowej sieciowej w sieci wirtualnej, zobacz artykuł [Filtrowanie ruchu sieciowego z sieciowymi grupami zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Podczas korzystania z urządzenia zapory przed źródłową bazą danych może być konieczne dodanie reguł zapory, aby umożliwić usłudze migracji usługi Azure Database dostęp do źródłowych baz danych w celu migracji.
* [Zapora sytemu Windows skonfigurowana pod kątem dostępu do aparatu bazy danych](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Włączony protokół TCP/I (domyślnie wyłączony podczas instalacji programu SQL Server Express). Aby go włączyć, wykonaj czynności opisane w artykule [Enable or Disable a Server Network Protocol (Włączanie lub wyłączanie protokołu sieciowego serwera)](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).

    > [!IMPORTANT]
    > Tworzenie wystąpienia usługi migracji bazy danych platformy Azure wymaga dostępu do ustawień sieci wirtualnej, które zwykle nie należą do tej samej grupy zasobów. W rezultacie użytkownik tworzący wystąpienie DMS wymaga uprawnień na poziomie subskrypcji. Aby utworzyć wymagane role, które można przypisać w razie potrzeby, uruchom następujący skrypt:
    >
    > ```
    >
    > $readerActions = `
    > "Microsoft.Network/networkInterfaces/ipConfigurations/read", `
    > "Microsoft.DataMigration/*/read", `
    > "Microsoft.Resources/subscriptions/resourceGroups/read"
    >
    > $writerActions = `
    > "Microsoft.DataMigration/services/*/write", `
    > "Microsoft.DataMigration/services/*/delete", `
    > "Microsoft.DataMigration/services/*/action", `
    > "Microsoft.Network/virtualNetworks/subnets/join/action", `
    > "Microsoft.Network/virtualNetworks/write", `
    > "Microsoft.Network/virtualNetworks/read", `
    > "Microsoft.Resources/deployments/validate/action", `
    > "Microsoft.Resources/deployments/*/read", `
    > "Microsoft.Resources/deployments/*/write"
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

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Wymagania wstępne dotyczące migracji programu SQL Server do bazy danych SQL Azure

Oprócz wymagań wstępnych usługi migracji bazy danych platformy Azure, które są wspólne dla wszystkich scenariuszy migracji, istnieją również wymagania wstępne, które mają zastosowanie w szczególności do jednego scenariusza lub innego.

Korzystając z usługi migracji bazy danych Azure do wykonywania migracji programu SQL Server do bazy danych SQL Azure, oprócz wymagań wstępnych, które są wspólne dla wszystkich scenariuszy migracji, należy rozwiązać następujące dodatkowe wymagania wstępne:

* Utworzone wystąpienie usługi Azure SQL Database — szczegółowe instrukcje znajdują w artykule [Tworzenie bazy danych usługi Azure SQL Database w witrynie Azure Portal](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
* Pobrany i zainstalowany program [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) w wersji 3.3 lub nowszej.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Utwórz [regułę zapory](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) na poziomie serwera dla serwera usługi Azure SQL Database, aby umożliwić usłudze Azure Database Migration Service dostęp do docelowych baz danych. Podaj zakres podsieci sieci wirtualnej używanej dla usługi migracji bazy danych Azure.
* Sprawdź, czy poświadczenia użyte do nawiązania połączenia ze źródłowym wystąpieniem programu SQL Server mają uprawnienia [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
* Sprawdź, czy poświadczenia użyte do nawiązania połączenia z docelowym wystąpieniem usługi Azure SQL Database mają uprawnienie CONTROL DATABASE do docelowych baz danych Azure SQL Database.

   > [!NOTE]
   > Aby uzyskać pełną listę wymagań wstępnych wymaganych do korzystania z usługi migracji bazy danych Azure Database do przeprowadzania migracji z programu SQL Server do bazy danych SQL SQL, zobacz samouczek [Migrowanie programu SQL Server do bazy danych SQL Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql).
   >

## <a name="prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance"></a>Wymagania wstępne dotyczące migracji programu SQL Server do wystąpienia zarządzanego usługi Azure SQL Database

* Utwórz wystąpienie zarządzanego usługi Azure SQL Database, postępając zgodnie ze szczegółami w artykule [Tworzenie wystąpienia zarządzanego bazy danych SQL platformy Azure w portalu Azure.](https://aka.ms/sqldbmi)
* Otwórz zapory, aby zezwolić na ruch SMB na porcie 445 dla adresu IP usługi migracji bazy danych azure lub zakresu podsieci.
* Otwarcie zapory systemu Windows w celu zezwolenia usłudze Azure Database Migration Service na dostęp do źródłowego wystąpienia programu SQL Server, czyli domyślnie portu TCP 1433.
* Jeśli uruchomiono wiele nazwanych wystąpień programu SQL Server przy użyciu portów dynamicznych, konieczne może być włączenie usługi SQL Browser Service i zezwolenie na dostęp do portu UDP 1434 przez zapory, tak aby usługa Azure Database Migration Service mogła połączyć się z nazwanym wystąpieniem na serwerze źródłowym.
* Upewnienie się, że dane logowania używane do połączenia źródłowego programu SQL Server i docelowego wystąpienia zarządzanego należą do roli administratora systemu serwera.
* Utworzenie udziału sieciowego umożliwiającego wykonanie kopii zapasowej źródłowej bazy danych za pomocą usługi Azure Database Migration Service.
* Upewnienie się, że konto usługi z uruchomionym źródłowym wystąpieniem programu SQL Server ma uprawnienia w utworzonym udziale sieciowym oraz że konto komputera serwera źródłowego ma uprawnienia odczytu i zapisu do tego samego udziału.
* Zapisanie nazwy i hasła użytkownika systemu Windows, który ma uprawnienia do pełnej kontroli nad wcześniej utworzonym udziałem sieciowym. Usługa migracji bazy danych Azure personifikuje poświadczenia użytkownika, aby przekazać pliki kopii zapasowej do kontenera usługi Azure Storage w celu przywrócenia operacji.
* Utwórz kontener obiektów blob i pobierz jego identyfikator URI sygnatury dostępu Współdzielonego, wykonując kroki opisane w artykule [Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora magazynu.](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) Pamiętaj, aby zaznaczyć wszystkie uprawnienia (Odczyt, Zapis, Usuń, Lista) w oknie zasad podczas tworzenia identyfikatora URI sygnatury dostępu Współdzielonego.

   > [!NOTE]
   > Aby uzyskać pełną listę wymagań wstępnych wymaganych do użycia usługi migracji bazy danych Azure do przeprowadzania migracji z programu SQL Server do wystąpienia zarządzanego bazy danych SQL azure, zobacz samouczek [Migrowanie programu SQL Server do wystąpienia zarządzanego bazy danych SQL azure.](https://aka.ms/migratetomiusingdms)

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z omówieniem usługi migracji bazy danych platformy Azure i dostępności regionalnej, zobacz artykuł [Co to jest usługa migracji bazy danych platformy Azure](dms-overview.md).
