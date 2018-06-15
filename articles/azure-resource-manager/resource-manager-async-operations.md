---
title: Operacje asynchroniczne platformy Azure | Dokumentacja firmy Microsoft
description: Informacje dotyczące śledzenia operacji asynchronicznych na platformie Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2017
ms.author: tomfitz
ms.openlocfilehash: f62212f0488e4d1be49b419615b3a16b80033fd9
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358714"
---
# <a name="track-asynchronous-azure-operations"></a>Śledzenie Azure operacji asynchronicznych
Niektóre operacje Azure REST jest uruchamiane asynchronicznie, ponieważ nie można ukończyć operacji, szybko. W tym temacie opisano, jak śledzić stan operacji asynchronicznych za pomocą wartości zwracanych w odpowiedzi.  

## <a name="status-codes-for-asynchronous-operations"></a>Kody stanu dla operacji asynchronicznych
Operacja asynchroniczna początkowo zwraca kod stanu HTTP albo:

* 201 (utworzono)
* 202 (zaakceptowane) 

Po pomyślnym zakończeniu operacji zwraca albo:

* 200 (OK)
* 204 (Brak zawartości) 

Zapoznaj się [dokumentacja interfejsu API REST](/rest/api/) do odpowiedzi dla operacji są wykonywane. 

## <a name="monitor-status-of-operation"></a>Monitor stanu operacji
Operacje asynchroniczne REST nagłówka wartości zwracane, które można określić stanu operacji. Istnieją potencjalnie trzy wartości nagłówka do sprawdzenia:

* `Azure-AsyncOperation` — Adres URL do sprawdzania stanu trwających operacji. Jeśli operacja zwraca tę wartość, zawsze używać go (a nie lokalizacja) do śledzenia stanu operacji.
* `Location` — Adres URL, określając po zakończeniu operacji. Użyj tej wartości, tylko wtedy, gdy nie są zwracane przez operację asynchroniczną Azure.
* `Retry-After` -Liczbę sekund oczekiwania przed sprawdzeniem stanu operacji asynchronicznej.

Jednak nie każda operacja asynchroniczna zwraca wszystkie te wartości. Na przykład może być konieczne obliczyć wartość nagłówka operację asynchroniczną Azure dla jednej operacji, a wartość nagłówka lokalizacji innej operacji. 

Można pobrać wartości nagłówka, ponieważ może pobrać wartości nagłówka żądania. Na przykład w języku C#, możesz pobrać wartość nagłówka z `HttpWebResponse` obiektu o nazwie `response` z następującym kodem:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Azure AsyncOperation żądań i odpowiedzi

Aby uzyskać stan operacji asynchronicznej, Wyślij żądanie GET do adresu URL w wartości nagłówka operację asynchroniczną na platformie Azure.

Treść odpowiedzi z tej operacji zawiera informacje na temat operacji. W poniższym przykładzie przedstawiono możliwe wartości zwrócony przez operację:

```json
{
    "id": "{resource path from GET operation}",
    "name": "{operation-id}", 
    "status" : "Succeeded | Failed | Canceled | {resource provider values}", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "percentComplete": {double between 0 and 100 },
    "properties": {
        /* Specific resource provider values for successful operations */
    },
    "error" : { 
        "code": "{error code}",  
        "message": "{error description}" 
    }
}
```

Tylko `status` są zwracane do wszystkich odpowiedzi. Obiekt błąd jest zwracany, gdy stan to niepowodzenie lub anulowane. Wszystkie inne wartości są opcjonalne; w związku z tym odpowiedzi otrzymywanych może różnić się od tego przykładu.

## <a name="provisioningstate-values"></a>wartości właściwości provisioningState

Operacje, które tworzenie, aktualizowanie lub usuwanie (PUT, PATCH, Usuń) zasobu zwracają zazwyczaj `provisioningState` wartość. Po ukończeniu operacji, zwracany jest jeden z trzech następujących wartości: 

* Powodzenie
* Niepowodzenie
* Anulowane

Wszystkie inne wartości oznaczają, że operacji jest nadal uruchomiona. Dostawca zasobów można zwrócić dostosowanych wartości wskazującej, jego stan. Na przykład może zostać wyświetlony **zaakceptowane** podczas żądania jest odebrane i uruchomiona.

## <a name="example-requests-and-responses"></a>Przykład żądań i odpowiedzi

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Uruchom maszynę wirtualną (202 z operację asynchroniczną Azure)
W tym przykładzie pokazano, jak ustalić stan **start** operacji dla maszyn wirtualnych. Początkowe żądanie znajduje się w następującym formacie:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Zwraca kod stanu 202. Wśród wartości nagłówka wyświetlane są:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Aby sprawdzić stan operacji asynchronicznej, wysyłanie innego żądania do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Treść odpowiedzi zawiera stan operacji:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Wdrażanie zasobów (201 z operację asynchroniczną Azure)

W tym przykładzie pokazano, jak ustalić stan **wdrożeń** Operacja wdrażania zasobów na platformie Azure. Początkowe żądanie znajduje się w następującym formacie:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Zwraca kod stanu 201. Treść odpowiedzi zawiera:

```json
"provisioningState":"Accepted",
```

Wśród wartości nagłówka wyświetlane są:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Aby sprawdzić stan operacji asynchronicznej, wysyłanie innego żądania do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Treść odpowiedzi zawiera stan operacji:

```json
{"status":"Running"}
```

Po zakończeniu wdrożenia, zawiera odpowiedzi:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Tworzenie konta magazynu (202 z lokalizacji i ponów próbę po)

W tym przykładzie pokazano, jak ustalić stan **utworzyć** operacji dla kont magazynu. Początkowe żądanie znajduje się w następującym formacie:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

I treść żądania zawiera właściwości konta magazynu:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Zwraca kod stanu 202. Wśród wartości nagłówka zobacz temat dwóch wartości:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Po oczekiwanie na liczbę sekund określonej w ponownych prób po, sprawdź stan operacji asynchronicznej, wysyłając innego żądania do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Jeśli żądanie jest nadal uruchomiony, zostanie wyświetlony kod stanu 202. Jeśli żądanie zostało ukończone, z kodem stanu 200 odbierania i treść odpowiedzi zawiera właściwości konta magazynu, który został utworzony.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać dokumentację każdej operacji REST, zobacz [dokumentacja interfejsu API REST](/rest/api/).
* informacje o wdrażaniu szablonów za pomocą interfejsu REST API usługi Resource Manager, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu REST API usługi Resource Manager](resource-group-template-deploy-rest.md).