---
title: Tworzenie harmonogramów złożone i zaawansowane cyklu z Harmonogram systemu Azure
description: Informacje o sposobie tworzenia harmonogramów złożone i zaawansowane cyklu z Harmonogram systemu Azure.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 4293442e13fc4bae871b1f32a3ed4231d9f32632
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2018
---
# <a name="build-complex-schedules-and-advanced-recurrence-with-azure-scheduler"></a>Tworzenie harmonogramów złożone i zaawansowane cyklu z Harmonogram systemu Azure

Podstawowe zadania harmonogramu Azure jest harmonogram. Harmonogram Określa, kiedy i jak harmonogramu wykonuje zadanie. 

Można użyć harmonogramu, aby ustawić wiele harmonogramów jednorazowych i cyklicznego dla zadania. Harmonogramów jednorazowych uruchamiają się jeden raz o określonym czasie. Harmonogramów jednorazowych jest harmonogramów cyklicznych, które są wykonywane tylko raz. Harmonogramów cyklicznych wyzwalać na wstępnie określoną częstotliwością.

Z tego rodzaju elastyczności można użyć harmonogramu dla różnych scenariuszy biznesowych:

* **Porządkowanie danych okresowe**. Na przykład codziennie, Usuń wszystkie tweetów, które są starsze niż trzy miesiące.
* **Archiwizowanie**. Na przykład co miesiąc, Historia faktury wypychania do usługi tworzenia kopii zapasowej.
* **Żądania dotyczące danych zewnętrznych**. Na przykład co 15 minut, pobierają nowe ski prognoza pogody z NOAA.
* **Przetwarzanie obrazu**. Na przykład każdy dzień tygodnia, poza godzinami szczytu, użyj chmury obliczeniowej do skompresowania obrazów, które zostały przekazane do danego dnia.

