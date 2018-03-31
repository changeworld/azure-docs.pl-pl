---
title: Tworzenie rozwiązania IoT przy użyciu usługi Stream Analytics | Dokumentacja firmy Microsoft
description: Samouczka wprowadzającego dla scenariusza budki rozwiązania IoT analiza strumienia
keywords: rozwiązania iot, funkcje okna
documentationcenter: ''
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/21/2018
ms.author: sngun
ms.openlocfilehash: b36833a9fe35f14eba6d9e397eb0958b716b313b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Tworzenie rozwiązania IoT przy użyciu usługi analiza strumienia

## <a name="introduction"></a>Wprowadzenie
Z tego samouczka dowiesz się, jak używać usługi Azure Stream Analytics Aby uzyskać wgląd w czasie rzeczywistym danych. Deweloperzy mogą łatwo łączyć strumienie danych, takich jak kliknij strumieni, dzienników i zdarzenia generowane przez urządzenie, rekordy historyczne lub dane referencyjne do uzyskania biznesowych. Jako usługa obliczeń strumienia w pełni zarządzana, w czasie rzeczywistym, która jest hostowana na platformie Microsoft Azure Azure Stream Analytics zapewnia odporność wbudowanych, małe opóźnienia i skalowalności na uruchomienie czasu w minutach.

Po ukończeniu tego samouczka, będą mieć możliwość:

* Zapoznaj się z portalem Azure Stream Analytics.
* Konfigurowanie i wdrażanie zadanie przesyłania strumieniowego.
* Zwrócone rzeczywistych problemów i ich rozwiązanie przy użyciu języka zapytań usługi Stream Analytics.
* Tworzenie strumienia rozwiązania dla klientów za pomocą usługi Stream Analytics bez obaw.
* Umożliwia monitorowanie i rejestrowanie środowisko rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne
Następujące wymagania wstępne do ukończenia tego samouczka jest potrzebne:

