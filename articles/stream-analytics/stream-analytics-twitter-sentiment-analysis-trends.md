---
title: W czasie rzeczywistym analizy wskaźniki nastrojów klientów Twitter z usługą Azure Stream Analytics
description: W tym artykule opisano, jak używać usługi Stream Analytics w czasie rzeczywistym analizy wskaźniki nastrojów klientów usługi Twitter. Wskazówki krok po kroku od wygenerowania zdarzenia do danych na żywo pulpitu nawigacyjnego.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 4c77c8a7209825477929ddc0997b75ace5fe04a1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>W czasie rzeczywistym analizy usługi Azure Stream Analytics wskaźniki nastrojów klientów usługi Twitter

Dowiedz się, jak zbudować rozwiązanie do analizy wskaźniki nastrojów klientów w celu wykonania analizy mediów społecznościowych przełączając zdarzenia Twitter w czasie rzeczywistym w usłudze Azure Event Hubs. Następnie użyj zapisu zapytań usługi analiza strumienia Azure do analizowania danych, a następnie zapisać wyniki dla później lub pulpit nawigacyjny i [usługi Power BI](https://powerbi.com/) do wgląd w czasie rzeczywistym.

Narzędzia do analizy mediów społecznościowych pomagające organizacjom Zrozumienie trendów tematów. Tematy trendów są tematy i stanowisk, w których znajduje się duża liczba wpisów w mediów społecznościowych. Wskaźniki nastrojów klientów analizy, która jest również nazywany *wyszukiwania opinii*, używa narzędzia do analizy mediów społecznościowych w celu określenia stanowisk kierunku produktu, pomysł i tak dalej. 

W czasie rzeczywistym analizy trendów w serwisie Twitter jest doskonałym przykład narzędzia do analizy, ponieważ subskrypcji hasztagiem umożliwia nasłuchiwania określonych słów kluczowych (hashtagów) i opracowanie analizy wskaźniki nastrojów klientów źródła danych.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scenariusz: Mediów społecznościowych wskaźniki nastrojów klientów analizy w czasie rzeczywistym

Firma, która ma nośnika wiadomości witryny sieci Web jest zainteresowana uzyskanie korzyści wyprzedzenia konkurencji przez zawierających zawartość witryny, natychmiast istotne dla swoich czytelników. Firma używa analizy mediów społecznościowych na tematy, które są istotne dla czytników wykonując wskaźniki nastrojów klientów w czasie rzeczywistym analizy danych Twitter.

Aby zidentyfikować tematy trendów w czasie rzeczywistym w serwisie Twitter, firma potrzebuje kluczowe tematy analiz w czasie rzeczywistym o wielkości tweet i wskaźniki nastrojów klientów. Innymi słowy jest aparat analizy analytics wskaźniki nastrojów klientów, oparty na tym nośniku społecznościowych źródła danych.

## <a name="prerequisites"></a>Wymagania wstępne
W tym samouczku użyjesz aplikacji klienckiej, która łączy się z serwisem Twitter oraz szuka tweetów mających niektórych hashtagów (które można ustawić). Aby uruchomić aplikację i analizować tweetów przy użyciu usługi analiza strumienia Azure, należy dysponować następującymi elementami:

* Subskrypcja platformy Azure
* Konto w serwisie Twitter 
* W przypadku aplikacji Twitter i [token dostępu OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) dla tej aplikacji. Firma Microsoft udostępnia ogólne instrukcje dotyczące tworzenia aplikacji Twitter później.
* Aplikacja TwitterWPFClient odczytuje kanału informacyjnego usługi Twitter. Aby uzyskać tę aplikację, należy pobrać [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) plik z serwisu GitHub, a następnie Rozpakuj pakiet do folderu na komputerze. Jeśli chcesz wyświetlić źródła kodu i uruchomić aplikację w debugerze, można pobrać kodu źródłowego z [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Tworzenie Centrum zdarzeń dla analizy strumienia danych wejściowych

Przykładowa aplikacja generuje zdarzenia i umieszcza je w Centrum zdarzeń platformy Azure. Usługa Azure event hubs są preferowaną metodą wprowadzanie zdarzeń dla usługi Stream Analytics. Aby uzyskać więcej informacji, zobacz [dokumentacji usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Tworzenie Centrum zdarzeń w przestrzeni nazw i Centrum zdarzeń
W tej procedurze należy najpierw utworzyć przestrzeń nazw Centrum zdarzeń, a następnie Centrum zdarzeń należy dodać do tej przestrzeni nazw. Przestrzenie nazw Centrum zdarzeń są używane do logicznego grupowania wystąpień magistrali powiązanych zdarzeń. 

1. Zaloguj się do portalu Azure, a następnie kliknij przycisk **Utwórz zasób** > **Internetu rzeczy** > **Centrum zdarzeń**. 

2. W **tworzenie przestrzeni nazw** bloku, wprowadź nazwę przestrzeni nazw, takich jak `<yourname>-socialtwitter-eh-ns`. Można użyć dowolnej nazwy przestrzeni nazw, ale nazwa musi być prawidłowa dla danego adresu URL i między Azure musi być unikatowa. 
    
3. Wybierz subskrypcję i Utwórz lub wybierz grupę zasobów, a następnie kliknij przycisk **Utwórz**. 

    ![Tworzenie przestrzeni nazw Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Po zakończeniu przestrzeń nazw wdrażania można znaleźć przestrzeni nazw zdarzenia koncentratora na liście zasobów platformy Azure. 

5. Kliknij nowy obszar nazw, a w bloku przestrzeni nazw kliknij  **+ &nbsp;Centrum zdarzeń**. 

    ![Przycisk Dodaj Centrum zdarzeń do tworzenia nowego Centrum zdarzeń ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Nazwa nowego Centrum zdarzeń `socialtwitter-eh`. Można użyć innej nazwy. Jeśli to zrobisz, zanotuj, ponieważ później potrzebne. Nie ma potrzeby ustaw inne opcje dla Centrum zdarzeń.

    ![Blok do tworzenia nowego Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Kliknij przycisk **Utwórz**.


### <a name="grant-access-to-the-event-hub"></a>Udziel dostępu do Centrum zdarzeń

Aby proces może wysyłać dane do Centrum zdarzeń, Centrum zdarzeń muszą mieć zasadę, która umożliwia uzyskanie odpowiedniego dostępu. Zasady dostępu generuje ciąg połączenia, który zawiera informacje o autoryzacji.

1.  W bloku przestrzeni nazw zdarzeń, kliknij przycisk **usługi Event Hubs** , a następnie kliknij nazwę nowego Centrum zdarzeń.

2.  W bloku Centrum zdarzeń, kliknij przycisk **zasady dostępu współużytkowanego** , a następnie kliknij przycisk  **+ &nbsp;Dodaj**.

    >[!NOTE]
    >Upewnij się, że pracujesz z Centrum zdarzeń, a nie nazw Centrum zdarzeń.

3.  Dodaj zasady o nazwie `socialtwitter-access` i **oświadczeń**, wybierz pozycję **Zarządzaj**.

    ![Blok do tworzenia nowych zasad dostępu do Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Kliknij przycisk **Utwórz**.

5.  Po wdrożeniu zasad, kliknij go na liście zasady dostępu współdzielonego.

6.  Znajdź pole o nazwie **klucz podstawowy ciąg połączenia** i kliknij przycisk Kopiuj obok ciągu połączenia. 
    
    ![Kopiowanie klucza podstawowego połączenia ciągu z zasad dostępu](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Wklej parametry połączenia w edytorze tekstu. Należy tego ciągu połączenia dla następnej sekcji, po wprowadzeniu niektóre zmiany mała do niego.

    Parametry połączenia wygląda następująco:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Powiadomienie, że parametry połączenia zawierają wiele par klucz wartość, oddzielając je średnikami: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, i `EntityPath`.  

    > [!NOTE]
    > Dla bezpieczeństwa zostały usunięte części parametrów połączenia w przykładzie.

8.  W edytorze tekstu, Usuń `EntityPath` parę z ciągu połączenia (nie zapomnij Usuń średnik przechwyceniem). Gdy wszystko będzie gotowe, ciąg połączenia wygląda następująco:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Skonfiguruj i uruchom aplikację klienta usługi Twitter
Aplikacja kliencka pobiera zdarzenia tweet bezpośrednio z usługą Twitter. Aby to zrobić, wymaga uprawnienia do wywoływania usługi Twitter API przesyłania strumieniowego. Aby skonfigurować te uprawnienia, utworzyć aplikację w serwisie Twitter, generowany unikatowy poświadczeń (takich jak token OAuth). Następnie można skonfigurować aplikacji klienckiej, aby używać tych poświadczeń podczas wykonywania wywołań interfejsu API. 

### <a name="create-a-twitter-application"></a>Tworzenie aplikacji usługi Twitter
Jeśli nie masz już aplikację usługi Twitter, która służy do celów tego samouczka, można go utworzyć. Musi już konta w usłudze Twitter.

> [!NOTE]
> Dokładne proces w ramach Twitter do tworzenia aplikacji i pobierania kluczy, kluczy tajnych i token mogą ulec zmianie. Jeśli te instrukcje nie są zgodne, można znaleźć w witrynie Twitter, zapoznaj się z dokumentacją dewelopera usługi Twitter.

1. Przejdź do [strony zarządzania aplikacji Twitter](https://apps.twitter.com/). 

2. Utwórz nową aplikację. 

    * Adres URL witryny sieci Web Określ prawidłowy adres URL. Nie ma być lokacją na żywo. (Nie można określić tylko `localhost`.)
    * Pole jest puste wywołania zwrotnego. Aplikacja kliencka używanej w tym samouczku nie wymaga wywołania zwrotne.

    ![Tworzenie aplikacji w usłudze Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Opcjonalnie można zmienić uprawnienia aplikacji tylko do odczytu.

4. Po utworzeniu aplikacji przejdź do **kluczy i tokenów dostępu** strony.

5. Kliknij przycisk, aby wygenerować dostępu i token klucz tajny tokenu dostępu.

Zachowaj te informacje przydatne, ponieważ będzie potrzebny w następnej procedurze.

>[!NOTE]
>Klucze i klucze tajne aplikacji Twitter zapewniają dostęp do konta w usłudze Twitter. Traktować te informacje jako poufne, takie same, jak hasło usługi Twitter. Na przykład nie osadzaj te informacje w aplikacji do przekazania do innych użytkowników. 


### <a name="configure-the-client-application"></a>Konfigurowanie aplikacji klienta
Utworzyliśmy aplikacji klienckiej, która łączy się przy użyciu danych Twitter [API przesyłania strumieniowego w serwisie Twitter](https://dev.twitter.com/streaming/overview) służąca do gromadzenia zdarzeń tweet o określony zbiór tematów. Aplikacja używa [Sentiment140](http://help.sentiment140.com/) narzędzi typu open source, który przypisuje do każdego tweet następującą wartość wskaźniki nastrojów klientów:

* 0 = ujemna
* 2 = neutral
* 4 = dodatnią

Po przypisaniu zdarzenia tweet wartość wskaźniki nastrojów klientów, są one przenoszone do Centrum zdarzeń, który został utworzony wcześniej.

Przed uruchomieniem aplikacji, wymaga pewne informacje, takie jak klucze Twitter i parametry połączenia Centrum zdarzeń. Można podać informacje o konfiguracji w następujący sposób:

* Uruchom aplikację, a następnie wprowadź kluczy, kluczy tajnych i parametry połączenia za pomocą interfejsu użytkownika aplikacji. Jeśli to zrobisz, informacje o konfiguracji jest używany dla bieżącej sesji, ale nie jest on zapisany.
* Edytowanie pliku config aplikacji i tam ustawione wartości. Ta metoda będzie nadal występował, informacje o konfiguracji, ale oznacza to również, że te potencjalnie wrażliwe informacje są przechowywane w postaci zwykłego tekstu na komputerze.

Poniższa procedura dokumentów obu podejść. 

1. Upewnij się, że zostały pobrane i rozpakowane [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) aplikacji, wymienione w wymaganiach wstępnych.

2. Aby ustawić wartości w czasie wykonywania (i tylko dla bieżącej sesji), uruchom `TwitterWPFClient.exe` aplikacji. Gdy aplikacja wyświetli monit, wprowadź następujące wartości:

    * Klucz klienta usługi Twitter (klucz interfejsu API).
    * Tajny klienta usługi Twitter (klucz tajny interfejsu API).
    * Token dostępu usługi Twitter.
    * Twitter klucz tajny tokenu dostępu.
    * Ciągu połączenia, który został wcześniej zapisany. Należy upewnić się, że parametry połączenia, które usunięto `EntityPath` pary klucz wartość z.
    * Słowa kluczowe Twitter, które chcesz określić wskaźniki nastrojów klientów dla.

   ![Aplikacja TwitterWpfClient uruchomiony, pokazujący ustawienia pośrednie](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Aby ustawić wartości trwale, otworzyć plik TwitterWpfClient.exe.config za pomocą edytora tekstu. Następnie w `<appSettings>` element, w tym:

    * Ustaw `oauth_consumer_key` do klucz klienta usługi Twitter (klucz interfejsu API). 
    * Ustaw `oauth_consumer_secret` do klucz tajny klienta usługi Twitter (klucz tajny interfejsu API).
    * Ustaw `oauth_token` do tokenu dostępu usługi Twitter.
    * Ustaw `oauth_token_secret` do klucz tajny tokenu dostępu usługi Twitter.

    W dalszej `<appSettings>` element, wprowadź następujące zmiany:

    * Ustaw `EventHubName` do nazwy Centrum zdarzeń (oznacza to, że wartość ścieżki jednostek).
    * Ustaw `EventHubNameConnectionString` w parametrach połączenia. Należy upewnić się, że parametry połączenia, które usunięto `EntityPath` pary klucz wartość z.

    `<appSettings>` Sekcji wygląda jak w następującym przykładzie. (Dla jasności i zabezpieczeń, możemy opakowana wiersze i usunąć niektóre znaki.)

    ![TwitterWpfClient pliku konfiguracji aplikacji w edytorze tekstów, przedstawiający Twitter kluczy i kluczy tajnych i informacje o parametrach połączenia Centrum zdarzeń](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Jeśli nie została już uruchomić aplikację, uruchom teraz TwitterWpfClient.exe. 

5. Kliknij przycisk start zielony zbierać społecznościowych wskaźniki nastrojów klientów. Zobacz Tweet zdarzeń o **CreatedAt**, **tematu**, i **SentimentScore** wartości są wysyłane do Centrum zdarzeń.

    ![Uruchomiona jest aplikacja TwitterWpfClient, przedstawiający listę tweetów](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Jeśli występuje błąd, a nie widzisz strumień tweetów wyświetlane w dolnej części okna, należy dokładnie sprawdzić kluczy i kluczy tajnych. Sprawdź również parametry połączenia (Upewnij się, że nie obejmuje `EntityPath` kluczy i wartości.)


## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics

Teraz, gdy zdarzenia tweet strumieniowe w czasie rzeczywistym z serwisem Twitter, można skonfigurować zadania usługi analiza strumienia do analizowania tych zdarzeń w czasie rzeczywistym.

1. W portalu Azure kliknij **Utwórz zasób** > **Internetu rzeczy** > **zadanie usługi Stream Analytics**.

2. Nazwa zadania `socialtwitter-sa-job` i określ subskrypcję, lokalizacji i grupy zasobów.

    Należy dobrze jest umieścić zadania i Centrum zdarzeń, w tym samym regionie, w celu uzyskania najlepszej wydajności i tak, aby nie płaci się na przesyłanie danych między regionami.

    ![Tworzenie nowego zadania usługi analiza strumienia](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Kliknij przycisk **Utwórz**.

    Zadanie jest tworzony i portalu Wyświetla szczegóły zadania.


## <a name="specify-the-job-input"></a>Określ dane wejściowe zadania

1. W przypadku zadania Stream Analytics w obszarze **topologii zadania** środku bloku zadania, kliknij przycisk **dane wejściowe**. 

2. W **dane wejściowe** bloku, kliknij przycisk  **+ &nbsp;Dodaj** , a następnie wypełnij bloku z tymi wartościami:

    * **Alias wejściowy**: Użyj nazwy `TwitterStream`. Użyj innej nazwy, aby je zapisać, ponieważ będzie potrzebny później.
    * **Typ źródła**: Wybierz **strumienia danych**.
    * **Źródło**: Wybierz **Centrum zdarzeń**.
    * **Opcji importowania**: Wybierz **Użyj Centrum zdarzeń z bieżącej subskrypcji**. 
    * **Przestrzeń nazw magistrali usług**: Wybierz obszar nazw Centrum zdarzeń utworzonego wcześniej (`<yourname>-socialtwitter-eh-ns`).
    * **Centrum zdarzeń**: wybierz utworzony wcześniej Centrum zdarzeń (`socialtwitter-eh`).
    * **Nazwa zasad Centrum zdarzeń**: Wybierz zasady dostępu, który został utworzony wcześniej (`socialtwitter-access`).

    ![Utwórz nowe dane wejściowe zadania przesyłania strumieniowego usługi analiza](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Kliknij przycisk **Utwórz**.


## <a name="specify-the-job-query"></a>Określ zapytanie dotyczące zadań

Analiza strumienia obsługuje prosty, deklaratywny model zapytań opisujący przekształcenia. Aby dowiedzieć się więcej na temat języka, zobacz [Azure Stream Analytics zapytania Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx).  Ten samouczek ułatwia tworzenie i testowanie kilka zapytań za pośrednictwem danych Twitter.

Aby porównać liczbę uwagi między tematami, można użyć [okno wirowania](https://msdn.microsoft.com/library/azure/dn835055.aspx) można pobrać liczby uwagi w temacie co pięć sekund.

1. Zamknij **dane wejściowe** bloku, jeśli nie jest jeszcze.

2. W bloku zadania kliknij **zapytania** pole. Azure wymieniono wejściach i wyjściach, które są skonfigurowane dla zadania i umożliwia utworzenie zapytania umożliwiające przekształcanie strumień wejściowy podczas przesyłania danych wyjściowych.

3. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

3. W **zapytania** bloku, kliknij punkty `TwitterStream` danych wejściowych, a następnie wybierz **przykładowe dane z danych wejściowych**.

    ![Opcje menu do użycia dla wpisu zadanie analizy przesyłania strumieniowego, z "Przykładowych danych z danych wejściowych" wybrane przykładowe dane](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Spowoduje to otwarcie bloku, który pozwala określić, ile przykładowych danych, aby uzyskać zdefiniowanych pod względem czasu odczytu strumienia wejściowego.

4. Ustaw **minut** 3, a następnie kliknij przycisk **OK**. 
    
    ![Próbkowanie strumienia wejściowego z "3 minuty" wybrane opcje.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure przykłady 3 minut, przez które danych ze strumienia wejściowego i powiadamia użytkownika, gdy będzie gotowy przykładowych danych. (Trwa to krótki czas). 

    Przykładowe dane są tymczasowo przechowywane i jest dostępny, gdy masz okna kwerendy. Jeśli zamkniesz okno kwerendy przykładowe dane są usuwane i należy utworzyć nowy zestaw przykładowych danych. 

5. Zmień zapytanie w edytorze kodu do następującego:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Jeśli nie użyto `TwitterStream` jako alias dla danych wejściowych, Zastąp aliasu dla `TwitterStream` w zapytaniu.  

    To zapytanie używa **TIMESTAMP BY** — słowo kluczowe w celu określenia pola sygnatury czasowej w ładunku ma być używana podczas obliczania danych czasowych. Jeśli to pole nie jest określony, operacja okien jest wykonywana na podstawie czasu, który każdego zdarzenia dotarła do Centrum zdarzeń. Dowiedz się więcej w części "Godzina nadejścia vs czas aplikacji" [Stream Analytics kwerendy odwołania](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    To zapytanie również uzyskuje dostęp do sygnatury czasowej do końca każdej okna przy użyciu **System.Timestamp** właściwości.

5. Kliknij przycisk **testu**. Zapytanie jest uruchamiana dla danych, który pobrano.
    
6. Kliknij pozycję **Zapisz**. Spowoduje to zapisanie zapytania jako część zadania przesyłania strumieniowego Analytics. (Nie zostanie on zapisany przykładowe dane).


## <a name="experiment-using-different-fields-from-the-stream"></a>Przy użyciu różnych pól ze strumienia eksperymentu 

W poniższej tabeli wymieniono pola, które są częścią JSON strumienia danych. Możesz swobodnie eksperymentować w edytorze zapytań.

|Właściwość JSON | Definicja|
|--- | ---|
|CreatedAt | Czas utworzenia tweet|
|Temat | Temat, który odpowiada określonym — słowo kluczowe|
|SentimentScore | Wskaźniki nastrojów klientów wyniku Sentiment140|
|Autor | Dojście Twitter wysyłane tweet|
|Tekst | Pełna treść tweet|


## <a name="create-an-output-sink"></a>Utwórz ujście danych wyjściowych

Teraz zdefiniowano strumienia zdarzeń Centrum zdarzeń wejście do pozyskiwania zdarzenia i zapytanie w celu wykonania transformację przez strumień. Ostatnim krokiem jest określenie ujście danych wyjściowych zadania.  

W tym samouczku możesz zapisać zdarzeń zagregowane tweet zapytania zadania do magazynu obiektów Blob platformy Azure.  Można również wypychanie wyniki do bazy danych SQL Azure, Magazyn tabel Azure Event Hubs lub usługa Power BI, w zależności od aplikacji wymaga.

## <a name="specify-the-job-output"></a>Określ dane wyjściowe zadania

1. W **topologii zadania** kliknij **dane wyjściowe** pole. 

2. W **dane wyjściowe** bloku, kliknij przycisk  **+ &nbsp;Dodaj** , a następnie wypełnij bloku z tymi wartościami:

    * **Alias wyjściowy**: Użyj nazwy `TwitterStream-Output`. 
    * **Obiekt sink**: Wybierz **magazynu obiektów Blob**.
    * **Opcje importowania**: Wybierz **Użyj magazynu obiektów blob z bieżącej subskrypcji**.
    * **Konto magazynu**. Wybierz **Utwórz nowe konto magazynu.**
    * **Konto magazynu** (drugie pole). Wprowadź `YOURNAMEsa`, gdzie `YOURNAME` jest nazwa użytkownika lub inny unikatowy ciąg. Nazwę można użyć tylko małe litery i cyfry, i między Azure musi być unikatowa. 
    * **Kontener**. Wprowadź `socialtwitter`.
    Nazwa konta magazynu i nazwę kontenera są używane razem zapewnienie identyfikator URI magazynu obiektów blob, jak to: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    ![Blok "Nowe dane wyjściowe" zadania usługi analiza strumienia](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Kliknij przycisk **Utwórz**. 

    Azure tworzy konto magazynu i automatycznie generuje klucz. 

5. Zamknij **dane wyjściowe** bloku. 


## <a name="start-the-job"></a>Uruchom zadanie

Dane wejściowe zadania, zapytań i dane wyjściowe zostały określone. Wszystko jest gotowe do rozpoczęcia zadania usługi analiza strumienia.

1. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

2. W bloku zadania kliknij **Start**.

    ![Uruchom zadanie usługi analiza strumienia](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. W **rozpoczęcia zadania** bloku dla **dane wyjściowe zadania godzina rozpoczęcia**, wybierz pozycję **teraz** , a następnie kliknij przycisk **Start**. 

    ![Blok "Uruchomić zadanie" zadania usługi analiza strumienia](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure umożliwia powiadomienie, gdy zadanie została uruchomiona, a następnie w bloku zadania stan jest wyświetlany jako **systemem**.

    ![Uruchomione zadanie](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Widok danych wyjściowych analizy wskaźniki nastrojów klientów

Po uruchomieniu zadania przetwarza strumienia w czasie rzeczywistym usługi Twitter, można wyświetlić dane wyjściowe do analizy wskaźniki nastrojów klientów.

Można użyć narzędzia, takiego jak [Eksploratora usługi Storage Azure](https://http://storageexplorer.com/) lub [Eksploratora Azure](http://www.cerebrata.com/products/azure-explorer/introduction) Aby wyświetlić dane wyjściowe zadania w czasie rzeczywistym. W tym miejscu, można użyć [usługi Power BI](https://powerbi.com/) rozszerzenie aplikacji zawierającej dostosowany pulpit nawigacyjny tak jak pokazano na poniższym zrzucie ekranu:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Utwórz innego zapytania w celu zidentyfikowania trendów — tematy

Inne zapytanie, można użyć do zrozumienia Twitter wskaźniki nastrojów klientów jest oparta na [przedłużanie okna](https://msdn.microsoft.com/library/azure/dn835051.aspx). Aby zidentyfikować tematy trendzie, możesz wyszukiwać tematy przekraczających wartość progową uwagi w określonym czasie.

Na potrzeby tego samouczka możesz sprawdzić tematy, które są wymienione więcej niż 20 razy w ciągu ostatnich 5 sekund.

1. W bloku zadania kliknij **zatrzymać** zatrzymania zadania. 

2. W **topologii zadania** kliknij **zapytania** pole. 

3. Zmień zapytanie do następującego:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Kliknij pozycję **Zapisz**.

5. Upewnij się, że aplikacja TwitterWpfClient jest uruchomiona. 

6. Kliknij przycisk **Start** do ponownego uruchamiania zadania przy użyciu nowego zapytania.


## <a name="get-support"></a>Uzyskiwanie pomocy technicznej
Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
