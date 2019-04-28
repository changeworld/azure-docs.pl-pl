---
title: Jak modelowanie złożonych typów danych — usługa Azure Search
description: Zagnieżdżone lub mogą być modelowane struktury hierarchiczne dane do indeksu usługi Azure Search przy użyciu spłaszczonych zestawów wierszy i typ danych kolekcji.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.custom: seodec2018
ms.openlocfilehash: 973623d6c4cb57518af2012bccf67c969146d23c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076212"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Jak modelowanie złożonych typów danych w usłudze Azure Search
Zewnętrznych zestawów danych używanych do wypełniania indeksu usługi Azure Search czasami obejmują hierarchiczne lub zagnieżdżonych struktur podrzędnych, które nie podzielić się starannego na tabelarycznego zestawu wierszy. Przykłady takich struktur może obejmują wiele lokalizacji i numery telefonów dla jednego klienta, wiele kolory i rozmiary dla jednej jednostki SKU wielu autorów jednej książce i tak dalej. Modelowanie warunki, można napotkać te struktury nazywane *złożone typy danych*, *złożone typy danych*, *złożone typy danych*, lub *agregacji typy danych*, kilka.

Złożone typy danych nie są natywnie obsługiwane w usłudze Azure Search, ale sprawdzone rozwiązania zawiera dwuetapowy proces spłaszczanie struktury, a następnie używając **kolekcji** typ danych do odtworzenia wewnętrznej struktury. Następujące techniki opisanej w tym artykule umożliwia zawartość ma być przeszukiwany aspektową, filtrować i sortować.

## <a name="example-of-a-complex-data-structure"></a>Przykład struktury złożonych danych
Zazwyczaj w danym znajdują się dane jako zbiór dokumentów JSON lub XML lub jako elementy z magazynu NoSQL, takiego jak usługa Azure Cosmos DB. Strukturalnie wyzwanie wynika z posiadanie wielu elementów podrzędnych, które muszą być wyszukiwane i filtrowane.  Jako punktu wyjścia w przypadku przedstawiania obejścia problemu wykonaj następujące dokument JSON, który zawiera zestaw kontaktów, na przykład:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Pola o nazwie "id", "name" i "Firma" łatwo można zamapować typu jako pola w ramach indeksu usługi Azure Search, pole "lokalizacje" zawiera tablicę lokalizacji, mających zarówno zestaw identyfikatorów lokalizacji, a także opisów lokalizacji. Biorąc pod uwagę, że usługa Azure Search nie ma typ danych, który obsługuje tę funkcję, potrzebujemy inny sposób do tego modelu w usłudze Azure Search. 

> [!NOTE]
> Ta technika opisana przez Kirk Evans w wpis w blogu [indeksowanie usługi Azure Cosmos DB przy użyciu usługi Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), technikę o nazwie "spłaszczanie danych", który wskazuje, według których należy pole o nazwie `locationsID` i `locationsDescription` które są zarówno [kolekcje](https://msdn.microsoft.com/library/azure/dn798938.aspx) (lub tablica ciągów).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Część 1: Spłaszczanie tablicy do poszczególnych pól
Aby utworzyć indeks usługi Azure Search, która może pomieścić tego zestawu danych, Utwórz poszczególne pola dla zagnieżdżonych konstrukcja: `locationsID` i `locationsDescription` z typem danych [kolekcje](https://msdn.microsoft.com/library/azure/dn798938.aspx) (lub tablica ciągów). W tych polach będzie indeks wartości "1" i "2" do `locationsID` pole Jan Kowalski i wartości "3" i "4" w `locationsID` pole Jen Campbell.  

Dane w usłudze Azure Search będzie wyglądać następująco: 

![dane przykładowe, 2 wiersze](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Część 2: Dodaj pole kolekcji w definicji indeksu
W schemacie indeksu definicje pól może wyglądać podobnie do tego przykładu.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Sprawdź poprawność wyszukiwań i opcjonalnie rozszerzono indeksu
Zakładając, że utworzono indeks i ładowania danych, teraz możesz przetestować rozwiązanie, aby sprawdzić wyszukiwania wykonywania zapytań względem zestawu danych. Każdy **kolekcji** pole powinno być **wyszukiwanie**, **filtrowanie** i **aspektów**. Można do uruchamiania kwerend, takich jak:

* Znajdź wszystkie osoby, którzy pracują w siedzibie"Adventureworks".
* Pobierz liczbę osób, które działają w Office głównej.  
* Osób pracujących w biurze głównej pokazują, jakie inne biura działają wraz z liczbą osób w każdej lokalizacji.  

Gdy ta technika znajduje się od siebie jest, gdy należy wykonać wyszukiwania, który łączy zarówno identyfikator lokalizacji, a także opis lokalizacji. Na przykład:

* Znajdź wszystkie osoby, gdzie mają one Office główną i ma identyfikator lokalizacji, 4.  

Jeśli pamiętasz oryginalną zawartość zapoznaniu się następująco:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Jednak teraz, gdy firma Microsoft wydzieliła je do różnych pól, firma Microsoft nie ma możliwości wiedząc, że jeśli Office głównej dla Jen Campbell odnosi się do `locationsID 3` lub `locationsID 4`.  

Aby obsłużyć ten przypadek, należy zdefiniować innego pola w indeksie, który łączy wszystkie dane w jedną kolekcję.  W naszym przykładzie będzie przez nas wywoływany w tym polu `locationsCombined` i firma Microsoft będzie oddzielić zawartości przy użyciu `||` Chociaż można wybrać żadnych separatora, która Twoim zdaniem będzie unikatowy zestaw znaków dla zawartości. Na przykład: 

![dane przykładowe, 2 wiersze z separatorem](./media/search-howto-complex-data-types/sample-data-2.png)

Za pomocą tego `locationsCombined` pola, firma Microsoft może teraz obsłużyć więcej zapytań, takich jak:

* Pokaż liczbę osób, które działają w urzędzie "głównej" z lokalizacją identyfikator '4'.  
* Wyszukiwanie osób pracujących w biurze główną lokalizacją identyfikator '4'. 

## <a name="limitations"></a>Ograniczenia
Ta technika jest przydatna dla różnych scenariuszy, ale nie ma on zastosowania w każdym przypadku.  Na przykład:

1. Jeśli nie masz statyczny zestaw pól w swojej złożonym typie danych, a nie było sposobu mapowania wszystkich możliwych typów jedno pole. 
2. Aktualizowanie obiektów zagnieżdżonych wymaga pewnej dodatkowej pracy, aby określić dokładnie co musi zostać zaktualizowany w indeksie usługi Azure Search

## <a name="sample-code"></a>Przykładowy kod
Przykładowy wynik można obejrzeć na temat indeksu złożonego zestawu danych JSON do usługi Azure Search i wykonywać zapytania za pośrednictwem tego zestawu danych, w tym [repozytorium GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Następny krok
[Zagłosuj na natywną obsługę złożone typy danych](https://feedback.azure.com/forums/263029-azure-search) w usłudze Azure Search UserVoice strony i podanie dodatkowych danych wejściowych, które możemy aspektem wdrożenia funkcji. Można również skontaktowanie się ze mną bezpośrednio Matta na Twitterze @liamca.

