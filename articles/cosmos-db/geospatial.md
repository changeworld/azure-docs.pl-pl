---
title: Praca z danymi z danymi geograficznymi w konto interfejsu API SQL usługi Azure Cosmos DB
description: Dowiedz się, jak tworzyć, indeksu i zapytań przestrzennych obiektów za pomocą usługi Azure Cosmos DB i interfejsu API SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1654ccabab751896fcbbab09d112080ccc2f67b6
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467728"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Dane lokalizacji geograficznych i GeoJSON za pomocą konta interfejsu API SQL usługi Azure Cosmos DB

Ten artykuł stanowi wprowadzenie do funkcji geoprzestrzennych w usłudze Azure Cosmos DB. Obecnie przechowywania i uzyskiwania dostępu do danych dane geograficzne jest obsługiwany przez tylko konta interfejsu API SQL usługi Cosmos DB. Po przeczytaniu tego artykułu, możliwe będzie odpowiadać na następujące pytania:

* Jak przechowywać dane przestrzenne w usłudze Azure Cosmos DB?
* Jak tworzyć zapytania dane geograficzne w usłudze Azure Cosmos DB SQL i LINQ
* Jak włączyć lub wyłączyć indeksowania przestrzennego w usłudze Azure Cosmos DB?

W tym artykule przedstawiono sposób pracy z danymi przestrzennymi przy użyciu interfejsu API SQL. Zobacz ten [projektu GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) dla przykładów kodu.

## <a name="introduction-to-spatial-data"></a>Wprowadzenie do danych przestrzennych
Dane przestrzenne w tym artykule opisano położenie i kształt obiektów w przestrzeni. W większości aplikacji te odnoszą się do obiektów na ziemi i dane geograficzne danych. Dane przestrzenne może służyć do reprezentowania lokalizacji osoby, miejsca lub granic mieście lub usługi Data lake. Typowe przypadki użycia często obejmują zapytań dotyczących odległości, na przykład, "Znajdź wszystkich kawiarnie blisko swojej bieżącej lokalizacji." 

