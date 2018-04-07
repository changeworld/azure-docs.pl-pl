---
title: Uruchom usługi Azure Functions z zadania usługi analiza strumienia Azure
description: W tym artykule opisano sposób konfigurowania usługi Azure Functions ujściem danych wyjściowych zadania usługi analiza strumienia, w przypadku obciążeń zdarzeń dysku.
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/19/2017
ms.openlocfilehash: a8eebfa0c40caa455eb20431e5cf4acb8eeb248c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="run-azure-functions-from-azure-stream-analytics-jobs"></a>Uruchom usługi Azure Functions z zadania usługi analiza strumienia Azure 

Usługi Azure Functions można uruchomić z usługi Azure Stream Analytics, konfigurując funkcje wśród sink danych wyjściowych do zadania usługi analiza strumienia. Funkcje są sterowane zdarzeniami obliczeń na żądanie środowisko, które umożliwia implementowania kodu wyzwalane przez zdarzenia występujące w Azure lub usług innych firm. Możliwość odpowiadanie na wyzwalaczy funkcji ułatwia fizycznych danych wyjściowych do zadania usługi analiza strumienia.

Analiza strumienia wywołuje funkcje za pomocą wyzwalaczy HTTP. Adapter wyjścia funkcji umożliwia użytkownikom łączenie się Stream Analytics z z funkcji tak, aby zdarzenia mogą być wyzwalane opartych na kwerendach Stream Analytics. 

