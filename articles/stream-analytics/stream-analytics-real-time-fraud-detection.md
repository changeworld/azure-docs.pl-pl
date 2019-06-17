---
title: Wykrywanie oszustw w czasie rzeczywistym przy użyciu usługi Azure Stream Analytics
description: Dowiedz się, jak utworzyć rozwiązanie wykrywania oszustw w czasie rzeczywistym za pomocą usługi Stream Analytics. Użyj Centrum zdarzeń do przetwarzania zdarzeń w czasie rzeczywistym.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: cfb7dc8ef41c8829caebed6fff2d881093dbbe4d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076234"
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Rozpoczęcie korzystania z usługi Azure Stream Analytics: Wykrywanie oszustw w czasie rzeczywistym

Ten samouczek zawiera end-to-end ilustruje sposób używania usługi Azure Stream Analytics. Omawiane kwestie: 

* Przenieś do wystąpienia usługi Azure Event Hubs przesyłania strumieniowego wydarzeń. W tym samouczku użyjesz aplikacji, która symuluje strumienia metadanych telefon komórkowy rekordów.

* Pisanie zapytań przypominający SQL Stream Analytics do przekształcania danych, agregacji informacji lub będzie zaglądać wzorców. Zobaczysz jak używać zapytania do badania przychodzącego strumienia i wyszukując wywołania, które mogą być fałszywe.

* Wysłać wyniki do ujścia danych wyjściowych (magazyn), które możesz analizować, aby uzyskać dodatkowe informacje szczegółowe. W tym przypadku będzie wysłać dane wywołania podejrzanej w usłudze Azure Blob storage.

W tym samouczku użyto przykładu wykrywanie oszustw w czasie rzeczywistym na podstawie danych połączeń telefonicznych. Techniki przedstawione nadaje się również dla innych typów wykrywania oszustw, np. karta kredytowa oszustwa lub kradzieży tożsamości. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenariusz: Telekomunikacji i SIM wykrywanie oszustw w czasie rzeczywistym

