---
title: Tworzenie rozwiązania IoT przy użyciu usługi Azure Stream Analytics
description: Wprowadzenie do samouczka dotyczącego rozwiązania Stream Analytics IoT scenariusza budki
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426447"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Tworzenie rozwiązania IoT przy użyciu usługi Stream Analytics

## <a name="introduction"></a>Wprowadzenie
W tym rozwiązaniu dowiesz się, jak uzyskać wgląd w czasie rzeczywistym z danych za pomocą usługi Azure Stream Analytics. Deweloperzy mogą łatwo łączyć strumienie danych, takich jak kliknięcie strumieni, dzienniki i zdarzenia generowane przez urządzenie, z rekordami historycznymi lub danymi referencyjnymi w celu uzyskania szczegółowych informacji biznesowych. Jako usługa obliczeń w pełni zarządzane, w czasie rzeczywistym strumień, który znajduje się w systemie Microsoft Azure Azure Stream Analytics oferuje wbudowaną odporność, małe opóźnienia i skalowalności można rozpocząć w ciągu kilku minut.

Po zakończeniu tego rozwiązania, możliwe jest:

* Zapoznaj się z portalu usługi Azure Stream Analytics.
* Konfigurowanie i wdrażanie zadania przesyłania strumieniowego.
* Sposób działania rzeczywistych problemów i ich rozwiązania przy użyciu języka zapytań usługi Stream Analytics.
* Opracowywanie rozwiązań do przesyłania strumieniowego dla swoich klientów za pomocą usługi Stream Analytics bez obaw.
* Umożliwia monitorowanie i rejestrowanie środowisko rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne
Potrzebne są następujące wymagania wstępne do ukończenia tego rozwiązania:
* [Subskrypcja platformy Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Wprowadzenie do scenariusza: "Hello, płatny!"
Płatny stacji jest typowym zjawiskiem. Użytkownik je napotka na wielu trasy szybkiego ruchu, mostków i tunele na całym świecie. Każda stacja płatny ma wiele kabiny płatny. Ręczne kabiny można zatrzymać płatne Opiekun płatny. Na automatyczne kabiny czujnika na górze każdej stoisku skanuje karty RFID, które jest umieszczone na szyby pojazdu, jak przekazać stoisku płatny. To ułatwia wizualizowanie przejście pojazdów przez te stacje płatny jako strumień zdarzeń, w którym można wykonać operacji interesujące.

![Zdjęcia samochodów w kabinach](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Dane przychodzące
To rozwiązanie działa z dwóch strumieni danych. Czujniki zainstalowane w wejścia i wyjścia z stacje płatny utworzyć pierwszy strumienia. Drugi strumień jest statyczny wyszukiwania zestawu danych, zawierającej dane rejestracyjne pojazdów.

### <a name="entry-data-stream"></a>Strumień danych wpisu
Strumień danych wpis zawiera informacji na temat samochodów wejdzie z stacje płatny. Zakończ zdarzenia danych działają strumieniowo do kolejki usługi Centrum zdarzeń z aplikacji sieci Web zawarte w przykładowej aplikacji.

| TollID | EntryTime | LicensePlate | Stan | Tworzenie | Model | VehicleType | VehicleWeight | Płatny | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |CDE 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Poniżej przedstawiono krótki opis kolumny:

| Kolumna | Opis |
| --- | --- |
| TollID |Identyfikator punktu płatny, który unikatowo identyfikuje stoisku płatny |
| EntryTime |Data i godzina zgłoszenia pojazdu na stoisku płatny w formacie UTC |
| LicensePlate |Numer rejestracyjny pojazdu |
| Stan |Stan, w Stanach Zjednoczonych |
| Tworzenie |Producent samochodów |
| Model |Liczba modeli samochodów |
| VehicleType |1 dla pojazdów pasażerskich lub 2 dla pojazdów komercyjnych |
| WeightType |Masy pojazdu w tonach; 0 dla pojazdów pasażerskich |
| Płatny |Wartość płatny w USD |
| Tag |E-Tag na samochodów, który automatyzuje płatności; puste, jeśli płatność została wykonana ręcznie |

### <a name="exit-data-stream"></a>Stream data zakończenia
Stream data zakończenia zawiera informacje o samochodów, pozostawiając stacji płatny. Zakończ zdarzenia danych działają strumieniowo do kolejki usługi Centrum zdarzeń z aplikacji sieci Web zawarte w przykładowej aplikacji.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000 Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000 Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000 Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000 Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000 Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000 Z |CDE 1007 |

Poniżej przedstawiono krótki opis kolumny:

| Kolumna | Opis |
| --- | --- |
| TollID |Identyfikator punktu płatny, który unikatowo identyfikuje stoisku płatny |
| ExitTime |Data i godzina zakończenia pojazdu z stoisku płatny w formacie UTC |
| LicensePlate |Numer rejestracyjny pojazdu |

### <a name="commercial-vehicle-registration-data"></a>Dane rejestracyjne pojazdów komercyjnych
Rozwiązanie używa statycznego migawki bazy danych rejestracji pojazdów użytkowych. Te dane są zapisywane w formacie JSON w usłudze Azure blob storage, zawarte w przykładzie.

| LicensePlate | Identyfikator | Wygasłe |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BAC 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Poniżej przedstawiono krótki opis kolumny:

| Kolumna | Opis |
| --- | --- |
| LicensePlate |Numer rejestracyjny pojazdu |
| Identyfikator |Identyfikator rejestracji pojazdu |
| Wygasłe |Stan rejestracji pojazdu: 0, jeśli rejestracja vehicle jest aktywny, 1, jeśli rejestracji wygasł. |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Konfigurowanie środowiska usługi Azure Stream Analytics
Aby wykonać to rozwiązanie, należy do subskrypcji Microsoft Azure. Jeśli nie masz konta platformy Azure, możesz to zrobić [żądań bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

Należy koniecznie wykonaj kroki opisane w sekcji "Wyczyść konta platformy Azure" na końcu tego artykułu, aby można było optymalnie wykorzystać możliwości platformy Azure w wysokości.

## <a name="deploy-the-sample"></a>Wdrażanie przykładu
Istnieje kilka zasobów, które można łatwo wdrożyć w grupie zasobów, wraz z kilkoma kliknięciami. Definicja rozwiązania jest hostowana w repozytorium GitHub w [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Wdrażanie szablonu TollApp w witrynie Azure portal
1. Przeprowadzić wdrożenie w środowisku TollApp na platformie Azure, użyj tego linku, aby [wdrażania szablonu usługi Azure TollApp](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json).

2. Zaloguj się do witryny Azure portal, jeśli zostanie wyświetlony monit.

3. Wybierz subskrypcję, w którym różne zasoby są rozliczane.

4. Na przykład określ nową grupę zasobów o unikatowej nazwie `MyTollBooth`.

5. Wybierz lokalizację platformy Azure.

6. Określ **interwał** jako liczbę sekund. Ta wartość jest używana przykładowa aplikacja sieci web, jak często wysyłać dane do Centrum zdarzeń.

7. **Sprawdź** musieli się zgodzić na warunki i postanowienia.

8. Wybierz **Przypnij do pulpitu nawigacyjnego** tak, aby łatwy dostęp do zasobów później.

9. Wybierz **zakupu** wdrożyć przykładowy szablon.

10. Po kilku chwilach pojawi się powiadomienie, aby potwierdzić **wdrażanie zakończyło się pomyślnie**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Przejrzyj zasoby usługi Azure Stream Analytics TollApp

1. Logowanie się do witryny Azure Portal

2. Znajdź grupę zasobów, który został określony w poprzedniej sekcji.

3. Sprawdź, czy następujące zasoby są wyświetlane w grupie zasobów:
   - Konto jednej usługi Cosmos DB
   - Jedno zadanie programu usługi Azure Stream Analytics
   - Jedno konto magazynu platformy Azure
   - Jedno Centrum zdarzeń platformy Azure
   - Dwie aplikacje sieci Web

## <a name="examine-the-sample-tollapp-job"></a>Sprawdź przykładowe zadania TollApp
1. Począwszy od grupy zasobów w poprzedniej sekcji, wybierz zadanie przesyłania strumieniowego usługi Stream Analytics, rozpoczynając od nazwy **tollapp** (nazwa zawiera losowo wybranych znaków, aby zapewnić unikatowość).

2. Na **Przegląd** strony zadania, zwróć uwagę **zapytania** pole, aby wyświetlić składnię zapytań.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Aby paraphrase intencji zapytania, załóżmy, że trzeba liczbę pojazdów, które należy wprowadzić stoisku płatny. Ponieważ stoisku płatny Kalifornijskiej ma ciągłego strumienia pojazdów, są zdarzenia wejścia są analogiczne do strumienia, który nigdy nie zatrzymuje się. Określenie strumień, należy zdefiniować "okres czasu" do mierzenia za pośrednictwem. Spróbujmy uściślić pytanie w ramach dodatkowo "ile pojazdów wprowadzenia stoisku płatny co trzy minuty?" Jest to często nazywane liczba wirowania.

   Jak widać, Azure Stream Analytics używa języka zapytań, takiego jak SQL i dodaje kilka rozszerzeń na potrzeby określania aspektów czas zapytania.  Aby uzyskać więcej informacji, przeczytaj o [zarządzanie czasem](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) i [obsługi okien](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) konstrukcji używanych w zapytaniu.

3. Sprawdź dane wejściowe TollApp przykładowego zadania. Wartość wejściowa EntryStream jest używany w bieżącym zapytaniu.
   - **EntryStream** danych wejściowych jest połączenie z Centrum zdarzeń, która kolejkuje danych reprezentujący każdorazowo samochodu wprowadza budki na drogach. Utworzenie aplikacji sieci web, która jest część próbki zdarzeń i danych znajduje się w kolejce w tym Centrum zdarzeń. Należy pamiętać, że te dane wejściowe jest wysyłane zapytanie w klauzuli FROM zapytania przesyłania strumieniowego.
   - **ExitStream** danych wejściowych jest połączenie z Centrum zdarzeń, która kolejkuje danych reprezentujący każdorazowo samochodu zamyka budki na drogach. Wejście to przesyłania strumieniowego jest używany w nowszych różnice składni zapytania.
   - **Rejestracja** dane wejściowe są połączenia magazynu obiektów Blob platformy Azure, do pliku statycznego registration.json, używany do wyszukiwania, zgodnie z potrzebami. Ten wejściowych danych referencyjnych jest używany w późniejszym różnice składni zapytania.

4. Sprawdź dane wyjściowe TollApp przykładowego zadania.
   - **Cosmos DB** Output to kontener bazy danych Cosmos, który odbiera zdarzenia wyjściowego ujścia. Należy pamiętać, że te dane wyjściowe jest używany w w klauzuli zapytania przesyłania strumieniowego.

## <a name="start-the-tollapp-streaming-job"></a>Uruchamianie zadania przesyłania strumieniowego TollApp
Wykonaj następujące kroki, aby uruchomić zadanie przesyłania strumieniowego:

1. Na **Przegląd** strony zadania, wybierz opcję **Start**.

2. Na **zadanie rozpoczęcia** okienku wybierz **teraz**.

3. Po chwili, gdy zadanie jest uruchomione, na **Przegląd** strony zadania przesyłania strumieniowego widoku **monitorowanie** wykresu. Wykres powinny pokazywać kilka tysięcy zdarzeń wejściowych oraz dziesiątki zdarzeń, danych wyjściowych.

## <a name="review-the-cosmosdb-output-data"></a>Przejrzyj dane wyjściowe bazy danych cosmos DB
1. Znajdź grupę zasobów, która zawiera zasoby TollApp.

2. Wybierz konto Azure Cosmos DB z wzorcem nazwy **tollapp\<losowo\>-Cosmos**.

3. Wybierz **Eksplorator danych** nagłówek, aby otworzyć stronę Eksploratora danych.

4. Rozwiń **tollAppDatabase** > **tollAppCollection** > **dokumenty**.

5. Na liście identyfikatorów kilka docs są wyświetlane po udostępnieniu dane wyjściowe.

6. Zaznacz każdy identyfikator, aby zapoznać się z dokumentu JSON. Zwróć uwagę, każdy tollid windowend czas i liczba samochodów z tego okna.

7. Po trzy minuty dodatkowe inny zbiór cztery dokumenty, które jest dostępny, jeden dokument na tollid.


## <a name="report-total-time-for-each-car"></a>Łączny czas raportu dla każdego samochodu
Średni czas, który jest wymagany dla samochodu dopuszczone płatny pomaga ocenić wydajność procesu i obsługi klienta.

Aby znaleźć całkowity czas, Dołącz do strumienia EntryTime za pomocą usługi stream ExitTime. Możesz połączyć dwa strumienie danych wejściowych w taki sam pasujących kolumn TollId i LicencePlate. **Dołącz** operator wymaga określenia danych czasowych swobodę, opisujący dopuszczalnego czasu oczekiwania różnicy dołączonym do zdarzenia. Użyj **DATEDIFF** funkcję, aby określić, że zdarzenia powinny być co najwyżej 15 minut od siebie nawzajem. Mają zastosowanie również w **DATEDIFF** funkcję, aby zakończyć pracę i czas wejścia do obliczania czasu rzeczywistego samochodu spędza w stacji płatny. Należy zanotować różnicę użytkowania **DATEDIFF** gdy jest używany w **wybierz** instrukcji zamiast **Dołącz** warunku.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aby zaktualizować TollApp przesyłania strumieniowego składni zapytania zadania:

1. Na **Przegląd** strony zadania, wybierz opcję **zatrzymać**.

2. Poczekaj kilka minut na powiadomienie, że zadanie zostało zatrzymane.

3. Pod nagłówkiem TOPOLOGIA zadań, wybrać **< > zapytania**

4. Wklej skorygowany przesyłania strumieniowego zapytanie SQL.

5. Wybierz **Zapisz** Aby zapisać kwerendę. Upewnij się, **tak** Aby zapisać zmiany.

6. Na **Przegląd** strony zadania, wybierz opcję **Start**.

7. Na **zadanie rozpoczęcia** okienku wybierz **teraz**.

### <a name="review-the-total-time-in-the-output"></a>Przejrzyj całkowity czas w danych wyjściowych
Powtórz kroki opisane w poprzedniej sekcji, aby zapoznać się z danych wyjściowych cosmos DB z zadania przesyłania strumieniowego. Przejrzyj najnowsze dokumentów JSON.

Na przykład w tym dokumencie przedstawiono samochodu przykład z talerza licencji, czas entrytime i wyjścia i pola obliczeniowego durationinminutes DATEDIFF, wyświetlanie czasu trwania stoisku płatny co dwie minuty:
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

## <a name="report-vehicles-with-expired-registration"></a>Pojazdów raportu z wygaśnięcie rejestracji
Usługa Azure Stream Analytics można użyć migawki statyczne dane referencyjne na potrzeby połączenia ze strumieni danych czasowych. Aby zademonstrować tę możliwość, należy użyć następujących pytania próbki. Dane wejściowe rejestracji jest plik json statycznych obiektów blob, który wyświetla wygaśnięcia licencji tagów. Dołączając na płycie licencji danych referencyjnych jest porównywany z każdego pojazdu przechodzi przez płatny obu.

Jeśli w firmie płatny nie zarejestrowano pojazdów użytkowych, można przekazać za pośrednictwem punktu płatny bez zatrzymania w celu przeprowadzenia inspekcji. Użyj tabeli odnośników rejestracji, aby zidentyfikować wszystkie pojazdów użytkowych, które wygasły rejestracji.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Powtórz kroki opisane w poprzedniej sekcji, aby zaktualizować TollApp przesyłania strumieniowego składni zapytania zadania.

2. Powtórz kroki opisane w poprzedniej sekcji, aby zapoznać się z danych wyjściowych cosmos DB z zadania przesyłania strumieniowego.

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
Usługa Azure Stream Analytics jest przeznaczony do elastyczne skalowanie, dzięki czemu może obsługiwać duże ilości danych. Zapytanie Azure Stream Analytics może użyć klauzuli **Partition by** , aby poinformować system, że ten krok skaluje się. **PartitionID** jest specjalną kolumną, którą system dodaje do identyfikatora partycji danych wejściowych (centrum zdarzeń).

Aby skalować zapytanie do partycji, Edytuj składnia zapytania z następującym kodem:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Skalowanie w górę zadania przesyłania strumieniowego do większej liczby jednostek przesyłania strumieniowego:

1. **Zatrzymaj** bieżącego zadania.

2. Uaktualnij składnię zapytań w **< > zapytania** strony, a następnie zapisz zmiany.

3. W pozycji Konfiguruj w zadaniu przesyłania strumieniowego, wybierz **skalowania**.

4. Przesuń **jednostek przesyłania strumieniowego** suwak z zakresu od 1 do 6. Jednostki przesyłania strumieniowego zdefiniowanie ilości mocy obliczeniowej, która może odbierać zadania. Wybierz pozycję **Zapisz**.

5. **Rozpocznij** zadania przesyłania strumieniowego, aby zademonstrować dodatkowe skali. Usługa Azure Stream Analytics rozdziela pracę w większej ilości zasobów obliczeniowych i osiągnąć lepszą przepustowość partycjonowanie pracy między zasobami przy użyciu kolumny określone w klauzuli PARTITION BY.

## <a name="monitor-the-job"></a>Monitorowanie zadania
**MONITOR** obszar zawiera dane statystyczne o uruchomionego zadania. Konfiguracja po raz pierwszy jest potrzebna do używania konta magazynu, w tym samym regionie (nazwa numer płatny takich jak pozostałej części tego dokumentu).

![Usługa Azure Stream Analytics zadania monitorowania](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Możesz uzyskać dostęp **dzienników aktywności** na pulpicie nawigacyjnym zadania **ustawienia** także obszaru.

## <a name="clean-up-the-tollapp-resources"></a>Oczyszczanie zasobów TollApp
1. Zatrzymaj zadanie usługi Stream Analytics w witrynie Azure portal.

2. Znajdź grupę zasobów, która zawiera osiem zasoby związane z szablonu TollApp.

3. Wybierz pozycję **Usuń grupę zasobów**. Wpisz nazwę grupy zasobów, aby potwierdzić usunięcie.

## <a name="conclusion"></a>Podsumowanie
To rozwiązanie umożliwia wprowadzenie do usługi Azure Stream Analytics. Jego wykazać, jak skonfigurować dane wejściowe i wyjściowe dla zadania usługi Stream Analytics. Dzięki scenariuszowi danych płatny rozwiązania opisano typowe rodzaje problemów, które występują w przestrzeni danych w ruchu i jak można rozwiązać za pomocą prostych zapytań przypominający SQL w usłudze Azure Stream Analytics. Rozwiązanie opisane struktur rozszerzenia SQL do pracy z danymi danych czasowych. Jego pokazano, jak sprzęgać strumienie danych, jak wzbogacanie strumienia danych z statyczne dane referencyjne i sposób skalowanie zapytania w celu uzyskania większej przepływności.

Chociaż to rozwiązanie zapewnia dobre wprowadzenie, nie jest ukończone w jakikolwiek sposób. Można znaleźć więcej wzorców zapytań w języku SAQL [przykłady dla typowych wzorców użycia usługi Stream Analytics zapytania](stream-analytics-stream-analytics-query-patterns.md).
