---
title: Rozliczenia funkcji trwałych — Azure Functions
description: Zapoznaj się z wewnętrznymi zachowaniami funkcji trwałych i wpływem rozliczeń na Azure Functions.
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: b79d50adf932bd5c316fbda9d0964eea7c0484ca
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935887"
---
# <a name="durable-functions-billing"></a>Rozliczanie Durable Functions

[Durable Functions](durable-functions-overview.md) są rozliczane tak samo jak Azure Functions. Aby uzyskać więcej informacji, zobacz [cennika usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Podczas wykonywania funkcji programu Orchestrator w [planie zużycia](../functions-scale.md#consumption-plan)Azure Functions należy pamiętać o rozliczeniach. W poniższych sekcjach opisano te zachowania i ich wpływ na bardziej szczegółowy.

## <a name="orchestrator-function-replay-billing"></a>Rozliczanie powtarzania funkcji programu Orchestrator

[Funkcje programu Orchestrator](durable-functions-orchestrations.md) mogą wielokrotnie powtarzać się w okresie istnienia aranżacji. Każde powtórzenie jest wyświetlane przez środowisko uruchomieniowe Azure Functions jako odrębne wywołanie funkcji. Z tego powodu w planie zużycia Azure Functions naliczane są opłaty za każde odtwarzanie funkcji programu Orchestrator. Inne typy planów nie będą naliczane opłaty za odtwarzanie funkcji programu Orchestrator.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Oczekiwanie na funkcje programu Orchestrator i ich uzyskanie

Gdy funkcja programu Orchestrator czeka na wykonanie akcji asynchronicznej za pomocą `await` (C#) lub `yield` (JavaScript), środowisko uruchomieniowe uważa, że określone wykonanie zostało ukończone. Rozliczanie dla funkcji programu Orchestrator zostaje zatrzymane i nie zostanie wznowione do momentu ponownego uruchomienia następnej funkcji programu Orchestrator. Nie są naliczane opłaty za każdy czas spędzony na czekaniu lub w funkcji programu Orchestrator.

> [!NOTE]
> Funkcje wywołujące inne funkcje są uważane przez część jako antywzorców. Wynika to z faktu, że występuje problem znany jako _podwójne rozliczenia_. Gdy funkcja wywołuje inną funkcję bezpośrednio, oba są wykonywane w tym samym czasie. Wywoływany program aktywnie wykonuje kod, podczas gdy obiekt wywołujący oczekuje na odpowiedź. W takim przypadku należy zapłacić za czas, przez który obiekt wywołujący oczekuje na wykonanie wywoływanego elementu. Funkcje programu Orchestrator nie poniosły tego podwójnego rozliczania, ponieważ rozliczenia funkcji programu Orchestrator zatrzymają się podczas oczekiwania na wynik funkcji działania (lub aranżacji podrzędnej).

## <a name="durable-http-polling"></a>Trwałe sondowanie HTTP

Funkcje programu Orchestrator mogą wykonywać długotrwałe wywołania HTTP do zewnętrznych punktów końcowych, zgodnie z opisem w artykule [funkcje http](durable-functions-http-features.md) . Metoda (C#) i `callHttp` Metoda (JavaScript) mogą wewnętrznie sondować punkt końcowy protokołu HTTP w przypadku [wzorca asynchronicznego 202](durable-functions-http-features.md#http-202-handling). `CallHttpAsync` Obecnie nie są naliczane żadne bezpośrednie rozliczenia dla wewnętrznych operacji sondowania HTTP. Jednak wewnętrzne sondowanie może spowodować okresowe powtarzanie funkcji programu Orchestrator, a opłaty będą naliczane za korzystanie z tej funkcji wewnętrznej.

## <a name="azure-storage-transactions"></a>Transakcje usługi Azure Storage

Durable Functions domyślnie korzysta z usługi Azure Storage, aby utrwalać stan, przetwarzać komunikaty i zarządzać partycjami za pośrednictwem dzierżaw obiektów BLOB. To konto magazynu należy do użytkownika, więc opłaty za transakcje są naliczane w ramach subskrypcji platformy Azure. Aby uzyskać więcej informacji na temat artefaktów usługi Azure Storage używanych przez Durable Functions, zobacz artykuł dotyczący [centrów zadań](durable-functions-task-hubs.md) .

Niektóre czynniki przyczyniają się do rzeczywistych kosztów usługi Azure Storage ponoszonych przez aplikację Durable Functions.

* Pojedyncza aplikacja funkcji jest skojarzona z jednym centrum zadań, który udostępnia zestaw zasobów usługi Azure Storage. Te zasoby są używane przez wszystkie funkcje trwałe w aplikacji funkcji. Rzeczywista liczba funkcji w aplikacji funkcji nie ma wpływu na koszty transakcji usługi Azure Storage.
* Każde wystąpienie aplikacji funkcji wewnętrznie sonduje wiele kolejek na koncie magazynu przy użyciu algorytmu sondowania wykładniczego wycofywania. Wystąpienie aplikacji bezczynnej sonduje kolejki rzadziej niż w przypadku aktywnej aplikacji, co spowoduje zmniejszenie kosztów transakcji. Aby uzyskać więcej informacji na temat zachowania sondowania kolejki Durable Functions, zobacz [sekcję sondowanie kolejki w artykule dotyczącym wydajności i skalowania](durable-functions-perf-and-scale.md#queue-polling) .
* Po uruchomieniu w planach użycia Azure Functions lub Premium [kontroler skalowania Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) regularnie sonduje wszystkie kolejki centrum zadań w tle. W jasnym i średnim skalowaniu tylko pojedyncze wystąpienie kontrolera skalowania będzie sondował te kolejki. Jeśli aplikacja funkcji jest skalowana do dużej liczby wystąpień, można dodać więcej wystąpień kontrolera skalowania. Te dodatkowe wystąpienia kontrolera skalowania mogą zwiększyć łączną liczbę kosztów transakcji kolejki.
* Każde wystąpienie aplikacji funkcji konkuruje z zestawem dzierżaw obiektów BLOB. Te wystąpienia będą okresowo wykonywać wywołania do Blob service platformy Azure, aby odnowić dzierżawy w trakcie odnawiania, próbując uzyskać nowe dzierżawy. Liczba dzierżaw obiektów BLOB jest określana przez skonfigurowaną liczbę partycji centrum zadań. Skalowanie w górę do większej liczby wystąpień aplikacji funkcji prawdopodobnie spowoduje zwiększenie kosztów transakcji usługi Azure Storage skojarzonych z tymi operacjami dzierżawy.

Więcej informacji na temat cennika usługi Azure Storage można znaleźć w dokumentacji [cennika usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Azure Functions cenach](https://azure.microsoft.com/pricing/details/functions/)
