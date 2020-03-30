---
title: Wyzwalacze i powiązania w usłudze Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań, aby połączyć funkcję platformy Azure ze zdarzeniami online i usługami w chmurze.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276506"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Pojęcia powiązań i wyzwalaczy usługi Azure Functions

W tym artykule dowiesz się, że pojęcia wysokiego poziomu otaczające wyzwalacze funkcji i powiązania.

Wyzwalacze są przyczyną uruchomienia funkcji. Wyzwalacz definiuje sposób wywoływania funkcji i funkcja musi mieć dokładnie jeden wyzwalacz. Wyzwalacze mają skojarzone dane, które często są dostarczane jako ładunek funkcji. 

Powiązanie z funkcją jest sposobem deklaratywnie łączącego inny zasób z funkcją; powiązania mogą być podłączone jako *wiązania wejściowe,* *wiązania wyjściowe*lub oba te elementy. Dane z powiązań są podawane do funkcji jako parametry.

Możesz mieszać i dopasowywać różne powiązania, aby dostosować je do własnych potrzeb. Powiązania są opcjonalne, a funkcja może mieć jedno lub wiele powiązań wejściowych i/lub wyjściowych.

Wyzwalacze i powiązania pozwalają uniknąć twardego dostępu do innych usług. Funkcja będzie odbierać dane (np. zawartość komunikatu kolejki) w parametrach funkcji. Możesz wysłać dane (np. w celu utworzenia komunikatu kolejki) przy użyciu wartości zwracanej przez funkcję. 

Należy wziąć pod uwagę poniższe przykłady, jak można zaimplementować różne funkcje.

| Przykładowy scenariusz | Wyzwalacz | Powiązanie wejściowe | Oprawa wyjściowa |
|-------------|---------|---------------|----------------|
| Nadchodzi nowy komunikat kolejki, który uruchamia funkcję zapisu do innej kolejki. | Kolejki<sup>*</sup> | *Brak* | Kolejki<sup>*</sup> |
|Zaplanowane zadanie odczytuje zawartość magazynu obiektów Blob i tworzy nowy dokument usługi Cosmos DB. | Czasomierz | Blob Storage | Cosmos DB |
|Siatka zdarzeń służy do odczytywania obrazu z magazynu obiektów Blob i dokumentu z usługi Cosmos DB w celu wysłania wiadomości e-mail. | Event Grid | Magazyn obiektów blob i usługa Cosmos DB | SendGrid |
| Element webhook, który używa programu Microsoft Graph do aktualizacji arkusza programu Excel. | HTTP | *Brak* | Microsoft Graph |

<sup>\*</sup>Reprezentuje różne kolejki

Te przykłady nie mają być wyczerpujące, ale są dostarczane w celu zilustrowania, jak można używać wyzwalaczy i powiązań razem.

###  <a name="trigger-and-binding-definitions"></a>Definicje wyzwalania i wiązania

Wyzwalacze i powiązania są definiowane inaczej w zależności od podejścia rozwoju.

| Platforma | Wyzwalacze i powiązania są konfigurowane przez... |
|-------------|--------------------------------------------|
| Biblioteka klas języka C# | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;metody i parametry dekorowania za pomocą atrybutów języka C# |
| Wszystkie inne (w tym Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aktualizowanie [pliku function.json](./functions-reference.md) ([schemat](http://json.schemastore.org/function)) |

Portal udostępnia interfejs użytkownika dla tej konfiguracji, ale można edytować plik bezpośrednio, otwierając **edytor zaawansowany** dostępny za pośrednictwem karty **Integruj** funkcję.

W .NET typ parametru definiuje typ danych dla danych wejściowych. Na przykład `string` użyj do powiązania z tekstem wyzwalacza kolejki, tablicy bajtowej do odczytu jako binarny i typu niestandardowego do de-serializacji do obiektu.

W przypadku języków, które są dynamicznie wpisywane, takich jak JavaScript, należy użyć `dataType` właściwości w pliku *function.json.* Na przykład, aby odczytać zawartość żądania HTTP w `dataType` `binary`formacie binarnym, ustawiono na:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Inne opcje `dataType` `stream` są `string`i .

## <a name="binding-direction"></a>Kierunek wiązania

Wszystkie wyzwalacze i powiązania `direction` mają właściwość w pliku [function.json:](./functions-reference.md)

- W przypadku wyzwalaczy kierunek jest zawsze`in`
- Wiązania wejściowe i `in` wyjściowe`out`
- Niektóre wiązania obsługują specjalny `inout`kierunek . Jeśli używasz, `inout`tylko **edytor zaawansowany** jest dostępny za pośrednictwem karty **Integruj** w portalu.

W przypadku [używania atrybutów w bibliotece klas](functions-dotnet-class-library.md) do konfigurowania wyzwalaczy i powiązań kierunek jest podany w konstruktorze atrybutów lub wywnioskowany z typu parametru.

## <a name="supported-bindings"></a>Obsługiwane powiązania

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Aby uzyskać informacje o tym, które powiązania są w wersji zapoznawczej lub są zatwierdzone do użytku w produkcji, zobacz [Obsługiwane języki](supported-languages.md).

## <a name="resources"></a>Resources
- [Wyrażenia wiązania i wzorce](./functions-bindings-expressions-patterns.md)
- [Korzystanie z wartości zwracanej funkcji platformy Azure](./functions-bindings-return-value.md)
- [Jak zarejestrować wyrażenie wiążące](./functions-bindings-register.md)
- Testowania:
  - [Strategie testowania kodu w usłudze Azure Functions](functions-test-a-function.md)
  - [Ręczne uruchamianie funkcji niewyzwalanej przez protokół HTTP](functions-manually-run-non-http.md)
- [Obsługa błędów wiązania](./functions-bindings-errors.md)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Rejestrowanie rozszerzeń powiązań usługi Azure Functions](./functions-bindings-register.md)
