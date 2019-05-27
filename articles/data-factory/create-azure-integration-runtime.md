---
title: Tworzenie środowiska Azure integration runtime w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć środowisko IR platformy Azure w usłudze Azure Data Factory, która służy do kopiowania danych i wysyłanie działań przekształcania.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 4b166ded3dcef4a89951eb81f7f1b321f89a0e67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66153398"
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Jak utworzyć i skonfigurować środowisko IR platformy Azure
Integration Runtime (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory w celu zapewnienia możliwości integracji danych w różnych środowiskach sieciowych. Aby uzyskać więcej informacji na temat środowiska IR, zobacz [środowiska Integration runtime](concepts-integration-runtime.md).

Środowisko IR platformy Azure zapewnia zasoby obliczeniowe w pełni zarządzane natywnie wykonywania danych przemieszczania i wysyłaj działania przekształcania danych do obliczenia usług, takich jak HDInsight. Ona znajduje się w środowisku platformy Azure i obsługuje łączenie z zasobami w publicznych środowiska sieciowego przy użyciu dostępnych publicznie punktów końcowych.

W tym dokumencie przedstawiono, jak utworzyć i skonfigurować środowisko IR Azure. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-azure-ir"></a>Domyślne środowisko IR platformy Azure
Domyślnie każda fabryka danych ma środowiska Azure IR w wewnętrznej bazie danych, który obsługuje operacje w chmurze, magazyny danych i usług obliczeniowych w sieci publicznej. Lokalizacja tego środowiska Azure IR jest automatyczne rozwiązanie. Jeśli **connectVia** właściwość nie jest określona w definicji połączonej usługi, domyślnego środowiska Azure IR jest używane. Musisz jawnie tworzyć środowiska Azure IR, kiedy chcesz jawnie zdefiniować lokalizacji środowiska IR lub jeśli chcesz praktycznie grupie wykonania działania na różnych środowisk IR w celu zarządzania. 

## <a name="create-azure-ir"></a>Utwórz środowisko IR platformy Azure
Środowisko Integration Runtime mogą być tworzone za pomocą **AzDataFactoryV2IntegrationRuntime zestaw** polecenia cmdlet programu PowerShell. Aby utworzyć środowisko IR platformy Azure, należy określić nazwę, lokalizację i typ do polecenia. Oto przykładowe polecenie, aby utworzyć środowisko IR Azure przy użyciu lokalizacji ustawiona na "W regionie Europa Zachodnia":

```powershell
Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Dla środowiska Azure IR, typ musi być równa **zarządzane**. Nie trzeba możliwość określania szczegółów obliczeniowych, ponieważ jest w pełni zarządzany elastycznie w chmurze. Określ obliczeń szczegółowe informacje, takie jak rozmiar węzła i języka node count, gdy chcesz utworzyć Azure-SSIS IR. Aby uzyskać więcej informacji, zobacz [utworzyć i skonfigurować środowisko IR Azure-SSIS](create-azure-ssis-integration-runtime.md).

Można skonfigurować istniejącego środowiska Azure IR można zmienić jego lokalizację przy użyciu polecenia cmdlet programu PowerShell Set-AzDataFactoryV2IntegrationRuntime. Aby uzyskać więcej informacji o lokalizacji środowiska Azure IR, zobacz [wprowadzenie do produktu integration runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Użyj środowisko IR platformy Azure

Po utworzeniu środowiska Azure IR można odwoływać się do niej w definicji połączonej usługi. Poniżej są przykładowe sposoby odwoływania się do środowiska Azure Integration Runtime utworzonego powyżej z połączoną usługę Azure Storage:  

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
Zobacz następujące artykuły na temat tworzenia innych typów środowiska integration Runtime:

- [Create self-hosted integration runtime (Tworzenie środowiska Integration Runtime (Self-hosted)](create-self-hosted-integration-runtime.md)
- [Tworzenie środowiska Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md)
 
