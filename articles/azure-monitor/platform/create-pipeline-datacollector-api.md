---
title: Tworzenie potoku danych za pomocą interfejsu API modułu zbierającego dane
description: Za pomocą interfejsu API modułu zbierającego dane HTTP monitora platformy Azure można dodać dane POST JSON do obszaru roboczego usługi Log Analytics od dowolnego klienta, który może wywołać interfejs API REST. W tym artykule opisano sposób przekazywania danych przechowywanych w plikach w sposób zautomatyzowany.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: 96c64f6a0167b678f14bf0199069ecd6b4c8d57a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055108"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Tworzenie potoku danych za pomocą interfejsu API modułu zbierającego dane

[Interfejs API modułu zbierającego dane usługi Azure Monitor](data-collector-api.md) umożliwia importowanie dowolnych niestandardowych danych dziennika do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Jedynymi wymaganiami są, że dane są sformatowane json i podzielone na 30 MB lub mniej segmentów. Jest to całkowicie elastyczny mechanizm, który można podłączyć na wiele sposobów: od danych wysyłanych bezpośrednio z aplikacji po jednorazowe przesyłanie adhoc. W tym artykule opisano niektóre punkty wyjścia dla wspólnego scenariusza: konieczność przekazywania danych przechowywanych w plikach w sposób regularny, zautomatyzowany. Chociaż potok przedstawiony w tym miejscu nie będzie najbardziej wydajny lub w inny sposób zoptymalizowany, ma służyć jako punkt wyjścia do budowy własnego rurociągu produkcyjnego.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Przykładowy problem
W dalszej części tego artykułu zbadamy dane widoku strony w usłudze Application Insights. W naszym hipotetycznym scenariuszu chcemy skorelować informacje geograficzne zbierane domyślnie przez zestaw SDK usługi Application Insights z danymi niestandardowymi zawierającymi populację każdego kraju/regionu na świecie, w celu określenia, gdzie powinniśmy wydawać najwięcej dolarów marketingowych. 

