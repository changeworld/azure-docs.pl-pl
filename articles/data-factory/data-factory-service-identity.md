---
title: Tożsamości zarządzanej przez usługę Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o tożsamości zarządzanej przez usługę Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260752"
---
# <a name="managed-identity-for-data-factory"></a>Tożsamość zarządzana dla usługi Data Factory

Ten artykuł pomoże Ci zrozumieć, jaka jest tożsamość zarządzaną dla fabryki danych (wcześniej znane jako tożsamość usługi zarządzanej/MSI) i jak to działa.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Przegląd

Podczas tworzenia fabryki danych, można utworzyć tożsamości zarządzanej oraz tworzenie fabryki. Tożsamość zarządzana jest aplikacji zarządzanej, zarejestrowany w usłudze Azure Active Directory i przedstawia tę fabrykę danych z konkretnych.

Tożsamość zarządzaną przez usługę Data Factory korzyści następujące funkcje:

- [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md), w którym to przypadku tożsamości zarządzanych fabryki danych jest używany do uwierzytelniania usługi Azure Key Vault.
- Łączników, takich jak [usługi Azure Blob storage](connector-azure-blob-storage.md), [usługi Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [usługi Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [usługi Azure SQL Database](connector-azure-sql-database.md), i [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Działania w sieci Web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generowanie tożsamości zarządzanej

Tożsamości zarządzanej przez usługę Data Factory jest generowany w następujący sposób:

- Podczas tworzenia fabryki danych za pośrednictwem **witryny Azure portal lub programu PowerShell**zarządzania tożsamościami zawsze zostaną utworzone automatycznie.
- Podczas tworzenia fabryki danych za pośrednictwem **zestawu SDK**zarządzania tożsamości zostanie utworzona tylko wtedy, gdy należy określić "tożsamość = FactoryIdentity() nowy" w obiekcie fabryka do tworzenia. Zobacz przykład w [.NET Przewodnik Szybki Start — tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Podczas tworzenia fabryki danych za pośrednictwem **interfejsu API REST**zarządzania tożsamości zostanie utworzona tylko wtedy, gdy określasz sekcji "identity" w treści żądania. Zobacz przykład w [REST Szybki Start — Tworzenie usługi data factory](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Możesz odnaleźć fabryki danych nie ma tożsamości zarządzanej skojarzone następujące [pobrać tożsamość zarządzaną](#retrieve-managed-identity) instrukcji, można jawnie generować za programowe aktualizowanie fabryki danych za pomocą inicjatora tożsamości:

- [Generowanie tożsamości zarządzanej przy użyciu programu PowerShell](#generate-managed-identity-using-powershell)
- [Generowanie tożsamości zarządzanej przy użyciu interfejsu API REST](#generate-managed-identity-using-rest-api)
- [Generowanie tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generowanie tożsamości zarządzanej przy użyciu zestawu SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- Nie można zmodyfikować tożsamość zarządzaną. Aktualizowanie data factory, która jeszcze tożsamość zarządzana nie ma żadnego wpływu, tożsamość zarządzaną pozostaje bez zmian.
>- Jeśli zaktualizujesz data factory, która jeszcze tożsamość zarządzaną bez określenia parametru "tożsamość" w obiekcie fabryki lub bez określenia sekcji "identity" w treści żądania REST, zostanie wyświetlony błąd.
>- Podczas usuwania fabryki danych wzdłuż zostaną usunięte skojarzone tożsamość zarządzaną.

### <a name="generate-managed-identity-using-powershell"></a>Generowanie tożsamości zarządzanej przy użyciu programu PowerShell

Wywołaj **AzDataFactoryV2 zestaw** polecenie ponownie, a następnie zostanie wyświetlony "Tożsamość" fields nowo generowany:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Generowanie tożsamości zarządzanej przy użyciu interfejsu API REST

Wywołanie poniżej interfejsu API z sekcją "tożsamość" w treści żądania:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Treść żądania**: Dodaj "tożsamość": {"type": "SystemAssigned" }.

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

**Odpowiedź**: tożsamość zarządzaną jest tworzony automatycznie i sekcji "identity" jest wypełniana odpowiednio.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generowanie tożsamości zarządzanej przy użyciu szablonu usługi Azure Resource Manager

**Szablon**: Dodaj "tożsamość": {"type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Generowanie tożsamości zarządzanej przy użyciu zestawu SDK

Wywołaj funkcję create_or_update fabryki danych przy użyciu tożsamości = FactoryIdentity() nowe. Przykładowy kod przy użyciu platformy .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Pobieranie tożsamości zarządzanej

Możesz pobrać tożsamość zarządzaną w witrynie Azure portal lub programowo. W poniższych sekcjach przedstawiono kilka przykładów.

>[!TIP]
> Jeśli nie widzisz tożsamość zarządzaną [zarządzanych tożsamości wygenerowania](#generate-managed-identity) , aktualizując fabryką.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Pobieranie tożsamości zarządzanej przy użyciu witryny Azure portal

Można znaleźć informacje o tożsamości zarządzanej z witryny Azure portal -> fabryki danych -> Właściwości:

- Identyfikator obiektu tożsamości zarządzanej
- Dzierżawa tożsamości zarządzanej
- **Zarządzany identyfikator aplikacji tożsamości** > Skopiuj tę wartość

![Pobieranie tożsamości zarządzanej](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Pobieranie tożsamości zarządzanej przy użyciu programu PowerShell

Tożsamość zarządzaną identyfikator podmiotu zabezpieczeń i identyfikator dzierżawy jest zwracana, gdy otrzymasz fabrykę danych określonego w następujący sposób:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Skopiuj identyfikator podmiotu zabezpieczeń, a następnie uruchom poniższe polecenie w usłudze Azure Active Directory o identyfikatorze podmiotu zabezpieczeń jako parametru, aby pobrać **ApplicationId**, którego używasz, aby udzielić dostępu:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące tematy, które wprowadzają, kiedy i jak używać usługi data factory z tożsamości zarządzanej:

- [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md)
- [Kopiowanie danych z i do usługi Azure Data Lake Store, przy użyciu zarządzanych tożsamości do uwierzytelniania zasobów platformy Azure](connector-azure-data-lake-store.md)

Zobacz [tożsamości zarządzanych, aby uzyskać przegląd zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/overview) dla większej ilości informacji kontekstowych na zarządzanych tożsamości dla zasobów platformy Azure, która fabryka danych tożsamości zarządzanej opiera się na. 