Ten samouczek pokazuje, jak połączyć usługi Stream Analytics do [pamięć podręczna Redis Azure](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md), za pomocą [usługi Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Skonfiguruj uruchomienie funkcji zadania usługi analiza strumienia 

W tej sekcji przedstawiono sposób konfigurowania zadania Stream Analytics, aby uruchomić funkcję, która zapisuje dane w pamięci podręcznej Redis Azure. Zadanie Stream Analytics odczytuje zdarzenia z usługi Azure Event Hubs i przeprowadza kwerendę, która wywołuje funkcję. Ta funkcja odczytuje dane z zadania usługi analiza strumienia i zapisuje go w pamięci podręcznej Redis Azure.

![Diagram przedstawiający relacje usług Azure](./media/stream-analytics-with-azure-functions/image1.png)

Poniższe kroki są wymagane do wykonania tego zadania:
* [Utwórz zadanie usługi Stream Analytics z usługą Event Hubs jako dane wejściowe](#create-stream-analytics-job-with-event-hub-as-input)  
* [Utwórz wystąpienie pamięci podręcznej Redis Azure](#create-an-azure-redis-cache)  
* [Utwórz funkcję za pomocą usługi Azure Functions, który może zapisać danych w pamięci podręcznej Redis Azure](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Aktualizacja zadania Stream Analytics za pomocą funkcji jako dane wyjściowe](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Sprawdź, czy pamięć podręczna Redis Azure wyników](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Utwórz zadanie usługi Stream Analytics z usługą Event Hubs jako dane wejściowe

Postępuj zgodnie z [wykrywanie oszustw w czasie rzeczywistym](stream-analytics-real-time-fraud-detection.md) samouczek tworzenia Centrum zdarzeń, uruchom aplikację generator zdarzenia i utworzyć zadania usługi analiza strumienia. (Pomiń kroki umożliwiające utworzenie zapytania i dane wyjściowe. Zamiast tego zobacz następujące sekcje umożliwiające konfigurowanie dane wyjściowe funkcji).

## <a name="create-an-azure-redis-cache-instance"></a>Utwórz wystąpienie pamięci podręcznej Redis Azure

1. Tworzenie pamięci podręcznej w pamięci podręcznej Redis Azure za pomocą procedury opisanej w [tworzenia pamięci podręcznej](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Po utworzeniu pamięci podręcznej, w obszarze **ustawienia**, wybierz pozycję **klucze dostępu**. Zanotuj **parametry połączenia podstawowej**.

   ![Parametry połączenia zrzut ekranu z pamięci podręcznej Redis Azure](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Utwórz funkcję za pomocą usługi Azure Functions, który może zapisać danych w pamięci podręcznej Redis Azure

1. Zobacz [tworzenia aplikacji funkcji](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) części dokumentacji funkcji. To przeprowadzi Cię przez proces tworzenia aplikacji funkcji i [funkcji wyzwalanych przez protokół HTTP w usługi Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function), przy użyciu języka CSharp.  

2. Przejdź do **run.csx** funkcji. Zaktualizuj go następującym kodem. (Pamiętaj zastąpić "\<ciąg połączenia pamięci podręcznej redis tu\>" z parametrami połączenia głównej pamięci podręcznej Redis Azure pobrany w poprzedniej sekcji.)  

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

   Gdy Stream Analytics odbiera "HTTP żądania jednostki zbyt duże" wyjątków od funkcji, zmniejsza rozmiar partii wysyłanych do funkcji. W funkcji należy użyć poniższego kodu, aby sprawdzić, czy usługi Stream Analytics nie wysyła partie zbyt duży. Upewnij się, że maksymalna liczba i rozmiar wartości partii używany w funkcji są zgodne z wartości wprowadzone w portalu usługi analiza strumienia.

   ```csharp
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. W edytorze tekstów wybranych przez użytkownika, należy utworzyć plik JSON o nazwie **project.json**. Użyć poniższego kodu i zapisz go na komputerze lokalnym. Ten plik zawiera zależności pakietów NuGet, które są wymagane przez funkcję C#.  
   
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
 
4. Wróć do portalu Azure. Z **funkcji platformy** karcie, przejdź do funkcji. W obszarze **narzędzi programistycznych**, wybierz pozycję **Edytor usług aplikacji**. 
 
   ![Zrzut ekranu Edytor usługi aplikacji](./media/stream-analytics-with-azure-functions/image3.png)

5. W edytorze usługi aplikacji, kliknij prawym przyciskiem myszy katalogu głównego i przekazać **project.json** pliku. Po pomyślnym przekazywania Odśwież stronę. Powinien zostać wyświetlony plik wygenerowany automatycznie o nazwie **plikiem project.lock.json**. Automatycznie wygenerowany plik zawiera odwołania do plików .dll, które są określone w pliku project.json.  

   ![Zrzut ekranu Edytor usługi aplikacji](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Aktualizacja zadania Stream Analytics za pomocą funkcji jako dane wyjściowe

1. Otwórz zadania Stream Analytics w portalu Azure.  

2. Przejdź do funkcji, a następnie wybierz **omówienie** > **dane wyjściowe** > **Dodaj**. Aby dodać nowe dane wyjściowe, zaznacz **funkcji platformy Azure** opcji ujścia. Nowa karta danych wyjściowych funkcji jest dostępna, z następującymi właściwościami:  

   |**Nazwa właściwości**|**Opis**|
   |---|---|
   |Alias wyjściowy| Przyjazna dla użytkownika nazwa używana w zapytaniu zadanie odwołania do danych wyjściowych. |
   |Opcja importu| Można użyć funkcji z bieżącej subskrypcji lub skonfiguruj ustawienia ręcznie, jeśli funkcja znajduje się w innej subskrypcji. |
   |Aplikacja usługi Functions| Nazwa funkcji aplikacji. |
   |Funkcja| Nazwa funkcji w funkcji aplikacji (nazwa funkcji run.csx).|
   |Rozmiar partii maksymalna|Ustawia maksymalny rozmiar poszczególnych partii danych wyjściowych, które są przesyłane do funkcji. Domyślnie ta wartość jest równa 256 KB.|
   |Maksymalna liczba wsadów|Określa maksymalną liczbę zdarzeń w każdej z partii, wysyłany do funkcji. Wartość domyślna to 100. Ta właściwość jest opcjonalna.|
   |Klucz|Pozwala na użycie funkcji z innej subskrypcji. Podaj wartość klucza dostęp do funkcji. Ta właściwość jest opcjonalna.|

3. Podaj nazwę aliasu danych wyjściowych. W tym samouczku będziemy nazwa **saop1** (możesz użyć dowolnej nazwy wybranych przez użytkownika). Wprowadź inne szczegóły.  

4. Otwórz zadania usługi analiza strumienia i zaktualizuj zapytanie do następującego. (Upewnij się, że Zamień tekst "saop1", jeśli inne nazwy ujście danych wyjściowych).  

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

5. Uruchom aplikację telcodatagen.exe, uruchamiając następujące polecenie w wierszu polecenia (Użyj formatu `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`):  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Uruchom zadanie usługi Stream Analytics.

## <a name="check-azure-redis-cache-for-results"></a>Sprawdź, czy pamięć podręczna Redis Azure wyników

1. Przejdź do portalu Azure i Znajdź pamięć podręczna Redis Azure. Wybierz **konsoli**.  

2. Użyj [poleceń pamięci podręcznej Redis](https://redis.io/commands) Aby sprawdzić, czy dane w pamięci podręcznej Redis. (Polecenia przybiera format {klucza} Get.) Na przykład:

   **Pobierz "12/19/2017 21:32:24-123414732"**

   To polecenie należy wydrukować wartości dla określonego klucza:

   ![Dane wyjściowe zrzut ekranu z pamięci podręcznej Redis Azure](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Znane problemy

W portalu Azure, podczas próby Resetuj rozmiar partii Max-wartość maksymalna, liczba partii opróżnić (ustawienie domyślne), wartość zostanie zmieniona wartość wcześniej wprowadzonych na Zapisz. Ręcznie w tym przypadku wprowadź wartości domyślne dla tych pól.

