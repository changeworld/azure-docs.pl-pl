---
title: Importowanie danych do analizy w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Importowanie danych statycznych w celu dołączenia do telemetrii aplikacji lub zaimportować strumienia osobne dane do zapytania przy użyciu usługi Analytics.
services: application-insights
keywords: Otwórz schemat, importowanie danych
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/14/2018
ms.author: mbullwin
ms.openlocfilehash: 40b2959a59acb10443dfa917814f42cf98954640
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682711"
---
# <a name="import-data-into-analytics"></a>Importowanie danych do analizy

Importowanie danych tabelarycznych w [Analytics](app-insights-analytics.md), albo aby dołączyć ją za pomocą [usługi Application Insights](app-insights-overview.md) danych telemetrycznych z Twojej aplikacji, lub tak, aby przeanalizować je jako oddzielne strumienia. Analytics to zaawansowany język zapytań dobrze nadaje się do analizowania strumieni oznaczony sygnaturą czasową dużych ilości danych telemetrycznych.
Możesz zaimportować dane do analizy przy użyciu własnego schematu. Nie trzeba używać standardowych schematów usługi Application Insights, takich jak żądania lub śledzenia.

Można zaimportować JSON lub widoku źródła danych (wartości rozdzielonych ogranicznikami - przecinek, średnik lub kartę) plików.

> [!IMPORTANT]
> Ten artykuł został **przestarzałe**. Zalecaną metodę pobierania danych do usługi Log Analytics jest za pośrednictwem [moduł zbierający dane usługi Log Analytics interfejsu API.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api)

Istnieją trzy sytuacje, w których jest użyteczny importowania danych do analizy:

* **Dołącz do dzięki telemetrii aplikacji.** Na przykład można zaimportować tabelę, która mapuje adresy URL z witryny sieci Web do bardziej czytelny tytułów stron. W usłudze Analytics można utworzyć raport wykresu pulpit nawigacyjny, który zawiera dziesięć najpopularniejszych stron w witrynie sieci Web. Teraz on zawierać tytułów stron, zamiast adresów URL.
* **Korelowanie danych telemetrycznych aplikacji** z innych źródeł, takie jak ruch w sieci, dane serwera lub CDN plików dziennika.
* **Zastosowanie analizy do strumienia danych.** Analiza usługi Application Insights to zaawansowane narzędzie, które działa dobrze z rozrzedzony, oznaczony sygnaturą czasową strumieniami — znacznie lepsze niż SQL, w wielu przypadkach. Jeśli masz takie strumienia z innego źródła, można analizować je przy użyciu usługi Analytics.

Wysyłanie danych do źródła danych jest łatwe. 

1. (Jeden raz) Należy zdefiniować schemat danych w źródle danych.
2. (Okresowo) Przekazywanie danych do usługi Azure storage i wywołania interfejsu API REST Powiadom nas, które czeka na pozyskiwanie nowych danych. W ciągu kilku minut dane są dostępne dla zapytania w usłudze Analytics.

Częstotliwość przekazywania jest zdefiniowany przez użytkownika i jak szybko czy dane mają być dostępne dla zapytań. Jest bardziej wydajne, aby przekazać dane w większe fragmenty, ale nie większą niż 1GB.

