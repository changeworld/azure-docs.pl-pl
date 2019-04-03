---
title: Wskazówki dotyczące projektowania usługi Azure Functions | Dokumentacja firmy Microsoft
description: Poznaj pojęcia dotyczące usługi Azure Functions i technik, które są potrzebne do opracowania funkcji na platformie Azure we wszystkich językach programowania i powiązania.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Funkcje przewodnik platformy azure dla deweloperów, funkcje, przetwarzanie zdarzeń, elementy webhook, obliczanie dynamiczne, architektura bez serwera
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 01fce9813299465767bdaed7f3c2939813cfab87
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "56339497"
---
# <a name="azure-functions-developers-guide"></a>Przewodnik dla deweloperów usługi Azure Functions
W usłudze Azure Functions określonych funkcji udostępniania kilka podstawowych pojęć technicznych i składniki, niezależnie od języka lub powiązania, którego używasz. Zanim przejdziesz do nauki szczegóły specyficzne dla danego języka lub powiązania, koniecznie zapoznaj się z tego omówienia, która ma zastosowanie do wszystkich z nich.

W tym artykule założono, że zostały już przeczytane [omówienia usługi Azure Functions](functions-overview.md).

## <a name="function-code"></a>Kod — funkcja
A *funkcja* to podstawowe pojęcie w usłudze Azure Functions. Funkcja zawiera dwie części ważne — kodu, które mogą być napisane w różnych językach i niektórych konfiguracji, plik function.json. Językach kompilowanych ten plik konfiguracji jest generowana automatycznie z adnotacjami w kodzie. W językach skryptów musisz podać w pliku config samodzielnie.

Plik function.json definiuje wyzwalacza funkcji, powiązania i inne ustawienia konfiguracji. Każda funkcja ma jeden i tylko jeden wyzwalacz. Środowisko wykonawcze używa tego pliku konfiguracji, aby określić zdarzenia do monitorowania oraz sposób przekazywania danych do i zwrócić dane z wykonywania funkcji. Oto przykładowy plik function.json.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

`bindings` Właściwość jest którym skonfigurować zarówno wyzwalaczy i powiązań. Każde powiązanie udostępnia kilka typowych ustawień i niektóre ustawienia, które są specyficzne dla danego typu powiązania. Każde powiązanie wymaga następujących ustawień:

| Właściwość | Wartości/typy | Komentarze |
| --- | --- | --- |
| `type` |string |Typ powiązania. Na przykład `queueTrigger`. |
| `direction` |"in" "out" |Wskazuje, czy powiązanie jest dla odbierania danych do funkcji lub wysyłanie danych przez funkcję. |
| `name` |string |Nazwa która jest używana do powiązanych danych w funkcji. Dla języka C# jest to nazwa argumentu; dla języka JavaScript to klucz na liście kluczy/wartości. |

## <a name="function-app"></a>Aplikacja funkcji
Aplikacja funkcji zapewnia kontekst wykonania na platformie Azure, w którym funkcje są uruchomione. Aplikacja funkcji składa się z co najmniej jeden poszczególnych funkcji, które są zarządzane, wdrożyć i skalowane razem. Wszystkie funkcje w aplikacji funkcji Udostępnianie tego samego planu cenowego i ciągłe Wdrażanie wersji środowiska uruchomieniowego. Aplikacja funkcji można traktować jako sposób organizowania i zbiorczo zarządzania funkcjami. 

> [!NOTE]
> Wszystkie funkcje w aplikacji funkcji musi zostać utworzona w tym samym języku. W [poprzednie wersje](functions-versions.md) środowiska uruchomieniowego usługi Azure Functions, nie był to wymagane.

## <a name="folder-structure"></a>Struktura folderów
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Powyżej jest ustawieniem domyślnym (i zalecana) struktura folderów dla aplikacji funkcji. Jeśli chcesz zmienić lokalizację plików kodu funkcji, zmodyfikuj `scriptFile` części _function.json_ pliku. Zalecamy również przy użyciu [pakiet wdrażania](deployment-zip-push.md) do wdrażania projektu do aplikacji funkcji na platformie Azure. Można również użyć istniejących narzędzi, takich jak [ciągłej integracji i ciągłego wdrażania](functions-continuous-deployment.md) i DevOps platformy Azure.

