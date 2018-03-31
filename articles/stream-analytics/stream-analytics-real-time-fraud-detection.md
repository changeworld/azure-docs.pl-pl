---
title: 'Usługi analiza strumienia: Wykrywanie oszustw w czasie rzeczywistym | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak utworzyć rozwiązanie wykrywania oszustw w czasie rzeczywistym z usługi Stream Analytics. Użyj Centrum zdarzeń do przetwarzania zdarzeń w czasie rzeczywistym.
keywords: wykrywanie anomalii, wykrywanie oszustw, wykrywania anomalii w czasie rzeczywistym
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: c10dd53f-d17a-4268-a561-cb500a8c04eb
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: sngun
ms.openlocfilehash: a622b7851f52f65efe4450191c2cf65e73e816bc
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Rozpoczynanie pracy przy użyciu usługi Azure Stream Analytics: wykrywanie oszustw w czasie rzeczywistym

Ten samouczek zawiera ilustrację end-to-end można używać usługi Azure Stream Analytics. Omawiane kwestie: 

* Przełącz strumienia zdarzeń do wystąpienia usługi Azure Event hubs. W tym samouczku użyjesz aplikacji, która udostępniamy symulujący strumienia rekordów metadanych telefonu komórkowego.

* Pisanie zapytań usługi Stream Analytics przypominającego SQL do przekształcania danych, agregowanie informacji lub będzie zaglądać wzorców. Pojawi się jak używać kwerendy do badania strumienia przychodzącego i wyszukaj wywołania, które mogą być fałszywe.

* Wysyłać wyniki do ujścia danych wyjściowych (magazyn), który można analizować, aby uzyskać dodatkowe informacje szczegółowe. W takim przypadku będzie wysłać dane podejrzane wywołania do magazynu obiektów Blob Azure.

W tym samouczku używamy przykład wykrywanie oszustw w czasie rzeczywistym na podstawie danych z połączenia telefonicznego. Jednak techniki, które firma Microsoft ilustrują nadaje się również dla innych typów wykrywania oszustw, takie jak karty kredytowej oszustwa lub kradzieży tożsamości. 

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>Scenariusz: Telekomunikacyjnych i SIM wykrywanie oszustw w czasie rzeczywistym

Firmy telekomunikacyjnych ma duże ilości danych na połączenia przychodzące. Firma chce, aby wykryć fałszywych wywołań w czasie rzeczywistym, aby mogli powiadamiać klientów lub wyłączyć usługi dla określonej liczby. Jeden typ oszustwa SIM obejmuje wiele wywołań z tej samej tożsamości zbliżonym do czasu, ale w geograficznie różnych lokalizacjach. Do wykrywania oszustw tego typu, firma musi zbadać przychodzące rekordów telefonu i szukania wzorców określonych — w takim przypadku dla wywołań zbliżonym do czasu z różnych krajów. Rekordy telefonu, które należą do tej kategorii są zapisywane w pamięci masowej do dalszej analizy.

## <a name="prerequisites"></a>Wymagania wstępne

W tym samouczku będzie symulować rozmowy telefonicznej danych przy użyciu aplikacji klienta, która generuje próbki rozmowy telefonicznej metadanych. Niektóre rekordy, które tworzy aplikację wyglądać fałszywych wywołania. 

Przed rozpoczęciem upewnij się, że należy dysponować następującymi elementami:

