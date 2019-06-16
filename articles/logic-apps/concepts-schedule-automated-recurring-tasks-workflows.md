---
title: Planowanie zadań cyklicznych i przepływów pracy w usłudze Azure Logic Apps
description: Przegląd informacji o planowaniu zadań cyklicznych automatycznych, procesów i przepływów pracy za pomocą usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 7f15dc5b28a44dc8405e2f0524913e6012ebe380
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66356043"
---
# <a name="schedule-and-run-recurring-automated-tasks-processes-and-workflows-with-azure-logic-apps"></a>Planowanie i uruchamianie automatycznych powtarzających się zadań, procesów i przepływów pracy z usługą Azure Logic Apps

Usługa Logic Apps ułatwia tworzenie i uruchamianie automatyczne i powtarzalne zadania i procesy zgodnie z harmonogramem. Tworząc przepływ pracy aplikacji logiki, która rozpoczyna się od wbudowanych wyzwalacza cyklu lub oknie kroczącym, będące wyzwalaczy typu harmonogramu można uruchamiać zadania od razu, w późniejszym czasie, lub na interwał powtarzania. Możesz wywoływać usługi z i spoza platformy Azure, takich jak punkty końcowe HTTP lub HTTPS, umieszczać komunikaty do usług platformy Azure, takich jak Azure Storage i Azure Service Bus lub pobieranie plików przekazany do udziału plików. Za pomocą wyzwalacza, można również skonfigurować złożonych harmonogramów i zaawansowanych cykli do uruchamiania zadań. Aby uzyskać więcej informacji o wbudowanych wyzwalaczach harmonogramu i akcji, zobacz [harmonogramu i wykonywania cyklicznych automatycznych, zadań i przepływów pracy za pomocą usługi Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Poniżej przedstawiono kilka przykładów, które pokazują rodzaje zadań, które można uruchomić:

* Pobieranie danych wewnętrznych, taki jak przebieg procedury składowane SQL każdego dnia.

* Pobierz dane zewnętrzne, takie jak raporty o pogodzie ściągnięcia z agencji NOAA co 15 minut.

* Wyślij dane raportu, takie jak wiadomości e-mail podsumowanie wszystkich zamówień przekracza określoną ilością w ostatnim tygodniu.

* Przetwarzania danych, takich jak dzisiaj Kompresuj przekazał obrazów w każdy dzień tygodnia, poza godzinami szczytu.

* Czyszczenie danych, np. Usuń wszystkie tweety starsze niż trzy miesiące.

* Archiwizowanie danych, takie jak faktury wypychane do usługi kopii zapasowej o 1:00 każdego dnia sprawdzać następne dziewięć miesięcy.

Aby wstrzymać przepływ pracy przed uruchomieniem następnej akcji, na przykład umożliwia także wbudowane akcje harmonogramu:

* Poczekaj, aż weekday do wysyłania aktualizacji stanu za pośrednictwem poczty e-mail.

* Opóźnienie przepływu pracy, dopóki wywołania HTTP ma czasu na zakończenie przed wznowieniem i pobieranie wyników.

W tym artykule opisano funkcje wbudowane wyzwalaczach harmonogramu i akcji.

## <a name="schedule-triggers"></a>Wyzwalacze harmonogramu

Przepływ pracy aplikacji logiki można uruchomić przy użyciu wyzwalacza lub wyzwalacz przesuwanego okna, które nie są skojarzone z określonej usługi lub systemu, na przykład usługi Office 365 Outlook lub programu SQL Server. Te wyzwalacze Uruchom i uruchomić przepływ pracy na podstawie Twojej określonego cyklu wybierania interwał i częstotliwość, takie jak liczba sekund, minut i godziny, zarówno wyzwalaczy lub liczbę dni, tygodnie lub miesiące w przypadku wyzwalacza. Można również ustawić daty rozpoczęcia i czas, a także strefy czasowej. Każdorazowo, gdy wyzwalacza, Logic Apps tworzy i uruchamia nowe wystąpienie przepływu pracy aplikacji logiki.

Poniżej przedstawiono różnice między tych wyzwalaczy:

* **Cykl**: Uruchamia przepływ pracy w regularnych odstępach czasu na podstawie zgodnie z określonym harmonogramem. W przypadku zostaną pominięte cykle, wyzwalacz cykliczny nie przetwarza pominięte cykle, ale powoduje ponowne uruchomienie cykli przy następnym zaplanowanym interwale. Można określić daty rozpoczęcia i czas, a także strefy czasowej. Jeśli wybierzesz opcję "Day", można określić godziny, dnia i minuty godziny, na przykład codziennie o godzinie 2:30. Jeśli wybierzesz opcję "Week", możesz wybrać dni tygodnia, takie jak środa i sobota. Aby uzyskać więcej informacji, zobacz [Utwórz, harmonogramu i wykonywania powtarzających się zadań i przepływów pracy za pomocą wyzwalacza](../connectors/connectors-native-recurrence.md).

* **Oknem kroczącym**: Uruchamia przepływ pracy w regularnych odstępach czasu, które obsługują dane we fragmentach ciągłe. Jeśli zostaną pominięte cykle, wyzwalacz przesuwanego okna powraca i przetwarza pominięte cykle. Można określić daty rozpoczęcia i czas, strefę czasową i czas trwania opóźnienia każdy cykl w przepływie pracy. Ten wyzwalacz nie ma opcji, aby określić dni, tygodnie i miesiące, godzin dnia, minuty, godziny i dni tygodnia. Aby uzyskać więcej informacji, zobacz [Utwórz, harmonogramu i wykonywania powtarzających się zadań i przepływów pracy za pomocą wyzwalacza przesuwanego okna](../connectors/connectors-native-sliding-window.md).

## <a name="schedule-actions"></a>Harmonogram działania

Po żadnych działań w przepływie pracy aplikacji logiki umożliwia akcji opóźnienia i opóźnienia do czasu oczekiwania przed następnym uruchomieniu akcji przepływ pracy.

* **Opóźnienie**: Poczekaj na uruchomienie następnej akcji dla określonej liczby jednostek czasu, takich jak sekundy, minuty, godziny, dni, tygodnie lub miesiące. Aby uzyskać więcej informacji, zobacz [opóźnienie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

* **Opóźnienie do**: Poczekaj, aby uruchomić akcję dalej, aż do określonej daty i godziny. Aby uzyskać więcej informacji, zobacz [opóźnienie następnej akcji w przepływach pracy](../connectors/connectors-native-delay.md).

## <a name="patterns-for-start-date-and-time"></a>Data i godzina rozpoczęcia wzorce

<a name="start-time"></a>

Poniżej przedstawiono niektóre wzorce pokazujące, jak można kontrolować cykli z użyciem Data i godzina rozpoczęcia, a działania te cykli usługi Logic Apps:

| Godzina rozpoczęcia | Cykl bez harmonogramu | Cykl z harmonogramem (tylko wyzwalacz cykliczny) |
|------------|-----------------------------|----------------------------------------------------|
| {Brak} | Uruchamia natychmiast pierwszy obciążenia. <p>Uruchamia przyszłych obciążeń na podstawie ostatniego czasu wykonywania. | Uruchamia natychmiast pierwszy obciążenia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. |
| Godzina rozpoczęcia w przeszłości | **Cykl** wyzwalacza: Oblicza czas wykonywania na podstawie określonym czasie rozpoczęcia i czas odrzucenia ostatnie uruchomienie. Uruchamia pierwszy obciążenie w przyszłości dalej, czas wykonywania. <p>Uruchamia przyszłych obciążeń na podstawie obliczeń z ostatniego czasu wykonywania. <p><p>**Oknem kroczącym** wyzwalacza: Oblicza czas wykonywania w oparciu o określonym czasie rozpoczęcia i godziny ostatnich uwzględniane uruchamianie. <p>Uruchamia przyszłych obciążeń na podstawie obliczeń z w określonym czasie rozpoczęcia. <p><p>Aby uzyskać więcej informacji zobacz przykład pod tą tabelą. | Uruchamia obciążenia pierwszego *nie wcześniej* niż czas rozpoczęcia opierają na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. <p>**Uwaga:** Jeśli Określ cykl z harmonogramem, ale nie określono, godziny lub minuty dla harmonogramu, następnie przyszłych czas wykonywania są obliczane przy użyciu godzin lub minut, odpowiednio, od momentu pierwszego uruchomienia. |
| Czas rozpoczęcia obecnie i w przyszłości | Uruchamia pierwszy obciążenia o określonej godzinie. <p>Uruchamia przyszłych obciążeń na podstawie obliczeń z ostatniego czasu wykonywania. | Uruchamia obciążenia pierwszego *nie wcześniej* niż czas rozpoczęcia opierają na harmonogramie obliczonym na podstawie czasu rozpoczęcia. <p>Uruchamia przyszłych obciążeń na podstawie określonego harmonogramu. <p>**Uwaga:** Jeśli Określ cykl z harmonogramem, ale nie określono, godziny lub minuty dla harmonogramu, następnie przyszłych czas wykonywania są obliczane przy użyciu godzin lub minut, odpowiednio, od momentu pierwszego uruchomienia. |
||||

*Przykład dotyczący ostatnich czas rozpoczęcia i cyklu, ale bez harmonogramu*

Załóżmy, że bieżąca data i godzina jest 8 września 2017 r. o 13:00. Data i godzina rozpoczęcia można określić jako 7 września 2017 r. o 14:00, który znajduje się w przeszłości i cyklu, które jest uruchamiane co 2 dni.

| Godzina rozpoczęcia | Bieżący czas | Cykl | Harmonogram |
|------------|--------------|------------|----------|
| 2017-09-**07**T14:00:00Z <br>(2017-09 -**07** o 14:00) | 2017-09-**08**T13:00:00Z <br>(2017-09 -**08** o 13:00) | Co 2 dni | {Brak} |
|||||

Wyzwalacz cykliczny aplikacji logiki aparatu oblicza uruchomić razy na podstawie czasu uruchomienia odrzuca upłynął czas wykonywania, używa dalej przyszłość godzina rozpoczęcia dla pierwszego uruchomienia i oblicza przyszłość jest uruchamiana na podstawie ostatniego czasu wykonywania.

Oto jak wygląda cykl:

| Godzina rozpoczęcia | Uruchom po raz pierwszy | Przyszłość czasy uruchomienia |
|------------|----------------|------------------|
| 2017-09 -**07** o 14:00 | 2017-09 -**09** o 14:00 | 2017-09 -**11** o 14:00 </br>2017-09 -**13** o 14:00 </br>2017-09 -**15** o 14:00 </br>i tak dalej... |
||||

Tak, niezależnie od tego jak daleko w przeszłości, na przykład określić godzinę rozpoczęcia, 2017-09 -**05** o 14:00 lub 2017-09 -**01** o 14:00, pierwszego uruchomienia zawsze używa czas rozpoczęcia następnego przyszłość.

Oknie kroczącym wyzwalacza aplikacji logiki aparatu oblicza uruchomić razy na podstawie czasu uruchomienia uwzględniane w ciągu ostatnich czasy uruchomienia, używa czasu rozpoczęcia pierwszego uruchomienia i oblicza przyszłych uruchomień, na podstawie czasu uruchomienia.

Oto jak wygląda cykl:

| Godzina rozpoczęcia | Uruchom po raz pierwszy | Przyszłość czasy uruchomienia |
|------------|----------------|------------------|
| 2017-09 -**07** o 14:00 | 2017-09 -**07** o 14:00 | 2017-09 -**09** o 14:00 </br>2017-09 -**11** o 14:00 </br>2017-09 -**13** o 14:00 </br>2017-09 -**15** o 14:00 </br>i tak dalej... |
||||

Tak, niezależnie od tego jak daleko w przeszłości, na przykład określić godzinę rozpoczęcia, 2017-09 -**05** o 14:00 lub 2017-09 -**01** o 14:00, pierwszego uruchomienia zawsze używa określonego czas rozpoczęcia.

<a name="example-recurrences"></a>

## <a name="example-recurrences"></a>Przykład cykli

Oto różnych powtórzenia przykładzie można skonfigurować dla wyzwalaczy, które obsługują opcje:

| Wyzwalacz | Cykl | Interval | Częstotliwość | Godzina rozpoczęcia | W tych dniach | W tych godzinach | W tych minutach | Uwaga |
|---------|------------|----------|-----------|------------|---------------|----------------|------------------|------|
| Cykl <br>Okno przewijania | Uruchamiany co 15 minut (nie Data i godzina rozpoczęcia) | 15 | Minuta | {Brak} | {unavailable} | {Brak} | {Brak} | Ten harmonogram jest uruchamiany natychmiast, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. |
| Cykl <br>Okno przewijania | Uruchamiany co 15 minut (przy użyciu Data i godzina rozpoczęcia) | 15 | Minuta | *startDate*T*startTime*Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data rozpoczęcia i godzina, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. |
| Cykl <br>Okno przewijania | Uruchamiany co godzinę, o pełnej godzinie (przy użyciu Data i godzina rozpoczęcia) | 1 | Godzina | *Oprócz parametru startDate*Thh:00:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe powtórzenia uruchamiane co godzinę o "00" Oznacz minuty jest obliczana na podstawie czasu rozpoczęcia. <p>Jeśli częstotliwość wynosi "Week" lub "Month", ten harmonogram jest uruchamiany odpowiednio tylko raz w tygodniu lub raz na miesiąc. |
| Cykl <br>Okno przewijania | Uruchamiany co godzinę, codziennie (nie Data i godzina rozpoczęcia) | 1 | Godzina | {Brak} | {unavailable} | {Brak} | {Brak} | Ten harmonogram jest uruchamiany natychmiast i oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. <p>Jeśli częstotliwość wynosi "Week" lub "Month", ten harmonogram jest uruchamiany odpowiednio tylko raz w tygodniu lub raz na miesiąc. |
| Cykl <br>Okno przewijania | Uruchamiany co godzinę, codziennie (z Data i godzina rozpoczęcia) | 1 | Godzina | *startDate*T*startTime*Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data rozpoczęcia i godzina, a następnie oblicza przyszłe powtórzenia na podstawie ostatniego czasu wykonywania. <p>Jeśli częstotliwość wynosi "Week" lub "Month", ten harmonogram jest uruchamiany odpowiednio tylko raz w tygodniu lub raz na miesiąc. |
| Cykl <br>Okno przewijania | Uruchom co 15 minut po pełnej godzinie, co godzinę (przy użyciu Data i godzina rozpoczęcia) | 1 | Godzina | *startDate*T00:15:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data i godzina rozpoczęcia. Przyszłe powtórzenia Uruchom pozycji "15" minuty, który jest obliczany od samego początku czasu, dlatego o 00:15, 1:15:00, 2:15:00 i tak dalej. |
| Cykl | Uruchamiany co 15 minut po pełnej godzinie, co godzinę (nie daty i godziny rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 15 | Ten harmonogram jest uruchamiany o 00:15, 1:15:00, 2:15:00, i tak dalej. Ponadto ten harmonogram jest odpowiednikiem częstotliwości "Hour" i czas rozpoczęcia "15" minut. |
| Cykl | Uruchamiany co 15 minut w określonym znaczniki minuty (nie Data i godzina rozpoczęcia). | 1 | Dzień | {Brak} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram nie zaczyna się do czasu następnego określony znak 15 minut. |
| Cykl | Uruchamiany o 8:00:00 każdego dnia (nie Data i godzina rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 8 | {Brak} | Ten harmonogram jest uruchamiany o 8:00 każdego dnia na podstawie określonego harmonogramu. |
| Cykl | Uruchamiany o 8:00:00 każdego dnia (o datę i godzinę rozpoczęcia) | 1 | Dzień | *Oprócz parametru startDate*T08:00:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram uruchamia 8:00:00 każdego dnia, w oparciu o określonej godzinie. | 
| Cykl | Uruchamiany o 8:30:00 każdego dnia (nie Data i godzina rozpoczęcia) | 1 | Dzień | {Brak} | {unavailable} | 8 | 30 | Ten harmonogram jest uruchamiany o 8:30:00 każdego dnia na podstawie określonego harmonogramu. |
| Cykl | Uruchamiany o 8:30:00 każdego dnia (o datę i godzinę rozpoczęcia) | 1 | Dzień | *Oprócz parametru startDate*T08:30:00Z | {unavailable} | {Brak} | {Brak} | Ten harmonogram jest uruchamiany w określona data rozpoczęcia o 8:30:00. |
| Cykl | Uruchamiany o 8:30:00 i 16:30:00 każdego dnia | 1 | Dzień | {Brak} | {unavailable} | 8, 16 | 30 | |
| Cykl | Uruchamiany o 8:30:00, 8:45:00, 16:30:00 i 16:45:00 każdego dnia | 1 | Dzień | {Brak} | {unavailable} | 8, 16 | 30, 45 | |
| Cykl | Uruchom w każdą sobotę o godzinie 17 (nie Data i godzina rozpoczęcia) | 1 | Tydzień | {Brak} | "Sobota" | 17 | 00 | Ten harmonogram jest uruchamiany w każdą sobotę o godzinie 17:00. |
| Cykl | Uruchom w każdą sobotę o godzinie 17 (przy użyciu Data i godzina rozpoczęcia) | 1 | Tydzień | *Oprócz parametru startDate*T17:00:00Z | "Sobota" | {Brak} | {Brak} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data i godzina rozpoczęcia, w tym przypadku 9 września 2017 r. o godzinie 17:00. Przyszłe powtórzenia uruchamiania w każdą sobotę o godzinie 17:00. |
| Cykl | Uruchomienia każdy wtorek, czwartek o godzinie 17: 00 | 1 | Tydzień | {Brak} | "Wtorek", "Czwartek" | 17 | {Brak} | Ten harmonogram jest uruchamiany każdy wtorek i czwartek o godzinie 17:00. |
| Cykl | Uruchamiany co godzinę poza godzinami pracy | 1 | Tydzień | {Brak} | Wybierz wszystkie dni, z wyjątkiem soboty i niedziele. | Wybierz godzin dnia, którym chcesz. | Zaznacz wszystkie minut godzinę, która ma. | Na przykład jeśli godziny pracy są 8:00 AM do 5:00 PM, następnie wybierz pozycję "8, 9, 10, 11, 12, 13, 14, 15, 16, 17" jako godzin dnia. <p>Godziny pracy w przypadku 8:30:00 do 5:30 PM, należy wybrać ostatnich godzin dnia oraz "30" jako minuty godziny. |
| Cykl | Uruchom jeden raz każdego dnia w weekendy | 1 | Tydzień | {Brak} | "Sobota", "Niedziela" | Wybierz godzin dnia, którym chcesz. | Wybierz wszystkie minuty godziny zgodnie z potrzebami. | Ten harmonogram jest uruchamiany co sobota i niedziela z określonym harmonogramem. |
| Cykl | Uruchamiany co 15 minut, co dwa tygodnie w poniedziałek tylko | 2 | Tydzień | {Brak} | "Poniedziałek" | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | 0, 15, 30, 45 | Ten harmonogram jest uruchamiany co drugi poniedziałek pozycji co 15 minut. |
| Cykl | Uruchom co miesiąc | 1 | Miesiąc | *startDate*T*startTime*Z | {unavailable} | {unavailable} | {unavailable} | Ten harmonogram nie zaczyna się *wszelkie wcześniej* niż określona data i godzina rozpoczęcia i oblicza przyszłe powtórzenia na datę i godzinę rozpoczęcia. Jeśli nie określisz Data i godzina rozpoczęcia tego harmonogramu używa daty i godziny. |
| Cykl | Uruchamiany co godzinę przez jeden dzień w miesiącu | 1 | Miesiąc | {patrz Uwaga} | {unavailable} | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23 | {patrz Uwaga} | Jeśli nie określisz Data i godzina rozpoczęcia tego harmonogramu używa daty i godziny. Aby kontrolować minuty dla harmonogramu cyklu, określ minuty godziny, czas rozpoczęcia lub czas utworzenia. Na przykład jeśli czas rozpoczęcia lub godzina utworzenia 8:25:00, ten harmonogram jest wykonywany o 8:25:00, 9:25:00, 10:25:00, i tak dalej. |
|||||||||

<a name="run-once"></a>

## <a name="run-one-time-only"></a>Uruchom tylko jeden raz

Jeśli chcesz uruchomić aplikację logiki, tylko w tym samym czasie w przyszłości, możesz użyć **harmonogramu: Uruchom raz zadania** szablonu. Po utworzeniu nowej aplikacji logiki, ale przed otwarciem w obszarze Projektant aplikacji logiki **szablony** sekcji z **kategorii** listy wybierz pozycję **harmonogram**, a następnie wybierz pozycję Ten szablon:

![Wybierz pozycję "harmonogramu: Uruchamianie zadania raz"szablonu](./media/concepts-schedule-automated-recurring-tasks-workflows/choose-run-once-template.png)

Lub, jeśli można uruchomić aplikację logiki z **zostanie odebrane żądanie po HTTP - żądania** wyzwalacza i przekazać czas rozpoczęcia jako parametr dla wyzwalacza. W przypadku pierwszej akcji, użyj **opóźnienie do — Planowanie** akcji i podaj czas podczas uruchamiania następnej akcji.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza](../connectors/connectors-native-recurrence.md)
* [Tworzenie, planowanie i uruchamianie zadań cyklicznych i przepływów pracy za pomocą wyzwalacza przesuwanego okna](../connectors/connectors-native-sliding-window.md)
* [Wstrzymywanie przepływów pracy akcji opóźnienia](../connectors/connectors-native-delay.md)