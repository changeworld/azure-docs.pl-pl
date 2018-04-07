---
title: Praca z danymi dane geograficzne w usłudze Azure DB rozwiązania Cosmos | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu tworzenia, indeksu i zapytania przestrzennych obiektów z bazy danych rozwiązania Cosmos Azure i interfejsu API SQL.
services: cosmos-db
documentationcenter: ''
author: arramac
manager: kfile
ms.assetid: 82ce2898-a9f9-4acf-af4d-8ca4ba9c7b8f
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/20/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6a78426ed6b54f0227dbd89c150f23af712f1b35
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="working-with-geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Praca z dane geograficzne i danych lokalizacji GeoJSON w usłudze Azure DB rozwiązania Cosmos
Ten artykuł obejmuje wprowadzenie do funkcji geograficzne w [bazy danych Azure rozwiązania Cosmos](https://azure.microsoft.com/services/cosmos-db/). Po przeczytaniu tego, będzie mógł odpowiedzieć na następujące pytania:

* Sposób przechowywania danych przestrzennych w usłudze Azure DB rozwiązania Cosmos?
* Jak wykonać zapytanie dane geograficzne w usłudze Azure DB rozwiązania Cosmos w SQL i LINQ
* Jak włączyć lub wyłączyć indeksowanie przestrzenne w usłudze Azure DB rozwiązania Cosmos

W tym artykule przedstawiono sposób pracy z danymi przestrzennymi przy użyciu interfejsu API SQL. Zobacz to [projektu GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs) dla przykładów kodu.

## <a name="introduction-to-spatial-data"></a>Wprowadzenie do danych przestrzennych
Dane przestrzenne opisano pozycji i kształt obiektów w przestrzeni. W większości aplikacji odpowiadają one obiektów danych ziemi i dane geograficzne. Dane przestrzenne może służyć do reprezentowania lokalizacji osoby, miejsca lub granic miejscowości lub jeziora. Typowe przypadki użycia często obejmują zbliżeniowe kwerendy, na przykład, "Znajdź wszystkie kawiarniach niemal mojej bieżącej lokalizacji." 

### <a name="geojson"></a>GeoJSON
Azure DB rozwiązania Cosmos obsługuje indeksowanie i badania dane geograficzne punktu danych, które ma reprezentowanej przy użyciu [specyfikacji GeoJSON](https://tools.ietf.org/html/rfc7946). Struktury danych GeoJSON są zawsze prawidłowych obiektów JSON, dzięki czemu mogą być przechowywane i wyświetlić przy użyciu bazy danych Azure rozwiązania Cosmos bez żadnych specjalnych narzędzi i bibliotek. Zestawy SDK Azure rozwiązania Cosmos DB Podaj pomocnika klasy i metody, które ułatwiają pracę z danymi przestrzennymi. 

### <a name="points-linestrings-and-polygons"></a>Punkty, LineStrings i wielokątów
A **punktu** oznacza pojedynczego pozycję w przestrzeni. W danych geograficznych punkt reprezentuje dokładnej lokalizacji, która może być ulicę sklepów spożywczych, kiosku, samochodu lub miejscowości.  Punkt jest reprezentowana w pary GeoJSON (i bazy danych Azure rozwiązania Cosmos) przy użyciu jego współrzędnych lub długości i szerokości geograficznej. Oto przykład JSON dla punktu.

**Punkty Azure rozwiązania Cosmos bazy danych**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> Specyfikacja GeoJSON określa długość pierwszy i szerokości geograficznej drugiego. Podobnie jak w innych aplikacjach mapowania długości i szerokości geograficznej są kąty i wyświetlany w postaci stopni. Wartości długości geograficznej są mierzone od głównego południka i należą do zakresu od stopni-180 i 180.0 stopni i wartości szerokości geograficznej są mierzone od równika i należą do zakresu od stopni-90.0 i 90.0 stopni. 
> 
> Azure DB rozwiązania Cosmos interpretuje współrzędne reprezentowane na WGS 84 systemu odniesienia. Aby uzyskać więcej informacji o systemach współrzędnych odwołania są wymienione poniżej.
> 
> 

Można ją osadzić w dokumencie bazy danych Azure rozwiązania Cosmos opisane w tym przykładzie profilu użytkownika zawierającego dane dotyczące lokalizacji:

**Użyj profilu z lokalizacją przechowywane w usłudze Azure DB rozwiązania Cosmos**

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

Oprócz punktów GeoJSON obsługuje również LineStrings i wielokątów. **LineStrings** reprezentują dwa lub więcej punktów w miejsce i segmentów, które łączą te elementy. W polu dane geograficzne LineStrings często są używane do reprezentowania drogami lub rzek. A **wielokąta** jest granicę punktów połączonych formularzy LineString zamknięte. Wielokąty często są używane do reprezentowania tworzenie fizycznych, takich jak jeziora lub polityczną jurysdykcjach przykład miast i stanów. Oto przykład wielokąta w usłudze Azure DB rozwiązania Cosmos. 

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
> Specyfikacja GeoJSON wymaga, aby uzyskać prawidłowy wielokątów ostatnią parę współrzędnych podane powinien taka sama jak pierwsza strona, aby utworzyć zamknięty kształt.
> 
> Punktów w wielokąta muszą być określone w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt określony w kolejności zegara reprezentuje odwrotność regionu w niej.
> 
> 

Oprócz punktu, LineString i wielokąta GeoJSON określa również reprezentację sposób grupowania wielu lokalizacji geograficznych, a także sposobu kojarzenia dowolne właściwości z używanie funkcji geolokalizacji jako **funkcji**. Ponieważ te obiekty są poprawne dane JSON, ich można wszystkie przechowywane i przetwarzane w usłudze Azure DB rozwiązania Cosmos. Niemniej jednak bazy danych Azure rozwiązania Cosmos obsługuje tylko, automatycznego indeksowania punktów.

### <a name="coordinate-reference-systems"></a>Koordynacji systemów odniesienia
Ponieważ kształtu ziemi jest nieprawidłowa, współrzędne geograficzne dane są reprezentowane w wielu systemach współrzędnych odwołania (znaki CR), każdy z ramek odniesienia i jednostki miary. Na przykład "National siatki z Brytania" jest systemu odniesienia są bardzo dokładne Zjednoczone Królestwo, ale nie poza nią. 

Najbardziej popularnym używany obecnie jest System geodezyjny World [WGS 84](http://earth-info.nga.mil/GandG/wgs84/). Urządzenia GPS, a wiele usług mapowania tym map programu Google i interfejsów API map Bing Użyj WGS 84. Azure DB rozwiązania Cosmos obsługuje indeksowanie i wyszukiwanie przy użyciu CRS WGS 84 w tylko dane geograficzne. 

## <a name="creating-documents-with-spatial-data"></a>Tworzenie dokumentów z danymi przestrzennymi
Podczas tworzenia dokumentów, które zawierają wartości GeoJSON one są automatycznie indeksowane z indeksu przestrzennego w zgodnie z zasadami indeksowania w kolekcji. Podczas pracy z zestawem Azure rozwiązania Cosmos DB SDK w typach określanych dynamicznie języka Python lub Node.js, należy utworzyć prawidłowy GeoJSON.

**Utwórz dokument z dane geograficzne w środowisku Node.js**

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

Podczas pracy z interfejsami API zestawu SQL, można użyć `Point` i `Polygon` klas w ramach `Microsoft.Azure.Documents.Spatial` przestrzeni nazw osadzanie informacji o lokalizacji w obrębie obiektów aplikacji. Te klasy ułatwić serializacji i deserializacji obiektu danych przestrzennych w GeoJSON.

**Utwórz dokument z dane geograficzne w .NET**

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

Jeśli nie ma informacji współrzędne geograficzne, ale ma adresów fizycznych ani nazwy lokalizacji, takiej jak nazwa miejscowości lub kraj, rzeczywiste współrzędne można wyszukiwać za pomocą usług geokodowanie, takich jak usługi REST mapy Bing. Więcej informacji na temat usługi mapy Bing geokodowanie [tutaj](https://msdn.microsoft.com/library/ff701713.aspx).

## <a name="querying-spatial-types"></a>Wykonywanie zapytania typów przestrzennych
Teraz, gdy firma Microsoft zajęło przyjrzeć się jak wstawić dane geograficzne, Spójrzmy na sposób tworzenia zapytań te dane przy użyciu bazy danych rozwiązania Cosmos Azure przy użyciu programu SQL i LINQ.

### <a name="spatial-sql-built-in-functions"></a>Przestrzenne wbudowanych funkcji SQL
Azure DB rozwiązania Cosmos obsługuje następujące funkcje wbudowane Otwórz geograficzne konsorcjum (OGC) na potrzeby zapytań o dane geograficzne. Aby uzyskać więcej szczegółów na pełny zestaw funkcji wbudowanych w języku SQL, zobacz [kwerendy bazy danych z rozwiązania Cosmos Azure](sql-api-sql-query.md).

<table>
<tr>
  <td><strong>Użycie</strong></td>
  <td><strong>Opis</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (spatial_expr, spatial_expr)</td>
  <td>Zwraca odległość między dwoma wyrażeniami GeoJSON punktu wielokąta i LineString.</td>
</tr>
<tr>
  <td>ST_WITHIN (spatial_expr, spatial_expr)</td>
  <td>Zwraca wyrażenie logiczne, wskazującą, czy pierwszy obiekt GeoJSON (punkt, wielokąta lub LineString) znajduje się w drugim obiektu GeoJSON (punkt, wielokąta lub LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Zwraca wartość wskazującą, czy dwa określonych obiektów GeoJSON (punkt, wielokąta lub LineString) intersect wyrażenie logiczne.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON punktu wielokąta i LineString jest nieprawidłowy.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Zwraca wartość wartość JSON, zawierające wartość typu Boolean, jeśli określone wyrażenie GeoJSON punktu wielokąta i LineString jest prawidłowy, a nieprawidłowy, ponadto Przyczyna jako wartość typu ciąg.</td>
</tr>
</table>

Funkcje przestrzenne może służyć do wykonywania zapytań zbliżeniowe względem danych przestrzennych. Na przykład w tym miejscu jest kwerendę, która zwraca wszystkie dokumenty rodziny, które są w ciągu 30 km do określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE. 

**Zapytanie**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Results**

    [{
      "id": "WakefieldFamily"
    }]

Jeśli dołączysz przestrzennych indeksowania w zasady indeksowania, następnie "odległość zapytania" zostanie obsłużona wydajnie przez indeks. Więcej szczegółów na indeksowanie przestrzennych zobacz sekcję poniżej. Jeśli nie masz indeks przestrzenny dla określonej ścieżki, można wykonywać zapytania przestrzennych, określając `x-ms-documentdb-query-enable-scan` nagłówek żądania z ustawioną wartość "true". W środowisku .NET, można to zrobić przez przekazanie opcjonalny **FeedOptions** argument zapytania z [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) ustawioną na true. 

ST_WITHIN może służyć do sprawdzenia, czy punkt znajduje się wewnątrz wielokąta. Często wielokątów są używane do reprezentowania granice, takimi jak kodów pocztowych, stan granice lub tworzenie fizycznych. Ponownie Jeśli przestrzennych indeksowania w zasady indeksowania, następnie "w" zapytania zostanie obsłużona wydajnie przez indeks. 

Argumenty wielokąta ST_WITHIN może zawierać tylko jeden pierścień, czyli wielokątów nie może zawierać luk w nich. 

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
> Podobne jak niedopasowanych pracy typów w zapytaniu bazy danych rozwiązania Cosmos platformy Azure, jeśli określona wartość lokalizacji w albo argument jest źle sformułowany lub nieprawidłowy, a następnie daje w wyniku **Niezdefiniowany** i obliczane dokumentu do pominięcia z wyników zapytania. Jeśli zapytanie nie przynosi efektów, uruchom ST_ISVALIDDETAILED do debugowania Dlaczego typ przestrzenny jest nieprawidłowy.     
> 
> 

Azure DB rozwiązania Cosmos obsługuje również wykonywania kwerend odwrotny, oznacza to, możesz indeksu wierszy w usłudze Azure DB rozwiązania Cosmos lub wielokątów, a następnie wyszukać obszarów, które zawierają określony punkt. Ten wzorzec jest najczęściej używany w logistyki do identyfikowania na przykład gdy ciężarówka wprowadza lub pozostawia wyznaczonego obszaru. 

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

ST_ISVALID i ST_ISVALIDDETAILED może służyć do sprawdzenia, czy obiektu przestrzennego jest prawidłowy. Na przykład poniższe zapytanie sprawdza ważność punktu z poza zakresu wartości szerokości geograficznej (-132.8). ST_ISVALID zwraca wartość logiczną, natomiast ST_ISVALIDDETAILED logicznych i ciąg zawierający przyczyny, dlaczego jest uznawane za nieprawidłowe.

** Zapytania **

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**Results**

    [{
      "$1": false
    }]

Funkcje te mogą służyć do sprawdzania poprawności wielokątów. Na przykład w tym miejscu używamy ST_ISVALIDDETAILED do sprawdzania poprawności wielokąta, który nie jest zamknięty. 

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

### <a name="linq-querying-in-the-net-sdk"></a>Wykonywanie zapytania do zestawu .NET SDK LINQ
Zestaw SDK .NET SQL również dostawców stub metody `Distance()` i `Within()` do użytku w wyrażenia LINQ. Dostawca SQL LINQ tłumaczy tych wywołań metody odpowiednik wywołania funkcji wbudowanej SQL (ST_DISTANCE i ST_WITHIN odpowiednio). 

Oto przykład kwerendy LINQ znajduje wszystkie dokumenty w kolekcji bazy danych Azure rozwiązania Cosmos, którego wartość "Lokalizacja" jest w ramach usługi radius 30 km określonego punktu za pomocą LINQ.

**Zapytania LINQ odległość**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

Podobnie w tym miejscu jest zapytaniem wyszukanie wszystkich dokumentów, których "Lokalizacja" znajduje się w określonym polu/wielokąta. 

**W ramach kwerendy LINQ**

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


Teraz, gdy firma Microsoft zajęło przyjrzeć się jak wykonać zapytanie dotyczące dokumentów za pomocą LINQ i SQL, Spójrzmy na sposób konfigurowania bazy danych Azure rozwiązania Cosmos przestrzennych indeksowania.

## <a name="indexing"></a>Indeksowanie
Firma Microsoft opisane w [schematu niezależny od indeksowanie z bazy danych Azure rozwiązania Cosmos](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) papieru, możemy zaprojektowane aparatu bazy danych DB rozwiązania Cosmos Azure jako naprawdę niezależny od schematu oraz zapewnić wsparcie pierwszej klasie dla formatu JSON. Aparat bazy danych zoptymalizowanych pod kątem zapisu Azure DB rozwiązania Cosmos rozumie natywnie danych przestrzennych (punkty, wielokątów i linii), reprezentowany w standardzie GeoJSON.

Mówiąc, geometrii jest zaprojektowana geodezyjnej współrzędne na płaszczyźnie 2D, a następnie stopniowo podzielone komórki za pomocą **quadtree**. Tych komórek są mapowane na 1D na podstawie lokalizacji komórki znajdującej się w **Hilberta miejsca wypełnianie krzywej**, który zachowuje lokalizację punktów. Ponadto gdy danych lokalizacji jest indeksowany, przechodzi ona przez proces znany jako **tworzenia mozaiki**, oznacza to, że wszystkie komórki przecinających lokalizacji są zidentyfikowane i przechowywane jako klucze w indeksie bazy danych Azure rozwiązania Cosmos. Podczas przeszukiwania argumenty, takie jak punkty i wielokątów są również tesselowaną wyodrębnić zakresy identyfikatorów odpowiednie komórki, a następnie używane do pobierania danych z indeksu.

Jeśli można określić zasady indeksowania, który zawiera indeks przestrzenny dla / * (wszystkie ścieżki), a następnie wszystkie punkty znaleziony w kolekcji są indeksowane wydajnych zapytań przestrzennych (ST_WITHIN i ST_DISTANCE). Indeksy przestrzenne nie ma wartość precyzji i zawsze używaj domyślną wartość precyzji.

> [!NOTE]
> Obsługuje bazę danych systemu Azure rozwiązania Cosmos automatycznego indeksowania punktów, wielokątów i LineStrings
> 
> 

Poniższy fragment kodu JSON przedstawia zasady indeksowania z przestrzennego indeksowania włączone, to znaczy, indeksu dowolnego punktu GeoJSON znaleziono w dokumentach do wykonywania zapytań przestrzennych. W przypadku modyfikowania zasady indeksowania, przy użyciu portalu Azure, można określić następujące JSON dla zasady indeksowania umożliwiające przestrzennych indeksowania w kolekcji.

**Kolekcja indeksowania zasad JSON z przestrzennym włączone punktów i wielokątów**

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

Oto fragment kodu w programie .NET, który pokazuje, jak utworzyć kolekcję z przestrzennego indeksowania włączona dla wszystkich ścieżek zawierających punkty. 

**Utwórz kolekcję z przestrzennego indeksowania**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

A Oto, jak można zmodyfikować istniejącą kolekcję przeprowadzać przestrzennych indeksowania za pośrednictwem punktów, które są przechowywane w dokumentach.

**Zmodyfikuj istniejącą kolekcję z przestrzennego indeksowania**

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
> Jeśli lokalizacja wartość GeoJSON dokumentu jest źle sformułowany lub nieprawidłowy, następnie go zostanie nie indeksowanie przestrzennych zapytań. Można sprawdzić za pomocą ST_ISVALID i ST_ISVALIDDETAILED wartości lokalizacji.
> 
> Jeśli definicja kolekcja zawiera klucz partycji, indeksowania postępu przekształcenie nie został zgłoszony. 
> 
> 

## <a name="next-steps"></a>Kolejne kroki
Nolearned, które zostały wcześniej o tym, jak rozpocząć pracę z obsługą dane geograficzne w usłudze Azure DB rozwiązania Cosmos, można:

* Rozpoczęcie kodowania z [przykłady kodu .NET dane geograficzne w witrynie GitHub](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* Uzyskać ręce na z geograficzne zapytań w [Plac zabaw dla Azure rozwiązania Cosmos DB zapytań](http://www.documentdb.com/sql/demo#geospatial)
* Dowiedz się więcej o [kwerendy bazy danych Azure rozwiązania Cosmos](sql-api-sql-query.md)
* Dowiedz się więcej o [zasady indeksowania bazy danych Azure rozwiązania Cosmos](indexing-policies.md)

