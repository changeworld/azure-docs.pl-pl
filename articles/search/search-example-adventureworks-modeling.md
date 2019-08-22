---
title: 'Przykład: Modelowanie bazy danych magazynu AdventureWorks — Azure Search'
description: Dowiedz się, jak modelować dane relacyjne, przekształcając je w spłaszczony zestaw danych, na potrzeby indeksowania i wyszukiwania pełnotekstowego w Azure Search.
author: cstone
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 52ccf3edfca5b3481b038bd5d3449c1dd6354179
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649922"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Przykład: Modelowanie bazy danych magazynu AdventureWorks dla Azure Search

Modelowanie zawartości strukturalnej bazy danych w wydajnym indeksie wyszukiwania jest rzadko bardzo proste. Planowanie i zarządzanie zmianami, istnieje wyzwanie związane z denormalizacją wierszy źródłowych od ich stanu przyłączonego do tabeli. W tym artykule są używane dane przykładowe AdventureWorks, dostępne online, aby wyróżnić typowe środowiska przejścia z bazy danych do wyszukania. 

## <a name="about-adventureworks"></a>Informacje o AdventureWorks

Jeśli masz wystąpienie SQL Server, możesz zapoznać się z przykładową bazą danych AdventureWorks. Wśród tabel uwzględnionych w tej bazie danych znajdują się pięć tabel, które ujawniają informacje o produkcie.

+ **ProductModel**: Nazwa
+ **Produkt**: nazwa, kolor, koszt, rozmiar, waga, obraz, Kategoria (każdy wiersz jest przyłączany do określonego ProductModel)
+ **ProductDescription**: Opis
+ **ProductModelProductDescription**: locale (każdy wiersz jest przyłączany do ProductModel do określonego ProductDescription dla określonego języka)
+ **ProductCategory**: nazwa, Kategoria nadrzędna

Połączenie wszystkich tych danych z spłaszczonym zestawem wierszy, które można pozyskać w indeksie wyszukiwania, jest zadaniem w stanie. 

## <a name="considering-our-options"></a>Rozważanie naszych opcji

Podejście algorytmie byłoby indeksować wszystkie wiersze z tabeli produktów (przyłączone tam, gdzie to stosowne), ponieważ tabela produktów zawiera najbardziej szczegółowe informacje. Jednak takie podejście uwidacznia indeks wyszukiwania na postrzegane duplikaty w zestawie wyników. Na przykład model Road-650 jest dostępny w dwóch kolorach i sześciu rozmiarach. Dla zapytania o "rowery Bikes" można następnie pomieścić dwanaście wystąpień tego samego modelu, zróżnicowane tylko według rozmiaru i koloru. Pozostałe sześć modeli specyficznych dla dróg relegated się na świecie zweryfikowałoe: druga strona.

  ![Lista produktów](./media/search-example-adventureworks/products-list.png "Lista produktów")
 
Należy zauważyć, że model Road-650 ma 12 opcji. Wiersze "jeden do wielu" są najlepiej reprezentowane jako pola wielowartościowe lub pola wartości wstępnie zagregowanych w indeksie wyszukiwania.

Rozwiązanie tego problemu nie jest tak proste jak przeniesienie docelowego indeksu do tabeli ProductModel. Spowoduje to zignorowanie ważnych danych w tabeli Product, które powinny być reprezentowane w wynikach wyszukiwania.

## <a name="use-a-collection-data-type"></a>Użyj typu danych kolekcji

"Prawidłowe podejście" polega na użyciu funkcji wyszukiwania w schemacie, która nie ma bezpośredniego operatora równoległego w modelu bazy danych: **Kolekcja (EDM. String)** . Typ danych kolekcji jest używany, gdy masz listę pojedynczych ciągów, a nie bardzo długi (pojedynczy) ciąg. Jeśli masz Tagi lub słowa kluczowe, użyj typu danych kolekcji dla tego pola.

Przez definiowanie pól indeksu wielowartościowego **kolekcji (EDM. String)** dla "Color", "size" i "Image" informacje pomocnicze są zachowywane dla aspektów i filtrowania bez zanieczyszczania indeksu ze zduplikowanymi wpisami. Analogicznie Zastosuj funkcje agregujące do pól liczbowych produktu, indeksowanie **minListPrice** zamiast każdego pojedynczego produktu **listPrice**.

Mając indeks z tymi strukturami, wyszukiwanie "Mountain Bikes" będzie zawierać odrębne modele roweru, przy zachowaniu ważnych metadanych, takich jak kolor, rozmiar i najniższa cena. Poniższy zrzut ekranu przedstawia ilustrację.

  ![Przykład wyszukiwania roweru górskiego](./media/search-example-adventureworks/mountain-bikes-visual.png "Przykład wyszukiwania roweru górskiego")

## <a name="use-script-for-data-manipulation"></a>Używanie skryptu do manipulowania danymi

