---
title: Enterprise Edition środowiska uruchomieniowego integracji usług SSIS Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje i konfiguracja Enterprise Edition dla środowiska uruchomieniowego integracji usług SSIS Azure
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2018
ms.author: douglasl
ms.openlocfilehash: 42c0c000ef661a1a256ebf49cd099a4cae2185c0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="enterprise-edition-of-the-azure-ssis-integration-runtime"></a>Środowisko uruchomieniowe integracji usług SSIS Azure w wersji Enterprise

Enterprise Edition środowiska uruchomieniowego integracji usług SSIS Azure umożliwia, wykonaj następujące zaawansowane i funkcji premium:
-   Zmień składniki przechwytywania danych (CDC)
-   Łączniki programu SAP BW, Oracle i Teradata
-   Łączniki programu SQL Server Analysis Services (SSAS) i Azure Analysis Services (AAS) i przekształcenia
-   Rozmytego grupowania i wyszukiwania rozmytego przekształcenia
-   Przekształcenia wyodrębniania terminów i terminu wyszukiwania

Niektóre z tych funkcji wymagają zainstalowania dodatkowych składników, aby dostosować podczerwieni Azure SSIS. Aby uzyskać więcej informacji o sposobie instalowania dodatkowych składników, zobacz [Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Funkcje organizacji

| **Funkcje organizacji** | **Opisy** |
|---|---|
| Składniki CDC | Źródło CDC, zadanie kontroli i przekształcenie podziału są preinstalowany w Podczerwieni Azure SSIS w wersji Enterprise. Aby połączyć się z programem Oracle, również należy zainstalować CDC projektanta i usługi na innym komputerze. |
| Łączniki programu Oracle | Oracle Menedżera połączeń, źródłowy i docelowy są preinstalowany w Podczerwieni Azure SSIS w wersji Enterprise. Należy również zainstalować sterownik Oracle Call Interface (OCI), a w razie potrzeby skonfiguruj Oracle transportu sieciowego podłoża (nazwę) w podczerwieni Azure SSIS. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Łączniki programu Teradata | Musisz zainstalować Teradata Menedżera połączeń, źródła i docelowy, a także sterownik interfejsu API programu Teradata sam, jak równoległe (TPT) i Teradata ODBC w wersji Enterprise IR Azure SSIS. Aby uzyskać więcej informacji, zobacz [Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Łączniki programu SAP BW | Menedżer połączeń programu SAP BW, źródłowym i docelowym są preinstalowany w Podczerwieni Azure SSIS w wersji Enterprise. Należy również zainstalować sterownik programu SAP BW w podczerwieni Azure SSIS. Te łączniki obsługuje SAP BW 7.0 i jego wcześniejsze wersje. Aby połączyć się nowsze wersje programu SAP BW lub innych produktów SAP, można zakupu i zainstaluj łączniki SAP z niezależnym dostawcom oprogramowania innych firm w podczerwieni Azure SSIS. Aby uzyskać więcej informacji o sposobie instalowania dodatkowych składników, zobacz [Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Składniki usługi analizy               | Docelowy uczenia modelu wyszukiwania danych, docelowego przetwarzania wymiaru i docelowego przetwarzania partycji, a także transformacji zapytań wyszukiwania danych są preinstalowany w Podczerwieni Azure SSIS w wersji Enterprise. Wszystkie te składniki obsługi programu SQL Server Analysis Services (SSAS), ale tylko partycji przetwarzania docelowy obsługuje Azure Analysis Services (AAS). Aby nawiązać połączenie SSAS, trzeba również [skonfigurować poświadczenia uwierzytelniania systemu Windows w SSISDB](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Oprócz tych składników usług analizy wykonania zadań DDL, zadanie przetwarzania Analysis Services i zadanie zapytania wyszukiwania danych są również preinstalowany na Azure SSIS IR Standard/Enterprise Edition. |
| Rozmytego grupowania i wyszukiwania rozmytego przekształcenia  | Przekształcenia rozmytego grupowania i wyszukiwania rozmytego jest preinstalowany na IR Azure SSIS w wersji Enterprise. Te składniki obsługują program SQL Server i bazy danych SQL Azure do przechowywania danych referencyjnych. |
| Przekształcenia wyodrębniania terminów i terminu wyszukiwania | Przekształcenia wyodrębniania terminów i terminu wyszukiwania są preinstalowany w Podczerwieni Azure SSIS w wersji Enterprise. Te składniki obsługują program SQL Server i bazy danych SQL Azure do przechowywania danych referencyjnych. |

## <a name="instructions"></a>Instrukcje

1.  Pobierz i zainstaluj [programu Azure PowerShell (w wersji 5.4 lub nowszej)](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018).

2.  Podczas obsługi administracyjnej lub ponownie skonfigurować IR Azure SSIS przy użyciu programu PowerShell, uruchom `Set-AzureRmDataFactoryV2IntegrationRuntime` z **Enterprise** jako wartość **wersji** parametru przed rozpoczęciem podczerwieni Azure SSIS. Poniżej przedstawiono przykładowy skrypt:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Kolejne kroki

-   [Instalacja niestandardowa środowiska uruchomieniowego integracji usług SSIS Azure](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Jak wdrażać płatnej lub licencji niestandardowych składników środowiska uruchomieniowego integracji usług SSIS Azure](how-to-develop-azure-ssis-ir-licensed-components.md)