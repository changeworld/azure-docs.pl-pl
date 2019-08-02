---
title: Analiza tonacji w czasie rzeczywistym w usłudze Twitter przy użyciu Azure Stream Analytics
description: W tym artykule opisano, jak używać Stream Analytics do analizy tonacji w czasie rzeczywistym. Wskazówki krok po kroku dotyczące generowania zdarzeń do danych na aktywnym pulpicie nawigacyjnym.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/9/2019
ms.openlocfilehash: a0dd2499f3ddfaa1cd22a58e058c6adb7e40fd7e
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68620037"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analiza tonacji w czasie rzeczywistym w usłudze Twitter w Azure Stream Analytics

Dowiedz się, jak utworzyć rozwiązanie tonacji Analysis dla analizy mediów społecznościowych, umieszczając zdarzenia w usłudze Twitter w czasie rzeczywistym w usłudze Azure Event Hubs. Następnie napisz Azure Stream Analytics zapytanie, aby analizować dane i przechowywać wyniki do późniejszego użycia, lub utworzyć pulpit nawigacyjny [Power BI](https://powerbi.com/) , aby uzyskać wgląd w dane w czasie rzeczywistym.

Narzędzia analizy mediów społecznościowych pomagają organizacjom zrozumieć tematy trendów. Trendy dotyczące trendów to tematy i stanowiska, które mają wysoką ilość ogłoszeń na mediach społecznościowych. Analiza tonacji, która jest również nazywana *opinią*, używa narzędzi analitycznych mediów społecznościowych do określenia postanowień w kierunku produktu lub pomysłu. 

Analiza trendów w czasie rzeczywistym jest doskonałym przykładem narzędzia analitycznego, ponieważ model subskrypcji hasztagów pozwala na nasłuchiwanie określonych słów kluczowych (hasztagów) i opracowywanie analizy tonacjii źródła danych.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenariusz: Analiza tonacji mediów społecznościowych w czasie rzeczywistym

Firma, która ma witrynę internetową multimediów, chce uzyskać korzyści w stosunku do swoich konkurentów przez wypróbowanie zawartości witryny, która jest bezpośrednio istotna dla czytelników. Firma korzysta z analizy mediów społecznościowych dla tematów, które są istotne dla czytelników, wykonując analizę tonacji danych w czasie rzeczywistym.

Aby identyfikować tematy trendów w czasie rzeczywistym w serwisie Twitter, firma potrzebuje analiz w czasie rzeczywistym o woluminie tweetu i tonacji dla kluczowych tematów.

## <a name="prerequisites"></a>Wymagania wstępne
W tym przewodniku jak korzystać z aplikacji klienckiej, która nawiązuje połączenie z usługą Twitter, i wyszukuje tweety, które mają określone hasztagów (które można ustawić). Aby uruchomić aplikację i analizować tweety przy użyciu usługi Azure Streaming Analytics, musisz mieć następujące elementy:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto w usłudze [Twitter](https://twitter.com) .
* Aplikacja TwitterWPFClient, która odczytuje kanał informacyjny usługi Twitter. Aby pobrać tę aplikację, Pobierz plik [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) z usługi GitHub, a następnie Rozpakuj pakiet do folderu na komputerze. Jeśli chcesz zobaczyć kod źródłowy i uruchomić aplikację w debugerze, możesz pobrać kod źródłowy z usługi [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Tworzenie centrum zdarzeń na potrzeby danych wejściowych analizy przesyłania strumieniowego

Przykładowa aplikacja generuje zdarzenia i wypycha je do centrum zdarzeń platformy Azure. Usługa Azure Event Hub jest preferowaną metodą pozyskiwania zdarzeń dla Stream Analytics. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Tworzenie centrum zdarzeń w przestrzeni nazw i centrum zdarzeń
Utwórz przestrzeń nazw centrum zdarzeń, a następnie Dodaj centrum zdarzeń do tej przestrzeni nazw. Przestrzenie nazw centrum zdarzeń są używane do logicznego pogrupowania powiązanych wystąpień magistrali zdarzeń. 

1. Zaloguj się do Azure Portal i kliknij pozycję **Utwórz zasób** > **Internet rzeczy** > **centrum zdarzeń**. 

2. W bloku **Tworzenie przestrzeni nazw** wprowadź nazwę przestrzeni nazw, taką jak `<yourname>-socialtwitter-eh-ns`. Możesz użyć dowolnej nazwy dla przestrzeni nazw, ale nazwa musi być prawidłowa dla adresu URL i musi być unikatowa na platformie Azure. 
    
3. Wybierz subskrypcję i Utwórz lub wybierz grupę zasobów, a następnie kliknij przycisk **Utwórz**. 

    ![Tworzenie przestrzeni nazw centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Po zakończeniu wdrażania obszaru nazw Znajdź obszar nazw centrum zdarzeń na liście zasobów platformy Azure. 

5. Kliknij nową przestrzeń nazw, a następnie w bloku przestrzeń nazw kliknij pozycję  **+ &nbsp;centrum zdarzeń**. 

    ![Przycisk Dodaj centrum zdarzeń służący do tworzenia nowego centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nazwij nowe centrum `socialtwitter-eh`zdarzeń. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj je, ponieważ potrzebujesz tej nazwy później. Nie trzeba ustawiać żadnych innych opcji centrum zdarzeń.

    ![Blok służący do tworzenia nowego centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Kliknij przycisk **Utwórz**.


### <a name="grant-access-to-the-event-hub"></a>Udzielanie dostępu do centrum zdarzeń

Aby proces mógł wysyłać dane do centrum zdarzeń, centrum zdarzeń musi mieć zasady zezwalające na odpowiedni dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1.  W bloku przestrzeń nazw zdarzenia kliknij **Event Hubs** a następnie kliknij nazwę nowego centrum zdarzeń.

2.  W bloku centrum zdarzeń kliknij pozycję **zasady dostępu** współdzielonego, a następnie kliknij przycisk  **+ &nbsp;Dodaj**.

    >[!NOTE]
    >Upewnij się, że pracujesz z centrum zdarzeń, a nie z przestrzeni nazw centrum zdarzeń.

3.  Dodaj zasady o nazwach `socialtwitter-access` i dla usługi **Claims**, a następnie wybierz pozycję **Zarządzaj**.

    ![Blok służący do tworzenia nowych zasad dostępu do centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Kliknij przycisk **Utwórz**.

5.  Po wdrożeniu zasad kliknij je na liście zasad dostępu współdzielonego.

6.  Znajdź pole z etykietą **Parametry połączenia — klucz podstawowy** i kliknij przycisk kopiowania obok parametrów połączenia. 
    
    ![Kopiowanie klucza podstawowego parametrów połączenia z zasad dostępu](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Wklej parametry połączenia do edytora tekstu. Te parametry połączenia są potrzebne dla następnej sekcji, po wprowadzeniu niewielkich zmian.

    Parametry połączenia wyglądają następująco:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Zwróć uwagę, że parametry połączenia zawierają wiele par klucz-wartość, oddzielone średnikami `Endpoint`: `SharedAccessKeyName`, `SharedAccessKey`, i `EntityPath`.  

    > [!NOTE]
    > W celu zapewnienia bezpieczeństwa części ciągu połączenia w przykładzie zostały usunięte.

8.  W edytorze tekstów Usuń `EntityPath` parę z parametrów połączenia (Pamiętaj, aby usunąć średnik, który poprzedza go). Gdy skończysz, parametry połączenia wyglądają następująco:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurowanie i uruchamianie aplikacji klienckiej usługi Twitter
Aplikacja kliencka pobiera zdarzenia tweetu bezpośrednio z usługi Twitter. Aby to zrobić, musi mieć uprawnienia do wywoływania interfejsów API przesyłania strumieniowego w usłudze Twitter. Aby skonfigurować to uprawnienie, należy utworzyć aplikację w usłudze Twitter, która generuje unikatowe poświadczenia (na przykład token OAuth). Następnie można skonfigurować aplikację kliencką tak, aby korzystała z tych poświadczeń podczas wykonywania wywołań interfejsu API. 

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter
Jeśli nie masz jeszcze aplikacji usługi Twitter, której możesz użyć do tego przewodnika, możesz ją utworzyć. Musisz mieć już konto w usłudze Twitter.

> [!NOTE]
> Dokładny proces w serwisie Twitter służący do tworzenia aplikacji i uzyskiwania kluczy, wpisów tajnych i tokenu może ulec zmianie. Jeśli te instrukcje nie pasują do zawartości widocznej w witrynie Twitter, zapoznaj się z dokumentacją dla deweloperów w usłudze Twitter.

1. W przeglądarce sieci Web przejdź do usługi [Twitter dla deweloperów](https://developer.twitter.com/en/apps), a następnie wybierz pozycję **Utwórz aplikację**. Może zostać wyświetlony komunikat informujący o konieczności zastosowania konta dewelopera usługi Twitter. Możesz to zrobić bezpłatnie, a po zatwierdzeniu aplikacji powinna zostać wyświetlona wiadomość e-mail z potwierdzeniem. Zatwierdzenie konta dewelopera może potrwać kilka dni.

   ![Potwierdzenie konta dewelopera usługi Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Potwierdzenie konta dewelopera usługi Twitter")

   ![Szczegóły aplikacji usługi Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Szczegóły aplikacji usługi Twitter")

2. Na stronie **Create an application (Tworzenie aplikacji)** podaj szczegóły nowej aplikacji, a następnie wybierz pozycję **Create your Twitter application (Utwórz aplikację usługi Twitter)** .

   ![Szczegóły aplikacji usługi Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Szczegóły aplikacji usługi Twitter")

3. Na stronie aplikacja wybierz kartę **klucze i tokeny** , a następnie skopiuj wartości **klucza interfejsu API klienta** i **klucza tajnego interfejsu API konsumenta**. Ponadto w celu wygenerowania tokenów dostępu wybierz pozycję **Utwórz** w obszarze **klucz dostępu token i dostęp do klucza tajnego tokenu** dostępu. Skopiuj wartości pól **Access Token (Token dostępu)** i **Access Token Secret (Klucz tajny tokenu dostępu)** .

    ![Szczegóły aplikacji usługi Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Szczegóły aplikacji usługi Twitter")

Zapisz wartości dotyczące aplikacji usługi Twitter. Potrzebujesz wartości w dalszej części tego artykułu.

>[!NOTE]
>Klucze i wpisy tajne aplikacji usługi Twitter zapewniają dostęp do konta usługi Twitter. Traktuj te informacje jako poufne, tak samo jak hasło usługi Twitter. Na przykład nie osadzaj tych informacji w aplikacji, którą przekazujesz innym osobom. 


### <a name="configure-the-client-application"></a>Konfigurowanie aplikacji klienckiej
Utworzyliśmy aplikację kliencką, która nawiązuje połączenie z danymi usługi Twitter przy użyciu [interfejsów API przesyłania strumieniowego usługi Twitter](https://dev.twitter.com/streaming/overview) w celu zbierania zdarzeń tweetów dotyczących określonego zestawu tematów. Aplikacja używa narzędzia [Sentiment140](http://help.sentiment140.com/) Open Source, które przypisuje następującą wartość tonacji do każdego tweetu:

* 0 = ujemna
* 2 = neutralny
* 4 = pozytywne

Po przypisaniu wartości tonacji przez zdarzenia tweetu są one przekazywane do centrum zdarzeń utworzonego wcześniej.

Przed uruchomieniem aplikacji wymagane są pewne informacje, takie jak klucze serwisu Twitter i parametry połączenia centrum zdarzeń. Informacje o konfiguracji można podać w następujący sposób:

* Uruchom aplikację, a następnie użyj interfejsu użytkownika aplikacji, aby wprowadzić klucze, wpisy tajne i parametry połączenia. Jeśli to zrobisz, informacje o konfiguracji są używane dla bieżącej sesji, ale nie są zapisywane.
* Edytuj plik. config aplikacji i ustaw dla niego wartości. Takie podejście utrzymuje informacje o konfiguracji, ale oznacza również, że te potencjalnie poufne informacje są przechowywane w postaci zwykłego tekstu na komputerze.

W poniższej procedurze przedstawiono oba podejścia. 

1. Upewnij się, że pobrano i rozpakowane aplikację [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) , zgodnie z opisem w sekcji wymagania wstępne.

2. Aby ustawić wartości w czasie wykonywania (i tylko dla bieżącej sesji), uruchom `TwitterWPFClient.exe` aplikację. Gdy aplikacja zostanie pożądana, wprowadź następujące wartości:

    * Klucz klienta usługi Twitter (klucz interfejsu API).
    * Wpis tajny klienta usługi Twitter (klucz tajny interfejsu API).
    * Token dostępu w usłudze Twitter.
    * Wpis tajny tokenu dostępu w usłudze Twitter.
    * Zapisane wcześniej informacje o parametrach połączenia. Upewnij się, że używasz parametrów połączenia, z których usunięto `EntityPath` parę klucz-wartość z.
    * Słowa kluczowe usługi Twitter, dla których chcesz określić tonacji.

   ![Aplikacja TwitterWpfClient z systemem, pokazująca ustawienia zasłonięte](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Aby trwale ustawić wartości, użyj edytora tekstów, aby otworzyć plik TwitterWpfClient. exe. config. Następnie w `<appSettings>` elemencie wykonaj następujące czynności:

   * Ustaw `oauth_consumer_key` wartość klucza klienta usługi Twitter (klucz interfejsu API). 
   * Ustaw `oauth_consumer_secret` na wpis tajny klienta usługi Twitter (klucz tajny interfejsu API).
   * Ustaw `oauth_token` na token dostępu w usłudze Twitter.
   * Ustaw `oauth_token_secret` klucz tajny tokenu dostępu w usłudze Twitter.

     W dalszej `<appSettings>` części elementu wprowadź następujące zmiany:

   * Ustaw `EventHubName` nazwę centrum zdarzeń (czyli wartość ścieżki jednostki).
   * Ustaw `EventHubNameConnectionString` parametry połączenia. Upewnij się, że używasz parametrów połączenia, z których usunięto `EntityPath` parę klucz-wartość z.

     `<appSettings>` Sekcja wygląda jak w poniższym przykładzie. (W celu zapewnienia przejrzystości i bezpieczeństwa zawinięte zostały pewne wiersze i usunięto kilka znaków).

     ![Plik konfiguracji aplikacji TwitterWpfClient w edytorze tekstów, przedstawiający klucze i wpisy tajne serwisu Twitter oraz informacje o parametrach połączenia centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Jeśli aplikacja nie została jeszcze uruchomiona, uruchom program TwitterWpfClient. exe teraz. 

5. Kliknij zielony przycisk Start, aby zebrać tonacji społecznościowe. Zobaczysz zdarzenia tweetu z wartościami **CreatedAt**, **temat**i **SentimentScore** wysyłanymi do centrum zdarzeń.

    ![Aplikacja TwitterWpfClient z systemem, pokazująca listę tweetów](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Jeśli zobaczysz błędy i nie zobaczysz strumienia tweetów wyświetlanych w dolnej części okna, należy dwukrotnie sprawdzić klucze i wpisy tajne. Sprawdź również parametry połączenia (Upewnij się, że nie zawierają `EntityPath` klucza i wartości).


## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Teraz, gdy zdarzenia tweetu są przesyłane strumieniowo w czasie rzeczywistym z serwisu Twitter, można skonfigurować zadanie Stream Analytics, aby analizować te zdarzenia w czasie rzeczywistym.

1. W Azure Portal kliknij pozycję **Utwórz zasób** > **Internet rzeczy** > **zadania Stream Analytics**.

2. Nazwij zadanie `socialtwitter-sa-job` i określ subskrypcję, grupę zasobów i lokalizację.

    Dobrym pomysłem jest umieszczenie zadania i centrum zdarzeń w tym samym regionie w celu uzyskania najlepszej wydajności, aby nie zapłaciła za transfer danych między regionami.

    ![Tworzenie nowego zadania Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Kliknij przycisk **Utwórz**.

    Zadanie zostanie utworzone, a w portalu zostaną wyświetlone szczegóły zadania.


## <a name="specify-the-job-input"></a>Określ dane wejściowe zadania

1. W ramach zadania Stream Analytics w obszarze **topologia zadania** w środkowym bloku zadania kliknij pozycję **dane wejściowe**. 

2. W bloku **dane wejściowe** kliknij pozycję  **+ &nbsp;Dodaj** , a następnie wypełnij blok przy użyciu następujących wartości:

   * **Alias danych wejściowych**: Użyj nazwy `TwitterStream`. Jeśli używasz innej nazwy, zanotuj ją, ponieważ będzie potrzebna później.
   * **Typ źródła**: Wybierz pozycję **strumień danych**.
   * **Źródło**: Wybierz pozycję **centrum zdarzeń**.
   * **Opcja importowania**: Wybierz pozycję **Użyj centrum zdarzeń z bieżącej subskrypcji**. 
   * **Przestrzeń nazw usługi Service Bus**: Wybierz utworzoną wcześniej przestrzeń nazw centrum zdarzeń (`<yourname>-socialtwitter-eh-ns`).
   * **Centrum zdarzeń**: Wybierz utworzone wcześniej centrum zdarzeń (`socialtwitter-eh`).
   * **Nazwa zasad centrum zdarzeń**: Wybierz utworzone wcześniej zasady dostępu (`socialtwitter-access`).

     ![Utwórz nowe dane wejściowe dla zadania usługi Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Kliknij przycisk **Utwórz**.


## <a name="specify-the-job-query"></a>Określ zapytanie dotyczące zadania

Stream Analytics obsługuje prosty, deklaratywny model zapytań, który opisuje przekształcenia. Aby dowiedzieć się więcej o języku, zobacz [informacje dotyczące języka zapytań Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Ten przewodnik ułatwia tworzenie i testowanie kilku zapytań za pośrednictwem danych usługi Twitter.

Aby porównać liczbę wzmianki między tematami, można użyć [okna wirowania](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) , aby uzyskać liczbę wzmianki według tematu co pięć sekund.

1. Zamknij blok **dane wejściowe** , jeśli jeszcze tego nie zrobiono.

2. W bloku **Przegląd** kliknij pozycję **Edytuj zapytanie** w prawym górnym rogu pola zapytania. Na platformie Azure są wyświetlane dane wejściowe i wyjściowe, które są skonfigurowane dla zadania, a także można utworzyć zapytanie, które pozwala przekształcić strumień wejściowy w taki sposób, w jaki jest wysyłany do danych wyjściowych.

3. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

3. W bloku **zapytania** kliknij kropki obok `TwitterStream` danych wejściowych, a następnie wybierz pozycję **dane przykładowe z danych wejściowych**.

    ![Opcje menu służące do korzystania z przykładowych danych dla wpisu zadania usługi Stream Analytics z wybraną opcją "przykładowe dane z danych wejściowych"](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Spowoduje to otwarcie bloku, który umożliwia określenie ilości przykładowych danych do pobrania, zdefiniowanych w warunkach czas odczytu strumienia wejściowego.

4. Ustaw **minuty** na 3, a następnie kliknij przycisk **OK**. 
    
    ![Opcje próbkowania strumienia wejściowego z wybraną opcją "3 minuty".](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Dane z strumienia wejściowego są dostępne 3 minuty i powiadamiane, gdy dane przykładowe są gotowe. (Trwa to krótko). 

    Przykładowe dane są przechowywane tymczasowo i są dostępne, kiedy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, dane przykładowe są odrzucane i konieczne będzie utworzenie nowego zestawu przykładowych danych. 

5. Zmień zapytanie w edytorze kodu na następujące:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Jeśli nie użyto `TwitterStream` jako aliasu dla danych wejściowych, Zastąp swój `TwitterStream` alias w zapytaniu.  

    To zapytanie używa słowa kluczowego **timestamp by** w celu określenia pola sygnatury czasowej w ładunku, który ma być używany podczas obliczeń czasowych. Jeśli to pole nie zostanie określone, operacja okna jest wykonywana przy użyciu czasu, w którym każde zdarzenie dotarło do centrum zdarzeń. Więcej informacji znajduje się w sekcji "godzina przybycia i czas aplikacji" w [Stream Analytics odwołania do zapytania](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

    To zapytanie również uzyskuje dostęp do sygnatury czasowej dla końca każdego okna przy użyciu właściwości **System. timestamp** .

5. Kliknij przycisk **Testuj**. Zapytanie jest wykonywane względem danych, które zostały próbkowane.
    
6. Kliknij polecenie **Zapisz**. Spowoduje to zapisanie zapytania jako części zadania usługi Stream Analytics. (Dane przykładowe nie są zapisywane).


## <a name="experiment-using-different-fields-from-the-stream"></a>Eksperymentowanie przy użyciu różnych pól ze strumienia 

W poniższej tabeli wymieniono pola, które są częścią danych strumienia JSON. Możesz korzystać z bezpłatnego eksperymentu w edytorze zapytań.

|Właściwość JSON | Definicja|
|--- | ---|
|CreatedAt | Godzina utworzenia tweetu|
|Temat | Temat, który jest zgodny z określonym słowem kluczowym|
|SentimentScore | Wynik tonacji z Sentiment140|
|Autor | Uchwyt usługi Twitter, który wysłał Tweet|
|Text | Cała treść tweetu|


## <a name="create-an-output-sink"></a>Tworzenie ujścia danych wyjściowych

Zdefiniowano teraz strumień zdarzeń, dane wejściowe centrum zdarzeń do zdarzeń pozyskiwania oraz zapytanie w celu przeprowadzenia transformacji przez strumień. Ostatnim krokiem jest zdefiniowanie ujścia danych wyjściowych dla zadania.  

W tym przewodniku krok po kroku napiszesz zagregowane zdarzenia tweetu z kwerendy zadań do usługi Azure Blob Storage.  Możesz również wypchnąć wyniki do Azure SQL Database, Azure Table Storage, Event Hubs lub Power BI, w zależności od potrzeb aplikacji.

## <a name="specify-the-job-output"></a>Określ dane wyjściowe zadania

1. W sekcji **topologia zadania** kliknij pole **dane wyjściowe** . 

2. W bloku dane **wyjściowe** kliknij pozycję  **+ &nbsp;Dodaj** , a następnie wypełnij blok przy użyciu następujących wartości:

   * **Alias danych wyjściowych**: Użyj nazwy `TwitterStream-Output`. 
   * **Ujścia**: Wybierz pozycję **Blob storage**.
   * **Opcje importowania**: Wybierz pozycję **Użyj magazynu obiektów blob z bieżącej subskrypcji**.
   * **Konto magazynu**. Wybierz pozycję **Utwórz nowe konto magazynu.**
   * **Konto magazynu** (drugie pole). Wprowadź `YOURNAMEsa`, gdzie `YOURNAME` to nazwa lub inny ciąg unikatowy. Nazwę można użyć tylko małe litery i cyfry, a na platformie Azure musi być unikatowa. 
   * **Kontener**. Wprowadź polecenie `socialtwitter`.
     Nazwa konta magazynu i nazwa kontenera są używane razem w celu zapewnienia identyfikatora URI dla magazynu obiektów blob, takiego jak: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Blok "nowe dane wyjściowe" dla zadania Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Kliknij przycisk **Utwórz**. 

    Platforma Azure tworzy konto magazynu i automatycznie generuje klucz. 

5. Zamknij blok dane **wyjściowe** . 


## <a name="start-the-job"></a>Uruchamianie zadania

Określono dane wejściowe zadania, zapytania i wyjścia. Możesz przystąpić do uruchomienia zadania Stream Analytics.

1. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

2. W bloku zadania kliknij przycisk **Uruchom**.

    ![Uruchamianie zadania Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. W bloku **zadania uruchamiania** , w polu **czas rozpoczęcia zadań wyjściowych**, wybierz pozycję **teraz** , a następnie kliknij przycisk **Uruchom**. 

    ![Blok "Uruchom zadanie" dla zadania Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    System Azure powiadamia użytkownika, gdy zadanie zostało uruchomione, a w bloku zadanie stan jest wyświetlany jako **uruchomiony**.

    ![Uruchomiono zadanie](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Wyświetlanie danych wyjściowych dla analizy tonacji

Po rozpoczęciu pracy zadania i przetworzeniu strumienia usługi Twitter w czasie rzeczywistym można wyświetlić dane wyjściowe analizy tonacji.

Możesz użyć narzędzia, takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com/) lub [Eksplorator platformy Azure](https://www.cerebrata.com/products/azure-explorer/introduction) , aby wyświetlić dane wyjściowe zadania w czasie rzeczywistym. W tym miejscu możesz użyć [Power BI](https://powerbi.com/) , aby zwiększyć swoją aplikację w celu uwzględnienia dostosowanego pulpitu nawigacyjnego, takiego jak przedstawiony na poniższym zrzucie ekranu:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Utwórz kolejne zapytanie w celu zidentyfikowania tematów trendów

Inne zapytanie, którego można użyć do zrozumienia usługi Twitter tonacji, zależy od [przesuwanego okna](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics). Aby zidentyfikować tematy trendu, należy poszukać tematów, które przecinają wartość progową dla wzmianki w określonym czasie.

Aby uzyskać informacje na temat tego, jak to zrobić, należy sprawdzić tematy wymienione powyżej 20 razy w ciągu ostatnich 5 sekund.

1. W bloku zadania kliknij przycisk **Zatrzymaj** , aby zatrzymać zadanie. 

2. W sekcji **topologia zadania** kliknij pole **zapytania** . 

3. Zmień zapytanie na następujące:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Kliknij polecenie **Zapisz**.

5. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

6. Kliknij przycisk **Uruchom** , aby ponownie uruchomić zadanie przy użyciu nowego zapytania.


## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dalszą pomoc, Wypróbuj nasz [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
