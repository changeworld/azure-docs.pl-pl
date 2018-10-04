---
title: Tożsamości usługi w usłudze Azure Data Factory | Dokumentacja firmy Microsoft
description: Więcej informacji na temat tożsamość usługi fabryki danych w usłudze Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: db0bc0cb64c0b6d7df9319c8d2c5850a27e767a1
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249217"
---
# <a name="azure-data-factory-service-identity"></a>Tożsamości usługi w usłudze Azure Data Factory

Ten artykuł pomaga zrozumieć, jaka jest tożsamość usługi fabryki danych i sposób jej działania.

## <a name="overview"></a>Przegląd

Podczas tworzenia fabryki danych, można utworzyć tożsamości usługi oraz tworzenie fabryki. Tożsamość usługi jest aplikacji zarządzanej, zarejestrowany w usłudze Azure Active Directory i przedstawia tę fabrykę danych z konkretnych.

Tożsamość usługi fabryki danych korzyści następujące funkcje:

- [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md), w którym to przypadku tożsamość usługi fabryki danych jest używany do uwierzytelniania usługi Azure Key Vault.
- Łączników, takich jak [usługi Azure Blob storage](connector-azure-blob-storage.md), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [usługi Azure SQL Database](connector-azure-sql-database.md), i [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).

## <a name="generate-service-identity"></a>Generowanie tożsamości usługi

Tożsamość usługi fabryki danych jest generowany w następujący sposób:

- Podczas tworzenia fabryki danych za pośrednictwem **witryny Azure portal lub programu PowerShell**, tożsamość usługi zawsze zostaną utworzone automatycznie.
- Podczas tworzenia fabryki danych za pośrednictwem **SDK**, tożsamość usługi zostanie utworzona tylko wtedy, gdy należy określić "tożsamość = FactoryIdentity() nowy" w obiekcie fabryka do tworzenia. Zobacz przykład w [.NET Przewodnik Szybki Start — tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Podczas tworzenia fabryki danych za pośrednictwem **interfejsu API REST**, tożsamość usługi zostanie utworzona tylko wtedy, gdy określasz sekcji "identity" w treści żądania. Zobacz przykład w [REST Szybki Start — Tworzenie usługi data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Możesz odnaleźć fabryki danych nie ma usługi tożsamość skojarzoną następujące [pobrać tożsamości usługi](#retrieve-service-identity) instrukcji, można jawnie generować za programowe aktualizowanie fabryki danych za pomocą inicjatora tożsamości:

- [Generowanie tożsamości usługi za pomocą programu PowerShell](#generate-service-identity-using-powershell)
- [Generowanie tożsamości usługi za pomocą interfejsu API REST](#generate-service-identity-using-rest-api)
- [Generowanie tożsamości usługi za pomocą zestawu SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- Nie można zmodyfikować tożsamości usługi. Aktualizowanie data factory, która jeszcze tożsamość usługi nie ma żadnego wpływu, tożsamość usługi pozostaje bez zmian.
>- Jeśli zaktualizujesz data factory, która jeszcze tożsamości usługi bez określenia parametru "tożsamość" w obiekcie fabryki lub bez określenia sekcji "identity" w treści żądania REST, zostanie wyświetlony błąd.
>- Podczas usuwania fabryki danych zostanie usunięta tożsamość skojarzona usługa wzdłuż.

### <a name="generate-service-identity-using-powershell"></a>Generowanie tożsamości usługi za pomocą programu PowerShell

Wywołaj **Set-AzureRmDataFactoryV2** polecenie ponownie, a następnie zostanie wyświetlony "Tożsamość" fields nowo generowany:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>Generowanie tożsamości usługi za pomocą interfejsu API REST

Wywołanie poniżej interfejsu API z sekcją "tożsamość" w treści żądania:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Treść żądania**: Dodaj "tożsamość": {"type": "wartość SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Odpowiedź**: tożsamość usługi jest tworzony automatycznie i sekcji "identity" jest wypełniana odpowiednio.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>Generowanie tożsamości usługi za pomocą zestawu SDK

Wywołaj funkcję create_or_update fabryki danych przy użyciu tożsamości = FactoryIdentity() nowe. Przykładowy kod przy użyciu platformy .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Pobieranie tożsamości usługi

Możesz pobrać tożsamości usługi w witrynie Azure portal lub programowo. W poniższych sekcjach przedstawiono kilka przykładów.

>[!TIP]
> Jeśli nie widzisz tożsamości usługi [usługi tożsamości wygenerowania](#generate-service-identity) , aktualizując fabryką.

### <a name="retrieve-service-identity-using-azure-portal"></a>Pobieranie tożsamości usługi za pomocą witryny Azure portal

Można znaleźć informacje o tożsamości usługi w witrynie Azure portal -> fabryki danych -> Ustawienia -> Właściwości:

- IDENTYFIKATOR TOŻSAMOŚCI USŁUGI
- DZIERŻAWA TOŻSAMOŚCI USŁUGI
- **Identyfikator aplikacji tożsamości usługi** > Skopiuj tę wartość

![Pobieranie tożsamości usługi](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Pobieranie tożsamości usługi za pomocą programu PowerShell

Tożsamość usługi identyfikator podmiotu zabezpieczeń i identyfikator dzierżawy zostanie zwrócona po otrzymaniu fabrykę danych określonego w następujący sposób:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Skopiuj identyfikator podmiotu zabezpieczeń, a następnie uruchom poniższe polecenie w usłudze Azure Active Directory o identyfikatorze podmiotu zabezpieczeń jako parametru, aby pobrać **ApplicationId**, którego używasz, aby udzielić dostępu:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące tematy, które wprowadzają, kiedy i jak używać tożsamość usługi fabryki danych:

- [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiowanie danych z i do usługi Azure Data Lake Store, przy użyciu zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure](connector-azure-data-lake-store.md)

Zobacz [tożsamości zarządzanych, aby uzyskać przegląd zasobów platformy Azure](~/articles/active-directory/msi-overview.md) Aby uzyskać więcej ogólnych informacji o zarządzanych tożsamości dla zasobów platformy Azure, których tożsamość usługi fabryki danych opiera się na. 