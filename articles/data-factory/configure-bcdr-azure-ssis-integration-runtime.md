---
title: Konfigurowanie środowiska Azure-SSIS Integration Runtime w trybie failover bazy danych SQL Database | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS Integration Runtime przy użyciu replikacji geograficznej usługi Azure SQL Database i trybu failover dla bazy danych SSISDB
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2012ccf4d9fd3e62ba248f29f922f868077e4061
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054803"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurowanie środowiska Azure-SSIS Integration Runtime przy użyciu replikacji geograficznej usługi Azure SQL Database i trybu failover

W tym artykule opisano sposób konfigurowania środowiska Azure-SSIS Integration Runtime przy użyciu usługi Azure SQL Database replikacji geograficznej dla bazy danych SSISDB. W przypadku przejścia w tryb failover można zapewnić, że Azure-SSIS IR nadal działa z dodatkowej bazy danych.

Aby uzyskać więcej informacji na temat replikacji geograficznej i trybu failover dla usługi SQL Database, zobacz [omówienie: aktywnej replikacji geograficznej i automatyczny tryb failover grup](../sql-database/sql-database-geo-replication-overview.md).

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenariusz 1 — wskazuje Azure-SSIS IR do odczytu i zapisu punkt końcowy odbiornika

### <a name="conditions"></a>Warunki

Ten rozdział ma zastosowanie, gdy są spełnione następujące warunki:

- Azure-SSIS IR wskazuje punkt końcowy odbiornika do odczytu i zapisu grupy trybu failover.

  I

- Serwer bazy danych SQL jest *nie* skonfigurowano reguły punktu końcowego usługi sieci wirtualnej.

### <a name="solution"></a>Rozwiązanie

Po przejściu do trybu failover, aplikacja nie wie Azure-SSIS IR. Azure-SSIS IR automatycznie łączy się z nową podstawową grupy trybu failover.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenariusz 2 - Azure-SSIS IR wskazuje punkt końcowy serwera podstawowego

### <a name="conditions"></a>Warunki

Ten rozdział ma zastosowanie, gdy jest spełniony jeden z następujących warunków:

- Azure-SSIS IR wskazuje punkt końcowy serwera podstawowego grupy trybu failover. Ten punkt końcowy ulega zmianie, po przejściu do trybu failover.

  LUB

- Serwer usługi Azure SQL Database jest skonfigurowany z regułą punktu końcowego usługi sieci wirtualnej.

  LUB

- Serwer bazy danych jest baza danych wystąpienia zarządzanego SQL skonfigurowany z siecią wirtualną.

### <a name="solution"></a>Rozwiązanie

Po przejściu do trybu failover, należy wykonać następujące czynności:

1. Zatrzymaj Azure-SSIS IR.

2. Ponownie skonfiguruj środowisko IR, aby wskazywały nowy podstawowego punktu końcowego i sieci wirtualnej w nowym regionie.

3. Uruchom ponownie IR.

W poniższych sekcjach opisano te kroki, które bardziej szczegółowo.

### <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że możliwe jest Odzyskiwanie po awarii dla serwera usługi Azure SQL Database w przypadku, gdy serwer ma ulegnie awarii, w tym samym czasie. Aby uzyskać więcej informacji, zobacz [omówienie ciągłości działania za pomocą usługi Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Jeśli używasz sieci wirtualnej w bieżącym regionie, należy użyć innej sieci wirtualnej w nowym regionie połączyć środowiska Azure-SSIS integration runtime. Aby uzyskać więcej informacji, zobacz [dołączyć środowisko Azure-SSIS integration runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).

- Jeśli używasz instalacji niestandardowej, może być konieczne przygotowanie innego identyfikatora URI połączenia SAS dla kontenera obiektów blob, który przechowuje skryptu instalacji niestandardowej i skojarzone pliki, aby go w dalszym ciągu być niedostępne podczas przestoju. Aby uzyskać więcej informacji, zobacz [konfigurowania ustawień niestandardowych na środowiska Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Kroki

Wykonaj następujące kroki, aby zatrzymać środowiska IR Azure-SSIS, przełączyć środowisko IR w nowym regionie i uruchom go ponownie.

1. Zatrzymaj środowisko IR w regionie, oryginalnym.

2. Wywołaj następujące polecenie w programie PowerShell, aby zaktualizować środowisko IR z nowymi ustawieniami.

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Aby uzyskać więcej informacji na temat tego polecenia programu PowerShell, zobacz [tworzenie środowiska Azure-SSIS integration runtime w usłudze Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Uruchom ponownie środowisko IR.

## <a name="next-steps"></a>Kolejne kroki

Należy wziąć pod uwagę te inne opcje konfiguracji dla środowiska Azure-SSIS IR:

- [Konfigurowanie środowiska Azure-SSIS Integration Runtime, dla wysoko wydajnych](configure-azure-ssis-integration-runtime-performance.md)

- [Dostosowywanie ustawień środowiska Azure-SSIS integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Aprowizowanie środowiska Azure-SSIS Integration Runtime w wersji Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)
