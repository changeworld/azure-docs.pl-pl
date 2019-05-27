---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 5abefd55aa06f53bc3b437b29a2582b3e2239a65
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131614"
---
```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|routePrefix|interfejs API|Prefiks trasy, która ma zastosowanie do wszystkich tras. Użyj pustego ciągu, aby usunąć domyślny prefiks. |
|maxOutstandingRequests|200*|Maksymalna liczba oczekujących żądań, które są przechowywane w dowolnym momencie. Ten limit obejmuje żądań, które są umieszczane w kolejce, ale nie rozpoczęły wykonywania, a także w toku wykonań. Wszystkie przychodzące żądania przekracza ten limit są odrzucane przy użyciu odpowiedzi 429 "Zbyt zajęta". Która pozwala obiektom wywołującym mogą wykorzystać strategii ponawiania oparte na czasie i pomaga także kontrolować żądania maksymalnej wartości opóźnienia. Ta czynność reguluje tylko kolejkowania wykonywaną w ramach ścieżki wykonywania skryptu hosta. Nadal będą inne kolejek, takich jak kolejki żądań ASP.NET i to ustawienie nie ma wpływu. * Wartość domyślna dla wersji 1.x jest nieograniczona. Wartość domyślna dla wersji 2.x planu zużycie to 200. Wartość domyślna dla wersji 2.x w dedykowanego planu jest nieograniczona.|
|maxConcurrentRequests|100 *|Maksymalna liczba funkcje http, które będą wykonywane równolegle. Dzięki temu można kontroli współbieżności, która może ułatwić zarządzanie wykorzystanie zasobów. Na przykład Niewykluczone, że funkcja protokołu http, która używa dużej ilości zasobów systemowych (gniazda pamięci/procesora) w taki sposób, że powoduje problemy, gdy współbieżność jest zbyt wysoka. Lub może być funkcją, która sprawia, że żądania wychodzące do usługi innej firmy, a te wywołania trzeba częstość jest ograniczona. W takich przypadkach zastosowanie ograniczania w tym miejscu może pomóc. * Wartość domyślna dla wersji 1.x jest nieograniczona. Wartość domyślna dla wersji 2.x planu zużycie wynosi 100. Wartość domyślna dla wersji 2.x w dedykowanego planu jest nieograniczona.|
|dynamicThrottlesEnabled|true*|Po włączeniu, będzie to ustawienie powoduje potoku przetwarzania żądań mogą okresowo sprawdzać wydajność systemu liczników, takich jak połączenia/wątki/procesów/pamięci/procesora cpu/itd. oraz żądań w przypadku spełnienia dowolnego z tych liczników powyżej wartości progowej wysokiej wbudowane (80%) odrzucone przy użyciu odpowiedzi 429 "Jest zbyt zajęty", dopóki Counter (s) powrócić do normalnego poziomu. * Wartość domyślna dla wersji 1.x ma wartość false. Wartość domyślna dla wersji 2.x planu zużycie ma wartość true. Wartość domyślna dla wersji 2.x w dedykowanego planu ma wartość false.|
