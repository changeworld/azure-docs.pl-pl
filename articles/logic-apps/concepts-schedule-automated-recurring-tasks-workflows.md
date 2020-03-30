---
title: Planowanie cyklicznych zadań i przepływów pracy w usłudze Azure Logic Apps
description: Omówienie planowania cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy za pomocą usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 0f6ec158cf6ab855191e6796be3abec7d37439a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270565"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Planowanie i uruchamianie cyklicznych automatycznych zadań, procesów i przepływów pracy przy użyciu usługi Azure Logic Apps

Aplikacja Logika ułatwia tworzenie i uruchamianie zautomatyzowanych zadań cyklicznych i procesów zgodnie z harmonogramem. Tworząc przepływ pracy aplikacji logiki, który rozpoczyna się od wbudowanego wyzwalacza cyklu lub wyzwalacza okna przesuwnego, które są wyzwalaczami typu Harmonogram, można uruchamiać zadania natychmiast, w późniejszym czasie lub w interwale cyklicznym. Można wywołać usługi wewnątrz i na zewnątrz platformy Azure, takie jak punkty końcowe HTTP lub HTTPS, publikować wiadomości w usługach platformy Azure, takich jak Usługa Azure Storage i usługa Azure Service Bus, lub uzyskać pliki przekazywane do udziału plików. Za pomocą wyzwalacza cyklu można również skonfigurować złożone harmonogramy i zaawansowane cykle do uruchamiania zadań. Aby dowiedzieć się więcej o wbudowanych wyzwalaczach i akcjach harmonogramu, zobacz [Planowanie wyzwalaczy](#schedule-triggers) i [Planowanie akcji](#schedule-actions). 

> [!TIP]
> Można zaplanować i uruchomić cykliczne obciążenia bez tworzenia oddzielnej aplikacji logiki dla każdego zaplanowanego zadania i uruchamiania [w limit przepływów pracy na region i subskrypcję](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Zamiast tego można użyć wzorca aplikacji logiki, który jest tworzony przez [szablon Szybki start platformy Azure: Harmonogram zadań aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/).
>
> Szablon harmonogramu zadań aplikacji logiki tworzy aplikację logiki CreateTimerJob, która wywołuje aplikację logiki TimerJob. Następnie można wywołać CreateTimerJob aplikacji logiki jako interfejsu API, wykonując żądanie HTTP i przekazywanie harmonogramu jako dane wejściowe dla żądania. Każde wywołanie createTimerJob aplikacji logiki wywołuje również timerJob logiki aplikacji, która tworzy nowe timerJob wystąpienie, które stale działa na podstawie określonego harmonogramu lub do spełnienia określonego limitu. W ten sposób można uruchomić dowolną liczbę wystąpień TimerJob, nie martwiąc się o limity przepływu pracy, ponieważ wystąpienia nie są indywidualnymi definicjami przepływu pracy aplikacji logiki ani zasobami.

Na tej liście przedstawiono przykładowe zadania, które można uruchamiać za pomocą wbudowanych wyzwalaczy harmonogramu:

* Pobierz dane wewnętrzne, takie jak uruchamianie procedury składowanej SQL każdego dnia.

* Otrzymuj dane zewnętrzne, takie jak ściąganie raportów pogodowych z NOAA co 15 minut.

* Wyślij dane raportu, takie jak wysyłanie pocztą e-mail podsumowania dla wszystkich zamówień większych niż określona kwota w ostatnim tygodniu.

* Przetwarzaj dane, takie jak kompresowanie przesłanych obrazów każdego dnia tygodnia poza godzinami szczytu.

* Wyczyść dane, takie jak usuwanie wszystkich tweetów starszych niż trzy miesiące.

* Archiwizuj dane, takie jak wypychanie faktur do usługi tworzenia kopii zapasowych o godzinie 1:00 każdego dnia przez następne dziewięć miesięcy.

Można również użyć akcji wbudowanych Harmonogram, aby wstrzymać przepływ pracy przed rozpoczęciem następnej akcji, na przykład:

* Poczekaj do dnia tygodnia, aby wysłać aktualizację stanu za pośrednictwem poczty e-mail.

* Opóźnij przepływ pracy, dopóki wywołanie HTTP nie będzie miało czasu na zakończenie przed wznowieniem i pobraniem wyniku.

W tym artykule opisano możliwości wbudowanych wyzwalaczy i akcji harmonogramu.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Zaplanuj wyzwalacze

Przepływ pracy aplikacji logiki można uruchomić przy użyciu wyzwalacza cyklu lub wyzwalacza okna przesuwnego, który nie jest skojarzony z żadną konkretną usługą lub systemem, na przykład z usługą Office 365 Outlook lub SQL Server. Te wyzwalacze uruchamiają i uruchamiają przepływ pracy na podstawie określonego cyklu, w którym można wybrać interwał i częstotliwość, takie jak liczba sekund, minut i godzin dla obu wyzwalaczy lub liczba dni, tygodni lub miesięcy dla wyzwalacza cyklu. Można również ustawić datę i godzinę rozpoczęcia, a także strefę czasową. Za każdym razem, gdy wyzwalacz jest uruchamiany, aplikacje logiki tworzy i uruchamia nowe wystąpienie przepływu pracy dla aplikacji logiki.

Oto różnice między tymi wyzwalaczami:

* **Cykl:** Uruchamia przepływ pracy w regularnych odstępach czasu na podstawie określonego harmonogramu. Jeśli cykle zostaną pominięte, wyzwalacz cyklu nie przetwarza nieodebranych cykli, ale ponownie uruchamia cykle z następnym zaplanowanym interwałem. Można określić datę i godzinę rozpoczęcia, a także strefę czasową. Jeśli wybierzesz "Dzień", możesz określić godziny dnia i minuty godziny, na przykład codziennie o 2:30. Jeśli wybierzesz "Tydzień", możesz również wybrać dni tygodnia, takie jak środa i sobota. Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza cyklu](../connectors/connectors-native-recurrence.md).

* **Okno przesuwne:** Uruchamia przepływ pracy w regularnych odstępach czasu, które obsługują dane w ciągłych fragmentach. Jeśli powtórzeń nie zostaną pominięte, wyzwalacz okna przesuwnego powraca i przetwarza nieodebrane powtórzenia. Można określić datę i godzinę rozpoczęcia, strefę czasową i czas trwania, aby opóźnić każdy cykl w przepływie pracy. Ten wyzwalacz nie ma opcji określania dni, tygodni i miesięcy, godzin dnia, minut godziny i dni tygodnia. Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza Okna przesuwnego](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Planowanie akcji

Po dowolnej akcji w przepływie pracy aplikacji logiki, można użyć delay i delay until akcje, aby przepływ pracy czekać przed następną akcję jest uruchamiany.

* **Opóźnienie:** Zaczekaj na uruchomienie następnej akcji dla określonej liczby jednostek czasu, takich jak sekundy, minuty, godziny, dni, tygodnie lub miesiące. Aby uzyskać więcej informacji, zobacz [Opóźnianie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

* **Opóźnienie do**: Poczekaj, aby uruchomić następną akcję do określonej daty i godziny. Aby uzyskać więcej informacji, zobacz [Opóźnianie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Wzory daty i godziny rozpoczęcia

<a name="start-time"></a>

Oto kilka wzorców, które pokazują, jak można kontrolować cykl z datą i godziną rozpoczęcia oraz jak usługa Aplikacje logiki uruchamia te cykle:

| Godzina rozpoczęcia | Cykl bez harmonogramu | Cykl z harmonogramem (tylko wyzwalacz cyklu) |
|------------|-----------------------------|----------------------------------------------------|
| {brak} | Natychmiast uruchamia pierwsze obciążenie. <p>Uruchamia przyszłe obciążenia na podstawie ostatniego czasu wykonywania. | Natychmiast uruchamia pierwsze obciążenie. <p>Uruchamia przyszłe obciążenia na podstawie określonego harmonogramu. |
| Czas rozpoczęcia w przeszłości | Wyzwalacz **cyklu:** oblicza czasy wykonywania na podstawie określonego czasu rozpoczęcia i odrzuca poprzednie czasy wykonywania. Uruchamia pierwsze obciążenie w następnym przyszłym czasie wykonywania. <p>Uruchamia przyszłe obciążenia na podstawie obliczeń z ostatniego czasu wykonywania. <p><p>**Wyzwalacz okna przesuwnego:** oblicza czasy wykonywania na podstawie określonego czasu rozpoczęcia i honoruje poprzednie czasy wykonywania. <p>Uruchamia przyszłe obciążenia na podstawie obliczeń z określonego czasu rozpoczęcia. <p><p>Aby uzyskać więcej wyjaśnień, zobacz przykład po tej tabeli. | Uruchamia pierwsze obciążenie *nie wcześniej* niż czas rozpoczęcia, na podstawie harmonogramu obliczonego od czasu rozpoczęcia. <p>Uruchamia przyszłe obciążenia na podstawie określonego harmonogramu. <p>**Uwaga:** Jeśli określisz cykl z harmonogramem, ale nie określisz godzin ani minut dla harmonogramu, przyszłe czasy wykonywania są obliczane przy użyciu odpowiednio godzin lub minut od pierwszego czasu wykonywania. |
| Czas rozpoczęcia w chwili obecnej lub w przyszłości | Uruchamia pierwsze obciążenie o określonej godzinie rozpoczęcia. <p>Uruchamia przyszłe obciążenia na podstawie obliczeń z ostatniego czasu wykonywania. | Uruchamia pierwsze obciążenie *nie wcześniej* niż czas rozpoczęcia, na podstawie harmonogramu obliczonego od czasu rozpoczęcia. <p>Uruchamia przyszłe obciążenia na podstawie określonego harmonogramu. <p>**Uwaga:** Jeśli określisz cykl z harmonogramem, ale nie określisz godzin ani minut dla harmonogramu, przyszłe czasy wykonywania są obliczane przy użyciu odpowiednio godzin lub minut od pierwszego czasu wykonywania. |
||||

> [!IMPORTANT]
> Gdy cykle nie określają zaawansowanych opcji planowania, przyszłe cykle są oparte na ostatnim czasie wykonywania.
> Godziny rozpoczęcia dla tych cykli może dryfować z powodu czynników, takich jak opóźnienie podczas wywołania magazynu. Aby upewnić się, że aplikacja logiki nie przegapi cyklu, zwłaszcza gdy częstotliwość jest w dniach lub dłużej, użyj jednej z następujących opcji:
> 
> * Podaj godzinę rozpoczęcia dla cyklu.
> 
> * Określ godziny i minuty, kiedy należy uruchomić cykl przy użyciu **w tych godzinach** i w tych **minutach** właściwości.
> 
> * Użyj [wyzwalacza okna przesuwnego](../connectors/connectors-native-sliding-window.md)zamiast wyzwalacza cyklu.

*Przykład dla przeszłego czasu rozpoczęcia i cyklu, ale bez harmonogramu*

Załóżmy, że bieżąca data i godzina to 8 września 2017 o 13:00. Określ datę i godzinę rozpoczęcia jako 7 września 2017 r. o godzinie 14:00, która jest w przeszłości, i cykl, który jest uruchamiany co dwa dni.

| Godzina rozpoczęcia | Bieżący czas | Cykl | Harmonogram |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09-**07** godz. 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09-**08** godz. 13:00) | Co dwa dni | {brak} |
|||||

W przypadku wyzwalacza cyklu aparat aplikacji logiki oblicza czasy wykonywania na podstawie czasu rozpoczęcia, odrzuca czasy wykonywania w przeszłości, używa następnego przyszłego czasu rozpoczęcia pierwszego uruchomienia i oblicza przyszłe uruchomienia na podstawie ostatniego czasu wykonywania.

Oto jak wygląda ten cykl:

| Godzina rozpoczęcia | Pierwszy czas pracy | Przyszłe czasy uruchamiania |
|------------|----------------|------------------|
| 2017-09-**07** o 14:00 | 2017-09-**09** o 14:00 | 2017-09-**11** o 14:00 </br>2017-09-**13** o 14:00 </br>2017-09-**15** o 14:00 </br>i tak dalej... |
||||

Tak więc, bez względu na to, jak daleko w przeszłości określisz czas rozpoczęcia, na przykład 2017-09-**05** o 14:00 lub 2017-09-**01** o 14:00, twój pierwszy bieg zawsze wykorzystuje następny przyszły czas rozpoczęcia.

W przypadku wyzwalacza okna przesuwnego aparat aplikacji logiki oblicza czasy wykonywania na podstawie godziny rozpoczęcia, honoruje czasy wykonywania w przeszłości, używa czasu rozpoczęcia pierwszego uruchomienia i oblicza przyszłe uruchomienia na podstawie czasu rozpoczęcia.

Oto jak wygląda ten cykl:

| Godzina rozpoczęcia | Pierwszy czas pracy | Przyszłe czasy uruchamiania |
|------------|----------------|------------------|
| 2017-09-**07** o 14:00 | 2017-09-**07** o 14:00 | 2017-09-**09** o 14:00 </br>2017-09-**11** o 14:00 </br>2017-09-**13** o 14:00 </br>2017-09-**15** o 14:00 </br>i tak dalej... |
||||

Tak więc, bez względu na to, jak daleko w przeszłości określisz godzinę rozpoczęcia, na przykład 2017-09-**05** o 14:00 lub 2017-09-**01** o 14:00, twój pierwszy bieg zawsze używa określonego czasu rozpoczęcia.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Przykładowe powtórzenia

Oto różne przykładowe cykle, które można skonfigurować dla wyzwalaczy obsługujących opcje:

| Wyzwalacz | Cykl | Interval | Częstotliwość | Godzina rozpoczęcia | W tych dniach | W tych godzinach | W tych minutach | Uwaga |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Cyklu <br>Okno przewijania | Uruchamianie co 15 minut (bez daty i godziny rozpoczęcia) | 15 | Minuta | {brak} | {niedostępne} | {brak} | {brak} | Ten harmonogram rozpoczyna się natychmiast, a następnie oblicza przyszłe cykle na podstawie ostatniego czasu wykonywania. |
| Cyklu <br>Okno przewijania | Uruchamianie co 15 minut (z datą rozpoczęcia i godziną) | 15 | Minuta | *startDate* T*startTime*Z | {niedostępne} | {brak} | {brak} | Ten harmonogram nie rozpoczyna się wcześniej niż *określona* data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle na podstawie ostatniego czasu wykonywania. |
| Cyklu <br>Okno przewijania | Uruchom co godzinę, na godzinę (z datą i godziną rozpoczęcia) | 1 | Godzina | *startDate* Thh:00:00Z | {niedostępne} | {brak} | {brak} | Ten harmonogram nie uruchamia się *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe cykle są uruchamiane co godzinę przy znaczniku "00" minuty, który jest obliczany od godziny rozpoczęcia. <p>Jeśli częstotliwość to "Tydzień" lub "Miesiąc", ten harmonogram działa odpowiednio tylko jeden dzień w tygodniu lub jeden dzień w miesiącu. |
| Cyklu <br>Okno przewijania | Biegaj co godzinę, codziennie (bez daty i godziny rozpoczęcia) | 1 | Godzina | {brak} | {niedostępne} | {brak} | {brak} | Ten harmonogram rozpoczyna się natychmiast i oblicza przyszłe cykle na podstawie ostatniego czasu wykonywania. <p>Jeśli częstotliwość to "Tydzień" lub "Miesiąc", ten harmonogram działa odpowiednio tylko jeden dzień w tygodniu lub jeden dzień w miesiącu. |
| Cyklu <br>Okno przewijania | Biegaj co godzinę, codziennie (z datą i godziną rozpoczęcia) | 1 | Godzina | *startDate* T*startTime*Z | {niedostępne} | {brak} | {brak} | Ten harmonogram nie rozpoczyna się wcześniej niż *określona* data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle na podstawie ostatniego czasu wykonywania. <p>Jeśli częstotliwość to "Tydzień" lub "Miesiąc", ten harmonogram działa odpowiednio tylko jeden dzień w tygodniu lub jeden dzień w miesiącu. |
| Cyklu <br>Okno przewijania | Biegaj co 15 minut po godzinie, co godzinę (z datą i godziną rozpoczęcia) | 1 | Godzina | *startDate* T00:15:00Z | {niedostępne} | {brak} | {brak} | Ten harmonogram nie uruchamia się *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe cykle są uruchamiane w znaczniku "15" minut, który jest obliczany od czasu rozpoczęcia, więc o 00:15, 1:15, 2:15 i tak dalej. |
| Cykl | Biegaj co 15 minut po godzinie, co godzinę (bez daty i godziny rozpoczęcia) | 1 | Day | {brak} | {niedostępne} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ten harmonogram jest uruchamiany o 00:15, 1:15, 2:15 i tak dalej. Ponadto ten harmonogram jest odpowiednikiem częstotliwości "Godziny" i czasu rozpoczęcia z "15" minut. |
| Cykl | Uruchom co 15 minut w określonych znakach minut (bez daty i godziny rozpoczęcia). | 1 | Day | {brak} | {niedostępne} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram nie rozpoczyna się do następnego określonego znaku 15 minut. |
| Cykl | Uruchamianie codziennie o 8:00 *plus* znak minut od momentu zapisania aplikacji logicznej | 1 | Day | {brak} | {niedostępne} | 8 | {brak} | Bez daty i godziny rozpoczęcia ten harmonogram jest uruchamiany na podstawie czasu zapisywania aplikacji logiki (operacja PUT). |
| Cykl | Bieg codziennie o 8:00 (z datą rozpoczęcia i godziną) | 1 | Day | *startDate* T08:00:00Z | {niedostępne} | {brak} | {brak} | Ten harmonogram nie uruchamia się *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe wydarzenia są uruchamiane codziennie o 8:00. | 
| Cykl | Bieg codziennie o 8:30 (brak daty i godziny rozpoczęcia) | 1 | Day | {brak} | {niedostępne} | 8 | 30 | Ten harmonogram jest uruchamiany codziennie o 8:30. |
| Cykl | Biegaj codziennie o 8:30 i 16:30 | 1 | Day | {brak} | {niedostępne} | 8, 16 | 30 | |
| Cykl | Bieg codziennie o 8:30, 8:45, 16:30 i 16:45 | 1 | Day | {brak} | {niedostępne} | 8, 16 | 30, 45 | |
| Cykl | Bieg w każdą sobotę o godzinie 17:00 (brak daty i godziny rozpoczęcia) | 1 | Tydzień | {brak} | "Bardzo" | 17 | 00 | Harmonogram ten jest uruchamiany w każdą sobotę o godzinie 17:00. |
| Cykl | Bieg w każdą sobotę o godzinie 17:00 (z datą i godziną rozpoczęcia) | 1 | Tydzień | *startDate* T17:00:00Z | "Bardzo" | {brak} | {brak} | Ten harmonogram nie rozpoczyna się wcześniej niż *określona* data i godzina rozpoczęcia, w tym przypadku 9 września 2017 o godzinie 17:00. Przyszłe cykle odbywają się w każdą sobotę o 17:00. |
| Cykl | Uruchom w każdy wtorek, czwartek o 17:00 *plus* minutowy znak od momentu zapisania aplikacji logicznej| 1 | Tydzień | {brak} | "Wtorek", "Czwartek" | 17 | {brak} | |
| Cykl | Uruchamianie co godzinę w godzinach pracy | 1 | Tydzień | {brak} | Wybierz wszystkie dni z wyjątkiem soboty i niedzieli. | Wybierz żądane godziny dnia. | Wybierz dowolną liczbę minut godziny. | Jeśli na przykład godziny pracy to od 8:00 do 17:00, wybierz "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" jako godziny dnia. <p>Jeśli godziny pracy są od 8:30 do 17:30, wybierz poprzednie godziny dnia plus "30" jako minuty godziny. |
| Cykl | Biegaj raz dziennie w weekendy | 1 | Tydzień | {brak} | "Sobota", "Niedziela" | Wybierz żądane godziny dnia. | Wybierz odpowiednie minuty godziny. | Ten harmonogram jest uruchamiany w każdą sobotę i niedzielę zgodnie z określonym harmonogramem. |
| Cykl | Biegaj co 15 minut co dwa lata tylko w poniedziałki | 2 | Tydzień | {brak} | "Bardzo się wys. | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram jest uruchamiany co drugi poniedziałek co 15 minut. |
| Cykl | Biegaj co miesiąc | 1 | Month | *startDate* T*startTime*Z | {niedostępne} | {niedostępne} | {niedostępne} | Ten harmonogram nie rozpoczyna się wcześniej niż *określona* data i godzina rozpoczęcia i oblicza przyszłe cykle w dniu i godzinie rozpoczęcia. Jeśli nie określisz daty i godziny rozpoczęcia, ten harmonogram używa daty i godziny utworzenia. |
| Cykl | Biegaj co godzinę przez jeden dzień w miesiącu | 1 | Month | {patrz uwaga} | {niedostępne} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {patrz uwaga} | Jeśli nie określisz daty i godziny rozpoczęcia, ten harmonogram używa daty i godziny utworzenia. Aby kontrolować minuty dla harmonogramu cyklu, należy określić minuty godziny, godzinę rozpoczęcia lub użyć czasu tworzenia. Na przykład jeśli godzina rozpoczęcia lub tworzenia wynosi 8:25, ten harmonogram jest uruchamiany o 8:25, 9:25, 10:25 i tak dalej. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Uruchamianie tylko raz

Jeśli chcesz uruchomić aplikację logiki tylko w tym samym czasie w przyszłości, można użyć **harmonogram: Uruchom raz zadania** szablonu. Po utworzeniu nowej aplikacji logiki, ale przed otwarciem Projektanta aplikacji logiki, w sekcji **Szablony,** z listy **Kategoria** wybierz pozycję **Harmonogram**, a następnie wybierz ten szablon:

![Wybierz szablon "Harmonogram: Uruchom raz zadania"](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Lub, jeśli można uruchomić aplikację logiki z **When żądanie HTTP jest odbierane — wyzwalacz żądania** i przekazać czas rozpoczęcia jako parametr dla wyzwalacza. W przypadku pierwszej akcji należy użyć akcji **Opóźnienie do — Zaplanuj** i podaj czas rozpoczęcia następnej akcji.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza cyklu](../connectors/connectors-native-recurrence.md)
* [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy za pomocą wyzwalacza okna przesuwnego](../connectors/connectors-native-sliding-window.md)
* [Wstrzymywanie przepływów pracy z akcjami opóźnienia](../connectors/connectors-native-delay.md)