W tym celu korzystamy z publicznego źródła danych, takiego jak [Światowe Perspektywy Ludności ONZ.](https://esa.un.org/unpd/wpp/) Dane będą miały następujący prosty schemat:

![Przykładowy schemat prosty](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

W naszym przykładzie zakładamy, że przekażemy nowy plik z danymi z ostatniego roku, gdy tylko stanie się dostępny.

## <a name="general-design"></a>Ogólny projekt
Używamy klasycznej logiki typu ETL do projektowania naszego rurociągu. Architektura będzie wyglądać w następujący sposób:

![Architektura potoku zbierania danych](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

W tym artykule nie opisano sposobu tworzenia danych ani [przekazywania ich na konto usługi Azure Blob Storage.](../../storage/blobs/storage-upload-process-images.md) Zamiast tego możemy podnieść przepływ, jak tylko nowy plik zostanie przesłany do obiektu blob. z o.o.

1. Proces wykryje, że nowe dane zostały przekazane.  W naszym przykładzie użyto [aplikacji Logika platformy Azure](../../logic-apps/logic-apps-overview.md), która ma dostępny wyzwalacz do wykrywania nowych danych przekazywanych do obiektu blob.

2. Procesor odczytuje te nowe dane i konwertuje je na JSON, format wymagany przez usługę Azure Monitor W tym przykładzie używamy [funkcji platformy Azure](../../azure-functions/functions-overview.md) jako lekki, ekonomiczny sposób wykonywania naszego kodu przetwarzania. Funkcja jest uruchamiana przez tę samą aplikację logiki, która została użyta do wykrycia nowych danych.

3. Na koniec, gdy obiekt JSON jest dostępny, jest wysyłany do usługi Azure Monitor. Ta sama aplikacja logiki wysyła dane do usługi Azure Monitor przy użyciu wbudowanej aktywności modułu zbierającego dane usługi Log Analytics.

Podczas gdy szczegółowa konfiguracja magazynu obiektów blob, aplikacji logiki lub funkcji platformy Azure nie jest opisana w tym artykule, szczegółowe instrukcje są dostępne na stronach określonych produktów.

Aby monitorować ten potok, używamy usługi Application Insights do monitorowania szczegółów funkcji platformy Azure [w tym miejscu,](../../azure-functions/functions-monitoring.md)a usługa Azure Monitor do monitorowania szczegółów aplikacji logiki [w tym miejscu.](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md) 

## <a name="setting-up-the-pipeline"></a>Konfigurowanie potoku
Aby ustawić potok, najpierw upewnij się, że kontener obiektów blob został utworzony i skonfigurowany. Podobnie upewnij się, że jest tworzony obszar roboczy usługi Log Analytics, do którego chcesz wysłać dane.

## <a name="ingesting-json-data"></a>Połknienie danych JSON
Pozyskiwania danych JSON jest trywialne z logic apps i ponieważ nie transformacja musi mieć miejsce, możemy otoczyć cały potok w jednej aplikacji logiki. Po skonfigurowaniu kontenera obiektów blob i obszaru roboczego usługi Log Analytics utwórz nową aplikację logiki i skonfiguruj ją w następujący sposób:

![Przykład przepływu pracy aplikacji logiki](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Zapisz aplikację logiki i przystąpić do jej testowania.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Połkliwianie XML, CSV lub innych formatów danych
Logic Apps dzisiaj nie ma wbudowanych możliwości łatwego przekształcania XML, CSV lub innych typów w formacie JSON. Dlatego musimy użyć innego środka, aby zakończyć tę transformację. W tym artykule używamy możliwości obliczeniowych bez użycia serwera usługi Azure Functions jako bardzo lekki i przyjazny dla kosztów sposób. 

W tym przykładzie analizujemy plik CSV, ale każdy inny typ pliku może być podobnie przetworzony. Po prostu zmodyfikuj deserializację części funkcji platformy Azure, aby odzwierciedlić poprawną logikę dla określonego typu danych.

1.  Utwórz nową funkcję platformy Azure, przy użyciu środowiska wykonawczego function v1 i na podstawie użycia po wyświetleniu monitu.  Wybierz szablon **wyzwalacza HTTP** przeznaczony dla języka C# jako punkt wyjścia, który konfiguruje powiązania zgodnie z wymaganiami. 
2.  Na karcie **Widok plików** w prawym okienku utwórz nowy plik o nazwie **project.json** i wklej następujący kod z pakietów NuGet, których używamy:

    ![Przykładowy projekt usługi Azure Functions](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Przełącz się do **pliku run.csx** z prawego okienka i zastąp domyślny kod następującym. 

    >[!NOTE]
    >Dla projektu należy zastąpić model rekordu (klasa "PopulationRecord") własnym schematem danych.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Zapisz swoją funkcję.
5. Przetestuj funkcję, aby upewnić się, że kod działa poprawnie. Przełącz się do karty **Test** w prawym okienku, konfigurując test w następujący sposób. Umieść łącze do obiektu blob z przykładowymi danymi w treści **tekstu treści Żądanie.** Po kliknięciu przycisku **Uruchom**powinien zostać wyświetlony wynik JSON w polu **Dane wyjściowe:**

    ![Kod testowy aplikacji funkcyjnych](./media/create-pipeline-datacollector-api/functions-test-01.png)

Teraz musimy wrócić i zmodyfikować aplikację logiki, którą wcześniej rozpoczęliśmy, aby uwzględnić dane ponajmowane i konwertowane na format JSON.  Korzystając z projektanta widoku, skonfiguruj w następujący sposób, a następnie zapisz aplikację logiki:

![Pełny przykład przepływu pracy aplikacji logiki](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testowanie rurociągu
Teraz możesz przekazać nowy plik do obiektu blob skonfigurowanego wcześniej i monitorować go za pomocą aplikacji logiki. Wkrótce powinno zostać wyświetlona nowa instancja aplikacji logiki, wywołanie funkcji platformy Azure, a następnie pomyślne wysłanie danych do usługi Azure Monitor. 

>[!NOTE]
>Może upłynąć do 30 minut, aby dane były wyświetlane w usłudze Azure Monitor przy pierwszym wysłaniu nowego typu danych.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korelowanie z innymi danymi w usłudze Log Analytics i usłudze Application Insights
Aby osiągnąć nasz cel, jakim jest skorelowanie danych widoku strony usługi Application Insights z danymi populacji, które pozyskaliśmy z naszego niestandardowego źródła danych, uruchom następującą kwerendę z okna usługi Application Insights Analytics lub obszaru roboczego usługi Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

Dane wyjściowe powinny wyświetlać dwa źródła danych teraz połączone.  

![Korelowanie danych z odłączonych w przykładzie wyników wyszukiwania](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Sugerowane ulepszenia dla rurociągu produkcyjnego
W tym artykule przedstawiono działający prototyp, za którym można zastosować logikę do prawdziwego rozwiązania o jakości produkcji. W przypadku takiego rozwiązania o jakości produkcji zaleca się następujące ulepszenia:

* Dodaj logikę obsługi błędów i ponawiania prób w aplikacji logiki i funkcji.
* Dodaj logikę, aby upewnić się, że limit wywołań interfejsu API 30 MB/pojedynczy interfejs dziennika pozyskiwania nie zostanie przekroczony. W razie potrzeby podziel dane na mniejsze segmenty.
* Skonfiguruj zasady oczyszczania w magazynie obiektów blob. Po pomyślnym wysłaniu do obszaru roboczego usługi Log Analytics, chyba że chcesz zachować nieprzetworzone dane dostępne do celów archiwalnych, nie ma powodu, aby kontynuować ich przechowywanie. 
* Sprawdź, czy monitorowanie jest włączone w całym potoku, dodając punkty śledzenia i alerty, stosownie do przypadku.
* Wykorzystuj kontrolę źródła do zarządzania kodem dla funkcji i aplikacji logiki.
* Upewnij się, że przestrzegane są odpowiednie zasady zarządzania zmianami, tak aby w przypadku zmiany schematu funkcja i aplikacje logiki są odpowiednio modyfikowane.
* Jeśli przekazujesz wiele różnych typów danych, segreguj je do poszczególnych folderów w kontenerze obiektów blob i utwórz logikę do administratora logiki na podstawie typu danych. 


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [interfejsie API modułu zbierającego dane](data-collector-api.md) do zapisywania danych w obszarze roboczym usługi Log Analytics z dowolnego klienta interfejsu API REST.
