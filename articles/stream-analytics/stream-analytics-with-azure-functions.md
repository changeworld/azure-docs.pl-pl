---
title: 'Samouczek: uruchamianie usługi Azure Functions za pomocą zadań usługi Azure Stream Analytics | Microsoft Docs'
description: W tym samouczku przedstawiono konfigurowanie usługi Azure Functions jako ujścia danych wyjściowych dla zadań usługi Stream Analytics.
services: stream-analytics
author: jasonwhowell
manager: kfile
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc
ms.workload: data-services
ms.date: 04/09/2018
ms.author: jasonh
ms.reviewer: jasonh
ms.openlocfilehash: 1d33c3f0a4c36dc681aaa42bc68ae56eec234401
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="run-azure-functions-from-azure-stream-analytics-jobs"></a>Uruchamianie usługi Azure Functions z zadań usługi Azure Stream Analytics 

Usługę Azure Functions można uruchomić z usługi Azure Stream Analytics, konfigurując usługę Functions jako jedno z ujść danych wyjściowych dla zadania usługi Stream Analytics. Usługa Functions to sterowane zdarzeniami środowisko obliczeń na żądanie, które umożliwia implementowanie kodu wyzwalanego przez zdarzenia występujące na platformie Azure lub w usługach innych firm. Dzięki możliwości reagowania na wyzwalacze usługa Functions stanowi naturalne wyjście dla zadań usługi Stream Analytics.

Usługa Stream Analytics wywołuje usługę Functions za pomocą wyzwalaczy protokołu HTTP. Adapter wyjścia usługi Functions umożliwia użytkownikom połączenie usługi Functions z usługą Stream Analytics, tak aby można było wyzwalać zdarzenia na podstawie zapytań usługi Stream Analytics. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie zadania usługi Stream Analytics
> * Tworzenie funkcji platformy Azure
> * Konfigurowanie funkcji platformy Azure jako wyjścia zadania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Konfigurowanie zadania usługi Stream Analytics pod kątem uruchamiania funkcji 

W tej sekcji przedstawiono sposób konfigurowania zadania usługi Stream Analytics pod kątem uruchamiania funkcji zapisującej dane w usłudze Azure Redis Cache. Zadanie usługi Stream Analytics odczytuje zdarzenia z usługi Azure Event Hubs i uruchamia zapytanie wywołujące funkcję. Ta funkcja odczytuje dane z zadania usługi Stream Analytics i zapisuje je w usłudze Azure Redis Cache.

![Diagram przedstawiający relacje między usługami platformy Azure](./media/stream-analytics-with-azure-functions/image1.png)

