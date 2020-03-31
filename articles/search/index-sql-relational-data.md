---
title: Modelowanie danych relacyjnych SQL do importowania i indeksowania
titleSuffix: Azure Cognitive Search
description: Dowiedz się, jak modelować dane relacyjne, de-znormalizowane do płaskiego zestawu wyników, do indeksowania i wyszukiwania pełnotekstowego w usłudze Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3b973dd05d23d190c77986ca9bf6d39656739cd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790094"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Jak modelować relacyjnych danych SQL do importowania i indeksowania w usłudze Azure Cognitive Search

Usługa Azure Cognitive Search akceptuje płaski zestaw wierszy jako dane wejściowe do [potoku indeksowania.](search-what-is-an-index.md) Jeśli dane źródłowe pochodzą z tabel sprzężonych w relacyjnej bazie danych programu SQL Server, w tym artykule wyjaśniono, jak skonstruować zestaw wyników i jak modelować relację nadrzędny-podrzędny w indeksie usługi Azure Cognitive Search.

Jako ilustrację, będziemy odnosić się do hipotetycznej bazy danych hoteli, w oparciu o [dane demo](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Załóżmy, że baza danych składa się z tabeli Hotels$ z 50 hotelami i tabeli Pokoje $ z pokojami różnego rodzaju, stawkami i udogodnieniami, w sumie 750 pokoi. Istnieje relacja jeden do wielu między tabelami. W naszym podejściu widok zapewni kwerendę, która zwraca 50 wierszy, jeden wiersz na hotel, ze skojarzonymi szczegółami pokoju osadzonymi w każdym wierszu.

   ![Tabele i wyświetlanie w bazie danych hoteli](media/index-sql-relational-data/hotels-database-tables-view.png "Tabele i wyświetlanie w bazie danych hoteli")


## <a name="the-problem-of-denormalized-data"></a>Problem z denormalizowanych danych

Jednym z wyzwań w pracy z relacji jeden do wielu jest, że standardowe zapytania oparte na tabelach sprzężonych zwróci zdenormalizowane dane, które nie działa dobrze w scenariuszu usługi Azure Cognitive Search. Rozważmy poniższy przykład, który łączy hotele i pokoje.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Wyniki tego zapytania zwracają wszystkie pola hotelowe, a następnie wszystkie pola pokoju, ze wstępnymi informacjami hotelowymi powtarzającymi się dla każdej wartości pokoju.

   ![Dane zdenormalizowane, nadmiarowe dane hotelowe po dodaniu pól pokoju](media/index-sql-relational-data/denormalize-data-query.png "Dane zdenormalizowane, nadmiarowe dane hotelowe po dodaniu pól pokoju")


Podczas gdy ta kwerenda powiedzie się na powierzchni (podając wszystkie dane w zestawie wierszy płaskich), kończy się niepowodzeniem w dostarczaniu odpowiedniej struktury dokumentu dla oczekiwanego środowiska wyszukiwania. Podczas indeksowania usługa Azure Cognitive Search utworzy jeden dokument wyszukiwania dla każdego wynajętego wiersza. Gdyby twoje dokumenty wyszukiwania wyglądały jak powyższe wyniki, można by dostrzec duplikaty - siedem oddzielnych dokumentów dla samego hotelu Twin Dome. Zapytanie o "hotele na Florydzie" zwróci siedem wyników tylko dla hotelu Twin Dome, przesuwając inne odpowiednie hotele w głąb wyników wyszukiwania.

Aby uzyskać oczekiwane doświadczenie jednego dokumentu na hotel, należy podać zestaw wierszy na prawo ziarnistość, ale z pełnymi informacjami. Na szczęście można to łatwo zrobić, przyjmując techniki w tym artykule.

## <a name="define-a-query-that-returns-embedded-json"></a>Definiowanie kwerendy zwracającej osadzone JSON

Aby zapewnić oczekiwane środowisko wyszukiwania, zestaw danych powinien składać się z jednego wiersza dla każdego dokumentu wyszukiwania w usłudze Azure Cognitive Search. W naszym przykładzie chcemy po jednym wierszu dla każdego hotelu, ale chcemy również, aby nasi użytkownicy mogli wyszukiwać inne pola związane z pomieszczeniami, na których im zależy, takie jak stawka za noc, rozmiar i liczba łóżek lub widok na plażę, z których wszystkie są częścią szczegółów pokoju.

Rozwiązaniem jest przechwycenie szczegółów pomieszczenia jako zagnieżdżonego JSON, a następnie wstawienie struktury JSON do pola w widoku, jak pokazano w drugim kroku. 

1. Załóżmy, że masz dwa połączone stoły, Hotels$ i Rooms$, które zawierają szczegóły dotyczące 50 hoteli i 750 pokoi, i są połączone na polu HotelID. Indywidualnie tabele te zawierają 50 hoteli i 750 powiązanych pokoi.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Utwórz widok składający się ze`SELECT * from dbo.Hotels$`wszystkich pól w tabeli nadrzędnej ( ), z dodatkiem nowego pola *Pokoje,* które zawiera dane wyjściowe kwerendy zagnieżdżonej. Klauzula **DLA JSON AUTO** na `SELECT * from dbo.Rooms$` struktury danych wyjściowych jako JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Poniższy zrzut ekranu przedstawia wynikowy widok z polem *Pokoje* nvarchar na dole. Pole *Pokoje* istnieje tylko w widoku HotelRooms.

   ![Widok na pokoje hotelowe](media/index-sql-relational-data/hotelsrooms-view.png "Widok HoteRooms")

1. Uruchom, `SELECT * FROM dbo.HotelRooms` aby pobrać zestaw wierszy. Ta kwerenda zwraca 50 wierszy, po jednym na hotel, ze skojarzonymi informacjami o pokoju jako kolekcją JSON. 

   ![Zestaw rowów z widoku HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Zestaw rowów z widoku HotelRooms")

Ten zestaw wierszy jest teraz gotowy do zaimportowania do usługi Azure Cognitive Search.

> [!NOTE]
> Takie podejście zakłada, że osadzone JSON jest w [maksymalnych limitów rozmiaru kolumny programu SQL Server](https://docs.microsoft.com/sql/sql-server/maximum-capacity-specifications-for-sql-server). Jeśli dane nie pasują, możesz wypróbować podejście programowe, jak pokazano na [przykładzie: Modelowanie bazy danych AdventureWorks Inventory dla usługi Azure Cognitive Search.](search-example-adventureworks-modeling.md)

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Użyj złożonej kolekcji dla "wielu" stron relacji jeden-do-wielu

Po stronie usługi Azure Cognitive Search utwórz schemat indeksu, który modeluje relację jeden do wielu przy użyciu zagnieżdżonego JSON. Zestaw wyników utworzony w poprzedniej sekcji zazwyczaj odpowiada schematowi indeksu podanemu poniżej (wycinamy niektóre pola dla zwięzłości).

Poniższy przykład jest podobny do przykładu w [jak modelować złożone typy danych](search-howto-complex-data-types.md#creating-complex-fields). *Struktura Pokoje,* która była przedmiotem tego artykułu, znajduje się w zbiorach pól indeksu o nazwie *hotele.* W tym przykładzie pokazano również typ złożony *adresu*, który różni się od *Pokoje* tym, że składa się ze stałego zestawu elementów, w przeciwieństwie do wielu, dowolną liczbę elementów dozwolonych w kolekcji.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Biorąc pod uwagę poprzedni zestaw wyników i powyższy schemat indeksu, masz wszystkie składniki wymagane dla pomyślnej operacji indeksowania. Spłaszczony zestaw danych spełnia wymagania dotyczące indeksowania, ale zachowuje szczegółowe informacje. W indeksie usługi Azure Cognitive Search wyniki wyszukiwania łatwo należą do jednostek hotelowych, zachowując kontekst poszczególnych pomieszczeń i ich atrybuty.

## <a name="next-steps"></a>Następne kroki

Korzystając z własnego zestawu danych, można użyć [Kreatora importu danych](search-import-data-portal.md) do utworzenia i załadowania indeksu. Kreator wykrywa osadzoną kolekcję JSON, taką jak ta zawarta w *pokojach,* i wylicza schemat indeksu zawierający złożoną kolekcję typów. 

  ![Indeks wywnioskowany przez Kreatora importu danych](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Indeks wywnioskowany przez Kreatora importu danych")

Skorzystaj z następującego przewodnika Szybki start, aby zapoznać się z podstawowymi krokami kreatora importowania danych.

> [!div class="nextstepaction"]
> [Szybki start: tworzenie indeksu wyszukiwania przy użyciu witryny Azure portal](search-get-started-portal.md)