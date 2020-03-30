---
title: Tworzenie rozwiązania IoT przy użyciu usługi Azure Stream Analytics
description: Wprowadzenie samouczek dla rozwiązania IoT usługi Stream Analytics scenariusza punktu poboru opłat
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: f506cc526a824d45ae2d6b7a75e1c1a99dae4d64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426447"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Tworzenie rozwiązania IoT przy użyciu usługi Stream Analytics

## <a name="introduction"></a>Wprowadzenie
W tym rozwiązaniu dowiesz się, jak korzystać z usługi Azure Stream Analytics w celu uzyskania szczegółowych informacji w czasie rzeczywistym na podstawie danych. Deweloperzy mogą łatwo łączyć strumienie danych, takie jak strumienie kliknięć, dzienniki i zdarzenia generowane przez urządzenie, z rekordami historycznymi lub danymi referencyjnymi w celu uzyskania szczegółowych informacji biznesowych. Jako w pełni zarządzana usługa obliczania strumienia w czasie rzeczywistym, która jest hostowana na platformie Microsoft Azure, usługa Azure Stream Analytics zapewnia wbudowaną odporność, małe opóźnienia i skalowalność, aby rozpocząć i uruchomić w ciągu kilku minut.

Po zakończeniu tego rozwiązania, jesteś w stanie:

* Zapoznaj się z portalem usługi Azure Stream Analytics.
* Konfigurowanie i wdrażanie zadania przesyłania strumieniowego.
* Artykułuj rzeczywiste problemy i rozwiązuj je za pomocą języka zapytań usługi Stream Analytics.
* Opracuj rozwiązania do przesyłania strumieniowego dla swoich klientów, korzystając z usługi Stream Analytics z ufnością.
* Użyj monitorowania i rejestrowania środowiska do rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć to rozwiązanie, potrzebne są następujące wymagania wstępne:
* [Subskrypcja platformy Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Wprowadzenie scenariusza: "Cześć, Toll!"
Punkt poboru opłat jest powszechnym zjawiskiem. Można je spotkać na wielu drogach ekspresowych, mostach i tunelach na całym świecie. Każdy punkt poboru opłat ma wiele punktów poboru opłat. Na kabinach ręcznych zatrzymujesz się, aby uiścić opłatę drogową na rzecz opiekuna. W zautomatyzowanych kabinach czujnik na górze każdego stoiska skanuje kartę RFID przymocowaną do przedniej szyby pojazdu podczas przejazdu po stanowisku poboru opłat. Łatwo jest wizualizować przejazd pojazdów przez te stacje poboru opłat jako strumień zdarzeń, nad którym można wykonać ciekawe operacje.

![Zdjęcie samochodów na punktach poboru opłat](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Dane przychodzące
To rozwiązanie działa z dwoma strumieniami danych. Czujniki zainstalowane przy wejściu i wyjściu ze stacji poboru opłat wytwarzają pierwszy strumień. Drugi strumień to statyczny zestaw danych wyszukiwania zawierający dane rejestracyjne pojazdu.

### <a name="entry-data-stream"></a>Strumień danych wprowadzania
Strumień danych wejściowych zawiera informacje o samochodach wjeżdżanych do stacji poboru opłat. Zdarzenia danych wyjściowych są przesyłane strumieniowo na żywo do kolejki centrum zdarzeń z aplikacji sieci Web zawartej w przykładowej aplikacji.

| TollID (opłata drogowaID) | Czas wejścia | Tablica licencyjna | Stan | Marka | Model | Typ pojazdu | Waga pojazdu | Płatnych | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |Crv |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |Abc 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |Crv |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |Cde 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Oto krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| TollID (opłata drogowaID) |Identyfikator punktu poboru opłat, który jednoznacznie identyfikuje punkt poboru opłat |
| Czas wejścia |Data i godzina wjazdu pojazdu do punktu poboru opłat w UTC |
| Tablica licencyjna |Numer rejestracyjny pojazdu |
| Stan |Stan w Stanach Zjednoczonych |
| Marka |Producent samochodu |
| Model |Numer modelu samochodu |
| Typ pojazdu |1 dla pojazdów osobowych lub 2 dla pojazdów użytkowych |
| Rodzaj wagi |Masa pojazdu w tonach; 0 dla samochodów osobowych |
| Płatnych |Wartość opłaty drogowej w USD |
| Tag |E-Tag na samochodzie, który automatyzuje płatności; puste, gdzie płatność została dokonana ręcznie |

### <a name="exit-data-stream"></a>Wyjście ze strumienia danych
Strumień danych wyjściowych zawiera informacje o samochodach opuszczających punkt poboru opłat. Zdarzenia danych wyjściowych są przesyłane strumieniowo na żywo do kolejki centrum zdarzeń z aplikacji sieci Web zawartej w przykładowej aplikacji.

| **TollId (ład opłaty drogow** | **Exittime** | **Tablica licencyjna** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000Z |Abc 1004 |
| 2 |2014-09-10T12:07:00.0000000Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000Z |Cde 1007 |

Oto krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| TollID (opłata drogowaID) |Identyfikator punktu poboru opłat, który jednoznacznie identyfikuje punkt poboru opłat |
| Exittime |Data i godzina wychodzenia z punktu poboru opłat drogowych w UTC |
| Tablica licencyjna |Numer rejestracyjny pojazdu |

### <a name="commercial-vehicle-registration-data"></a>Dane rejestracyjne pojazdów użytkowych
Rozwiązanie wykorzystuje statyczną migawkę bazy danych rejestracji pojazdów użytkowych. Te dane są zapisywane jako plik JSON w magazynie obiektów blob platformy Azure, zawarte w przykładzie.

| Tablica licencyjna | Identyfikator rejestracji | Wygasłe |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| Bac 1005 |876133137 |1 |
| RER 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Oto krótki opis kolumn:

| Kolumna | Opis |
| --- | --- |
| Tablica licencyjna |Numer rejestracyjny pojazdu |
| Identyfikator rejestracji |Identyfikator rejestracyjny pojazdu |
| Wygasłe |Status rejestracyjny pojazdu: 0, jeśli rejestracja pojazdu jest aktywna, 1, jeśli rejestracja wygasła |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>Konfigurowanie środowiska dla usługi Azure Stream Analytics
Aby ukończyć to rozwiązanie, potrzebujesz subskrypcji platformy Microsoft Azure. Jeśli nie masz konta platformy Azure, możesz [poprosić o bezpłatną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).

Wykonaj kroki opisane w sekcji "Oczyść swoje konto platformy Azure" na końcu tego artykułu, aby jak najlepiej wykorzystać środki platformy Azure.

## <a name="deploy-the-sample"></a>Wdrażanie próbki
Istnieje kilka zasobów, które można łatwo wdrożyć w grupie zasobów wraz z kilkoma kliknięciami. Definicja rozwiązania jest hostowana w repozytorium GitHub pod adresem [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp).

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>Wdrażanie szablonu TollApp w witrynie Azure portal
1. Aby wdrożyć środowisko TollApp na platformie Azure, użyj tego łącza, aby [wdrożyć szablon platformy Azure TollApp.](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)

2. Zaloguj się do witryny Azure portal, jeśli zostanie wyświetlony monit.

3. Wybierz subskrypcję, w której są rozliczane różne zasoby.

4. Określ nową grupę zasobów o unikatowej nazwie, na przykład `MyTollBooth`.

5. Wybierz lokalizację platformy Azure.

6. Określ **interwał** jako liczbę sekund. Ta wartość jest używana w przykładowej aplikacji sieci web, jak często wysyłać dane do Centrum zdarzeń.

7. **Sprawdź,** czy zgadzasz się z warunkami.

8. Wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** aby można było łatwo zlokalizować zasoby później.

9. Wybierz **pozycję Zakup,** aby wdrożyć przykładowy szablon.

10. Po kilku chwilach pojawia się powiadomienie potwierdzające, że **wdrożenie zakończyło się pomyślnie.**

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Przeglądanie zasobów usługi Azure Stream Analytics TollApp

1. Logowanie się do witryny Azure Portal

2. Znajdź grupę zasobów nazwaną w poprzedniej sekcji.

3. Sprawdź, czy w grupie zasobów znajdują się następujące zasoby:
   - Jedno konto usługi Cosmos DB
   - Jedno zadanie usługi Azure Stream Analytics
   - Jedno konto usługi Azure Storage
   - Jedno centrum zdarzeń platformy Azure
   - Dwie aplikacje sieci Web

## <a name="examine-the-sample-tollapp-job"></a>Sprawdź przykładowe zadanie TollApp
1. Zaczynając od grupy zasobów w poprzedniej sekcji, wybierz zadanie przesyłania strumieniowego usługi Stream Analytics, zaczynając od nazwy **tollapp** (nazwa zawiera losowe znaki dla unikatowości).

2. Na stronie **Przegląd** zadania zwróć uwagę na pole **Kwerenda,** aby wyświetlić składnię kwerendy.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Aby sparafrazować intencję zapytania, załóżmy, że musisz policzyć liczbę pojazdów, które wjeżdżają do punktu poboru opłat. Ponieważ punkt poboru opłat autostradowych ma ciągły strumień pojazdów wjeżdżających, są to zdarzenia wjazdowe są analogiczne do strumienia, który nigdy się nie zatrzymuje. Aby określić ilościowo strumień, należy zdefiniować "okres czasu", aby zmierzyć. Przyjmijmy jeszcze bardziej pytanie: "Ile pojazdów wjeżdża do punktu poboru opłat co trzy minuty?" Jest to powszechnie określane jako liczba tumbling.

   Jak widać, usługa Azure Stream Analytics używa języka zapytań, który jest podobny do języka SQL i dodaje kilka rozszerzeń, aby określić aspekty związane z czasem kwerendy.  Aby uzyskać więcej informacji, przeczytaj o [zarządzanie czasem](https://docs.microsoft.com/stream-analytics-query/time-management-azure-stream-analytics) i [windowing](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) konstrukcje używane w kwerendzie.

3. Sprawdź dane wejściowe przykładowego zadania TollApp. Tylko entrystream danych wejściowych jest używany w bieżącej kwerendy.
   - **EntryStream** input to połączenie centrum zdarzeń, które kolejkuje dane reprezentujące za każdym razem, gdy samochód wjeżdża do punktu poboru opłat na autostradzie. Aplikacja sieci web, która jest częścią próbki jest tworzenie zdarzeń i że dane są umieszczane w kolejce w tym Centrum zdarzeń. Należy zauważyć, że to dane wejściowe jest wyszukiwany w from klauzuli kwerendy przesyłania strumieniowego.
   - **ExitStream** wejście jest połączenie centrum zdarzeń, które kolejkuje dane reprezentujące za każdym razem, gdy samochód wychodzi z punktu poboru opłat na autostradzie. To dane wejściowe przesyłania strumieniowego jest używane w późniejszych odmianach składni kwerendy.
   - **Dane** wejściowe rejestracji to połączenie magazynu obiektów Blob platformy Azure, wskazujące na statyczny plik registration.json, używane do wyszukiwania w razie potrzeby. To dane wejściowe odwołania są używane w późniejszych odmianach składni kwerendy.

4. Sprawdź wyniki przykładowego zadania TollApp.
   - **Dane wyjściowe usługi Cosmos DB** to kontener bazy danych usługi Cosmos, który odbiera zdarzenia ujścia danych wyjściowych. Należy zauważyć, że to dane wyjściowe jest używany w into klauzuli kwerendy przesyłania strumieniowego.

## <a name="start-the-tollapp-streaming-job"></a>Rozpocznij pracę przesyłania strumieniowego TollApp
Aby rozpocząć pracę przesyłania strumieniowego, wykonaj następujące czynności:

1. Na stronie **Przegląd** zadania wybierz pozycję **Start**.

2. W okienku **Zadania startowego** wybierz pozycję **Teraz**.

3. Po kilku chwilach po uruchomieniu zadania na stronie **Przegląd** zadania przesyłania strumieniowego wyświetl wykres **monitorowania.** Wykres powinien przedstawiać kilka tysięcy zdarzeń wejściowych i dziesiątki zdarzeń wyjściowych.

## <a name="review-the-cosmosdb-output-data"></a>Przeglądanie danych wyjściowych usługi CosmosDB
1. Znajdź grupę zasobów zawierającą zasoby TollApp.

2. Wybierz konto usługi Azure Cosmos DB z wzorcem nazwy **tollapp\<random\>-cosmos**.

3. Wybierz nagłówek **Eksploratora danych,** aby otworzyć stronę Eksploratora danych.

4. Rozwiń **poboru opłatAppDatabaseAppCollection** > **tollAppCollection** > **Documents**.

5. Na liście identyfikatorów kilka dokumentów jest wyświetlanych po udostępnieniu danych wyjściowych.

6. Wybierz każdy identyfikator, aby przejrzeć dokument JSON. Zwróć uwagę na każdy tollid, czas windowend i liczbę samochodów z tego okna.

7. Po dodatkowych trzech minutach dostępny jest kolejny zestaw czterech dokumentów, po jednym dokumencie na tollid.


## <a name="report-total-time-for-each-car"></a>Zgłoś całkowity czas dla każdego samochodu
Średni czas potrzebny do przejazdu samochodu przez opłatę drogową pomaga ocenić efektywność procesu i doświadczenie klienta.

Aby znaleźć całkowity czas, dołącz do strumienia EntryTime ze strumieniem ExitTime. Dołącz do dwóch strumieni wejściowych w równych pasujących kolumnach TollId i LicencePlate. **Join** Operator wymaga określenia czasowego schyłku, który opisuje dopuszczalną różnicę czasu między zdarzeniami sprzężonymi. Użyj **DATEDIFF** funkcji, aby określić, że zdarzenia powinny być nie więcej niż 15 minut od siebie. Należy również zastosować funkcję **DATEDIFF,** aby wyjść i wjechać, aby obliczyć rzeczywisty czas, który samochód spędza w stacji poboru opłat. Należy zwrócić uwagę na różnicę w użyciu **DATEDIFF,** gdy jest używany w **SELECT** instrukcji, a nie warunek **JOIN.**

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>Aby zaktualizować składnię kwerendy przesyłania strumieniowego TollApp:

1. Na stronie **Przegląd** zadania wybierz pozycję **Zatrzymaj**.

2. Poczekaj chwilę na powiadomienie, że zadanie zostało zatrzymane.

3. W nagłówku JOB TOPOLOGY wybierz **pozycję< > Kwerenda**

4. Wklej dostosowaną kwerendę SQL do przesyłania strumieniowego.

5. Wybierz **pozycję Zapisz,** aby zapisać kwerendę. Potwierdź **tak,** aby zapisać zmiany.

6. Na stronie **Przegląd** zadania wybierz pozycję **Start**.

7. W okienku **Zadania startowego** wybierz pozycję **Teraz**.

### <a name="review-the-total-time-in-the-output"></a>Przejrzyj całkowity czas w danych wyjściowych
Powtórz kroki opisane w poprzedniej sekcji, aby przejrzeć dane wyjściowe usługi CosmosDB z zadania przesyłania strumieniowego. Przejrzyj najnowsze dokumenty JSON.

Na przykład w tym dokumencie pokazano przykładowy samochód z określoną tablicą rejestracyjną, czasem wejścia i wyjścia oraz obliczonym czasem trwania DATEDIFF, w którym czas trwania punktu poboru opłat drogowych trwa jako dwie minuty:
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

## <a name="report-vehicles-with-expired-registration"></a>Zgłaszanie pojazdów z wygasłą rejestracją
Usługa Azure Stream Analytics może używać statycznych migawek danych referencyjnych do łączenia się z strumieniami danych czasowych. Aby zademonstrować tę funkcję, należy użyć następującego przykładowego pytania. Dane wejściowe rejestracja to statyczny plik json obiektu blob, który zawiera listę wygaśnięcia tagów licencji. Łącząc się na tablicy rejestracyjnej, dane referencyjne są porównywane do każdego pojazdu przechodzącego przez opłatę drogową.

Jeżeli pojazd użytkowy jest zarejestrowany w przedsiębiorstwie poboru opłat drogowych, może przejść przez punkt poboru opłat bez zatrzymania do kontroli. Użyj tabeli odnośnych rejestracji, aby zidentyfikować wszystkie pojazdy użytkowe, które wygasły rejestracje.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Powtórz kroki opisane w poprzedniej sekcji, aby zaktualizować składnię zapytania o zadanie przesyłania strumieniowego TollApp.

2. Powtórz kroki opisane w poprzedniej sekcji, aby przejrzeć dane wyjściowe usługi CosmosDB z zadania przesyłania strumieniowego.

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
Usługa Azure Stream Analytics została zaprojektowana do elastycznego skalowania, dzięki czemu może obsługiwać duże ilości danych. Zapytanie usługi Azure Stream Analytics można użyć **klauzuli PARTITION BY,** aby poinformować system, że ten krok jest skalowany w poziomie. **PartitionId** to specjalna kolumna dodana przez system w celu dopasowania identyfikatora partycji danych wejściowych (centrum zdarzeń).

Aby skalować kwerendę w poziomie do partycji, edytuj składnię kwerendy do następującego kodu:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

Aby zwiększyć skalowanie zadania przesyłania strumieniowego do większej liczby jednostek przesyłania strumieniowego:

1. **Zatrzymaj** bieżące zadanie.

2. Zaktualizuj składnię kwerendy na stronie **kwerendy< > ** i zapisz zmiany.

3. W obszarze nagłówka KONFIGURUJ w zadaniu przesyłania strumieniowego wybierz pozycję **Skaluj**.

4. Przesuń suwak **Jednostek przesyłania strumieniowego** z 1 do 6. Jednostki przesyłania strumieniowego określają ilość mocy obliczeniowej, jaką może otrzymać zadanie. Wybierz **pozycję Zapisz**.

5. **Uruchom** zadanie przesyłania strumieniowego, aby zademonstrować dodatkową skalę. Usługa Azure Stream Analytics dystrybuuje pracę w większej ilości zasobów obliczeniowych i osiąga lepszą przepływność, rozdzielając pracę między zasobami przy użyciu kolumny wyznaczonej w klauzuli PARTITION BY.

## <a name="monitor-the-job"></a>Monitorowanie zadania
Obszar **MONITOR** zawiera statystyki dotyczące uruchomionego zadania. Konfiguracja po raz pierwszy jest potrzebna do korzystania z konta magazynu w tym samym regionie (nazwa toll jak reszta tego dokumentu).

![Monitorowanie zadań usługi Azure Stream Analytics](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

Dostęp do **dzienników aktywności** można uzyskać również z obszaru **Ustawienia** pulpitu nawigacyjnego zadania.

## <a name="clean-up-the-tollapp-resources"></a>Oczyszczanie zasobów TollApp
1. Zatrzymaj zadanie usługi Stream Analytics w witrynie Azure portal.

2. Znajdź grupę zasobów zawierającą osiem zasobów związanych z szablonem TollApp.

3. Wybierz pozycję **Usuń grupę zasobów**. Wpisz nazwę grupy zasobów, aby potwierdzić usunięcie.

## <a name="conclusion"></a>Podsumowanie
To rozwiązanie wprowadziło Cię do usługi Azure Stream Analytics. Pokazano, jak skonfigurować dane wejściowe i wyjściowe dla zadania usługi Stream Analytics. Korzystając ze scenariusza Dane opłaty drogowej, rozwiązanie wyjaśniło typowe typy problemów, które pojawiają się w przestrzeni danych w ruchu i jak można je rozwiązać za pomocą prostych zapytań podobnych do SQL w usłudze Azure Stream Analytics. W rozwiązaniu opisano konstrukcje rozszerzenia SQL do pracy z danymi czasowymi. Pokazano, jak łączyć strumienie danych, jak wzbogacić strumień danych o statyczne dane referencyjne i jak skalować kwerendę w poziomie, aby osiągnąć wyższą przepływność.

Chociaż to rozwiązanie zapewnia dobre wprowadzenie, nie jest kompletne w żaden sposób. Więcej wzorców zapytań można znaleźć przy użyciu języka SAQL w [przykładach zapytań dla typowych wzorców użycia usługi Stream Analytics.](stream-analytics-stream-analytics-query-patterns.md)