* Najnowszą wersję [programu Azure PowerShell](/powershell/azure/overview)
* Visual Studio 2017 r 2015 lub wolnych [Visual Studio Community](https://www.visualstudio.com/products/visual-studio-community-vs.aspx)
* [Subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/)
* Uprawnienia administracyjne na komputerze
* Pobierz kod źródłowy do analizy danych czujnika TollApp z [repozytorium GitHub analiza strumienia azure.](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp). To repozytorium zawiera przykładowych danych i zapytania, które będą używane w kolejnych sekcjach. 

## <a name="scenario-introduction-hello-toll"></a>Wprowadzenie do scenariusza: "tekst Hello, przez!"
Stacja przez jest zjawiskiem wspólnej. Wystąpią ich na wielu trasy szybkiego ruchu, mostki i tunele całym świecie. Każda stacja przez ma wiele kabiny przez. Ręczne kabiny można zatrzymać na rzecz przez opiekun. Na automatyczne kabiny czujnik u góry każdego stoisku skanuje karty RFID, który jest umieszczony szyby Twojego pojazdów przy przesuwaniu stoisku przez. Jest łatwy do wizualizacji przejście pojazdów przez te stacje przez jako strumień zdarzeń, w którym interesujące operacje mogą być wykonywane.

![Obraz samochodów na kabiny przez](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dane przychodzące
W tym samouczku współpracuje z dwóch strumieni danych. Zainstalowane w wejście i wyjście stacji przez czujniki utworzyć pierwszy strumienia. Drugi strumień jest statyczny wyszukiwania zestawu danych, który zawiera dane rejestracji pojazdów.

### <a name="entry-data-stream"></a>Wpis strumienia danych
Strumień danych wpis zawiera informacje o samochodów, wejście przez stacje.

| TollID | EntryTime | LicensePlate | Stan | Wprowadź | Model | VehicleType | VehicleWeight | Przez | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Poniżej przedstawiono krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| TollID |Identyfikator stoisku przez, który unikatowo identyfikuje stoisku przez |
| EntryTime |Data i godzina wprowadzenia pojazdów do stoisku przez w formacie UTC |
| LicensePlate |Liczba licencji płytkę mechanizm |
| Stan |Stan w Stanach Zjednoczonych |
| Wprowadź |Producent samochodów |
| Model |Liczba modeli samochodów |
| VehicleType |1 dla samochodów osobowych lub 2 dla pojazdów |
| WeightType |Vehicle wagi w tonach; 0 dla pojazdów pasażerów |
| Przez |Wartość przez USD |
| Tag |E-Tag na samochodów, który zautomatyzuje payment; puste, jeśli płatność została wykonana ręcznie |

### <a name="exit-data-stream"></a>Strumień danych zakończenia
Strumień danych zakończenia zawiera informacje o samochodów, pozostawiając przez stacji.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |CDE 1007 |

Poniżej przedstawiono krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| TollID |Identyfikator stoisku przez, który unikatowo identyfikuje stoisku przez |
| ExitTime |Data i godzina zakończenia mechanizm z stoisku przez w formacie UTC |
| LicensePlate |Liczba licencji płytkę mechanizm |

### <a name="commercial-vehicle-registration-data"></a>Dane rejestracji pojazdów użytkowych
W samouczku statyczna migawka bazy danych rejestracji pojazdów użytkowych.

| LicensePlate | RegistrationId | Wygasłe |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Poniżej przedstawiono krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| LicensePlate |Liczba licencji płytkę mechanizm |
| RegistrationId |Identyfikator rejestracji vehicle |
| Wygasłe |Stan rejestracji mechanizm: 0, jeśli rejestracji pojazdów jest aktywny, 1, jeśli wygaśnięcia rejestracji |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Konfigurowanie środowiska dla usługi Azure Stream Analytics
Do ukończenia tego samouczka, konieczna jest subskrypcja Microsoft Azure. Firma Microsoft oferuje bezpłatna wersja próbna usług Microsoft Azure.

Jeśli nie masz konta platformy Azure, możesz [żądania bezpłatną wersję próbną](http://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Aby utworzyć konto bezpłatnej wersji próbnej, należy urządzenia przenośnego, które mogą odbierać wiadomości SMS oraz ważnej karty kredytowej.
> 
> 

Należy postępować zgodnie z instrukcjami w sekcji "Wyczyść konta platformy Azure" na końcu tego artykułu tak, aby można było wprowadzać najlepsze wykorzystanie środków platformy Azure.

## <a name="provision-azure-resources-required-for-the-tutorial"></a>Zapewnij Azure zasoby wymagane do samouczka
Ten samouczek wymaga dwóch centra zdarzeń, aby otrzymać *wpis* i *zakończyć* strumieni danych. Baza danych SQL Azure generuje wyniki zadania usługi analiza strumienia. Usługa Azure Storage przechowuje dane odwołanie rejestracji pojazdów.

Skrypt Setup.ps1 w folderze TollApp w witrynie GitHub umożliwia utworzenie wszystkich wymaganych zasobów. W celu czasu firma Microsoft zaleca, uruchom go. Jeśli chcesz dowiedzieć się więcej o sposobie konfigurowania tych zasobów w portalu Azure, zapoznaj się dodatek "Konfigurowanie samouczek zasobów w portalu Azure".

Pobierz i Zapisz towarzyszące [TollApp](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TollApp/TollApp.zip) folderów i plików.

Otwórz **Microsoft Azure PowerShell** okna *jako administrator*. Jeśli nie masz jeszcze programu Azure PowerShell, postępuj zgodnie z instrukcjami [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) go zainstalować.

Ponieważ system Windows blokuje automatycznie .ps1, dll i plików .exe, należy ustawić zasady wykonywania przed uruchomieniem skryptu. Upewnij się, że okno programu Azure PowerShell jest uruchomione *jako administrator*. Uruchom **Set-ExecutionPolicy unrestricted**. Po wyświetleniu monitu wpisz **Y**.

![Zrzut ekranu przedstawiający "Set-ExecutionPolicy unrestricted" uruchomione w oknie programu Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image2.png)

Uruchom **Get-ExecutionPolicy** aby upewnić się, że polecenie pracy.

![Zrzut ekranu przedstawiający "Get-ExecutionPolicy" uruchomione w oknie programu Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image3.png)

Przejdź do katalogu, który ma skrypty i generator aplikacji.

![Zrzut ekranu przedstawiający "cd .\TollApp\TollApp" uruchomione w oknie programu Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image4.png)

Typ **.\\ Setup.ps1** Aby skonfigurować konta platformy Azure, tworzenie i konfigurowanie wszystkich wymaganych zasobów i rozpocząć generowanie zdarzeń. Skrypt losowo wybiera regionie można utworzyć zasoby. Aby jawnie określić region, można przekazać **-lokalizacji** parametru, jak w poniższym przykładzie:

**. \\Setup.ps1 — lokalizacja "Środkowe stany USA"**

![Zrzut ekranu przedstawiający Azure strony logowania](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image5.png)

Skrypt zostanie otwarty **logowania** strony platformy Microsoft Azure. Wprowadź poświadczenia konta.

> [!NOTE]
> Jeśli konto użytkownika ma dostęp do wielu subskrypcji, użytkownik jest proszony o wprowadzenie nazwy subskrypcji, który ma być używany dla tego samouczka.
> 
> 

Skrypt może potrwać kilka minut. Po jej zakończeniu, dane wyjściowe powinny wyglądać jak poniższy zrzut ekranu.

![Zrzut ekranu przedstawiający dane wyjściowe skryptu w oknie programu Azure PowerShell](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image6.PNG)

Widoczne będzie także inne okno, który jest podobny do następującego zrzutu ekranu. Ta aplikacja jest wysyłania zdarzeń do usługi Azure Event Hubs, co jest wymagane do uruchomienia w samouczku. Tak nie Zatrzymaj aplikację lub zamknąć to okno, aż do zakończenia tego samouczka.

![Zrzut ekranu przedstawiający "Wysyłanie zdarzeń centrum danych"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image7.png)

Można teraz wyświetlić zasoby w portalu Azure. Przejdź do <https://portal.azure.com>i zaloguj się przy użyciu poświadczeń konta. Należy pamiętać, obecnie niektórych funkcji używa klasycznego portalu. Te kroki będzie widoczna.

### <a name="azure-event-hubs"></a>Azure Event Hubs

W portalu Azure kliknij **więcej usług** w dolnej części okienka po lewej stronie zarządzania. Typ **usługi Event hubs** w wyświetlonym polu widać nowej przestrzeni nazw Centrum zdarzeń, która rozpoczyna się od **tolldata**. Ta namesapce jest utworzony przez skrypt Setup.ps1. Zobaczysz dwa elementy Hub zdarzenia o nazwie **wpis** i **zakończyć** utworzone w tej przestrzeni nazw.

### <a name="azure-storage-container"></a>Kontener magazynu Azure
W portalu Azure, przejdź do konta magazynu, powinny być widoczne konta magazynu, który rozpoczyna się od **tolldata**. Kliknij przycisk **tolldata** kontenera, aby zobaczyć przekazanego pliku JSON, który zawiera dane rejestracji pojazdów.

### <a name="azure-sql-database"></a>Azure SQL Database
1. Wróć do portalu Azure na pierwszej karcie, która została otwarta w przeglądarce. Kliknij przycisk **baz danych SQL** po lewej stronie portalu Azure, aby zobaczyć bazy danych SQL, która będzie używana w tym samouczku, a następnie kliknij przycisk **tolldatadb**.
   
    ![Zrzut ekranu przedstawiający utworzono bazę danych SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15.png)
2. Skopiuj nazwę serwera bez numeru portu (*servername*. database.windows.net, na przykład).
    ![Zrzut ekranu przedstawiający utworzonej bazy danych bazy danych SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image15a.png)

## <a name="connect-to-the-database-from-visual-studio"></a>Połączenie z bazą danych z programu Visual Studio
Dostęp do wyników zapytania w bazie danych wyjściowych za pomocą programu Visual Studio.

Połączenie z bazą danych SQL (docelowy) z programu Visual Studio:

1. Otwórz program Visual Studio, a następnie kliknij przycisk **narzędzia** > **Połącz z bazą danych**.
2. Jeśli pojawi się monit, kliknij przycisk **programu Microsoft SQL Server** jako źródła danych.
   
    ![Okno dialogowe Zmienianie źródła danych](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image16.png)
3. W **nazwy serwera** pól, Wklej skopiowany z portalu Azure w poprzedniej sekcji nazwę (to znaczy *servername*. database.windows.net).
4. Kliknij przycisk **używać uwierzytelniania programu SQL Server**.
5. Wprowadź **tolladmin** w **nazwy użytkownika** pola i **123toll!** w **hasło** pola.
6. Kliknij przycisk **wybierz lub wprowadź nazwę bazy danych**i wybierz **TollDataDB** jako bazy danych.
   
    ![Dodaj połączenie — okno dialogowe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image17.jpg)
7. Kliknij przycisk **OK**.
8. Otwórz Eksploratora serwera.
   
    ![Eksplorator serwera](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image18.png)
9. Zobacz cztery tabele w bazie danych TollDataDB.
   
    ![Tabele w bazie danych TollDataDB](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image19.jpg)

## <a name="event-generator-tollapp-sample-project"></a>Generator zdarzeń: TollApp przykładowy projekt
Skrypt programu PowerShell jest uruchamiana automatycznie wysyłać zdarzenia za pomocą programu TollApp przykładowej aplikacji. Nie trzeba wykonywać żadnych dodatkowych czynności.

Jednak jeśli interesuje Cię szczegóły implementacji, można znaleźć kodu źródłowego aplikacji TollApp w serwisie GitHub [przykłady/TollApp](https://aka.ms/azure-stream-analytics-toll-source).

![Zrzut ekranu przedstawiający przykładowy kod wyświetlany w programie Visual Studio](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image20.png)

## <a name="create-a-stream-analytics-job"></a>Tworzenie zadania usługi Stream Analytics
1. W portalu Azure kliknij zielony znak plus w lewym górnym rogu strony, aby utworzyć nowe zadanie usługi Stream Analytics. Wybierz **analizy i analiza** , a następnie kliknij przycisk **zadanie usługi Stream Analytics**.
   
    ![Przycisk Nowy](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image21.png)
2. Podaj nazwę zadania, sprawdzanie poprawności subskrypcji poprawić, a następnie utworzyć nową grupę zasobów, w tym samym regionie co magazyn Centrum zdarzeń (wartość domyślna to południowo-środkowe stany skryptu).
3. Kliknij przycisk **Przypnij do pulpitu nawigacyjnego** , a następnie **Utwórz** w dolnej części strony.
   
    ![Utwórz opcję zadania usługi analiza strumienia](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image22.png)

## <a name="define-input-sources"></a>Definiowanie źródeł dla wejścia
1. Zadania spowoduje utworzenie i otwarcie strony zadania. Lub kliknięciu analytics utworzone zadanie na pulpicie nawigacyjnym portalu.

2. Kliknij przycisk **dane wejściowe** kartę, aby zdefiniować dane źródłowe.
   
    ![Na karcie dane wejściowe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image24.png)
3. Kliknij przycisk **dodać dane wejściowe**.
   
    ![Dodaj opcję dane wejściowe](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image25.png)
4. Wprowadź **EntryStream** jako **ALIAS wejściowy**.
5. Typ źródła jest **strumienia danych**
6. Źródło jest **Centrum zdarzeń**.
7. **Przestrzeń nazw magistrali usług** powinna być TollData jeden w polu listy rozwijanej.
8. **Nazwa Centrum zdarzeń** powinien być ustawiony na **wpis**.
9. **Nazwa zasad Centrum zdarzeń*jest **RootManageSharedAccessKey** (wartość domyślna).
10. Wybierz **JSON** dla **FORMAT SERIALIZACJI zdarzeń** i **UTF8** dla **KODOWANIE**.
   
    Ustawienia będą wyglądać:
   
    ![Ustawienia Centrum zdarzeń](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image28.png)

10. Kliknij przycisk **Utwórz** w dolnej części strony, aby zakończyć pracę kreatora.
    
    Teraz, po utworzeniu wpisu strumienia będzie wykonaj te same kroki, aby utworzyć strumienia zakończenia. Należy wprowadzić wartości jak na poniższym zrzucie ekranu.
    
    ![Ustawienia dla tego strumienia zakończenia](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image31.png)
    
    Zdefiniowano dwóch strumieni wejściowych:
    
    ![Definicja strumienie wejściowe w portalu Azure](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image32.png)
    
    Następnie dodasz wejściowych danych referencyjnych pliku blob, który zawiera dane rejestracji samochodów.
11. Kliknij przycisk **dodać**, a następnie wykonaj te same czynności dla wejścia strumienia, ale wybierz **danych referencyjnych** zamiast **strumienia danych** i **Alias wejściowy** jest **rejestracji**.

12. Konto magazynu, który rozpoczyna się od **tolldata**. Nazwa kontenera powinna być **tolldata**i **wzorzec ścieżki** powinien być **registration.json**. Nazwa pliku jest rozróżniana wielkość liter i powinna być **małych**.
    
    ![Blog dotyczący miejsca do magazynowania](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image34.png)
13. Kliknij przycisk **Utwórz** aby zakończyć pracę kreatora.

Teraz wszystkie dane wejściowe są zdefiniowane.

## <a name="define-output"></a>Definiowanie danych wyjściowych
1. W okienku omówienie zadania usługi analiza strumienia wybierz **dane wyjściowe**.
   
    ![Karta danych wyjściowych i opcję "Dodaj wyjścia"](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image37.png)
2. Kliknij pozycję **Add** (Dodaj).
3. Ustaw **alias wyjściowy** na "Wyjście", a następnie **Sink** do **bazy danych SQL**.
3. Wybierz nazwę serwera używanego w sekcji "Połączenia do bazy danych z programu Visual Studio" tego artykułu. Nazwa bazy danych jest **TollDataDB**.
4. Wprowadź **tolladmin** w **USERNAME** pola **123toll!** w **hasło** pola i **TollDataRefJoin** w **tabeli** pola.
   
    ![Ustawienia bazy danych SQL](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image38.png)
5. Kliknij przycisk **Utwórz**.

## <a name="azure-stream-analytics-query"></a>Azure Stream analytics zapytania
**Zapytania** karta zawiera zapytanie SQL, które przekształca przychodzących danych.

![Zapytanie dodany do karty zapytania](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image39.png)

W tym samouczku próbuje odpowiedzi na kilka pytania biznesowe, które są powiązane z numer danych i konstrukcje zapytań usługi Stream Analytics, używane w Azure Stream Analytics w celu zapewnienia odpowiedniego odpowiedzi.

Przed rozpoczęciem pierwszego zadania Stream Analytics, Przyjrzyjmy się kilka scenariuszy i składnia zapytania.

## <a name="introduction-to-azure-stream-analytics-query-language"></a>Wprowadzenie do języka zapytań usługi Azure Stream Analytics
- - -
Załóżmy, że należy policzyć pojazdów, które należy wprowadzić stoisku przez. Ponieważ jest to stały strumień zdarzeń, należy zdefiniować "pewien czas." Umożliwia modyfikowanie zapytania jako "ilu pojazdów wprowadź stoisku przez co trzy minuty?". To jest często określana jako liczność wirowania.

Przyjrzyjmy się kwerendy usługi Azure Stream Analytics, która zawiera odpowiedzi na to pytanie:

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
    FROM EntryStream TIMESTAMP BY EntryTime
    GROUP BY TUMBLINGWINDOW(minute, 3), TollId

Jak widać, usługi Azure Stream Analytics wykorzystuje język kwerendy, takiego jak SQL i dodaje kilka rozszerzeń, aby określić czas aspektów zapytania.

Aby uzyskać więcej informacji, przeczytaj o [zarządzanie czasem](https://msdn.microsoft.com/library/azure/mt582045.aspx) i [Okienkową](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstrukcji używanych w zapytaniu w witrynie MSDN.

## <a name="testing-azure-stream-analytics-queries"></a>Testowanie zapytań usługi Azure Stream Analytics
Teraz, musieli napisać pierwszego zapytania usługi Azure Stream Analytics, nadszedł czas na przetestować go przy użyciu przykładowych plików danych znajdujących się w folderze TollApp w następującej ścieżce:

**.. \\TollApp\\TollApp\\danych**

Ten folder zawiera następujące pliki:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-1-number-of-vehicles-entering-a-toll-booth"></a>Pytanie 1: Liczba pojazdów stoisku przez
1. Otwórz Azure portal i przejdź do utworzonego zadania usługi analiza strumienia Azure. Kliknij przycisk **zapytania** karcie i Wklej zapytanie z poprzedniej sekcji.

2. Można sprawdzić poprawności tego zapytania dotyczącego przykładowych danych, przekazać dane do wprowadzania EntryStream klikając... symboli i wybierając **przekazać dane przykładowe z pliku**.

    ![Zrzut ekranu przedstawiający plik Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image41.png)
3. W wyświetlonym okienku wybierz plik (Entry.json) na komputerze lokalnym, a następnie kliknij przycisk **OK**. **Testu** ikona teraz oświetlenia i być aktywne.
   
    ![Zrzut ekranu przedstawiający plik Entry.json](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image42.png)
3. Sprawdź, czy wyniki kwerendy jest zgodnie z oczekiwaniami:
   
    ![Wyniki testu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image43.png)

## <a name="question-2-report-total-time-for-each-car-to-pass-through-the-toll-booth"></a>Pytanie 2: Łączny czas raport dla każdego samochodu do przekazywania stoisku przez
Średni czas, które są wymagane dla samochodów na przekazywanie przez pomaga ocenić wydajność procesu i obsługi klienta.

Aby znaleźć całkowity czas, należy dołączyć strumienia EntryTime ze strumieniem ExitTime. Dołączy strumieni w kolumnach TollId i LicencePlate. **JOIN** operator wymaga określenia danych czasowych swobodę, opisujący różnica akceptowalnego czasu między zdarzeniami dołączonego do. Użyjesz **DATEDIFF** funkcji, aby określić, że zdarzenia powinny być dłużej niż 15 minut od siebie. Możesz także obowiązują **DATEDIFF** funkcji, aby zakończyć pracę i czas wejścia do obliczenia czasu rzeczywistego samochodu spędzanego w stacji przez. Różnice stosowania **DATEDIFF** gdy jest używana w **wybierz** instrukcji zamiast **JOIN** warunku.

    SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute , EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
    FROM EntryStream TIMESTAMP BY EntryTime
    JOIN ExitStream TIMESTAMP BY ExitTime
    ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
    AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15

1. Aby przetestować tę kwerendę, zaktualizuj zapytanie na **zapytania** zadania. Dodaj plik testu dla **ExitStream** podobnie jak **EntryStream** wprowadzono powyżej.
   
2. Kliknij przycisk **testu**.

3. Zaznacz pole wyboru, aby przetestować zapytanie i wyświetlić dane wyjściowe:
   
    ![Dane wyjściowe testu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image45.png)

## <a name="question-3-report-all-commercial-vehicles-with-expired-registration"></a>Pytanie 3: Raport wszystkich pojazdów użytkowych z wygaśnięcie rejestracji
Usługa Azure Stream Analytics można użyć statycznej migawki danych w celu dołączenia strumieni danych czasowych. Aby zademonstrować tej funkcji, należy użyć następujących pytania próbki.

Jeśli pojazdów użytkowych jest zarejestrowany w systemie przez firmy, można przekazać za pośrednictwem stoisku przez bez zatrzymania inspekcji. Tabela odnośników pojazdów użytkowych rejestracji zostanie umożliwia zidentyfikowanie wszystkich pojazdów handlowych, które wygasły rejestracji.

```
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

Aby przetestować zapytanie przy użyciu danych referencyjnych, musisz zdefiniować źródło danych wejściowych danych referencyjnych, która została już utworzona.

Aby przetestować tę kwerendę, Wklej zapytanie w **zapytania** , kliknij pozycję **testu**i określ dwóch źródeł danych wejściowych i rejestracji przykładowe dane i kliknij przycisk **testu**.  
   
![Dane wyjściowe testu](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image46.png)

## <a name="start-the-stream-analytics-job"></a>Uruchom zadanie usługi analiza strumienia
Teraz nadszedł czas, aby zakończyć konfigurację i uruchomić zadanie. Zapisz zapytanie z 3 zapytania, które dają danych wyjściowych, który jest zgodny ze schematem **TollDataRefJoin** tabeli wyników.

Przejdź do zadania **pulpitu NAWIGACYJNEGO**i kliknij przycisk **START**.

![Zrzut ekranu przedstawiający przycisk Start na pulpicie nawigacyjnym zadania](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image48.png)

W otwartym oknie dialogowym Zmienianie **START wyjściowy** czas na **czasu niestandardowe**. Zmień godzinę na godzinę przed bieżącym czasem. Ta zmiana zapewnia, że wszystkie zdarzenia z Centrum zdarzeń są przetwarzane od czasu uruchomienia do generowania zdarzeń na początku tego samouczka. Teraz kliknij **Start** przycisk, aby uruchomić zadanie.

![Wybór czasu niestandardowych](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image49.png)

Uruchamianie zadania może potrwać kilka minut. Dla usługi Stream Analytics można zobaczyć stan na stronie najwyższego poziomu.

![Zrzut ekranu przedstawiający stan zadania](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image50.png)

## <a name="check-results-in-visual-studio"></a>Sprawdź wyniki w programie Visual Studio
1. Otwórz Eksploratora serwera w usłudze Visual Studio, a następnie kliknij prawym przyciskiem myszy **TollDataRefJoin** tabeli.
2. Kliknij przycisk **Pokaż dane tabeli** aby zobaczyć dane wyjściowe z zadania.
   
    ![Wybór "Pokaż tabeli danych" w Eksploratorze serwera](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image51.jpg)

## <a name="scale-out-azure-stream-analytics-jobs"></a>Skalowanie usługi Azure Stream Analytics zadania
Usługa Azure Stream Analytics jest przeznaczona do elastycznie skalowalnego tak, aby mogły obsługiwać dużą ilość danych. Można użyć zapytania usługi Azure Stream Analytics **PARTITION BY** klauzuli system stwierdzić, że ten krok zostanie skalowanie w poziomie. **PartitionId** to specjalne kolumna system doda do dopasowania Identyfikatora partycji danych wejściowych (Centrum zdarzeń).

    SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
    FROM EntryStream TIMESTAMP BY EntryTime PARTITION BY PartitionId
    GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId

1. Zatrzymać bieżące zadanie, zaktualizuj zapytanie w **zapytania** , a następnie otwórz **ustawienia** narzędzi na pulpicie nawigacyjnym zadania. Kliknij przycisk **skali**.
   
    **JEDNOSTKI przesyłania STRUMIENIOWEGO** zdefiniowanie ilości mocy obliczeniowej, który może odbierać zadania.
2. Zmiany listy w dół od 1 z 6.
   
    ![Zrzut ekranu przedstawiający wybranie 6 jednostki przesyłania strumieniowego](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image52.png)
3. Przejdź do **dane wyjściowe** karcie i Zmień nazwę tabeli SQL, aby **TollDataTumblingCountPartitioned**.

Jeśli zadanie zostanie uruchomione teraz Azure Stream Analytics można rozpraszających między większą ilością zasobów obliczeniowych i osiągnąć lepszą przepustowość. Należy pamiętać, aplikacja TollApp również wysyła zdarzenia partycjonowanego TollId.

## <a name="monitor"></a>Monitorowanie
**MONITOR** obszar zawiera Statystyka wykonywanym zadaniem. Po raz pierwszy konfiguracji jest niezbędne do używania magazynu konta w tym samym regionie (nazwa przez jak pozostałej części tego dokumentu).   

![Zrzut ekranu przedstawiający monitor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Dostęp można uzyskać **Dzienniki aktywności** z poziomu pulpitu nawigacyjnego zadania **ustawienia** również obszaru.


## <a name="conclusion"></a>Podsumowanie
W tym samouczku wprowadzone do usługi Azure Stream Analytics. Konieczne wykazanie, jak skonfigurować wejściami i wyjściami zadania usługi analiza strumienia. Scenariusz danych przez samouczek wyjaśniono typowych problemów, które pojawiają się w obszarze danych w ruchu i jak można rozwiązać za pomocą prostego zapytania przypominającego SQL w Azure Stream Analytics. Samouczek opisane konstrukcje rozszerzenia SQL do pracy z danymi danych czasowych. Go pokazano, jak sprzęgać strumienie danych, jak wzbogacić strumienia danych z statyczne dane referencyjne i porady skalowana w poziomie zapytanie w celu osiągnięcia wyższej przepustowości.

Chociaż ten samouczek zawiera wprowadzenie dobry, nie została ukończona w jakikolwiek sposób. Można znaleźć więcej wzorców zapytań przy użyciu języka SAQL w [zapytania przykłady typowych wzorców użycia usługi Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
Zapoznaj się [dokumentacji online](https://azure.microsoft.com/documentation/services/stream-analytics/) Aby dowiedzieć się więcej na temat usługi Azure Stream Analytics.

## <a name="clean-up-your-azure-account"></a>Wyczyść konta platformy Azure
1. Zatrzymaj zadanie usługi Stream Analytics w portalu Azure.
   
    Skrypt Setup.ps1 tworzy dwa centra zdarzeń i bazy danych SQL. Poniższe instrukcje pomogą Ci czyszczenie zasobów na końcu samouczka.
2. W oknie programu PowerShell, wpisz **.\\ CleanUp.ps1** uruchomić skrypt, który usuwa zasoby używane w samouczku.
   
   > [!NOTE]
   > Zasoby są identyfikowane przez nazwę. Upewnij się, że dokładnie przejrzyj poszczególne elementy przed potwierdzeniem usuwania.
   > 
   > 