W tym artykule firma Microsoft przeprowadzenie przykład zadań, które można utworzyć za pomocą harmonogramu. Firma Microsoft udostępnia dane JSON, który opisano każdy z harmonogramów. Jeśli używasz [interfejsu API REST harmonogramu](https://msdn.microsoft.com/library/mt629143.aspx), można użyć tego samego JSON do [Tworzenie zadania harmonogramu](https://msdn.microsoft.com/library/mt629145.aspx).

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Przykłady w tym artykule przedstawiono szerokość scenariusze, które obsługuje harmonogramu. Przykłady szeroko przedstawiono sposób tworzenia harmonogramów wielu wzorców zachowanie, w tym:

* Uruchom po określonej dacie i godzinie.
* Uruchom i powtarzanie określoną liczbę razy.
* Natychmiastowe uruchomienie i powtarzanie.
* Uruchom i powtarzanie co  *n*  minuty, godziny, dni, tygodnie lub miesiące, począwszy od określonego czasu.
* Uruchom i powtarzanie częstotliwością co tydzień lub co miesiąc, ale tylko w określone dni tygodnia lub w określone dni miesiąca.
* Uruchom i powtarzanie wiele razy w okresie. Na przykład, w ostatni piątek i ostatniego poniedziałku co miesiąc lub w 5:15:00 i 17:15:00 każdego dnia.

## <a name="date-and-date-time"></a>Data i godzina daty
Data odwołania w wykonaj zadania harmonogramu [specyfikacji ISO 8601](http://en.wikipedia.org/wiki/ISO_8601)i zawierają tylko data.

Postępuj zgodnie z odwołaniami do daty i godziny w zadania harmonogramu [specyfikacji ISO 8601](http://en.wikipedia.org/wiki/ISO_8601)i zawiera zarówno datę i godzinę. Daty i godziny, która nie określa przesunięcie UTC zakłada się, że czas UTC.  

## <a name="use-json-and-the-rest-api-to-create-a-schedule"></a>Użyj formatu JSON i interfejsu API REST, aby utworzyć harmonogram
Aby utworzyć harmonogram podstawowe za pomocą [interfejsu API REST harmonogramu](https://msdn.microsoft.com/library/mt629143), pierwszy [zarejestrować subskrypcji u dostawcy zasobów](https://msdn.microsoft.com/library/azure/dn790548.aspx). Nazwa dostawcy harmonogramu jest **Microsoft.Scheduler**. Następnie [tworzenie kolekcji zadań](https://msdn.microsoft.com/library/mt629159.aspx). Na koniec [utworzyć zadanie](https://msdn.microsoft.com/library/mt629145.aspx). 

Podczas tworzenia zadania planowania i cyklu można określić za pomocą formatu JSON, tak samo, jak w tym fragmencie:

    {
        "startTime": "2012-08-04T00:00Z", // Optional
         …
        "recurrence":                     // Optional
        {
            "frequency": "week",     // Can be "year", "month", "day", "week", "hour", or "minute"
            "interval": 1,                // How often to fire
            "schedule":                   // Optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // Optional (default to recur infinitely)
            "endTime": "2012-11-04",      // Optional (default to recur infinitely)
        },
        …
    }

## <a name="job-schema-basics"></a>Podstawowe informacje o zadaniu schematu
Poniższa tabela zawiera omówienie głównych elementów, które służy do ustawiania cyklu i Planowanie zadania:

| Nazwa JSON | Opis |
|:--- |:--- |
| **startTime** |Wartość daty i godziny. Podstawowe harmonogramów **startTime** jest pierwsze wystąpienie. Złożone harmonogramów rozpoczęcia zadania nie wcześniej niż **startTime**. |
| **recurrence** |Określa zasady cyklu zadania i cyklu, jaką zadanie działa. Obiekt cyklu obsługuje elementy **częstotliwość**, **interwał**, **endTime**, **liczba**, i **harmonogram**. Jeśli **cyklu** jest zdefiniowany, **częstotliwość** jest wymagana. Inne **cyklu** elementy są opcjonalne. |
| **frequency** |Ciąg, który reprezentuje jednostkę częstotliwość powtarzania zadania. Obsługiwane wartości to "min", "Godzina", "day", "tydzień" i "miesiąc". |
| **interval** |Dodatnia liczba całkowita. **Interwał** Określa interwał **częstotliwość** wartość, która określa, jak często zadanie działa. Na przykład jeśli **interwał** 3 i **częstotliwość** jest "tydzień" zadanie wystąpi co trzy tygodnie.<br /><br />Harmonogram obsługuje maksymalnie **interwał** 18 miesięczne częstotliwości, 78 częstotliwości co tydzień i 548 częstotliwości codziennie. Godziny i minuty częstotliwość obsługiwanych zakres to 1 < = **interwał** < = 1000. |
| **endTime** |Ciąg określający daty i godziny, po przekroczeniu którego zadanie nie działa. Można ustawić wartość **endTime** w przeszłości. Jeśli **endTime** i **liczby** nie są określone, zadanie działa nieograniczonej. Nie może zawierać jednocześnie **endTime** i **liczba** w tym samym zadaniu. |
| **Liczba** |Dodatnią liczbą całkowitą (większe od zera) określa, ile razy zadanie jest uruchamiane przed jego ukończeniem.<br /><br />**Liczba** reprezentuje liczbę razy, że zadanie będzie uruchamiane przed ukończeniem jest ustalony. Na przykład dla zadania, która jest wykonywana codziennie o **liczba** 5 i Data początkowa poniedziałek zadanie zostało ukończone po wykonaniu w piątek. Jeśli data rozpoczęcia przypada w przeszłości, pierwszy wykonywania jest obliczana na podstawie czasu utworzenia.<br /><br />Jeśli nie **endTime** lub **liczba** określono, że zadanie będzie uruchamiane nieograniczonej. Nie może zawierać jednocześnie **endTime** i **liczba** w tym samym zadaniu. |
| **schedule** |Zadanie o określonej częstotliwości zmienia jego cyklu na podstawie harmonogramu cyklu. A **harmonogram** wartość zawiera zmiany na podstawie minuty, godziny, dni tygodnia, dni miesiąca i numer tygodnia. |

## <a name="job-schema-defaults-limits-and-examples"></a>Wartości domyślne schematu zadania, ograniczenia i przykłady
W dalszej części tego artykułu omówiono poszczególnych elementów następujące szczegóły:

| Nazwa JSON | Typ wartości | Wymagana? | Wartość domyślna | Prawidłowe wartości | Przykład |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** |ciąg |Nie |None |ISO 8601-daty i godziny |`"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** |obiekt |Nie |None |Obiekt cyklu |`"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **frequency** |ciąg |Yes |None |"min", "Godzina", "day", "tydzień", "miesiąc" |`"frequency" : "hour"` |
| **interval** |numer |Yes |None |1 – 1000 |`"interval":10` |
| **endTime** |ciąg |Nie |Brak |Wartość daty i godziny, który reprezentuje czas w przyszłości |`"endTime" : "2013-02-09T09:30:00-08:00"` |
| **Liczba** |numer |Nie |None |>= 1 |`"count": 5` |
| **schedule** |obiekt |Nie |Brak |Obiekt harmonogramu |`"schedule" : { "minute" : [30], "hour" : [8,17] }` |

## <a name="deep-dive-starttime"></a>Szczegółowe informacje: startTime
W poniższej tabeli opisano sposób **startTime** kontroluje sposób, który uruchamia zadania:

| Wartość startTime | Brak cyklu | Cykl, bez harmonogramu | Cykl z harmonogramem |
|:--- |:--- |:--- |:--- |
| **Brak godziny rozpoczęcia** |Uruchom raz natychmiast. |Uruchom raz natychmiast. Uruchom podczas kolejnych wykonań kodu obliczana na podstawie ostatniego wykonania. |Uruchom raz natychmiast.<br /><br />Uruchom podczas kolejnych wykonań kodu na podstawie harmonogramu cyklu. |
| **Godzina rozpoczęcia w przeszłości** |Uruchom raz natychmiast. |Oblicz przyszłych wykonywania po raz pierwszy po czasie rozpoczęcia i teraz uruchomić.<br /><br />Uruchom podczas kolejnych wykonań kodu obliczana na podstawie ostatniego wykonania. <br /><br />Aby uzyskać więcej informacji zobacz przykład poniżej tej tabeli. |Zadania uruchamiania *nie sooner niż* określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia.<br /><br />Uruchom podczas kolejnych wykonań kodu na podstawie harmonogramu cyklu. |
| **Godzina rozpoczęcia w przyszłości lub bieżący czas** |Uruchom raz o określonym czasie rozpoczęcia. |Uruchom raz o określonym czasie rozpoczęcia.<br /><br />Uruchom podczas kolejnych wykonań kodu obliczana na podstawie ostatniego wykonania.|Zadania uruchamiania *nie sooner niż* określonym czasie rozpoczęcia. Pierwsze wystąpienie jest na podstawie harmonogramu, obliczana na podstawie czasu rozpoczęcia.<br /><br />Uruchom podczas kolejnych wykonań kodu na podstawie harmonogramu cyklu. |

Oto przykład co się stanie, gdy **startTime** przypada w przeszłości w przypadku ponownego wystąpienia, ale bez harmonogramu.  Załóżmy, że bieżący czas jest 2015-04-08 13:00, **startTime** jest 2015-04-07 14:00 i **cyklu** to dwa dni (zdefiniowane z **częstotliwość**: dnia i **interwał**: 2.) Należy pamiętać, że **startTime** przypada w przeszłości i występuje przed bieżącym czasem.

W tych warunkach wykonywania pierwszy będzie na 2015-04-09 na 14:00\. Aparat harmonogramu oblicza wystąpienia wykonania od czasu rozpoczęcia. Wszystkie wystąpienia w przeszłości są odrzucane. Aparat wykorzystuje następne wystąpienie, które ma miejsce w przyszłości. W takim przypadku **startTime** jest 2015-04-07 godzinie 2:00, więc następne wystąpienie jest dwa dni od tego momentu, która jest 2015-04-09 w 2:00 PM.

Należy pamiętać, że pierwszy wykonywania będzie taki sam czy **startTime** jest 2015-04-05-14:00 lub 2015-04-01-14:00\. Po pierwszym wykonaniu kolejne wykonania są obliczane przy użyciu harmonogramu. One zostanie na 2015-04-11 godzinie 2:00, a następnie 2015-04-13 godzinie 2:00, a następnie na 2015-04-15 godzinie 2:00 i tak dalej.

Na koniec, po zadanie ma harmonogramu, jeśli godzinach i minutach nie są ustawione w harmonogramie, domyślne wartości na godziny i minuty pierwszy wykonywania odpowiednio.

## <a name="deep-dive-schedule"></a>Nowości: harmonogramu
Można użyć **harmonogram** do *limit* Liczba wykonań zadania. Na przykład, jeśli zadanie o **częstotliwość** "miesiąca" ma harmonogram, który działa tylko na dzień 31, zadanie będzie uruchamiane tylko w miesiącach, które mają trzydzieści pierwszego dnia.

Można również użyć **harmonogram** do *rozwiń* Liczba wykonań zadania. Na przykład, jeśli zadanie o **częstotliwość** "miesiąca" ma harmonogram, który jest uruchamiany na dni miesiąca 1 i 2, zadanie działa w pierwszej i drugiej dni miesiąca, a nie tylko raz miesiąca.

Jeśli określisz wiele elementów harmonogramu, kolejność jest od największej do najmniejszej: numer tygodnia, miesiąc, dzień, dzień tygodnia, godziny i minuty.

W poniższej tabeli opisano szczegółowo elementy harmonogramu:

| Nazwa JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minutes** |Minuty, godziny, o której zadanie działa. |Tablica liczb całkowitych. |
| **hours** |Godziny, dnia, o której zadanie działa. |Tablica liczb całkowitych. |
| **weekDays** |Dni tygodnia, zadanie działa. Można określić tylko z częstotliwością co tydzień. |Tablica któregokolwiek z następujących wartości (rozmiar maksymalny tablicy wynosi 7):<br />-"Poniedziałek"<br />-"Wtorek"<br />-"Środa"<br />-"Czwartek"<br />-"Piątek"<br />-"Sobota"<br />-"Niedziela"<br /><br />Bez uwzględniania wielkości liter. |
| **monthlyOccurrences** |Określa, które dni miesiąca, zadanie działa. Można określić tylko w przypadku miesiąc. |Tablica **monthlyOccurrences** obiektów:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dzień** dzień tygodnia, zadanie działa. Na przykład *{niedziela}* oznacza niedzielę każdego miesiąca. Wymagany.<br /><br />**wystąpienie** jest wystąpieniem dzień w miesiącu. Na przykład *{niedziela, -1}* jest niedzielę ostatniego dnia miesiąca. Opcjonalny. |
| **monthDays** |Dzień miesiąca uruchamiania zadania. Można określić tylko w przypadku miesiąc. |Tablica z następujących wartości:<br />-Wszelkie wartość < = -1 i > =-31<br />-Wszelkie wartość > = 1 i < = 31|

## <a name="examples-recurrence-schedules"></a>Przykłady: Harmonogramy cyklu
W poniższych przykładach pokazano różne harmonogramy cyklu. Przykłady skupić się na obiekt harmonogramu i jego podelementy.

Te harmonogramy przyjęto założenie, że **interwał** jest ustawiona na 1\. Przykłady założono również odpowiedniego **częstotliwość** wartości dla wartości w **harmonogram**. Na przykład nie można użyć **częstotliwość** z "day" i mieć **monthDays** modyfikacji **harmonogram**. Opisano te ograniczenia we wcześniejszej części tego artykułu.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` |Jednocześnie 5: 00 każdego dnia.<br /><br />Harmonogram dopasowania każdej wartości w "Godzina" z każdej wartości w "min", z nich, aby utworzyć listę cały czas, w którym zadanie działa. |
| `{"minutes":[15], "hours":[5]}` |Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` |Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` |Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Uruchamiany co godzinę.<br /><br />To zadanie jest uruchamiane co godzinę. Minuta jest kontrolowany przez wartość dla **startTime**, jeśli je określono. Jeśli nie **startTime** jest określona, minuty zależy od czasu utworzenia. Na przykład, jeśli czas rozpoczęcia i godzina utworzenia (którekolwiek ma zastosowanie) jest godzina 12:25, zadanie jest uruchamiane w 00:25, 01:25 02:25,..., 23:25.<br /><br />Harmonogram jest odpowiednikiem zadanie o **częstotliwość** "Godzina" **interwał** 1 i nie **harmonogram** wartość. Różnica polega na to, czy można użyć tego harmonogramu z różnymi **częstotliwość** i **interwał** wartości, aby utworzyć inne zadania. Na przykład jeśli **częstotliwość** jest tylko raz w miesiącu zamiast codziennie "miesiąc" działa harmonogram (Jeśli **częstotliwość** jest "dzień"). |
| `{minutes:[0]}` |Uruchamiany co godzinę o pełnej godzinie.<br /><br />To zadanie jest uruchamiane co godzinę, ale na godzinę (12 AM, 1 AM, 2 AM i tak dalej). Jest to równoważne zadanie o **częstotliwość** "Godzina" **startTime** wartość zero minut, a nie **harmonogram**, jeśli częstotliwość wynosi "dzień". Jednak jeśli **częstotliwość** jest "week" lub "month" harmonogram odpowiednio wykonuje tylko jeden dzień tygodnia lub jeden dzień w miesiącu. |
| `{"minutes":[15]}` |Uruchom na 15 minut po pełnej godzinie, co godzinę.<br /><br />Jest uruchamiana co godzinę, począwszy od 00:15 AM, 1:15:00, 2:15 AM, i tak dalej. Kończy godzinie 23:15:00. |
| `{"hours":[17], "weekDays":["saturday"]}` |Jednocześnie 17: 00 w sobotę co tydzień. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Jednocześnie 17: 00 w poniedziałek, środę i piątek co tydzień. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchom na 5: 00 a 17 w poniedziałek, środę i piątek co tydzień. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchom na 5:15:00, 5:45:00, 17:15:00, a: 45 17 w poniedziałek, środę i piątek co tydzień. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Uruchom co 15 minut w dni robocze miedzy 9 a 4:45 PM. |
| `{"weekDays":["sunday"]}` |Uruchom w niedziele podczas uruchamiania. |
| `{"weekDays":["tuesday", "thursday"]}` |Uruchom we wtorki i czwartki podczas uruchamiania. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Uruchom o 6: 00 dwudziestego ósmego dnia każdego miesiąca (przy założeniu **częstotliwość** "miesiąca"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Uruchom o 6: 00 ostatniego dnia miesiąca.<br /><br />Jeśli chcesz uruchomić zadanie ostatniego dnia miesiąca, zamiast dnia 28, 29, 30 i 31 -1. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Uruchom o 6: 00 pierwszy i ostatni dzień każdego miesiąca. |
| `{monthDays":[1,-1]}` |Uruchom na pierwszy i ostatni dzień co miesiąc podczas uruchamiania. |
| `{monthDays":[1,14]}` |Uruchom pierwszy i czternastego dnia każdego miesiąca, w momencie rozpoczęcia. |
| `{monthDays":[2]}` |Uruchom dnia miesiąca w momencie rozpoczęcia. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Uruchom w pierwszym piątek każdego miesiąca o godzinie 5: 00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Uruchom w pierwszym piątek co miesiąc podczas uruchamiania. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Uruchom na trzeci piątek od zakończenia miesiąca, co miesiąc podczas uruchamiania. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Podczas uruchamiania, należy uruchomić na pierwszy i ostatni piątek miesiąca. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Uruchom w piątym piątek co miesiąc podczas uruchamiania.<br /><br />Jeśli w miesiącu nie ma żadnych piątej piątek, zadanie nie działa. Należy rozważyć użycie -1 zamiast 5 dla wystąpienia Jeśli chcesz uruchamiać zadania na ostatniego wystąpienia piątek miesiąca. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |

## <a name="see-also"></a>Zobacz także

- [Co to jest Scheduler?](scheduler-intro.md)
- [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
- [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)
- [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)
- [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)
- [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)
- [Azure harmonogramu wysokiej dostępności i niezawodności](scheduler-high-availability-reliability.md)
- [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)
- [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

