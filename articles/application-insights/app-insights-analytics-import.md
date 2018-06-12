---
title: Importowanie danych do analizy w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Importuj dane statyczne, aby dołączyć za pomocą telemetrii aplikacji lub zaimportować strumienia danych do zapytania z Analytics.
services: application-insights
keywords: Otwórz schematu, importowania danych
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2017
ms.author: mbullwin
ms.openlocfilehash: 688d620e19a8a6f536d134d9c4d7c837ec06bbdc
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293625"
---
# <a name="import-data-into-analytics"></a>Importowanie danych do analityka

Importowanie danych tabelarycznych w [Analytics](app-insights-analytics.md), albo przyłączenie jej z [usługi Application Insights](app-insights-overview.md) dane telemetryczne z aplikacji, lub tak, aby można analizować je jako osobne strumienia. Analytics to dobrze nadaje się do analizowania oznaczony znacznikiem czasowym dużych strumieni danych telemetrycznych język zaawansowanych zapytań.

Możesz zaimportować dane analizy przy użyciu własnego schematu. Nie trzeba użyć standardowe schematów usługi Application Insights, takich jak żądania lub śledzenia.

Możesz zaimportować JSON lub widoku źródła danych (wartości rozdzielonych ogranicznikami - przecinek, średnik lub kartę) plików.

Istnieją trzy sytuacjach, gdy importowania Analytics jest przydatna:

* **Dołącz z danych telemetrycznych aplikacji.** Na przykład można zaimportować tabelę, która mapuje adresy URL z witryny sieci Web był bardziej czytelny tytułów stron. W module analiz można utworzyć raport wykresu pulpitu nawigacyjnego, który zawiera dziesięć najpopularniejszych stron w witrynie sieci Web. Teraz mogą być prezentowane tytuły stron zamiast adresów URL.
* **Korelowanie dane telemetryczne aplikacji** z innych źródeł, takie jak ruch w sieci, dane serwera lub CDN pliki dziennika.
* **Zastosuj Analytics do strumienia danych.** Application Insights Analytics jest zaawansowane narzędzia, która współdziała również z rozrzedzony, strumienie oznaczony znacznikiem czasowym — znacznie lepszą niż SQL w wielu przypadkach. Jeśli masz takich strumienia z z innego źródła, można analizować je z Analytics.

Wysyłanie danych do źródła danych jest bardzo proste. 

1. (Jeden raz) Zdefiniuj schemat danych w źródle danych.
2. (Okresowo) Przekazywanie danych do magazynu Azure i wywołania interfejsu API REST, aby powiadomić wynika, że nowe dane oczekuje na wprowadzanie. W ciągu kilku minut dane są dostępne dla kwerendy w module analiz.

Częstotliwość przekazywania jest zdefiniowany przez użytkownika i jak szybko czy chcesz, aby dane były dostępne dla zapytań. Jest bardziej wydajne, aby przekazać dane w większych fragmentów, ale nie większą niż 1GB.

