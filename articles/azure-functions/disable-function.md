---
title: Jak wyłączyć funkcje w usłudze Azure Functions
description: Dowiedz się, jak wyłączyć lub włączyć funkcje w usłudze Azure Functions w wersji 1.x i 2.x.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
origin.date: 07/24/2018
ms.date: 08/31/2018
ms.author: v-junlch
ms.openlocfilehash: a32b4815a2716428ceeec034ddc5589e3aa062e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710581"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Jak wyłączyć funkcje w usłudze Azure Functions

W tym artykule wyjaśniono, jak wyłączyć funkcję w usłudze Azure Functions. Aby *wyłączyć* funkcji oznacza, że należy zignorować automatyczny wyzwalacz, który jest zdefiniowany dla funkcji środowiska uruchomieniowego. Sposób, możesz zrobić to zależy od wersji środowiska uruchomieniowego i języka programowania:

- Functions w wersji 1.x
  - Języki skryptów
  - Biblioteki klas C#
- Functions w wersji 2.x
  - Jednym ze sposobów dla wszystkich języków
  - Rozwiązanie opcjonalne dla bibliotek klas języka C#

## <a name="functions-1x---scripting-languages"></a>Funkcje 1.x - języków skryptów

W językach skryptów, takich jak skrypt języka C# i JavaScript, należy użyć `disabled` właściwość *function.json* plik, aby sprawdzić środowisko wykonawcze nie w celu wyzwolenia funkcji. Ta właściwość może być równa `true` lub nazwa ustawienia aplikacji:

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

W drugim przykładzie funkcja jest wyłączona, gdy istnieje ustawienie aplikacji o nazwie IS_DISABLED i ustawiono `true` lub 1.

Możesz edytować plik w witrynie Azure portal lub użyj **stan funkcji** przełącznika funkcji **Zarządzaj** kartę. Portal przełącznika działa, zmieniając *function.json* pliku.

![Przełącz stan — funkcja](./media/disable-function/function-state-switch.png)

## <a name="functions-1x---c-class-libraries"></a>Funkcje 1.x — bibliotek klas języka C#

W bibliotece klas 1.x funkcji używasz `Disable` atrybutu, aby uniemożliwić wyzwalane przez funkcję. Można użyć atrybutu bez parametru konstruktora, jak pokazano w poniższym przykładzie:

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

Ten atrybut nie parametrze Konstruktor wymaga ponownie skompilować i ponownie wdrożyć projekt, aby zmienić stan wyłączenia funkcji. Jest bardziej elastyczny sposób użyć atrybutu obejmujący parametr konstruktora, która odwołuje się do ustawienia logiczne aplikacji, jak pokazano w poniższym przykładzie:

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

Ta metoda umożliwia włączanie i wyłączanie funkcji, zmieniając ustawienia aplikacji bez konieczności ponownego kompilowania lub ponownego wdrażania. Zmiana ustawienia aplikacji powoduje, że aplikację funkcji, aby ponownie uruchomić, dzięki czemu zmiany w stanie wyłączenia rozpoznano natychmiast.

> [!IMPORTANT]
> `Disabled` Atrybut jest jedynym sposobem, aby wyłączyć funkcję biblioteki klas. Wygenerowany *function.json* plików dla funkcji biblioteki klasy nie jest przeznaczona do można edytować bezpośrednio. Jeśli edytujesz plik, niezależnie od rodzaju możesz zrobić, aby `disabled` właściwość odniesie żadnego skutku.
>
> To samo dotyczy **funkcji stan** Włącz **Zarządzaj** karcie, ponieważ działa, zmieniając *function.json* pliku.
>
> Należy również zauważyć, że portal może wskazywać, że funkcja jest wyłączona, gdy nie jest.



## <a name="functions-2x---all-languages"></a>Funkcje 2.x — wszystkie języki

W przypadku funkcji 2.x wyłączyć funkcję za pomocą ustawienia aplikacji. Na przykład, aby wyłączyć funkcję o nazwie `QueueTrigger`, utworzyć ustawienie aplikacji o nazwie `AzureWebJobs.QueueTrigger.Disabled`i ustaw ją na `true`. Aby włączyć funkcję, w ustawieniu aplikacji `false`. Można również użyć **stan funkcji** przełącznika funkcji **Zarządzaj** kartę. Przełącznik polega na tworzenie i usuwanie `AzureWebJobs.<functionname>.Disabled` ustawienia aplikacji.

![Przełącz stan — funkcja](./media/disable-function/function-state-switch.png)

## <a name="functions-2x---c-class-libraries"></a>Funkcje 2.x - bibliotek klas języka C#

W bibliotece klas 2.x funkcji firma Microsoft zaleca używanie metody, która działa dla wszystkich języków. Jeśli wolisz, możesz to zrobić, ale [użyć atrybutu wyłączyć funkcje w 1.x](#functions-1x---c-class-libraries).

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł dotyczy wyłączania automatycznych wyzwalaczy. Aby uzyskać więcej informacji na temat wyzwalaczy zobacz [wyzwalaczy i powiązań](functions-triggers-bindings.md).

