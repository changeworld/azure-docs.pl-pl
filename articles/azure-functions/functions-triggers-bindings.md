---
title: Wyzwalacze i powiązania w Azure Functions
description: Dowiedz się, jak używać wyzwalaczy i powiązań, aby połączyć funkcję platformy Azure ze zdarzeniami online i usługami w chmurze.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226560"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Pojęcia związane z wyzwalaczami i powiązaniami Azure Functions

Ten artykuł zawiera informacje o pojęciach dotyczących wysokiego poziomu otaczających wyzwalacze i powiązania funkcji.

Wyzwalacze to przyczyny uruchomienia funkcji. Wyzwalacz definiuje sposób wywoływania funkcji, a funkcja musi mieć dokładnie jeden wyzwalacz. Wyzwalacze mają skojarzone dane, które są często dostarczane jako ładunek funkcji. 

Powiązanie z funkcją jest sposobem deklaratywnego łączenia innego zasobu z funkcją; powiązania mogą być połączone jako *powiązania wejściowe*, *powiązania wyjściowe*lub oba te elementy. Dane z powiązań są przekazywane do funkcji jako parametry.

Możesz mieszać inne powiązania i dopasować je do swoich potrzeb. Powiązania są opcjonalne, a funkcja może mieć jedno lub wiele powiązań wejściowych i/lub wyjściowych.

Wyzwalacze i powiązania pozwalają uniknąć zakodowana dostępu do innych usług. Funkcja otrzymuje dane (na przykład zawartość komunikatu w kolejce) w parametrach funkcji. Wysyłasz dane (na przykład w celu utworzenia komunikatu w kolejce) przy użyciu wartości zwracanej funkcji. 

Rozważmy następujące przykłady sposobu implementacji różnych funkcji.

| Przykładowy scenariusz | Wyzwalacz | Powiązanie danych wejściowych | Powiązanie danych wyjściowych |
|-------------|---------|---------------|----------------|
| Zostanie wyświetlony nowy komunikat w kolejce, który uruchamia funkcję do zapisu w innej kolejce. | <sup>*</sup> kolejki | *Dawaj* | <sup>*</sup> kolejki |
|Zaplanowane zadanie odczytuje Blob Storage zawartość i tworzy nowy dokument Cosmos DB. | Czasomierz | Blob Storage | Cosmos DB |
|Event Grid jest używany do odczytywania obrazu z Blob Storage i dokumentu z Cosmos DB do wysłania wiadomości e-mail. | Event Grid | Blob Storage i Cosmos DB | SendGrid |
| Element webhook, który używa Microsoft Graph do aktualizowania arkusza programu Excel. | HTTP | *Dawaj* | Microsoft Graph |

<sup>\*</sup> Reprezentuje różne kolejki

Te przykłady nie są wyczerpujące, ale są dostarczane w celu zilustrowania, jak można używać wyzwalaczy i powiązań jednocześnie.

###  <a name="trigger-and-binding-definitions"></a>Definicje wyzwalacza i powiązania

Wyzwalacze i powiązania są zdefiniowane inaczej w zależności od podejścia do programowania.

| Platforma | Wyzwalacze i powiązania są konfigurowane przez... |
|-------------|--------------------------------------------|
| C#Biblioteka klas | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dekorowania nazwy metod i parametrów z C# atrybutami |
| Wszystkie inne (w tym Azure Portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;aktualizowanie [funkcji Function. JSON](./functions-reference.md) ([schemat](http://json.schemastore.org/function)) |

Portal udostępnia interfejs użytkownika dla tej konfiguracji, ale można edytować plik bezpośrednio, otwierając **Edytor zaawansowany** dostępny za pośrednictwem karty **integracja** funkcji.

W programie .NET typ parametru definiuje typ danych danych wejściowych. Na przykład użyj `string`, aby powiązać z tekstem wyzwalacza kolejki, tablicę bajtową do odczytu jako Binary i typ niestandardowy do deserializacji do obiektu.

W przypadku języków, które są dynamicznie wpisywane, takich jak JavaScript, użyj właściwości `dataType` w pliku *Function. JSON* . Aby na przykład odczytać zawartość żądania HTTP w formacie binarnym, ustaw `dataType` na `binary`:

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

Wszystkie wyzwalacze i powiązania mają właściwość `direction` w pliku [Function. JSON](./functions-reference.md) :

- W przypadku wyzwalaczy kierunek jest zawsze `in`
- Powiązania danych wejściowych i wyjściowych używają `in` i `out`
- Niektóre powiązania obsługują specjalny kierunek `inout`. Jeśli używasz `inout`, tylko **Edytor zaawansowany** jest dostępny za pośrednictwem karty **integracja** w portalu.

W przypadku używania [atrybutów w bibliotece klas](functions-dotnet-class-library.md) do konfigurowania wyzwalaczy i powiązań, kierunek jest udostępniany w konstruktorze atrybutu lub wywnioskowany na podstawie typu parametru.

## <a name="supported-bindings"></a>Obsługiwane powiązania

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

Aby uzyskać informacje o tym, które powiązania są w wersji zapoznawczej lub są zatwierdzone do użycia w środowisku produkcyjnym, zobacz [obsługiwane języki](supported-languages.md).

## <a name="resources"></a>Zasoby
- [Wyrażenia i wzorce powiązań](./functions-bindings-expressions-patterns.md)
- [Korzystanie z wartości zwracanej przez funkcję platformy Azure](./functions-bindings-return-value.md)
- [Jak zarejestrować wyrażenie powiązania](./functions-bindings-register.md)
- Testowy
  - [Strategie testowania kodu w usłudze Azure Functions](functions-test-a-function.md)
  - [Ręcznie uruchom funkcję niewyzwalaną przez protokół HTTP](functions-manually-run-non-http.md)
- [Obsługa błędów powiązań](./functions-bindings-errors.md)

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Rejestrowanie rozszerzeń powiązań Azure Functions](./functions-bindings-register.md)
