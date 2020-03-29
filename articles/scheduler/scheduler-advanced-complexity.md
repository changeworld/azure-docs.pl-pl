---
title: Tworzenie zaawansowanych harmonogramów zadań i cykli
description: Dowiedz się, jak tworzyć zaawansowane harmonogramy i cykle dla zadań w usłudze Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: b85932bf0d4fd080afadef2bc28d6a218b2d627a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898587"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Tworzenie zaawansowanych harmonogramów i cykli dla zadań w usłudze Azure Scheduler

> [!IMPORTANT]
> [Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje harmonogram platformy Azure, który jest [wycofywany.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Aby kontynuować pracę z zadaniami skonfigurowane w harmonogramie, należy jak [najszybciej przeprowadzić migrację do usługi Azure Logic Apps.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Harmonogram nie jest już dostępny w witrynie Azure portal, ale [interfejsy CMDLET interfejsu REST](/rest/api/scheduler) i [narzędzia cmdlet programu Azure Scheduler PowerShell](scheduler-powershell-reference.md) pozostają dostępne w tej chwili, dzięki czemu można zarządzać zadaniami i kolekcjami zadań.

W ramach zadania [harmonogram platformy Azure](../scheduler/scheduler-intro.md) harmonogram jest rdzeniem, który określa, kiedy i jak usługa harmonogram uruchamia zadanie. Można skonfigurować wiele harmonogramów jednorazowych i cyklicznych dla zadania z harmonogramem. Harmonogramy jednorazowe są uruchamiane tylko raz o określonej godzinie i są zasadniczo harmonogramami cyklicznymi, które są uruchamiane tylko raz. Harmonogramy cykliczne są uruchamiane z określoną częstotliwością. Dzięki tej elastyczności można użyć harmonogramu dla różnych scenariuszy biznesowych, na przykład:

* **Regularnie czyszczą dane**: Utwórz codzienne zadanie, które usuwa wszystkie tweety starsze niż trzy miesiące.

* **Archiwum danych:** Tworzenie miesięcznego zadania, które wypycha historię faktur do usługi tworzenia kopii zapasowych.

* **Żądanie danych zewnętrznych:** Utwórz zadanie, które jest uruchamiane co 15 minut i pobiera nowy raport pogodowy z NOAA.

* **Przetwarzanie obrazów:** Utwórz zadanie w dni powszednie, które działa poza godzinami szczytu i używa chmury obliczeniowej do kompresji obrazów przesłanych w ciągu dnia.

W tym artykule opisano przykładowe zadania, które można utworzyć przy użyciu interfejsu Api Harmonogram i [interfejsu REST usługi Azure Scheduler](/rest/api/scheduler)i zawiera definicję notacji obiektów JavaScript (JSON) dla każdego harmonogramu. 

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

W tych przykładach przedstawiono zakres scenariuszy, które obsługuje harmonogram platformy Azure i jak tworzyć harmonogramy dla różnych wzorców zachowań, na przykład:

* Uruchom raz w określonym dniu i godzinie.
* Uruchom i powtórz określoną liczbę razy.
* Uruchom natychmiast i powtarzaj.
* Uruchamianie i powtarzanie co *n* minut, godzin, dni, tygodni lub miesięcy, począwszy od określonej godziny.
* Uruchamianie i powtarzanie co tydzień lub co miesiąc, ale tylko w określone dni tygodnia lub w określone dni miesiąca.
* Uruchom i powtórz więcej niż jeden raz przez określony okres. Na przykład co miesiąc w ostatni piątek i poniedziałek lub codziennie o 5:15 i 17:15.

W tym artykule opisano później te scenariusze bardziej szczegółowo.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Tworzenie harmonogramu za pomocą interfejsu REST API

Aby utworzyć podstawowy harmonogram za pomocą [interfejsu API REST usługi Azure Scheduler,](/rest/api/scheduler)wykonaj następujące kroki:

1. Zarejestruj swoją subskrypcję platformy Azure u dostawcy zasobów przy użyciu [operacji Zarejestruj — Interfejs API REST menedżera zasobów](https://docs.microsoft.com/rest/api/resources/providers). Nazwa dostawcy usługi Harmonogram platformy Azure to **Microsoft.Scheduler**. 

1. Tworzenie kolekcji zadań przy użyciu [operacji Utwórz lub Aktualizuj dla kolekcji zadań](https://docs.microsoft.com/rest/api/scheduler/jobcollections) w interfejsie API REST harmonogramu. 

1. Tworzenie zadania przy użyciu [operacji Tworzenie lub aktualizowanie dla zadań](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate). 

## <a name="job-schema-elements"></a>Elementy schematu zadania

Ta tabela zawiera omówienie wysokiego poziomu dla głównych elementów JSON, których można używać podczas konfigurowania cykli i harmonogramów dla zadań. 

| Element | Wymagany | Opis | 
|---------|----------|-------------|
| **startTime** | Nie | Wartość ciągu DateTime w [formacie ISO 8601,](https://en.wikipedia.org/wiki/ISO_8601) która określa, kiedy zadanie jest uruchamiane po raz pierwszy w harmonogramie podstawowym. <p>W przypadku złożonych harmonogramów zadanie rozpoczyna się nie wcześniej niż **startTime**. | 
| **recurrence** | Nie | Reguły cyklu dla po uruchomieniu zadania. Obiekt **cyklu** obsługuje następujące elementy: **częstotliwość,** **interwał,** **harmonogram,** **zliczanie**i **koniecCzas.** <p>Jeśli używasz elementu **cyklu,** należy również użyć elementu **częstotliwości,** podczas gdy inne elementy **cyklu** są opcjonalne. |
| **frequency** | Tak, podczas korzystania z **cyklu** | Jednostka czasu między wystąpieniami i obsługuje te wartości: "Minuta", "Godzina", "Dzień", "Tydzień", "Miesiąc" i "Rok" | 
| **Interwał** | Nie | Dodatnia liczba całkowita określająca liczbę jednostek czasu między wystąpieniami na podstawie **częstotliwości**. <p>Na przykład jeśli **interwał** wynosi 10, a **częstotliwość** to "Tydzień", zadanie powtarza się co 10 tygodni. <p>Oto najwięcej interwałów dla każdej częstotliwości: <p>- 18 miesięcy <br>- 78 tygodni <br>- 548 dni <br>- W przypadku godzin i minut zakres wynosi 1 <= <*interwał*> <= 1000. | 
| **Harmonogram** | Nie | Definiuje zmiany cyklu na podstawie określonych znaczników minut, znaków godzinowych, dni tygodnia i dni miesiąca | 
| **Liczba** | Nie | Dodatnia liczba całkowita określająca liczbę uruchomień zadania przed zakończeniem. <p>Na przykład gdy dzienna liczba zadań ma **ustawioną liczbę** 7, a datą rozpoczęcia jest poniedziałek, zadanie kończy pracę w niedzielę. Jeśli data rozpoczęcia już minęła, pierwsze uruchomienie jest obliczane od czasu utworzenia. <p>Bez **końcaCzas** lub **zliczanie**, zadanie jest uruchamiane w nieskończoność. Nie można używać zarówno **count** i **endTime** w tym samym zadaniu, ale reguła, która kończy się jako pierwszy jest honorowany. | 
| **Endtime** | Nie | Wartość ciągu Date lub DateTime w [formacie ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) określająca, kiedy zadanie przestanie działać. Można ustawić wartość **endTime,** który jest w przeszłości. <p>Bez **końcaCzas** lub **zliczanie**, zadanie jest uruchamiane w nieskończoność. Nie można używać zarówno **count** i **endTime** w tym samym zadaniu, ale reguła, która kończy się jako pierwszy jest honorowany. |
|||| 

Na przykład ten schemat JSON opisuje podstawowy harmonogram i cykl dla zadania: 

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

*Daty i wartości DateTime*

* Daty w zadaniach harmonogramu obejmują tylko datę i są zgodne [ze specyfikacją ISO 8601](https://en.wikipedia.org/wiki/ISO_8601).

* Daty i godziny w zadaniach harmonogramu obejmują zarówno datę, jak i godzinę, należy postępować zgodnie ze [specyfikacją ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)i zakłada się, że są UTC, gdy nie określono przesunięcia UTC. 

Aby uzyskać więcej informacji, zobacz [Pojęcia, terminologia i jednostki](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Szczegóły: startTime

W tej tabeli opisano sposób **uruchamianiaTime** kontroluje sposób wykonywania zadania:

| startTime | Brak nawrotu | Cykl, bez harmonogramu | Cykl z harmonogramem |
|-----------|---------------|-------------------------|--------------------------|
| **Brak czasu rozpoczęcia** | Uruchom raz natychmiast. | Uruchom raz natychmiast. Uruchom późniejsze wykonania obliczone od ostatniego czasu wykonania. | Uruchom raz natychmiast. Uruchom późniejsze wykonania na podstawie harmonogramu cyklu. | 
| **Czas rozpoczęcia w przeszłości** | Uruchom raz natychmiast. | Oblicz pierwszy przyszły czas wykonywania po godzinie rozpoczęcia i uruchom w tym czasie. <p>Uruchom późniejsze wykonania obliczone od ostatniego czasu wykonania. <p>Zobacz przykład po tej tabeli. | Uruchom zadanie *nie wcześniej niż* w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchom późniejsze wykonania na podstawie harmonogramu cyklu. | 
| **Czas rozpoczęcia w przyszłości lub bieżący czas** | Uruchom raz o określonej godzinie rozpoczęcia. | Uruchom raz o określonej godzinie rozpoczęcia. <p>Uruchom późniejsze wykonania obliczone od ostatniego czasu wykonania. | Uruchom zadanie *nie wcześniej niż* w określonym czasie rozpoczęcia. Pierwsze wystąpienie opiera się na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchom późniejsze wykonania na podstawie harmonogramu cyklu. |
||||| 

Załóżmy, że w tym przykładzie z tych warunków: czas rozpoczęcia w przeszłości z cyklu, ale bez harmonogramu.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Bieżąca data i godzina to 8 kwietnia 2015 o godzinie 13:00.

* Data i godzina rozpoczęcia to 07 kwietnia 2015 o godzinie 14:00, czyli przed bieżącą datą i godziną.

* Nawrót jest co dwa dni.

1. W tych warunkach pierwsze wykonanie nastąpi 9 kwietnia 2015 r. o godzinie 14:00. 

   Harmonogram oblicza wystąpienia wykonania na podstawie czasu rozpoczęcia, odrzuca wszystkie wystąpienia w przeszłości i używa następnego wystąpienia w przyszłości. 
   W takim przypadku **startTime** jest 07 kwietnia 2015 o 14:00, więc następne wystąpienie jest dwa dni od tego czasu, który jest 09 kwietnia 2015 o 14:00.

   Pierwsze wykonanie jest taka sama, czy **startTime** jest 2015-04-05 14:00 lub 2015-04-01 14:00. Po pierwszym wykonaniu późniejsze wykonania są obliczane na podstawie harmonogramu. 
   
1. Następnie wykonania są następujące w następującej kolejności: 
   
   1. 2015-04-11 o godz.
   1. 2015-04-13 o godz. 
   1. 2015-04-15 o godz.
   1. I tak dalej...

1. Na koniec, gdy zadanie ma harmonogram, ale nie określono godzin i minut, te wartości domyślnie godzin i minut w pierwszym wykonaniu, odpowiednio.

<a name="schedule"></a>

## <a name="details-schedule"></a>Szczegóły: harmonogram

Harmonogram **można** użyć, aby *ograniczyć* liczbę wykonań zadań. Na przykład jeśli zadanie z **częstotliwością** "miesiąc" ma harmonogram, który działa tylko w dniu 31, zadanie jest uruchamiane tylko w miesiącach, które mają 31 dzień.

Można również użyć **harmonogramu,** aby *zwiększyć* liczbę wykonań zadań. Na przykład jeśli zadanie z **częstotliwością** "miesiąc" ma harmonogram, który działa w dniach miesiąca 1 i 2, zadanie jest uruchamiane w pierwszym i drugim dniu miesiąca, a nie tylko raz w miesiącu.

Jeśli określisz więcej niż jeden element harmonogramu, kolejność oceny jest od największego do najmniejszego: liczba tygodnia, dzień miesiąca, dzień tygodnia, godzina i minuta.

W poniższej tabeli opisano szczegółowo elementy harmonogramu:

| Nazwa JSON | Opis | Prawidłowe wartości |
|:--- |:--- |:--- |
| **minutes** |Protokół z godziny, w której zadanie jest uruchamiane. |Tablica liczby całkowitych. |
| **hours** |Godziny dnia, w którym działa zadanie. |Tablica liczby całkowitych. |
| **weekDays** |Dni tygodnia zadanie jest uruchamiane. Można określić tylko z częstotliwością tygodniową. |Tablica dowolnej z następujących wartości (maksymalny rozmiar tablicy to 7):<br />- "Poniedziałek"<br />- "Wtorek"<br />- "Środa"<br />- "Czwartek"<br />- "Piątek"<br />- "Sobota"<br />- "Niedziela"<br /><br />Nie dotyczy wielkości liter. |
| **monthlyOccurrences** |Określa, w których dniach miesiąca jest uruchamiane zadanie. Można określić tylko z częstotliwością miesięczną. |Tablica **miesięcznychWrozkażanie obiektów:**<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **dzień** jest dniem tygodnia, w którym działa zadanie. Na przykład *{Niedziela}* jest w każdą niedzielę miesiąca. Wymagany.<br /><br />**jest** występowanie dnia w ciągu miesiąca. Na przykład *{Niedziela, -1}* jest ostatnią niedzielą miesiąca. Element opcjonalny. |
| **monthDays** |Dzień miesiąca zadanie jest uruchamiane. Można określić tylko z częstotliwością miesięczną. |Tablica następujących wartości:<br />- Dowolna wartość <= -1 i >= -31<br />- Dowolna wartość >= 1 i <= 31|

## <a name="examples-recurrence-schedules"></a>Przykłady: harmonogramy cyklu

Poniższe przykłady przedstawiają różne harmonogramy cyklu. Przykłady koncentrują się na obiekcie harmonogramu i jego podelementach.

Harmonogramy te zakładają, że **interwał** jest ustawiony na 1\. Przykłady zakładają również poprawne wartości **częstotliwości** dla wartości w **harmonogramie**. Na przykład nie można użyć **częstotliwości** "dzień" i **miesiącDays modyfikacji** w **harmonogramie**. Opisujemy te ograniczenia wcześniej w artykule.

| Przykład | Opis |
|:--- |:--- |
| `{"hours":[5]}` |Codziennie o 5:00.<br /><br />Harmonogram dopasowuje każdą wartość w "godziny" z każdą wartością w "minutach", jeden po drugim, aby utworzyć listę wszystkich czasów, w których zadanie jest uruchamiane. |
| `{"minutes":[15], "hours":[5]}` |Uruchamiany o godz. 5:15 każdego dnia. |
| `{"minutes":[15], "hours":[5,17]}` |Uruchamiany o godz. 5:15 i 17:15 każdego dnia. |
| `{"minutes":[15,45], "hours":[5,17]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 każdego dnia. |
| `{"minutes":[0,15,30,45]}` |Uruchamiany co 15 minut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Uruchamiany co godzinę.<br /><br />To zadanie jest uruchamiane co godzinę. Minuta jest kontrolowana przez wartość **startTime**, jeśli jest określona. Jeśli nie jest określona wartość **startTime,** minuta jest kontrolowana przez czas tworzenia. Na przykład jeśli godzina rozpoczęcia lub czas utworzenia (w zależności od tego, która ma zastosowanie) wynosi 12:25, zadanie jest uruchamiane o 00:25, 01:25, 02:25, ..., 23:25.<br /><br />Harmonogram jest taki sam jak zadanie z **częstotliwością** "godzina", **interwał** 1 i bez wartości **harmonogramu.** Różnica polega na tym, że można użyć tego harmonogramu z różnymi wartościami **częstotliwości** i **interwałów** do tworzenia innych zadań. Na przykład, jeśli **częstotliwość** jest "miesiąc", harmonogram działa tylko raz w miesiącu, a nie codziennie (jeśli **częstotliwość** jest "dzień"). |
| `{minutes:[0]}` |Uruchamiany co godzinę o pełnej godzinie.<br /><br />To zadanie działa również co godzinę, ale w godzinach (12:00, 1:00, 2:00 i tak dalej). Ten harmonogram jest taki sam jak zadanie z **częstotliwością** "godzina", wartość **startTime** zero minut i bez **harmonogramu,** jeśli częstotliwość jest "dzień". Jednakże jeśli **częstotliwość** jest "tydzień" lub "miesiąc", harmonogram wykonuje tylko jeden dzień w tygodniu lub jeden dzień w miesiącu, odpowiednio. |
| `{"minutes":[15]}` |Biegaj po 15 minutach po godzinie co godzinę.<br /><br />Działa co godzinę, począwszy od 00:15, 1:15, 2:15 i tak dalej. Kończy się o 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` |W sobotę co tydzień o godzinie 17:00. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Biegaj co tydzień o godzinie 17:00 w poniedziałki, środy i piątki. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Uruchamiany o godz. 17:15 i 17:45 w każdy poniedziałek, środę i piątek. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Biegaj co tydzień o 17:00 i 17:00 w poniedziałki, środy i piątki. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Biegaj o 5:15, 17:45, 17:15 i 17:45 w poniedziałki, środy i piątki co tydzień. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Uruchamiany co 15 minut w dni robocze. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Biegaj co 15 minut w dni powszednie, od 9:00 do 16:45. |
| `{"weekDays":["sunday"]}` |Bieg w niedziele o godzinie startu. |
| `{"weekDays":["tuesday", "thursday"]}` |Bieg wtorki i czwartki o godzinie rozpoczęcia. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Bieg o godzinie 6:00 28 dnia każdego miesiąca (przy założeniu **częstotliwości** "miesiąca"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |Bieg o 6:00 ostatniego dnia miesiąca.<br /><br />Jeśli chcesz uruchomić zadanie ostatniego dnia miesiąca, użyj -1 zamiast dnia 28, 29, 30 lub 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Bieg o 6:00 pierwszego i ostatniego dnia każdego miesiąca. |
| `{monthDays":[1,-1]}` |Biegaj pierwszego i ostatniego dnia każdego miesiąca o godzinie rozpoczęcia. |
| `{monthDays":[1,14]}` |Bieg pierwszego i 14 dnia każdego miesiąca o godzinie rozpoczęcia. |
| `{monthDays":[2]}` |Uruchom drugiego dnia miesiąca o godzinie rozpoczęcia. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Bieg w pierwszy piątek każdego miesiąca o godzinie 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Bieg w pierwszy piątek każdego miesiąca o godzinie startu. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |Bieg w trzeci piątek od końca miesiąca, co miesiąc, o godzinie rozpoczęcia. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Uruchamiany w pierwszy i ostatni piątek każdego miesiąca o godz. 5:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Biegaj w pierwszy i ostatni piątek każdego miesiąca o godzinie rozpoczęcia. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Bieg w piąty piątek każdego miesiąca o godzinie startu.<br /><br />Jeśli nie ma piątego piątku w miesiącu, zadanie nie zostanie uruchomione. Można rozważyć użycie -1 zamiast 5 dla wystąpienia, jeśli chcesz uruchomić zadanie w ostatni występujący piątek miesiąca. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Uruchamiany co 15 minut w ostatni piątek miesiąca. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Uruchamiany o godz. 5:15, 5:45, 17:15 i 17:45 w trzecią środę każdego miesiąca. |

## <a name="next-steps"></a>Następne kroki

* [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)
* [Dokumentacja interfejsu API REST usługi Azure Scheduler](/rest/api/scheduler)
* [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)
* [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)