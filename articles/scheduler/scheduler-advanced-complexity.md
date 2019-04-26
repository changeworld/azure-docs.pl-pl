---
title: Tworzenie zaawansowanych harmonogramów zadań i cykle — usługa Azure Scheduler
description: Dowiedz się, jak tworzyć zaawansowane harmonogramy i cykli dla zadań w usłudze Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: a413261d251c8dfc1de9209168ee8137b85009f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531829"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Tworzenie zaawansowanych harmonogramów i cykli dla zadań w usłudze Azure Scheduler

> [!IMPORTANT]
> Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która zostanie wycofana. Zamiast niej [spróbuj używać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) do planowania zadań. 

W ramach [usługi Azure Scheduler](../scheduler/scheduler-intro.md) zadania, harmonogram jest core, która określa, kiedy i w jaki sposób usługa Harmonogram uruchamia zadanie. Dzięki usłudze Scheduler można skonfigurować wiele harmonogramów jednorazowych lub cykliczne zadania. Harmonogramów jednorazowych uruchomić tylko raz w określonym czasie i w zasadzie są cykliczne harmonogramy, które są wykonywane tylko raz. Cykliczne harmonogramy są uruchamiane z określoną częstotliwością. Dzięki tej elastyczności można użyć harmonogramu dla różnych scenariuszy biznesowych, na przykład:

* **Oczyszczanie danych regularnie**: Utwórz codzienne zadanie, które usuwa wszystkie tweety starsze niż trzy miesiące.

* **Archiwizowanie danych**: Tworzenie przypadku zadania comiesięcznego czy wypchnięć faktury historii usługi kopii zapasowej.

* **Żądanie danych zewnętrznych**: Utwórz zadanie jest uruchamiane co 15 minut, która ściąga nowy raport pogodowe z agencji NOAA.

* **Przetwarzanie obrazów**: Utwórz zadanie dzień tygodnia, w które poza godzinami pracy, korzysta z chmury obliczeniowej kompresowania obrazów przekazanych w ciągu dnia.

W tym artykule opisano przykład zadań, można utworzyć za pomocą harmonogramu i [interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)i zawiera definicję JavaScript Object Notation (JSON) dla każdego harmonogramu. 

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

W tych przykładach zakres scenariuszy, które obsługuje usługi Azure Scheduler oraz jak utworzyć harmonogramy dla różnych wzorców zachowania, na przykład:

* Uruchamiany raz o określonej daty i godziny.
* Uruchom, a następnie powtórz określoną liczbę razy.
* Natychmiastowe uruchomienie i powtarzanie.
* Uruchom i powtarzanie co *n* minuty, godziny, dni, tygodni lub miesięcy, zaczynając od określonego czasu.
* Uruchom, a następnie powtórz co tydzień lub co miesiąc, ale tylko w określonych dniach tygodnia lub w określone dni miesiąca.
* Uruchom, a następnie powtórz więcej niż jeden raz w danym okresie. Na przykład co miesiąc na poniedziałek i ostatni piątek lub codziennie o 5:15:00 i o godzinie 17:15.

W tym artykule opisano później tych scenariuszy, które bardziej szczegółowo.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Tworzenie harmonogramu za pomocą interfejsu API REST

Aby utworzyć harmonogram podstawowe z [interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler), wykonaj następujące kroki:

