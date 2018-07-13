```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|Element routePrefix|api|Prefiks trasy, która ma zastosowanie do wszystkich tras. Użyj pustego ciągu, aby usunąć domyślny prefiks. |
|maxOutstandingRequests|-1|Maksymalna liczba oczekujących żądań, które są przechowywane w dowolnym momencie. Ten limit obejmuje żądań, które są umieszczane w kolejce, ale nie rozpoczęły wykonywania, a także w toku wykonań. Wszystkie przychodzące żądania przekracza ten limit są odrzucane przy użyciu odpowiedzi 429 "Zbyt zajęta". Która pozwala obiektom wywołującym mogą wykorzystać strategii ponawiania oparte na czasie i pomaga także kontrolować żądania maksymalnej wartości opóźnienia. Ta czynność reguluje tylko kolejkowania wykonywaną w ramach ścieżki wykonywania skryptu hosta. Nadal będą inne kolejek, takich jak kolejki żądań ASP.NET i to ustawienie nie ma wpływu. Ustawieniem domyślnym jest nieograniczona.|
|maxConcurrentRequests|-1|Maksymalna liczba funkcje http, które będą wykonywane równolegle. Dzięki temu można kontroli współbieżności, która może ułatwić zarządzanie wykorzystanie zasobów. Na przykład Niewykluczone, że funkcja protokołu http, która używa dużej ilości zasobów systemowych (gniazda pamięci/procesora) w taki sposób, że powoduje problemy, gdy współbieżność jest zbyt wysoka. Lub może być funkcją, która sprawia, że żądania wychodzące do usługi innej firmy, a te wywołania trzeba częstość jest ograniczona. W takich przypadkach zastosowanie ograniczania w tym miejscu może pomóc. Ustawieniem domyślnym jest nieograniczona.|
|dynamicThrottlesEnabled|false|Po włączeniu, będzie to ustawienie powoduje potoku przetwarzania żądań mogą okresowo sprawdzać wydajność systemu liczników, takich jak połączenia/wątki/procesów/pamięci/procesora cpu/itd. oraz żądań w przypadku spełnienia dowolnego z tych liczników powyżej wartości progowej wysokiej wbudowane (80%) odrzucone przy użyciu odpowiedzi 429 "Jest zbyt zajęty", dopóki Counter (s) powrócić do normalnego poziomu.|
