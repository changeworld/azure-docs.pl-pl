---
title: Analiza tonacji w czasie rzeczywistym usługi Twitter z usługą Azure Stream Analytics
description: W tym artykule opisano jak używać usługi Stream Analytics dla analiza tonacji w czasie rzeczywistym usługi Twitter. Instrukcje krok po kroku ze wskazówkami generowanie zdarzeń z danymi na żywo pulpitu nawigacyjnego.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: abb2a89f41340e8e2e26fa36cc20b790341618d0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60763361"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analiza tonacji w czasie rzeczywistym usługi Twitter w usłudze Azure Stream Analytics

> [!IMPORTANT] 
> Tworzenie aplikacji usługi Twitter nie jest już dostępna za pośrednictwem [apps.twitter.com](https://apps.twitter.com/). W tym samouczku jest w trakcie aktualizowana w celu uwzględnienia nowego interfejsu API usługi Twitter.

Dowiedz się, jak tworzyć rozwiązania analizującego nastroje klientów, analizy mediów społecznościowych, przenosząc zdarzenia w czasie rzeczywistym usługi Twitter do usługi Azure Event Hubs. Następnie użyj zapisu zapytanie usługi Azure Stream Analytics do analizowania danych, a następnie zapisać wyniki później lub pulpitu nawigacyjnego możesz i [usługi Power BI](https://powerbi.com/) udostępnienie szczegółowych informacji w czasie rzeczywistym.

Narzędzia do analizy mediów społecznościowych ułatwić organizacjom Zrozumienie trendów tematów. Popularne tematy są tematy i postawy, które mają dużą liczbę wpisów w mediach społecznościowych. Analiza tonacji, nazywana także *wyszukiwania opinii*, używa narzędzia do analizy mediów społecznościowych w celu ustalenia postawy kierunku produktu, pomysł i tak dalej. 

Analiza trendu w czasie rzeczywistym usługi Twitter jest świetny przykład narzędziem analizy, ponieważ modelu subskrypcji hasztag umożliwia nasłuchiwania konkretnych słów kluczowych (hasztagi) i tworzyć analizy tonacji kanału informacyjnego.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenariusz: Analiza tonacji w mediach społecznościowych w czasie rzeczywistym

Firma, która ma mediami witryny sieci Web jest zainteresowana zyskuje przewagę nad konkurencji, zawierających zawartość witryny, natychmiast odpowiednią dla jego czytników. Analiza mediów społecznościowych są używane w firmie do tematów, które są istotne dla czytników, wykonując analizy tonacji w czasie rzeczywistym danych z usługi Twitter.

Aby zidentyfikować trendów tematów w czasie rzeczywistym w serwisie Twitter, firma musi analizy w czasie rzeczywistym o wielkości tweetu i wskaźniki nastrojów klientów kluczowe tematy. Innymi słowy jest aparat analityczny analizy tonacji, która opiera się na tym mediów społecznościowych, źródła danych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku za pomocą aplikacji klienta, który jest podłączany do usługi Twitter i szuka tweety, które zawierają określone hasztagi, (które można skonfigurować). Aby uruchomić aplikację i analizowanie tweetów za pomocą usługi Azure Stream Analytics, musisz mieć następujące czynności:

* Subskrypcja platformy Azure
* Konto w serwisie Twitter 
* W przypadku aplikacji usługi Twitter i [token dostępu OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) dla tej aplikacji. Firma Microsoft zapewnia ogólne instrukcje dotyczące sposobu tworzenia aplikacji usługi Twitter w dalszej części.
* Aplikacja TwitterWPFClient odczytuje informacyjny usługi Twitter. Aby uzyskać tę aplikację, Pobierz [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) plik z repozytorium GitHub, a następnie Rozpakuj pakiet do folderu na komputerze. Jeśli chcesz zobaczyć źródło kodu i uruchomić aplikację w debugerze, możesz pobrać kod źródłowy z [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Tworzenie Centrum zdarzeń dla danych wejściowych usługi Stream Analytics

Przykładowa aplikacja generuje zdarzenia i wypycha je do Centrum zdarzeń platformy Azure. Usługi Azure event hubs są preferowaną metodą przyjmowanie zdarzeń dla usługi Stream Analytics. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Utwórz przestrzeń nazw Centrum zdarzeń i Centrum zdarzeń
W tej procedurze należy najpierw utworzyć przestrzeń nazw Centrum zdarzeń, a następnie dodaj Centrum zdarzeń do tego obszaru nazw. Przestrzenie nazw Centrum zdarzeń są używane do logicznego pogrupowania wystąpień magistrali powiązanych zdarzeń. 

1. Zaloguj się do witryny Azure portal, a następnie kliknij przycisk **Utwórz zasób** > **Internet of Things** > **Centrum zdarzeń**. 

2. W **tworzenie przestrzeni nazw** bloku, wprowadź nazwę przestrzeni nazw, takich jak `<yourname>-socialtwitter-eh-ns`. Można użyć dowolnej nazwy dla przestrzeni nazw, ale nazwa musi mieć prawidłowy dla danego adresu URL i na platformie Azure musi być unikatowa. 
    
3. Wybierz subskrypcję i Utwórz lub wybierz grupę zasobów, a następnie kliknij przycisk **Utwórz**. 

    ![Tworzenie przestrzeni nazw centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Po zakończeniu wdrażania przestrzeni nazw, należy znaleźć przestrzeń nazw Centrum zdarzeń, na liście zasobów platformy Azure. 

5. Kliknij nową przestrzeń nazw, a następnie w bloku przestrzeni nazw kliknij  **+ &nbsp;Centrum zdarzeń**. 

    ![Przycisk Dodaj Centrum zdarzeń do tworzenia nowego Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nazwa nowego Centrum zdarzeń `socialtwitter-eh`. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj je, ponieważ nazwa będą potrzebne później. Nie ma potrzeby ustaw inne opcje dla Centrum zdarzeń.

    ![Blok do tworzenia nowego Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Kliknij pozycję **Utwórz**.


### <a name="grant-access-to-the-event-hub"></a>Udzielanie dostępu do Centrum zdarzeń

Zanim proces może wysyłać dane do Centrum zdarzeń, Centrum zdarzeń musi mieć zasady, które zezwalają na odpowiedni dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1.  W bloku przestrzeni nazw zdarzeń kliknij **usługi Event Hubs** a następnie kliknij nazwę nowego Centrum zdarzeń.

2.  W bloku Centrum zdarzeń, kliknij przycisk **zasady dostępu współdzielonego** a następnie kliknij przycisk  **+ &nbsp;Dodaj**.

    >[!NOTE]
    >Upewnij się, że pracujesz z Centrum zdarzeń, a nie nazw Centrum zdarzeń.

3.  Dodawanie zasad o nazwie `socialtwitter-access` i **oświadczenia**, wybierz opcję **Zarządzaj**.

    ![Blok do tworzenia nowych zasad dostępu do Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Kliknij pozycję **Utwórz**.

5.  Po wdrożeniu zasad kliknij je na liście zasad dostępu współdzielonego.

6.  Znajdź pole o nazwie **parametry połączenia — klucz podstawowy** i kliknij przycisk kopiowania obok parametrów połączenia. 
    
    ![Kopiowanie ciągu klucza podstawowego połączenia z zasad dostępu](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Wklej parametry połączenia do edytora tekstu. Potrzebujesz te parametry połączenia dla następnej sekcji, po wprowadzeniu niektórych drobnych modyfikacji do niego.

    Parametry połączenia wyglądają następująco:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Zwróć uwagę, że parametry połączenia zawierają wiele par klucz wartość, oddzielając je średnikami: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, i `EntityPath`.  

    > [!NOTE]
    > Dla bezpieczeństwa części ciągu połączenia w przykładzie zostały usunięte.

8.  W edytorze tekstów, Usuń `EntityPath` parę z parametrów połączenia (nie zapomnij usunąć poprzedzającego ją średnika). Gdy wszystko będzie gotowe, parametry połączenia wyglądają następująco:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Skonfiguruj i uruchom aplikację klienta usługi Twitter
Aplikacja kliencka pobiera zdarzenia tweeta bezpośrednio z serwisu Twitter. Aby to zrobić, musi ona uprawnienia do wywołania API przesyłania strumieniowego w usłudze Twitter. Aby skonfigurować to uprawnienie, utworzysz aplikację w usłudze Twitter, który generuje unikatowy poświadczeń (takich jak OAuth token). Następnie można skonfigurować aplikacji klienckiej podczas wykonywania wywołań interfejsu API przy użyciu tych poświadczeń. 

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter
Jeśli nie masz już aplikację usługi Twitter, używanego na potrzeby tego samouczka, można go utworzyć. Musi już mieć konto w usłudze Twitter.

> [!NOTE]
> Dokładne procesu w usłudze Twitter do tworzenia aplikacji i uzyskiwanie kluczy, wpisów tajnych i token mogą ulec zmianie. Jeśli te instrukcje nie są zgodne, zostanie wyświetlony w witrynie Twitter, zapoznaj się z dokumentacją dla deweloperów usługi Twitter.

1. Przejdź do [strony zarządzania aplikacji usługi Twitter](https://apps.twitter.com/). 

2. Utwórz nową aplikację. 

   * W przypadku adresu URL witryny sieci Web należy określić prawidłowy adres URL. Nie ma być aktywnej witryny. (Nie można określić tylko `localhost`.)
   * Pole jest puste wywołania zwrotnego. Aplikacja kliencka, których używasz na potrzeby tego samouczka nie wymagają wywołań zwrotnych.

     ![Tworzenie aplikacji w usłudze Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Opcjonalnie można zmienić uprawnienia aplikacji tylko do odczytu.

4. Gdy aplikacja zostanie utworzona, przejdź do **klucze i tokeny dostępu** strony.

5. Kliknij przycisk, aby wygenerować token i dostęp klucz tajny tokenu dostępu.

Zachowaj te informacje przydatne, ponieważ będzie potrzebny w następnej procedurze.

>[!NOTE]
>Kluczy i wpisów tajnych dla aplikacji usługi Twitter zapewniają dostęp do konta w usłudze Twitter. Traktować te informacje jako poufne, takie same, jak hasło Twitter. Na przykład nie osadzaj te informacje w aplikacji, która udzielić innym osobom. 


### <a name="configure-the-client-application"></a>Konfigurowanie aplikacji klienta
Utworzyliśmy aplikację kliencką, która łączy się dane za pomocą usługi Twitter [API przesyłania strumieniowego w usłudze Twitter](https://dev.twitter.com/streaming/overview) służąca do gromadzenia zdarzeń tweet o określony zbiór tematów. Aplikacja używa [Sentiment140](http://help.sentiment140.com/) narzędzia typu open source, który przypisuje następujące wartości tonacji do każdego tweetu:

* 0 = ujemna
* 2 = neutral
* 4 = dodatnią

Po przypisaniu zdarzenia tweet wartości tonacji, są one wypychane do Centrum zdarzeń, który został utworzony wcześniej.

Zanim aplikacja zostanie uruchomiona, wymaga pewne informacje, takie jak klucze usługi Twitter i parametry połączenia Centrum zdarzeń. Można podać informacje o konfiguracji w następujący sposób:

* Uruchom aplikację, a następnie wprowadź kluczami, wpisami tajnymi i parametry połączenia za pomocą interfejsu użytkownika aplikacji. Jeśli to zrobisz, informacje o konfiguracji jest używany dla bieżącej sesji, ale nie jest on zapisany.
* Edytowanie pliku config aplikacji i tam ustawione wartości. Ta metoda będzie nadal występował, informacje o konfiguracji, ale oznacza to również, że te potencjalnie wrażliwe informacje są przechowywane w postaci zwykłego tekstu na komputerze.

Poniższa procedura dokumenty oba podejścia. 

1. Upewnij się, pobrałeś i rozpakowanej [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) aplikacji, zgodnie z opisem w wymaganiach wstępnych.

2. Aby ustawić wartości w czasie wykonywania (i tylko dla bieżącej sesji), uruchom `TwitterWPFClient.exe` aplikacji. Gdy aplikacja wyświetli monit, wprowadź następujące wartości:

    * Klucz klienta usługi Twitter (klucz interfejsu API).
    * Tajny klienta usługi Twitter (klucz tajny interfejsu API).
    * Token dostępu usługi Twitter.
    * Twitter klucz tajny tokenu dostępu.
    * Informacje o parametrach połączenia, który został wcześniej zapisany. Należy upewnić się, że parametry połączenia, które zostały usunięte `EntityPath` pary klucz wartość z.
    * Słowa kluczowe Twitter, które chcesz określić wskaźniki nastrojów klientów dotyczące.

   ![Aplikacja TwitterWpfClient uruchomiony oraz że przedstawiająca zasłonięte ustawienia](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Aby ustawić wartości trwałe, Edytor tekstu można otworzyć pliku TwitterWpfClient.exe.config. Następnie w `<appSettings>` elementu, w tym:

   * Ustaw `oauth_consumer_key` na klucz klienta usługi Twitter (klucz interfejsu API). 
   * Ustaw `oauth_consumer_secret` się klucz tajny klienta usługi Twitter (klucz tajny interfejsu API).
   * Ustaw `oauth_token` do tokena dostępu usługi Twitter.
   * Ustaw `oauth_token_secret` się klucz tajny tokenu dostępu serwisu Twitter.

     W dalszej części `<appSettings>` elementu, wprowadź następujące zmiany:

   * Ustaw `EventHubName` do nazwy Centrum zdarzeń (czyli wartość ścieżka jednostki).
   * Ustaw `EventHubNameConnectionString` parametry połączenia. Należy upewnić się, że parametry połączenia, które zostały usunięte `EntityPath` pary klucz wartość z.

     `<appSettings>` Sekcja wygląda podobnie jak w poniższym przykładzie. (Dla jasności i zabezpieczeń, firma Microsoft opakowane niektórych wierszy i usunąć niektóre znaki)

     ![Plik konfiguracji aplikacji TwitterWpfClient w edytorze tekstów, kluczy usługi Twitter i wpisy tajne i informacje o parametrach połączenia Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Jeśli aplikacja jeszcze nie została uruchomiona, uruchom teraz TwitterWpfClient.exe. 

5. Kliknij przycisk start zielonego do zbierania opinii społecznościowych. Zobacz zdarzenia Tweet za pomocą **CreatedAt**, **tematu**, i **SentimentScore** wartości, które są wysyłane do Centrum zdarzeń.

    ![Uruchomiona jest aplikacja TwitterWpfClient, przedstawiający lista tweetów](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Jeśli występują błędy, a nie widać strumień tweetów wyświetlane w dolnej części okna, należy dokładnie sprawdzić kluczy i wpisów tajnych. Również Sprawdź parametry połączenia (Upewnij się, że nie obejmuje `EntityPath` klucza i wartości.)


## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Teraz, gdy tweet zdarzenia są przesyłania strumieniowego w czasie rzeczywistym z usługi Twitter, możesz skonfigurować zadanie usługi Stream Analytics do analizowania tych zdarzeń w czasie rzeczywistym.

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** > **Internet of Things** > **zadania usługi Stream Analytics**.

2. Nadaj nazwę zadaniu `socialtwitter-sa-job` i określ subskrypcji, grupy zasobów i lokalizacji.

    Jest dobrym pomysłem jest umieszczenie zadania i Centrum zdarzeń w tym samym regionie, w celu uzyskania najlepszej wydajności i tak, aby nie płacić za transfer danych między regionami.

    ![Tworzenie nowego zadania usługi Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Kliknij pozycję **Utwórz**.

    Zadanie jest tworzone i portalu są wyświetlane szczegóły zadania.


## <a name="specify-the-job-input"></a>Określ dane wejściowe zadania

1. W ramach zadania usługi Stream Analytics w obszarze **topologia zadań** środku blok zadania kliknij **dane wejściowe**. 

2. W **dane wejściowe** bloku kliknij  **+ &nbsp;Dodaj** a następnie wypełnij blok następującymi wartościami:

   * **Alias danych wejściowych**: Użyj nazwy `TwitterStream`. Jeśli używasz innej nazwy, zanotuj je, ponieważ będą potrzebne później.
   * **Typ źródła**: Wybierz **strumienia danych**.
   * **Źródło**: Wybierz **Centrum zdarzeń**.
   * **Opcja importu**: Wybierz **Użyj Centrum zdarzeń z bieżącej subskrypcji**. 
   * **Przestrzeń nazw magistrali usług**: Wybierz przestrzeń nazw Centrum zdarzeń, który został utworzony wcześniej (`<yourname>-socialtwitter-eh-ns`).
   * **Centrum zdarzeń**: Wybierz Centrum zdarzeń, który został utworzony wcześniej (`socialtwitter-eh`).
   * **Nazwa zasad Centrum zdarzeń**: Wybierz zasady dostępu, który został utworzony wcześniej (`socialtwitter-access`).

     ![Utwórz nowe dane wejściowe dla zadania usługi Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Kliknij pozycję **Utwórz**.


## <a name="specify-the-job-query"></a>Określ zapytanie zadania

Stream Analytics obsługuje prosty, deklaratywny model zapytań opisujący przekształcenia. Aby dowiedzieć się więcej na temat języka, zobacz [dokumentacja języka zapytań usługi Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ten samouczek ułatwia tworzenie i testowanie kilka zapytań na danych z usługi Twitter.

Aby porównać liczbę wzmianki między tematami, można użyć [okno wirowania](https://msdn.microsoft.com/library/azure/dn835055.aspx) można pobrać liczby wzmianki według tematu co pięć sekund.

1. Zamknij **dane wejściowe** bloku, jeśli jeszcze go.

2. W **Przegląd** bloku kliknij **Edytuj zapytanie** w górnej prawej strony pola zapytania. Platforma Azure zawiera dane wejściowe i wyjściowe, które są skonfigurowane dla zadania i pozwala utworzyć kwerendę, która umożliwia przekształcenie strumienia wejściowego, ponieważ są wysyłane do wyjścia.

3. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

3. W **zapytania** bloku kliknij kropki obok `TwitterStream` dane wejściowe, a następnie wybierz pozycję **przykładowe dane z danych wejściowych**.

    ![Opcje menu, aby użyć danych przykładowych dla wpisu zadania usługi Stream Analytics, za pomocą "Przykładowe dane z danych wejściowych" wybrane](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Spowoduje to otwarcie bloku, w którym możesz określić ilość przykładowych danych, aby uzyskać zdefiniowane pod względem czasu do odczytu strumienia wejściowego.

4. Ustaw **minut** na 3 a następnie kliknij przycisk **OK**. 
    
    ![Próbkowanie strumienia wejściowego z "3 minut" wybrane opcje.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Platforma Azure 3 minut, przez które dane ze strumienia wejściowego z przykładami i powiadamia użytkownika, gdy dane przykładowe są gotowe. (Trwa krótko). 

    Przykładowe dane są przechowywane tymczasowo i są dostępne, kiedy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, przykładowe dane zostaną usunięte i należy utworzyć nowy zestaw przykładowych danych. 

5. Zmień zapytanie w edytorze kodu do następujących:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Jeśli został użyty przez Ciebie `TwitterStream` jako alias dla danych wejściowych, Zastąp aliasu dla `TwitterStream` w zapytaniu.  

    To zapytanie używa **TIMESTAMP BY** — słowo kluczowe, aby określić pole znacznika czasu w ładunku ma być używany do obliczenia danych czasowych. Jeśli to pole nie jest określony, operacja obsługi okien odbywa się na podstawie czasu, który każde zdarzenie dotarła do Centrum zdarzeń. Dowiedz się więcej, zobacz sekcję "Czas nadejścia vs czas aplikacji" [dokumentacja zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    To zapytanie również uzyskuje dostęp do sygnatury czasowej do końca każdego okna przy użyciu **System.Timestamp** właściwości.

5. Kliknij przycisk **testu**. Uruchamia zapytania względem danych, który pobrano.
    
6. Kliknij pozycję **Zapisz**. Spowoduje to zapisanie zapytania jako część zadania usługi Stream Analytics. (Nie zostanie on zapisany przykładowych danych).


## <a name="experiment-using-different-fields-from-the-stream"></a>Eksperymentu przy użyciu różnych pól ze strumienia 

Poniższa tabela zawiera listę pól, które są częścią JSON, dane przesyłane strumieniowo. Możesz eksperymentować w edytorze zapytań.

|Właściwość JSON | Definicja|
|--- | ---|
|createdAt | Czas, który został utworzony tweet|
|Temat | Temat, który jest zgodny z określonym słowem kluczowym|
|SentimentScore | Ocenę tonacji z Sentiment140|
|Autor | Uchwyt Twitter, który wysłał tweet|
|Text | Pełna treść tweetu|


## <a name="create-an-output-sink"></a>Tworzenie ujścia danych wyjściowych

Masz teraz zdefiniowany strumienia zdarzeń, danych wejściowych w celu pozyskania zdarzeń i zapytania przekształcenie w strumieniu Centrum zdarzeń. Ostatnim krokiem jest określenie ujścia danych wyjściowych dla zadania.  

W tym samouczku piszesz zdarzenia zagregowane tweet z zapytania zadania do usługi Azure Blob storage.  Można również przeprowadzać wypychanie wyniki do usługi Azure SQL Database, Azure Table storage, usługa Event Hubs lub usługi Power BI, w zależności od aplikacji wymaga.

## <a name="specify-the-job-output"></a>Określ dane wyjściowe zadania

1. W **topologia zadań** kliknij **dane wyjściowe** pole. 

2. W **dane wyjściowe** bloku kliknij  **+ &nbsp;Dodaj** a następnie wypełnij blok następującymi wartościami:

   * **Alias danych wyjściowych**: Użyj nazwy `TwitterStream-Output`. 
   * **Obiekt sink**: Wybierz pozycję **Blob storage**.
   * **Opcje importowania**: Wybierz **usługa blob storage z bieżącej subskrypcji**.
   * **Konto magazynu**. Wybierz **Utwórz nowe konto magazynu.**
   * **Konto magazynu** (drugie pole). Wprowadź `YOURNAMEsa`, gdzie `YOURNAME` jest nazwą lub inny unikatowy ciąg. Nazwę można użyć tylko małe litery i cyfry, a na platformie Azure musi być unikatowa. 
   * **Kontener**. Wprowadź polecenie `socialtwitter`.
     Nazwa kontenera i nazwy konta magazynu są używane razem do Podaj identyfikator URI dla magazynu obiektów blob, w następujący sposób: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Blok "Nowe dane wyjściowe" zadania usługi Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Kliknij pozycję **Utwórz**. 

    Platforma Azure utworzy konto magazynu i automatycznie generuje klucz. 

5. Zamknij **dane wyjściowe** bloku. 


## <a name="start-the-job"></a>Uruchamianie zadania

Dane wejściowe zadania, zapytania i wyjścia są określone. Jesteś gotowy do uruchomienia zadania usługi Stream Analytics.

1. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

2. W bloku zadania kliknij **Start**.

    ![Uruchamianie zadania usługi Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. W **zadanie rozpoczęcia** bloku dla **czas rozpoczęcia dane wyjściowe zadania**, wybierz opcję **teraz** a następnie kliknij przycisk **Start**. 

    ![Blok "Uruchamianie zadania" zadania usługi Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure powiadamia użytkownika, gdy zadanie zostało rozpoczęte, a następnie w bloku zadania stan jest wyświetlany jako **systemem**.

    ![Zadanie uruchomione](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Wyświetlanie danych wyjściowych do analizy tonacji

Po zadaniu rozpoczęło działanie i trwa jego przetwarzanie strumienia w czasie rzeczywistym usługi Twitter, można wyświetlić dane wyjściowe do analizy tonacji.

Można użyć narzędzia, takiego jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/) lub [Eksploratora usługi Azure](https://www.cerebrata.com/products/azure-explorer/introduction) Aby wyświetlić dane wyjściowe zadania w czasie rzeczywistym. W tym miejscu możesz użyć [usługi Power BI](https://powerbi.com/) rozszerzenie aplikacji dostosowanego pulpitu nawigacyjnego, tak jak pokazano na poniższym zrzucie ekranu:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Utwórz inne zapytanie w celu zidentyfikowania trendów tematów

Inne zapytanie, można użyć, aby zrozumieć opinii w serwisie Twitter jest oparty na [oknie kroczącym](https://msdn.microsoft.com/library/azure/dn835051.aspx). Aby zidentyfikować popularne tematy, możesz wyszukiwać tematy, które przekraczają wartość progową wzmianki w określonym czasie.

Do celów tego samouczka możesz sprawdzić tematów, które są wymienione ponad 20 razy w ciągu ostatnich 5 sekund.

1. W bloku zadania kliknij **zatrzymać** zatrzymania zadania. 

2. W **topologia zadań** kliknij **zapytania** pole. 

3. Zmień zapytanie do następujących:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Kliknij pozycję **Zapisz**.

5. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

6. Kliknij przycisk **Start** ponownie uruchomić zadanie przy użyciu nowego zapytania.


## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