> [!NOTE]
> Jeśli wdrożenie pakietu ręcznie, upewnij się, że wdrażanie Twojej _host.json_ plików i funkcja folderów bezpośrednio do `wwwroot` folderu. Nie dołączaj `wwwroot` folderu we wdrożeniach. W przeciwnym razie na końcu `wwwroot\wwwroot` folderów.

#### <a name="use-local-tools-and-publishing"></a>Korzystaj z narzędzi lokalnych i publikowania
Aplikacje funkcji można tworzyć i publikować przy użyciu różnych narzędzi, takich jak [programu Visual Studio](./functions-develop-vs.md), [programu Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [środowiskaEclipse](./functions-create-maven-eclipse.md)i [narzędzia Core usługi Azure Functions](./functions-develop-local.md). Aby uzyskać więcej informacji, zobacz [kodu i testowanie usługi Azure Functions lokalnie](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Jak edytować funkcji w witrynie Azure portal
Edytor funkcji wbudowanych w witrynie Azure portal umożliwia aktualizowania kodu i *function.json* bezpośrednio pliku wbudowanego. Jest to zalecane tylko w przypadku niewielkich zmian lub dowody koncepcji — najlepszym rozwiązaniem jest użycie narzędzia rozwoju lokalnego, takiego jak program VS Code.

## <a name="parallel-execution"></a>Wykonywanie równoległe
Gdy wiele wyzwalająca zdarzenia występują szybciej niż jednowątkowe funkcji środowiska uruchomieniowego mogły je przetwarzać, środowisko uruchomieniowe może wywołania funkcji wiele razy w sposób równoległy.  Jeśli aplikacja funkcji używa [planu hostingu zużycie](functions-scale.md#how-the-consumption-plan-works), aplikacja funkcji może automatycznego skalowania w poziomie.  Każde wystąpienie aplikacji funkcji, czy aplikacja jest uruchamiana na zużycie hosting plan lub wyrażenie [usługi App Service plan hostingu](../app-service/overview-hosting-plans.md), może przetwarzać wywołania funkcji współbieżnych równolegle przy użyciu wielu wątków.  Maksymalna liczba wywołań funkcji współbieżnych w każdym wystąpieniu aplikacji funkcji zależy od typu wyzwalacza są używane, a także zasoby używane przez inne funkcje w ramach aplikacji funkcji.

## <a name="functions-runtime-versioning"></a>Przechowywanie wersji środowiska uruchomieniowego funkcji

Można skonfigurować wersję przy użyciu środowiska uruchomieniowego funkcji `FUNCTIONS_EXTENSION_VERSION` ustawienia aplikacji. Na przykład wartość "~ 2" wskazuje, czy aplikacja funkcji będzie używać 2.x jako jego wersji głównej. Aplikacje funkcji są uaktualniane do każda nowa wersja pomocnicza, po ich wydaniu. Aby uzyskać więcej informacji, jak wyświetlić dokładną wersję aplikacji funkcji, w tym temacie [sposobu kierowania wersje środowiska uruchomieniowego usługi Azure Functions](set-runtime-version.md).

## <a name="repositories"></a>Repozytoria
Kod dla usługi Azure Functions jest typu open source i przechowywane w repozytoriach usługi GitHub:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Hosta funkcji platformy Azure](https://github.com/Azure/azure-functions-host/)
* [Portal usługi Azure Functions](https://github.com/azure/azure-functions-ux)
* [Szablony usługi Azure Functions](https://github.com/azure/azure-functions-templates)
* [Zestawu Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Rozszerzenia zestawu Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Powiązania
W tym miejscu znajduje się tabela wszystkie obsługiwane powiązania.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Masz problemy z błędami pochodzące z powiązaniami? Przegląd [kody błędów powiązania funkcji Azure](functions-bindings-error-pages.md) dokumentacji.

## <a name="reporting-issues"></a>Raportowanie problemów
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Kolejne kroki
Więcej informacji zawierają następujące zasoby:

* [Wyzwalacze w usłudze Azure Functions i powiązania](functions-triggers-bindings.md)
* [Kodowanie i testowanie usługi Azure Functions lokalnie](./functions-develop-local.md)
* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Usługa Azure Functions dla deweloperów odwołanie w C#](functions-reference-csharp.md)
* [Dokumentacja dla deweloperów w usłudze Azure Functions NodeJS](functions-reference-node.md)
