---
title: Wyzwalacze i powiązania w usłudze Azure Functions
description: Dowiedz się użyć wyzwalaczy i powiązań funkcji platformy Azure łączenie się z wydarzenia online i usług w chmurze.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 0896a829a24335f3e62581fa3b63e013478781bc
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2019
ms.locfileid: "67480991"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Pojęcia powiązania i Wyzwalacze usługi Azure Functions

W tym artykule dowiesz się szczegółowo pojęcia, otaczającego funkcje wyzwalaczy i powiązań.

Wyzwalacze to, co spowodować uruchamianie funkcji. Wyzwalacz definiuje sposób wywoływania funkcji i funkcji musi mieć dokładnie jeden wyzwalacz. Wyzwalacze mieć skojarzone dane, które często jest dostarczana jako ładunek funkcji. 

Powiązanie funkcji jest sposobem deklaratywne nawiązywania połączenia z innym zasobem funkcji; powiązania mogą być połączone jako *powiązań wejściowych*, *danych wyjściowych powiązania*, lub obu. Dane z powiązań podano funkcji jako parametrów.

Można mieszać i dopasowywać różnych powiązania do własnych potrzeb. Powiązania są opcjonalne, a funkcja może mieć jednego lub wielu danych wejściowych i/lub danych wyjściowych powiązania.

Wyzwalacze i powiązania pozwalają uniknąć hardcoding dostępu do innych usług. Funkcja odbiera dane (na przykład zawartość komunikatu w kolejce) w parametrach funkcji. Możesz wysłać danych (na przykład w celu utworzenia komunikatu w kolejce) przy użyciu wartość zwracaną przez funkcję. 

Należy wziąć pod uwagę następujące przykłady sposobu implementacji różnych funkcji.

| Przykładowy scenariusz | Wyzwalacz | Powiązania danych wejściowych | Powiązanie danych wyjściowych |
|-------------|---------|---------------|----------------|
| Nadejdzie nowy komunikat kolejki, która wykonuje funkcję, aby zapisać do innej kolejki. | kolejki<sup>*</sup> | *Brak* | kolejki<sup>*</sup> |
|Zaplanowane zadanie odczytuje zawartość usługi Blob Storage i tworzy nowy dokument usługi Cosmos DB. | Czasomierz | Blob Storage | Cosmos DB |
|Usługi Event Grid jest używany do odczytu obrazu z magazynu obiektów Blob i za pomocą usługi Cosmos DB, aby wysłać wiadomość e-mail. | Event Grid | Magazyn obiektów blob i Cosmos DB | SendGrid |
| Element webhook, który używa programu Microsoft Graph, aby zaktualizować arkusz programu Excel. | HTTP | *Brak* | Microsoft Graph |

<sup>\*</sup> Reprezentuje różnych kolejek

Te przykłady nie mają być wyczerpująca, ale są udostępniane, aby zilustrować, jak skorzystać wyzwalaczy i powiązań razem.

###  <a name="trigger-and-binding-definitions"></a>Definicje powiązań i wyzwalaczy

Wyzwalacze i powiązania są zdefiniowane inaczej w zależności od metody programowania.

| Platforma | Wyzwalacze i powiązania są konfigurowane przez... |
|-------------|--------------------------------------------|
| C#Biblioteka klas | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;urządzanie metody i parametrów za pomocą C# atrybutów |
| Wszystkie pozostałe (w tym witryny Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aktualizowanie [function.json](./functions-reference.md) ([schematu](http://json.schemastore.org/function)) |

Portal udostępnia interfejs wielokrotnego użytku tej konfiguracji, ale plik można edytować bezpośrednio, otwierając **edytor zaawansowany** dostępne za pośrednictwem **integracja** kartę funkcji.

Na platformie .NET typ parametru definiuje typ danych dla danych wejściowych. Na przykład użyć `string` do powiązania w tekście wyzwalacz kolejki, tablica bajtów do wczytania danych binarnych oraz typ niestandardowy w celu deserializować do obiektu.

W przypadku języków, które są dynamicznie wpisane, takich jak JavaScript, użyj `dataType` właściwość *function.json* pliku. Na przykład, aby odczytać zawartość żądania HTTP w formacie binarnym, należy ustawić `dataType` do `binary`:

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

Inne opcje `dataType` są `stream` i `string`.

## <a name="binding-direction"></a>Kierunek powiązania

Wszystkie wyzwalacze i powiązania ma `direction` właściwość [function.json](./functions-reference.md) pliku:

- Wyzwalacze kierunku jest zawsze `in`
- Użyj powiązania danych wejściowych i wyjściowych `in` i `out`
- Niektóre wiązania obsługuje kierunku specjalne `inout`. Jeśli używasz `inout`, tylko **edytor zaawansowany** jest dostępny za pośrednictwem **integracja** karty w portalu.

Kiedy używasz [atrybutów w bibliotece klas](functions-dotnet-class-library.md) do skonfigurowania, wyzwalaczy i powiązań, kierunku jest podana w Konstruktorze atrybutu lub wywnioskowane na podstawie typu parametru.

## <a name="supported-bindings"></a>Obsługiwane powiązania

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Aby uzyskać informacje o tym, które są w wersji zapoznawczej powiązań, lub są zatwierdzone do użycia w środowisku produkcyjnym, zobacz [obsługiwane języki](supported-languages.md).

## <a name="resources"></a>Zasoby
- [Wyrażenia wiązania i wzorców](./functions-bindings-expressions-patterns.md)
- [Przy użyciu wartości zwracanej funkcji platformy Azure](./functions-bindings-return-value.md)
- [Jak zarejestrować wyrażenia wiązania](./functions-bindings-register.md)
- Testing:
  - [Strategie testowania kodu w usłudze Azure Functions](functions-test-a-function.md)
  - [Ręcznie uruchomić bez funkcji wyzwalanej przez HTTP](functions-manually-run-non-http.md)
- [Obsługa błędów powiązań](./functions-bindings-errors.md)

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Rejestrowanie rozszerzeń powiązania usługi Azure Functions](./functions-bindings-register.md)