Firma ma dużą ilość danych dla połączeń przychodzących. Firma chce wykrywanie fałszywych połączeń w czasie rzeczywistym, aby mogli powiadomienie dla klientów lub wyłączenie usługi dla określonej liczby. Jeden typ o oszustwie SIM obejmuje wiele wywołań z tej samej tożsamości, w tym samym czasie, ale w geograficznie różnych lokalizacjach. Aby wykryć tego rodzaju oszustwa, firma musi sprawdzić rekordach przychodzących telefonu i poszukaj pod kątem określonych wzorców — w tym przypadku do wywołania w tym samym czasie w różnych krajach/regionach. Wszystkie rekordy telefonu, które należą do tej kategorii są zapisywane do magazynu w celu dalszej analizy.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku będziesz symulować danych połączeń telefonicznych za pomocą aplikacji klienckiej, która generuje przykładowe połączenia telefonicznego metadanych. Niektóre rekordy, które aplikacja generuje wyglądać fałszywe wywołania. 

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Konto platformy Azure.
* Aplikację generatora zdarzeń wywołania, [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip), który można pobrać z Microsoft Download Center. Rozpakuj pakiet do folderu na komputerze. Jeśli chcesz zobaczyć źródło kodu i uruchomić aplikację w debugerze, możesz pobrać kod źródłowy aplikacji z [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >Windows może spowodować zablokowanie pliku zip pobranego. Jeśli nie można rozpakować go, kliknij prawym przyciskiem myszy plik, a następnie wybierz **właściwości**. Jeśli zostanie wyświetlony komunikat "ten plik pochodzi z innego komputera i może zostać zablokowany, aby pomóc chronić ten komputer", wybierz opcję **odblokowanie** opcji, a następnie kliknij przycisk **Zastosuj**.

Jeśli chcesz przejrzeć wyniki zadania usługi Stream Analytics, należy również narzędzie do przeglądania zawartości kontenera usługi Azure Blob Storage. Jeśli używasz programu Visual Studio, możesz użyć [Azure Tools dla programu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) lub [programu Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Alternatywnie, można zainstalować narzędzi autonomicznych, takich jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/) lub [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Tworzenie usługi Azure Event Hubs w celu pozyskania zdarzeń

Do analizowania strumienia danych, możesz *pozyskiwania* go na platformę Azure. Typowy sposób w celu pozyskiwania danych jest użycie [usługi Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), który umożliwia obsługę milionów zdarzeń na sekundę i następnie przetwarzania i przechowywania informacji o zdarzeniach. Na potrzeby tego samouczka utworzysz Centrum zdarzeń i wybrać aplikację generatora zdarzeń wywołania wysłania danych połączeń do tego Centrum zdarzeń. Aby uzyskać więcej informacji o usłudze event hubs, zobacz [dokumentacji usługi Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Bardziej szczegółowe wersja tej procedury, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Tworzenie przestrzeni nazw i Centrum zdarzeń
W tej procedurze należy najpierw utworzyć przestrzeń nazw Centrum zdarzeń, a następnie dodaj Centrum zdarzeń do tego obszaru nazw. Przestrzenie nazw Centrum zdarzeń są używane do logicznego pogrupowania wystąpień magistrali powiązanych zdarzeń. 

1. Zaloguj się do witryny Azure portal, a następnie kliknij przycisk **Utwórz zasób** > **Internet of Things** > **Centrum zdarzeń**. 

2. W **tworzenie przestrzeni nazw** okienku, wprowadź nazwę przestrzeni nazw, takich jak `<yourname>-eh-ns-demo`. Można użyć dowolnej nazwy dla przestrzeni nazw, ale nazwa musi mieć prawidłowy dla danego adresu URL i na platformie Azure musi być unikatowa. 
    
3. Wybierz subskrypcję i Utwórz lub wybierz grupę zasobów, a następnie kliknij przycisk **Utwórz**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png" alt="Create event hub namespace in Azure portal" width="300px"/>

4. Po zakończeniu wdrażania przestrzeni nazw, należy znaleźć przestrzeń nazw Centrum zdarzeń, na liście zasobów platformy Azure. 

5. Kliknij nową przestrzeń nazw, a następnie w okienku przestrzeni nazw kliknij **Centrum zdarzeń**.

   ![Przycisk Dodaj Centrum zdarzeń do tworzenia nowego Centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nazwa nowego Centrum zdarzeń `asa-eh-frauddetection-demo`. Możesz użyć innej nazwy. Jeśli to zrobisz, zanotuj je, ponieważ nazwa będą potrzebne później. Nie musisz teraz Ustaw inne opcje dla Centrum zdarzeń.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png" alt="Name event hub in Azure portal" width="400px"/>
    
 
7. Kliknij pozycję **Utwórz**.

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udzielanie dostępu do centrum zdarzeń i pobieranie parametrów połączenia

Zanim proces może wysyłać dane do Centrum zdarzeń, Centrum zdarzeń musi mieć zasady, które zezwalają na odpowiedni dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1.  W okienku przestrzeni nazw zdarzeń kliknij **usługi Event Hubs** a następnie kliknij nazwę nowego Centrum zdarzeń.

2.  W okienku Centrum zdarzeń, kliknij przycisk **zasady dostępu współdzielonego** a następnie kliknij przycisk  **+ &nbsp;Dodaj**.

    >[!NOTE]
    >Upewnij się, że pracujesz z Centrum zdarzeń, a nie nazw Centrum zdarzeń.

3.  Dodawanie zasad o nazwie `sa-policy-manage-demo` i **oświadczenia**, wybierz opcję **Zarządzaj**.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png" alt="Create shared access policy for Stream Analytics" width="300px"/>
 
4.  Kliknij pozycję **Utwórz**.

5.  Po wdrożeniu zasad kliknij je na liście zasad dostępu współdzielonego.

6.  Znajdź pole o nazwie **parametry połączenia — klucz podstawowy** i kliknij przycisk kopiowania obok parametrów połączenia. 

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png" alt="Stream Analytics shared access policy" width="300px"/>
 
7.  Wklej parametry połączenia do edytora tekstu. Potrzebujesz te parametry połączenia dla następnej sekcji, po wprowadzeniu niektórych drobnych modyfikacji do niego.

    Parametry połączenia wyglądają następująco:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=asa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=asa-eh-frauddetection-demo

    Zwróć uwagę, że parametry połączenia zawierają wiele par klucz wartość, oddzielając je średnikami: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, i `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Skonfigurować i uruchomić aplikację generatora zdarzeń

Przed uruchomieniem aplikacji TelcoGenerator należy skonfigurować tak, aby rekordy połączeń zostanie wysłane do Centrum zdarzeń, który został utworzony.

### <a name="configure-the-telcogenerator-app"></a>Konfigurowanie aplikacji TelcoGenerator

1. W edytorze, gdzie został skopiowany ciąg połączenia, zanotuj `EntityPath` wartości, a następnie usuń `EntityPath` pary (nie zapomnij usunąć poprzedzającego ją średnika). 

2. W folderze, w którym rozpakowano plik TelcoGenerator.zip Otwórz plik telcodatagen.exe.config w edytorze. (Istnieje więcej niż jeden plik Config, dlatego upewnij się, że otworzyć właściwy).

3. W `<appSettings>` elementu:

   * Ustaw wartość `EventHubName` klucza nazwę Centrum zdarzeń (czyli wartość ścieżka jednostki).
   * Ustaw wartość `Microsoft.ServiceBus.ConnectionString` klucz do parametrów połączenia. 

   `<appSettings>` Sekcji będzie wyglądać podobnie jak w poniższym przykładzie. (W celu uściślenia wiersze zostaną opakowane i niektóre znaki zostały usunięte z tokenu autoryzacji).

   ![Plik konfiguracji TelcoGenerator zawiera parametry połączenia i nazwę Centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4. Zapisz plik. 

### <a name="start-the-app"></a>Uruchom aplikację
1.  Otwórz okno polecenia i przejdź do folderu, w którym aplikacja TelcoGenerator jest rozpakowany.

2.  Wprowadź następujące polecenie:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

    Dostępne są następujące parametry: 

    * Liczba CDR na godzinę. 
    * Prawdopodobieństwo oszustwa karty SIM: Jak często w procentach wszystkich wywołań, że aplikacja powinna symulować fałszywe połączenia. Wartość 0,2 oznacza, że około 20% rekordów połączeń będzie wyglądać na fałszywe.
    * Czas trwania w godzinach. Liczba godzin, które aplikacja powinna działać. Można je również zatrzymać aplikację dowolnym momencie, naciskając klawisze Ctrl + C w wierszu polecenia.

    Po kilku sekundach aplikacja rozpocznie wyświetlanie rekordów połączeń telefonicznych na ekranie w miarę wysyłania ich do centrum zdarzeń.

Niektóre pola klucza, które będą używane w tej aplikacji wykrywanie oszustw w czasie rzeczywistym są następujące:

|**Rekord**|**Definicja**|
|----------|--------------|
|`CallrecTime`|Znacznik czasu godziny rozpoczęcia połączenia. |
|`SwitchNum`|Centrala telefoniczna używana do wykonania połączenia. W tym przykładzie centrale są ciągami, które reprezentują kraj/region pochodzenia (USA, Chiny, Zjednoczone Królestwo, Niemcy lub Australia). |
|`CallingNum`|Numer telefonu dzwoniącego. |
|`CallingIMSI`|Numer IMSI (International Mobile Subscriber Identity). Jest to unikatowy identyfikator dzwoniącego. |
|`CalledNum`|Numer telefonu odbiorcy połączenia. |
|`CalledIMSI`|Numer IMSI (International Mobile Subscriber Identity). Jest to unikatowy identyfikator odbiorcy połączenia. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Tworzenie zadania usługi Stream Analytics do zarządzania danymi przesyłania strumieniowego

Teraz, gdy masz strumień zdarzeń połączeń, możesz skonfigurować zadanie usługi Stream Analytics. Zadanie będzie odczytywać dane z Centrum zdarzeń, który został ustawiony. 

### <a name="create-the-job"></a>Tworzenie zadania 

1. W witrynie Azure portal kliknij pozycję **Utwórz zasób** > **Internet of Things** > **zadania usługi Stream Analytics**.

2. Nadaj nazwę zadaniu `asa_frauddetection_job_demo`, określ subskrypcji, grupy zasobów i lokalizacji.

    Jest dobrym pomysłem jest umieszczenie zadania i Centrum zdarzeń w tym samym regionie, w celu uzyskania najlepszej wydajności i tak, aby nie płacić za transfer danych między regionami.

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png" alt="Create Stream Analytics job in portal" width="300px"/>

3. Kliknij pozycję **Utwórz**.

    Zadanie jest tworzone i portalu są wyświetlane szczegóły zadania. Nic nie jest jeszcze uruchomiona, chociaż — należy skonfigurować zadania, zanim można go uruchomić.

### <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

1. Na pulpicie nawigacyjnym lub **wszystkie zasoby** okienko, Znajdź i wybierz `asa_frauddetection_job_demo` zadania usługi Stream Analytics. 
2. W **Przegląd** sekcji okienka zadania usługi Stream Analytics, kliknij przycisk **dane wejściowe** pole.

   ![Pole wejściowe w ramach topologii, w okienku zadania usługi Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kliknij przycisk **Dodaj wejście strumienia** i wybierz **Centrum zdarzeń**. Następnie wprowadź nową stronę danych wejściowych z następującymi informacjami:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych  |  CallStream   |  Wprowadź nazwę identyfikującą dane wejściowe zadania.   |
   |Subskrypcja   |  \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, która ma Centrum zdarzeń utworzonego.   |
   |Przestrzeń nazw centrum zdarzeń  |  asa-eh-ns-demo |  Wprowadź nazwę przestrzeni nazw Centrum zdarzeń.   |
   |Nazwa centrum zdarzeń  | asa-eh-frauddetection-demo | Wybierz nazwę Centrum zdarzeń.   |
   |Nazwa zasad centrum zdarzeń  | asa-policy-manage-demo | Wybierz zasady dostępu, który został utworzony wcześniej.   |

    </br>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png" alt="Create Stream Analytics input in portal" width="300px"/>


4. Kliknij pozycję **Utwórz**.

## <a name="create-queries-to-transform-real-time-data"></a>Tworzenie zapytań do przekształcania danych w czasie rzeczywistym

W tym momencie należy skonfigurować do odczytywania przychodzącego strumienia danych zadania usługi Stream Analytics. Następnym krokiem jest, aby utworzyć zapytanie, które analizuje dane w czasie rzeczywistym. Stream Analytics obsługuje prosty, deklaratywny model zapytań opisujący przekształcenia do przetwarzania w czasie rzeczywistym. Zapytania korzystają podobnego do SQL języka, który ma niektórych rozszerzeń, które są specyficzne dla usługi Stream Analytics. 

Proste zapytanie może być tylko odczytać wszystkie dane przychodzące. Jednak często tworzenia kwerend wyszukiwania dla określonych danych lub relacji w danych. W tej części samouczka tworzenie i testowanie kilka zapytań, aby dowiedzieć się więcej na kilka sposobów, w których można przekształcać strumień wejściowy do analizy. 

Zapytania tworzone w tym miejscu po prostu wyświetli przekształconych danych do ekranu. W dalszej części tego tematu należy skonfigurować ujścia danych wyjściowych i zapytania, który zapisuje przekształcone dane do tego obiektu sink.

Aby dowiedzieć się więcej na temat języka, zobacz [dokumentacja języka zapytań usługi Azure Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Pobierz przykładowe dane do testowania zapytań

Aplikacja TelcoGenerator wysyła rekordy połączeń do Centrum zdarzeń i zadania usługi Stream Analytics jest skonfigurowana do odczytu z Centrum zdarzeń. Zapytanie służy do testowania zadań, aby upewnić się, że jest prawidłowo odczytu. Aby przetestować zapytanie w konsoli platformy Azure, potrzebujesz przykładowych danych. W tym przewodniku wyodrębnimy przykładowe dane ze strumienia, który przychodzi do Centrum zdarzeń.

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona i tworzyć rekordy połączeń.
2. W portalu wróć do okienka zadania usługi Stream Analytics. (Jeśli został zamknięty okienka, wyszukaj `asa_frauddetection_job_demo` w **wszystkie zasoby** okienka.)
3. Kliknij przycisk **zapytania** pole. Platforma Azure zawiera dane wejściowe i wyjściowe, które są skonfigurowane dla zadania i pozwala utworzyć kwerendę, która umożliwia przekształcenie strumienia wejściowego, ponieważ są wysyłane do wyjścia.
4. W **zapytania** okienku kliknij kropki obok `CallStream` dane wejściowe, a następnie wybierz pozycję **przykładowe dane z danych wejściowych**.

   ![Opcje menu, aby użyć danych przykładowych dla wpisu zadania usługi Stream Analytics, za pomocą "Przykładowe dane z danych wejściowych" wybrane](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)


5. Ustaw **minut** na 3 a następnie kliknij przycisk **OK**. 
    
   ![Próbkowanie strumienia wejściowego z więcej niż trzy minuty wybrane opcje](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Platforma Azure 3 minut, przez które dane ze strumienia wejściowego z przykładami i powiadamia użytkownika, gdy dane przykładowe są gotowe. (Trwa krótko). 

Przykładowe dane są przechowywane tymczasowo i są dostępne, kiedy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, przykładowe dane zostaną usunięte i konieczne będzie utworzenie nowego zestawu przykładowych danych. 

Jako alternatywę, można uzyskać plik JSON zawierający przykładowe dane w nim [z serwisu GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a następnie przekaż ten plik JSON do użycia jako przykładowe dane dla `CallStream` danych wejściowych. 

### <a name="test-using-a-pass-through-query"></a>Testowanie przy użyciu zapytania przekazującego

Jeśli chcesz zarchiwizować każdego zdarzenia, można użyć zapytania przekazującego, można odczytać wszystkich pól w ładunku zdarzenia.

1. W oknie zapytania wprowadź tego zapytania:
        
   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Podobnie jak w przypadku programu SQL, słowa kluczowe nie jest rozróżniana wielkość liter, a odstęp, nie ma znaczenia.

    W tym zapytaniu `CallStream` jest alias określony podczas tworzenia danych wejściowych. Jeśli używany jest inny alias, należy użyć tej nazwy.

2. Kliknij przycisk **testu**.

    Zadanie usługi Stream Analytics wykonuje zapytanie względem przykładowych danych i wyświetla dane wyjściowe w dolnej części okna. Wyniki wskazują, że Centrum zdarzeń i zadania usługi Stream Analytics są prawidłowo skonfigurowane. (Jak wspomniano, później utworzymy który zapytanie może zapisać danych do ujścia danych wyjściowych.)

   ![Stream Analytics dane wyjściowe zadania, przedstawiający 73 rekordy generowane](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Dokładna liczba rekordów wyświetlanych będzie zależeć od tego, ile rekordy zostały przechwycone w Twoim przykładzie 3-minutowy.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Zmniejsz liczbę pól za pomocą projekcji kolumny

W wielu przypadkach analizy nie potrzebuje wszystkich kolumn ze strumienia wejściowego. Zapytanie służy do projektu mniejszy zestaw pól zwrócone, niż zapytania przekazującego.

1. Zmień zapytanie w edytorze kodu do następujących:

   ```SQL
   SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
   FROM 
       CallStream
   ```

2. Kliknij przycisk **testu** ponownie. 

   ![Stream Analytics zadanie projekcji dane wyjściowe 25 rekordów](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Liczba wywołań przychodzących od regionu: Okno wirowania z agregacją

Załóżmy, że zechcesz policzyć liczbę wywołań przychodzących na region. W danych przesyłanych strumieniowo, gdy chcesz wykonać funkcje agregujące, takie jak inwentaryzacja, musisz segmentu strumienia do jednostek danych czasowych (ponieważ sam strumień danych jest skutecznie nieskończone). Możesz to zrobić za pomocą usługi Stream Analytics [funkcji okna](stream-analytics-window-functions.md). Możesz pracować z danych wewnątrz tego okna, jako jednostka.

Dla tej transformacji sekwencja danych czasowych systemu Windows, które się nie nakładały — każde okno będzie mieć odrębny zestaw danych, które można grupować i agregacji. Ten typ okna jest nazywany *okno wirowania*. W ramach okna wirowania, możesz uzyskać liczbę wywołań przychodzących, pogrupowane według `SwitchNum`, która reprezentuje kraj/region, gdzie zainicjowane było wywołanie. 

1. Zmień zapytanie w edytorze kodu do następujących:

        ```SQL
        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
        ```

    To zapytanie używa `Timestamp By` — słowo kluczowe w `FROM` klauzulę, aby określić które pole znacznika czasu w strumieniu wejściowym, aby użyć, aby zdefiniować okno wirowania. W tym przypadku okno dzieli dane na segmenty według `CallRecTime` pola w każdym rekordzie. (Jeśli żadne pole nie zostanie określony, operacja obsługi okien używa czasu każdego zdarzenia dociera do Centrum zdarzeń. Zobacz sekcję "Czas aplikacji Vs czas nadejścia" w [Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    Obejmuje projekcji `System.Timestamp`, która zwraca sygnaturę czasową na koniec każdego okna. 

    Aby określić, czy użyć okna wirowania, należy użyć [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) działa w programach `GROUP BY` klauzuli. W funkcji należy określić jednostkę czasu (dowolnym z mikrosekund na dzień) i rozmiaru okna (liczbę jednostek). W tym przykładzie okno wirowania składa się z odstępach 5-sekundowego, dzięki czemu otrzymasz liczbą według kraju/regionu dla co 5 sekund, przez które wywołania.

2. Kliknij przycisk **testu** ponownie. W wynikach, zwróć uwagę, że sygnatury czasowe, w obszarze **WindowEnd** znajdują się w przyrostach co 5 sekund.

   ![Dane wyjściowe do agregacji wyświetlanie rekordów 13 zadania usługi Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Wykrywanie oszustw SIM przy użyciu samosprzężenie

W tym przykładzie należy rozważyć użycie fałszywe się wywołania, które pochodzą z tego samego użytkownika, ale w różnych lokalizacjach w ciągu 5 sekund od siebie. Na przykład ten sam użytkownik nie może rzeczywiście wykonywać w tym samym czasie połączeń ze Stanów Zjednoczonych i Australii. 

Aby sprawdzić, czy te przypadki, służy samosprzężenie danych strumieniowych do dołączenia do strumienia do samego siebie na podstawie `CallRecTime` wartości. Następnie można wyszukać wywołanie rekordy, w których `CallingIMSI` wartość (numer źródłowy) jest taka sama, ale `SwitchNum` wartość (kraj/region źródła) nie jest taka sama.

Gdy używasz sprzężenia danych przesyłanych strumieniowo sprzężenie musi udostępniać pewne ograniczenia odległość między dwoma pasującymi wierszami, można oddzielić w czasie. (Jak wspomniano wcześniej, strumień danych jest skutecznie nieskończone). Granice czasowe dla relacji są określone w `ON` klauzuli sprzężenia, przy użyciu `DATEDIFF` funkcji. W tym przypadku przyłączenia opiera się na 5-sekundowego interwału połączenia danych.

1. Zmień zapytanie w edytorze kodu do następujących: 

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

    To zapytanie jest podobnie jak wszelkie sprzężenie SQL, z wyjątkiem `DATEDIFF` funkcja sprzężenia. Ta wersja `DATEDIFF` jest specyficzny dla usługi Stream Analytics i musi znajdować się w `ON...BETWEEN` klauzuli. Parametry są jednostka czasu (w sekundach w tym przykładzie) i aliasy dwóch źródeł, w celu utworzenia sprzężenia. To różni się od standardowego SQL `DATEDIFF` funkcji.

    `WHERE` Klauzula zawiera warunek, który flagi fałszywe połączenia: źródłowy przełączniki nie są takie same. 

2. Kliknij przycisk **testu** ponownie. 

   ![Dane wyjściowe generowane samosprzężenie, widoczny 6 rekordów zadania usługi Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kliknij przycisk **Zapisz** można zapisać zapytania samosprzężenie w ramach zadania usługi Stream Analytics. (Nie zostanie on zapisany przykładowych danych).

    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png" alt="Save Stream Analytics query in portal" width="300px"/>

## <a name="create-an-output-sink-to-store-transformed-data"></a>Tworzenie ujścia danych wyjściowych do przechowywania przekształconych danych

Zdefiniowany przez użytkownika strumienia zdarzeń, danych wejściowych w celu pozyskania zdarzeń i zapytania przekształcenie w strumieniu Centrum zdarzeń. Ostatnim krokiem jest określenie ujścia danych wyjściowych dla zadania — czyli miejsce do przekształcony potok do zapisu. 

Można używać wielu zasobów jako ujścia danych wyjściowych — bazy danych programu SQL Server, usługi table storage, magazyn usługi Data Lake, usługa Power BI i nawet inny Centrum zdarzeń. W tym samouczku przedstawiono tworzenie strumienia do magazynu obiektów Blob platformy Azure, która jest typowym wyborem do zbierania informacji o zdarzeniach do późniejszej analizy, ponieważ obsługuje dane bez określonej struktury.

Jeśli masz istniejące konto magazynu obiektów blob, można użyć, który. W tym samouczku dowiesz się, jak utworzyć nowe konto magazynu.

### <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta usługi Azure Blob Storage

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **Utwórz zasób** > **Magazyn** > **Konto magazynu**. Wypełnij strony zadania konta magazynu przy użyciu **nazwa** ustawiona na "asaehstorage" **lokalizacji** ustawiona na "Wschodnie stany USA", **grupy zasobów** ustawiona na "asa-eh-ns-rg" (host konto magazynu w tej samej grupie zasobów co zadanie przesyłania strumieniowego, aby zwiększyć wydajność). W przypadku pozostałych ustawień można pozostawić ich wartości domyślne.  

   ![Utwórz konto magazynu w witrynie Azure portal](./media/stream-analytics-real-time-fraud-detection/stream-analytics-storage-account-create.png)

2. W witrynie Azure portal wróć do okienka zadania usługi Stream Analytics. (Jeśli został zamknięty okienka, wyszukaj `asa_frauddetection_job_demo` w **wszystkie zasoby** okienka.)

3. W **topologia zadań** kliknij **dane wyjściowe** pole.

4. W **dane wyjściowe** okienku kliknij **Dodaj** i wybierz **magazynu obiektów Blob**. Następnie wypełnij nowej strony w danych wyjściowych z następującymi informacjami:

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wyjściowych  |  CallStream-FraudulentCalls   |  Wprowadź nazwę identyfikującą dane wyjściowe zadania.   |
   |Subskrypcja   |  \<Twoja subskrypcja\> |  Wybierz subskrypcję platformy Azure, która ma utworzone konto magazynu. Konto magazynu może być w tej samej lub innej subskrypcji. W tym przykładzie przyjęto założenie, że konto magazynu zostało utworzone w tej samej subskrypcji. |
   |Konto magazynu  |  asaehstorage |  Wprowadź nazwę utworzonego konta magazynu. |
   |Kontener  | asa-fraudulentcalls-demo | Wybierz pozycję Utwórz nowy, a następnie wprowadź nazwę kontenera. |

    <br/>
    <img src="./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png" alt="Create blob output for Stream Analytics job" width="300px"/>
    
5. Kliknij pozycję **Zapisz**. 


## <a name="start-the-streaming-analytics-job"></a>Uruchamianie zadania usługi Stream Analytics

Zadanie jest teraz skonfigurowane. Został określony, dane wejściowe (Centrum zdarzeń), transformacji (zapytanie wyszukując fałszywe wywołania) i danych wyjściowych (magazyn obiektów blob). Teraz można uruchomić zadania. 

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona.

2. W okienku zadania kliknij **Start**. W **zadanie rozpoczęcia** okienka danych wyjściowych godziną rozpoczęcia zadania, wybierz opcję **teraz**. 

   ![Uruchamianie zadania usługi Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start.png)



## <a name="examine-the-transformed-data"></a>Sprawdzanie przekształconych danych

Masz teraz ukończonego zadania usługi Stream Analytics. Zadanie jest badanie strumienia metadanych połączenia telefonicznego, wyszukiwanie fałszywych połączeń telefonicznych w czasie rzeczywistym i zapisywania informacji o tych fałszywych połączeń w magazynie. 

Do ukończenia tego samouczka, możesz przyjrzeć się danym są przechwytywane przez zadanie usługi Stream Analytics. Dane są zapisywane do magazynu Azure Blog Storage we fragmentach (pliki). Można użyć dowolnego narzędzia, która odczytuje usługi Azure Blob Storage. Jak wspomniano w sekcji wymagania wstępne, można użyć rozszerzeń platformy Azure w programie Visual Studio lub można użyć narzędzia, takiego jak [Eksploratora usługi Azure Storage](https://storageexplorer.com/) lub [Cerulean](https://www.cerebrata.com/products/cerulean/features/azure-storage). 

Po sprawdzeniu zawartości pliku w usłudze blob storage, zostanie wyświetlony podobny do poniższego:

   ![Usługa Azure blob storage za pomocą usługi Stream Analytics w danych wyjściowych](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Istnieją dodatkowe artykuły, które nadal ze scenariuszem wykrywanie oszustw i korzystać z zasobów utworzonych w ramach tego samouczka. Jeśli chcesz kontynuować, zobacz sugestie w obszarze **następne kroki**.

Jednak jeśli wszystko będzie gotowe, które nie potrzebują zasoby, które zostały utworzone, możesz je usunąć, aby nie powodują naliczania niepotrzebnych opłat platformy Azure. W takim przypadku zaleca się, należy wykonać następujące czynności:

1. Zatrzymaj zadanie usługi Stream Analytics. W **zadania** okienku kliknij **zatrzymać** u góry.
2. Zatrzymaj Telco aplikację generatora. W oknie wiersza polecenia, które wcześniej uruchomiono aplikację naciśnij klawisze Ctrl + C.
3. Jeśli utworzono nowe konto magazynu obiektów blob tylko do celów tego samouczka, należy go usunąć. 
4. Usuwanie zadania usługi Stream Analytics.
5. Usuwanie Centrum zdarzeń.
6. Usuń przestrzeń nazw Centrum zdarzeń.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby uzyskać dalszą pomoc, spróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

Możesz kontynuować z tego samouczka z następującym artykułem:

* [Stream Analytics i usługa Power BI: Pulpit nawigacyjny analizy w czasie rzeczywistym dla danych przesyłanych strumieniowo](stream-analytics-power-bi-dashboard.md). W tym artykule dowiesz się, jak wysyłać TelCo dane wyjściowe zadania usługi Stream Analytics do usługi Power BI do wizualizacji w czasie rzeczywistym i analizy.

Aby uzyskać więcej informacji na temat usługi Stream Analytics ogólnie rzecz biorąc, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
