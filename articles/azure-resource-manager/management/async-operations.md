---
title: Stan operacji asynchronicznych
description: W tym artykule opisano, jak śledzić operacji asynchronicznych na platformie Azure. Pokazuje wartości, które umożliwiają Pobierz stan operacji długotrwałej.
ms.topic: conceptual
ms.date: 12/09/2018
ms.custom: seodec18
ms.openlocfilehash: 1cf8898e5fd63e35447f6580e13347ba6d7fc413
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485444"
---
# <a name="track-asynchronous-azure-operations"></a>Śledzenie operacji asynchronicznych na platformie Azure
Niektóre operacje REST platformy Azure jest uruchamiane asynchronicznie, ponieważ nie można ukończyć operacji, szybko. W tym artykule opisano, jak śledzić stan operacji asynchronicznych za pomocą wartości zwracanych w odpowiedzi.  

## <a name="status-codes-for-asynchronous-operations"></a>Kody stanu dla operacji asynchronicznych
Operacja asynchroniczna początkowo zwraca kod stanu HTTP albo:

* 201 (utworzono)
* 202 (zaakceptowano) 

Po pomyślnym zakończeniu operacji zwraca albo:

* 200 (OK)
* 204 (Brak zawartości) 

Zapoznaj się [dokumentację interfejsu API REST](/rest/api/) do odpowiedzi dla operacji wykonujesz.

## <a name="monitor-status-of-operation"></a>Monitorowanie stanu operacji
Asynchroniczne operacje REST zwracają wartości nagłówka, które można określić stanu operacji. Istnieją potencjalnie trzy wartości nagłówka do sprawdzenia:

* `Azure-AsyncOperation` — Adres URL do sprawdzania stanu trwających operacji. Jeśli operacja zwraca tę wartość, zawsze używać go (a nie lokalizację) do śledzenia stanu operacji.
* `Location` — Adres URL do określania, po zakończeniu operacji. Użyj tej wartości, tylko wtedy, gdy elementu Azure-AsyncOperation nie jest zwracany.
* `Retry-After` -Liczba sekund oczekiwania przed sprawdzania stanu operacji asynchronicznej.

Nie każda operacja asynchroniczna zwraca wszystkie te wartości. Na przykład może być konieczne należy ocenić wartość nagłówka elementu Azure-AsyncOperation jeden raz i wartość nagłówka lokalizacji innej operacji. 

Możesz pobrać wartości nagłówka, jak będzie pobierać dowolną wartość nagłówka żądania. Na przykład w języku C#, możesz pobrać wartość nagłówka z `HttpWebResponse` obiektu o nazwie `response` następującym kodem:

```cs
response.Headers.GetValues("Azure-AsyncOperation").GetValue(0)
```

## <a name="azure-asyncoperation-request-and-response"></a>Elementu Azure-AsyncOperation żądań i odpowiedzi

Aby pobrać stan operacji asynchronicznej, Wyślij żądanie Pobierz do adresu URL w elementu Azure-AsyncOperation wartość nagłówka.

Treść odpowiedzi z tej operacji zawiera informacje na temat operacji. Możliwe wartości zwrócony przez operację można znaleźć w poniższym przykładzie:

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

Tylko `status` jest zwracane są wszystkie odpowiedzi. Obiekt błąd jest zwracany, jeśli jest w stanie Niepowodzenie lub anulowane. Wszystkie inne wartości są opcjonalne; w związku z tym odpowiedzi, które otrzymujesz mogą różnić się od tego przykładu.

## <a name="provisioningstate-values"></a>wartości provisioningState

Operacje, które utworzenia, aktualizacji lub usunięcia (PUT, PATCH, DELETE) zasobu zwracają zazwyczaj `provisioningState` wartość. Po zakończeniu operacji jest jedną z następujących trzech wartości zwracane: 

* Powodzenie
* Niepowodzenie
* Anulowane

