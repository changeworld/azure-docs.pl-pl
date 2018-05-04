---
title: Jak model złożone typy danych w usłudze Azure Search | Dokumentacja firmy Microsoft
description: Zagnieżdżone lub mogą być modelowane struktury hierarchicznej danych do indeksu usługi Azure Search przy użyciu spłaszczoną wierszy i typ danych kolekcji.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 81298bedd43a89ea948753dffc5f80248f5429ca
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Jak model złożone typy danych w usłudze Azure Search
Zewnętrznych zestawów danych używanych do wypełniania indeksu usługi Azure Search czasami to hierarchiczna lub zagnieżdżony struktur podrzędnych, które nie podzielić się starannie na tabelarycznych zestawu wierszy. Przykłady takich konstrukcji może obejmują wiele lokalizacji i numerów telefonów dla jednego klienta, wiele kolorów i rozmiary dla jednej jednostki SKU wielu autorów jednej książce i tak dalej. Modelowanie warunki, można napotkać tych struktur, określany jako *złożone typy danych*, *złożone typy danych*, *złożone typy danych*, lub *agregacji typy danych*, kilka.

Złożone typy danych nie są natywnie obsługiwane w usłudze Azure Search, ale sprawdzonych obejście zawiera dwuetapowy proces spłaszczanie struktury, a następnie użyć **kolekcji** — typ danych do odtworzenia wewnętrznej struktury. Następujące techniki opisane w tym artykule umożliwia zawartości, które mają być wyszukiwane aspektowej, filtrować i sortować.

## <a name="example-of-a-complex-data-structure"></a>Przykład struktury danych złożonych
Zazwyczaj w danych znajduje się jako zestaw dokumentów JSON i XML lub jako elementy w magazynie NoSQL, takie jak bazy danych Azure rozwiązania Cosmos. Strukturę wyzwanie wynika z o wiele elementów podrzędnych, które muszą być wyszukiwane i filtrowane.  Jako punkt początkowy dla pokazujący obejścia problemu wykonaj następujące dokument JSON, który zawiera zestaw kontaktów, na przykład:

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

Gdy pola o nazwie 'id', 'name' i 'firma' łatwo można mapować jeden do jednego jako pola w indeksie usługi Azure Search, pole "w lokalizacji" zawiera tablicę lokalizacje mających zarówno zestaw identyfikatorów lokalizacji, a także opisy lokalizacji. Biorąc pod uwagę, że usługi Azure Search nie ma typu danych, która obsługuje tę funkcję, potrzebujemy inną metodę modelu to w usłudze Azure Search. 

> [!NOTE]
> Ta technika jest również przez Kirk Evans opisane w artykule w blogu [indeksowania rozwiązania Cosmos bazy danych platformy Azure z usługi Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/), technika o nazwie "spłaszczanie danych", który wskazuje, według których należy pole o nazwie `locationsID` i `locationsDescription` są takie [kolekcje](https://msdn.microsoft.com/library/azure/dn798938.aspx) (lub tablicą ciągów).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Część 1: Spłaszczanie tablicy do poszczególnych pól
Aby utworzyć indeks usługi Azure Search, uwzględniający ten zestaw danych, należy utworzyć poszczególnych pól dla zagnieżdżonej konstrukcja: `locationsID` i `locationsDescription` z typem danych [kolekcje](https://msdn.microsoft.com/library/azure/dn798938.aspx) (lub tablicą ciągów). W tych polach czy indeksu wartości "1" i "2" do `locationsID` pola Jan Kowalski i wartości "3" i "4" w `locationsID` dla Jen Campbell pole.  

Dane w usłudze Azure Search będzie wyglądać następująco: 

![Przykładowe dane 2 wierszy](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Część 2: Dodaj pole kolekcji w definicji indeksu
W schemacie indeksu definicje pól może wyglądać podobnie jak w tym przykładzie.

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

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Sprawdź poprawność zachowań wyszukiwania i opcjonalnie rozszerzono indeksu
Zakładając, że utworzony indeks i załadować dane, możesz sprawdzić rozwiązania można zweryfikować wykonywania zapytania wyszukiwania dla zestawu danych. Każdy **kolekcji** pole powinno być **wyszukiwanie**, **filtrowanie** i **aspektów**. Można uruchomić zapytania, takie jak:

* Znajdowanie wszystkich użytkowników, którzy pracują w centrali"Adventureworks".
* Pobierz liczbę liczba osób pracujących w biurze głównej.  
* Osób, które działają w biurze głównej pokazują, jakie inne biura one działać wraz z liczbą osób w danej lokalizacji.  

Gdy ta metoda znajduje się od siebie jest w przypadku należy wykonać wyszukiwanie łączącą zarówno identyfikator lokalizacji, a także opis lokalizacji. Na przykład:

* Znajdź wszystkie osoby, które mają Office głównej i ma identyfikator lokalizacji 4.  

Jeżeli pamiętasz oryginalnej zawartości po zapoznaniu się następująco:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Jednak teraz, możemy zostały rozdzielone danych na różnych pól, firma Microsoft nie ma możliwości wiedząc, że jeśli Office głównej dla Jen Campbell odnosi się do `locationsID 3` lub `locationsID 4`.  

Aby obsłużyć ten przypadek, zdefiniuj innego pola w indeksie, który łączy wszystkie dane w jednej kolekcji.  W naszym przykładzie zostanie nazywamy to pole `locationsCombined` i będzie oddzielić zawartość z `||` Chociaż można wybrać żadnych separatora, które należy wziąć pod uwagę byłoby unikatowy zestaw znaków dla zawartości. Na przykład: 

![Przykładowe dane 2 wierszy z separatorem](./media/search-howto-complex-data-types/sample-data-2.png)

Za pomocą tej `locationsCombined` pola, firma Microsoft może teraz obsłużyć więcej zapytania, takie jak:

* Pokaż liczba osób pracujących w "Głównej urzędzie" z lokalizacją Id "4".  
* Wyszukaj osoby, które działają w biurze głównej z lokalizacją Id "4". 

## <a name="limitations"></a>Ograniczenia
Ta metoda jest przydatna do kilka scenariuszy, ale nie ma on zastosowania w każdym przypadku.  Na przykład:

1. Jeśli nie masz statycznego zestawu pól w Twojej złożonym typie danych i nie było możliwości wykonania do mapowania wszystkich możliwych typów jedno pole. 
2. Aktualizowanie obiektów zagnieżdżonych wymaga dodatkowej pracy do określenia dokładnie co musi zostać zaktualizowany w indeksie usługi wyszukiwanie Azure

## <a name="sample-code"></a>Przykładowy kod
Na temat indeksu złożonego zestawu danych JSON w usłudze Azure Search i wykonywać wiele zapytań za pośrednictwem tego elementu dataset w tej znajduje się przykład [repozytorium GitHub](https://github.com/liamca/AzureSearchComplexTypes).

## <a name="next-step"></a>Następny krok
[Głos dla macierzystą obsługę złożonych typach danych](https://feedback.azure.com/forums/263029-azure-search) na UserVoice wyszukiwania Azure strony i podaj dodatkowe dane wejściowe, który mamy aspektem implementację funkcji. Użytkownik może również dotrzeć do mnie bezpośrednio w serwisie Twitter na @liamca.

