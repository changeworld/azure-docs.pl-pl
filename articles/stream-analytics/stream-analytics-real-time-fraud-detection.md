---
title: Wykrywanie oszustw w czasie rzeczywistym za pomocą usługi Azure Stream Analytics
description: Dowiedz się, jak utworzyć rozwiązanie do wykrywania oszustw w czasie rzeczywistym za pomocą usługi Stream Analytics. Użyj Centrum zdarzeń do przetwarzania zdarzeń w czasie rzeczywistym.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: c0b2943e1f0d7f2386ec09da03d297a570eede7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80276482"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Wprowadzenie do korzystania z usługi Azure Stream Analytics: wykrywanie oszustw w czasie rzeczywistym

Ten samouczek zawiera kompleksową ilustrację, jak korzystać z usługi Azure Stream Analytics. Omawiane kwestie: 

* Przenieś zdarzenia przesyłania strumieniowego do wystąpienia usługi Azure Event Hubs. W tym samouczku użyjesz aplikacji, która symuluje strumień rekordów metadanych telefonu komórkowego.

* Pisanie zapytań usługi Stream Analytics podobnych do języka SQL w celu przekształcenia danych, agregowania informacji lub szukania wzorców. Zobaczysz, jak użyć kwerendy do zbadania strumienia przychodzącego i wyszukują wywołania, które mogą być fałszywe.

* Wyślij wyniki do ujścia danych wyjściowych (magazynu), który można analizować w celu uzyskania dodatkowych szczegółowych informacji. W takim przypadku wyślesz podejrzane dane wywołania do magazynu obiektów Blob platformy Azure.

W tym samouczku użyto przykład wykrywania oszustw w czasie rzeczywistym na podstawie danych połączeń telefonicznych. Przedstawiona technika jest również odpowiednia do innych rodzajów wykrywania oszustw, takich jak oszustwa związane z kartami kredytowymi lub kradzież tożsamości. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenariusz: Wykrywanie oszustw telekomunikacyjnych i SIM w czasie rzeczywistym

