---
title: Aprowizowanie wersji Enterprise Edition dla środowiska Azure-SSIS Integration Runtime | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje Enterprise Edition dla środowiska Azure-SSIS Integration Runtime i jak aprowizować go
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d2b06d044f68972ef72dd9b53401980e84ef779f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152425"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Aprowizowanie środowiska Azure-SSIS Integration Runtime w wersji Enterprise Edition

Azure-SSIS Integration Runtime w wersji Enterprise pozwala użyć następujące zaawansowane i funkcje w warstwie premium:
-   Zmień dane funkcji Przechwytywania składników
-   Łączniki bazy danych Oracle, Teradata i system SAP BW
-   Łączniki programu SQL Server Analysis Services (SSAS) i Azure Analysis Services (AAS) i przekształcenia
-   Rozmyte przekształcenia grupowania i wyszukiwania rozmytego
-   Przekształcenia określenie wyodrębnianie i termin wyszukiwania

Niektóre z tych funkcji wymaga zainstalowania dodatkowych składników, aby dostosować Azure-SSIS IR. Aby uzyskać więcej informacji na temat sposobu instalowania dodatkowych składników, zobacz [niestandardowa konfiguracja środowiska Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funkcje wersji Enterprise

| **Funkcje wersji Enterprise** | **Opisy** |
|---|---|
| Przechwytywanie zmian danych — składniki | Źródło przechwytywania zmian danych, zadania kontroli i przekształcania rozdzielacza są preinstalowane na Azure-SSIS IR Enterprise Edition. Połączyć się z oprogramowania Oracle, należy również zainstalować na innym komputerze przez projektanta przechwytywania zmian danych i usługi. |
| Łączniki bazy danych Oracle | Menedżer połączeń bazy danych Oracle, źródłowym i docelowym są preinstalowane na Azure-SSIS IR Enterprise Edition. Należy również zainstalować sterownik Oracle wywołania interfejsu (OCI) i w razie potrzeby skonfiguruj Oracle transportu sieciowego podłoża (TNS) Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Łączniki programu Teradata | Musisz zainstalować Menedżera połączeń Teradata, źródła i docelowy, a także sterownik Teradata równoległe przewoźnika (TPT) interfejsu API i Teradata ODBC w wersji Enterprise środowiska IR Azure-SSIS. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Łączniki systemu SAP BW | Menedżera połączeń systemu SAP BW, źródłowym i docelowym jest preinstalowany na Azure-SSIS IR Enterprise Edition. Należy również zainstalować sterownik systemu SAP BW na platformie Azure-SSIS IR. Te łączniki pomocy technicznej SAP BW w wersji 7.0 lub starszych wersji. Połączyć się z nowszymi wersjami systemu SAP BW lub innych produktów SAP, można kupić i zainstalować łączniki systemu SAP z innych producentów niezależnych dostawców oprogramowania na platformie Azure-SSIS IR. Aby uzyskać więcej informacji na temat sposobu instalowania dodatkowych składników, zobacz [niestandardowa konfiguracja środowiska Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Składników usług Analysis Services               | Docelowy szkolenie modelu wyszukiwania danych, docelowy przetwarzania wymiaru i docelowy przetwarzanie partycji, a także transformacji zapytań wyszukiwania danych jest preinstalowany na środowiska Azure-SSIS IR Enterprise Edition. Wszystkie te składniki obsługi programu SQL Server Analysis Services (SSAS), ale tylko partycji przetwarzania docelowy obsługuje usługi Azure Analysis Services (AAS). Aby nawiązać połączenie SSAS, trzeba będzie również [skonfigurować poświadczenia uwierzytelniania Windows w SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Oprócz tych składników usług analizy wykonaj zadania języka DDL, zadanie przetwarzania Analysis Services i zadanie zapytań wyszukiwania danych są również wstępnie zainstalowane w wersji Standard/Enterprise środowiska IR Azure-SSIS. |
| Rozmyte przekształcenia grupowania i wyszukiwania rozmytego  | Przekształcenia grupowania rozmyte i Wyszukiwanie rozmyte jest preinstalowany w Azure-SSIS IR Enterprise Edition. Te składniki pomocy technicznej usługi Azure SQL Database i programu SQL Server do przechowywania danych referencyjnych. |
| Przekształcenia określenie wyodrębnianie i termin wyszukiwania | Przekształcenia określenie wyodrębnianie i termin wyszukiwania jest preinstalowany na Azure-SSIS IR Enterprise Edition. Te składniki pomocy technicznej usługi Azure SQL Database i programu SQL Server do przechowywania danych referencyjnych. |

## <a name="instructions"></a>Instrukcje

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Pobierz i zainstaluj [programu Azure PowerShell](/powershell/azure/install-az-ps).

2.  Podczas aprowizowania lub ponownie skonfigurować środowisko IR Azure-SSIS przy użyciu programu PowerShell, uruchom `Set-AzDataFactoryV2IntegrationRuntime` z **Enterprise** jako wartość pozycji **wersji** parametru przed rozpoczęciem korzystania z usługi Azure-SSIS IR. Poniżej przedstawiono przykładowy skrypt:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Kolejne kroki

-   [Niestandardowa konfiguracja środowiska Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak tworzyć aplikacje płatne lub licencjonowane niestandardowych składników dla środowiska Azure-SSIS integration runtime](how-to-develop-azure-ssis-ir-licensed-components.md)
