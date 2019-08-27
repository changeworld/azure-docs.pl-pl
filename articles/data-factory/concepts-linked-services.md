---
title: Połączone usługi w Azure Data Factory | Microsoft Docs
description: Dowiedz się więcej o połączonych usługach w Data Factory. Połączone usługi łączą magazyny obliczeniowe z danymi do fabryki danych.
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
ms.openlocfilehash: 904e063ae64a971de7f34fbfac63b7679f3bc363
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019951"
---
# <a name="linked-services-in-azure-data-factory"></a>Połączone usługi w Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-create-datasets.md)
> * [Bieżąca wersja](concepts-datasets-linked-services.md)

W tym artykule opisano, jakie są połączone usługi, jak są zdefiniowane w formacie JSON i jak są używane w potokach Azure Data Factory.

Jeśli dopiero zaczynasz Data Factory, zobacz [wprowadzenie do Azure Data Factory](introduction.md) przegląd.

## <a name="overview"></a>Omówienie
Fabryka danych może obejmować jeden lub wiele potoków. **Potok** jest logicznym grupą **działań** , które wspólnie wykonują zadanie. Działania w potoku definiują akcje do wykonania na danych. Można na przykład użyć działania kopiowania w celu skopiowania danych z SQL Server lokalnego do usługi Azure Blob Storage. Następnie możesz użyć działania programu Hive, które uruchamia skrypt programu Hive w klastrze usługi Azure HDInsight, aby przetwarzać dane z magazynu obiektów BLOB w celu utworzenia danych wyjściowych. Na koniec możesz użyć drugiego działania kopiowania, aby skopiować dane wyjściowe do Azure SQL Data Warehouse, na podstawie których są tworzone rozwiązania do raportowania analizy biznesowej (BI). Aby uzyskać więcej informacji na temat potoków i działań, zobacz [potoki i działania](concepts-pipelines-activities.md) w Azure Data Factory.

Teraz **zestaw** danych to nazwany widok zawierający dane, które po prostu wskazuje lub odwołuje się do danych, które mają być używane w działaniach jako dane wejściowe i wyjściowe.

Przed utworzeniem zestawu danych należy utworzyć **połączoną usługę** , aby połączyć magazyn danych z fabryką danych. Połączone usługi działają podobnie do parametrów połączenia, umożliwiając definiowanie informacji wymaganych przez usługę Data Factory do nawiązywania połączeń z zasobami zewnętrznymi. Zastanów się to w ten sposób; DataSet reprezentuje strukturę danych w połączonych magazynach danych, a połączona usługa definiuje połączenie ze źródłem danych. Na przykład połączona usługa Azure Storage łączy konto magazynu z fabryką danych. Zestaw danych obiektów blob platformy Azure reprezentuje kontener obiektów blob i folder w ramach tego konta usługi Azure Storage, które zawiera wejściowe obiekty blob do przetworzenia.

Oto przykładowy scenariusz. Aby skopiować dane z magazynu obiektów BLOB do bazy danych SQL, należy utworzyć dwie połączone usługi: Azure Storage i Azure SQL Database. Następnie Utwórz dwa zestawy danych: Zestaw danych obiektów blob platformy Azure (który odwołuje się do połączonej usługi Azure Storage) i zestawu danych tabeli SQL Azure (który odnosi się do Azure SQL Database połączonej usługi). Połączone usługi Azure Storage i Azure SQL Database zawierają parametry połączenia, które Data Factory używane w środowisku uruchomieniowym do nawiązywania połączenia z usługą Azure Storage i Azure SQL Database. Zestaw danych obiektów blob platformy Azure Określa kontener obiektów blob i folder obiektów blob, który zawiera wejściowe obiekty blob w magazynie obiektów BLOB. Zestaw danych tabeli SQL Azure określa tabelę SQL w bazie danych SQL, do której mają zostać skopiowane dane.

