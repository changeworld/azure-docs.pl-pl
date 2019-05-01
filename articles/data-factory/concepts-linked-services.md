---
title: Połączone usługi w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat połączone usługi w usłudze Data Factory. Połączone usługi łączą magazyny danych/obliczeń do usługi data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870135"
---
# <a name="linked-services-in-azure-data-factory"></a>Połączone usługi w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-datasets-linked-services.md)

W tym artykule opisano, jakie połączone usługi są, jak są one definiowane w formacie JSON i w jaki sposób są one używane w potoki usługi Azure Data Factory.

Jeśli jesteś nowym użytkownikiem usługi Data Factory, zobacz [wprowadzenie do usługi Azure Data Factory](introduction.md) omówienie.

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. A **potoku** jest logicznym grupowaniem **działania** wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Może na przykład użyć działania kopiowania, aby skopiować dane z lokalnego programu SQL Server do usługi Azure Blob storage. Następnie należy użyć działania programu Hive, które uruchamia skrypt Hive w klastrze usługi HDInsight platformy Azure do przetwarzania danych z magazynu obiektów Blob w celu wygenerowania danych wyjściowych. Może na koniec użyj drugiego działania kopiowania, aby skopiować dane wyjściowe do usługi Azure SQL Data Warehouse, na podstawie której raportowania dotyczącego rozwiązania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [potoki i działania](concepts-pipelines-activities.md) w usłudze Azure Data Factory.

Teraz **dataset** jest nazwany widok danych, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w Twojej **działania** jako dane wejściowe i wyjściowe.

Przed utworzeniem zestawu danych, należy utworzyć **połączoną usługę** połączyć usługi magazynu danych w usłudze data factory. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Mechanizm działa w ten sposób; zestaw danych reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa zawiera definicję połączenia ze źródłem danych. Na przykład Azure Storage połączona usługa łączy konto usługi storage z fabryką danych. Zestaw danych obiektów Blob platformy Azure reprezentuje kontener obiektów blob oraz folder, w ramach tego konta magazynu platformy Azure, który zawiera wejściowe obiekty BLOB mają być przetwarzane.

Poniżej przedstawiono przykładowy scenariusz. Aby skopiować dane z magazynu obiektów Blob do usługi SQL database, utworzysz dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie należy utworzyć dwa zestawy danych: Azure zestaw danych obiektów Blob (która odwołuje się do połączonej usługi Azure Storage) i zestaw danych tabeli SQL Azure, (która odwołuje się do usługi Azure SQL Database, połączone). Usługi Azure Storage i Azure SQL Database, połączone usługi zawiera parametry połączenia, które usługi Data Factory używa w środowisku uruchomieniowym połączyć się z usługi Azure Storage i Azure SQL Database, odpowiednio. Zestaw danych obiektów Blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty BLOB w usłudze Blob storage. Zestaw danych tabeli SQL Azure Określa tabelę SQL w usłudze SQL database, do której ma zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potoku, działania, zestaw danych i połączonej usługi w usłudze Data Factory:

![Relacja potoku, działania, zestaw danych, połączonych usług](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Notacja JSON połączonej usługi
W formacie JSON połączonej usługi w usłudze Data Factory jest zdefiniowana w następujący sposób:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

W poniższej tabeli opisano właściwości w powyższy kod JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa połączonej usługi. Zobacz [usługi Azure Data Factory — reguły nazewnictwa](naming-rules.md). |  Yes |
type | Typ połączonej usługi. Na przykład: AzureStorage (magazynu danych) lub AzureBatch (składnik obliczeniowy). Zobacz opis typeProperties. | Yes |
typeProperties | Właściwości typu są różne dla każdego magazynu danych lub obliczeń. <br/><br/> Obsługiwane dane można przechowywać w typów i ich właściwości typu, zobacz [typ zestawu danych](concepts-datasets-linked-services.md#dataset-type) tabeli, w tym artykule. Przejdź do artykułu łącznika magazynu danych, aby dowiedzieć się więcej o właściwościach typu specyficznych dla magazynu danych. <br/><br/> Typy obsługiwane obliczeniowych i ich właściwości typu, zobacz [usługi połączone usługi Compute](compute-linked-services.md). | Yes |
connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. (Jeśli Twój magazyn danych znajduje się w sieci prywatnej), można użyć środowiska Azure Integration Runtime lub środowiskiem Integration Runtime. Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie

## <a name="linked-service-example"></a>Przykład połączona usługa
Następujące połączonej usługi jest połączona usługa Azure Storage. Należy zauważyć, że typ jest ustawiona na AzureStorage. Właściwości typu połączonej usługi Azure Storage obejmują parametry połączenia. Usługa Data Factory używa tych parametrów połączenia, aby nawiązać połączenie z magazynem danych w czasie wykonywania.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Tworzenie połączonych usług
Utworzysz usługi połączone przy użyciu jednej z następujących narzędzi lub zestawów SDK: [interfejsu API platformy .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [interfejsu API REST](quickstart-create-data-factory-rest-api.md), szablon usługi Azure Resource Manager i witryny Azure portal

## <a name="data-store-linked-services"></a>Połączone usługi magazynu danych
Łączenie z magazynami danych można znaleźć w naszej [obsługiwane magazyny danych i formatów](copy-activity-overview.md#supported-data-stores-and-formats). Odniesienie do listy właściwości określonego połączenia potrzebne do różnych sklepach.

## <a name="compute-linked-services"></a>Usługi połączone usługi Compute
Odwołanie [obsługiwanych środowisk obliczeniowych](compute-linked-services.md) szczegółowe informacje o różnych obliczeniowych środowisk można nawiązać połączenie z fabryką danych, a także różne konfiguracje.

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące samouczki krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednej z następujących narzędzi lub zestawów SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki Start: tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki Start: tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md)
- [Szybki Start: tworzenie fabryki danych przy użyciu witryny Azure portal](quickstart-create-data-factory-portal.md)