Firma telekomunikacyjna ma dużą ilość danych dla połączeń przychodzących. Firma chce wykryć fałszywe połączenia w czasie rzeczywistym, aby mogli powiadomić klientów lub zamknąć usługę dla określonego numeru. Jeden rodzaj oszustwa na kartę SIM obejmuje wiele połączeń z tej samej tożsamości w tym samym czasie, ale w geograficznie różnych lokalizacjach. Aby wykryć tego typu oszustwa, firma musi zbadać przychodzące rekordy telefonów i poszukać konkretnych wzorców — w tym przypadku dla połączeń wykonanych w tym samym czasie w różnych krajach/regionach. Wszelkie rekordy telefonu, które należą do tej kategorii są zapisywane do magazynu do późniejszej analizy.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku symulujesz dane połączeń telefonicznych przy użyciu aplikacji klienckiej, która generuje przykładowe metadane połączeń telefonicznych. Niektóre rekordy, które tworzy aplikacja wyglądają jak fałszywe połączenia. 

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Konto platformy Azure.
* Aplikacja generatora zdarzeń wywoławcowych, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), którą można pobrać z Centrum pobierania Microsoft. Rozpaj ten pakiet do folderu na komputerze. Jeśli chcesz zobaczyć kod źródłowy i uruchomić aplikację w debugerze, możesz uzyskać kod źródłowy aplikacji z [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >System Windows może zablokować pobrany plik zip. Jeśli nie możesz go rozpakować, kliknij plik prawym przyciskiem myszy i wybierz polecenie **Właściwości**. Jeśli zostanie wyświetlony komunikat "Ten plik pochodzi z innego komputera i może być zablokowany w celu ochrony tego komputera", wybierz opcję **Odblokuj,** a następnie kliknij przycisk **Zastosuj**.

Jeśli chcesz sprawdzić wyniki zadania analizy przesyłania strumieniowego, potrzebujesz również narzędzia do wyświetlania zawartości kontenera usługi Azure Blob Storage. Jeśli używasz programu Visual Studio, można użyć [narzędzia azure dla programu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) lub Visual Studio Cloud [Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Alternatywnie można zainstalować autonomiczne narzędzia, takie jak [Azure Storage Explorer](https://storageexplorer.com/) lub [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Tworzenie centrów zdarzeń platformy Azure do pozyskiwania zdarzeń

Aby przeanalizować strumień danych, należy go *posuwać* na platformę Azure. Typowym sposobem pozyskiwania danych jest użycie [usługi Azure Event Hubs,](../event-hubs/event-hubs-what-is-event-hubs.md)która umożliwia pozyskiwania milionów zdarzeń na sekundę, a następnie przetwarzanie i przechowywanie informacji o zdarzeniu. W tym samouczku utworzysz centrum zdarzeń, a następnie masz aplikację generatora zdarzeń wywołania wysyłać dane wywołania do tego centrum zdarzeń. Aby uzyskać więcej informacji o centrach zdarzeń, zobacz [dokumentację usługi Azure Service Bus.](https://docs.microsoft.com/azure/service-bus/)

>[!NOTE]
>Aby uzyskać bardziej szczegółową wersję tej procedury, zobacz [Tworzenie obszaru nazw centrum zdarzeń i centrum zdarzeń przy użyciu portalu Azure.](../event-hubs/event-hubs-create.md) 

### <a name="create-a-namespace-and-event-hub"></a>Tworzenie obszaru nazw i centrum zdarzeń
W tej procedurze najpierw należy utworzyć obszar nazw centrum zdarzeń, a następnie dodać centrum zdarzeń do tego obszaru nazw. Przestrzenie nazw centrum zdarzeń są używane do logicznej grupowanie wystąpień magistrali zdarzeń powiązanych. 

1. Zaloguj się do witryny Azure portal i kliknij pozycję **Utwórz zasób** w lewym górnym rogu ekranu.

2. Wybierz pozycję **Wszystkie usługi** w menu po lewej stronie, a następnie wybierz **gwiazdkę (`*`)** obok pozycji **Event Hubs** w kategorii **Analiza**. Upewnij się, że usługa **Event Hubs** została dodana do kategorii **ULUBIONE** w menu nawigacji po lewej stronie. 

   ![Wyszukiwanie usługi Event Hubs](./media/stream-analytics-real-time-fraud-detection/select-event-hubs-menu.png)

3. Wybierz pozycję **Event Hubs** w obszarze **ULUBIONE** w menu nawigacji po lewej stronie, a następnie wybierz pozycję **Dodaj** na pasku narzędzi.

   ![Przycisk dodawania](./media/stream-analytics-real-time-fraud-detection/event-hubs-add-toolbar.png)

4. W okienku **Tworzenie obszaru nazw** wprowadź nazwę `<yourname>-eh-ns-demo`obszaru nazw, taką jak . Można użyć dowolnej nazwy dla obszaru nazw, ale nazwa musi być prawidłowa dla adresu URL i musi być unikatowa na platformie Azure. 
    
5. Wybierz subskrypcję i utwórz lub wybierz grupę zasobów, a następnie kliknij przycisk **Utwórz**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

6. Po zakończeniu wdrażania obszaru nazw znajdź obszar nazw centrum zdarzeń na liście zasobów platformy Azure. 

7. Kliknij nowy obszar nazw, a następnie w okienku obszaru nazw kliknij pozycję **Centrum zdarzeń**.

   ![Przycisk Dodaj centrum zdarzeń służący do tworzenia nowego centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
8. Nazwij nowe `asa-eh-frauddetection-demo`centrum zdarzeń . Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj to, ponieważ potrzebujesz nazwy później. Nie musisz teraz ustawiać żadnych innych opcji dla centrum zdarzeń.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
9. Kliknij przycisk **Utwórz**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udzielanie dostępu do centrum zdarzeń i pobieranie parametrów połączenia

Aby proces mógł wysyłać dane do centrum zdarzeń, centrum zdarzeń musi mieć zasady, które umożliwiają odpowiedni dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1. W okienku obszaru nazw zdarzeń kliknij pozycję **Centra zdarzeń,** a następnie kliknij nazwę nowego centrum zdarzeń.

2. W okienku centrum zdarzeń kliknij pozycję Zasady ** + &nbsp;** dostępu **udostępnionego,** a następnie kliknij pozycję Dodaj .

    > [!NOTE]
    > Upewnij się, że pracujesz z centrum zdarzeń, a nie z obszarem nazw centrum zdarzeń.

3. Dodaj zasadę `asa-policy-manage-demo` o nazwie i **oświadczenie**wybierz pozycję **Zarządzaj**.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4. Kliknij przycisk **Utwórz**.

5. Po wdrożeniu zasad kliknij ją na liście zasad dostępu współdzielonego.

6. Znajdź pole oznaczone **jako CONNECTION STRING-PRIMARY KEY** i kliknij przycisk kopiuj obok ciągu połączenia. 

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7. Wklej parametry połączenia do edytora tekstu. Ten ciąg połączenia jest potrzebny do następnej sekcji, po dokonaniu kilku drobnych zmian.

    Parametry połączenia wyglądają następująco:

    `Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo`

    Należy zauważyć, że ciąg połączenia zawiera wiele par klucz-wartość, `SharedAccessKey`oddzielonych `EntityPath`średnikami: `Endpoint`, `SharedAccessKeyName`, , i .  


## <a name="configure-and-start-the-event-generator-application"></a>Konfigurowanie i uruchamianie aplikacji generatora zdarzeń

Przed uruchomieniem aplikacji TelcoGenerator należy ją skonfigurować w taki sposób, aby wysyłała rekordy wywołań do utworzonego centrum zdarzeń.

### <a name="configure-the-telcogenerator-app"></a>Konfigurowanie aplikacji TelcoGenerator

1. W edytorze, w którym skopiowano parametry połączenia, `EntityPath` zanotuj `EntityPath` wartość, a następnie usuń parę (nie zapomnij usunąć średnika, który go poprzedza). 

2. W folderze, w którym rozpakowałeś plik TelcoGenerator.zip, otwórz plik telcodatagen.exe.config w edytorze. (Istnieje więcej niż jeden plik .config, więc upewnij się, że otworzysz odpowiedni plik).

3. W `<appSettings>` elemencie:

   * Ustaw wartość klucza `EventHubName` na nazwę centrum zdarzeń (czyli wartość ścieżki jednostki).
   * Ustaw wartość klucza `Microsoft.ServiceBus.ConnectionString` na ciąg połączenia. 

   Sekcja `<appSettings>` będzie wyglądać następująco:

    ```xml
    <appSettings>
     <!-- Service Bus specific app setings for messaging connections -->
     <add key="EventHubName" value="asa-eh-ns-demo"/>
     <add key="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://asa-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=GEcnTKf2//1MRn6SN1A2u0O76MP9pj3v0Ccyf1su4Zo="/>
   </appSettings>
    ```
 
4. Zapisz plik. 

### <a name="start-the-app"></a>Uruchamianie aplikacji

1. Otwórz okno polecenia i przejdź do folderu, w którym rozpakowana jest aplikacja TelcoGenerator.

2. Wprowadź następujące polecenie:

    ```console
    telcodatagen.exe 1000 0.2 2
    ```

   Parametry są następujące: 

   * Liczba cdr na godzinę. 
   * Prawdopodobieństwo oszustwa kart SIM: jak często, jako procent wszystkich połączeń, że aplikacja powinna symulować fałszywe połączenie. Wartość 0,2 oznacza, że około 20% rekordów połączeń będzie wyglądać na fałszywe.
   * Czas trwania w godzinach. Liczba godzin, przez które aplikacja powinna być uruchamiana. Aplikację można również zatrzymać w dowolnym momencie, naciskając klawisze Ctrl+C w wierszu polecenia.

   Po kilku sekundach aplikacja rozpocznie wyświetlanie rekordów połączeń telefonicznych na ekranie w miarę wysyłania ich do centrum zdarzeń.

Niektóre z kluczowych pól, które będą używane w tej aplikacji wykrywania oszustw w czasie rzeczywistym są następujące:

|**Rekord**|**Definicja**|
|----------|--------------|
|`CallrecTime`|Znacznik czasu godziny rozpoczęcia połączenia. |
|`SwitchNum`|Centrala telefoniczna używana do wykonania połączenia. W tym przykładzie przełączniki są ciągami, które reprezentują kraj/region pochodzenia (USA, Chiny, Wielka Brytania, Niemcy lub Australia). |
|`CallingNum`|Numer telefonu dzwoniącego. |
|`CallingIMSI`|Numer IMSI (International Mobile Subscriber Identity). Jest to unikatowy identyfikator wywołującego. |
|`CalledNum`|Numer telefonu odbiorcy połączenia. |
|`CalledIMSI`|Numer IMSI (International Mobile Subscriber Identity). Jest to unikatowy identyfikator odbiorcy wywołania. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Tworzenie zadania usługi Stream Analytics w celu zarządzania danymi przesyłania strumieniowego

Teraz, gdy masz strumień zdarzeń wywołania, możesz skonfigurować zadanie usługi Stream Analytics. Zadanie odczyta dane z centrum zdarzeń, które można skonfigurować. 

### <a name="create-the-job"></a>Tworzenie zadania 

1. W witrynie Azure portal kliknij pozycję **Utwórz zadanie** > usługi Internet**of Things** > **w ramach usługi Stream Analytics**.

2. Nazwij `asa_frauddetection_job_demo`zadanie , określ subskrypcję, grupę zasobów i lokalizację.

    Warto umieścić zadanie i centrum zdarzeń w tym samym regionie, aby uzyskać najlepszą wydajność i nie płacić za przesyłanie danych między regionami.

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Kliknij przycisk **Utwórz**.

    Zadanie zostanie utworzone, a w portalu zostaną wyświetlone szczegóły zadania. Nic jeszcze nie działa — musisz skonfigurować zadanie, zanim będzie można je uruchomić.

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. Na pulpicie nawigacyjnym lub w okienku **Wszystkie zasoby** znajdź i wybierz zadanie `asa_frauddetection_job_demo` Usługi Stream Analytics. 
2. W sekcji **Przegląd** w okienku zadań usługi Stream Analytics kliknij pole **Dane wejściowe.**

   ![Pole Wprowadzanie w obszarze Topologia w okienku zadań Usługi Usługi Przesyłania strumieniowego Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kliknij **pozycję Dodaj dane wejściowe strumienia** i wybierz pozycję Centrum **zdarzeń**. Następnie wypełnij stronę Nowe dane wejściowe następującymi informacjami:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych  |  CallStream   |  Wprowadź nazwę, aby zidentyfikować dane wejściowe zadania.   |
   |Subskrypcja   |  \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, która ma utworzone centrum zdarzeń.   |
   |Przestrzeń nazw centrum zdarzeń  |  asa-eh-ns-demo |  Wprowadź nazwę obszaru nazw Centrum zdarzeń.   |
   |Nazwa centrum zdarzeń  | asa-eh-oszustwadetection-demo | Wybierz nazwę Centrum zdarzeń.   |
   |Nazwa zasad centrum zdarzeń  | asa-policy-manage-demo | Wybierz wcześniej utworzone zasady dostępu.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Kliknij przycisk **Utwórz**.

## <a name="create-queries-to-transform-real-time-data"></a>Tworzenie zapytań w celu przekształcania danych w czasie rzeczywistym

W tym momencie masz zadanie usługi Stream Analytics skonfigurowane do odczytu przychodzącego strumienia danych. Następnym krokiem jest utworzenie kwerendy, która analizuje dane w czasie rzeczywistym. Usługa Stream Analytics obsługuje prosty, deklaratywny model zapytań, który opisuje przekształcenia do przetwarzania w czasie rzeczywistym. Kwerendy używają języka podobnego do JĘZYKA SQL, który ma pewne rozszerzenia specyficzne dla usługi Stream Analytics. 

Proste zapytanie może po prostu odczytać wszystkie dane przychodzące. Jednak często tworzysz kwerendy, które szukają określonych danych lub relacji w danych. W tej sekcji samouczka można utworzyć i przetestować kilka zapytań, aby dowiedzieć się kilka sposobów, w którym można przekształcić strumień wejściowy do analizy. 

Kwerendy, które tworzysz tutaj, będą po prostu wyświetlać przekształcone dane na ekranie. W dalszej sekcji skonfigurujesz ujście danych wyjściowych i kwerendę, która zapisuje przekształcone dane do tego ujścia.

Aby dowiedzieć się więcej o języku, zobacz [odwołanie do języka kwerendy usługi Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="get-sample-data-for-testing-queries"></a>Pobieranie przykładowych danych do testowania zapytań

Aplikacja TelcoGenerator wysyła rekordy połączeń do centrum zdarzeń, a zadanie usługi Stream Analytics jest skonfigurowane do odczytu z centrum zdarzeń. Kwerendy można użyć, aby przetestować zadanie, aby upewnić się, że jest odczytywanie poprawnie. Aby przetestować kwerendę w konsoli platformy Azure, potrzebujesz przykładowych danych. W tym instruktażu wyodrębnisz przykładowe dane ze strumienia, który przechodzi do Centrum zdarzeń.

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona i tworzy rekordy połączeń.
2. W portalu wróć do okienka zadań Usługi Analizy przesyłania strumieniowego. (Jeśli okienko zostało zamknięte, `asa_frauddetection_job_demo` wyszukaj w okienku **Wszystkie zasoby).**
3. Kliknij pole **Kwerenda.** Platforma Azure wyświetla listę danych wejściowych i wyjściowych, które są skonfigurowane dla zadania i umożliwia utworzenie kwerendy, która umożliwia przekształcenie strumienia wejściowego, gdy jest wysyłany do danych wyjściowych.
4. W okienku **Kwerenda** kliknij kropki `CallStream` obok danych wejściowych, a następnie wybierz **pozycję Przykładowe dane z danych wejściowych**.

   ![Opcje menu do używania przykładowych danych dla wpisu zadania Usługi Streaming Analytics z wybraną opcją "Przykładowe dane z danych wejściowych"](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Ustaw **minuty** na 3, a następnie kliknij przycisk **OK**. 
    
   ![Opcje pobierania próbek strumienia wejściowego z wybraną 3 minutami](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Platforma Azure pobiera próbki danych o wartości 3 minut ze strumienia wejściowego i powiadamia, gdy przykładowe dane są gotowe. (Zajmuje to chwilę.) 

Przykładowe dane są przechowywane tymczasowo i są dostępne, kiedy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, przykładowe dane zostaną usunięte i konieczne będzie utworzenie nowego zestawu przykładowych danych. 

Alternatywnie można pobrać plik .json, który zawiera przykładowe dane [z gitHub,](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json)a następnie przekazać ten `CallStream` plik .json do użycia jako przykładowe dane dla danych wejściowych. 

### <a name="test-using-a-pass-through-query"></a>Testowanie przy użyciu kwerendy przekazywalnego

Jeśli chcesz zarchiwizować każde zdarzenie, możesz użyć kwerendy przekazu, aby odczytać wszystkie pola w ładunku zdarzenia.

1. W oknie kwerendy wprowadź tę kwerendę:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Podobnie jak w przypadku języka SQL, słowa kluczowe nie są rozróżniane wielkość liter, a odstępy nie są istotne.

    W tej `CallStream` kwerendzie jest alias, który został określony podczas tworzenia danych wejściowych. Jeśli użyto innego aliasu, użyj tej nazwy.

2. Kliknij **przycisk Testuj**.

    Zadanie usługi Stream Analytics uruchamia kwerendę względem przykładowych danych i wyświetla dane wyjściowe w dolnej części okna. Wyniki wskazują, że usługa Event Hub i zadanie analizy przesyłania strumieniowego są poprawnie skonfigurowane. (Jak wspomniano, później utworzysz ujście danych wyjściowych, do którego kwerenda może zapisywać dane).

   ![Dane wyjściowe zadania usługi Stream Analytics z 73 wygenerowanymi rekordami](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Dokładna liczba rekordów będzie zależeć od liczby rekordów przechwyconych w próbce 3-minutowej.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Zmniejszanie liczby pól przy użyciu rzutowania kolumn

W wielu przypadkach analiza nie wymaga wszystkich kolumn ze strumienia wejściowego. Kwerendy można użyć do projektu mniejszy zestaw zwróconych pól niż w kwerendzie przekazu.

1. Zmień kwerendę w edytorze kodu na następującą:

    ```SQL
    SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
    FROM 
        CallStream
    ```

2. Kliknij **ponownie pozycję Testuj.** 

   ![Dane wyjściowe zadania usługi Stream Analytics dla projekcji pokazują 25 rekordów](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Zliczanie połączeń przychodzących według regionu: okno tumbling z agregacją

Załóżmy, że chcesz policzyć liczbę połączeń przychodzących na region. W przesyłaniu strumieniowego danych, gdy chcesz wykonywać funkcje agregacji, takie jak zliczanie, należy segmentować strumień do jednostek czasowych (ponieważ sam strumień danych jest skutecznie nieskończony). Można to zrobić za pomocą [funkcji okna](stream-analytics-window-functions.md)Usługi Analizy strumieniowej . Następnie można pracować z danymi wewnątrz tego okna jako jednostki.

W przypadku tej transformacji chcesz sekwencji okien czasowych, które nie nakładają się na siebie — każde okno będzie miało oddzielny zestaw danych, które można grupować i agregować. Ten typ okna jest określany jako *okno tumbling*. W oknie Tumbling można uzyskać liczbę połączeń przychodzących `SwitchNum`pogrupowanych według , która reprezentuje kraj/region, z którego pochodzi wywołanie. 

1. Zmień kwerendę w edytorze kodu na następującą:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Ta kwerenda `Timestamp By` używa słowa `FROM` kluczowego w klauzuli, aby określić, które pole sygnatury czasowej w strumieniu wejściowym do użycia do definiowania okna tumbling. W takim przypadku okno dzieli dane na segmenty według `CallRecTime` pola w każdym rekordzie. (Jeśli żadne pole nie zostanie określone, operacja okna używa czasu, przez który każde zdarzenie dociera do Centrum zdarzeń. Zobacz "Czas przybycia a czas aplikacji" w [odwołaniu do języka zapytań usługi Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). 

    Projekcja `System.Timestamp`zawiera , który zwraca sygnaturę czasową dla końca każdego okna. 

    Aby określić, że chcesz użyć okna tumbling, należy użyć funkcji `GROUP BY` [TUMBLINGWINDOW](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) w klauzuli. W funkcji należy określić jednostkę czasu (w dowolnym miejscu od mikrosekundy do dnia) i rozmiar okna (ile jednostek). W tym przykładzie okno Tumbling składa się z 5-sekundowych interwałów, dzięki czemu otrzymasz liczbę według kraju/regionu dla co 5 sekund wartości wywołań.

2. Kliknij **ponownie pozycję Testuj.** W wynikach należy zauważyć, że sygnatury czasowe w obszarze **WindowEnd** są w przyrostach 5-sekundowych.

   ![Dane wyjściowe zadania usługi Stream Analytics dla agregacji z 13 rekordami](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Wykrywanie oszustw związanych z kartą SIM za pomocą samoskładnia

W tym przykładzie należy wziąć pod uwagę oszukańcze użycie jako wywołania, które pochodzą od tego samego użytkownika, ale w różnych lokalizacjach w ciągu 5 sekund od siebie. Na przykład ten sam użytkownik nie może rzeczywiście wykonywać w tym samym czasie połączeń ze Stanów Zjednoczonych i Australii. 

Aby sprawdzić, czy te przypadki, można użyć samoskładnia danych przesyłania `CallRecTime` strumieniowego do przyłączenia strumienia do siebie na podstawie wartości. Następnie można wyszukać rekordy `CallingIMSI` wywołań, w których wartość (numer źródłowy) jest taka sama, ale `SwitchNum` wartość (kraj/region pochodzenia) nie jest taka sama.

Korzystając ze sprzężenia z danymi przesyłania strumieniowego, sprzężenie musi podać pewne ograniczenia dotyczące tego, jak daleko pasujące wiersze mogą być oddzielone w czasie. (Jak wspomniano wcześniej, dane przesyłania strumieniowego jest skutecznie nieskończone.) Granice czasu dla relacji są określone `ON` wewnątrz klauzuli sprzężenia, przy użyciu `DATEDIFF` funkcji. W takim przypadku sprzężenie opiera się na 5-sekundowy interwał danych wywołania.

1. Zmień kwerendę w edytorze kodu na następującą: 

    ```SQL
    SELECT  System.Timestamp as Time, 
        CS1.CallingIMSI, 
        CS1.CallingNum as CallingNum1, 
        CS2.CallingNum as CallingNum2, 
        CS1.SwitchNum as Switch1, 
        CS2.SwitchNum as Switch2 
    FROM CallStream CS1 TIMESTAMP BY CallRecTime 
        JOIN CallStream CS2 TIMESTAMP BY CallRecTime 
        ON CS1.CallingIMSI = CS2.CallingIMSI 
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5 
    WHERE CS1.SwitchNum != CS2.SwitchNum
    ```

    Ta kwerenda jest jak każde `DATEDIFF` sprzężenie SQL z wyjątkiem funkcji w sprzężeniu. Ta wersja `DATEDIFF` jest specyficzna dla usługi `ON...BETWEEN` Streaming Analytics i musi być wyświetlana w klauzuli. Parametry są jednostką czasu (sekundy w tym przykładzie) i aliasami dwóch źródeł sprzężenia. Różni się to od `DATEDIFF` standardowej funkcji SQL.

    Klauzula `WHERE` zawiera warunek, który oznacza fałszywe wywołanie: przełączniki źródłowe nie są takie same. 

2. Kliknij **ponownie pozycję Testuj.** 

   ![Dane wyjściowe zadania usługi Stream Analytics dla sprzężenia własnego, z 6 wygenerowanymi rekordami](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kliknij **przycisk Zapisz,** aby zapisać zapytanie samoskładnia w ramach zadania Usługi Analizy przesyłania strumieniowego. (Nie zapisuje przykładowych danych).

    <br/><img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Tworzenie ujścia wyjściowego w celu przechowywania przekształconych danych

Zdefiniowano strumień zdarzeń, dane wejściowe centrum zdarzeń do pozyskiwania zdarzeń i kwerendę do przeprowadzenia transformacji za pomocą strumienia. Ostatnim krokiem jest zdefiniowanie ujścia wyjściowego dla zadania — czyli miejsca, do w które można zapisać przekształcony strumień. 

Wiele zasobów można używać jako pochłaniaczy danych wyjściowych — bazy danych programu SQL Server, magazynu tabel, magazynu usługi Data Lake, usługi Power BI, a nawet innego centrum zdarzeń. W tym samouczku napiszesz strumień do usługi Azure Blob Storage, który jest typowym wyborem do zbierania informacji o zdarzeniach do późniejszej analizy, ponieważ obsługuje dane nieustrukturyzowane.

Jeśli masz istniejące konto magazynu obiektów blob, można go użyć. W tym samouczku dowiesz się, jak utworzyć nowe konto magazynu.

### <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta usługi Azure Blob Storage

1. W lewym górnym rogu witryny Azure portal wybierz pozycję **Utwórz** > **konto magazynu****magazynu** > zasobów . Wypełnij stronę zadania konta magazynu o **nazwie** ustawionej na "asaehstorage", **Location** set to "East US", **Resource group** set to "asa-eh-ns-rg" (hostowanie konta magazynu w tej samej grupie zasobów co zadanie przesyłania strumieniowego w celu zwiększenia wydajności). W przypadku pozostałych ustawień można pozostawić ich wartości domyślne.  

   ![Tworzenie konta magazynu w witrynie Azure portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. W witrynie Azure portal wróć do okienka zadań usługi Analizy przesyłania strumieniowego. (Jeśli okienko zostało zamknięte, `asa_frauddetection_job_demo` wyszukaj w okienku **Wszystkie zasoby).**

3. W sekcji **Topologia zadań** kliknij pole **Dane wyjściowe.**

4. W okienku **Dane wyjściowe** kliknij pozycję **Dodaj** i wybierz pozycję **Magazyn obiektów Blob**. Następnie wypełnij stronę Nowe dane wyjściowe następującymi informacjami:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wyjściowych  |  CallStream-Oszukańcze Połączenia   |  Wprowadź nazwę, aby zidentyfikować dane wyjściowe zadania.   |
   |Subskrypcja   |  \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, która ma utworzone konto magazynu. Konto magazynu może być w tej samej lub innej subskrypcji. W tym przykładzie przyjęto założenie, że konto magazynu zostało utworzone w tej samej subskrypcji. |
   |Konto magazynu  |  asaehstorage |  Wprowadź nazwę utworzonego konta magazynu. |
   |Kontener  | asa-fraudulentcalls-demo | Wybierz pozycję Utwórz nowy i wprowadź nazwę kontenera. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Kliknij przycisk **Zapisz**. 


## <a name="start-the-streaming-analytics-job"></a>Uruchom zadanie analizy przesyłania strumieniowego

Zadanie jest teraz skonfigurowane. Określono dane wejściowe (centrum zdarzeń), transformację (kwerendę wyszukującą fałszywe wywołania) i dane wyjściowe (magazyn obiektów blob). Teraz możesz rozpocząć zadanie. 

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona.

2. W okienku zadań kliknij przycisk **Start**. W okienku **Zadania uruchamiania** dla czasu rozpoczęcia wyjścia zadania wybierz pozycję **Teraz**. 

   ![Uruchamianie zadania usługi Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Badanie przekształconych danych

Masz teraz pełne zadanie analizy strumieniowej. Zadanie polega na badaniu strumienia metadanych połączeń telefonicznych, poszukiwaniu fałszywych połączeń telefonicznych w czasie rzeczywistym i pisaniu informacji o tych fałszywych połączeniach do magazynu. 

Aby ukończyć ten samouczek, warto przyjrzeć się danym przechwyconym przez zadanie analizy przesyłania strumieniowego. Dane są zapisywane w usłudze Azure Blog Storage w fragmentach (pliki). Można użyć dowolnego narzędzia, które odczytuje usługi Azure Blob Storage. Jak wspomniano w sekcji Wymagania wstępne, można użyć rozszerzeń platformy Azure w programie Visual Studio lub użyć narzędzia, takiego jak [Azure Storage Explorer](https://storageexplorer.com/) lub [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Podczas badania zawartości pliku w magazynie obiektów blob, widać coś takiego jak:

   ![Magazyn obiektów blob platformy Azure z danymi wyjściowymi usługi Streaming Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Istnieją dodatkowe artykuły, które są kontynuowane w scenariuszu wykrywania oszustw i używają zasobów utworzonych w tym samouczku. Jeśli chcesz kontynuować, zobacz sugestie w obszarze **Następne kroki**.

Jeśli jednak skończysz i nie potrzebujesz utworzonych zasobów, możesz je usunąć, aby nie ponosić niepotrzebnych opłat za korzystanie z platformy Azure. W takim przypadku zalecamy wykonać następujące czynności:

1. Zatrzymaj zadanie Analityka strumieniowego. W okienku **Zadania** kliknij pozycję **Zatrzymaj** u góry.
2. Zatrzymaj aplikację Generator Telco. W oknie polecenia, w którym uruchomiono aplikację, naciśnij klawisze Ctrl+C.
3. Jeśli utworzono nowe konto magazynu obiektów blob tylko dla tego samouczka, usuń go. 
4. Usuń zadanie Analizy przesyłania strumieniowego.
5. Usuń centrum zdarzeń.
6. Usuń obszar nazw centrum zdarzeń.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby uzyskać dalszą pomoc, wypróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Następne kroki

Możesz kontynuować ten samouczek z następującym artykułem:

* [Analiza strumienia i usługa Power BI: pulpit nawigacyjny analizy w czasie rzeczywistym do przesyłania strumieniowego danych.](stream-analytics-power-bi-dashboard.md) W tym artykule pokazano, jak wysłać dane wyjściowe usługi TelCo zadania usługi Stream Analytics do usługi Power BI w celu wizualizacji i analizy w czasie rzeczywistym.

Aby uzyskać więcej informacji na temat usługi Stream Analytics w ogóle, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
