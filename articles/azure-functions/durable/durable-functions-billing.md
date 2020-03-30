---
title: Rozliczanie trwałych funkcji — funkcje platformy Azure
description: Dowiedz się więcej o wewnętrznych zachowaniach funkcji trwałych i ich wpływ na rozliczenia dla usług Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 504ef93a0002895bc5662d95ad269c8593170ee2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74233009"
---
# <a name="durable-functions-billing"></a>Rozliczanie trwałych funkcji

[Funkcje trwałe](durable-functions-overview.md) są rozliczane w taki sam sposób jak usługi Azure Functions. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Podczas wykonywania funkcji programu orchestrator w [planie użycia](../functions-scale.md#consumption-plan)usług Azure Functions, należy pamiętać o niektórych zachowaniach rozliczeń. W poniższych sekcjach opisano te zachowania i ich wpływ bardziej szczegółowo.

## <a name="orchestrator-function-replay-billing"></a>Rozliczanie powtórek funkcji orkiestry

[Funkcje koordynatora](durable-functions-orchestrations.md) może odtwarzać kilka razy w ciągu okresu istnienia aranżacji. Każda powtórka jest postrzegana przez środowisko uruchomieniowe usługi Azure Functions jako wywołanie funkcji różnych. Z tego powodu w planie zużycia usług Azure Functions są rozliczane dla każdej powtórki funkcji koordynatora. Inne typy planu nie pobierają opłat za odtwarzanie funkcji koordynatora.

## <a name="awaiting-and-yielding-in-orchestrator-functions"></a>Oczekiwanie i ustępowanie w funkcjach orkiestratora

Gdy funkcja orchestrator czeka na akcję asynchroniczną, aby zakończyć przy użyciu **await** w języku C# lub **wydajność** w języku JavaScript, środowisko wykonawcze uważa, że określone wykonanie do ukończenia. Rozliczenia za funkcję koordynatora zatrzymuje się w tym momencie. Nie zostanie wznowiona do następnej powtórki funkcji orkiestry. Nie są rozliczane za czas spędzony w oczekiwaniu lub plonowanie w funkcji orkiestratora.

> [!NOTE]
> Funkcje wywołujące inne funkcje są uważane przez niektórych za wzortern. Wynika to z problemu znanego jako _podwójne rozliczenie_. Gdy funkcja wywołuje inną funkcję bezpośrednio, oba są uruchamiane w tym samym czasie. Wywoływana funkcja aktywnie uruchamia kod, podczas gdy funkcja wywołująca oczekuje na odpowiedź. W takim przypadku należy zapłacić za czas, który funkcja wywołująca spędza czekając na uruchomienie wywoływaną funkcję.
>
> Nie ma podwójnego fakturowania w funkcjach orkiestratora. Rozliczenia funkcji aranżatora zatrzymuje się, gdy czeka na wynik funkcji działania lub podaranżacji.

## <a name="durable-http-polling"></a>Trwałe sondowanie HTTP

Funkcje programu Orchestrator mogą wykonywać długotrwałe wywołania HTTP do zewnętrznych punktów końcowych zgodnie z opisem w [artykule o funkcjach HTTP](durable-functions-http-features.md). Wywołanie **metodyHttpAsync** w języku C# i **wywołanieHttp** metoda w języku JavaScript może wewnętrznie sondowania punktu końcowego HTTP podczas po [asynchronicznego wzorca 202](durable-functions-http-features.md#http-202-handling).

Obecnie nie ma bezpośredniego fakturowania dla wewnętrznych operacji sondowania HTTP. Jednak wewnętrzne sondowanie może spowodować, że funkcja koordynatora będzie okresowo powtarzać. Opłaty standardowe będą naliczane za te powtórki funkcji wewnętrznych.

## <a name="azure-storage-transactions"></a>Transakcje usługi Azure Storage

Funkcje trwałe domyślnie używa usługi Azure Storage, aby zachować stan trwałe, komunikaty procesu i zarządzać partycjami za pośrednictwem dzierżaw obiektów blob. Ponieważ jesteś właścicielem tego konta magazynu, wszelkie koszty transakcji są rozliczane do subskrypcji platformy Azure. Aby uzyskać więcej informacji na temat artefaktów usługi Azure Storage używanych przez funkcje trwałe, zobacz [centrum zadań w artykule](durable-functions-task-hubs.md).

Kilka czynników przyczynia się do rzeczywistych kosztów usługi Azure Storage poniesionych przez aplikację Funkcje trwałe:

* Aplikacja z jedną funkcją jest skojarzona z centrum pojedynczych zadań, które współudziało w zestawie zasobów usługi Azure Storage. Te zasoby są używane przez wszystkie funkcje trwałe w aplikacji funkcji. Rzeczywista liczba funkcji w aplikacji funkcji nie ma wpływu na koszty transakcji usługi Azure Storage.
* Każde wystąpienie aplikacji funkcji wewnętrznie sonduje wiele kolejek na koncie magazynu przy użyciu algorytmu sondowania wykładniczego-backoff. Bezczynne wystąpienie aplikacji sonduje kolejki rzadziej niż aktywna aplikacja, co powoduje mniejsze koszty transakcji. Aby uzyskać więcej informacji na temat zachowania sondowania kolejki funkcji trwałych, zobacz [sekcję sondowania kolejek w artykule Wydajność i skalowanie](durable-functions-perf-and-scale.md#queue-polling).
* Podczas uruchamiania w planach zużycie usług Azure Functions lub Premium [kontroler skalowania usług Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) regularnie sonduje wszystkie kolejki centrum zadań w tle. Jeśli aplikacja funkcji jest w skali od lekkiej do umiarkowanej, tylko wystąpienie kontrolera pojedynczej skali sonduje te kolejki. Jeśli aplikacja funkcji skaluje się w poziomie do dużej liczby wystąpień, można dodać więcej wystąpień kontrolera skalowania. Te dodatkowe wystąpienia kontrolera skalowania można zwiększyć całkowite koszty transakcji kolejki.
* Każde wystąpienie aplikacji funkcji konkuruje o zestaw dzierżaw obiektów blob. Te wystąpienia będą okresowo wykonywać wywołania usługi Azure Blob albo odnowić dzierżawy przechowywane lub próby uzyskania nowych dzierżaw. Liczba partycji skonfigurowanych w centrum zadań określa liczbę dzierżaw obiektów blob. Skalowanie w poziomie większej liczby wystąpień aplikacji funkcji prawdopodobnie zwiększa koszty transakcji usługi Azure Storage skojarzone z tymi operacjami dzierżawy.

Więcej informacji na temat cen usługi Azure Storage można znaleźć w dokumentacji [cenowej usługi Azure Storage.](https://azure.microsoft.com/pricing/details/storage/) 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o cenach usługi Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
