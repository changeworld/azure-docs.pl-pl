---
title: Omówienie rozszerzenia Durable Functions — Azure
description: Wprowadzenie do rozszerzenia Durable Functions dla usługi Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 12/22/2018
ms.author: glenga
ms.reviewer: azfuncdf
ms.openlocfilehash: 6e1acf5f605d7f6fb42d24e6e7ec624a317b0e31
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612863"
---
# <a name="what-are-durable-functions"></a>Co to jest Durable Functions?

*Durable Functions* to rozszerzenie usługi [Azure Functions](../functions-overview.md) umożliwiające zapisywanie funkcji stanowych w środowisku bezserwerowym. Rozszerzenie zarządza stanem, punktami kontrolnymi i ponownym uruchamianiem.

## <a name="benefits"></a>Korzyści

To rozszerzenie umożliwia definiowanie stanowych przepływów pracy za pomocą [*funkcji orkiestratora*](durable-functions-types-features-overview.md#orchestrator-functions), co zapewnia następujące korzyści:

* Możliwość definiowania przepływów pracy w kodzie. Brak konieczności korzystania z projektantów i schematów JSON.
* Możliwość synchronicznego i asynchronicznego wywoływania innych funkcji. Możliwość zapisywania danych wyjściowych z wywołanych funkcji w zmiennych lokalnych.
* Automatyczne tworzenie punktów kontrolnych postępu podczas oczekiwania funkcji. Możliwość odtwarzania procesu lub ponownego uruchamiania maszyny wirtualnej bez utraty stanu lokalnego.

## <a name="application-patterns"></a>Wzorce aplikacji

Podstawowym zastosowaniem rozszerzenia Durable Functions jest uproszczenie złożonych wymagań związanych z koordynacją stanową w aplikacjach bezserwerowych. Poniżej przedstawiono wybrane typowe wzorce aplikacji, w przypadku których korzystne może być zastosowanie rozszerzenia Durable Functions:

* [Łączenie w łańcuchy](durable-functions-concepts.md#chaining)
* [Model fan-out/fan-in](durable-functions-concepts.md#fan-in-out)
* [Interfejsy API protokołu HTTP Async](durable-functions-concepts.md#async-http)
* [Monitorowanie](durable-functions-concepts.md#monitoring)
* [Interakcja z użytkownikami](durable-functions-concepts.md#human)

## <a name="language-support"></a>Obsługiwane języki

Rozszerzenie Durable Functions obsługuje obecnie następujące języki:

* **C#** : zarówno [prekompilowane biblioteki klas](../functions-dotnet-class-library.md), jak i [skrypt języka C#](../functions-reference-csharp.md).
* **F#** : prekompilowane biblioteki klas i skrypt języka F#. Skrypt języka F# jest obsługiwany tylko w przypadku wersji 1.x środowiska uruchomieniowego usługi Azure Functions.
* **JavaScript**: obsługiwany tylko w przypadku wersji 2.x środowiska uruchomieniowego usługi Azure Functions. Wymaga rozszerzenia Durable Functions w wersji 1.7.0 lub nowszej. 

Docelowo rozszerzenie Durable Functions ma obsługiwać wszystkie [języki obsługiwane w usłudze Azure Functions](../supported-languages.md). Zobacz [listę problemów z rozszerzeniem Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues), aby poznać aktualny stan prac nad obsługą dodatkowych języków.

Np. usługi Azure Functions są szablony, które pomogą Ci tworzyć niezawodne funkcje przy użyciu [Visual Studio 2019](durable-functions-create-first-csharp.md), [programu Visual Studio Code](quickstart-js-vscode.md)i [witryny Azure portal](durable-functions-create-portal.md).

## <a name="billing"></a>Rozliczenia

Opłaty za rozszerzenie Durable Functions są naliczane tak samo, jak w przypadku usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="jump-right-in"></a>Błyskawicznie rozpocznij pracę

Ukończ jeden z tych samouczków Szybki start dotyczących poszczególnych języków, aby rozpocząć korzystanie z rozszerzenia Durable Functions w niecałe 10 minut:

* [C# using Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript w programie Visual Studio Code](quickstart-js-vscode.md)

W obu przewodnikach Szybki start utworzysz lokalnie i przetestujesz funkcję trwałą „hello world”. Kod funkcji zostanie następnie opublikowany na platformie Azure. Utworzona przez Ciebie funkcja aranżuje i łączy w łańcuchy wywołania do innych funkcji.

## <a name="learn-more"></a>Dowiedz się więcej

Poniższy klip wideo prezentuje zalety rozszerzenia Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Ponieważ Durable Functions to zaawansowane rozszerzenie usługi [Azure Functions](../functions-overview.md), nie jest odpowiednie dla wszystkich aplikacji. Aby dowiedzieć się więcej na temat rozszerzenia Durable Functions, zobacz [Durable Functions patterns and technical concepts (Durable Functions — wzorce i zagadnienia techniczne)](durable-functions-concepts.md). Aby zapoznać się z porównaniem technologii orkiestracji dostępnych na platformie Azure, zobacz [Porównanie usług Azure Functions i Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Durable Functions patterns and technical concepts (Durable Functions — wzorce i zagadnienia techniczne)](durable-functions-concepts.md)