1. Zarejestruj swoją subskrypcję platformy Azure za pomocą dostawcy zasobów za pomocą [rejestrowanie operacji — interfejs REST API usługi Resource Manager](https://docs.microsoft.com/rest/api/resources/providers). Nazwa dostawcy usługi Azure Scheduler jest **Microsoft.Scheduler**. 

1. Tworzenie kolekcji zadań przy użyciu [operacji tworzenia lub aktualizacji dla kolekcji zadań](https://docs.microsoft.com/rest/api/scheduler/jobcollections) w interfejsie API REST usługi Scheduler. 

1. Tworzenie zadania przy użyciu [operacji tworzenia lub aktualizacji dla zadań](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementy schematu zadania

Ta tabela zawiera ogólne omówienie głównych elementów JSON używanych podczas konfigurowania cykli i harmonogramy zadań. 

| Element | Wymagany | Opis | 
|---------|----------|-------------|
| **startTime** | Nie | Wartość ciągu daty/godziny w [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) , który określa po pierwszym uruchomieniu zadania w harmonogramie podstawowe. <p>W przypadku harmonogramów złożonych zadania nie jest uruchamiany wcześniej niż **startTime**. | 
| **recurrence** | Nie | Zasady ponownego uruchamiania zadania. **Cyklu** obiektu obsługuje następujące elementy: **częstotliwość**, **interwał**, **harmonogram**, **liczba**, i **endTime**. <p>Jeśli używasz **cyklu** elementu, musisz również użyć **częstotliwość** elementu, podczas gdy inne **cyklu** elementy są opcjonalne. |
| **frequency** | Tak, gdy używasz **cyklu** | Jednostka czasu między wystąpieniami i obsługuje następujące wartości: "Minute", "Hour", "Day", "Week", "Month" i "Rok" | 
| **interval** | Nie | Dodatnia liczba całkowita określająca liczbę jednostek czasu między wystąpieniami na podstawie **częstotliwość**. <p>Na przykład jeśli **interwał** wynosi 10 i **częstotliwość** to "Week", zadanie jest powtarzany co 10 tygodnie. <p>Poniżej przedstawiono najbardziej liczba interwałów, które dla każdej częstotliwości: <p>-18 miesięcy <br>-78 tygodni <br>-548 dni <br>— W przypadku godziny i minuty, zakres to 1 < = <*interwał*>< = 1000. | 
| **schedule** | Nie | Definiuje zmiany do powtarzania, w oparciu o te określonego minuta — znaczniki, znaczniki godziny, dni tygodnia i dni miesiąca | 
| **count** | Nie | Dodatnia liczba całkowita, która określa liczbę prób, zadanie zostanie uruchomione przed zakończeniem. <p>Na przykład, jeśli zadania codziennego ma **liczba** Ustaw do 7, a data rozpoczęcia to poniedziałek, zakończeniu zadania uruchomione w niedzielę. Jeśli data rozpoczęcia już został przekazany, przy pierwszym uruchomieniu jest obliczany od czasu utworzenia. <p>Bez **endTime** lub **liczba**, zadanie zostanie uruchomione w nieskończoność. Nie można używać obu **liczba** i **endTime** w tym samym zadaniu, ale zasada, czy zakończeniu najpierw zostanie uznane. | 
| **endTime** | Nie | Wartość ciągu daty lub daty/godziny w [formatu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) określający, kiedy zatrzymania zadania uruchamiania. Można ustawić wartości **endTime** jest w przeszłości. <p>Bez **endTime** lub **liczba**, zadanie zostanie uruchomione w nieskończoność. Nie można używać obu **liczba** i **endTime** w tym samym zadaniu, ale zasada, czy zakończeniu najpierw zostanie uznane. |
|||| 

Na przykład, ten schemat JSON w tym artykule opisano podstawowe harmonogramu i cyklu dla zadania: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Wartości daty/godziny i daty*

* Daty w zadania usługi Scheduler obejmują tylko data i postępuj zgodnie z [specyfikacji ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Daty i godziny w zadania usługi Scheduler obejmują daty i godziny, postępuj zgodnie z [specyfikacji ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)i są zakłada się, że to UTC, jeśli nie określono żadnych przesunięcie czasu UTC. 

Aby uzyskać więcej informacji, zobacz [pojęcia, terminologia oraz jednostki](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Szczegółowe informacje: startTime

Poniższa tabela zawiera opis sposobu **startTime** kontroluje sposób wykonywania zadania:

| startTime | Brak cyklu | Cykl bez harmonogramu | Cykl z harmonogramem |
|-----------|---------------|-------------------------|--------------------------|
| **Brak godziny rozpoczęcia** | Uruchom raz od razu. | Uruchom raz od razu. Wykonania nowsze obliczonym na podstawie ostatniego czasu wykonania. | Uruchom raz od razu. Uruchom nowszego wykonania opierają na harmonogramie cyklu. | 
| **Godzina rozpoczęcia w przeszłości** | Uruchom raz od razu. | Oblicz przyszłość pierwszego uruchamiania czas po czasie rozpoczęcia i w tym czasie. <p>Wykonania nowsze obliczonym na podstawie ostatniego czasu wykonania. <p>Zobacz przykład tą tabelą. | Uruchamianie zadania *nie wcześniej niż* w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchom nowszego wykonania opierają na harmonogramie cyklu. | 
| **Godzina rozpoczęcia w przyszłości lub bieżący czas** | Uruchamiany raz o określonej godzinie. | Uruchamiany raz o określonej godzinie. <p>Wykonania nowsze obliczonym na podstawie ostatniego czasu wykonania. | Uruchamianie zadania *nie wcześniej niż* w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchom nowszego wykonania opierają na harmonogramie cyklu. |
||||| 

Załóżmy, że użytkownik ten przykład z tymi warunkami: godzinę rozpoczęcia w przeszłości, z cyklem, ale bez harmonogramu.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Bieżącą datę i godzinę wynosi 13:00:00 08 kwietnia 2015 r.

* Data i godzina rozpoczęcia jest 07 kwietnia 2015 r. o 14:00, czyli przed bieżącą datą i godziną.

* Cykl jest określony na 2 dni.

1. W tych warunkach pierwsze wykonanie następuje od 09 kwietnia 2015 r. o 14:00. 

   Harmonogramu oblicza wystąpienia wykonania na podstawie czasu uruchomienia odrzuca wszystkie wystąpienia w przeszłości i wykorzystuje następne wystąpienie w przyszłości. 
   W tym przypadku **startTime** jest 07 kwietnia 2015 roku o 14:00, więc kolejne wystąpienie ma miejsce dwa dni po tym czasie, czyli 09 kwietnia 2015 r. o 14:00.

   Pierwszego wykonania jest taka sama czy **startTime** to 2015-04-05-14:00 lub 2015-04-01 14:00. Po pierwszym wykonaniu nowszego wykonania są obliczane na podstawie harmonogramu. 
   
1. Liczba wykonań postępuj w następującej kolejności: 
   
   1. 2015-04-11 o 14:00
   1. 2015-04-13 o 14:00 
   1. 2015-04-15, o 14:00
   1. i tak dalej...

1. Na koniec, gdy zadanie ma harmonogram, ale nie określonej godziny i minuty, te domyślne wartości godziny i minuty w pierwszym wykonaniu odpowiednio.

<a name="schedule"></a>

## <a name="details-schedule"></a>Szczegóły: harmonogram

Możesz użyć **harmonogram** do *limit* liczby wykonań zadania. Na przykład, jeśli zadanie **częstotliwość** "Month" ma harmonogram uruchamiany tylko w dniu 31, zadanie będzie uruchamiane tylko w miesiącach, które mają 31 dni.

Można również użyć **harmonogram** do *rozwiń* liczby wykonań zadania. Na przykład, jeśli zadanie **częstotliwość** "Month" ma harmonogram uruchamiany w dniach miesiąca 1 i 2, zadanie zostanie uruchomione na pierwszego i drugiego dnia miesiąca, a nie tylko raz przez miesiąc.

Jeśli określisz więcej niż jeden element harmonogramu, kolejność obliczania następuje od największego do najmniejszego: numer tygodnia, miesiąc, dzień, dzień tygodnia, godziny i minuty.

W poniższej tabeli opisano szczegółowo elementy harmonogramu:

| Nazwa JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minutes** |Minuty godziny, o której zadanie zostanie uruchomione. |Tablica liczb całkowitych. |
| **hours** |Godziny dnia, o której zadanie zostanie uruchomione. |Tablica liczb całkowitych. |
| **weekDays** |Dni tygodnia, zadanie zostanie uruchomione. Można określić tylko z częstotliwością tygodniową. |Tablica dowolnych z następujących wartości (maksymalny rozmiar tablicy to 7):<br />-"Poniedziałek"<br />-"Wtorek"<br />-"Środa"<br />-"Czwartek"<br />-"Piątek"<br />-"Sobota"<br />-"Niedziela"<br /><br />Bez uwzględniania wielkości liter. |
| **monthlyOccurrences** |Określa, które dni miesiąca, zadanie zostanie uruchomione. Można określić tylko z częstotliwością miesięczną. |Tablica **monthlyOccurrences** obiektów:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dzień** jest dniem tygodnia, zadanie zostanie uruchomione. Na przykład *{niedziela}* są każdą niedzielę miesiąca. Wymagany.<br /><br />**wystąpienie** to wystąpienie dnia w miesiącu. Na przykład *{niedziela, -1}* to Ostatnia niedziela miesiąca. Opcjonalny. |
| **monthDays** |Dzień w miesiącu, w którym zadanie działa. Można określić tylko z częstotliwością miesięczną. |Tablica poniższych wartości:<br />- Dowolna wartość <= -1 i >= -31<br />- Dowolna wartość >= 1 i <= 31|

## <a name="examples-recurrence-schedules"></a>Przykłady: Harmonogramy cyklu

W poniższych przykładach pokazano różne harmonogramy cyklu. Przykłady skupić się na obiekt harmonogramu i jego podelementy.

Te harmonogramy przyjęto założenie, że **interwał** jest ustawiona na 1\. W przykładach założono również poprawny **częstotliwość** wartości dla wartości w **harmonogram**. Na przykład nie można użyć **częstotliwość** "Day" i mieć **monthDays** modyfikacji **harmonogram**. Opisano te ograniczenia we wcześniejszej części tego artykułu.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` |Uruchamiany o 5: 00 każdego dnia.<br /><br />Harmonogram dopasowania każdej wartości w ciągu "godzin" z każdej wartości w "min" i z nich, aby utworzyć listę cały czas, w którym zadanie działa. |
| `{"minutes":[15], "hours":[5]}` |Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` |Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` |Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Uruchamiany co godzinę.<br /><br />To zadanie jest uruchamiane co godzinę. Minuta jest kontrolowana przez wartość dla **startTime**, jeśli jest określona. Jeśli nie **startTime** jest określona, minuty są kontrolowane przez czas tworzenia. Na przykład jeśli czas rozpoczęcia lub godzina utworzenia (zależnie od sytuacji) to 12:25, zadanie jest uruchamiane godzinie 0:25, 01:25, 02:25,..., 23:25.<br /><br />Harmonogram jest taka sama jak zadanie z **częstotliwość** "Hour", **interwał** 1 i bez **harmonogram** wartość. Różnica polega na to, że można użyć tego harmonogramu z różnymi **częstotliwość** i **interwał** wartości, aby utworzyć inne zadania. Na przykład jeśli **częstotliwość** jest "month", harmonogram jest uruchamiany tylko raz w miesiącu, a nie każdego dnia (Jeśli **częstotliwość** "Day"). |
| `{minutes:[0]}` |Uruchamiany co godzinę o pełnej godzinie.<br /><br />To zadanie jest uruchamiane co godzinę, ale o pełnej godzinie (12 AM, 1 AM, 2 AM i tak dalej). Ten harmonogram jest taka sama jak zadanie z **częstotliwość** "Hour", **startTime** wartość zero minut, a nie **harmonogram**, jeśli częstotliwość wynosi "day". Jednak jeśli **częstotliwość** to "week" lub "month", harmonogram jest wykonywany tylko jeden dzień tygodnia lub raz w miesiącu, odpowiednio. |
| `{"minutes":[15]}` |Uruchamiany 15 minut po pełnej godzinie, co godzinę.<br /><br />Uruchamiany co godzinę, począwszy od 00:15 AM, 1:15:00, 2:15:00, i tak dalej. Kończy się ona w 23:15:00. |
| `{"hours":[17], "weekDays":["saturday"]}` |Uruchamiany o 17: 00 w sobotę. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o 17: 00 w każdy poniedziałek, środę i piątek. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o 5: 00 i 17: 00 w każdy poniedziałek, środę i piątek co tydzień. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o 5:15:00, 5:45:00, 17:15 i 17:45:00 w każdy poniedziałek, środę i piątek co tydzień. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Uruchamiany co 15 minut w dni robocze od 9: 00 i 16:45:00. |
| `{"weekDays":["sunday"]}` |Uruchom w niedziele o godzinie rozpoczęcia. |
| `{"weekDays":["tuesday", "thursday"]}` |Uruchom we wtorki i czwartki o godzinie rozpoczęcia. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Uruchamiany o 6: 00 w 28. dnia każdego miesiąca (zakładając, że **częstotliwość** "Month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Uruchamiany o 6: 00 ostatniego dnia miesiąca.<br /><br />Jeśli chcesz uruchomić zadanie ostatniego dnia miesiąca, użyj wartości -1 zamiast 28, 29, 30 lub 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Uruchamiany o 6: 00 pierwszego i ostatniego dnia każdego miesiąca. |
| `{monthDays":[1,-1]}` |Uruchom na pierwszego i ostatniego dnia każdego miesiąca o godzinie rozpoczęcia. |
| `{monthDays":[1,14]}` |Uruchom na i 14. dnia każdego miesiąca o godzinie rozpoczęcia. |
| `{monthDays":[2]}` |Uruchom na drugi dzień miesiąca o godzinie rozpoczęcia. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Uruchom w pierwszy piątek każdego miesiąca o 5: 00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Uruchom w pierwszy piątek każdego miesiąca o godzinie rozpoczęcia. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Uruchom w Trzeci piątek od końca każdego miesiąca, w czasie rozpoczęcia. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Uruchom w pierwszy i ostatni piątek każdego miesiąca o godzinie rozpoczęcia. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Uruchom w piąty piątek każdego miesiąca o godzinie rozpoczęcia.<br /><br />Jeśli w danym miesiącu jest nie piątego piątku, zadanie nie działa. Należy rozważyć przy użyciu -1 zamiast 5 dla wystąpienia aby zadanie zostanie uruchomione ostatniego wystąpienia piątek miesiąca. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |

## <a name="see-also"></a>Zobacz także

* [Czym jest Azure Scheduler?](scheduler-intro.md)
* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)