### <a name="geojson"></a>GeoJSON
Usługa Azure Cosmos DB obsługuje indeksowanie i wykonywanie zapytań z danymi geograficznymi punktu danych, które jest reprezentowane za pomocą [specyfikacją GeoJSON](https://tools.ietf.org/html/rfc7946). GeoJSON struktury danych są zawsze prawidłowe obiekty JSON, dzięki czemu mogą być przechowywane i wyświetlić przy użyciu usługi Azure Cosmos DB bez specjalistycznych narzędzi i bibliotek. Zestawów SDK usługi Azure Cosmos DB zapewniają pomocnika klasy i metody, które ułatwiają pracę z danymi przestrzennymi. 

### <a name="points-linestrings-and-polygons"></a>Punkty, LineStrings i wielokątów
A **punktu** oznacza jednej pozycji w miejscu. W danych geoprzestrzennych punkt reprezentuje dokładnej lokalizacji, co może być adres magazynu połówce, kiosku, samochodów i Miasto.  Punkt jest reprezentowana w parę GeoJSON (i usługi Azure Cosmos DB) przy użyciu jego współrzędnych lub długość i szerokość geograficzną. Oto przykładowy plik JSON dla punktu.

**Punkty w usłudze Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> Specyfikacją GeoJSON określa długość geograficzna pierwszy i szerokości geograficznej drugiego. Podobnie jak w innych aplikacjach mapowania długości i szerokości geograficznej są kąty i reprezentowane w stopniach. Wartości długości geograficznej jest mierzony od południków Prime należą do zakresu od stopni-180 i 180.0 stopni i wartości szerokości geograficznej jest mierzony od równika i należą do zakresu od stopni-90.0 i 90.0 stopni. 
> 
> Usługa Azure Cosmos DB interpretuje współrzędne reprezentowane na WGS 84 systemu odniesienia. Poniżej zamieszczono więcej informacji na temat systemów współrzędnych odniesienia.
> 
> 

To mogą być osadzone w dokumencie usługi Azure Cosmos DB, jak pokazano w tym przykładzie zawierający dane do lokalizacji profilu użytkownika:

**Użyj profilu z lokalizacją przechowywanych w usłudze Azure Cosmos DB**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

Oprócz punktów GeoJSON obsługuje również LineStrings i wielokątów. **LineStrings** reprezentują dwa lub więcej punktów w miejsce i segmentów linii, które łączą te elementy. W danych geoprzestrzennych LineStrings są często używane do reprezentowania drogami lub rzek. A **wielokąta** granicę połączone punkty, który wchodzi w skład LineString zamknięte. Wielokąty są często używane do reprezentowania formacji fizyczne, takie jak lakes lub polityczną jurysdykcjach, takich jak miast i stanów. Oto przykład wielokąta w usłudze Azure Cosmos DB. 

**Wielokąty w GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> Specyfikacją GeoJSON wymaga, aby uzyskać prawidłowy wielokątów ostatnią parę współrzędnych podane powinien taka sama jak pierwsza, aby utworzyć kształt zamknięty.
> 
> Punkty, w ramach Wielokąt musi być określona w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt podane w kolejności do ruchu wskazówek zegara reprezentuje odwrotność region znajdujący się w nim.
> 
> 

Oprócz punktu, LineString i wielokąta GeoJSON również określa reprezentację sposób grupowania wielu lokalizacji geograficznych, a także jak skojarzyć dowolne właściwości z geolokalizacji jako **funkcji**. Ponieważ te obiekty są prawidłowym kodem JSON, ich można wszystkie przechowywane i przetwarzane w usłudze Azure Cosmos DB. Jednak usługa Azure Cosmos DB obsługuje tylko, automatycznego indeksowania punktów.

### <a name="coordinate-reference-systems"></a>Koordynowanie systemów odniesienia
Ponieważ kształt ziemi jest nieprawidłowa, współrzędne dane geograficzne są reprezentowane w wielu systemach odwołanie współrzędnych (CRS), każda z ich własnymi ramek odniesienia i jednostek miary. Na przykład "National siatki z Brytanii" jest systemu odniesienia dokładne dla Wielkiej Brytanii, ale nie poza nim. 

Najbardziej popularne używana już dziś jest System geodezyjny World [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Urządzenia GPS i wieloma usługami mapowania, w tym map Google i interfejsów API usługi mapy Bing używają WGS 84. Usługa Azure Cosmos DB obsługuje indeksowanie i wyszukiwanie geoprzestrzenne danych przy użyciu tylko za pomocą CRS WGS 84. 

## <a name="creating-documents-with-spatial-data"></a>Tworzenie dokumentów z danymi przestrzennymi
Podczas tworzenia dokumentów, które zawierają wartości GeoJSON one są zgodnie z zasad indeksowania kontenera automatycznie indeksowane za pomocą indeksu przestrzennego. Jeśli pracujesz za pomocą usługi Azure Cosmos DB zestawu SDK w języku o typach określanych dynamicznie, takich jak Python lub Node.js, należy utworzyć prawidłowy GeoJSON.

**Utwórz dokument za pomocą danych geograficznych w środowisku Node.js**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Jeśli pracujesz z interfejsami API SQL, możesz użyć `Point` i `Polygon` klas w obrębie `Microsoft.Azure.Documents.Spatial` przestrzeni nazw do osadzenia informacji o lokalizacji w ramach obiektów aplikacji. W ramach tych zajęć ułatwiają serializacji i deserializacji danych przestrzennych w GeoJSON.

**Utwórz dokument za pomocą danych geograficznych w .NET**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

Jeśli nie masz informacji o szerokości geograficznej i długości geograficznej, ale masz adresy fizyczne lub lokalizacje, takie jak miasto lub kraj/region, można wyszukiwać rzeczywiste współrzędne przy użyciu usługi geokodowania, takiej jak usługi w usłudze mapy Bing. Dowiedz się więcej o geokodowanie map Bing [tutaj](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Wykonywanie zapytania dotyczącego typów przestrzennych
Teraz, że zrobiliśmy przyjrzeć się jak wstawić dane geoprzestrzenne, Spójrzmy na sposób tworzenia zapytań względem tych danych za pomocą usługi Azure Cosmos DB przy użyciu języków SQL i LINQ.

### <a name="spatial-sql-built-in-functions"></a>Wbudowane funkcje przestrzenne programu SQL
Usługa Azure Cosmos DB obsługuje następujące funkcje wbudowane Otwórz dane geograficzne Consortium (OGC) do wykonywania zapytań na danych geoprzestrzennych. Aby uzyskać więcej informacji na temat pełnego zestawu funkcji wbudowanych w języku SQL, zobacz [zapytań usługi Azure Cosmos DB](how-to-sql-query.md).

|**Użycie**|**Opis**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Zwraca odległość między dwoma wyrażeniami GeoJSON typu Point, Polygon lub LineString.|
|ST_WITHIN (spatial_expr, spatial_expr) | Zwraca wyrażenie warunkowe wskazujące, czy pierwszy obiekt GeoJSON (Point, Polygon lub LineString) znajduje się w drugim obiekcie GeoJSON (Point, Polygon lub LineString).|
|ST_INTERSECTS (wyrażenie_przestrzenne, wyrażenie_przestrzenne)| Zwraca wyrażenie warunkowe wskazujące, czy dwa określone obiekty GeoJSON (Point, Polygon lub LineString) przecinają się.|
|ST_ISVALID| Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe.|
| ST_ISVALIDDETAILED| Zwraca wartość JSON zawierającą wartość logiczną, jeśli określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest prawidłowe, a jeśli jest nieprawidłowe, dodatkowo zwraca przyczynę jako wartość ciągu.|

Funkcje przestrzenne mogą być używane do wykonywania zapytań dotyczących odległości względem danych przestrzennych. Na przykład w tym miejscu jest zapytanie, które zwraca wszystkie rodziny dokumenty, które znajdują się w 30 km określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE. 

**Zapytanie**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Jeśli dołączysz indeksowania przestrzennego w zasady indeksowania, następnie "odległość zapytania" będzie ich obsługa jest wydajna za pomocą indeksu. Aby uzyskać więcej informacji dotyczących indeksowania przestrzennego zobacz sekcję poniżej. Jeśli nie masz indeks przestrzenny dla określonych ścieżek, można wykonywać zapytań przestrzennych, określając `x-ms-documentdb-query-enable-scan` nagłówek żądania z ustawioną wartość "true". Na platformie .NET, można to zrobić, przekazując opcjonalny **FeedOptions** argument zapytań przy użyciu [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) ma wartość true. 

ST_WITHIN może służyć do sprawdzania, jeśli punkt znajduje się w obrębie wielokąta. Często wielokątów są używane do reprezentowania granice, takie jak kodów pocztowych, granice stanu lub formacji fizycznych. Ponownie Jeśli dołączysz indeksowania przestrzennego w zasady indeksowania, następnie "w ciągu" zapytania będą ich obsługa jest wydajna za pomocą indeksu. 

Argumenty wielokąta w ST_WITHIN może zawierać tylko jednego pierścień, czyli wielokątów nie mogą zawierać luki w nich. 

**Zapytanie**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**Results**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Podobnie jak niedopasowanych pracy typów w zapytaniu usługi Azure Cosmos DB, gdy wartość lokalizacji określone w albo argument jest źle sformułowany lub nieprawidłowy, a następnie daje w wyniku **niezdefiniowane** i ocenianą dokument do pominięcia z wyników kwerendy. Jeśli zapytanie nie zwraca żadnych wyników, uruchom ST_ISVALIDDETAILED do debugowania Dlaczego przestrzenne typu jest nieprawidłowy.     
> 
> 

Usługa Azure Cosmos DB obsługuje także wykonywanie kwerendy odwrotne, oznacza to, że możesz indeksowanie, wielokąty lub wiersze w usłudze Azure Cosmos DB, a następnie zapytanie dla obszarów, które zawierają określony punkt. Ten wzorzec często służy logistycznych do identyfikowania, na przykład podczas ciężarówki wprowadza lub go opuszcza wyznaczony obszar. 

**Zapytanie**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**Results**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID i ST_ISVALIDDETAILED może służyć do sprawdzania, czy obiektu przestrzennego jest nieprawidłowy. Na przykład następujące zapytanie sprawdza ważność punkt z poza zakresu wartość szerokości geograficznej (-132.8). ST_ISVALID zwraca wartość logiczną, natomiast ST_ISVALIDDETAILED typu Boolean i ciąg zawierający powód, dlaczego jest uznawane za nieprawidłowe.

**Zapytanie**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Te funkcje można również sprawdzić wielokątów. Na przykład tutaj używamy ST_ISVALIDDETAILED do sprawdzania poprawności wielokąta, który nie jest zamknięty. 

**Zapytanie**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**Results**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>LINQ, wykonywanie zapytań w zestawie SDK platformy .NET
Zestaw SDK platformy .NET SQL również dostawców namiastki metody `Distance()` i `Within()` do użycia w wyrażeniach zapytań LINQ. Dostawca LINQ do SQL tłumaczy tej metody wywołania równoważne wywołań funkcji wbudowanej SQL (ST_DISTANCE i ST_WITHIN odpowiednio). 

Poniżej przedstawiono przykładowe zapytanie LINQ, które znajdzie wszystkie dokumenty w kolekcji usługi Azure Cosmos DB, w której wartość "Lokalizacja" jest w promieniu 30 km określonego punktu za pomocą LINQ.

**Zapytania LINQ dla odległości**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Podobnie w tym miejscu jest zapytania służące do znajdowania wszystkich dokumentów, w których "Lokalizacja" znajduje się w określonym polu/wielokąta. 

**W ciągu kwerendy LINQ**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


Skoro zrobiliśmy poznać sposób wykonywania zapytań dla dokumentów za pomocą LINQ i SQL, Spójrzmy na sposób konfigurowania usługi Azure Cosmos DB dla indeksowania przestrzennego.

## <a name="indexing"></a>Indeksowanie
Zgodnie z opisem firma Microsoft w [schemat niezależny od indeksowania za pomocą usługi Azure Cosmos DB](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) papieru, został zaprojektowany aparatu bazy danych Azure Cosmos DB to naprawdę niezależnej od schematu i zapewnienia pierwszorzędnej obsługi dla formatu JSON. Aparat bazy danych zoptymalizowany pod kątem zapisu w usłudze Azure Cosmos DB natywną obsługę danych przestrzennych (punkty, wielokąty i wierszy), reprezentowana w standardzie GeoJSON.

Mówiąc, przewidywany geodezyjnej współrzędne na płaszczyznę 2D następnie stopniowo dzielą komórek przy użyciu geometrii **obszaru quadtree**. Tych komórek są mapowane na 1D na podstawie lokalizacji komórkę w **krzywej wypełnianie miejsca Hilberta**, który zachowuje lokalizacja punktów. Ponadto po zindeksowaniu danych lokalizacji, przechodzi on przez proces ten jest znany jako **mozaikowania**, oznacza to, że wszystkie komórki, które przecinają lokalizacji zostały zidentyfikowane i są przechowywane jako klucze do indeksu usługi Azure Cosmos DB. Podczas przeszukiwania argumentów, takich jak punktów i wielokątów są również tesselowaną można wyodrębnić zakresów identyfikator odpowiednich komórek, a następnie używane do pobierania danych z indeksu.

Jeśli określisz zasady indeksowania, która zawiera indeks przestrzenny dla / * (wszystkie ścieżki), a następnie wszystkie punkty można znaleźć w kolekcji są indeksowane w celu efektywnego zapytań przestrzennych (ST_WITHIN i ST_DISTANCE). Indeksy przestrzenne nie mają wartość dokładności i zawsze używaj wartości dokładności domyślnej.

> [!NOTE]
> Usługa Azure Cosmos DB obsługuje automatyczne indeksowanie punktów i wielokątów, a LineStrings
> 
> 

Poniższy fragment kodu JSON zawiera zasady indeksowania za pomocą indeksowania przestrzennego włączone, to znaczy, indeks kiedykolwiek GeoJSON można znaleźć w dokumentach zapytań przestrzennych. W przypadku modyfikowania zasad indeksowania, przy użyciu witryny Azure portal, można określić następujące dane JSON dla zasad indeksowania umożliwiające przestrzennych, indeksowania w kolekcji.

**Kolekcja indeksowania zasad JSON z przestrzennym włączone dla punktów i wielokątów**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

Poniżej przedstawiono fragment kodu na platformie .NET, który pokazuje, jak utworzyć kolekcję z indeksowania przestrzennego włączona dla wszystkich ścieżek zawierających punkty. 

**Utwórz kolekcję o indeksowania przestrzennego**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

A Oto, jak można zmodyfikować istniejącą kolekcję z zalet indeksowania przestrzennego w żadnym z punktów, które są przechowywane w dokumentach.

**Modyfikowanie istniejącej kolekcji za pomocą indeksowania przestrzennego**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> Jeśli lokalizacja GeoJSON wartości w obrębie dokumentu jest źle sformułowany lub nieprawidłowy, następnie go będzie nie indeksowanie zapytań przestrzennych. Można sprawdzić poprawność wartości lokalizacji przy użyciu ST_ISVALID i ST_ISVALIDDETAILED.
> 
> Jeśli definicji kolekcja zawiera klucz partycji, indeksowanie postępu transformacji nie został zgłoszony. 
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy wiesz jak rozpocząć pracę z obsługą dane geograficzne w usłudze Azure Cosmos DB, następnie można wykonywać następujące czynności:

* Rozpocznij kodowanie dzięki [przykładów kodu .NET dane geograficzne w usłudze GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Zapoznaj się z danymi geograficznymi zapytań w [Plac zabaw dla usługi Azure Cosmos DB zapytań](https://www.documentdb.com/sql/demo#geospatial)
* Dowiedz się więcej o [usługi Azure Cosmos DB zapytania](how-to-sql-query.md)
* Dowiedz się więcej o [zasad indeksowania usługi Azure Cosmos DB](index-policy.md)

