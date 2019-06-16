---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 35b087d13099b975a1c9c6d2dbd449935f5f0d1d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66249077"
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
|routePrefix|api|Prefiks trasy, która ma zastosowanie do wszystkich tras. Użyj pustego ciągu, aby usunąć domyślny prefiks. |
|maxOutstandingRequests|200<sup>*</sup>|Maksymalna liczba oczekujących żądań, które są przechowywane w dowolnym momencie. Ten limit obejmuje żądań, które są umieszczane w kolejce, ale nie rozpoczęły wykonywania, a także w toku wykonań. Wszystkie przychodzące żądania przekracza ten limit są odrzucane przy użyciu odpowiedzi 429 "Zbyt zajęta". Która pozwala obiektom wywołującym mogą wykorzystać strategii ponawiania oparte na czasie i pomaga także kontrolować żądania maksymalnej wartości opóźnienia. Ta czynność reguluje tylko kolejkowania wykonywaną w ramach ścieżki wykonywania skryptu hosta. Nadal będą inne kolejek, takich jak kolejki żądań ASP.NET i to ustawienie nie ma wpływu. <sup>*</sup>Wartość domyślna dla wersji 1.x jest nieograniczona (`-1`). Wartość domyślna dla wersji 2.x planu zużycie to 200. Wartość domyślna dla wersji 2.x w dedykowanego planu jest nieograniczona (`-1`).|
|maxConcurrentRequests|100<sup>*</sup>|Maksymalna liczba funkcje http, które będą wykonywane równolegle. Dzięki temu można kontroli współbieżności, która może ułatwić zarządzanie wykorzystanie zasobów. Na przykład Niewykluczone, że funkcja protokołu http, która używa dużej ilości zasobów systemowych (gniazda pamięci/procesora) w taki sposób, że powoduje problemy, gdy współbieżność jest zbyt wysoka. Lub może być funkcją, która sprawia, że żądania wychodzące do usługi innej firmy, a te wywołania trzeba częstość jest ograniczona. W takich przypadkach zastosowanie ograniczania w tym miejscu może pomóc. <sup>*</sup>Wartość domyślna dla wersji 1.x jest nieograniczona (`-1`). Wartość domyślna dla wersji 2.x planu zużycie wynosi 100. Wartość domyślna dla wersji 2.x w dedykowanego planu jest nieograniczona (`-1`).|
|dynamicThrottlesEnabled|Wartość true<sup>*</sup>|Po włączeniu, będzie to ustawienie powoduje potoku przetwarzania żądań mogą okresowo sprawdzać wydajność systemu liczników, takich jak połączenia/wątki/procesów/pamięci/procesora cpu/itd. oraz żądań w przypadku spełnienia dowolnego z tych liczników powyżej wartości progowej wysokiej wbudowane (80%) odrzucone przy użyciu odpowiedzi 429 "Jest zbyt zajęty", dopóki Counter (s) powrócić do normalnego poziomu. <sup>*</sup>Wartość domyślna dla wersji 1.x ma wartość false. Wartość domyślna dla wersji 2.x planu zużycie ma wartość true. Wartość domyślna dla wersji 2.x w dedykowanego planu ma wartość false.|
