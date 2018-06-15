---
title: Tworzenie rozwiązania IoT przy użyciu usługi Azure Stream Analytics
description: Samouczka wprowadzającego dla scenariusza budki rozwiązania IoT analiza strumienia
services: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, sngun
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/21/2018
ms.openlocfilehash: 80e287d09fdc5ab7157b9ee46bc830fd2db4d501
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30912275"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Tworzenie rozwiązania IoT przy użyciu usługi analiza strumienia

## <a name="introduction"></a>Wprowadzenie
W tym rozwiązaniu Dowiedz się jak używać usługi Azure Stream Analytics Aby uzyskać wgląd w czasie rzeczywistym danych. Deweloperzy mogą łatwo łączyć strumienie danych, takich jak kliknij strumieni, dzienników i zdarzenia generowane przez urządzenie, rekordy historyczne lub dane referencyjne do uzyskania biznesowych. Jako usługa obliczeń strumienia w pełni zarządzana, w czasie rzeczywistym, która jest hostowana na platformie Microsoft Azure Azure Stream Analytics zapewnia odporność wbudowanych, małe opóźnienia i skalowalności na uruchomienie czasu w minutach.

Po zakończeniu działania tego rozwiązania, jest możliwość:

* Zapoznaj się z portalem Azure Stream Analytics.
* Konfigurowanie i wdrażanie zadanie przesyłania strumieniowego.
* Zwrócone rzeczywistych problemów i ich rozwiązanie przy użyciu języka zapytań usługi Stream Analytics.
* Tworzenie strumienia rozwiązania dla klientów za pomocą usługi Stream Analytics bez obaw.
* Umożliwia monitorowanie i rejestrowanie środowisko rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebne są następujące wymagania wstępne do ukończenia tego rozwiązania:
* [Subskrypcji platformy Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Wprowadzenie do scenariusza: "tekst Hello, przez!"
Stacja przez jest zjawiskiem wspólnej. Wystąpią ich na wielu trasy szybkiego ruchu, mostki i tunele całym świecie. Każda stacja przez ma wiele kabiny przez. Ręczne kabiny można zatrzymać na rzecz przez opiekun. Na automatyczne kabiny czujnik u góry każdego stoisku skanuje karty RFID, który jest umieszczony szyby Twojego pojazdów przy przesuwaniu stoisku przez. Jest łatwy do wizualizacji przejście pojazdów przez te stacje przez jako strumień zdarzeń, w którym interesujące operacje mogą być wykonywane.

![Obraz samochodów na kabiny przez](media/stream-analytics-build-an-iot-solution-using-stream-analytics/image1.jpg)

## <a name="incoming-data"></a>Dane przychodzące
To rozwiązanie działa z dwóch strumieni danych. Zainstalowane w wejście i wyjście stacji przez czujniki utworzyć pierwszy strumienia. Drugi strumień jest statyczny wyszukiwania zestawu danych, który zawiera dane rejestracji pojazdów.

### <a name="entry-data-stream"></a>Wpis strumienia danych
Strumień danych wpis zawiera informacje o samochodów, wejście przez stacje. Zakończ zdarzenia danych są aktywne strumieniowo do kolejki Centrum zdarzeń z aplikacji sieci Web zawarte w przykładowej aplikacji.

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
Strumień danych zakończenia zawiera informacje o samochodów, pozostawiając przez stacji. Zakończ zdarzenia danych są aktywne strumieniowo do kolejki Centrum zdarzeń z aplikacji sieci Web zawarte w przykładowej aplikacji.

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
W tym rozwiązaniu zastosowano statyczna migawka bazy danych rejestracji pojazdów użytkowych. Te dane są zapisywane w formacie JSON do magazynu obiektów blob platformy Azure, zawarte w próbce.

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
Aby wykonać to rozwiązanie, konieczna jest subskrypcja Microsoft Azure. Jeśli nie masz konta platformy Azure, możesz [żądania bezpłatną wersję próbną](http://azure.microsoft.com/pricing/free-trial/).

Należy postępować zgodnie z instrukcjami w sekcji "Wyczyść konta platformy Azure" na końcu tego artykułu tak, aby można było wprowadzać najlepsze wykorzystanie środków platformy Azure.

## <a name="deploy-the-sample"></a>Wdrażanie przykładowej 
Istnieje kilka zasobów, które łatwo można wdrożyć w grupie zasobów z kilku kliknięć. Określenie rozwiązania znajduje się w repozytorium github pod adresem [ https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp ](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Wdrażanie szablonu TollApp w portalu Azure
1. Aby wdrożyć środowisko TollApp Azure, użyj to łącze, aby [wdrażanie szablonu Azure TollApp](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Zaloguj się do portalu Azure w przypadku wyświetlenia monitu.

3. Wybierz subskrypcję, w którym są rozliczane różnych zasobów.

4. Określ nową grupę zasobów, na przykład o unikatowej nazwie `MyTollBooth`. 

5. Wybierz lokalizację platformy Azure.

6. Określ **interwał** jako liczbę sekund. Ta wartość jest używana w przykładowej aplikacji sieci web, jak często w celu wysyłania danych do Centrum zdarzeń. 

7. **Sprawdź** do wyrażenia zgody na warunki i postanowienia.

8. Wybierz **Przypnij do pulpitu nawigacyjnego** umożliwiając łatwy dostęp do zasobów później.

9. Wybierz **zakupu** przykładowy szablon wdrażania.

10. Po kilku chwilach, pojawi się powiadomienie, aby potwierdzić **wdrożenie zakończyło się pomyślnie**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Przejrzyj zasobów Azure Stream Analytics TollApp
1. Logowanie do witryny Azure Portal

2. Znajdź grupę zasobów, który został określony w poprzedniej sekcji.

3. Sprawdź, czy w grupie zasobów są wyświetlane następujące zasoby:
   - Konto jednego rozwiązania Cosmos bazy danych
   - Jednego zadania usługi analiza strumienia Azure
   - Jedno konto magazynu Azure
   - Jeden Centrum zdarzeń platformy Azure
   - Dwie aplikacje sieci Web

## <a name="examine-the-sample-tollapp-job"></a>Przejrzyj zadania TollApp próbki 
1. Począwszy od grupy zasobów w poprzedniej sekcji, wybierz zadanie przesyłania strumieniowego usługi Stream Analytics uruchamiania o nazwie **tollapp** (nazwa zawiera losowo wybranych znaków unikatowość).

2. Na **omówienie** stronę zadania powiadomienia **zapytania** pole, aby wyświetlić składnię.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Aby paraphrase celem zapytanie, załóżmy, że należy policzyć pojazdów, które należy wprowadzić stoisku przez. Ponieważ stoisku przez autostrady ma stały strumień pojazdów wprowadzania, te są wejściu zdarzeń są podobne do strumienia, który nigdy nie zatrzymuje. Określenie strumienia, należy zdefiniować "pewien czas" do mierzenia za pośrednictwem. Załóżmy uściślić Ponadto pytanie "ilu pojazdów wprowadzenia stoisku przez co trzy minuty?" To jest często określana jako liczność wirowania.

   Jak widać, usługi Azure Stream Analytics wykorzystuje język kwerendy, takiego jak SQL i dodaje kilka rozszerzeń, aby określić czas aspektów zapytania.  Aby uzyskać więcej informacji, przeczytaj o [zarządzanie czasem](https://msdn.microsoft.com/library/azure/mt582045.aspx) i [Okienkową](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstrukcji używanych w zapytaniu.

3. Sprawdź, czy dane wejściowe TollApp przykładowe zadania. Wartość wejściowa EntryStream jest używany w bieżącym zapytaniu.
   - **EntryStream** danych wejściowych jest połączeniem Centrum zdarzeń, które dane reprezentujące zawsze samochodu wchodzi budki autostrady kolejki. Aplikacja sieci web, który jest częścią próbki jest tworzenie zdarzenia i danych jest w kolejce w tym Centrum zdarzeń. Należy pamiętać, że tych danych wejściowych jest poddawany kwerendzie w klauzuli FROM zapytania przesyłania strumieniowego.
   - **ExitStream** danych wejściowych jest połączeniem Centrum zdarzeń, które dane reprezentujące zawsze samochodu opuszcza budki na autostrady kolejki. Ta przesyłania strumieniowego dane wejściowe są używane w późniejszym różnice składni zapytania.
   - **Rejestracja** wejściowy jest połączenie z magazynu obiektów Blob platformy Azure, do pliku registration.json statycznych, używany do wyszukiwania, zgodnie z potrzebami. To odwołanie do danych wejściowych jest używany w późniejszym różnice składni zapytania.

4. Sprawdź, czy dane wyjściowe zadania próbki TollApp.
   - **Rozwiązania cosmos DB** dane wyjściowe są rozwiązania Cosmos kolekcji bazy danych, który odbiera zdarzenia ujście danych wyjściowych. Należy pamiętać, że te dane wyjściowe jest używana w w klauzuli zapytania przesyłania strumieniowego.

## <a name="start-the-tollapp-streaming-job"></a>Uruchom zadanie przesyłania strumieniowego TollApp
Wykonaj następujące kroki, aby uruchomić zadanie przesyłania strumieniowego:

1. Na **omówienie** strony zadania, wybierz opcję **Start**.

2. Na **rozpoczęcia zadania** okienku wybierz **teraz**.

3. Po chwili, gdy zadanie jest uruchomione, na **omówienie** strony zadania przesyłania strumieniowego, widok **monitorowanie** wykresu. Na wykresie powinny pojawiać kilku tysięcy zdarzenia wejściowe i dziesiątki zdarzeń, danych wyjściowych.

## <a name="review-the-cosmosdb-output-data"></a>Przejrzyj CosmosDB danych wyjściowych
1. Znajdź grupę zasobów, która zawiera zasoby TollApp.

2. Wybierz konto bazy danych Azure rozwiązania Cosmos mających wzorzec nazwy **tollapp<random>-rozwiązania cosmos**.

3. Wybierz **Eksploratora danych** nagłówek, aby otworzyć stronę Eksploratora danych.

4. Rozwiń węzeł **tollAppDatabase** > **tollAppCollection** > **dokumenty**.

5. Na liście identyfikatorów kilka dokumentów dotyczących są wyświetlane po danych wyjściowych jest dostępna.

6. Zaznacz każdy identyfikator, aby przejrzeć dokument JSON. Zwróć uwagę, każdy tollid windowend czasu i liczby samochodów z tego okna.

7. Po dodatkowe trzy minuty inny zestaw cztery dokumenty, które jest dostępne, jeden dokument na tollid. 


## <a name="report-total-time-for-each-car"></a>Łączny czas raportu dla każdego samochodu
Średni czas, które są wymagane dla samochodów na przekazywanie przez pomaga ocenić wydajność procesu i obsługi klienta.

Aby znaleźć całkowity czas, Dołącz strumienia EntryTime ze strumieniem ExitTime. Dołącz dwa strumienie wejściowe na takie same pasujące kolumny TollId i LicencePlate. **JOIN** operator wymaga określenia danych czasowych swobodę, opisujący różnica akceptowalnego czasu między zdarzeniami dołączonego do. Użyj **DATEDIFF** funkcji, aby określić, że zdarzenia powinny być dłużej niż 15 minut od siebie. Mają zastosowanie również **DATEDIFF** funkcji, aby zakończyć pracę i czas wejścia do obliczenia czasu rzeczywistego samochodu spędzanego w stacji przez. Różnice stosowania **DATEDIFF** gdy jest używana w **wybierz** instrukcji zamiast **JOIN** warunku.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aby zaktualizować TollApp przesyłania strumieniowego składnia zapytania zadania:

1. Na **omówienie** strony zadania, wybierz opcję **zatrzymać**.

2. Poczekaj kilka chwil powiadomienia, że zadanie zostało zatrzymane.

3. Pod nagłówkiem TOPOLOGII zadanie, wybierz **< > zapytania**

4. Wklej skorygowaną przesyłania strumieniowego zapytanie SQL.

5. Wybierz **zapisać** Aby zapisać kwerendę. Potwierdź **tak** Aby zapisać zmiany.

6. Na **omówienie** strony zadania, wybierz opcję **Start**.

7. Na **rozpoczęcia zadania** okienku wybierz **teraz**.

### <a name="review-the-total-time-in-the-output"></a>Przejrzyj całkowity czas w danych wyjściowych
Powtórz kroki opisane w poprzedniej sekcji, aby przejrzeć CosmosDB danych wyjściowych z zadania przesyłania strumieniowego. Przejrzyj najnowszych dokumentów JSON. 

Na przykład w tym dokumencie przedstawiono przykład samochodu z tablicy licencji, czas entrytime i wyjście i pole obliczeniowe durationinminutes DATEDIFF przedstawiający czas trwania stoisku przez co dwie minuty: 
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Raport pojazdów z wygaśnięcie rejestracji
Usługa Azure Stream Analytics można użyć statycznej migawki danych referencyjnych w celu dołączenia strumieni danych czasowych. Aby zademonstrować tej funkcji, należy użyć następujących pytania próbki. Dane wejściowe rejestracji są statyczne blob pliku json, który wyświetla wygaśnięcia licencji tagów. Po dołączeniu do na płycie licencji, danych referencyjnych jest porównywany z każdego vehicle przechodzącej przez przez oba. 

Jeśli pojazdów użytkowych jest zarejestrowany w systemie przez firmy, można przekazać za pośrednictwem stoisku przez bez zatrzymania inspekcji. Tabela odnośników rejestracji umożliwia zidentyfikowanie wszystkich pojazdów handlowych, które wygasły rejestracji.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Powtórz kroki opisane w poprzedniej sekcji do zaktualizowania TollApp przesyłania strumieniowego składnia zapytania zadania.

2. Powtórz kroki opisane w poprzedniej sekcji, aby przejrzeć CosmosDB danych wyjściowych z zadania przesyłania strumieniowego. 

Przykładowe dane wyjściowe:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>Skalowanie w poziomie zadania
Usługa Azure Stream Analytics jest przeznaczona do elastycznie skalowalnego tak, aby mogły obsługiwać dużych ilości danych. Można użyć zapytania usługi Azure Stream Analytics **PARTITION BY** klauzuli system stwierdzić, że ten krok skaluje się. **PartitionId** to specjalne kolumna system doda do dopasowania Identyfikatora partycji danych wejściowych (Centrum zdarzeń).

Do skalowania w poziomie zapytania na partycje, edytować składni zapytania następujący kod:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream 
TIMESTAMP BY EntryTime 
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Aby skalować na więcej jednostek przesyłania strumieniowego przesyłania strumieniowego zadania:

1. **Zatrzymaj** bieżącego zadania. 

2. Zmień elementy składni zapytania w **< > zapytania** strony, a następnie zapisz zmiany.

3. W pozycji konfiguracji w zadaniu przesyłania strumieniowego, wybierz **skali**.
   
4. Slajd **jednostki przesyłania strumieniowego** suwaka od 1 do 6. Jednostki przesyłania strumieniowego zdefiniowanie ilości mocy obliczeniowej, który może odbierać zadania. Wybierz pozycję **Zapisz**.

5. **Uruchom** zadanie przesyłania strumieniowego, aby zademonstrować dodatkowe skali. Usługa Azure Stream Analytics rozdziela pracę w większą ilością zasobów obliczeniowych i osiągnąć lepszą przepustowość, partycjonowanie pracy między zasobami przy użyciu kolumny określone w klauzuli PARTITION BY. 

## <a name="monitor-the-job"></a>Monitorowanie zadania
**MONITOR** obszar zawiera Statystyka wykonywanym zadaniem. Konfiguracja po raz pierwszy jest potrzebna, aby skorzystać z konta magazynu w tym samym regionie (nazwa przez jak pozostałej części tego dokumentu).   

![Zrzut ekranu przedstawiający monitor](media/stream-analytics-build-an-iot-solution-using-stream-analytics/monitoring.png)

Dostęp można uzyskać **Dzienniki aktywności** z poziomu pulpitu nawigacyjnego zadania **ustawienia** również obszaru.

## <a name="clean-up-the-tollapp-resources"></a>Oczyszczanie zasobów TollApp
1. Zatrzymaj zadanie usługi Stream Analytics w portalu Azure.

2. Znajdź grupę zasobów, która zawiera osiem zasobów związanych z szablonu TollApp.

3. Wybierz pozycję **Usuń grupę zasobów**. Wpisz nazwę grupy zasobów, aby potwierdzić usunięcie.

## <a name="conclusion"></a>Podsumowanie
To rozwiązanie wprowadzone do usługi Azure Stream Analytics. Konieczne wykazanie, jak skonfigurować wejściami i wyjściami zadania usługi analiza strumienia. Scenariusz danych przez rozwiązanie wyjaśniono typowych problemów, które pojawiają się w obszarze danych w ruchu i jak można rozwiązać za pomocą prostego zapytania przypominającego SQL w Azure Stream Analytics. Rozwiązanie opisane konstrukcje rozszerzenia SQL do pracy z danymi danych czasowych. Go pokazano, jak sprzęgać strumienie danych, jak wzbogacić strumienia danych z statyczne dane referencyjne i porady skalowana w poziomie zapytanie w celu osiągnięcia wyższej przepustowości.

Chociaż to rozwiązanie zapewnia dobre wprowadzenie, nie została ukończona w jakikolwiek sposób. Można znaleźć więcej wzorców zapytań przy użyciu języka SAQL w [zapytania przykłady typowych wzorców użycia usługi Stream Analytics](stream-analytics-stream-analytics-query-patterns.md).
