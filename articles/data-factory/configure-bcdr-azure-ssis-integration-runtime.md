---
title: Konfigurowanie Azure-SSIS Integration Runtime dla SQL Database trybu failover
description: W tym artykule opisano sposób konfigurowania Azure-SSIS Integration Runtime z Azure SQL Database replikacją geograficzną i trybem failover dla bazy danych SSISDB
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/14/2018
ms.openlocfilehash: 92f7d25a9c19409b220b6a71fba87da91e51a415
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928497"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurowanie Azure-SSIS Integration Runtime z Azure SQL Database replikacją geograficzną i trybem failover

W tym artykule opisano sposób konfigurowania Azure-SSIS Integration Runtime przy użyciu Azure SQL Database replikacji geograficznej dla bazy danych SSISDB. W przypadku przejścia w tryb failover można upewnić się, że Azure-SSIS IR nadal pracuje z pomocniczą bazą danych.

Aby uzyskać więcej informacji na temat replikacji geograficznej i trybu failover dla SQL Database, zobacz temat [przegląd: aktywnej replikacji geograficznej i grup Autotryb failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenariusz 1 — Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu i zapisu

### <a name="conditions"></a>Warunki

Ta sekcja ma zastosowanie w przypadku spełnienia następujących warunków:

- Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu i zapisu grupy trybu failover.

  AND

- Serwer SQL Database *nie* jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover jest on niewidoczny dla Azure-SSIS IR. Azure-SSIS IR automatycznie nawiązuje połączenie z nowym podstawowym grupą trybu failover.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenariusz 2 — Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera

### <a name="conditions"></a>Warunki

Ta sekcja ma zastosowanie w przypadku spełnienia jednego z następujących warunków:

- Azure-SSIS IR wskazuje podstawowy punkt końcowy serwera grupy trybu failover. Ten punkt końcowy zmienia się w przypadku przejścia w tryb failover.

  LUB

- Serwer Azure SQL Database jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej.

  LUB

- Serwer bazy danych jest SQL Database wystąpieniem zarządzanym skonfigurowanym za pomocą sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

W przypadku przejścia w tryb failover należy wykonać następujące czynności:

1. Zatrzymaj Azure-SSIS IR.

2. Skonfiguruj ponownie środowisko IR, aby wskazywało nowy podstawowy punkt końcowy i sieć wirtualną w nowym regionie.

3. Uruchom ponownie środowisko IR.

W poniższych sekcjach opisano te kroki bardziej szczegółowo.

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że włączono funkcję odzyskiwania po awarii dla serwera Azure SQL Database na wypadek wystąpienia awarii w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Omówienie ciągłości działania w Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Jeśli używasz sieci wirtualnej w bieżącym regionie, musisz użyć innej sieci wirtualnej w nowym regionie, aby połączyć środowisko Azure-SSIS Integration Runtime. Aby uzyskać więcej informacji, zobacz [dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).

- Jeśli używasz konfiguracji niestandardowej, może być konieczne przygotowanie innego identyfikatora URI sygnatury dostępu współdzielonego dla kontenera obiektów blob, który przechowuje niestandardowy skrypt instalacji i skojarzone pliki, więc będzie on nadal dostępny podczas przestoju. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konfiguracji niestandardowej w środowisku Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroki

Wykonaj następujące kroki, aby zatrzymać Azure-SSIS IR, przełącz środowisko IR do nowego regionu i uruchom go ponownie.

1. Zatrzymaj środowisko IR w oryginalnym regionie.

2. Wywołaj następujące polecenie w programie PowerShell, aby zaktualizować środowisko IR przy użyciu nowych ustawień.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Uruchom ponownie środowisko IR.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenariusz 3 — dołączanie istniejącego SSISDB (katalog SSIS) do nowego Azure-SSIS IR

Gdy w bieżącym regionie wystąpi awaria ADF lub Azure-SSIS IR, można sprawić, aby SSISDB pracował z nowym Azure-SSIS IR w nowym regionie.

### <a name="prerequisites"></a>Wymagania wstępne

- Jeśli używasz sieci wirtualnej w bieżącym regionie, musisz użyć innej sieci wirtualnej w nowym regionie, aby połączyć środowisko Azure-SSIS Integration Runtime. Aby uzyskać więcej informacji, zobacz [dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).

- Jeśli używasz konfiguracji niestandardowej, może być konieczne przygotowanie innego identyfikatora URI sygnatury dostępu współdzielonego dla kontenera obiektów blob, który przechowuje niestandardowy skrypt instalacji i skojarzone pliki, więc będzie on nadal dostępny podczas przestoju. Aby uzyskać więcej informacji, zobacz [Konfigurowanie konfiguracji niestandardowej w środowisku Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroki

Wykonaj następujące kroki, aby zatrzymać Azure-SSIS IR, przełącz środowisko IR do nowego regionu i uruchom go ponownie.

1. Wykonaj procedurę składowaną, aby SSISDB dołączone do **\<new_data_factory_name\>** lub **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Utwórz nową fabrykę danych o nazwie **\<new_data_factory_name\>** w nowym regionie. Aby uzyskać więcej informacji, zobacz Tworzenie fabryki danych.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie fabryki danych Azure przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)

3. Utwórz nową Azure-SSIS IR o nazwie **\<new_integration_runtime_name\>** w nowym regionie przy użyciu Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Aby uzyskać więcej informacji o tym poleceniu programu PowerShell, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime w Azure Data Factory](create-azure-ssis-integration-runtime.md)

4. Uruchom ponownie środowisko IR.

## <a name="next-steps"></a>Następne kroki

Należy wziąć pod uwagę następujące inne opcje konfiguracji Azure-SSIS IR:

- [Konfigurowanie Azure-SSIS Integration Runtime na potrzeby wysokiej wydajności](configure-azure-ssis-integration-runtime-performance.md)

- [Dostosowywanie konfiguracji środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Udostępnianie wersji Enterprise Edition dla Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
