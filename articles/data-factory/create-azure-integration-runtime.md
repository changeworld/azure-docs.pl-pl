---
title: Tworzenie środowiska uruchomieniowego integracji Azure w fabryce danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowiska uruchomieniowego integracji Azure w fabryce danych Azure, który służy do kopiowania danych i wysyłania transformacji działania.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: douglasl
ms.openlocfilehash: cb1a263c0a33a291a44e7c60b3c032d7f9dc16a3
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054079"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak utworzyć i skonfigurować środowiska uruchomieniowego integracji Azure
Integracja środowiska uruchomieniowego (IR) jest używany przez fabryki danych Azure zapewnienie możliwości integracji danych w różnych środowiskach sieci infrastruktury obliczeniowej. Aby uzyskać więcej informacji na temat IR, zobacz [integrację środowiska uruchomieniowego](concepts-integration-runtime.md).

Azure IR udostępnia zasoby obliczeniowe pełni zarządzana natywnie wykonywania danych przemieszczania i wysyłania danych przekształcania działań do obliczenia usług, takich jak usługa HDInsight. Znajduje się w środowisku platformy Azure, a obsługuje połączenia z zasobami w publicznych środowisko sieci z publiczne punkty końcowe dostępny.

Tym dokumencie przedstawiono sposób tworzenia i konfigurowania środowiska uruchomieniowego integracji Azure. 

## <a name="default-azure-ir"></a>Domyślna Azure IR.
Domyślnie każdy fabryki danych ma IR Azure w wewnętrznej bazy danych, która obsługuje operacje w chmurze, dane są przechowywane i obliczeniowe usług w sieci publicznej. Lokalizacja tej IR Azure jest automatyczne rozwiązanie. Jeśli **connectVia** właściwość nie została określona w definicji połączonej usługi, domyślne Azure IR jest używana. Musisz jawnie tworzyć IR Azure, gdy ma zostać jawnie zdefiniuj lokalizację IR, lub jeśli chcesz praktycznie grupy wykonania działania na różnych amerykańskim w celu zarządzania. 

## <a name="create-azure-ir"></a>Utwórz Azure IR.
Integracja środowiska uruchomieniowego można tworzyć przy użyciu **AzureRmDataFactoryV2IntegrationRuntime zestaw** polecenia cmdlet programu PowerShell. Aby utworzyć IR Azure, należy określić nazwę, lokalizację i typ polecenia. Oto przykład polecenia do utworzenia IR Azure z lokalizacją ustawioną wartość "Europa Zachodnia":

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Dla usługi Azure IR, typ musi mieć ustawioną **zarządzane**. Nie trzeba określić szczegóły obliczeń, ponieważ jest on pełni zarządzany elastycznie w chmurze. Określ obliczeń szczegółowych informacji, takich jak rozmiar węzła i węzła liczba, gdy chcesz utworzyć podczerwieni Azure SSIS. Aby uzyskać więcej informacji, zobacz [tworzenie i konfigurowanie IR Azure SSIS](create-azure-ssis-integration-runtime.md).

Można skonfigurować istniejącą IR Azure do zmiany lokalizacji za pomocą polecenia cmdlet programu PowerShell Set-AzureRmDataFactoryV2IntegrationRuntime. Aby uzyskać więcej informacji o lokalizacji IR Azure, zobacz [wprowadzenie do integracji środowiska uruchomieniowego](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Użyj Azure IR.

Po utworzeniu IR Azure można odwoływać się do niej w definicji połączonej usługi. Poniżej jest przykładowe sposoby odwoływania się do środowiska uruchomieniowego integracji Azure utworzone powyżej z połączonej usługi magazynu Azure:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące artykuły na temat tworzenia innych typów środowisk uruchomieniowych integracji:

- [Create self-hosted integration runtime (Tworzenie środowiska Integration Runtime (Self-hosted)](create-self-hosted-integration-runtime.md)
- [Tworzenie środowiska uruchomieniowego integracji usług SSIS Azure](create-azure-ssis-integration-runtime.md)
 
