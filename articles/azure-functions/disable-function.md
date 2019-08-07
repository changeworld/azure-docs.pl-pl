---
title: Jak wyłączyć funkcje w Azure Functions
description: Dowiedz się, jak wyłączyć i włączyć funkcje w Azure Functions 1. x i 2. x.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: 183056d01146194b2854a70df790802e1a0bb839
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782234"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Jak wyłączyć funkcje w Azure Functions

W tym artykule wyjaśniono, jak wyłączyć funkcję w Azure Functions. Aby *wyłączyć* funkcję oznacza, że środowisko uruchomieniowe zignoruje automatyczny wyzwalacz, który jest zdefiniowany dla funkcji. W ten sposób zależy od wersji środowiska uruchomieniowego i języka programowania:

* Funkcje 2. x:
  * Jeden ze sposobów dla wszystkich języków
  * Opcjonalna Metoda C# dla bibliotek klas
* Funkcje 1. x:
  * Języki skryptów
  * C#biblioteki klas

## <a name="functions-2x---all-languages"></a>Funkcje 2. x — wszystkie języki

W funkcjach 2. x można wyłączyć funkcję przy użyciu ustawienia aplikacji w formacie `AzureWebJobs.<FUNCTION_NAME>.Disabled`. To ustawienie można utworzyć i zmodyfikować programowo przy użyciu interfejsu wiersza polecenia platformy Azure. Można to również zrobić z karty **Zarządzanie** funkcją w [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) polecenia platformy Azure można użyć polecenie, aby utworzyć i zmodyfikować ustawienie aplikacji. Następujące polecenie wyłącza funkcję o nazwie `QueueTrigger` przez utworzenie ustawienia aplikacji o nazwie `AzureWebJobs.QueueTrigger.Disabled` ustaw ją na `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Aby ponownie włączyć funkcję, należy ponownie uruchomić to samo polecenie o wartości `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portal

Można również użyć przełącznika **stanu funkcji** na karcie **Zarządzanie** funkcją. Przełącznik działa po utworzeniu i usunięciu `AzureWebJobs.<FUNCTION_NAME>.Disabled` ustawienia aplikacji.

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2. x C# — biblioteki klas

W bibliotece klas funkcji 2. x zalecamy użycie metody, która działa dla wszystkich języków. Ale jeśli wolisz, możesz [użyć atrybutu Disable jako w funkcjach 1. x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Functions 1. x — Języki obsługi skryptów

W przypadku języków skryptów, C# takich jak skrypty i JavaScript, należy `disabled` użyć właściwości pliku *Function. JSON* , aby określić, że środowisko uruchomieniowe nie wyzwala funkcji. Dla `true` tej właściwości można ustawić wartość lub na nazwę ustawienia aplikacji:

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
lub 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

W drugim przykładzie funkcja jest wyłączona, gdy istnieje ustawienie aplikacji o nazwie IS_DISABLED i ma wartość `true` lub 1.

Można edytować plik w Azure Portal lub użyć przełącznika **stanu funkcji** na karcie **Zarządzanie** funkcją. Przełącznik portalu działa przez zmianę pliku *Function. JSON* .

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1. x C# — biblioteki klas

W bibliotece klas Functions 1. x należy użyć `Disable` atrybutu, aby zapobiec wyzwoleniu funkcji. Można użyć atrybutu bez konstruktora, jak pokazano w następującym przykładzie:

```csharp
public static class QueueFunctions
{
    [Disable]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Atrybut bez parametru konstruktora wymaga ponownego skompilowania i ponownego wdrożenia projektu w celu zmiany stanu wyłączenia funkcji. Bardziej elastycznym sposobem korzystania z tego atrybutu jest dołączenie parametru konstruktora, który odwołuje się do ustawienia aplikacji logicznej, jak pokazano w następującym przykładzie:

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

Ta metoda pozwala włączać i wyłączać funkcję przez zmianę ustawienia aplikacji, bez ponownej kompilacji lub ponownego wdrożenia. Zmiana ustawienia aplikacji powoduje, że aplikacja funkcji zostanie ponownie uruchomiona, więc zmiana stanu wyłączenia zostanie natychmiast rozpoznana.

> [!IMPORTANT]
> Ten `Disabled` atrybut jest jedynym sposobem wyłączenia funkcji biblioteki klas. Nie można bezpośrednio edytować pliku generated *Function. JSON* dla funkcji biblioteki klas. Jeśli edytujesz ten plik, nie ma to żadnego wpływu `disabled` na właściwość.
>
> Ta sama wartość dotyczy przełącznika **stanu funkcji** na karcie **Zarządzanie** , ponieważ działa przez zmianę pliku *Function. JSON* .
>
> Należy również pamiętać, że w portalu może wskazywać, że funkcja jest wyłączona, gdy nie jest.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano wyłączanie wyzwalaczy automatycznych. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md).