> [!NOTE]
> *Masz wiele źródeł danych do analizy?* [*Należy rozważyć użycie* logstash *do wysłania dane do usługi Application Insights.*](https://github.com/Microsoft/logstash-output-application-insights)
> 

## <a name="before-you-start"></a>Przed rozpoczęciem

Potrzebne elementy:

1. Zasób usługi Application Insights, w systemie Microsoft Azure.

 * Jeśli chcesz analizować dane niezależnie od innych telemetrii [Utwórz nowy zasób usługi Application Insights](app-insights-create-new-resource.md).
 * Jeśli dołączenie lub porównywania danych przy użyciu telemetrii z aplikacji, która jest już skonfigurowane za pomocą usługi Application Insights, można użyć zasobu dla danej aplikacji.
 * Dostęp współautora lub właściciela do tego zasobu.
 
2. Usługa Azure storage. Przekaż do usługi Azure storage i Analytics pobiera dane z tego miejsca. 

 * Zaleca się tworzenie konta magazynu dedykowanego dla obiektów blob. Jeśli obiekty BLOB są współużytkowane z innymi procesami, zajmuje więcej czasu na naszych procesów odczytu obiektów blob.


## <a name="define-your-schema"></a>Definiowania schematu

Przed zaimportowaniem danych, należy zdefiniować *źródła danych* określający schemat danych.
Może mieć maksymalnie 50 źródeł danych w pojedynczym zasobie usługi Application Insights

1. Uruchom Kreatora źródła danych. Użyj przycisku "Dodaj nowe źródło danych". Alternatywnie — kliknij przycisk Ustawienia w prawym górnym rogu i wybierz pozycję "Źródła danych" w menu rozwijanym.

    ![Dodaj nowe źródło danych](./media/app-insights-analytics-import/add-new-data-source.png)

    Podaj nazwę dla nowego źródła danych.

2. Definiowanie formatu plików, które będą przekazywać.

    Można ręcznie zdefiniować format lub Przekaż przykładowy plik.

    Jeśli dane są w formacie CSV, pierwszy wiersz przykładu można nagłówków kolumn. Można zmienić nazwy pól w następnym kroku.

    Plik powinien zawierać co najmniej 10 wierszy lub rekordów danych.

    Nazwy kolumn lub pola mają nazwy alfanumeryczne (bez spacji i znaków interpunkcyjnych).

    ![Przekaż przykładowy plik](./media/app-insights-analytics-import/sample-data-file.png)


3. Przegląd schematu, który Kreator ma stało się. Typy przykładu go wywnioskować, może być konieczne dostosowanie wywnioskowane typy kolumn.

    ![Przegląd schematu wywnioskowane](./media/app-insights-analytics-import/data-source-review-schema.png)

 * (opcjonalnie) Przekaż definicji schematu. Zobacz poniższy format.

 * Wybierz sygnaturę czasową. Wszystkie dane w usłudze Analytics musi mieć pola sygnatury czasowej. Musi mieć typ `datetime`, ale nie musi mieć nazwę "timestamp". Jeśli dane mają kolumnę zawierającą datę i godzinę w formacie ISO, wybierz tę opcję jako kolumnę sygnatur czasowych. W przeciwnym razie wybierz opcję "jako dane dotarła", a proces importowania spowoduje dodanie pola sygnatury czasowej.

5. Utwórz źródło danych.

### <a name="schema-definition-file-format"></a>Format pliku definicji schematu

Zamiast edytowania schematu w interfejsie użytkownika, możesz załadować z pliku definicji schematu. Format definicji schematu jest w następujący sposób: 

Rozdzielany formatu 
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

* Lokalizacja — rozdzielonym pliku flagowana jest pozycja mapowanej wartości. Dla formatu JSON jest jpath zamapowanego klucza.
* Nazwa — Nazwa wyświetlana kolumny.
* Typ — typ danych tej kolumny.

> [!NOTE]
> W przypadku, gdy użyto przykładowych danych i format pliku jest rozdzielany, definicji schematu musi mapować wszystkie kolumny i dodać nowe kolumny na końcu.
> 
> JSON umożliwia mapowania częściowe dane, w związku z tym definicji schematu przy użyciu formatu JSON nie będą musieli mapować każdy klucz, który znajduje się w przykładowych danych. Można również mapować kolumny, które nie należą do przykładowych danych. 

## <a name="import-data"></a>Importowanie danych

Aby zaimportować dane, przekaż go do usługi Azure storage, Utwórz klucz dostępu dla niego i następnie wykonywanie wywołania interfejsu API REST.

![Dodaj nowe źródło danych](./media/app-insights-analytics-import/analytics-upload-process.png)

Wykonaj następujący proces ręcznie lub skonfigurować zautomatyzowany system, aby to zrobić w regularnych odstępach czasu. Należy wykonać następujące kroki dla każdego bloku danych, które chcesz zaimportować.

1. Przekazywanie danych do [usługi Azure blob storage](../storage/blobs/storage-quickstart-blobs-dotnet.md). 

 * Obiekty BLOB mogą być dowolnego rozmiaru bez kompresji do 1GB. Duże obiekty BLOB setek MB są idealnym rozwiązaniem z punktu widzenia wydajności.
 * Można ją skompresować za pomocą narzędzia Gzip, aby poprawić czas przekazywania i czas oczekiwania na dane, które mają być dostępne dla zapytania. Użyj `.gz` rozszerzenie nazwy pliku.
 * Najlepiej użyć oddzielnego konta magazynu w tym celu, aby uniknąć wywołania z różnymi usługami spowolnienia wydajności.
 * Podczas wysyłania danych w wysokiej częstotliwości, co kilka sekund, zaleca się używać więcej niż jedno konto magazynu, ze względu na wydajność.

 
2. [Utwórz klucz podpisu dostępu współdzielonego dla obiektu blob](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md). Klucz powinien mieć okres ważności jeden dzień i zapewniają dostęp do odczytu.
3. Wykonywać wywołanie interfejsu REST w celu powiadomienia usługi Application Insights, który oczekuje danych.

 * Punkt końcowy: `https://dc.services.visualstudio.com/v2/track`
 * Metoda HTTP: WPIS
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

Symbole zastępcze są następujące:

* `Blob URI with Shared Access Key`: Można uzyskać z procedury tworzenia klucza. Odnosi się do obiektu blob.
* `Schema ID`: Schemat identyfikator wygenerowany dla zdefiniowanego schematu. Dane w tym obiekcie blob powinna być zgodna ze schematem.
* `DateTime`: Czas przesłaniem żądania, UTC. Możemy zaakceptować te formaty: ISO8601 (np. "2016-01-01 13:45:01"); Rfc822 ("Środa, 16 gru 14 14:57:01 + 0000"); RFC850 ("Środa, 16-14-Dec 14:57:00 UTC"); RFC1123 ("Środa 14 grudnia 2016 14:57:00 + 0000").
* `Instrumentation key` zasobu usługi Application Insights.

Dane są dostępne w usłudze Analytics po kilku minutach.

## <a name="error-responses"></a>Odpowiedzi na błędy

* **odpowiedź 400 Niewłaściwe żądanie**: wskazuje, że ładunek żądania jest nieprawidłowy. Sprawdź:
 * Klucz Instrumentacji poprawne.
 * Prawidłową wartość czasu. Powinno być teraz czasu UTC.
 * JSON zdarzenia jest zgodny ze schematem.
* **403 Zabroniony**: wysłanych do obiektu blob nie jest dostępny. Upewnij się, czy klucz dostępu współdzielonego jest prawidłowa i czy nie wygasł.
* **404 Nie znaleziono**:
 * Obiekt blob nie istnieje.
 * Identyfikator jest nieprawidłowy.

Bardziej szczegółowe informacje są dostępne w komunikacie o błędzie odpowiedzi.


## <a name="sample-code"></a>Przykładowy kod

Ten kod używa [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/9.0.1) pakietu NuGet.

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

### <a name="ingest-data"></a>Pozyskiwanie danych

Użyj tego kodu dla każdego obiektu blob. 

```csharp
   AnalyticsDataSourceClient client = new AnalyticsDataSourceClient(); 

   var ingestionRequest = new AnalyticsDataSourceIngestionRequest("iKey", "sourceId", "blobUrlWithSas"); 

   bool success = await client.RequestBlobIngestion(ingestionRequest);
```

## <a name="next-steps"></a>Kolejne kroki

* [Samouczek języka zapytań usługi Log Analytics](../log-analytics/query-language/get-started-analytics-portal.md)
* Jeśli używasz programu Logstash, użyj [wtyczkę Logstash w celu wysyłania danych do usługi Application Insights](https://github.com/Microsoft/logstash-output-application-insights)
