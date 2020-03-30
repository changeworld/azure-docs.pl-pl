---
title: Powiązania wyjściowe HTTP usług Azure Functions
description: Dowiedz się, jak zwracać odpowiedzi HTTP w usłudze Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: a25658677e436edf4d001599bb4981f527016596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277624"
---
# <a name="azure-functions-http-output-bindings"></a>Powiązania wyjściowe HTTP usług Azure Functions

Użyj powiązania wyjścia HTTP, aby odpowiedzieć na nadawcę żądania HTTP. To powiązanie wymaga wyzwalacza HTTP i pozwala na dostosowanie odpowiedzi skojarzonej z żądaniem wyzwalacza.

Domyślna wartość zwracana dla funkcji wyzwalanej przez HTTP to:

- `HTTP 204 No Content`z pustym korpusem w funkcjach 2.x lub nowszych
- `HTTP 200 OK`z pustym korpusem w funkcji 1.x

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli opisano właściwości konfiguracji powiązania, które można ustawić w pliku *function.json.* W przypadku bibliotek klas języka C# nie ma żadnych właściwości atrybutu, które odpowiadają tym właściwościom *function.json.*

|Właściwość  |Opis  |
|---------|---------|
| **Typu** |Musi być `http`ustawiona na . |
| **Kierunku** | Musi być `out`ustawiona na . |
| **Nazwa** | Nazwa zmiennej używana w kodzie `$return` funkcji dla odpowiedzi lub do użycia wartości zwracanej. |

## <a name="usage"></a>Sposób użycia

Aby wysłać odpowiedź HTTP, użyj wzorców odpowiedzi w standardzie języka. W skrypcie C# lub C# należy wprowadzić typ `IActionResult` zwracania funkcji lub `Task<IActionResult>`. W języku C#atrybut wartości zwracanej nie jest wymagany.

Na przykład odpowiedzi można znaleźć w [przykładzie wyzwalacza](./functions-bindings-http-webhook-trigger.md#example).

## <a name="hostjson-settings"></a>ustawienia host.json

W tej sekcji opisano globalne ustawienia konfiguracji dostępne dla tego powiązania w wersjach 2.x lub nowszych. Przykładowy plik host.json poniżej zawiera tylko ustawienia w wersji 2.x+ dla tego powiązania. Aby uzyskać więcej informacji na temat globalnych ustawień konfiguracji w wersjach 2.x i nowszych, zobacz [odwołanie do host.json dla usługi Azure Functions](functions-host-json.md).

> [!NOTE]
> Aby uzyskać odwołanie do pliku host.json w usługach 1.x, zobacz [odwołanie do host.json dla usługi Azure Functions 1.x](functions-host-json-v1.md#http).

```json
{
    "extensions": {
        "http": {
            "routePrefix": "api",
            "maxOutstandingRequests": 200,
            "maxConcurrentRequests": 100,
            "dynamicThrottlesEnabled": true,
            "hsts": {
                "isEnabled": true,
                "maxAge": "10"
            },
            "customHeaders": {
                "X-Content-Type-Options": "nosniff"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
| customHeaders (Niestandardowe głowice)|brak|Umożliwia ustawienie niestandardowych nagłówków w odpowiedzi HTTP. W poprzednim przykładzie dodaje `X-Content-Type-Options` nagłówek do odpowiedzi, aby uniknąć wąchania typu zawartości. |
|dynamicThrottlesEnabled|True<sup>\*</sup>|Po włączeniu to ustawienie powoduje, że potok przetwarzania żądań `connections/threads/processes/memory/cpu/etc` okresowo sprawdza liczniki wydajności systemu, takie jak i jeśli którykolwiek z `429 "Too Busy"` tych liczników przekracza wbudowany wysoki próg (80%), żądania zostaną odrzucone z odpowiedzią, dopóki liczniki nie powrócą do normalnego poziomu.<br/><sup>\*</sup>Wartość domyślna w `true`planie zużycia to . Wartość domyślna w `false`planie dedykowanym to .|
|hsts (hsts)|nie włączone|Gdy `isEnabled` jest `true`ustawiona na , [HTTP Strict Transport Security (HSTS) zachowanie .NET Core](/aspnet/core/security/enforcing-ssl?view=aspnetcore-3.0&tabs=visual-studio#hsts) jest wymuszane, zgodnie z definicją [ `HstsOptions` ](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions?view=aspnetcore-3.0)w klasie . Powyższy przykład ustawia [`maxAge`](/dotnet/api/microsoft.aspnetcore.httpspolicy.hstsoptions.maxage?view=aspnetcore-3.0#Microsoft_AspNetCore_HttpsPolicy_HstsOptions_MaxAge) również właściwość do 10 dni. Obsługiwane właściwości `hsts` to: <table><tr><th>Właściwość</th><th>Opis</th></tr><tr><td>wykluczoneHosts</td><td>Tablica ciągów nazw hostów, dla których nie jest dodawany nagłówek HSTS.</td></tr><tr><td>zawierasubDomains</td><td>Wartość logiczna wskazująca, czy parametr includeSubDomain nagłówka Strict-Transport-Security jest włączony.</td></tr><tr><td>Maxage</td><td>Ciąg definiujący parametr max-age nagłówka Strict-Transport-Security.</td></tr><tr><td>preload</td><td>Wartość logiczna wskazująca, czy włączona jest paraparat nagłówka Strict-Transport-Security.</td></tr></table>|
|maxConcurrentRequests|100<sup>\*</sup>|Maksymalna liczba funkcji HTTP, które są wykonywane równolegle. Ta wartość umożliwia kontrolowanie współbieżności, co może pomóc w zarządzaniu wykorzystaniem zasobów. Na przykład może mieć funkcję HTTP, która używa dużej liczby zasobów systemowych (pamięci/cpu/sockets) w taki sposób, że powoduje problemy, gdy współbieżność jest zbyt wysoka. Lub może mieć funkcję, która sprawia, że wychodzące żądania do usługi innej firmy, a te wywołania muszą być ograniczone stawki. W takich przypadkach zastosowanie przepustnicy tutaj może pomóc. <br/><sup>*</sup>Wartość domyślna dla planu zużycia to 100. Wartość domyślna planu dedykowanego jest`-1`niezwiązana ( ).|
|maxOutstandingRequests|200<sup>\*</sup>|Maksymalna liczba zaległych żądań, które są przechowywane w danym momencie. Ten limit obejmuje żądania, które są w kolejce, ale nie rozpoczęły wykonywania, a także wszelkie w toku wykonania. Wszystkie przychodzące żądania przekraczające ten limit są odrzucane z odpowiedzią 429 "Zbyt zajęty". Dzięki temu wywołujący można stosować strategie ponawiania na podstawie czasu, a także pomaga kontrolować maksymalne opóźnienia żądań. To tylko steruje kolejkowaniem, który występuje w ścieżce wykonywania hosta skryptu. Inne kolejki, takie jak kolejka żądań ASP.NET, będą nadal obowiązywać i nie będą objęte tym ustawieniem. <br/><sup>\*</sup>Wartość domyślna dla planu zużycia wynosi 200. Wartość domyślna planu dedykowanego jest`-1`niezwiązana ( ).|
|routePrefix|api|Prefiks trasy, który ma zastosowanie do wszystkich tras. Użyj pustego ciągu, aby usunąć domyślny prefiks. |

## <a name="next-steps"></a>Następne kroki

- [Uruchamianie funkcji z żądania HTTP](./functions-bindings-http-webhook-trigger.md)