---
title: 'Przykład: Modelowanie bazy danych AdventureWorks Inventory'
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak modelować dane relacyjne, przekształcając je w spłaszczony zestaw danych do indeksowania i wyszukiwania pełnotekstowego w usłudze Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: heidist
ms.openlocfilehash: edb6162724938962df8a7340afea6e930a0b1049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792997"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-cognitive-search"></a>Przykład: Modelowanie bazy danych AdventureWorks Inventory dla usługi Azure Cognitive Search

Usługa Azure Cognitive Search akceptuje spłaszczony zestaw wierszy jako dane wejściowe do [potoku indeksowania (pozyskiwania danych).](search-what-is-an-index.md) Jeśli dane źródłowe pochodzą z relacyjnej bazy danych programu SQL Server, w tym artykule pokazano jedno podejście do tworzenia spłaszczone rowset przed indeksowania, przy użyciu AdventureWorks przykładowej bazy danych jako przykład.

## <a name="about-adventureworks"></a>Więcej o: AdventureWorks

Jeśli masz wystąpienie programu SQL Server, możesz znać [przykładową bazę danych AdventureWorks](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017). Wśród tabel zawartych w tej bazie danych jest pięć tabel, które ujawniają informacje o produkcie.

+ **ProductModel**: nazwa
+ **Produkt**: nazwa, kolor, koszt, rozmiar, waga, obraz, kategoria (każdy wiersz łączy się z określonym ProductModel)
+ **ProductDescription**: opis
+ **ProductModelProductDescription**: ustawienia regionalne (każdy wiersz łączy ProductModel do określonego ProductDescription dla określonego języka)
+ **Kategoria produktu**: nazwa, kategoria nadrzędna

Połączenie wszystkich tych danych w spłaszczony zestaw wierszy, które mogą być spożywane w indeksie wyszukiwania jest celem tego przykładu. 

## <a name="considering-our-options"></a>Biorąc pod uwagę nasze opcje

Naiwnym podejściem byłoby indeksować wszystkie wiersze z product tabeli (połączone w stosownych przypadkach), ponieważ Product tabeli ma najbardziej szczegółowych informacji. Jednak takie podejście będzie uwidocznić indeks wyszukiwania do postrzeganych duplikatów w pliku wyników. Na przykład model Road-650 jest dostępny w dwóch kolorach i sześciu rozmiarach. Zapytanie dotyczące "rowerów szosowych" byłoby następnie zdominowane przez dwanaście wystąpień tego samego modelu, zróżnicowanych tylko pod względem rozmiaru i koloru. Pozostałe sześć modeli specyficznych dla dróg zostanie zdegradowanych do dosyć świata wyszukiwania: strona druga.

  ![Lista produktów](./media/search-example-adventureworks/products-list.png "Lista produktów")
 
Należy zauważyć, że model Road-650 ma dwanaście opcji. Wiersze jednostek jeden do wielu najlepiej są reprezentowane jako pola wielowartościowe lub wstępnie zagregowane pola wartości w indeksie wyszukiwania.

Rozwiązanie tego problemu nie jest tak proste, jak przeniesienie indeksu docelowego do tabeli ProductModel. W ten sposób można zignorować ważne dane w product tabeli, które nadal powinny być reprezentowane w wynikach wyszukiwania.

## <a name="use-a-collection-data-type"></a>Używanie typu danych kolekcji

"Poprawne podejście" polega na wykorzystaniu funkcji schematu wyszukiwania, która nie ma bezpośredniego równoległego w modelu bazy danych: **Collection(Edm.String).** Ta konstrukcja jest zdefiniowana w schemacie indeksu usługi Azure Cognitive Search. Typ danych kolekcji jest używany, gdy trzeba reprezentować listę poszczególnych ciągów, a nie bardzo długi (pojedynczy) ciąg. Jeśli masz tagi lub słowa kluczowe, należy użyć typu danych kolekcji dla tego pola.

Definiując wielowartościowe pola indeksu **Collection(Edm.String)** dla "color", "size" i "image", informacje pomocnicze są zachowywane do fasetowania i filtrowania bez zanieczyszczania indeksu zduplikowanymi wpisami. Podobnie zastosuj funkcje agregujące do pól liczbowych Produktu, indeksując **minListPrice** zamiast każdej **pojedynczej listy produktówCece**.

Biorąc pod uwagę indeks z tych struktur, wyszukiwanie "rowery górskie" pokaże dyskretne modele rowerów, przy jednoczesnym zachowaniu ważnych metadanych, takich jak kolor, rozmiar i najniższa cena. Poniższy zrzut ekranu zawiera ilustrację.

  ![Przykład wyszukiwania rowerów górskich](./media/search-example-adventureworks/mountain-bikes-visual.png "Przykład wyszukiwania rowerów górskich")

## <a name="use-script-for-data-manipulation"></a>Używanie skryptu do manipulowania danymi

Niestety tego typu modelowania nie można łatwo osiągnąć za pomocą instrukcji SQL sam. Zamiast tego użyj prostego skryptu NodeJS, aby załadować dane, a następnie zamapuj je na przyjazne dla wyszukiwania jednostki JSON.

Końcowe mapowanie wyszukiwania bazy danych wygląda następująco:

+ model (Edm.String: przeszukiwalny, filtrowany, można go pobrać) z "ProductModel.Name"
+ description_en (Edm.String: searchable) z "ProductDescription" dla modelu, w którym culture='en'
+ color (Collection(Edm.String): przeszukiwalny, filtrowany, typowalny, można go pobrać): unikatowe wartości z "Product.Color" dla modelu
+ size (Kolekcja(Edm.String): przeszukiwalny, filtrowany, typowalny, można go pobrać): unikatowe wartości z "Produktu.Rozmiar" dla modelu
+ obraz (Kolekcja(Edm.String): możliwe do pobrania): unikatowe wartości z "Product.ThumbnailPhoto" dla modelu
+ minStandardCost (Edm.Double: filtrowalne, typowalne, sortowalne, możliwe do pobrania): zagregowane minimum wszystkich "Product.StandardCost" dla modelu
+ minListPrice (Edm.Double: filtrowalne, typowalne, sortowalne, możliwe do pobrania): zagregowane minimum wszystkich "Product.ListPrice" dla modelu
+ minWeight (Edm.Double: filtrowalne, fasetowalne, sortowalne, możliwe do pobrania): zagregowane minimum wszystkich "Product.Weight" dla modelu
+ produkty (Kolekcja(Edm.String): przeszukiwalne, filtrowalne, możliwe do pobrania): unikalne wartości z "Product.Name" dla modelu

Po dołączeniu do tabeli ProductModel z produktem i ProductDescription użyj [lodash](https://lodash.com/) (lub Linq w języku C#), aby szybko przekształcić zestaw wyników:

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

Wynikowy JSON wygląda następująco:

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

Na koniec oto zapytanie SQL, aby zwrócić początkowy rekord. Użyłem modułu [mssql](https://www.npmjs.com/package/mssql) npm, aby załadować dane do mojej aplikacji NodeJS.

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
> [Przykład: Taksonomie wielopoziomowe w usłudze Azure Cognitive Search](search-example-adventureworks-multilevel-faceting.md)