Niestety, tego typu modelowania nie można łatwo osiągnąć za poorednictwem instrukcji SQL. Zamiast tego należy użyć prostego skryptu NodeJS do załadowania danych, a następnie zamapowania ich do jednostek JSON przyjaznych dla wyszukiwania.

Ostateczne mapowanie wyszukiwania bazy danych wygląda następująco:

+ Model (EDM. String: wyszukiwanie, filtrowanie, pobieranie) z "ProductModel.Name"
+ description_en (EDM. String: wyszukiwanie) z "ProductDescription" dla modelu, gdzie Culture = "pl"
+ Color (kolekcja (EDM. String): możliwość wyszukiwania, filtrowania, tworzenia i pobierania): unikatowe wartości z "Product. Color" dla modelu
+ size (kolekcja (EDM. String): możliwość wyszukiwania, filtrowania, tworzenia i pobierania): unikatowe wartości z "Product. size" dla modelu
+ Image (Collection (EDM. String): możliwy do pobrania): unikatowe wartości z "Product. ThumbnailPhoto" dla modelu
+ minStandardCost (EDM. Double: możliwość filtrowania, tworzenia i sortowania): agregowanie minimum wszystkich "Product. StandardCost" dla modelu
+ minListPrice (EDM. Double: możliwość filtrowania, tworzenia i sortowania): agregowanie minimum wszystkich "Product. ListPrice" dla modelu
+ minWeight (EDM. Double: możliwość filtrowania, tworzenia i sortowania): zagregowana wartość minimalna wszystkich "Product. wag" dla modelu
+ Produkty (kolekcja (EDM. String): możliwość wyszukiwania, filtrowania, pobierania): unikatowe wartości z "Product.Name" dla modelu

Po dołączeniu tabeli ProductModel do produktu i ProductDescription Użyj [lodash](https://lodash.com/) (lub LINQ in C#), aby szybko przekształcić zestaw wyników:

```javascript
var records = queryYourDatabase();
var models = _(records)
  .groupBy('ModelName')
  .values()
  .map(function(d) {
    return {
      model: _.first(d).ModelName,
      description: _.first(d).Description,
      colors: _(d).pluck('Color').uniq().compact().value(),
      products: _(d).pluck('ProductName').uniq().compact().value(),
      sizes: _(d).pluck('Size').uniq().compact().value(),
      images: _(d).pluck('ThumbnailPhotoFilename').uniq().compact().value(),
      minStandardCost: _(d).pluck('StandardCost').min(),
      maxStandardCost: _(d).pluck('StandardCost').max(),
      minListPrice: _(d).pluck('ListPrice').min(),
      maxListPrice: _(d).pluck('ListPrice').max(),
      minWeight: _(d).pluck('Weight').min(),
      maxWeight: _(d).pluck('Weight').max(),
    };
  })
  .value();
```

Wynikowy kod JSON wygląda następująco:

```json
[
  {
    "model": "HL Road Frame",
    "colors": [
      "Black",
      "Red"
    ],
    "products": [
      "HL Road Frame - Black, 58",
      "HL Road Frame - Red, 58",
      "HL Road Frame - Red, 62",
      "HL Road Frame - Red, 44",
      "HL Road Frame - Red, 48",
      "HL Road Frame - Red, 52",
      "HL Road Frame - Red, 56",
      "HL Road Frame - Black, 62",
      "HL Road Frame - Black, 44",
      "HL Road Frame - Black, 48",
      "HL Road Frame - Black, 52"
    ],
    "sizes": [
      "58",
      "62",
      "44",
      "48",
      "52",
      "56"
    ],
    "images": [
      "no_image_available_small.gif"
    ],
    "minStandardCost": 868.6342,
    "maxStandardCost": 1059.31,
    "minListPrice": 1431.5,
    "maxListPrice": 1431.5,
    "minWeight": 961.61,
    "maxWeight": 1043.26
  }
]
```

Na koniec jest to zapytanie SQL zwracające początkowy zestaw rekordów. Moduł [MSSQL](https://www.npmjs.com/package/mssql) npm został użyty do załadowania danych do aplikacji NodeJS.

```T-SQL
SELECT
  m.Name as ModelName,
  d.Description,
  p.Name as ProductName,
  p.*
FROM 
  SalesLT.ProductModel m
INNER JOIN 
  SalesLT.ProductModelProductDescription md
  ON m.ProductModelId = md.ProductModelId
INNER JOIN 
  SalesLT.ProductDescription d
  ON md.ProductDescriptionId = d.ProductDescriptionId
LEFT JOIN 
  SalesLT.product p
  ON m.ProductModelId = p.ProductModelId
WHERE
  md.Culture='en'
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Przykład: Taksonomie aspektów wielopoziomowych w Azure Search](search-example-adventureworks-multilevel-faceting.md)