Poniższe kroki są wymagane do wykonania tego zadania:
* [Utworzenie zadania usługi Stream Analytics z usługą Event Hubs jako wejściem](#create-stream-analytics-job-with-event-hub-as-input)  
* [Utworzenie wystąpienia usługi Azure Redis Cache](#create-an-azure-redis-cache)  
* [Utworzenie funkcji w usłudze Azure Functions, która może zapisywać dane w usłudze Azure Redis Cache](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Aktualizacja zadania usługi Stream Analytics za pomocą funkcji jako wyjścia](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Sprawdzenie usługi Azure Redis Cache pod kątem wyników](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Tworzenie zadania usługi Stream Analytics z usługą Event Hubs jako wejściem

Postępuj zgodnie z samouczkiem [Wykrywanie oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md), aby utworzyć centrum zdarzeń, uruchomić aplikację generatora zdarzeń i utworzyć zadanie usługi Stream Analytics. (Pomiń kroki tworzenia zapytania i wyjścia. Zamiast tego zapoznaj się z następującymi sekcjami w celu skonfigurowania wyjścia usługi Functions).

## <a name="create-an-azure-redis-cache-instance"></a>Tworzenie wystąpienia usługi Azure Redis Cache

1. Tworzenie pamięci podręcznej w ramach usługi Azure Redis Cache za pomocą procedury opisanej w artykule [Tworzenie pamięci podręcznej](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Po utworzeniu pamięci podręcznej w obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**. Zanotuj wartość **Podstawowe parametry połączenia**.

   ![Zrzut ekranu parametrów połączenia usługi Azure Redis Cache](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Tworzenie funkcji w usłudze Azure Functions, która może zapisywać dane w usłudze Azure Redis Cache

1. Zobacz sekcję [Tworzenie aplikacji funkcji](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) w dokumentacji usługi Functions. Zawiera ona procedurę tworzenia aplikacji funkcji i [funkcji wyzwalanej za pomocą protokołu HTTP w usłudze Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function) w języku CSharp.  

2. Przejdź do funkcji **run.csx**. Zaktualizuj ją za pomocą następującego kodu. (Pamiętaj o zastąpieniu ciągu „\<tutaj podaj parametry połączenia z usługą redis cache\>” podstawowymi parametrami połączenia z usługą Azure Redis Cache pobranymi w poprzedniej sekcji).  

   ```csharp
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
    }    

   ```

   Gdy usługa Stream Analytics odbierze z funkcji wyjątek dotyczący zbyt dużej jednostki żądania HTTP, to zmniejszy rozmiar partii wysyłanych do usługi Functions. W ramach funkcji użyj poniższego kodu, aby sprawdzić, czy usługa Stream Analytics nie wysyła za dużych partii. Upewnij się, że wartości rozmiaru i maksymalnej liczby partii używane w funkcji są zgodne z wartościami podanymi w portalu usługi Stream Analytics.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. W edytorze tekstów utwórz plik JSON o nazwie **project.json**. Użyj poniższego kodu i zapisz go na komputerze lokalnym. Ten plik zawiera zależności pakietów NuGet, które są wymagane przez funkcję języka C#.  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. Wróć do witryny Azure Portal. Na karcie **Funkcje platformy** przejdź do Twojej funkcji. W obszarze **Narzędzia programistyczne** wybierz pozycję **Edytor usługi App Service**. 
 
   ![Zrzut ekranu edytora usługi App Service](./media/stream-analytics-with-azure-functions/image3.png)

5. W edytorze usługi App Service kliknij prawym przyciskiem myszy katalog główny i przekaż plik **project.json**. Po pomyślnym przekazaniu pliku odśwież stronę. Teraz powinien zostać wyświetlony wygenerowany automatycznie plik o nazwie **project.lock.json**. Wygenerowany automatycznie plik zawiera odwołania do plików dll określonych w pliku project.json.  

   ![Zrzut ekranu edytora usługi App Service](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Aktualizacja zadania usługi Stream Analytics za pomocą funkcji jako wyjścia

1. Otwórz zadanie usługi Stream Analytics w witrynie Azure Portal.  

2. Przejdź do funkcji, a następnie wybierz pozycje **Omówienie** > **Wyjścia** > **Dodaj**. Aby dodać nowe wyjście, wybierz pozycję **Azure Function** jako opcję ujścia. Jest dostępny nowy adapter wyjściowy usługi Functions z następującymi właściwościami:  

   |**Nazwa właściwości**|**Opis**|
   |---|---|
   |Alias danych wyjściowych| Przyjazna nazwa używana w zapytaniu zadania do przywołania wyjścia. |
   |Opcja importu| Można użyć funkcji z bieżącej subskrypcji lub, jeśli funkcja znajduje się w innej subskrypcji, podać ustawienia ręcznie. |
   |Aplikacja usługi Functions| Nazwa aplikacji usługi Functions. |
   |Funkcja| Nazwa funkcji w aplikacji usługi Functions (nazwa funkcji run.csx).|
   |Maksymalny rozmiar partii|Umożliwia ustawienie maksymalnego rozmiaru poszczególnych partii danych wyjściowych, które są przesyłane do funkcji. Domyślnie ta wartość jest równa 256 KB.|
   |Maksymalna liczba partii|Umożliwia określenie maksymalnej liczby zdarzeń w każdej z partii wysyłanych do funkcji. Wartość domyślna to 100. Ta właściwość jest opcjonalna.|
   |Klucz|Pozwala na użycie funkcji z innej subskrypcji. Podaj wartość klucza, aby uzyskać dostęp do funkcji. Ta właściwość jest opcjonalna.|

3. Podaj nazwę aliasu danych wyjściowych. W tym samouczku użyjemy nazwy **saop1** (możesz użyć dowolnej nazwy). Podaj inne szczegóły.  

4. Otwórz zadanie usługi Stream Analytics i zaktualizuj zapytanie w następujący sposób. (Pamiętaj o zastąpieniu tekstu „saop1”, jeśli ujście danych wyjściowych zostało nazwane inaczej).  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Uruchom aplikację telcodatagen.exe, uruchamiając następujące polecenie w wierszu polecenia (użyj formatu `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Uruchom zadanie usługi Stream Analytics.

## <a name="check-azure-redis-cache-for-results"></a>Sprawdzanie usługi Azure Redis Cache pod kątem wyników

1. Przejdź do witryny Azure Portal i znajdź usługę Azure Redis Cache. Wybierz pozycję **Konsola**.  

2. Użyj [poleceń pamięci podręcznej Redis Cache](https://redis.io/commands), aby sprawdzić, czy dane znajdują się w pamięci podręcznej Redis Cache. (Polecenie ma format Get {klucz}). Na przykład:

   **Get "12/19/2017 21:32:24 - 123414732"**

   To polecenie powinno wyświetlić wartość dla określonego klucza:

   ![Zrzut ekranu danych wyjściowych usługi Azure Redis Cache](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Znane problemy

Przy próbie resetowania wartości Maksymalny rozmiar partii/Maksymalna liczba partii na wartość pustą (domyślną) w witrynie Azure Portal wartość zostanie zmieniona z powrotem na wartość podaną wcześniej przy zapisywaniu. W takim przypadku ręcznie podaj wartości domyślne dla tych pól.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użycie zadania w przyszłości, możesz zatrzymać je i uruchomić ponownie później w razie potrzeby. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego przewodnika Szybki start:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu.  
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono proste zadanie usługi Stream Analytics uruchamiające usługę Azure Function. Aby dowiedzieć się więcej na temat zadań usługi Stream Analytics, kontynuuj pracę w następnym samouczku:

> [!div class="nextstepaction"]
> [Uruchamianie funkcji języka JavaScript zdefiniowanych przez użytkownika w ramach zadań usługi Stream Analytics](stream-analytics-javascript-user-defined-functions.md)