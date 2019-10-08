---
title: 'Samouczek: Tworzenie zadania Stream Analytics i zarządzanie nim za pomocą Azure Portal'
description: Ten samouczek zawiera kompleksową prezentację, jak używać Azure Stream Analytics do analizowania fałszywych wywołań w strumieniu połączeń telefonicznych.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: 540cd0e136dc602848c34edbd3914d69ca725758
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72000554"
---
# <a name="analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Analizowanie danych połączeń telefonicznych za pomocą Stream Analytics i wizualizacji wyników na pulpicie nawigacyjnym Power BI

Ten samouczek uczy się, jak analizować dane połączeń telefonicznych przy użyciu Azure Stream Analytics. Dane połączenia telefonicznego generowane przez aplikację kliencką zawierają pewne fałszywe wywołania, które będą filtrowane przez zadanie Stream Analytics.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Generowanie przykładowych danych połączeń telefonicznych i wysyłanie ich do usługi Azure Event Hubs
> * Tworzenie zadania Stream Analytics
> * Konfigurowanie danych wejściowych i wyjściowych zadania
> * Zdefiniuj zapytanie w celu filtrowania fałszywych wywołań
> * Testowanie i uruchamianie zadania
> * Wizualizuj wyniki w Power BI

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wykonaj następujące czynności:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/).
* Zaloguj się do [Azure Portal](https://portal.azure.com/).
* Pobierz aplikację generatora zdarzeń rozmowy telefonicznej [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) z centrum pobierania Microsoft lub Pobierz kod źródłowy z usługi [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Będziesz potrzebować konta Power BI.

## <a name="create-an-azure-event-hub"></a>Tworzenie centrum zdarzeń platformy Azure

Zanim Stream Analytics będzie mogła analizować fałszywy strumień danych wywołań, dane muszą być wysyłane do platformy Azure. W tym samouczku dane będą wysyłane do platformy Azure przy użyciu [usługi azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Wykonaj następujące kroki, aby utworzyć centrum zdarzeń i wysłać dane wywołania do tego centrum zdarzeń:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Event Hubs**.

   ![Tworzenie centrum zdarzeń platformy Azure w portalu](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Wypełnij okienko **Tworzenie przestrzeni nazw** następującymi wartościami:

   |**Konfigurowania**  |**Sugerowana wartość** |**Opis**  |
   |---------|---------|---------|
   |Nazwa     | myEventHubsNS        |  Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.       |
   |Ramach     |   @no__t — subskrypcja 0Your @ no__t-1      |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć centrum zdarzeń.      |
   |Grupa zasobów     |   MyASADemoRG      |  Wybierz pozycję **Utwórz nową** i wprowadź nową nazwę grupy zasobów dla swojego konta.       |
   |Lokalizacja     |   Stany USA 2 zachodni      |    Lokalizacja, w której można wdrożyć przestrzeń nazw centrum zdarzeń.     |

4. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Utwórz**.

   ![Utwórz przestrzeń nazw centrum zdarzeń w Azure Portal](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. Po zakończeniu wdrażania obszaru nazw przejdź do pozycji **wszystkie zasoby** i Znajdź *myEventHubsNS* na liście zasobów platformy Azure. Wybierz pozycję *myEventHubsNS* , aby ją otworzyć.
6. Następnie wybierz pozycję **+ centrum zdarzeń** i wprowadź **nazwę** jako *MyEventHub* lub inną wybraną nazwę. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Utwórz**. Następnie poczekaj, aż wdrożenie zakończyło się pomyślnie.

   ![Konfiguracja centrum zdarzeń w Azure Portal](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Udzielanie dostępu do centrum zdarzeń i pobieranie parametrów połączenia

Aby aplikacja mogła wysyłać dane do usługi Azure Event Hubs, centrum zdarzeń musi mieć zasady zezwalające na odpowiedni dostęp. Zasady dostępu tworzą parametry połączenia, które zawierają informacje o autoryzacji.

1. Przejdź do centrum zdarzeń utworzonego w poprzednim kroku, MyEventHub *. Wybierz pozycję **zasady dostępu współdzielonego** w obszarze **Ustawienia**, a następnie wybierz pozycję **+ Dodaj**.

2. Nazwij zasady moje **zasady** i upewnij się, że opcja **Zarządzaj** jest zaznaczona. Następnie wybierz pozycję **Utwórz**.

   ![Tworzenie zasad dostępu współdzielonego centrum zdarzeń](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. Po utworzeniu zasad wybierz opcję otwierania zasad i Znajdź **Parametry połączenia — klucz podstawowy**. Wybierz przycisk niebieski **kopiowania** obok parametrów połączenia.

   ![Zapisz parametry połączenia zasad dostępu współdzielonego](media/stream-analytics-manage-job/save-connection-string.png)

4. Wklej parametry połączenia do edytora tekstu. Te parametry połączenia są potrzebne w następnej sekcji.

   Parametry połączenia wyglądają następująco:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Zwróć uwagę, że parametry połączenia zawierają wiele par klucz-wartość oddzielonych średnikami: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey**i **EntityPath**.

## <a name="start-the-event-generator-application"></a>Uruchamianie aplikacji generatora zdarzeń

Przed uruchomieniem aplikacji TelcoGenerator należy ją skonfigurować tak, aby dane były wysyłane do utworzonej wcześniej Event Hubs platformy Azure.

1. Wyodrębnij zawartość pliku [TelcoGenerator. zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) .
2. Otwórz plik `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` w wybranym edytorze tekstu (istnieje więcej niż jeden plik config. Upewnij się, że otwarto prawym przyciskiem myszy).

3. Zaktualizuj element `<appSettings>` w pliku konfiguracji, podając następujące informacje:

   * Ustaw wartość klucza *EventHubName* na wartość EntityPath w parametrach połączenia.
   * Ustaw wartość klucza *Microsoft. ServiceBus. ConnectionString* na parametry połączenia bez wartości EntityPath (Pamiętaj, aby usunąć średnik, który poprzedza go).

4. Zapisz plik.
5. Następnie otwórz okno polecenia i przejdź do folderu, w którym została odspakowana aplikacja TelcoGenerator. Następnie wprowadź następujące polecenie:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   To polecenie przyjmuje następujące parametry:
   * Liczba rekordów danych wywołań na godzinę.
   * Procent prawdopodobieństwa oszustwa, czyli częstotliwość symulowania fałszywych wywołań przez aplikację. Wartość 0,2 oznacza, że około 20% rekordów wywołań będzie wyglądało na fałszywie.
   * Czas trwania w godzinach, czyli liczba godzin uruchomienia aplikacji. Możesz również zatrzymać aplikację w dowolnym momencie, kończąc proces (**Ctrl + C**) w wierszu polecenia.

   Po kilku sekundach aplikacja rozpocznie wyświetlanie rekordów połączeń telefonicznych na ekranie, gdy wyśle je do centrum zdarzeń. Dane połączenia telefonicznego zawierają następujące pola:

   |**Rekord**  |**Definicja**  |
   |---------|---------|
   |CallrecTime    |  Sygnatura czasowa czasu rozpoczęcia wywołania.       |
   |SwitchNum     |  Przełącznik telefonu używany do nawiązywania połączenia. W tym przykładzie przełączniki są ciągami reprezentującymi kraj/region pochodzenia (USA, Chiny, Zjednoczone Królestwo, Niemcy lub Australia).       |
   |CallingNum     |  Numer telefonu obiektu wywołującego.       |
   |CallingIMSI     |  Międzynarodowa tożsamość subskrybenta komórkowego (IMSI). Jest to unikatowy identyfikator obiektu wywołującego.       |
   |CalledNum     |   Numer telefonu odbiorcy rozmowy.      |
   |CalledIMSI     |  Międzynarodowa tożsamość subskrybenta komórkowego (IMSI). Jest to unikatowy identyfikator odbiorcy rozmowy.       |

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania Stream Analytics

Teraz, gdy masz strumień zdarzeń wywołań, możesz utworzyć zadanie Stream Analytics, które odczytuje dane z centrum zdarzeń.

1. Aby utworzyć zadanie Stream Analytics, przejdź do [Azure Portal](https://portal.azure.com/).

2. Wybierz pozycję **Utwórz zasób** > **Internet rzeczy** > **Stream Analytics zadanie**.

3. Wypełnij okienko **nowe zadanie Stream Analytics** przy użyciu następujących wartości:

   |**Konfigurowania**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Nazwa zadania     |  O nazwie asatutorial       |   Unikatowa nazwa identyfikująca przestrzeń nazw centrum zdarzeń.      |
   |Ramach    |  @no__t — subskrypcja 0Your @ no__t-1   |   Wybierz subskrypcję platformy Azure, w której chcesz utworzyć zadanie.       |
   |Grupa zasobów   |   MyASADemoRG      |   Wybierz pozycję **Użyj istniejącej** i wprowadź nową nazwę grupy zasobów dla Twojego konta.      |
   |Lokalizacja   |    Stany USA 2 zachodni     |      Lokalizacja, w której można wdrożyć zadanie. Zaleca się umieszczenie zadania i centrum zdarzeń w tym samym regionie w celu uzyskania najlepszej wydajności, aby nie zapłaciła za transfer danych między regionami.      |
   |Środowisko hostingu    | Chmura        |     Zadania Stream Analytics można wdrożyć w chmurze lub na urządzeniach brzegowych. Chmura umożliwia wdrażanie w chmurze platformy Azure, a usługa Edge umożliwia wdrażanie na urządzeniu IoT Edge.    |
   |Jednostki przesyłania strumieniowego     |    1       |      Jednostki przesyłania strumieniowego reprezentują zasoby obliczeniowe, które są wymagane do wykonania zadania. Domyślnie ta wartość jest równa 1. Aby dowiedzieć się więcej na temat skalowania jednostek przesyłania strumieniowego, zobacz artykuł [Opis i Dostosowywanie jednostek przesyłania strumieniowego](stream-analytics-streaming-unit-consumption.md) .      |

4. Użyj opcji domyślnych w pozostałych ustawieniach, wybierz pozycję **Utwórz**i poczekaj na pomyślne wdrożenie.

   ![Tworzenie zadania Azure Stream Analytics](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurowanie danych wejściowych zadania

Następnym krokiem jest zdefiniowanie źródła danych wejściowych dla zadania w celu odczytania danych przy użyciu centrum zdarzeń utworzonego w poprzedniej sekcji.

1. W Azure Portal Otwórz okienko **wszystkie zasoby** i znajdź zadanie Stream Analytics *o nazwie asatutorial* .

2. W sekcji **topologia zadania** okienka zadania Stream Analytics wybierz opcję **dane wejściowe** .

3. Wybierz pozycję **+ Dodaj dane wejściowe strumienia** i **centrum zdarzeń**. Wypełnij okienko następującymi wartościami:

   |**Konfigurowania**  |**Sugerowana wartość**  |**Opis**  |
   |---------|---------|---------|
   |Alias wejściowy     |  Wejścia callstream       |  Podaj przyjazną nazwę, aby zidentyfikować dane wejściowe. Alias wejściowy może zawierać tylko znaki alfanumeryczne, łączniki i podkreślenia oraz musi składać się z od 3-63 znaków.       |
   |Ramach    |   @no__t — subskrypcja 0Your @ no__t-1      |   Wybierz subskrypcję platformy Azure, w której utworzono centrum zdarzeń. Centrum zdarzeń może być w tej samej lub innej subskrypcji co zadanie Stream Analytics.       |
   |Przestrzeń nazw centrum zdarzeń    |  myEventHubsNS       |  Wybierz przestrzeń nazw centrum zdarzeń utworzoną w poprzedniej sekcji. Wszystkie przestrzenie nazw centrum zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa centrum zdarzeń    |   MyEventHub      |  Wybierz centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie Centra zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |
   |Nazwa zasad centrum zdarzeń   |  Moje zasady       |  Wybierz zasady dostępu współdzielonego centrum zdarzeń utworzone w poprzedniej sekcji. Wszystkie zasady centrów zdarzeń dostępne w Twojej bieżącej subskrypcji są wyświetlane na liście rozwijanej.       |

4. Użyj opcji domyślnych w pozostałych ustawieniach i wybierz pozycję **Zapisz**.

   ![Skonfiguruj dane wejściowe Azure Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Konfigurowanie danych wyjściowych zadania

Ostatnim krokiem jest zdefiniowanie ujścia danych wyjściowych dla zadania, w którym można zapisać przekształcone dane. W tym samouczku dane wyjściowe są wyprowadzane i wizualizowane za pomocą Power BI.

1. W okienku Azure Portal Otwórz **wszystkie zasoby** i zadania Stream Analytics *o nazwie asatutorial* .

2. W sekcji **topologia zadania** okienka zadania Stream Analytics wybierz opcję dane **wyjściowe** .

3. Wybierz pozycję **+ dodaj** > **Power BI**. Następnie wypełnij formularz poniższymi szczegółami i wybierz pozycję **Autoryzuj**:

   |**Konfigurowania**  |**Sugerowana wartość**  |
   |---------|---------|
   |Alias wyjściowy  |  MyPBIoutput  |
   |Nazwa zestawu danych  |   ASAdataset  |
   |Nazwa tabeli |  ASATable  |

   ![Konfigurowanie danych wyjściowych Stream Analytics](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. Po wybraniu opcji **Autoryzuj**zostanie otwarte okno podręczne i zostanie wyświetlony monit o podanie poświadczeń w celu uwierzytelnienia na koncie Power BI. Po pomyślnym uwierzytelnieniu **Zapisz** ustawienia.

## <a name="define-a-query-to-analyze-input-data"></a>Definiowanie zapytania w celu przeanalizowania danych wejściowych

Następnym krokiem jest utworzenie transformacji, która analizuje dane w czasie rzeczywistym. Zapytanie przekształcenia można zdefiniować za pomocą [Stream Analytics języka zapytań](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Zapytanie użyte w tym samouczku wykrywa fałszywe wywołania z danych telefonu.

W tym przykładzie oszukańcze wywołania są wykonywane z tego samego użytkownika w ciągu pięciu sekund, ale w oddzielnych lokalizacjach. Na przykład ten sam użytkownik nie może legalnie wykonać wywołania z USA i Australii w tym samym czasie. Aby zdefiniować zapytanie transformacji dla zadania Stream Analytics:

1. W Azure Portal Otwórz okienko **wszystkie zasoby** i przejdź do utworzonego wcześniej zadania **o nazwie asatutorial** Stream Analytics.

2. W sekcji **topologia zadania** okienka zadania Stream Analytics wybierz opcję **zapytanie** . Okno zapytania zawiera listę wejść i wyjść, które są skonfigurowane dla zadania, i umożliwia utworzenie zapytania w celu przekształcenia strumienia wejściowego.

3. Zastąp istniejące zapytanie w edytorze następującym zapytaniem, które wykonuje własne sprzężenie w 5-sekundowym interwale danych wywołań:

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

   Aby sprawdzić fałszywe wywołania, możesz dołączyć do siebie dane przesyłane strumieniowo na podstawie wartości `CallRecTime`. Następnie można wyszukać rekordy wywołań, w których wartość `CallingIMSI` (numer źródłowy) jest taka sama, ale wartość `SwitchNum` (kraj/region pochodzenia) różni się. W przypadku używania operacji JOIN z danymi przesyłania strumieniowego, sprzężenie musi zawierać pewne ograniczenia dotyczące czasu, w którym można oddzielić pasujące wiersze. Ponieważ dane przesyłane strumieniowo są nieograniczone, granice czasu dla relacji są określane w klauzuli **on** sprzężenia przy użyciu funkcji [DateDiff](https://docs.microsoft.com/stream-analytics-query/datediff-azure-stream-analytics) .

   To zapytanie jest tak samo samo jak normalne sprzężenie SQL, z wyjątkiem funkcji **DateDiff** . Funkcja **DateDiff** używana w tym zapytaniu jest specyficzna dla Stream Analytics i musi znajdować się w klauzuli `ON...BETWEEN`.

4. **Zapisz** zapytanie.

   ![Definiowanie kwerendy Stream Analytics w portalu](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Testowanie zapytania

Można testować zapytanie z edytora zapytań za pomocą przykładowych danych. Wykonaj następujące kroki, aby przetestować zapytanie:

1. Upewnij się, że aplikacja TelcoGenerator jest uruchomiona i produkuje rekordy połączeń telefonicznych.

2. W okienku **zapytania** wybierz kropki obok danych wejściowych *wejścia callstream* , a następnie wybierz pozycję **dane przykładowe z danych wejściowych**.

3. Ustaw wartość **minut** na 3 i wybierz pozycję **OK**. Trzy minuty dane są następnie próbkowane ze strumienia wejściowego i otrzymasz powiadomienie, gdy przykładowe dane są gotowe. Stan próbkowania można wyświetlić na pasku powiadomień.

   Dane przykładowe są przechowywane tymczasowo i są dostępne, gdy okno zapytania jest otwarte. Jeśli zamkniesz okno zapytania, przykładowe dane zostaną odrzucone i będzie trzeba utworzyć nowy zestaw przykładowych danych, jeśli chcesz przetestować. Alternatywnie możesz użyć przykładowego pliku JSON danych z usługi [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json), a następnie przekazać ten plik JSON do użycia jako przykładowe dane dla danych wejściowych *wejścia callstream* .

   ![Wizualizacja przykładowych danych wejściowych dla Stream Analytics](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Wybierz **test** , aby przetestować zapytanie. Powinny być widoczne następujące wyniki:

   ![Dane wyjściowe z testu zapytania Stream Analytics](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Uruchamianie zadania i Wizualizacja danych wyjściowych

1. Aby rozpocząć zadanie, przejdź do okienka **Przegląd** zadania i wybierz pozycję **Uruchom**.

2. Wybierz pozycję **teraz** , aby rozpocząć pracę z danymi wyjściowymi zadania, a następnie wybierz pozycję **Uruchom**. Stan zadania można wyświetlić na pasku powiadomień.

3. Gdy zadanie zakończy się pomyślnie, przejdź do [Power BI](https://powerbi.com/) i zaloguj się przy użyciu konta służbowego. Jeśli wyniki zapytania zadania Stream Analytics są wyprowadzane, utworzony zestaw danych *ASAdataset* istnieje na karcie **zestawy** danych.

4. W obszarze roboczym Power BI wybierz pozycję **+ Utwórz** , aby utworzyć nowy pulpit nawigacyjny o nazwie *fałszywe wywołania*.

5. W górnej części okna wybierz pozycję **Dodaj kafelek**. Następnie wybierz pozycję **niestandardowe dane przesyłane strumieniowo** i **dalej**. Wybierz **ASAdataset** w obszarze **zestawy danych**. Wybierz **kartę** z listy rozwijanej **typ wizualizacji** i Dodaj **fałszywe wywołania** do **pól**. Wybierz pozycję **dalej** , aby wprowadzić nazwę kafelka, a następnie wybierz pozycję **Zastosuj** , aby utworzyć kafelek.

   ![Tworzenie kafelków pulpitu nawigacyjnego Power BI](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Wykonaj ponownie krok 5 z następującymi opcjami:
   * Po wybraniu typu wizualizacji wybierz pozycję wykres liniowy.
   * Dodaj oś i wybierz pozycję **windowend**.
   * Dodaj wartość i wybierz pozycję **fraudulentcalls**.
   * Aby **wyświetlić przedział czasu**, wybierz ostatnie 10 minut.

7. Pulpit nawigacyjny powinien wyglądać podobnie do poniższego przykładu po dodaniu obu kafelków. Zwróć uwagę, że jeśli aplikacja nadawcy centrum zdarzeń i aplikacja usługi Stream Analytics są uruchomione, pulpit nawigacyjny Power BI okresowo aktualizuje jako nowe dane.

   ![Wyświetl wyniki w Power BI pulpicie nawigacyjnym](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Osadzanie pulpitu nawigacyjnego Power BI w aplikacji sieci Web

W tej części samouczka użyjesz przykładowej aplikacji sieci Web [ASP.NET](https://asp.net/) utworzonej przez zespół Power BI, aby osadzić pulpit nawigacyjny. Aby uzyskać więcej informacji na temat osadzania pulpitów nawigacyjnych, zobacz artykuł [osadzanie w Power BI](https://docs.microsoft.com/power-bi/developer/embedding) artykule.

Aby skonfigurować aplikację, przejdź do repozytorium usługi [PowerBI — Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) w witrynie GitHub i postępuj zgodnie z instrukcjami w sekcji **Informacje o użytkowniku** . Użyj adresów URL przekierowania i strony głównej w podsekcji **Integruj-Web-App** . Ponieważ korzystamy z przykładu pulpitu nawigacyjnego, użyj kodu przykładowego **Integruj-Web-App** znajdującego się w [repozytorium GitHub](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-web-app).
Po uruchomieniu aplikacji w przeglądarce wykonaj następujące kroki, aby osadzić pulpit nawigacyjny utworzony wcześniej na stronie sieci Web:

1. Wybierz pozycję **Zaloguj się do Power BI**, co spowoduje przyznanie aplikacji dostępu do pulpitów nawigacyjnych na koncie Power BI.

2. Wybierz przycisk **Pobierz pulpity nawigacyjne** , aby wyświetlić pulpity nawigacyjne Twojego konta w tabeli. Znajdź nazwę utworzonego wcześniej pulpitu nawigacyjnego, usługi **PowerBI-Embedded-Dashboard**i skopiuj odpowiednie **EmbedUrl**.

3. Na koniec wklej **EmbedUrl** do odpowiedniego pola tekstowego i wybierz pozycję **Osadź pulpit nawigacyjny**. Teraz można wyświetlić ten sam pulpit nawigacyjny osadzony w aplikacji sieci Web.

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono proste zadanie Stream Analytics, przeanalizowane dane przychodzące i przedstawiono wyniki na pulpicie nawigacyjnym Power BI. Aby dowiedzieć się więcej o Stream Analytics zadaniach, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Uruchamianie Azure Functions w ramach Stream Analytics zadań](stream-analytics-with-azure-functions.md)
