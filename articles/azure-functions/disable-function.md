---
title: Jak wyłączyć funkcje w Azure Functions
description: Dowiedz się, jak wyłączyć i włączyć funkcje w Azure Functions 1. x i 2. x.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 7968580fcaa40575571a41f067fa74fbdc0a3a34
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233045"
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

W funkcjach 2. x można wyłączyć funkcję przy użyciu ustawienia aplikacji w formacie `AzureWebJobs.<FUNCTION_NAME>.Disabled`. To ustawienie aplikacji można utworzyć i zmodyfikować na wiele sposobów, w tym za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/) oraz z karty **zarządzanie** funkcją w [Azure Portal](https://portal.azure.com). 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure można użyć [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) polecenie, aby utworzyć i zmodyfikować ustawienie aplikacji. Następujące polecenie wyłącza funkcję o nazwie `QueueTrigger` przez utworzenie ustawienia aplikacji o nazwie `AzureWebJobs.QueueTrigger.Disabled` ustaw ją na `true`. 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

Aby ponownie włączyć funkcję, należy ponownie uruchomić to samo polecenie z wartością `false`.

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

### <a name="portal"></a>Portal

Można również użyć przełącznika **stanu funkcji** na karcie **Zarządzanie** funkcją. Przełącznik działa po utworzeniu i usunięciu ustawienia aplikacji `AzureWebJobs.<FUNCTION_NAME>.Disabled`.

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Functions 2. x C# — biblioteki klas

W bibliotece klas funkcji 2. x zalecamy użycie metody, która działa dla wszystkich języków. Ale jeśli wolisz, możesz [użyć atrybutu Disable jako w funkcjach 1. x](#functions-1x---c-class-libraries).

## <a name="functions-1x---scripting-languages"></a>Functions 1. x — Języki obsługi skryptów

W przypadku języków skryptów, C# takich jak skrypty i JavaScript, należy użyć właściwości `disabled` pliku *Function. JSON* , aby określić, że środowisko uruchomieniowe nie wyzwala funkcji. Dla tej właściwości można ustawić wartość `true` lub nazwę ustawienia aplikacji:

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

W drugim przykładzie funkcja jest wyłączona w przypadku ustawienia aplikacji o nazwie IS_DISABLED i jest ustawiona na `true` lub 1.

Można edytować plik w Azure Portal lub użyć przełącznika **stanu funkcji** na karcie **Zarządzanie** funkcją. Przełącznik portalu działa przez zmianę pliku *Function. JSON* .

![Przełącznik stanu funkcji](media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Functions 1. x C# — biblioteki klas

W bibliotece klas Functions 1. x należy użyć atrybutu `Disable`, aby zapobiec wyzwoleniu funkcji. Można użyć atrybutu bez konstruktora, jak pokazano w następującym przykładzie:

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
> Atrybut `Disabled` jest jedynym sposobem wyłączenia funkcji biblioteki klas. Nie można bezpośrednio edytować pliku generated *Function. JSON* dla funkcji biblioteki klas. Jeśli edytujesz ten plik, nie ma to żadnego wpływu na Właściwość `disabled`.
>
> Ta sama wartość dotyczy przełącznika **stanu funkcji** na karcie **Zarządzanie** , ponieważ działa przez zmianę pliku *Function. JSON* .
>
> Należy również pamiętać, że w portalu może wskazywać, że funkcja jest wyłączona, gdy nie jest.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano wyłączanie wyzwalaczy automatycznych. Aby uzyskać więcej informacji na temat wyzwalaczy, zobacz [wyzwalacze i powiązania](functions-triggers-bindings.md).
