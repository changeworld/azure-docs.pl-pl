---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: ec825bb59840784ca45e8a38caa664c19883fb32
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263328"
---
|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|routePrefix|api|Prefiks trasy dotyczący wszystkich tras. Użyj pustego ciągu, aby usunąć domyślny prefiks. |
|maxOutstandingRequests|200<sup>*</sup>|Maksymalna liczba oczekujących żądań, które są przechowywane w danym momencie. Ten limit obejmuje żądania, które są umieszczane w kolejce, ale nie rozpoczęto wykonywania, a także w trakcie wykonywania. Wszystkie żądania przychodzące przez ten limit są odrzucane przez odpowiedź 429 "zbyt zajęta". Dzięki temu obiekty wywołujące mogą korzystać z strategii ponawiania prób, a także kontrolować maksymalne opóźnienia żądania. Tylko kontroluje kolejkowanie, które występuje w ścieżce wykonywania hosta skryptu. Inne kolejki, takie jak Kolejka żądań ASP.NET, nadal będą obowiązywać i nie mają wpływu na to ustawienie. <sup>*</sup> Wartość domyślna dla wersji 1. x jest niepowiązana (`-1`). Wartością domyślną dla wersji 2. x w planie zużycia jest 200. Wartość domyślna dla wersji 2. x w dedykowanym planie jest nieograniczona (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Maksymalna liczba funkcji http, które będą wykonywane równolegle. Pozwala to na kontrolowanie współbieżności, co może ułatwić zarządzanie użyciem zasobów. Na przykład może istnieć funkcja http, która korzysta z dużej ilości zasobów systemowych (pamięć/procesor CPU/gniazda), co powoduje problemy, gdy współbieżność jest zbyt wysoka. Może też istnieć funkcja, która wysyła żądania wychodzące do usługi innej firmy, a te wywołania muszą mieć ograniczoną szybkość. W takich przypadkach można w tym celu zastosować ograniczenie przepustowości. <sup>*</sup> Wartość domyślna dla wersji 1. x jest niepowiązana (`-1`). Wartością domyślną dla wersji 2. x w planie zużycia jest 100. Wartość domyślna dla wersji 2. x w dedykowanym planie jest nieograniczona (`-1`).|
|dynamicThrottlesEnabled|true<sup>*</sup>|Włączenie tego ustawienia powoduje, że potok przetwarzania żądań okresowo sprawdza liczniki wydajności systemu, takie jak połączenia/wątki/procesy/pamięć/procesor CPU/itp. Jeśli którykolwiek z tych liczników korzysta ze wstępnie zdefiniowanego progu górnego (80%), żądania będą odrzucono z odpowiedzią na 429 "zbyt zajęte" do momentu powrotu liczników do normalnych poziomów. <sup>*</sup> Wartość domyślna dla wersji 1. x ma wartość false. Wartość domyślna dla wersji 2. x w planie zużycia jest prawdziwa. Wartość domyślna dla wersji 2. x w dedykowanym planie ma wartość false.|