Wszystkie inne wartości oznaczają, że operacja jest nadal uruchomiona. Dostawca zasobów może zwrócić dostosowane wartość, która wskazuje jej stan. Na przykład, może pojawić się **zaakceptowano** po żądania otrzymane i uruchomione.

## <a name="example-requests-and-responses"></a>Przykład żądań i odpowiedzi

### <a name="start-virtual-machine-202-with-azure-asyncoperation"></a>Uruchom maszynę wirtualną (202 przy użyciu elementu Azure-AsyncOperation)
W tym przykładzie pokazano, jak ustalić stan **start** operacji dla maszyn wirtualnych. Wstępne żądanie jest w następującym formacie:

```HTTP
POST 
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Compute/virtualMachines/{vm-name}/start?api-version=2016-03-30
```

Zwraca kod stanu: 202. Między wartościami nagłówka zostaną wyświetlone następujące czynności:

```HTTP
Azure-AsyncOperation : https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Aby sprawdzić stan operacji asynchronicznej, wysłanie kolejnego żądania do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/locations/{region}/operations/{operation-id}?api-version=2016-03-30
```

Treść odpowiedzi zawiera stanu operacji:

```json
{
  "startTime": "2017-01-06T18:58:24.7596323+00:00",
  "status": "InProgress",
  "name": "9a062a88-e463-4697-bef2-fe039df73a02"
}
```

### <a name="deploy-resources-201-with-azure-asyncoperation"></a>Wdrażanie zasobów (201 przy użyciu elementu Azure-AsyncOperation)

W tym przykładzie pokazano, jak ustalić stan **wdrożeń** Operacja wdrażania zasobów na platformie Azure. Wstępne żądanie jest w następującym formacie:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/microsoft.resources/deployments/{deployment-name}?api-version=2016-09-01
```

Zwraca kod stanu 201. Treść odpowiedzi zawiera:

```json
"provisioningState":"Accepted",
```

Między wartościami nagłówka zostaną wyświetlone następujące czynności:

```HTTP
Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Aby sprawdzić stan operacji asynchronicznej, wysłanie kolejnego żądania do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Resources/deployments/{deployment-name}/operationStatuses/{operation-id}?api-version=2016-09-01
```

Treść odpowiedzi zawiera stanu operacji:

```json
{"status":"Running"}
```

Po zakończeniu wdrożenia odpowiedź zawiera:

```json
{"status":"Succeeded"}
```

### <a name="create-storage-account-202-with-location-and-retry-after"></a>Tworzenie konta usługi storage (202 lokalizacją i Retry-After)

W tym przykładzie pokazano, jak ustalić stan **tworzenie** operacji dla kont magazynu. Wstępne żądanie jest w następującym formacie:

```HTTP
PUT
https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2016-01-01
```

I treść żądania zawiera właściwości dla konta magazynu:

```json
{ "location": "South Central US", "properties": {}, "sku": { "name": "Standard_LRS" }, "kind": "Storage" }
```

Zwraca kod stanu: 202. Między wartościami nagłówka zostaną wyświetlone następujące dwie wartości:

```HTTP
Location: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
Retry-After: 17
```

Po oczekiwanie na liczbę (w sekundach) określonego w Retry-After, sprawdź stan operacji asynchronicznej, wysyłając kolejne żądanie do tego adresu URL.

```HTTP
GET 
https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Storage/operations/{operation-id}?monitor=true&api-version=2016-01-01
```

Jeśli żądanie jest nadal uruchomione, zostanie wyświetlony kod stanu: 202. Jeśli żądanie zostało ukończone, Twoje wyświetlany kod stanu 200, a treść odpowiedzi zawiera właściwości konta magazynu, który został utworzony.

## <a name="next-steps"></a>Następne kroki

* Aby dokumentacji dotyczącej operacji REST, zobacz [dokumentację interfejsu API REST](/rest/api/).
* Aby uzyskać informacje na temat wdrażania szablonów za pomocą interfejsu API REST Menedżer zasobów, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejs API rest Menedżer zasobów](../templates/deploy-rest.md).