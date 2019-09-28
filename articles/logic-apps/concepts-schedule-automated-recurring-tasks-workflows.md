---
title: Planowanie cyklicznych zadań i przepływów pracy w Azure Logic Apps
description: Omówienie planowania cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 31260be6c65f5948eba6b9c6228b5ead695278d1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350709"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Planowanie i uruchamianie cyklicznych zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps

Logic Apps pomaga tworzyć i uruchamiać zautomatyzowane powtarzające się zadania i procesy zgodnie z harmonogramem. Tworząc przepływ pracy aplikacji logiki, który rozpoczyna się od wbudowanego wyzwalacza lub ruchomego wyzwalacza okna, które są wyzwalaczami typu harmonogram, można uruchamiać zadania natychmiast, w późniejszym czasie lub cyklicznie. Możesz wywoływać usługi wewnątrz i na zewnątrz platformy Azure, takie jak punkty końcowe HTTP lub HTTPS, wysyłanie komunikatów do usług platformy Azure, takich jak Azure Storage i Azure Service Bus, lub pobieranie plików przekazanych do udziału plików. Wyzwalacz cyklu umożliwia również Konfigurowanie złożonych harmonogramów i zaawansowanych cykli na potrzeby uruchamiania zadań. Aby dowiedzieć się więcej o wbudowanych wyzwalaczach i akcjach harmonogramu, zobacz temat [planowanie wyzwalaczy](#schedule-triggers) i [Planowanie akcji](#schedule-actions). 

> [!TIP]
> Można zaplanować i uruchamiać obciążenia cykliczne bez tworzenia oddzielnej aplikacji logiki dla każdego zaplanowanego zadania i uruchamiania w [limicie przepływów pracy na region i subskrypcję](../logic-apps/logic-apps-limits-and-config.md#definition-limits). Zamiast tego można użyć wzorca aplikacji logiki, który jest tworzony przez [szablon szybkiego startu platformy Azure: Logic Apps harmonogram](https://github.com/Azure/azure-quickstart-templates/tree/master/301-logicapps-jobscheduler/)zadań.
>
> Szablon harmonogramu zadań Logic Apps tworzy aplikację logiki CreateTimerJob, która wywołuje aplikację logiki TimerJob. Następnie można wywołać aplikację logiki CreateTimerJob jako interfejs API, wykonując żądanie HTTP i przekazując harmonogram jako dane wejściowe dla żądania. Każde wywołanie aplikacji logiki CreateTimerJob również wywołuje aplikację logiki TimerJob, która tworzy nowe wystąpienie TimerJob, które jest stale uruchamiane na podstawie określonego harmonogramu lub do czasu spełnienia określonego limitu. Dzięki temu można uruchamiać dowolną liczbę wystąpień TimerJob bez obaw o limity przepływu pracy, ponieważ wystąpienia nie są zależne od definicji przepływu pracy aplikacji logiki ani zasobów.

Ta lista zawiera przykładowe zadania, które można uruchomić za pomocą harmonogramu wbudowane wyzwalacze:

* Pobierz dane wewnętrzne, takie jak uruchamianie procedury składowanej SQL codziennie.

* Pobierz dane zewnętrzne, takie jak ściąganie raportów pogody z NOAA co 15 minut.

* Wyślij dane raportu, takie jak wysyłanie wiadomości e-mail do podsumowania dla wszystkich zamówień o wartości większej niż określona w ubiegłym tygodniu.

* Przetwarzaj dane, takie jak Kompresuj współczesne obrazy, co dzień tygodnia w godzinach poza godzinami szczytu.

* Wyczyść dane, na przykład Usuń wszystkie tweety starsze niż trzy miesiące.

* Archiwizuj dane, np. faktury wypychane do usługi kopii zapasowych w dniu 1:00, codziennie przez następne dziewięć miesięcy.

Możesz również użyć wbudowanych akcji harmonogramu, aby wstrzymać przepływ pracy przed rozpoczęciem następnej akcji, na przykład:

* Poczekaj, aż dzień tygodnia wyśle aktualizację stanu za pośrednictwem poczty e-mail.

* Opóźnij przepływ pracy do momentu zakończenia wywołania HTTP przed wznowieniem i pobraniem wyniku.

W tym artykule opisano możliwości harmonogramu wbudowane wyzwalacze i akcje.

<a name="schedule-triggers"></a>

## <a name="schedule-triggers"></a>Wyzwalacze harmonogramu

Przepływ pracy aplikacji logiki można uruchomić przy użyciu wyzwalacza cykl lub okna przesuwania, które nie jest skojarzone z żadną określoną usługą lub systemem, na przykład Office 365 Outlook lub SQL Server. Te wyzwalacze uruchamiają i uruchamiają przepływy pracy na podstawie określonego cyklu, w którym można wybrać interwał i częstotliwość, takie jak liczba sekund, minut i godzin dla obu wyzwalaczy lub liczba dni, tygodni lub miesięcy dla wyzwalacza cyklu. Możesz również ustawić datę i godzinę rozpoczęcia, a także strefę czasową. Za każdym razem, gdy uruchamiany jest wyzwalacz, Logic Apps tworzy i uruchamia nowe wystąpienie przepływu pracy dla aplikacji logiki.

Poniżej przedstawiono różnice między tymi wyzwalaczami:

* **Cykl**: Uruchamia przepływ pracy w regularnych odstępach czasu zgodnie z określonym harmonogramem. Jeśli pominięto cykle, wyzwalacz cyklu nie przetwarza pominiętych cykli, ale uruchamia ponownie cykle przy następnym zaplanowanym interwale. Możesz określić datę i godzinę rozpoczęcia, a także strefę czasową. W przypadku wybrania opcji "dzień" można określić godziny dnia i minuty godziny, na przykład codziennie o godzinie 2:30. W przypadku wybrania opcji "tydzień" można również wybrać dni tygodnia, takie jak środa i sobota. Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy z wyzwalaczem cyklu](../connectors/connectors-native-recurrence.md).

* **Okno przewijania**: Uruchamia przepływ pracy w regularnych odstępach czasu, które obsługują dane w ciągłych fragmentach. Jeśli pominięto cykle, wyzwalacz okna przesuwania wraca i przetwarza pominięte cykle. Możesz określić datę i godzinę rozpoczęcia, strefę czasową oraz czas trwania, aby opóźnić każdy cykl w przepływie pracy. Ten wyzwalacz nie zawiera opcji określających dni, tygodnie i miesiące, godziny dnia, minuty godziny i dni tygodnia. Aby uzyskać więcej informacji, zobacz [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy przy użyciu wyzwalacza okna przesuwanego](../connectors/connectors-native-sliding-window.md).

<a name="schedule-actions"></a>

## <a name="schedule-actions"></a>Planowanie akcji

Po dodaniu dowolnego działania w przepływie pracy aplikacji logiki można użyć opóźnień i opóźnień do momentu wykonania przepływu pracy przed rozpoczęciem następnej akcji.

* **Opóźnienie**: Zaczekaj na uruchomienie kolejnej akcji dla określonej liczby jednostek czasu, takich jak sekundy, minuty, godziny, dni, tygodnie lub miesiące. Aby uzyskać więcej informacji, zobacz [opóźnianie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

* **Opóźnij do**: Zaczekaj na uruchomienie następnej akcji do określonej daty i godziny. Aby uzyskać więcej informacji, zobacz [opóźnianie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Wzorce dla daty i godziny rozpoczęcia

<a name="start-time"></a>

Poniżej przedstawiono niektóre wzorce pokazujące, jak można kontrolować cykl z datą i godziną rozpoczęcia oraz jak usługa Logic Apps uruchamia następujące cykle:

| Czas rozpoczęcia | Cykl bez harmonogramu | Cykl z harmonogramem (tylko wyzwalacz cykliczny) |
|------------|-----------------------------|----------------------------------------------------|
| dawaj | Natychmiast uruchamia pierwsze obciążenie. <p>Uruchamia przyszłe obciążenia na podstawie czasu ostatniego uruchomienia. | Natychmiast uruchamia pierwsze obciążenie. <p>Uruchamia przyszłe obciążenia zgodnie z określonym harmonogramem. |
| Godzina rozpoczęcia w przeszłości | Wyzwalacz **cyklu** : Oblicza czasy wykonywania na podstawie określonego czasu rozpoczęcia i odrzuca czas wykonywania. Uruchamia pierwsze obciążenie w następnym przyszłym czasie wykonywania. <p>Uruchamia przyszłe obciążenia na podstawie obliczeń od czasu ostatniego uruchomienia. <p><p>Wyzwalacz **okna przewijania** : Oblicza czasy wykonywania na podstawie określonego czasu rozpoczęcia i jest uznawane za czas wykonywania. <p>Uruchamia przyszłe obciążenia na podstawie obliczeń z określonego czasu rozpoczęcia. <p><p>Aby uzyskać więcej wyjaśnień, zobacz przykład poniżej tej tabeli. | Uruchamia pierwsze obciążenie, *nie wcześniej* niż godzina rozpoczęcia, na podstawie harmonogramu obliczonego na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłe obciążenia zgodnie z określonym harmonogramem. <p>**Uwaga:** Jeśli określisz cykl z harmonogramem, ale nie określisz godzin lub minut dla harmonogramu, przyszłe czasy uruchomienia są obliczane odpowiednio przy użyciu godzin lub minut, od pierwszego uruchomienia. |
| Godzina rozpoczęcia lub w przyszłości | Uruchamia pierwsze obciążenie o określonej godzinie rozpoczęcia. <p>Uruchamia przyszłe obciążenia na podstawie obliczeń od czasu ostatniego uruchomienia. | Uruchamia pierwsze obciążenie, *nie wcześniej* niż godzina rozpoczęcia, na podstawie harmonogramu obliczonego na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłe obciążenia zgodnie z określonym harmonogramem. <p>**Uwaga:** Jeśli określisz cykl z harmonogramem, ale nie określisz godzin lub minut dla harmonogramu, przyszłe czasy uruchomienia są obliczane odpowiednio przy użyciu godzin lub minut, od pierwszego uruchomienia. |
||||

*Przykład minionego czasu rozpoczęcia i cyklu, ale bez harmonogramu*

Załóżmy, że bieżąca data i godzina to 8 września 2017 o 1:00 PM. Należy określić datę i godzinę rozpoczęcia jako 7 września 2017 o 2:00 PM, która przypada w przeszłości, a cykl, który jest uruchamiany co dwa dni.

| Czas rozpoczęcia | Bieżący czas | Cykl | Harmonogram |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 –**07** o 2:00 PM) | 2017-09-**08**T13:00:00Z <br>(2017-09 –**08** o 1:00 PM) | Co dwa dni | dawaj |
|||||

W przypadku wyzwalacza cyklicznego aparat Logic Apps oblicza czasy wykonywania na podstawie czasu rozpoczęcia, odrzuca przeszłe czasy wykonywania, używa następnego przyszłego czasu rozpoczęcia pierwszego uruchomienia i oblicza przyszłe uruchomienia w oparciu o czas ostatniego uruchomienia.

Oto jak wygląda ten cykl:

| Czas rozpoczęcia | Czas pierwszego uruchomienia | Przyszłe czasy wykonywania |
|------------|----------------|------------------|
| 2017-09 –**07** o 2:00 PM | 2017-09 –**09** o 2:00 PM | 2017-09 –**11** o 2:00 PM </br>2017-09 –**13** o 2:00 PM </br>2017-09 –**15** o 2:00 PM </br>i tak dalej... |
||||

W związku z tym niezależnie od tego, jak daleko w przeszłości określisz czas rozpoczęcia, na przykład 2017-09-**05** o 2:00 PM lub 2017-09-**01** o 2:00 PM, pierwsze uruchomienie zawsze będzie używane w następnym przyszłym czasie.

Dla wyzwalacza okna przesuwania, aparat Logic Apps oblicza czasy wykonywania na podstawie czasu rozpoczęcia, trwa czas rozpoczęcia pierwszego uruchomienia i oblicza przyszłe uruchomienia na podstawie czasu rozpoczęcia.

Oto jak wygląda ten cykl:

| Czas rozpoczęcia | Czas pierwszego uruchomienia | Przyszłe czasy wykonywania |
|------------|----------------|------------------|
| 2017-09 –**07** o 2:00 PM | 2017-09 –**07** o 2:00 PM | 2017-09 –**09** o 2:00 PM </br>2017-09 –**11** o 2:00 PM </br>2017-09 –**13** o 2:00 PM </br>2017-09 –**15** o 2:00 PM </br>i tak dalej... |
||||

W związku z tym niezależnie od tego, jak daleko w przeszłości określisz czas rozpoczęcia, na przykład 2017-09-**05** o 2:00 PM lub 2017-09-**01** o 2:00 PM, pierwsze uruchomienie zawsze używa określonego czasu rozpoczęcia.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Przykładowe cykle

Poniżej przedstawiono różne przykładowe cykle, które można skonfigurować dla wyzwalaczy, które obsługują opcje:

| Wyzwalacz | Cykl | Interval | Częstotliwość | Czas rozpoczęcia | W tych dniach | W tych godzinach | W tych minutach | Uwaga |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co 15 minut (bez daty i godziny rozpoczęcia) | 15 | Minuta | dawaj | {unavailable} | dawaj | dawaj | Ten harmonogram zaczyna się od razu, a następnie oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. |
| Wystąpieniu <br>Okno przewijania | Uruchamiany co 15 minut (z datą i godziną rozpoczęcia) | 15 | Minuta | *startDate*T*startTime*Z | {unavailable} | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. |
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co godzinę, na godzinę (z datą i godziną rozpoczęcia) | 1 | Godzina | *StartDate* THH: 00:00Z | {unavailable} | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe cykle są uruchamiane co godzinę w znaku "00" minuty, który jest obliczany na podstawie czasu rozpoczęcia. <p>Jeśli częstotliwość wynosi "tydzień" lub "miesiąc", ten harmonogram odpowiednio uruchamia tylko jeden dzień na tydzień lub jeden dzień miesięcznie. |
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co godzinę, codziennie (bez daty i godziny rozpoczęcia) | 1 | Godzina | dawaj | {unavailable} | dawaj | dawaj | Ten harmonogram zaczyna się od razu i oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. <p>Jeśli częstotliwość wynosi "tydzień" lub "miesiąc", ten harmonogram odpowiednio uruchamia tylko jeden dzień na tydzień lub jeden dzień miesięcznie. |
| Wystąpieniu <br>Okno przewijania | Uruchamiaj co godzinę, codziennie (z datą i godziną rozpoczęcia) | 1 | Godzina | *startDate*T*startTime*Z | {unavailable} | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle na podstawie czasu ostatniego uruchomienia. <p>Jeśli częstotliwość wynosi "tydzień" lub "miesiąc", ten harmonogram odpowiednio uruchamia tylko jeden dzień na tydzień lub jeden dzień miesięcznie. |
| Wystąpieniu <br>Okno przewijania | Uruchamiany co 15 minut po godzinie, co godzinę (z datą i godziną rozpoczęcia) | 1 | Godzina | *StartDate* T00:15:00Z | {unavailable} | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe cykle są uruchamiane z oznaczeniem "15" minut, który jest obliczany na podstawie czasu rozpoczęcia, więc o 00:15 AM, 1:15 AM, 2:15 AM i tak dalej. |
| Cykl | Uruchamiaj co 15 minut za godzinę, co godzinę (bez daty i godziny rozpoczęcia) | 1 | Day | dawaj | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ten harmonogram jest uruchamiany o godzinie 00:15, 1:15, 2:15 AM i tak dalej. Ponadto ten harmonogram jest równoważny z częstotliwością "godzina" i godziną rozpoczęcia z "15" min. |
| Cykl | Uruchamiany co 15 minut w określonych minutach (bez daty i godziny rozpoczęcia). | 1 | Day | dawaj | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram nie zostanie uruchomiony do następnego określonego 15-minutowego znacznika. |
| Cykl | Uruchamiaj codziennie o godzinie *8 am i od* momentu zapisania aplikacji logiki | 1 | Day | dawaj | {unavailable} | 8 | dawaj | Bez daty i godziny rozpoczęcia ten harmonogram jest uruchamiany w oparciu o godzinę zapisywania aplikacji logiki (PUT operację). |
| Cykl | Uruchom codziennie o godzinie 8:00 (z datą i godziną rozpoczęcia) | 1 | Day | *StartDate* T08:00:00Z | {unavailable} | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe wystąpienia są uruchamiane codziennie o godzinie 8:00. | 
| Cykl | Uruchom codziennie o godzinie 8:30 (bez daty i godziny rozpoczęcia) | 1 | Day | dawaj | {unavailable} | 8 | 30 | Ten harmonogram jest uruchamiany codziennie o godzinie 8:30. |
| Cykl | Uruchom codziennie o godzinie 8:30 AM i 4:30 PM | 1 | Day | dawaj | {unavailable} | 8, 16 | 30 | |
| Cykl | Uruchom codziennie o godzinie 8:30, 8:45, 4:30 PM i 4:45 PM | 1 | Day | dawaj | {unavailable} | 8, 16 | 30, 45 | |
| Cykl | Uruchamiaj każdą sobotę o godzinie 5 PM (bez daty i godziny rozpoczęcia) | 1 | Tydzień | dawaj | Sobotę | 17 | 00 | Ten harmonogram jest uruchamiany w każdą sobotę o godzinie 5:00 PM. |
| Cykl | Uruchamiaj każdą sobotę o godzinie 5 PM (z datą i godziną rozpoczęcia) | 1 | Tydzień | *StartDate* T17:00:00Z | Sobotę | dawaj | dawaj | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, w tym przypadku 9 września 2017 o 5:00 PM. Przyszłe cykle są uruchamiane w każdą sobotę o godzinie 5:00 PM. |
| Cykl | Uruchamiaj każdy wtorek, czwartek o godzinie 5 PM *oraz* oznaczenie minuty od momentu zapisania aplikacji logiki| 1 | Tydzień | dawaj | "Wtorek", "czwartek" | 17 | dawaj | |
| Cykl | Uruchamiaj co godzinę w godzinach pracy | 1 | Tydzień | dawaj | Wybierz wszystkie dni z wyjątkiem soboty i niedziela. | Wybierz godziny, których chcesz użyć. | Wybierz dowolną liczbę minut, które chcesz wykonać. | Na przykład jeśli godziny pracy mają 8:00 AM do 5:00 PM, wybierz wartość "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" jako godziny korzystania z tego dnia. <p>Jeśli godziny pracy są 8:30 od do 5:30 PM, wybierz poprzednie godziny dnia i "30" jako minuty godziny. |
| Cykl | Uruchamiaj raz dziennie w weekendy | 1 | Tydzień | dawaj | "Sobota", "Niedziela" | Wybierz godziny, których chcesz użyć. | Wybierz dowolną liczbę minut w odpowiedniej godzinie. | Ten harmonogram jest uruchamiany co soboty i niedziela zgodnie z określonym harmonogramem. |
| Cykl | Uruchamiany co 15 minut, co dwa tygodnie w poniedziałek | 2 | Tydzień | dawaj | Poniedziałek | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram jest uruchamiany co drugi poniedziałek przy co 15 minut. |
| Cykl | Uruchamiaj co miesiąc | 1 | Miesiąc | *startDate*T*startTime*Z | {unavailable} | {unavailable} | {unavailable} | Ten harmonogram nie jest uruchamiany *wcześniej* niż określona data i godzina rozpoczęcia, a następnie oblicza przyszłe cykle dla daty i godziny rozpoczęcia. Jeśli nie określisz daty i godziny rozpoczęcia, w tym harmonogramie zostanie użyta Data i godzina utworzenia. |
| Cykl | Uruchamiaj co godzinę przez jeden dzień miesięcznie | 1 | Miesiąc | {patrz Uwaga} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {patrz Uwaga} | Jeśli nie określisz daty i godziny rozpoczęcia, w tym harmonogramie zostanie użyta Data i godzina utworzenia. Aby kontrolować minuty dla harmonogramu cyklu, określ minuty godziny, godzinę rozpoczęcia lub Użyj czasu tworzenia. Na przykład, jeśli czas rozpoczęcia lub czas utworzenia to 8:25 AM, ten harmonogram jest uruchamiany o 8:25 AM, 9:25 AM, 10:25 AM i tak dalej. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Uruchom tylko raz

Jeśli chcesz uruchomić aplikację logiki tylko raz w przyszłości, możesz użyć **harmonogramu: Uruchamiany po szablonie** zadań. Po utworzeniu nowej aplikacji logiki, ale przed otwarciem programu Logic Apps Designer, w sekcji **Szablony** z listy **Kategoria** wybierz pozycję **harmonogram**, a następnie wybierz pozycję Ten szablon:

![Wybierz pozycję "Harmonogram: Uruchom raz na szablon zadań](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Lub, jeśli możesz uruchomić aplikację logiki przy użyciu **żądania HTTP i odebrać wyzwalacz żądania** , a następnie Przekaż czas rozpoczęcia jako parametr dla wyzwalacza. W przypadku pierwszej akcji Użyj Opóźnij **do-Schedule** akcji, a następnie podaj godzinę uruchomienia następnej akcji.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie, planowanie i uruchamianie cyklicznych zadań i przepływów pracy przy użyciu wyzwalacza cykl](../connectors/connectors-native-recurrence.md)
* [Twórz, Zaplanuj i uruchamiaj zadania cykliczne i przepływy pracy z wyzwalaczem okna przesuwanego](../connectors/connectors-native-sliding-window.md)
* [Wstrzymywanie przepływów pracy z opóźnionymi akcjami](../connectors/connectors-native-delay.md)