* Konto platformy Azure.
* Zdarzenie wywołania generator aplikacji. Możesz uzyskać dostęp do tej pobierając [pliku TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z Microsoft Download Center. Rozpakuj pakiet do folderu na komputerze. Jeśli chcesz wyświetlić źródła kodu i uruchamianie aplikacji w debugerze, można pobrać kodu źródłowego aplikacji z [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator). 

    >[!NOTE]
    >System Windows może blokować pliku zip pobranego. Jeśli nie można rozpakować go, kliknij prawym przyciskiem myszy plik i wybierz **właściwości**. Jeśli zostanie wyświetlony komunikat "ten plik pochodzi z innego komputera i może zostać zablokowany w celu ochrony tego komputera", wybierz **Odblokuj** opcji, a następnie kliknij przycisk **Zastosuj**.

Jeśli chcesz przejrzeć wyniki zadanie analizy przesyłania strumieniowego, należy również narzędzia do wyświetlania zawartości kontenera magazynu obiektów Blob Azure. Jeśli używasz programu Visual Studio, możesz użyć [Azure Tools dla programu Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) lub [programu Visual Studio Cloud Explorer](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer). Alternatywnie można zainstalować narzędzi autonomicznych, takich jak [Eksploratora usługi Storage Azure](http://storageexplorer.com/) lub [Eksploratora Azure](http://www.cerebrata.com/products/azure-explorer/introduction). 

## <a name="create-an-azure-event-hubs-to-ingest-events"></a>Tworzenie usługi Azure event koncentratory pozyskiwanie zdarzenia

Do analizy strumienia danych, możesz *pozyskiwania* go na platformie Azure. Typowy sposób pozyskiwania danych jest użycie [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md), które pozwala obsługiwać miliony zdarzeń na sekundę, a następnie przetwarzania i przechowywania informacji o zdarzeniu. W tym samouczku tworzenia Centrum zdarzeń i mieć aplikacji generator zdarzenie wywołania wysyłać dane wywołania do tego Centrum zdarzeń. Aby uzyskać więcej informacji na temat usługi event hubs, zobacz [dokumentacji usługi Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).

>[!NOTE]
>Bardziej szczegółowe wersja tej procedury dla [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą portalu Azure](../event-hubs/event-hubs-create.md). 

### <a name="create-a-namespace-and-event-hub"></a>Tworzenie Centrum przestrzeń nazw i zdarzeń
W tej procedurze należy najpierw utworzyć przestrzeń nazw Centrum zdarzeń, a następnie Centrum zdarzeń należy dodać do tej przestrzeni nazw. Przestrzenie nazw Centrum zdarzeń są używane do logicznego grupowania wystąpień magistrali powiązanych zdarzeń. 

1. Zaloguj się do portalu Azure, a następnie kliknij przycisk **Utwórz zasób** > **Internetu rzeczy** > **Centrum zdarzeń**. 

2. W **tworzenie przestrzeni nazw** okienku, wprowadź nazwę przestrzeni nazw, takich jak `<yourname>-eh-ns-demo`. Można użyć dowolnej nazwy przestrzeni nazw, ale nazwa musi być prawidłowa dla danego adresu URL i między Azure musi być unikatowa. 
    
3. Wybierz subskrypcję i Utwórz lub wybierz grupę zasobów, a następnie kliknij przycisk **Utwórz**. 

    ![Tworzenie przestrzeni nazw Centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-namespace-new-portal.png)
 
4. Po zakończeniu przestrzeń nazw wdrażania można znaleźć przestrzeni nazw zdarzenia koncentratora na liście zasobów platformy Azure. 

5. Kliknij nowy obszar nazw, a w okienku przestrzeni nazw kliknij **Centrum zdarzeń**.

    ![Przycisk Dodaj Centrum zdarzeń do tworzenia nowego Centrum zdarzeń ](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-button-new-portal.png)    
 
6. Nazwa nowego Centrum zdarzeń `sa-eh-frauddetection-demo`. Można użyć innej nazwy. Jeśli to zrobisz, zanotuj, ponieważ później potrzebne. Nie musisz teraz Ustaw inne opcje dla Centrum zdarzeń.

    ![Blok do tworzenia nowego Centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-eventhub-new-portal.png)
    
 
7. Kliknij przycisk **Utwórz**.
### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Zezwalaj na dostęp do Centrum zdarzeń i pobrać ciągu połączenia

Aby proces może wysyłać dane do Centrum zdarzeń, Centrum zdarzeń muszą mieć zasadę, która umożliwia uzyskanie odpowiedniego dostępu. Zasady dostępu generuje ciąg połączenia, który zawiera informacje o autoryzacji.

1.  W okienku przestrzeni nazw zdarzeń kliknij **usługi Event Hubs** , a następnie kliknij nazwę nowego Centrum zdarzeń.

2.  W okienku Centrum zdarzeń, kliknij przycisk **zasady dostępu współużytkowanego** , a następnie kliknij przycisk  **+ &nbsp;Dodaj**.

    >[!NOTE]
    >Upewnij się, że pracujesz z Centrum zdarzeń, a nie nazw Centrum zdarzeń.

3.  Dodaj zasady o nazwie `sa-policy-manage-demo` i **oświadczeń**, wybierz pozycję **Zarządzaj**.

    ![Blok do tworzenia nowych zasad dostępu do Centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-shared-access-policy-manage-new-portal.png)
 
4.  Kliknij przycisk **Utwórz**.

5.  Po wdrożeniu zasad, kliknij go na liście zasady dostępu współdzielonego.

6.  Znajdź pole o nazwie **klucz podstawowy ciąg połączenia** i kliknij przycisk Kopiuj obok ciągu połączenia. 
    
    ![Kopiowanie klucza podstawowego połączenia ciągu z zasad dostępu](./media/stream-analytics-real-time-fraud-detection/stream-analytics-shared-access-policy-copy-connection-string-new-portal.png)
 
7.  Wklej parametry połączenia w edytorze tekstu. Należy tego ciągu połączenia dla następnej sekcji, po wprowadzeniu niektóre zmiany mała do niego.

    Parametry połączenia wygląda następująco:

        Endpoint=sb://YOURNAME-eh-ns-demo.servicebus.windows.net/;SharedAccessKeyName=sa-policy-manage-demo;SharedAccessKey=Gw2NFZwU1Di+rxA2T+6hJYAtFExKRXaC2oSQa0ZsPkI=;EntityPath=sa-eh-frauddetection-demo

    Powiadomienie, że parametry połączenia zawierają wiele par klucz wartość, oddzielając je średnikami: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, i `EntityPath`.  

## <a name="configure-and-start-the-event-generator-application"></a>Skonfiguruj i uruchom aplikację generator zdarzeń

Przed rozpoczęciem TelcoGenerator aplikacji, możesz ją skonfigurować tak, aby wysyła wywołania rekordów do Centrum zdarzeń, który został utworzony.

### <a name="configure-the-telcogeneratorapp"></a>Skonfiguruj TelcoGeneratorapp

1.  W edytorze, której są kopiowane parametry połączenia, zanotuj `EntityPath` wartość, a następnie usuń `EntityPath` pary (nie zapomnij Usuń średnik przechwyceniem). 

2.  W folderze, gdzie unzipped pliku TelcoGenerator.zip Otwórz plik telcodatagen.exe.config w edytorze. (Istnieje więcej niż jeden plik .config, dlatego upewnij się, że otworzyć właściwy).

3.  W `<appSettings>` elementu:

    * Ustaw wartość `EventHubName` klucza do nazwy Centrum zdarzeń (oznacza to, że wartość jednostek ścieżki).
    * Ustaw wartość `Microsoft.ServiceBus.ConnectionString` klucza w parametrach połączenia. 

    `<appSettings>` Sekcji będzie wyglądać jak w następującym przykładzie. (Dla uzyskania przejrzystości, możemy opakowana wiersze i usunąć niektóre znaki z tokenu autoryzacji.)

    ![Plik konfiguracji aplikacji TelcoGenerator przedstawiający ciąg połączenia i nazwę Centrum zdarzeń](./media/stream-analytics-real-time-fraud-detection/stream-analytics-telcogenerator-config-file-app-settings.png)
 
4.  Zapisz plik. 

### <a name="start-the-app"></a>Uruchom aplikację
1.  Otwórz okno polecenia i przejdź do folderu, gdzie jest rozpakowanej aplikacji TelcoGenerator.
2.  Wprowadź następujące polecenie:

        telcodatagen.exe 1000 .2 2

    Dostępne są następujące parametry: 

    * Liczba CDR na godzinę. 
    * Prawdopodobieństwo oszustwa karta SIM: Jak często jako procent wszystkich wywołań, że aplikacja powinny symulować fałszywych wywołania. Wartość.2 oznacza, że który około 20% rekordów wywołania będzie wyglądać fałszywe.
    * Czas trwania w godzinach. Liczba godzin, które powinny być uruchamiane w aplikacji. Można również zatrzymać aplikację dowolnej chwili, naciskając klawisze Ctrl + C w wierszu polecenia.

    Po kilku sekundach uruchomieniu aplikacji wyświetlanie rekordy połączeń telefonicznych na ekranie, jak wysyła je do Centrum zdarzeń.

Niektóre pola klucza, które będą używane w tej aplikacji wykrywanie oszustw w czasie rzeczywistym, są następujące:

|**rekord**|**Definicja**|
|----------|--------------|
|`CallrecTime`|Godzina rozpoczęcia znacznik czasu wywołania. |
|`SwitchNum`|Przełącznik telefonów używane do połączenia. W tym przykładzie przełączniki są ciągów reprezentujących kraj pochodzenia (USA, Chinach, UK, Niemcy lub Australii). |
|`CallingNum`|Numer telefonu obiektu wywołującego. |
|`CallingIMSI`|Tożsamość subskrybenta przenośnych międzynarodowe (firmy IMSI). Jest to unikatowy identyfikator obiektu wywołującego. |
|`CalledNum`|Numer telefonu adresata wywołania. |
|`CalledIMSI`|Subskrybent międzynarodowy przenośnych tożsamość (firmy IMSI). Jest to unikatowy identyfikator odbiorcy wywołania. |


## <a name="create-a-stream-analytics-job-to-manage-streaming-data"></a>Utwórz zadanie usługi Stream Analytics do zarządzania danymi przesyłania strumieniowego

Teraz, gdy masz strumienia zdarzeń wywołania można skonfigurować zadania usługi analiza strumienia. Zadanie będzie odczytywać dane z Centrum zdarzeń, które można skonfigurować. 

### <a name="create-the-job"></a>Utwórz zadanie 

1. W portalu Azure kliknij **Utwórz zasób** > **Internetu rzeczy** > **zadanie usługi Stream Analytics**.

2. Nazwa zadania `sa_frauddetection_job_demo`, określ subskrypcję, lokalizacji i grupy zasobów.

    Należy dobrze jest umieścić zadania i Centrum zdarzeń, w tym samym regionie, w celu uzyskania najlepszej wydajności i tak, aby nie płaci się na przesyłanie danych między regionami.

    ![Tworzenie nowego zadania usługi analiza strumienia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-job-new-portal.png)

3. Kliknij przycisk **Utwórz**.

    Zadanie jest tworzony i portalu Wyświetla szczegóły zadania. Nic nie jest jeszcze uruchomiona, ale — należy skonfigurować zadania, aby można było go uruchomić.

### <a name="configure-job-input"></a>Skonfiguruj zadania w danych wejściowych

1. Na pulpicie nawigacyjnym lub **wszystkie zasoby** , Znajdź i wybierz `sa_frauddetection_job_demo` zadania usługi analiza strumienia. 
2. W **topologii zadania** sekcji okienka zadania Stream Analytics kliknij **dane wejściowe** pole.

    ![Pole wprowadzania w topologii w okienku zadania przesyłania strumieniowego analityka](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-input-box-new-portal.png)
 
3. Kliknij przycisk  **+ &nbsp;Dodaj** , a następnie wypełnij okienko z tymi wartościami:

    * **Alias wejściowy**: Użyj nazwy `CallStream`. Użyj innej nazwy, aby je zapisać, ponieważ będzie on potrzebny później.
    * **Typ źródła**: Wybierz **strumienia danych**. (**Danych referencyjnych** odwołuje się do danych statycznych wyszukiwania, które nie będą korzystać z tego samouczka.)
    * **Źródło**: Wybierz **Centrum zdarzeń**.
    * **Opcji importowania**: Wybierz **Użyj Centrum zdarzeń z bieżącej subskrypcji**. 
    * **Przestrzeń nazw magistrali usług**: Wybierz obszar nazw Centrum zdarzeń utworzonego wcześniej (`<yourname>-eh-ns-demo`).
    * **Centrum zdarzeń**: wybierz utworzony wcześniej Centrum zdarzeń (`sa-eh-frauddetection-demo`).
    * **Nazwa zasad Centrum zdarzeń**: Wybierz zasady dostępu, który został utworzony wcześniej (`sa-policy-manage-demo`).

    ![Utwórz nowe dane wejściowe zadania przesyłania strumieniowego usługi analiza](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sa-input-new-portal.png)

4. Kliknij przycisk **Utwórz**.

## <a name="create-queries-to-transform-real-time-data"></a>Tworzenie zapytań do przekształcania danych w czasie rzeczywistym

W tym momencie masz zadanie usługi Stream Analytics, konfigurowanie odczytać przychodzącego strumienia danych. Następnym krokiem jest, aby utworzyć transformację, która analizuje dane w czasie rzeczywistym. Można to zrobić, tworząc kwerendy. Analiza strumienia obsługuje prosty, deklaratywny model zapytań opisujący przekształceń do przetworzenia w czasie rzeczywistym. Zapytania używać języka przypominającego SQL, który ma niektóre rozszerzenia, które są specyficzne dla usługi analiza strumienia. 

Proste zapytanie tylko może odczytać wszystkich danych przychodzących. Jednak często tworzyć zapytania, które wyglądają dla określonych danych lub w przypadku relacji w danych. W tej części samouczka możesz utworzyć i przetestować kilka zapytań, aby dowiedzieć się więcej na kilka sposobów, w których można przekształcać strumień wejściowy do analizy. 

Zapytania tworzone w tym miejscu wyświetli tylko przekształconych danych do ekranu. W dalszej części artykułu należy skonfigurować ujście danych wyjściowych i zapytania, który zapisuje przekształcone dane do tego obiektu sink.

Aby dowiedzieć się więcej na temat języka, zobacz [Azure Stream Analytics zapytania Language Reference](https://msdn.microsoft.com/library/dn834998.aspx).

### <a name="get-sample-data-for-testing-queries"></a>Pobierz przykładowe dane do testowania zapytań

Aplikacja TelcoGenerator wysyła wywołania rekordów do Centrum zdarzeń i zadania usługi Stream Analytics jest skonfigurowany do odczytu z Centrum zdarzeń. Zapytanie służy do zadania, aby upewnić się, że jest poprawnie odczytywania testowego. Aby przetestować zapytanie w konsoli platformy Azure, należy przykładowych danych. W ramach tego przewodnika będą Wyodrębnij przykładowych danych ze strumienia, który będzie dostępna w Centrum zdarzeń.

1. Upewnij się, że TelcoGenerator aplikacji działa i tworzenie rekordów wywołania.
2. W portalu wrócić do okienka zadanie analizy przesyłania strumieniowego. (Jeśli został zamknięty okienku, wyszukaj `sa_frauddetection_job_demo` w **wszystkie zasoby** okienko.)
3. Kliknij przycisk **zapytania** pole. Azure wymieniono wejściach i wyjściach, które są skonfigurowane dla zadania i umożliwia utworzenie zapytania umożliwiające przekształcanie strumień wejściowy podczas przesyłania danych wyjściowych.
4. W **zapytania** okienku kliknij punktów `CallStream` danych wejściowych, a następnie wybierz **przykładowe dane z danych wejściowych**.

    ![Opcje menu do użycia dla wpisu zadanie analizy przesyłania strumieniowego, z "Przykładowych danych z danych wejściowych" wybrane przykładowe dane](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-sample-data-from-input.png)

    Spowoduje to otwarcie okienka, co pozwala określić, ile przykładowych danych, aby uzyskać zdefiniowanych pod względem czasu odczytu strumienia wejściowego.

5. Ustaw **minut** 3, a następnie kliknij przycisk **OK**. 
    
    ![Próbkowanie strumienia wejściowego z "3 minuty" wybrane opcje.](./media/stream-analytics-real-time-fraud-detection/stream-analytics-input-create-sample-data.png)

    Azure przykłady 3 minut, przez które danych ze strumienia wejściowego i powiadamia użytkownika, gdy będzie gotowy przykładowych danych. (Trwa to krótki czas). 

Przykładowe dane są tymczasowo przechowywane i jest dostępny, gdy masz okna kwerendy. Jeśli zamkniesz okno kwerendy przykładowe dane są usuwane, a następnie musisz utworzyć nowy zestaw przykładowych danych. 

Alternatywnie, można uzyskać pliku JSON, który zawiera przykładowe dane w nim [z usługi GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a następnie przekaż ten plik JSON ma być używana jako dane przykładowe dla `CallStream` wejściowego. 

### <a name="test-using-a-pass-through-query"></a>Testowanie przy użyciu zapytania przekazującego

Jeśli chcesz zarchiwizować każde zdarzenie, można użyć zapytania przekazującego, do odczytu wszystkich pól w ładunku zdarzenia.

1. W oknie zapytania wprowadź tego zapytania:

        SELECT 
            *
        FROM 
            CallStream

    >[!NOTE]
    >Podobnie jak w przypadku SQL, słowa kluczowe nie jest rozróżniana i spacji, nie ma znaczenia.

    W tym zapytaniu `CallStream` jest alias określony podczas tworzenia danych wejściowych. Jeśli używasz inny alias, należy użyć tej nazwy.

2. Kliknij przycisk **testu**.

    Zadanie Stream Analytics jest uruchamiana zapytanie dla przykładowych danych i wyświetla dane wyjściowe w dolnej części okna. Oznacza to, że Centrum zdarzeń i zadanie analizy przesyłania strumieniowego są prawidłowo skonfigurowane. (Jak wspomniano, później utworzysz ujścia danych wyjściowych, który kwerendy można zapisywać danych.)

    ![Zadania Stream Analytics dane wyjściowe, wyświetlanie rekordów 73 wygenerowany](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output.png)

    Dokładna liczba rekordów wyświetlanych będzie zależeć od liczbę rekordów zostały przechwycone w Twoim przykładzie 3 minuty.
 
### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Zmniejsz liczbę pól przy użyciu projekcji kolumny

W wielu przypadkach analizy nie potrzebuje wszystkich kolumn ze strumienia wejściowego. Zapytanie służy do projektu był mniejszy zestaw zwrócony pól niż zapytania przekazującego.

1. Zmień zapytanie w edytorze kodu do następującego:

        SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum 
        FROM 
            CallStream

2. Kliknij przycisk **testu** ponownie. 

    ![Zadania Stream Analytics wyjściowego dla projekcji, wyświetlanie 25 rekordów wygenerowany](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-projection.png)
 
### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Liczba przychodzących wywołuje według regionu: okno wirowania z agregacją

Załóżmy, że chcesz liczbę połączeń przychodzących na region. W przesyłanie strumieniowe danych, jeśli chcesz wykonywać funkcje agregujące, takie jak zliczanie, należy segmentów strumienia na jednostki danych czasowych (ponieważ sam strumień danych jest skutecznie nieskończone). Można to zrobić przy użyciu usługi analiza strumienia [funkcji okna](stream-analytics-window-functions.md). Możesz następnie pracować z danymi w danym przedziale jako jednostka.

Dla tej transformacji sekwencja danych czasowych systemu Windows, które nie nakładają się na — każde okno będzie mieć odrębny zestaw danych, które można grupować i agregacji. Ten typ okna jest określany jako *okno wirowania* . W ramach okno wirowania można uzyskać liczbę przychodzących pogrupowane według `SwitchNum`, który reprezentuje kraju, w którym zainicjowano wywołanie. 

1. Zmień zapytanie w edytorze kodu do następującego:

        SELECT 
            System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
        FROM
            CallStream TIMESTAMP BY CallRecTime 
        GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

    To zapytanie używa `Timestamp By` — słowo kluczowe w `FROM` klauzuli, aby określić w strumieniu wejściowym można użyć, aby zdefiniować okno wirowania pola sygnatury czasowej. W takim przypadku okno dzieli dane na segmenty przez `CallRecTime` w każdego rekordu. (Jeśli żadne pole nie zostanie określony, operacja okien używa podczas każdego zdarzenia dociera do Centrum zdarzeń. Zobacz sekcję "Godzina aplikacji Vs Godzina nadejścia" w [strumienia Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx). 

    Obejmuje projekcji `System.Timestamp`, która zwraca sygnatury czasowej do końca każde okno. 

    Aby określić, że okno wirowania, należy użyć [TUMBLINGWINDOW](https://msdn.microsoft.com/library/dn835055.aspx) działać w `GROUP BY `klauzuli. W funkcji należy określić jednostki czasu (dowolnym z mikrosekundy na dzień) i rozmiaru okna (liczbę jednostek). W tym przykładzie okno wirowania składa się z 5 sekund, tak liczba według kraju będzie uzyskać co 5 sekund, przez które wywołania.

2. Kliknij przycisk **testu** ponownie. W wynikach, zwróć uwagę, że sygnatury czasowe w obszarze **WindowEnd** w przyrostach 5 sekund.

    ![Zadania Stream Analytics dane wyjściowe do agregacji, wyświetlanie rekordów 13 wygenerowany](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-aggregation.png)
 
### <a name="detect-sim-fraud-using-a-self-join"></a>Wykrywanie oszustw SIM przy użyciu samosprzężenie

Na przykład firma Microsoft należy rozważyć użycie fałszywych jako wywołania, które pochodzą z tego samego użytkownika, ale w różnych lokalizacjach w ciągu 5 sekund od siebie. Na przykład tego samego użytkownika nie legalnie wywoływania z USA i klientów w Australii w tym samym czasie. 

Aby sprawdzić, czy te przypadki, umożliwia samosprzężenia potrzebne dane przesyłane strumieniowo przyłączyć się do samego siebie na podstawie strumienia `CallRecTime` wartość. Następnie można wyszukać wywołania rekordy, w których `CallingIMSI` wartość (liczba źródłowego) jest taka sama, ale `SwitchNum` wartość (kraj pochodzenia) nie jest taka sama.

Gdy używasz sprzężenia na danych przesyłanych strumieniowo sprzężenie należy udostępnić niektóre limity, jak daleko pasujących wierszy mogą być oddzielone w czasie. (Jak wspomniano wcześniej, dane przesyłane strumieniowo to skutecznie nieskończone). Granice czasu dla relacji są określone wewnątrz `ON` klauzuli sprzężenia, przy użyciu `DATEDIFF` funkcji. W takim przypadku sprzężenia jest oparta na interwał 5 sekund, połączenie danych.

1. Zmień zapytanie w edytorze kodu do następującego: 

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

    To zapytanie jest podobna wszelkich sprzężenia SQL, z wyjątkiem `DATEDIFF` funkcja sprzężenia. To jest wersja `DATEDIFF` jest specyficzne dla analizy przesyłania strumieniowego i musi znajdować się w `ON...BETWEEN` klauzuli. Parametry są jednostka czasu (w sekundach w tym przykładzie) i aliasy dwóch źródeł w celu utworzenia sprzężenia. (Ta różni się od standardowego SQL `DATEDIFF` funkcji.) 

    `WHERE` Klauzula zawiera warunek, który flagi fałszywych wywołania: przełączniki źródłowego nie są takie same. 

2. Kliknij przycisk **testu** ponownie. 

    ![Dane wyjściowe samosprzężenie, wyświetlony 6 rekordy generowane zadania Stream Analytics](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-sample-output-self-join.png)

3. Kliknij pozycję **Zapisz**. Spowoduje to zapisanie samosprzężenie zapytania jako część zadania przesyłania strumieniowego Analytics. (Nie zostanie on zapisany przykładowe dane).

    ![Zapisz zadanie usługi analiza strumienia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-save-button-new-portal.png)

## <a name="create-an-output-sink-to-store-transformed-data"></a>Utwórz ujście danych wyjściowych do przechowywania przekształcone dane

Zdefiniowany przez użytkownika strumienia zdarzeń Centrum zdarzeń wejście do pozyskiwania zdarzenia i zapytanie w celu wykonania transformację przez strumień. Ostatnim krokiem jest zdefiniowanie ujście danych wyjściowych zadania, czyli miejsce do przekształcony potok do zapisu. 

Wiele zasobów można używać jako dane wyjściowe wychwytywanie — bazy danych programu SQL Server, Magazyn tabel, Data Lake storage, usługi Power BI i nawet innej Centrum zdarzeń. W tym samouczku przedstawiono tworzenie strumienia do magazynu obiektów Blob Azure, co typowe nadaje się do zbierania informacji o zdarzeniach dotyczących późniejszej analizy, ponieważ bierze pod uwagę danych bez struktury.

Jeśli masz istniejące konto magazynu obiektów blob, można użyć, który. W tym samouczku pokażemy ci Tworzenie nowego konta magazynu tylko do celów tego samouczka.

### <a name="create-an-azure-blob-storage-account"></a>Tworzenie konta usługi Magazyn obiektów Blob Azure

1. W portalu Azure wrócić do okienka zadanie analizy przesyłania strumieniowego. (Jeśli został zamknięty okienku, wyszukaj `sa_frauddetection_job_demo` w **wszystkie zasoby** okienko.)
2. W **topologii zadania** kliknij **dane wyjściowe** pole. 
3. W **dane wyjściowe** okienku, kliknij przycisk  **+ &nbsp;Dodaj** , a następnie wypełnij okienko z tymi wartościami:

    * **Alias wyjściowy**: Użyj nazwy `CallStream-FraudulentCalls`. 
    * **Obiekt sink**: Wybierz **magazynu obiektów Blob**.
    * **Opcje importowania**: Wybierz **Użyj magazynu obiektów blob z bieżącej subskrypcji**.
    * **Konto magazynu**. Wybierz **Utwórz nowe konto magazynu.**
    * **Konto magazynu** (drugie pole). Wprowadź `YOURNAMEsademo`, gdzie `YOURNAME` jest nazwa użytkownika lub inny unikatowy ciąg. Nazwę można użyć tylko małe litery i cyfry, i między Azure musi być unikatowa. 
    * **Kontener**. Wprowadź `sa-fraudulentcalls-demo`.
    Nazwa konta magazynu i nazwę kontenera są używane razem zapewnienie identyfikator URI magazynu obiektów blob, jak to: 

    `http://yournamesademo.blob.core.windows.net/sa-fraudulentcalls-demo/...`
    
    !["Nowe dane wyjściowe" okienka zadania usługi analiza strumienia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-create-output-blob-storage-new-console.png)
    
4. Kliknij przycisk **Utwórz**. 

    Azure tworzy konto magazynu i automatycznie generuje klucz. 

5. Zamknij **dane wyjściowe** okienka. 

## <a name="start-the-streaming-analytics-job"></a>Uruchom zadanie analizy przesyłania strumieniowego

Zadanie jest teraz skonfigurowany. Zostały określone dane wejściowe (Centrum zdarzeń), transformacji (zapytanie do wyszukania fałszywych wywołań) i danych wyjściowych (magazynu obiektów blob). Można teraz uruchomić zadania. 

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona.

2. W okienku zadania kliknij **Start**.

    ![Uruchom zadanie usługi analiza strumienia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-output.png)

3. W **rozpoczęcia zadania** okienka dla danych wyjściowych czas uruchomienia zadania, wybierz opcję **teraz**. 

4. Kliknij przycisk **Start**. 

    !["Uruchomić zadanie" okienka zadania usługi analiza strumienia](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-start-job-blade.png)

    Azure umożliwia powiadomienie, gdy zadanie została uruchomiona, a w okienku zadania, stan jest wyświetlany jako **systemem**.

    ![Stan zadania Stream Analytics, przedstawiający "Uruchomiona"](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-running-status.png)
    

## <a name="examine-the-transformed-data"></a>Sprawdź przekształcone dane

Masz teraz pełną zadanie analizy przesyłania strumieniowego. Zadanie jest badanie strumienia metadanych rozmowy telefonicznej, wyszukiwanie fałszywych rozmowy telefoniczne w czasie rzeczywistym i zapisywania informacji o fałszywych wywołań do magazynu. 

Do ukończenia tego samouczka można znaleźć w danych są przechwytywane przez zadanie analizy przesyłania strumieniowego. Dane są zapisywane do magazynu Azure w blogu w fragmentów (pliki). Można użyć dowolnego narzędzia, które odczytuje magazynu obiektów Blob Azure. Jak wspomniano w sekcji wymagania wstępne, można użyć rozszerzeń Azure w programie Visual Studio lub skorzystać z narzędzia, takiego jak [Eksploratora usługi Storage Azure](http://storageexplorer.com/) lub [Eksploratora Azure](http://www.cerebrata.com/products/azure-explorer/introduction). 

Po sprawdzeniu zawartości pliku w magazynie obiektów blob, zostanie wyświetlony ekran podobny do następującego:

![Magazyn obiektów blob platformy Azure z analizy strumienia wyjściowego](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sa-job-blob-storage-view.png)
 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Mamy dodatkowe artykuły, które kontynuować ze scenariuszem wykrywania oszustw, które korzystają z zasobów, które zostały utworzone w tym samouczku. Jeśli chcesz kontynuować, zobacz sugestie pod **następne kroki** później.

Jednak jeśli wszystko będzie gotowe, a nie ma potrzeby zasoby, które zostały utworzone, można usunąć je, aby nie wiąże się z opłatami Azure niepotrzebne. W takim przypadku zaleca się wykonanie następujących czynności:

1. Zatrzymaj zadanie analizy przesyłania strumieniowego. W **zadania** okienku, kliknij przycisk **zatrzymać** u góry.
2. Zatrzymaj Telco Generator aplikacji. W oknie polecenia, w którym uruchomiono aplikację naciśnij klawisze Ctrl + C.
3. Jeśli utworzono nowe konto magazynu obiektów blob tylko do celów tego samouczka, należy ją usunąć. 
4. Usuń zadanie analizy przesyłania strumieniowego.
5. Usuń Centrum zdarzeń.
6. Usuń przestrzeń nazw Centrum zdarzeń.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Aby uzyskać dodatkową pomoc, spróbuj naszych [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Kolejne kroki

Można kontynuować tego samouczka z następujących artykułów:

* [Strumienia analizy i usługi Power BI: pulpitu nawigacyjnego analytics w czasie rzeczywistym do strumieniowego przesyłania danych](stream-analytics-power-bi-dashboard.md). W tym artykule przedstawiono sposób wysyłania TelCo dane wyjściowe zadania Stream Analytics z usługą Power BI w czasie rzeczywistym wizualizacji i analizy.

Aby uzyskać więcej informacji na temat usługi Stream Analytics ogólnie rzecz biorąc, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)
