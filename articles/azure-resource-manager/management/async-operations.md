---
title: Stan operacji asynchronicznych
description: W tym artykule opisano sposób śledzenia operacji asynchronicznych na platformie Azure. Pokazuje wartości używane do uzyskania stanu długotrwałej operacji.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485444"
---
# <a name="track-asynchronous-azure-operations"></a>Śledzenie asynchronicznych operacji platformy Azure
Niektóre operacje usługi Azure REST są uruchamiane asynchronicznie, ponieważ nie można szybko ukończyć operacji. W tym artykule opisano sposób śledzenia stanu operacji asynchronicznych za pomocą wartości zwracanych w odpowiedzi.  

## <a name="status-codes-for-asynchronous-operations"></a>Kody stanu dla operacji asynchronicznych
Operacja asynchronizawcza początkowo zwraca kod stanu HTTP jednego z:

* 201 (Utworzono)
* 202 (zaakceptowany) 

Po pomyślnym zakończeniu operacji zwraca:

* 200 (OK)
* 204 (Brak treści) 

Zapoznaj się z [dokumentacją interfejsu API REST,](/rest/api/) aby zobaczyć odpowiedzi na wykonywane działanie.

## <a name="monitor-status-of-operation"></a>Monitoruj stan działania
Operacje asynchroniczną REST zwracają wartości nagłówka, których używasz do określenia stanu operacji. Istnieją potencjalnie trzy wartości nagłówka do zbadania:

* `Azure-AsyncOperation`- Adres URL do sprawdzania bieżącego stanu operacji. Jeśli operacja zwraca tę wartość, zawsze należy jej używać (zamiast lokalizacji), aby śledzić stan operacji.
* `Location`- Adres URL do określania, kiedy operacja została zakończona. Tej wartości należy używać tylko wtedy, gdy usługa Azure-AsyncOperation nie jest zwracana.
* `Retry-After`- Liczba sekund oczekiwania przed sprawdzeniem stanu operacji asynchroniiowej.

Jednak nie każda operacja asynchronizacjowa zwraca wszystkie te wartości. Na przykład może być konieczne oszacowanie wartości nagłówka Azure-AsyncOperation dla jednej operacji i wartość nagłówka lokalizacji dla innej operacji. 

Można pobrać wartości nagłówka, jak można pobrać dowolną wartość nagłówka dla żądania. Na przykład w języku C#, można pobrać `HttpWebResponse` wartość `response` nagłówka z obiektu o nazwie z następującym kodem:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Żądanie i odpowiedź azure-asyncoperation

Aby uzyskać stan operacji asynchroniiowej, wyślij żądanie GET do adresu URL w wartości nagłówka Azure-AsyncOperation.

Treść odpowiedzi z tej operacji zawiera informacje o operacji. Poniższy przykład przedstawia możliwe wartości zwrócone z operacji:

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

Tylko `status` jest zwracany dla wszystkich odpowiedzi. Obiekt błędu jest zwracany, gdy stan jest nie powiodło się lub anulowane. Wszystkie inne wartości są opcjonalne; w związku z tym otrzymana odpowiedź może wyglądać inaczej niż w przykładzie.

## <a name="provisioningstate-values"></a>wartości stanu inicjowania obsługi administracyjnej

Operacje, które tworzą, aktualizują lub usuwają (PUT, PATCH, DELETE) zasób zazwyczaj zwracają `provisioningState` wartość. Po zakończeniu operacji zwracana jest jedna z następujących trzech wartości: 

* Powodzenie
* Niepowodzenie
* Anulowane

Wszystkie inne wartości wskazują, że operacja jest nadal uruchomiona. Dostawca zasobów może zwrócić dostosowaną wartość, która wskazuje jego stan. Na przykład może pojawić się **Zaakceptowane** po odebraniu i uruchomieniu żądania.

## <a name="example-requests-and-responses"></a>Przykładowe żądania i odpowiedzi

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Uruchamianie maszyny wirtualnej (202 z działaniem usługi Azure-Async)
W tym przykładzie pokazano, jak określić stan operacji **uruchamiania** maszyn wirtualnych. Początkowe żądanie jest w następującym formacie:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Zwraca kod stanu 202. Wśród wartości nagłówka widać:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Aby sprawdzić stan operacji asynchroniiowej, wyślij kolejne żądanie do tego adresu URL.

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

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Wdrażanie zasobów (201 za pomocą operacji azure-async)

W tym przykładzie pokazano, jak określić stan operacji **wdrożeń** do wdrażania zasobów na platformie Azure. Początkowe żądanie jest w następującym formacie:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Zwraca kod stanu 201. Treść odpowiedzi obejmuje:

```json
"provisioningState":"Accepted",
```

Wśród wartości nagłówka widać:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Aby sprawdzić stan operacji asynchroniiowej, wyślij kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Treść odpowiedzi zawiera stan operacji:

```json
{"status":"Running"}
```

Po zakończeniu wdrażania odpowiedź zawiera:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Tworzenie konta magazynu (202 z lokalizacją i ponawianie próby po)

W tym przykładzie pokazano, jak określić stan operacji **tworzenia** dla kont magazynu. Początkowe żądanie jest w następującym formacie:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

A treść żądania zawiera właściwości dla konta magazynu:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Zwraca kod stanu 202. Wśród wartości nagłówka są widoczne następujące dwie wartości:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Po odczekaniu liczby sekund określonej w ponów próbę-po, sprawdź stan operacji asynchroniiowej, wysyłając inne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Jeśli żądanie jest nadal uruchomione, otrzymasz kod stanu 202. Jeśli żądanie zostało ukończone, otrzymasz kod stanu 200, a treść odpowiedzi zawiera właściwości utworzonego konta magazynu.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać dokumentację dotyczącą każdej operacji REST, zobacz [dokumentację interfejsu API REST](/rest/api/).
* Aby uzyskać informacje dotyczące wdrażania szablonów za pośrednictwem interfejsu API REST Menedżera zasobów, zobacz [Wdrażanie zasobów za pomocą szablonów Menedżera zasobów i interfejsu API REST Menedżera zasobów](../templates/deploy-rest.md).