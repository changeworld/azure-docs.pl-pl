---
title: 'Samouczek: tworzenie zadania usługi Stream Analytics i zarządzanie nim przy użyciu witryny Azure Portal | Microsoft Docs'
description: Ten samouczek zawiera kompleksowy opis sposobu używania usługi Azure Stream Analytics do analizowania fałszywych połączeń w strumieniu połączeń telefonicznych.
services: stream-analytics
author: sidramadoss
ms.author: sidram
manager: kfile
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/04/2018
ms.openlocfilehash: c29131720de8d6016d134fe7c0118fc3db9e22be
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985645"
---
# <a name="create-a-stream-analytics-job-to-analyze-phone-call-data-and-visualize-results-in-a-power-bi-dashboard"></a>Tworzenie zadania usługi Stream Analytics na potrzeby analizowania danych połączeń telefonicznych i wizualizacji wyników na pulpicie nawigacyjnym usługi Power BI
 
Ten samouczek przedstawia sposób użycia usługi Azure Stream Analytics do analizowania przykładowego połączenia telefonicznego generowanego przez aplikację klienta. Dane połączeń telefonicznych wygenerowane przez aplikację klienta zawierają pewne fałszywe połączenia i zdefiniujemy zadanie usługi Stream Analytics służące do filtrowania takich połączeń.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Generowanie przykładowych danych połączeń telefonicznych i wysyłanie ich do usługi Azure Event Hubs  
> * Tworzenie zadania usługi Stream Analytics   
> * Konfigurowanie danych wejściowych i wyjściowych zadania  
> * Definiowanie zapytania w celu filtrowania fałszywych połączeń  
> * Testowanie i uruchamianie zadania  
> * Wizualizowanie wyników w usłudze Power BI 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/).  
* Zaloguj się do witryny [Azure Portal](https://portal.azure.com/).  
* Pobierz aplikację generatora zdarzeń połączeń telefonicznych [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z Centrum pobierania Microsoft. Ewentualnie uzyskaj kod źródłowy z witryny [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).  

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń usługi Azure Event Hub 

Zanim usługa Stream Analytics będzie mogła przeanalizować strumień danych fałszywych połączeń, należy wysłać dane do platformy Azure. W tym samouczku dane zostaną wysłane do platformy Azure przy użyciu usługi [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs). Na potrzeby tego samouczka utworzysz centrum zdarzeń i użyjesz aplikacji generatora zdarzeń w celu wysłania danych połączeń do tego centrum zdarzeń. Wykonaj następujące kroki, aby utworzyć centrum zdarzeń:

1. Zaloguj się w witrynie Azure Portal.  
2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Event Hubs**.  

   ![Znajdowanie centrum zdarzeń](media/stream-analytics-manage-job/find-eh.png)
3. Wypełnij okienko **Tworzenie przestrzeni nazw** następującymi wartościami:  

   |**Ustawienie**  |**Sugerowana wartość** |**Opis**  |
   |---------|---------|---------|
   |Name (Nazwa)     | myEventHubNS        |  Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.       |
   |Subskrypcja     |   \<Twoja subskrypcja\>      |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć centrum zdarzeń.      |
   |Grupa zasobów     |   MyASADemoRG      |  Wybierz pozycję **Utwórz nową** i wprowadź nazwę nowej grupy zasobów dla swojego konta.       |
   |Lokalizacja     |   Zachodnie stany USA 2      |    Lokalizacja, w której można wdrożyć przestrzeń nazw centrum zdarzeń.     |

4. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Utwórz**.  

   ![Tworzenie przestrzeni nazw centrum zdarzeń](media/stream-analytics-manage-job/create-ehns.png)

5. Po zakończeniu wdrażania przestrzeni nazw wybierz pozycję **Wszystkie zasoby**, znajdź zasób „myEventHubNS” na liście zasobów platformy Azure i wybierz go, aby go otworzyć.  
6. Następnie wybierz pozycję **+ Centrum zdarzeń** > **Nazwa** i podaj nazwę centrum zdarzeń „MyEventHub”. Możesz użyć innej nazwy. W pozostałych ustawieniach użyj opcji domyślnych, następnie wybierz przycisk **Utwórz** i poczekaj na pomyślne ukończenie wdrożenia.

   ![Tworzenie centrum zdarzeń](media/stream-analytics-manage-job/create-eh.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udzielanie dostępu do centrum zdarzeń i pobieranie parametrów połączenia

Aby aplikacja mogła wysyłać dane do usługi Azure Event Hubs, centrum zdarzeń musi mieć zasady, które zezwalają na odpowiedni dostęp. Zasady dostępu generują parametry połączenia, które zawierają informacje o autoryzacji.

1. Przejdź do **usługi Event Hubs** utworzonej w poprzednim kroku, czyli „MyEventHub”, wybierz pozycję **Zasady dostępu współdzielonego** w okienku centrum zdarzeń, a następnie wybierz pozycję **+ Dodaj**.  
2. W polu Nazwa zasad ustaw wartość **Mypolicy**, wybierz pozycję **Zarządzaj** i wybierz pozycję **Utwórz**.  

   ![Tworzenie zasad dostępu współdzielonego centrum zdarzeń](media/stream-analytics-manage-job/create-ehpolicy.png)

3. Po wdrożeniu zasad wybierz opcję, aby otworzyć zasady, znajdź pozycję **Parametry połączenia — klucz podstawowy** i wybierz przycisk **Kopiuj** obok parametrów połączenia.  
4. Wklej parametry połączenia do edytora tekstu. Te parametry połączenia będą potrzebne w następnej sekcji.  

   Parametry połączenia wyglądają następująco:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>` 

   Zauważ, że parametry połączenia zawierają wiele par klucz-wartość oddzielonych średnikami: Endpoint, SharedAccessKeyName, SharedAccessKey i EntityPath.  

5. Usuń parę EntityPath z parametrów połączenia (nie zapomnij usunąć poprzedzającego ją średnika).

## <a name="start-the-event-generator-application"></a>Uruchamianie aplikacji generatora zdarzeń

Przed uruchomieniem aplikacji TelcoGenerator należy ją skonfigurować do przesyłania danych do usługi Azure Event Hubs utworzonej wcześniej.

1. Wyodrębnij zawartość pliku [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).  
2. Otwórz plik `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` w wybranym edytorze tekstów (istnieje więcej niż jeden plik config, dlatego upewnij się, że otwierasz właściwy).  

3. Zaktualizuj element <appSettings> w pliku config przy użyciu następujących szczegółów:

   * Ustaw wartość klucza EventHubName na wartość EntityPath w parametrach połączenia.  
   * Ustaw wartość klucza Microsoft.ServiceBus.ConnectionString na wartość parametrów połączenia bez wartości EntityPath, czyli na wartość uzyskaną po wykonaniu kroku 5 w poprzedniej sekcji.

4. Zapisz plik.  
5. Następnie otwórz okno polecenia, przejdź do folderu, w którym znajduje się wyodrębniona aplikacja TelcoGenerator, i wprowadź następujące polecenie:

   ```
   telcodatagen.exe 1000 0.2 2
   ```

   To polecenie przyjmuje następujące parametry:
   * **Liczba rekordów danych połączeń na godzinę**.  
   * **Procentowe prawdopodobieństwo oszustwa** — czyli jak często aplikacja powinna symulować fałszywe połączenia. Wartość 0,2 oznacza, że około 20% rekordów połączeń będzie wyglądać na fałszywe.  
   * **Czas trwania w godzinach** — liczba godzin, przez jaką aplikacja ma być uruchomiona. W dowolnym momencie można również zatrzymać aplikację poprzez zakończenie procesu (Ctrl+C) w wierszu polecenia.

   Po kilku sekundach aplikacja rozpocznie wyświetlanie rekordów połączeń telefonicznych na ekranie w miarę wysyłania ich do centrum zdarzeń. Dane połączenia telefonicznego zawierają następujące pola:

   |**Rekord**  |**Definicja**  |
   |---------|---------|
   |CallrecTime    |  Znacznik czasu godziny rozpoczęcia połączenia.       |
   |SwitchNum     |  Centrala telefoniczna używana do wykonania połączenia. W tym przykładzie centrale są ciągami reprezentującymi kraj pochodzenia (USA, Chiny, Zjednoczone Królestwo, Niemcy lub Australia).       |
   |CallingNum     |  Numer telefonu dzwoniącego.       |
   |CallingIMSI     |  Numer IMSI (International Mobile Subscriber Identity). Unikatowy identyfikator dzwoniącego.       |
   |CalledNum     |   Numer telefonu odbiorcy połączenia.      |
   |CalledIMSI     |  Numer IMSI (International Mobile Subscriber Identity). Unikatowy identyfikator odbiorcy połączenia.       |

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics 

Teraz, gdy masz strumień zdarzeń połączeń, możesz utworzyć zadanie usługi Stream Analytics, które odczytuje dane z centrum zdarzeń.

1. Aby utworzyć zadanie usługi Stream Analytics, przejdź do witryny Azure Portal  

2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Zadanie usługi Stream Analytics**.  

3. Wypełnij okienko **Zadanie usługi Stream Analytics** przy użyciu następujących wartości:  

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Nazwa zadania     |  ASATutorial       |   Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.      |
   |Subskrypcja    |  \<Twoja subskrypcja\>   |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć zadanie.       |
   |Grupa zasobów   |   MyASADemoRG      |   Wybierz pozycję **Użyj istniejącej**, a następnie wprowadź nazwę nowej grupy zasobów dla swojego konta.      |
   |Lokalizacja   |    Zachodnie stany USA 2     |      Lokalizacja, w której można wdrożyć zadanie. Zaleca się umieszczenie zadania i centrum zdarzeń w tym samym regionie, aby uzyskać najlepszą wydajność i nie płacić za transfer danych między regionami.      |
   |Środowisko hostingu    | Chmura        |     Zadania usługi Stream Analytics można wdrożyć w chmurze lub na urządzeniu brzegowym. Wartość Chmura umożliwia wdrożenie w usłudze Azure Cloud, a Urządzenie brzegowe umożliwia wdrożenie na urządzeniu usługi IoT Edge.    |
   |Jednostki przesyłania strumieniowego     |    1       |      Jednostki przesyłania strumieniowego reprezentują zasoby obliczeniowe, które są wymagane do wykonania zadania. Domyślnie to ustawienie ma wartość 1. Aby dowiedzieć się więcej na temat skalowania jednostek przesyłania strumieniowego, zobacz artykuł [Understanding and adjusting streaming units (Opis i dostosowywanie jednostek przesyłania strumieniowego)](stream-analytics-streaming-unit-consumption.md).      |

   ![Tworzenie zadania](media/stream-analytics-manage-job/create-a-job.png)   

4. W pozostałych ustawieniach użyj opcji domyślnych, a następnie wybierz przycisk **Utwórz** i poczekaj na pomyślne ukończenie wdrożenia.

## <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

Następnym krokiem jest określenie źródła danych wejściowych zadania, z którego zadanie ma odczytywać dane. W tym samouczku jako źródła danych wejściowych użyjemy centrum zdarzeń utworzonego w poprzedniej sekcji. Wykonaj następujące kroki, aby skonfigurować dane wejściowe zadania:

1. W witrynie Azure Portal otwórz okienko **Wszystkie zasoby** i znajdź zadanie usługi Stream Analytics o nazwie ASATutorial.  

2. W sekcji **Topologia zadania** okienka zadania usługi Stream Analytics wybierz opcję **Dane wejściowe**.  

3. Wybierz pozycję **+Dodaj wejście strumienia** (wejście odwołania dotyczy statycznych danych wyszukiwania, które nie będą używane w tym samouczku), **Centrum zdarzeń**, a następnie wypełnij okienko przy użyciu następujących wartości:  

   |**Ustawienie**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias danych wejściowych     |  CallStream       |  Podaj przyjazną nazwę identyfikującą dane wejściowe. Alias danych wejściowych może zawierać tylko znaki alfanumeryczne, łączniki i podkreślenia oraz musi składać się z od 3 do 63 znaków.       |
   |Subskrypcja    |   \<Twoja subskrypcja\>      |   Wybierz subskrypcję platformy Azure, w której zostało utworzone centrum zdarzeń. Centrum zdarzeń może znajdować się w tej samej subskrypcji co zadanie usługi Stream Analytics lub w innej.       |
   |Przestrzeń nazw centrum zdarzeń    |  MyEventHubNS       |  Wybierz przestrzeń nazw centrum zdarzeń utworzoną w poprzedniej sekcji. Wszystkie przestrzenie nazw centrum zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa centrum zdarzeń    |   MyEventHub      |  Wybierz centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie centra zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa zasad centrum zdarzeń   |  Mypolicy       |  Wybierz zasady dostępu współdzielonego centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie zasady centrów zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |

   ![Konfigurowanie danych wejściowych](media/stream-analytics-manage-job/configure-input.png) 

4. Użyj opcji domyślnych w pozostałych ustawieniach, kliknij przycisk **Zapisz** i poczekaj na pomyślne ukończenie wdrożenia.

## <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania 

Ostatnim krokiem jest określenie ujścia danych wyjściowych zadania, w którym może ono zapisać przekształcone dane. W tym samouczku wyniki zostaną wyprowadzone do usługi Power BI i zostanie zwizualizowana data. Uruchom następujące kroki, aby skonfigurować dane wyjściowe zadania:

1. W witrynie Azure Portal otwórz okienko **Wszystkie zasoby** i zadanie usługi Stream Analytics o nazwie ASATutorial.  

2. W sekcji **Topologia zadania** okienka zadania usługi Stream Analytics wybierz opcję **Dane wyjściowe**.  

3. Wybierz pozycję **+ Dodaj** > **Power BI** i wypełnij formularz przy użyciu następujących szczegółów (możesz podać przyjazną nazwę identyfikującą alias danych wyjściowych, nazwę zestawu danych i nazwę tabeli, jak pokazano w tabeli), a następnie wybierz przycisk **Autoryzuj**:  

   |**Ustawienie**  |**Sugerowana wartość**  |
   |---------|---------|---------|
   |Alias danych wyjściowych  |  MyPBIoutput  |
   |Nazwa zestawu danych  |   ASAdataset  | 
   |Nazwa tabeli |  ASATable  | 

   ![Konfigurowanie danych wyjściowych](media/stream-analytics-manage-job/configure-output.png)  

4. Po wybraniu pozycji **Autoryzuj** zostanie otwarte okno podręczne i zostanie wyświetlona prośba o podanie poświadczeń w celu uwierzytelnienia na koncie usługi Power BI. Kiedy autoryzacja zakończy się pomyślnie, **zapisz** ustawienia. 

## <a name="define-a-query-to-analyze-input-data"></a>Definiowanie zapytania w celu analizowania danych wejściowych

Po skonfigurowaniu zadania usługi Stream Analytics do odczytu przychodzącego strumienia danych następnym krokiem jest utworzenie przekształcenia, które analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia należy zdefiniować przy użyciu [języka zapytań usługi Stream Analytics](https://msdn.microsoft.com/library/dn834998.aspx). W tym samouczku zostanie zdefiniowane zapytanie, które wykrywa fałszywe połączenia w danych telefonicznych. 

W tym przykładzie uznajemy, że fałszywymi połączeniami są te, które pochodzą od tego samego użytkownika, ale z różnych lokalizacji, a czas między dwoma połączeniami wynosi pięć sekund. Na przykład ten sam użytkownik nie może rzeczywiście wykonywać w tym samym czasie połączeń ze Stanów Zjednoczonych i Australii. Aby zdefiniować zapytanie przekształcenia dla zadania usługi Stream Analytics, uruchom następujące kroki:

1. W witrynie Azure Portal otwórz okienko **Wszystkie zasoby** i otwórz zadanie usługi Stream Analytics **ASATutorial** utworzone wcześniej.  

2. W sekcji **Topologia zadania** okienka zadania usługi Stream Analytics wybierz opcję **Zapytanie**. Okno podręczne zawiera wejścia i wyjścia skonfigurowane dla zadania i umożliwia utworzenie zapytania w celu przekształcania strumienia wejściowego.  

3. Następnie zastąp istniejące zapytanie w edytorze poniższymi danymi. To zapytanie wykonuje samosprzężenie danych połączeń z 5-sekundowego interwału:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Aby sprawdzić fałszywe połączenia, należy utworzyć samosprzężenie danych strumieniowych na podstawie wartości `CallRecTime`. Następnie można wyszukać rekordy połączeń, w których wartość `CallingIMSI` (numer źródłowy) jest taka sama, ale wartość `SwitchNum` (kraj źródłowy) różni się. W przypadku używania operacji JOIN na danych przesyłanych strumieniowo sprzężenie musi udostępniać pewne ograniczenia określające maksymalną odległość czasową między dwoma pasującymi wierszami. Ponieważ dane przesyłane strumieniowo są nieskończone, granice czasowe dla relacji są określone w klauzuli ON sprzężenia, przy użyciu funkcji [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics). 

   To zapytanie jest takie samo jak normalne sprzężenie SQL, z wyjątkiem funkcji DATEDIFF. Funkcja DATEDIFF używana w tym zapytaniu jest specyficzna dla usługi Stream Analytics i musi znajdować się w klauzuli `ON...BETWEEN`.  

4. **Zapisz** zapytanie.  

   ![Definiowanie zapytania](media/stream-analytics-manage-job/define-query.png) 

## <a name="test-your-query"></a>Testowanie zapytania

Zapytanie można przetestować z poziomu edytora zapytań. Aby przetestować zapytanie, potrzebujesz przykładowych danych. W tym przewodniku wyodrębnimy przykładowe dane ze strumienia połączeń telefonicznych, który przychodzi do centrum zdarzeń. Uruchom następujące kroki, aby przetestować zapytanie:

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona i tworzy rekordy połączeń telefonicznych.  

2. W okienku **Zapytanie** wybierz kropki obok wejścia CallStream, a następnie wybierz pozycję **Przykładowe dane z danych wejściowych**. Spowoduje to otwarcie okienka, w którym możesz określić ilość przykładowych danych do odczytania ze strumienia wejściowego.  

   ![Przykładowe dane wejściowe](media/stream-analytics-manage-job/sample-input-data.png)  

3. Ustaw opcję **Minuty** na wartość 3 i wybierz przycisk **OK**. Ze strumienia wejściowego zostanie pobrana próbka danych odpowiadająca trzem minutom. Kiedy przykładowe dane będą gotowe, zostanie wyświetlone powiadomienie. Stan próbkowania można zobaczyć na pasku powiadomień. 

   Przykładowe dane są przechowywane tymczasowo i są dostępne, kiedy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, przykładowe dane zostaną usunięte i konieczne będzie utworzenie nowego zestawu przykładowych danych. Alternatywnie możesz pobrać plik JSON zawierający przykładowe dane z usługi [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a następnie przekazać ten plik JSON do użycia jako przykładowe dane dla wejścia CallStream.  

4. Wybierz pozycję **Test**, aby przetestować zapytanie. Powinny zostać wyświetlone dane wyjściowe, tak jak pokazano na poniższym zrzucie ekranu:  

   ![Dane wyjściowe testu](media/stream-analytics-manage-job/test-output.png)

## <a name="start-the-job-and-visualize-output"></a>Uruchamianie zadania i wizualizacja danych wyjściowych

1. Aby uruchomić zadanie, przejdź do okienka **Przegląd** zadania, a następnie wybierz pozycję **Uruchom**.  

2. Wybierz wartość **Teraz** jako godzinę rozpoczęcia generowania danych wyjściowych zadania, a następnie wybierz pozycję **Uruchom**. Zadanie zostanie uruchomione w ciągu kilku minut, a jego stan będzie wyświetlany na pasku powiadomień.  

3. Po pomyślnym zakończeniu zadania przejdź do witryny [Powerbi.com](https://powerbi.com/) i zaloguj się przy użyciu konta służbowego. Jeśli zapytanie zadania usługi Stream Analytics zwraca dane wyjściowe, zobaczysz, że zestaw danych jest już utworzony. Przejdź na kartę **Zestawy danych**, na której możesz wyświetlić zestaw danych o nazwie „ASAdataset”.  

4. W obszarze roboczym wybierz pozycję **+Utwórz**. Utwórz nowy pulpit nawigacyjny i nadaj mu nazwę Fraudulent Calls. Dodasz dwa kafelki do tego pulpitu nawigacyjnego — jeden z nich służy do wyświetlania liczby fałszywych połączeń w podanym wystąpieniu, a drugi wyświetla wizualizację w postaci wykresu liniowego.  

5. W górnej części okna wybierz pozycję **Dodaj kafelek**, a następnie wybierz pozycję **Niestandardowe dane przesyłane strumieniowo** i przycisk Dalej. Wybierz pozycję **ASAdataset**, jako Typ wizualizacji wybierz wartość **Karta**, a opcję Pola ustaw na **fraudulentcalls**. Wybierz przycisk **Dalej**, wprowadź nazwę kafelka i wybierz pozycję **Zastosuj**.  

   ![Tworzenie kafelków](media/stream-analytics-manage-job/create-tiles.png)

6. Wykonaj ponownie krok 4, wybierając następujące opcje:
   * Gdy pojawi się opcja Typ wizualizacji, wybierz wartość Wykres liniowy.  
   * Dodaj oś i wybierz pozycję **windowend**.  
   * Dodaj wartość i wybierz pozycję **fraudulentcalls**.  
   * W ustawieniu **Okno czasowe do wyświetlenia** wybierz ostatnie 10 minut.  

7. Po dodaniu obu kafelków pulpit nawigacyjny wygląda jak na poniższym zrzucie ekranu. Można zauważyć, że jeśli aplikacja nadawcy centrum zdarzeń i aplikacja usługi Stream Analytics są uruchomione, pulpit nawigacyjny usługi Power BI jest okresowo aktualizowany po nadejściu nowych danych.  

   ![Wyniki usługi Power BI](media/stream-analytics-manage-job/power-bi-results.png)

## <a name="embedding-your-powerbi-dashboard-in-a-web-application"></a>Osadzanie pulpitu nawigacyjnego usługi Power BI w aplikacji internetowej

W tej części samouczka użyjesz przykładowej aplikacji internetowej [ASP.NET](https://asp.net/) utworzonej przez zespół usługi Power BI, aby osadzić pulpit nawigacyjny. Aby uzyskać więcej informacji na temat osadzania pulpitów nawigacyjnych, zobacz artykuł [Osadzanie za pomocą usługi Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

W tym samouczku wykonamy kroki dla aplikacji, której dane należą do użytkownika. Aby skonfigurować aplikację, przejdź do repozytorium GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) i postępuj zgodnie z instrukcjami w sekcji **User Owns Data (Dane należą do użytkownika)** — użyj adresów URL przekierowania i strony głównej z podsekcji **integrate-dashboard-web-app**. Ponieważ korzystamy z przykładu pulpitu nawigacyjnego, użyj przykładowego kodu integrate-dashboard-web-app znajdującego się w [repozytorium GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
Po uruchomieniu aplikacji w przeglądarce wykonaj następujące kroki, aby osadzić utworzony wcześniej pulpit nawigacyjny na stronie internetowej:

1. Wybierz opcję **Sign in to Power BI (Zaloguj się do usługi Power BI)**, aby przyznać aplikacji dostęp do pulpitów nawigacyjnych na Twoim koncie usługi Power BI.  

2. Wybierz przycisk **Get Dashboards (Pobierz pulpity nawigacyjne)**, aby wyświetlić pulpity nawigacyjne Twojego konta w tabeli. Znajdź nazwę pulpitu nawigacyjnego utworzonego wcześniej (powerbi-embedded-dashboard) i skopiuj odpowiedni element **EmbedUrl**.  

3. Na koniec wklej wartość **EmbedUrl** w odpowiednim polu tekstowym i wybierz pozycję **Embed Dashboard (Osadź pulpit nawigacyjny)**. Możesz teraz wyświetlić ten sam pulpit nawigacyjny osadzony w aplikacji internetowej.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono proste zadanie usługi Stream Analytics, przeanalizowano dane przychodzące i przedstawiono wyniki na pulpicie nawigacyjnym usługi Power BI. Aby dowiedzieć się więcej na temat zadań usługi Stream Analytics, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Uruchamianie funkcji Azure Functions w ramach zadań usługi Stream Analytics](stream-analytics-with-azure-functions.md)