Na poniższym diagramie przedstawiono relacje między potokiem, działaniem, zestawem danych i połączoną usługą w Data Factory:

![Relacja między potokiem, działaniem, zestawem danych, połączonymi usługami](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>KOD JSON połączonej usługi
Połączona usługa w Data Factory jest definiowana w formacie JSON w następujący sposób:

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

W poniższej tabeli opisano właściwości w powyższym kodzie JSON:

Właściwość | Opis | Wymagane |
-------- | ----------- | -------- |
name | Nazwa połączonej usługi. Zobacz [reguły nazewnictwa Azure Data Factory](naming-rules.md). |  Tak |
type | Typ połączonej usługi. Na przykład: AzureStorage (magazyn danych) lub AzureBatch (COMPUTE). Zobacz opis typeProperties. | Tak |
typeProperties | Właściwości typu są różne dla poszczególnych magazynów danych lub obliczeń. <br/><br/> W przypadku obsługiwanych typów magazynów danych i ich właściwości typów zobacz tabelę [typów zestawu danych](concepts-datasets-linked-services.md#dataset-type) w tym artykule. Przejdź do artykułu łącznika magazynu danych, aby dowiedzieć się więcej o właściwościach typu specyficznych dla magazynu danych. <br/><br/> W przypadku obsługiwanych typów obliczeniowych i ich właściwości typu zobacz [połączone usługi obliczeniowe](compute-linked-services.md). | Tak |
connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Możesz użyć Azure Integration Runtime lub samodzielnego Integration Runtime (Jeśli magazyn danych znajduje się w sieci prywatnej). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. | Nie

## <a name="linked-service-example"></a>Przykład połączonej usługi
Następująca połączona usługa to połączona usługa Azure Storage. Należy zauważyć, że typ jest ustawiony na AzureStorage. Właściwości typu połączonej usługi Azure Storage obejmują parametry połączenia. Usługa Data Factory używa tych parametrów połączenia do nawiązywania połączenia z magazynem danych w czasie wykonywania.

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
Połączone usługi można tworzyć przy użyciu jednego z tych narzędzi lub zestawów SDK: [interfejsów API platformy .NET](quickstart-create-data-factory-dot-net.md), [programu PowerShell](quickstart-create-data-factory-powershell.md), [interfejsu API REST](quickstart-create-data-factory-rest-api.md), Azure Resource Manager szablonu i Azure Portal

## <a name="data-store-linked-services"></a>Połączone usługi magazynu danych
Łączenie z magazynami danych można znaleźć w naszych [obsługiwanych magazynach i formatach danych](copy-activity-overview.md#supported-data-stores-and-formats). Odwołuje się do listy dla określonych właściwości połączenia, które są zbędne dla różnych sklepów.

## <a name="data-store-supported-activities"></a>Działania obsługiwane przez magazyn danych

[!INCLUDE [Connector-activity support matrix](../../includes/connector-activity-support-matrix.md)]

## <a name="compute-linked-services"></a>Usługi połączone usługi Compute
[Środowiska obliczeniowe](compute-linked-services.md) referencyjne obsługują szczegółowe informacje o różnych środowiskach obliczeniowych, z którymi można nawiązać połączenie z fabryki danych oraz w różnych konfiguracjach.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z poniższym samouczkiem, aby uzyskać instrukcje krok po kroku dotyczące tworzenia potoków i zestawów danych przy użyciu jednego z tych narzędzi lub zestawu SDK.

- [Quickstart: create a data factory using .NET (Szybki start: tworzenie fabryki danych przy użyciu platformy .NET)](quickstart-create-data-factory-dot-net.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu programu PowerShell](quickstart-create-data-factory-powershell.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu interfejsu API REST](quickstart-create-data-factory-rest-api.md)
- [Szybki Start: Tworzenie fabryki danych przy użyciu Azure Portal](quickstart-create-data-factory-portal.md)
