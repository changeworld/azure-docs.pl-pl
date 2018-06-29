---
title: Tożsamość usługi fabryka danych Azure | Dokumentacja firmy Microsoft
description: Więcej informacji o tożsamości usługi fabryka danych z fabryki danych Azure.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: jingwang
ms.openlocfilehash: ba965b0610525f66c24dd6d5bad3fd113a6b6758
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050869"
---
# <a name="azure-data-factory-service-identity"></a>Tożsamość usługi fabryka danych Azure

Ten artykuł pomaga zrozumieć, co to jest tożsamość usługi fabryki danych i jak działa.

## <a name="overview"></a>Przegląd

Podczas tworzenia fabryki danych, można utworzyć tożsamości usługi oraz tworzenie fabryki. Tożsamość usługi jest zarejestrowany do usługi Azure Directory działania aplikacji zarządzanej i reprezentuje tej fabryki danych.

Tożsamość usługi fabryka danych przynosi korzyści następujące dwie funkcje:

- [Przechowywania poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md), w którym to przypadku tożsamość usługi fabryka danych jest używany do uwierzytelniania usługi Azure Key Vault.
- [Kopiowanie danych z i do usługi Azure Data Lake Store](connector-azure-data-lake-store.md), w którym to przypadku tożsamość usługi fabryka danych może służyć jako jednego z obsługiwanych typów uwierzytelniania usługi Data Lake Store.

## <a name="generate-service-identity"></a>Generowanie tożsamości usługi

Tożsamość usługi fabryka danych jest generowany w następujący sposób:

- Podczas tworzenia fabryki danych za pośrednictwem **portalu Azure lub programu PowerShell**, tożsamość usługi zawsze zostaną utworzone automatycznie.
- Podczas tworzenia fabryki danych za pośrednictwem **SDK**, tożsamość usługi zostanie utworzona tylko w przypadku określenia "tożsamości = FactoryIdentity() nowy" w obiekcie fabrykę do tworzenia. Zobacz przykład w [.NET — Szybki Start — tworzenie fabryki danych](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Podczas tworzenia fabryki danych za pośrednictwem **interfejsu API REST**, tożsamość usługi zostanie utworzona tylko, jeśli określono sekcję "identity" w treści żądania. Zobacz przykład w [REST Szybki Start — tworzenie fabryki danych](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Jeśli okaże się fabrykę danych nie ma usługi tożsamość skojarzoną po [pobrać tożsamość usługi](#retrieve-service-identity) instrukcji, jawnie wygenerowany przez programowane aktualizowanie fabryki danych za pomocą inicjatora tożsamości:

- [Generowanie tożsamości usługi za pomocą programu PowerShell](#generate-service-identity-using-powershell)
- [Generowanie tożsamości usługi za pomocą interfejsu API REST](#generate-service-identity-using-rest-api)
- [Generowanie tożsamość usługi przy użyciu zestawu SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- Nie można zmodyfikować tożsamości usługi. Aktualizowanie fabryki danych, które mają już tożsamość usługi nie będą miały wpływu, tożsamość usługi pozostaje bez zmian.
>- Jeśli zaktualizujesz fabryki danych, które mają już tożsamości usługi bez określania "identity" parametru w obiekt fabryki lub bez określania sekcji "identity" w treści żądania REST, wystąpi błąd.
>- Podczas usuwania fabryki danych tożsamości skojarzonej usługi zostaną usunięte wraz z.

### <a name="generate-service-identity-using-powershell"></a>Generowanie tożsamości usługi za pomocą programu PowerShell

Wywołanie **AzureRmDataFactoryV2 zestaw** polecenie ponownie, a następnie zostanie wyświetlony pola "Identity" nowo tworzone:

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

Wywołanie poniżej interfejsu API z sekcją "identity" w treści żądania:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Treść żądania**: Dodaj "identity": {"type": "SystemAssigned"}.

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

**Odpowiedź**: tożsamość usługi jest tworzony automatycznie i odpowiednio zawiera sekcja "identity".

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

### <a name="generate-service-identity-using-sdk"></a>Generowanie tożsamość usługi przy użyciu zestawu SDK

Wywołaj funkcję create_or_update fabryka danych z tożsamością = FactoryIdentity() nowe. Przykładowy kod przy użyciu platformy .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Pobieranie tożsamości usługi

Możesz pobrać z portalu Azure lub programistycznie tożsamości usługi. W poniższych sekcjach przedstawiono kilka przykładów.

>[!TIP]
> Jeśli nie widzisz tożsamość usługi [Generowanie tożsamość usługi](#generate-service-identity) , aktualizując fabryce.

### <a name="retrieve-service-identity-using-azure-portal"></a>Pobieranie tożsamości usługi za pomocą portalu Azure

Można znaleźć informacje o tożsamości usługi z portalu Azure -> fabrykę danych -> Ustawienia -> Właściwości:

- IDENTYFIKATOR TOŻSAMOŚCI USŁUGI
- DZIERŻAWA TOŻSAMOŚCI USŁUGI
- **Identyfikator aplikacji tożsamości usługi** > skopiować tę wartość

![Pobieranie tożsamości usługi](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Pobieranie tożsamości usługi za pomocą programu PowerShell

Tożsamość usługi identyfikator podmiotu zabezpieczeń i identyfikator dzierżawcy zostaną zwrócone w wyniku pobrania fabryki danych określonego w następujący sposób:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Skopiuj identyfikator podmiotu zabezpieczeń, a następnie uruchom poniżej polecenia usługi Azure Active Directory z identyfikator podmiotu zabezpieczeń jako parametr, aby uzyskać **ApplicationId**, używanego w celu udzielenia dostępu:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Kolejne kroki
Zobacz następujące tematy, które wprowadzenie, kiedy i jak używać tożsamości usługi fabryka danych:

- [Przechowywania poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md)
- [Skopiuj dane z/do usługi Azure Data Lake Store przy użyciu uwierzytelniania tożsamości zarządzanych usług](connector-azure-data-lake-store.md)

Zobacz [omówienie MSI](~/articles/active-directory/msi-overview.md) więcej tła na tożsamość usługi zarządzane, na podstawie jest tożsamość usługi fabryka danych. 