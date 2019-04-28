---
title: 'Przykład: Model bazy danych AdventureWorks spisu — usługa Azure Search'
description: Dowiedz się, jak dane relacyjne, przekształcania go w spłaszczony zestaw danych, indeksowania i pełne wyszukiwanie tekstu w usłudze Azure Search modelu.
author: cstone
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: chstone
ms.openlocfilehash: 6d5d01dfbbcfda56818f5c38b06117a87e021445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291912"
---
# <a name="example-model-the-adventureworks-inventory-database-for-azure-search"></a>Przykład: Model bazy danych AdventureWorks magazynu na potrzeby usługi Azure Search

Modelowanie ze strukturą zawartości bazy danych do indeksu wyszukiwania wydajne rzadko jest proste wykonywanie. Planowanie i zarządzanie zmianami specjalnie, istnieje coraz denormalizing wierszy źródłowych od ich stanu dołączone do tabeli do jednostek przyjaznego dla wyszukiwania. W tym artykule używa AdventureWorks przykładowych danych, dostępne w trybie online, aby wyróżnić wspólnego środowiska w stanie przejścia z bazy danych do wyszukiwania. 

## <a name="about-adventureworks"></a>Temat AdventureWorks

Jeśli korzystasz z wystąpienia programu SQL Server, można zapoznać się z przykładową bazę danych AdventureWorks. Między tabelami zawarte w tej bazie danych są znajduje się pięć tabel, które udostępniają informacje o produkcie.

+ **ProductModel**: Nazwa
+ **Produkt**: nazwę, kolorów, kosztów, rozmiar, wagi, obraz, kategorii (każdy wiersz dołącza do określonych ProductModel)
+ **ProductDescription**: opis
+ **ProductModelProductDescription**: ustawienia regionalne (każdy wiersz dołącza ProductModel do określonych ProductDescription dla określonego języka)
+ **ProductCategory**: nazwa, Kategoria nadrzędna

Połączenie wszystkich tych danych do spłaszczonych zestawu wierszy, które mogą być pozyskiwane do indeksu wyszukiwania jest wykonywanego zadania. 

## <a name="considering-our-options"></a>Biorąc pod uwagę nasze możliwości

Podejście naiwni będzie, indeksowanie wszystkich wierszy z tabeli produktu (połączone z odpowiednim) od tabeli produktu ma bardziej konkretny od pozostałych informacji. Jednak takie podejście może narazić indeksów wyszukiwania, aby postrzegany duplikaty zestaw wyników. Na przykład model 650 drogowej jest dostępny w dwóch kolorów i sześć rozmiarów. Zapytanie "szosowe" będzie można następnie zdominowany przez dwanaście wystąpień tego samego modelu, zróżnicowane tylko według rozmiaru i koloru. Sześć inne modele specyficzne drogowym będą wszystkie można były odpowiedzialne świata narzędzia nether wyszukiwania: dwie strony.

  ![Lista produktów](./media/search-example-adventureworks/products-list.png "listy produktów")
 
Zwróć uwagę, model 650 drogowej nie ma opcji dwunastu. Jednostki jeden do wielu wierszy najlepiej są reprezentowane jako pola wielowartościowe lub wstępnej aggregated wartości pól w indeksie wyszukiwania.

Nie jest równie proste jak przenoszenie indeksu docelowego do tabeli ProductModel rozwiązania tego problemu. Ten sposób będzie ignorować ważne dane w tabeli Product, nadal powinny być reprezentowane w wynikach wyszukiwania.

## <a name="use-a-collection-data-type"></a>Użyj typu danych kolekcji

"Właściwe podejście" jest korzystanie z funkcji schemat wyszukiwania, który nie ma bezpośredniego równoległe w modelu bazy danych: **Collection(EDM.String)**. Typ danych kolekcji jest używany w przypadku listy poszczególnych ciągów, a nie bardzo długi ciąg (pojedynczą). Jeśli masz tagów lub słowa kluczowe, należy użyć typu danych kolekcji dla tego pola.

Definiując pól wielowartościowych indeksu **Collection(Edm.String)** "color", "rozmiar" i "obraz" pomocniczych informacje są zachowane dla tworzenia kategorii wyszukanych informacji i filtrowania bez zanieczyszczenie indeksu o zduplikowane wpisy. Podobnie, zastosowanie funkcji agregujących do pól liczbowych produktu indeksowania **minListPrice** zamiast każdego pojedynczego produktu **listPrice**.

Podany indeks z tych struktur, wyszukaj "rowery górskie", zostanie wyświetlony modeli dyskretnych bicycle przy jednoczesnym zachowaniu ważne metadane, takie jak kolor, rozmiar i najniższa cena. Poniższy zrzut ekranu stanowi ilustrację.

  ![Przykład wyszukiwania rowerów górski](./media/search-example-adventureworks/mountain-bikes-visual.png "przykładowe wyszukiwanie rowerów górski")

## <a name="use-script-for-data-manipulation"></a>Użyj skryptu dla manipulowanie danymi

Niestety tego rodzaju modelowania nie można z łatwością przeprowadzić przy użyciu instrukcji SQL samodzielnie. W zamian użyj prosty skrypt NodeJS do ładowania danych i jest mapowany do jednostek JSON przyjaznego dla wyszukiwania.

Mapowanie końcowego — wyszukiwanie w bazie danych wygląda następująco:

+ Model (Edm.String: wyszukiwanie, filtrowanie, pobieranie) z "ProductModel.Name"
+ description_en (Edm.String: wyszukiwanie) z "ProductDescription" dla modelu gdzie kulturze = "en"
+ kolor (Collection(Edm.String): wyszukiwanie, filtrowanie, aspektów, możliwe do pobierania): niepowtarzalne wartości z "Product.Color" dla modelu
+ rozmiar (Collection(Edm.String): wyszukiwanie, filtrowanie, aspektów, możliwe do pobierania): niepowtarzalne wartości z "Product.Size" dla modelu
+ Obraz (Collection(Edm.String): pobieranie): niepowtarzalne wartości z "Product.ThumbnailPhoto" dla modelu
+ minStandardCost (Edm.Double: filtrowanie, tworzenie aspektów, sortowanie, możliwe do pobierania): co najmniej agregacji wszystkich "Product.StandardCost" dla modelu
+ minListPrice (Edm.Double: filtrowanie, tworzenie aspektów, sortowanie, możliwe do pobierania): co najmniej agregacji wszystkich "Product.ListPrice" dla modelu
+ minWeight (Edm.Double: filtrowanie, tworzenie aspektów, sortowanie, możliwe do pobierania): co najmniej agregacji wszystkich "Product.Weight" dla modelu
+ produkty (Collection(Edm.String): wyszukiwanie, filtrowanie, możliwe do pobierania): niepowtarzalne wartości z "Product.Name" dla modelu

Po dołączeniu tabeli ProductModel przy użyciu produktów i ProductDescription, [lodash](https://lodash.com/) (x lub Linq w C#) do szybkiego transformowania zestawu wyników:

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

Ponadto w tym miejscu jest zapytanie SQL, aby zwrócić początkowego zestawu rekordów. Czy mogę używać [mssql](https://www.npmjs.com/package/mssql) moduł npm, załadować dane do mojej aplikacji node.js.

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

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przykład: Wielopoziomowe aspekt taksonomii w usłudze Azure Search](search-example-adventureworks-multilevel-faceting.md)


