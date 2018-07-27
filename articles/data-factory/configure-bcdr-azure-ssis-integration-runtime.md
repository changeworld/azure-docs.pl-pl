---
title: Zaleceń biznesowych ciągłość działalności biznesowej i odzyskiwanie po awarii recovery (BCDR) dla środowiska Azure-SSIS Integration Runtime | Dokumentacja firmy Microsoft
description: W tym artykule opisano zaleceń biznesowych ciągłość działalności biznesowej i odzyskiwanie po awarii odzyskiwania dla środowiska Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285782"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Zaleceń biznesowych ciągłość działalności biznesowej i odzyskiwanie po awarii recovery (BCDR) dla środowiska Azure-SSIS Integration Runtime

Na potrzeby odzyskiwania po awarii można zatrzymać Twojego środowiska Azure-SSIS integration runtime w regionie, w którym są uruchomione i przełącz się do innego regionu, aby uruchomić go ponownie. Firma Microsoft zaleca użycie [regiony sparowane platformy Azure](../best-practices-availability-paired-regions.md) do tego celu.

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że możliwe jest Odzyskiwanie po awarii dla serwera usługi Azure SQL Database w przypadku, gdy serwer ma ulegnie awarii, w tym samym czasie. Aby uzyskać więcej informacji, zobacz [omówienie ciągłości działania za pomocą usługi Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Jeśli używasz sieci wirtualnej w bieżącym regionie, należy użyć innej sieci wirtualnej w nowym regionie połączyć środowiska Azure-SSIS integration runtime. Aby uzyskać więcej informacji, zobacz [dołączyć środowisko Azure-SSIS integration runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).

- Jeśli używasz instalacji niestandardowej, może być konieczne przygotowanie innego identyfikatora URI połączenia SAS dla kontenera obiektów blob, który przechowuje skryptu instalacji niestandardowej i skojarzone pliki, aby go w dalszym ciągu być niedostępne podczas przestoju. Aby uzyskać więcej informacji, zobacz [konfigurowania ustawień niestandardowych na środowiska Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="steps"></a>Kroki

Wykonaj następujące kroki, aby zatrzymać środowiska IR Azure-SSIS, przełączyć środowisko IR w nowym regionie i uruchom go ponownie.

1. Zatrzymaj środowisko IR w regionie, oryginalnym.

2. Wywołaj następujące polecenie w programie PowerShell, aby zaktualizować środowisko IR

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