> [!NOTE]
> *Uzyskano wiele źródeł danych do analizowania?* [*Należy rozważyć użycie* logstash *do wysłania dane do usługi Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Przed rozpoczęciem

Potrzebne elementy:

1. Zasób usługi Application Insights w Microsoft Azure.

 * Jeśli chcesz do analizowania danych niezależnie od innych telemetrii [utworzyć nowy zasób usługi Application Insights](app-insights-create-new-resource.md).
 * Sprzęganie lub porównywanie danych za pomocą dane telemetryczne z aplikacji, która jest już skonfigurowane przy użyciu usługi Application Insights, można użyć zasobu dla danej aplikacji.
 * Współautor lub właściciela dostęp do tego zasobu.
 
2. Magazyn Azure. Możesz przekazać do magazynu Azure i analizy pobiera dane z tego miejsca. 

 * Zaleca się tworzenie konta magazynu dedykowane dla obiektów blob. Jeżeli obiektów blob są współużytkowane z innymi procesami, trwa dłużej dla naszych procesów do odczytu obiektów blob.


## <a name="define-your-schema"></a>Definiowania schematu

Przed importowaniem danych, należy zdefiniować *źródła danych* określającej schemat danych.
Może mieć maksymalnie 50 źródeł danych w pojedynczy zasób usługi Application Insights

1. Uruchom Kreatora źródła danych. Użyj przycisku "Dodaj nowe źródło danych". Alternatywnie — kliknij przycisk Ustawienia w prawym górnym rogu i wybierz w menu rozwijanym "Źródła danych".

    ![Dodaj nowe źródło danych](./media/app-insights-analytics-import/add-new-data-source.png)

    Podaj nazwę dla nowego źródła danych.

2. Definiowanie formatu plików, które możesz przekazać.

    Można ręcznie zdefiniować format lub Przekaż plik przykładowy.

    Jeśli dane są w formacie CSV, pierwszy wiersz próbki można nagłówki kolumn. Można zmienić nazwy pól w następnym kroku.

    Próbka powinna zawierać co najmniej 10 wierszy lub rekordów danych.

    Nazwy kolumn lub pole ma alfanumerycznych nazw (bez spacji i znaków interpunkcyjnych).

    ![Przekaż plik przykładowy](./media/app-insights-analytics-import/sample-data-file.png)


3. Przegląd schematu, do którego otrzymano kreatora. Typy z próbki go wywnioskować, może być konieczne dostosowanie wywnioskowanych typów kolumn.

    ![Przejrzyj wnioskowany schematu](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (opcjonalnie) Przekaż definicji schematu. Zobacz następujący format.

 * Wybierz sygnatury czasowej. Wszystkie dane w module analiz musi mieć pola sygnatury czasowej. Musi mieć typ `datetime`, ale nie musi mieć nazwę "timestamp". Jeśli dane mają kolumny zawierającej daty i godziny w formacie ISO, wybierz tę opcję, jako kolumnę znaczników czasu. W przeciwnym razie wybierz pozycję "jako dane dostarczone", a proces importowania doda pola sygnatury czasowej.

5. Tworzenie źródła danych.

### <a name="schema-definition-file-format"></a>Format pliku definicji schematu

Zamiast edytowania schematu w interfejsie użytkownika, można załadować definicji schematu z pliku. Format definicji schematu jest następujący: 

Format rozdzielanego 
```
[ 
    {"location": "0", "name": "RequestName", "type": "string"}, 
    {"location": "1", "name": "timestamp", "type": "datetime"}, 
    {"location": "2", "name": "IPAddress", "type": "string"} 
] 
```

JSON format 
```
[ 
    {"location": "$.name", "name": "name", "type": "string"}, 
    {"location": "$.alias", "name": "alias", "type": "string"}, 
    {"location": "$.room", "name": "room", "type": "long"} 
]
```
 
Każda kolumna jest identyfikowany przez lokalizację, nazwy i typu. 

* Lokalizacja — pliku rozdzielanego go sformatować jest pozycja mapowane wartości. JSON format jest jpath zamapowanych klucza.
* Nazwa — Nazwa wyświetlane kolumny.
* Typ — typ danych tej kolumny.
 
W przypadku użyto przykładowych danych i rozdzielana format pliku, definicji schematu należy zamapować wszystkich kolumn i dodać nowe kolumny na końcu. 

JSON umożliwia mapowania częściowe, danych, w związku z tym nie ma definicji schematu w formacie JSON do mapowania każdego klucza, który znajduje się w przykładowych danych. Można również mapować kolumn, które nie są częścią przykładowych danych. 

## <a name="import-data"></a>Importowanie danych

Aby zaimportować dane, przekaż go do magazynu Azure, Utwórz klucz dostępu dla niego i następnie wykonać wywołania interfejsu API REST.

![Dodaj nowe źródło danych](./media/app-insights-analytics-import/analytics-upload-process.png)

Wykonaj następujący proces ręcznie lub konfigurowania automatycznych systemu należy w regularnych odstępach czasu. Należy wykonać następujące kroki dla każdego bloku danych, które chcesz zaimportować.

1. Przekazywanie danych do [magazynu obiektów blob Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). 

 * Obiekty BLOB może być dowolną rozmiaru bez kompresji do 1GB. Duże obiekty BLOB setek MB idealnie nadają się z punktu widzenia wydajności.
 * Można skompresować je z Gzip, aby zwiększyć czas przekazywania i czas oczekiwania na dane, które mają być dostępne dla zapytania. Użyj `.gz` rozszerzenie nazwy pliku.
 * Najlepiej użyć oddzielnego konta magazynu w tym celu w celu uniknięcia wywołania z różnych usług spowolnienia.
 * Podczas wysyłania danych w wysokiej częstotliwości, co kilka sekund, zaleca się użyć więcej niż jedno konto magazynu, ze względu na wydajność.

 
2. [Utwórz klucz sygnatura dostępu współdzielonego dla obiektu blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Klucz powinien mieć okres ważności jeden dzień i zapewniają dostęp do odczytu.
3. Wykonywać wywołanie interfejsu REST w celu powiadomienia usługi Application Insights, która oczekuje na dane.

 * Punkt końcowy: `https://dc.services.visualstudio.com/v2/track`
 * Metoda HTTP: POST
 * Ładunek:

```JSON

    {
       "data": {
            "baseType":"OpenSchemaData",
            "baseData":{
               "ver":"2",
               "blobSasUri":"<Blob URI with Shared Access Key>",
               "sourceName":"<Schema ID>",
               "sourceVersion":"1.0"
             }
       },
       "ver":1,
       "name":"Microsoft.ApplicationInsights.OpenSchema",
       "time":"<DateTime>",
       "iKey":"<instrumentation key>"
    }
```

Symbole zastępcze są:

* `Blob URI with Shared Access Key`: Otrzymujesz to z procedury tworzenia klucza. Jest specyficzne dla obiektu blob.
* `Schema ID`Generowane dla określonych schemat Identyfikatora schematu. Dane w tym obiekcie blob powinna być zgodna ze schematem.
* `DateTime`: Czas, o której zostało przesłane żądanie, UTC. Możemy zaakceptować te formaty: ISO8601 (takich jak "2016-01-01-13:45:01"); Rfc822 ("śro, 14 gru 16 14:57:01 + 0000"); RFC850 ("Środa, 16-14-gru UTC 14:57:00"); RFC1123 ("śro 14 gru 2016 14:57:00 + 0000").
* `Instrumentation key` zasobu usługi Application Insights.

Dane są dostępne w module analiz po kilku minutach.

## <a name="error-responses"></a>Błąd odpowiedzi

* **Nieprawidłowe żądanie 400**: oznacza, że ładunku żądania jest nieprawidłowy. Sprawdzanie:
 * Instrumentacja poprawny klucz.
 * Wartość czas ważności. Należy go czasu w formacie UTC.
 * JSON zdarzenia jest zgodny ze schematem.
* **403 Zabroniony**: wysłanych do obiektu blob nie jest dostępny. Upewnij się, że klucz dostępu współdzielonego jest prawidłowa i czy nie wygasł.
* **Nie można odnaleźć 404**:
 * Obiekt blob nie istnieje.
 * Identyfikator jest nieprawidłowy.

Bardziej szczegółowe informacje są dostępne w odpowiedzi komunikat o błędzie.


## <a name="sample-code"></a>Przykładowy kod

Ten kod zawiera [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) pakietu NuGet.

### <a name="classes"></a>Klasy

```csharp
namespace IngestionClient 
{ 
    using System; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceIngestionRequest 
    { 
        #region Members 
        private const string BaseDataRequiredVersion = "2"; 
        private const string RequestName = "Microsoft.ApplicationInsights.OpenSchema"; 
        #endregion Members 

        public AnalyticsDataSourceIngestionRequest(string ikey, string schemaId, string blobSasUri, int version = 1) 
        { 
            Ver = version; 
            IKey = ikey; 
            Data = new Data 
            { 
                BaseData = new BaseData 
                { 
                    Ver = BaseDataRequiredVersion, 
                    BlobSasUri = blobSasUri, 
                    SourceName = schemaId, 
                    SourceVersion = version.ToString() 
                } 
            }; 
        } 


        [JsonProperty("data")] 
        public Data Data { get; set; } 

        [JsonProperty("ver")] 
        public int Ver { get; set; } 

        [JsonProperty("name")] 
        public string Name { get { return RequestName; } } 

        [JsonProperty("time")] 
        public DateTime Time { get { return DateTime.UtcNow; } } 

        [JsonProperty("iKey")] 
        public string IKey { get; set; } 
    } 

    #region Internal Classes 

    public class Data 
    { 
        private const string DataBaseType = "OpenSchemaData"; 

        [JsonProperty("baseType")] 
        public string BaseType 
        { 
            get { return DataBaseType; } 
        } 

        [JsonProperty("baseData")] 
        public BaseData BaseData { get; set; } 
    } 


    public class BaseData 
    { 
        [JsonProperty("ver")] 
        public string Ver { get; set; } 

        [JsonProperty("blobSasUri")] 
        public string BlobSasUri { get; set; } 

        [JsonProperty("sourceName")] 
        public string SourceName { get; set; } 

        [JsonProperty("sourceVersion")] 
        public string SourceVersion { get; set; } 
    } 

    #endregion Internal Classes 
} 


namespace IngestionClient 
{ 
    using System; 
    using System.IO; 
    using System.Net; 
    using System.Text; 
    using System.Threading.Tasks; 
    using Newtonsoft.Json; 

    public class AnalyticsDataSourceClient 
    { 
        #region Members 
        private readonly Uri endpoint = new Uri("https://dc.services.visualstudio.com/v2/track"); 
        private const string RequestContentType = "application/json; charset=UTF-8"; 
        private const string RequestAccess = "application/json"; 
        #endregion Members 

        #region Public 

        public async Task<bool> RequestBlobIngestion(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            HttpWebRequest request = WebRequest.CreateHttp(endpoint); 
            request.Method = WebRequestMethods.Http.Post; 
            request.ContentType = RequestContentType; 
            request.Accept = RequestAccess; 

            string notificationJson = Serialize(ingestionRequest); 
            byte[] notificationBytes = GetContentBytes(notificationJson); 
            request.ContentLength = notificationBytes.Length; 

            Stream requestStream = request.GetRequestStream(); 
            requestStream.Write(notificationBytes, 0, notificationBytes.Length); 
            requestStream.Close(); 

            try 
            { 
                using (var response = (HttpWebResponse)await request.GetResponseAsync())
                {
                    return response.StatusCode == HttpStatusCode.OK;
                }
            } 
            catch (WebException e) 
            { 
                HttpWebResponse httpResponse = e.Response as HttpWebResponse; 
                if (httpResponse != null) 
                { 
                    Console.WriteLine( 
                        "Ingestion request failed with status code: {0}. Error: {1}", 
                        httpResponse.StatusCode, 
                        httpResponse.StatusDescription); 
                    return false; 
                }
                throw; 
            } 
        } 
        #endregion Public 

        #region Private 
        private byte[] GetContentBytes(string content) 
        { 
            return Encoding.UTF8.GetBytes(content);
        } 


        private string Serialize(AnalyticsDataSourceIngestionRequest ingestionRequest) 
        { 
            return JsonConvert.SerializeObject(ingestionRequest); 
        } 
        #endregion Private 
    } 
} 
```

### <a name="ingest-data"></a>Pobieranie danych

Użyj tego kodu dla każdego obiektu blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek języka zapytań usługi Analiza dzienników](app-insights-analytics-tour.md)
* Jeśli używasz Logstash, użyj [Logstash wtyczki do przesyłania danych do usługi Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
