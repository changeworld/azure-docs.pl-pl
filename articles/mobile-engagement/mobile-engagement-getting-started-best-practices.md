---
title: Wprowadzenie do usługi Azure Mobile Engagement — przewodnik z najlepszymi rozwiązaniami
description: Przewodnik wprowadzający do usługi Azure Mobile Engagement i najlepsze rozwiązania dotyczące dołączania
services: mobile-engagement
documentationcenter: mobile
author: wesmc7777
manager: erikre
editor: ''
ms.assetid: dfce1183-6398-466e-aa7e-ed702fb52818
ms.service: mobile-engagement
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/04/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: cf9dc5febeae27473d2adb8a024536b945bb9d13
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Wprowadzenie do usługi Azure Mobile Engagement — przewodnik z najlepszymi rozwiązaniami
> [!IMPORTANT]
> Usługa Azure Mobile Engagement zostanie wycofana w dniu 31-03-2018. Ta strona zostanie usunięta wkrótce potem.
> 

## <a name="overview"></a>Omówienie
**Ekrany urządzeń przenośnych stają się coraz bardziej zatłoczone —** badanie przeprowadzone w 2013 r. ujawniło, że na urządzeniach przenośnych jest zainstalowanych średnio po 27 aplikacji. Na ich używanie użytkownicy zazwyczaj poświęcają 30 godzin w miesiącu. Większość tego czasu zajmuje korzystanie z sieci społecznościowych i gier (około 20 godzin). Na początku 2014 r. rynek aplikacji dla systemu Android obejmował około 1,5 miliona pozycji. W sklepie Apple Store dostępnych było mniej więcej 1,2 miliona aplikacji. Aplikacje mobilne wciąż zyskują na popularności, a ich deweloperzy konkurują ze sobą na tym rosnącym rynku. 

Przeciętny użytkownik urządzeń przenośnych często instaluje i odinstalowuje aplikacje, co jest efektem zmieniających się zainteresowań i doświadczeń dotyczących używania aplikacji. Aby ocenić powodzenie aplikacji, nie wystarczy tylko wiedzieć, ile osób ją zainstalowało. Ważne jest, aby znać stopień przydatności aplikacji oraz dysponować informacjami na temat tendencji jej użycia. Istotne stają się odpowiedzi na następujące pytania:

* Czy użytkownicy zaczynają postrzegać Twoją aplikację jako mniej interesującą lub przestarzałą? 
* Ile osób całkowicie przestało używać aplikacji? 
* Czy liczba zakupów w aplikacji rośnie czy maleje?
* Czy przepływy pracy pozostają nieukończone z powodu problemów z aplikacją lub braku zainteresowania? 
* Czy możliwe jest utrzymanie odpowiedniości i przydatności aplikacji dzięki oferowaniu użytkownikom nowej zawartości? 
* Czy odbiorcami nowej zawartości będą wszyscy użytkownicy czy będzie ona kierowana do poszczególnych segmentów użytkowników na podstawie ich zachowania w aplikacji? 

Odpowiedzi na takie pytania mogą pomóc wydłużyć czas życia aplikacji i zwiększyć poziom generowanych przychodów. Ułatwią również określenie i utrzymanie bazy użytkowników. 

Wysoki poziom przechowywania wśród użytkowników mają m.in. aplikacje służące do obsługi multimediów. Jednym z powodów takiego stanu rzeczy jest to, że stale dostarczają one nową zawartość. Wczesne wdrożenie użytecznych powiadomień wypychanych skierowanych do określonego segmentu użytkowników zwykle znacząco wpływa na poziom przechowywania aplikacji. 

Program Azure Mobile Engagement ma na celu umożliwienie wydłużenia czasu życia aplikacji i zwiększenia poziomu jej przechowuwania dzięki udostępnieniu metod, które pozwalają gromadzić i analizować szczegółowe informacje dotyczące użycia aplikacji. Ułatwi to sklasyfikowanie bazy użytkowników na podstawie ich zachowania, a także pomoże w tworzeniu ukierunkowanych kampanii obejmujących wysyłanie powiadomień wypychanych i komunikatów w aplikacjach do określonych segmentów użytkowników. Kluczowe wskaźniki wydajności (KPI) pozwalają mierzyć poziom aktywności użytkowników w świetle różnych aspektów korzystania z aplikacji. Usługa Azure Mobile Engagement udostępnia metody, które umożliwiają określenie kluczowych wskaźników wydajności. Dodatkowo zapewnia infrastrukturę potrzebną do zwiększenia interakcji użytkowników z aplikacją mobilną, co pomaga zwiększyć zwrot z inwestycji (ROI). 

Aby maksymalnie wykorzystać możliwości usługi Azure Mobile Engagement, najpierw należy opracować przemyślany plan zaangażowania. Pomoże Ci on ustalić, jakie szczegółowe dane będą potrzebne do określenia segmentów w bazie użytkowników. Można to zrobić na podstawie zachowania użytkowników oraz ich doświadczeń dotyczących używania aplikacji. Aby zapewnić powodzenie opracowanego planu, najlepiej jest jasno zdefiniować wskaźniki KPI, które będą mierzyć cele aplikacji. Dzięki temu można łatwo osadzić w aplikacji potrzebną logikę w celu gromadzenia szczegółowych danych, który będą używane do analizowania i oceniania wskaźników KPI. W tym temacie przedstawiono najlepsze rozwiązania w zakresie definiowania wskaźników KPI, które będą używane w planie zaangażowania. 

## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Krok 1. Definiowanie wskaźników KPI pasujących do modelu BET
Poprawne określenie wskaźników KPI może być trudne. Aplikacje przeznaczone dla różnych branż mają własne charakterystyki i cele. Ta różnorodność może być myląca. Aby zachować rozeznanie, cele i wskaźniki KPI należy przypisać do trzech głównych kategorii: **Biznes**, **Zaangażowanie** i **Kwestie techniczne**. Takie podejście jest określane mianem **modelu BET** (ang. Business, Engagement and Technical).

Dobry plan zwykle obejmuje cele oraz wskaźniki KPI mierzące poziom jego zrealizowania w poszczególnych kategoriach modelu BET.

#### <a name="business-kpis"></a>Wskaźniki KPI w kategorii Biznes
Wskaźniki KPI w kategorii Biznes są zwykle najłatwiejsze do określenia. Prawdopodobnie zostały już zdefiniowane w takiej czy innej formie na etapie planowania aplikacji mobilnej. Zwykle służą one do mierzenia zwrotu z inwestycji oraz przychodów generowanych przez aplikację. Poniższa lista zawiera przykładowe biznesowe wskaźniki KPI, które mogą być pomocne podczas określania wskaźników wydajności:

* Biznesowe wskaźniki KPI dotyczące multimediów
  * Liczba klikniętych reklam
  * Liczba odwiedzin strony przez użytkownika
  * Liczba bieżących subskrypcji
* Biznesowe wskaźniki KPI dotyczące gier
  * Liczba zakupów w aplikacji
  * Średni przychód na użytkownika
  * Czas trwania sesji
  * Liczba dni korzystania z gry i osiągnięty poziom
* Biznesowe wskaźniki KPI dotyczące handlu elektronicznego
  * Liczba dni używania aplikacji
  * Średni przychód na użytkownika
  * Średnia kwota w koszyku podczas finalizacji zakupu
  * Kategoria produktów najczęściej wyświetlanych i kupowanych
* Biznesowe wskaźniki KPI dotyczące bankowości i ubezpieczenia
  * Liczba kont
  * Aktywowane funkcje
  * Odwiedzone strony z ofertami
  * Kliknięte lub aktywowane alerty       

#### <a name="engagement-kpis"></a>Wskaźniki KPI w kategorii Zaangażowanie
Te wskaźniki wydajności służą do mierzenia stopnia zaangażowania użytkowników. Po wyznaczeniu trendów w obszarze zaangażowania można określić poziom przechowywania aplikacji. Oto kilka przykładowych wskaźników KPI należących do tej kategorii:

* Liczba aktywnych użytkowników w ciągu ostatnich 7 dni
* Liczba nieaktywnych użytkowników w ciągu ostatnich 7 dni
* Liczba użytkowników, którzy nie używali aplikacji w ciągu 30 dni  

Pewne czynniki zewnętrzne mogą w oczywisty sposób wpływać na te wskaźniki. Na przykład założenie, że użytkownicy urządzeń przenośnych mają je przy sobie przez cały czas, może, ale nie musi być prawdziwe. Użycie aplikacji gry może być większe w dni świąteczne, ponieważ gracze mają więcej wolnego czasu w dni wolne od pracy lub szkoły.   

Dobrze zdefiniowane wskaźniki KPI w tej kategorii powinny umożliwiać określenie relacji między aplikacją i klientami.

#### <a name="technical-kpis"></a>Wskaźniki KPI w kategorii Kwestie techniczne
Wskaźniki wydajności w tej kategorii pomagają ustalić, czy aplikacja działa prawidłowo, zawiesza się lub ulega awariom. Pozwalają one mierzyć kondycję aplikacji i znajdować problemy, które mogą uniemożliwiać użytkownikom korzystanie z aplikacji. Zebrane dane mogą również zawierać informacje o wydajności, które są istotne dla zespołów marketingowych. Dane mogą być również przydatne podczas rozwiązywania problemów przez dział IT i zespoły pomocy technicznej, a także mogą ułatwiać identyfikowanie niezgłoszonych błędów. 

Oto przykładowe wskaźniki KPI należące do kategorii Kwestie techniczne:

* Informacje dotyczące obsłużonych i nieobsłużonych wyjątków oraz liczba takich wyjątków 
* Sygnatura czasowa ostatniej awarii
* Ostatnio kliknięty przycisk lub ostatnio odwiedzona strona
* Użycie pamięci przez aplikację
* Szybkość klatek aplikacji
* Wersja systemu operacyjnego, w którym działa aplikacja
* Wersja aplikacji

Zdefiniowanie tych wskaźników KPI pomaga mierzyć wydajność aplikacji i znajdować potencjalne błędy. Wskaźniki te powinny pomóc w skróceniu czasu potrzebnego, aby dostarczyć poprawkę dla klientów. Wskaźników z tej kategorii można również użyć w celu określenia segmentu użytkowników, którzy napotkali konkretny problem. Umożliwi to przeprowadzenie kampanii obejmującej dostarczanie powiadomień o dostępnych poprawkach i spodziewanych promocjach, co pozwoli ponownie podnieść poziom zadowolenia klientów. 

#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Ćwiczenie 1. Tworzenie pulpitu nawigacyjnego wskaźników KPI
Rola wskaźników KPI używanych podczas definiowania strategii marketingowej polega na przedstawieniu perspektywy poszczególnych głównych celów aplikacji. Powinny to być jasno określone punkty danych, które pozwolą gromadzić informacje niezbędne do monitorowania aplikacji i zachowania użytkowników końcowych.

Utworzony pulpit nawigacyjny wskaźników KPI będzie zawierał następujące informacje.

1. Jakie są wskaźniki KPI dla aplikacji?
2. Jakie punkty danych będą używane do przedstawienia poszczególnych wskaźników?
3. Gdzie znajdują się dane dla aplikacji (np. na ekranie, w ustawieniach lub w systemie)?
4. Czy można odtworzyć sekwencję angażowania dla poszczególnych wskaźników KPI?

Przykłady i wskazówki można znaleźć w arkuszu służącym do **konstruowania wskaźników KPI**, który jest dostępny w [podręcznikowym szablonie dotyczącym multimediów][Media Playbook link].

## <a name="step-2-your-engagement-program"></a>Krok 2. Program zaangażowania
Jednym z kluczowych składników aplikacji powinien być dobry marketing na urządzeniach przenośnych. Absolutnie krytycznym wymogiem jest doskonały program powitalny, uruchamiany przez kilka pierwszych dni używania aplikacji. Zwykle ma on bardzo pozytywny wpływ na zaangażowanie użytkownika i poziom przechowywania aplikacji. Badania wykazały, że większość użytkowników przestaje korzystać z aplikacji po kilku dniach od jej zainstalowania. Celem dewelopera jest spełnienie lub przekroczenie oczekiwań klientów i wczesne pobudzenie ich zaangażowania, gdy są oni wciąż zainteresowani aplikacją. Należy zadbać o przedstawienie klientom najważniejszych zalet aplikacji i korzyści płynących z jej używania. 

![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Najlepsze sposób na nawiązanie interakcji z użytkownikami urządzeń przenośnych polega na użyciu powiadomień wypychanych. Należy jednak zachować dużą ostrożność podczas segmentowania użytkowników pod kątem powiadomień wypychanych. Jeśli użytkownik zacznie postrzegać powiadomienia jako nieinteresujące lub uzna je za spam, może szybko zdecydować o usunięciu aplikacji i nigdy do niej nie powróci. Zamiast ogólnych komunikatów użytkownicy powinni otrzymywać ściśle spersonalizowane informacje na temat korzyści płynących z używania aplikacji.

Gdy już uda się ich nakłonić do aktywnego korzystania z aplikacji, można skupić się na innych aspektach jej używania w obrębie programu zaangażowania.

Można na przykład utworzyć kampanię, w ramach której aktywni użytkownicy będą oceniać aplikację. Ponieważ są to najbardziej aktywne osoby, które mają najwięcej doświadczenia związanego z używaniem aplikacji, można oczekiwać, że ich oceny będą najdokładniejsze. Wysoka klasyfikacja może pomóc zwiększyć liczbę pobrań aplikacji oraz ograniczyć koszty pozyskiwania nowych klientów.

#### <a name="engagement-sequence"></a>Sekwencja angażowania
Globalny program zaangażowania obejmuje różne sekwencje, a każda z nich ma na celu osiągnięcie kilku celów.

###### <a name="life-push-sequence"></a>Sekwencja powiadomień wypychanych dotyczących cyklu życia
Cele tej sekwencji mogą być różne i zależą od przebiegu zaangażowania użytkowników w ramach korzystania z aplikacji. Użytkownicy mogą być nieaktywni lub bardzo aktywni albo mogą dopiero zaczynać poznawać aplikację. Nowa zawartość, udostępniana w postaci porad lub linków prowadzących do dokumentacji, może być przydatna na różnych etapach cyklu interakcji z aplikacją. 

Na przykład nowy użytkownik może potrzebować pomocy, aby zorientować się w strukturze aplikacji, albo może poczuć się bardziej komfortowo, gdy po pierwszym uruchomieniu aplikacji otrzyma mniej więcej taką zachętę...

*„Dobrze, że jesteś z nami! Zaloguj się, aby uzyskać pierwszy miesiąc za darmo!”*

###### <a name="behavioral-push-sequence"></a>Sekwencja powiadomień wypychanych dotyczących zachowania użytkowników
Celem tej sekwencji jest zwiększenie użycia aplikacji na podstawie informacji o zachowaniu użytkowników.  

Na przykład bardzo aktywny użytkownik fantastycznej gry w piłkę nożną może być zainteresowany po otrzymaniu takiego powiadomienia...

*„Jacku, jesteś wielkim fanem futbolu! Zaloguj się do naszej sekcji ligowej i wygraj bezpłatny dostęp do transmisji z Pucharu Ekstraklasy!”*

###### <a name="alerting-push-sequence"></a>Sekwencja powiadomień wypychanych dotyczących alertów
Użytkownikom spodoba się otrzymywanie komunikatów związanych z ich zainteresowaniami. Sekwencja powiadomień wypychanych dotyczących alertów umożliwia zwiększenie zaangażowania przez wysyłanie komunikatów dostosowanych do ściśle określonych zainteresowań użytkowników. Może to być jawne, gdy użytkownik wybierze swoje zainteresowania w aplikacji. Można to również określić niejawnie na podstawie danych zebranych podczas interakcji użytkownika z aplikacją.

Na przykład użytkownik aplikacji z branży handlu elektronicznego może regularnie kupować konkretną markę kawy, a informację o tym udało się przechwycić za pomocą biznesowego wskaźnika KPI. Poniższy alert może zwiększyć zainteresowanie tego użytkownika aplikacją.

*„Cześć, Tomasz, w pierwszym tygodniu września 2015 r. obniżamy o 25% cenę jednej z Twoich ulubionych marek kawy. Cieszymy się, że jesteś naszym klientem i dlatego specjalnie Cię o tym informujemy”.*

###### <a name="rentention-push-sequence"></a>Sekwencja powiadomień wypychanych dotyczących przechowywania
Celem tej sekwencji jest utrzymanie użytkowników przy użyciu powtarzanych kampanii obejmujących wysyłanie powiadomień wypychanych, które mają wykształcić regularny nawyk interakcji z aplikacją. Może to pomóc w zwiększeniu poziomu przechowywania aplikacji, o ile użytkownicy będą zadowoleni z tych interakcji. 

Na przykład użytkownicy aplikacji sportowych mogą co tydzień otrzymywać takie powiadomienie dotyczące ich ulubionych klubów:

*„Masz szansę wygrać 200 punktów. Przejdź do naszej sondy i odpowiedz na pytanie, czy Lech Poznań wygra jutrzejszy mecz z Legią Warszawa”.*

#### <a name="the-3w-approach"></a>Metoda „Kto, co i kiedy”
Opanowanie różnych sekwencji powiadomień wypychanych ułatwia angażowanie użytkowników końcowych. Jednak wysyłane powiadomienia wymagają personalizacji. Z pomocą przychodzi metoda „Kto, co i kiedy”. Metoda „Kto, co i kiedy” powinna być stosowana w przypadku każdego powiadomienia. Jeśli uda się uzyskać jasne odpowiedzi na te pytania, powiadomienia będą odpowiednio ukierunkowane na wzbudzenie zaangażowania klientów.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)

###### <a name="who-the-user-segment-that-will-receive-messages"></a>Kto: segment użytkowników, którzy będą otrzymywać wiadomości
Komunikację za pośrednictwem powiadomień wypychanych należy traktować bardzo ostrożnie. Wysyłane wiadomości muszą być starannie dopasowane do zainteresowań danego segmentu użytkowników. Niewłaściwie nakierowane powiadomienie może bardzo łatwo wywrzeć negatywne wrażenie na użytkownikach. Mogą oni uznać je za spam, co doprowadzi do odinstalowania aplikacji. 

Definiując segmenty użytkowników, którzy będą otrzymywać powiadomienia, należy zastosować kombinację specyficznych kryteriów technicznych i behawioralnych. Prosty przykład definicji segmentu użytkowników może wyglądać mniej więcej tak:

„Wszyscy użytkownicy, którzy pierwszy raz uruchomili aplikację 3 dni temu i dwukrotnie wyświetlili stronę logowania, ale nie zalogowali się”.

Takie wyrażenie pomoże określić dane, które należy zebrać na potrzeby obsługi danego scenariusza.

###### <a name="what-the-message-that-you-will-send"></a>Co: komunikat, który będzie wysyłany
**Ton komunikatu**

W powiadomieniach promujących zaangażowanie należy używać tonu odpowiedniego dla danego segmentu użytkowników. Jest to zdecydowanie zalecane w komunikacji z użytkownikami końcowymi, która ma upowszechniać zainteresowanie aplikacją. 

**Przekierowania**

Powiadomienia wypychane mogą być używane nie tylko podczas otwierania aplikacji. Jeśli powiadomienie udostępnia kontekst, na przykład materiały informacyjne lub dotyczące promocji produktów, może ono zawierać link, który bezpośrednio prowadzi do odpowiedniej zawartości w aplikacji. Aby to obsłużyć, należy utworzyć schemat adresów URL, który umożliwi aplikacji zarządzanie przekierowaniami. Należy koniecznie pamiętać o tym ważnym etapie podczas pracy z sekwencjach angażowania.

Przekierowaniami można zarządzać również w przypadku korzystania z innych systemów. Na przykład za pomocą adresu URL akcji można przekierowywać użytkowników końcowych do wielu innych systemów, takich jak:

* Witryna sieci Web
* Skrzynka pocztowa ze skonfigurowaną pocztą e-mail
* System SMS Box
* Usługa wybierania numeru
* Bezpośrednio do sklepu z aplikacjami w celu ocenienia aplikacji. 

Stwarza to wiele okazji do promowania zaangażowania użytkowników końcowych i konfigurowania automatycznych reguł usprawniających wydajność.

**Format/zawartość**

Różne typy i formaty powiadomień wypychanych:

1. **Anonse**: pozwalają wysyłać użytkownikom wiadomości z reklamami o różnych porach (podczas korzystania lub niekorzystania z aplikacji albo w dowolnym momencie).
2. **Ankiety**: umożliwiają zbieranie informacji od użytkowników końcowych za pośrednictwem pytań. Uzyskane odpowiedzi będą dostępne podczas tworzenia kryteriów na potrzeby kierowania działań na użytkowników końcowych.
3. **Wypychania danych**: pozwalają wysyłać binarne pliki danych lub pliki zakodowane w formacie base64 w celu zaktualizowania aplikacji. Informacje zawarte w wypychanych danych wysyłanych do aplikacji pozwalają personalizować środowisko użytkownika. Aplikacja musi być tak zaprojektowana, aby obsługiwać odbieranie wypychanych danych.
4. **Kafelki (tylko w systemie Windows Phone)**: pozwalają używać usługi powiadomień wypychanych firmy Microsoft (MPNS, Microsoft Push Notification Service) do wysyłania natywnych powiadomień wypychanych zawierających dane XML (Obsługiwane od wersji 0.9.0 zestawu SDK. Końcowy ładunek dla kafelków nie może przekraczać 32 kilobajtów). Wiadomość jest wyświetlana bezpośrednio na kafelku tablicy dewelopera.
5. **Elementy Webview**: są to okna podręczne z zawartością sieci Web. Są one wyświetlane, gdy użytkownik końcowy kliknie powiadomienie wypychane. Widok sieci Web pozwala poszerzyć interakcję z użytkownikami końcowymi.

> [!NOTE]
> Należy upewnić się, że zawartość powiadomień wypychanych jest zgodna z wytycznymi dla poszczególnych platform (iOS, Android, Windows) dotyczącymi tworzenia aplikacji i wysyłania powiadomień wypychanych.
> 
> 

###### <a name="when-the-timing-of-your-campaign"></a>Kiedy: czas wdrożenia kampanii
Jaki jest najlepszy moment na aktywowanie powiadomień wypychanych w ramach rozpoczęcia kampanii? Czy ma to odbywać się ręczne czy automatycznie? Czy takie powiadomienia mają być cykliczne? Określanie właściwego momentu lub częstotliwości ma kluczowe znaczenie dla uzyskania najlepszych wyników angażowania użytkowników. Dla każdego scenariusza i sekwencji angażowania należy wyznaczyć najlepszy moment na wysyłanie powiadomień wypychanych. Oto kilka przykładów:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

W przypadku codziennego wysyłania wielu powiadomień należy koniecznie mieć na uwadze, że użytkownicy mogą postrzegać taką komunikację jako spam. 

W usłudze Azure Mobile Engagement są dostępne dwie metody, które pozwalają temu zapobiec. Pierwsza z nich polega na szczegółowej segmentacji użytkowników, dzięki czemu komunikaty nie są kierowane do tych samych osób. Ponadto usługa Azure Mobile Engagement udostępnia funkcję „przydział”. Ta funkcja może ograniczyć powiadomienia wysyłane w ramach kampanii. Na przykład ustawienie domyślnego przydziału na 5 wiadomości w tygodniu gwarantuje, że użytkownicy należący do danego segmentu nie otrzymają więcej niż 5 powiadomień w tygodniu.

#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Ćwiczenie 2. Tworzenie programu zaangażowania
Należy poświęcić trochę czasu na podsumowanie celów i określenie spodziewanych kampanii prowadzonych przy użyciu specyficznych sekwencji. Należy pamiętać, aby powiadomienia w ramach kampanii uwzględniały metodę „Kto, co i kiedy”. 

Przykłady i wskazówki można znaleźć w arkuszu **programu zaangażowania**, który jest dostępny w [podręcznikowym szablonie dotyczącym multimediów][Media Playbook link].

## <a name="step-3-app-integration"></a>Krok 3. Integracja aplikacji
#### <a name="create-a-tag-plan"></a>Tworzenie planu dodawania tagów
W celu zintegrowania aplikacji z usługą Azure Mobile Engagement należy utworzyć plan dodawania tagów, który stanowi podstawę projektu. Definiuje on relacje między specyfikacjami marketingowymi, przepływem pracy w aplikacji oraz rzeczywistymi danymi tagów zebranymi przez aplikację w celu mierzenia wskaźników KPI. Plan dodawania tagów określa, jakie analizy można wyświetlić w portalu. Pomaga również w wyznaczeniu segmentów użytkowników i wysyłaniu ukierunkowanych powiadomień wypychanych, których celem jest angażowanie użytkowników końcowych. Po zdefiniowaniu planu dodawania tagów można za pomocą kodu łatwo zintegrować go z aplikacją, używając zestawu SDK usługi Azure Mobile Engagement.

W ramach planu dodawania tagów nie należy oznaczać wszystkich elementów aplikacji. Powinien on obejmować tylko dane tagów, które są częścią strategii marketingowej na urządzeniach przenośnych W przypadku różnych aplikacji te dane prawdopodobnie będą inne. [Szablon zawarty w podręczniku dotyczącym multimediów][Media Playbook link] udostępniany przez usługę Azure Mobile Engagement ułatwia utworzenie planu dodawania tagów za pomocą danej metody. Arkusz **planu dodawania tagów** służy jako przewodnik dotyczący tworzenia planu dodawania tagów.

Ważne jest, aby bardzo dokładnie zdefiniować sekcję tagów w arkuszu. Pozwoli to uniknąć pomyłek. Należy szczegółowo określić wszystkie oczekiwane scenariusze, w których będą wysyłane poszczególne tagi, dodając nazwy działań z osadzonymi tagami. Wszystkie te informacje należy uwzględnić w **informacyjnej** części arkusza. Arkusz planu dodawania tagów powinien stanowić główny punkt odniesienia dla testów weryfikacyjnych. 

W sekcji **danych do zebrania** deweloperzy mogą znaleźć typy, nazwy, wartości oraz dodatkowe pary klucz/wartość wymagane dla poszczególnych tagów, które zostaną osadzone w aplikacji.

Zalecamy, aby plan dodawania tagów został sprawdzony przez wszystkie zespoły biorące udział w projekcie. Po wprowadzeniu koniecznych poprawek należy upewnić się, że plan jest całkowicie zrozumiały dla zespołu marketingowego i deweloperskiego.

Arkusz **wykazu zakresu prac** może służyć jako przewodnik dla wszystkich uczestników projektu.

#### <a name="data-types"></a>Typy danych
Są to najczęściej spotykane typy danych obsługiwane przez usługę Azure Mobile Engagement.

###### <a name="devices-and-users"></a>Urządzenia i użytkownicy
Usługa Azure Mobile Engagement identyfikuje użytkowników za pomocą unikatowych identyfikatorów generowanych dla poszczególnych urządzeń. Te identyfikatory są nazywane identyfikatorami urządzeń. Są one generowane w taki sposób, że wszystkie aplikacje uruchomione na danym urządzeniu współużytkują ten sam identyfikator urządzenia.

###### <a name="sessions-and-activities"></a>Sesje i działania
Sesja to inaczej wystąpienie aplikacji uruchomionej przez użytkownika. Czas trwania sesji jest liczony od uruchomienia aplikacji do jej zamknięcia.

Termin działanie dotyczy logicznej grupy operacji wykonywanych przez aplikację podczas sesji. Zwykle jest to określony ekran aplikacji, ale działanie może odpowiadać dowolnemu elementowi logiki aplikacji. Minimalnym wymaganiem jest przypisanie tagów do wszystkich ekranów i działań aplikacji. Pozwoli to zapoznać się ze ścieżką użytkownika.

###### <a name="events"></a>Zdarzenia
Zdarzenia są używane do zgłaszania interakcji użytkowników z aplikacją. Mogą to być natychmiastowe akcje, takie jak udostępnianie zawartości lub uruchamianie klipu wideo. Oznaczenie zdarzeń tagami umożliwi zebranie danych opisujących interakcje użytkowników z aplikacją. 

###### <a name="jobs"></a>Zadania
Zadania są używane do zgłaszania akcji, dla których można określić czas trwania. Przykładowe zadania obejmują:

* Wykonywanie wywołań interfejsu API
* Czas wyświetlania reklam
* Czas trwania zadań w tle 
* Czas trwania procesu zakupu
* Wyświetlanie klipu wideo

###### <a name="errors"></a>Błędy
Błędy służą do zgłaszania problemów wykrytych przez aplikację, na przykład nieprawidłowych działań użytkowników lub nieudanych wywołań interfejsu API.

###### <a name="application-information"></a>Informacje o aplikacji
Informacje o aplikacji służą do oznaczania tagami danych dotyczących środowiska pracy użytkownika. Są one generowane w czasie interakcji z aplikacją. 

Dla danego klucza informacji o aplikacji usługa Azure Mobile Engagement śledzi tylko najnowszą wartość (historia nie jest dostępna). Informacje o aplikacji udostępniają stan aplikacji lub użytkowników końcowych, na przykład stan logowania lub stan grupy ulubionych produktów użytkownika.

###### <a name="crash-data"></a>Dane dotyczące awarii
Dane dotyczące awarii są automatycznie gromadzone przez zestaw SDK usługi Mobile Engagement i zawierają zgłoszenia błędów, które nie zostały obsłużone przez aplikację. Mogą to być na przykład nieobsłużone wyjątki.

###### <a name="extra-data"></a>Dodatkowe dane
Do zdarzeń, błędów, działań i zadań mogą być dołączane parametry. Deweloperzy mogą korzystać z tych informacji dodatkowych, traktując je jako dane specyficzne dla konkretnej aplikacji. Jest to ważne w przypadku definiowania precyzyjnej segmentacji. 

Na przykład wartość tagu „artykuł” pozwoli określić segment użytkowników, którzy wyświetlili stronę z konkretnym artykułem. Jednak taka informacja może nie być wystarczająca. Lepszym rozwiązaniem byłoby dołączenie w ramach działania dodatkowych informacji, takich jak „kategoria_nowości”, do tagu „artykuł”. Dzięki temu będzie można dynamicznie określać ulubione kategorie użytkownika. 

Dodatkowe informacje są zgłaszane w postaci pary klucz/wartość. W tej przykładowej aplikacji multimedialnej dodatkowe informacje dla tagu „kategoria_nowości” stanowią wartość tej kategorii, na przykład „sport”, „gospodarka” lub „polityka”.

#### <a name="tag-and-sdk-integration"></a>Integracja tagów z zestawem SDK
Szczegółowe instrukcje dotyczące integracji aplikacji z zestawem SDK usługi Azure Mobile Engagement można znaleźć w artykule [Engagement SDK Integration](mobile-engagement-windows-store-integrate-engagement.md) (Integracja zestawu SDK usługi Mobile Engagement) dostępnym w dokumentacji w witrynie sieci Web platformy Azure. W górnej części strony znajdują się linki dla różnych platform docelowych.

Zaleca się utworzenie dwóch projektów aplikacji korzystających z usługi Azure Mobile Engagement. Pierwszy z nich będzie używany na etapie programowania i testowania, a drugi będzie przeznaczony do wdrożenia produkcyjnego. Po pomyślnym przeprowadzeniu testów akceptacyjnych przez użytkowników zespół IT może promować projekt z etapu testowego do produkcyjnego.

#### <a name="user-acceptance-testing-uat"></a>Testy akceptacyjne użytkowników
Testy akceptacyjne użytkowników służą upewnieniu się, że wszystko działa zgodnie z oczekiwaniami. Testy umożliwiają ukończenie przepływów pracy i zebranie wszystkich potrzebnych danych w oparciu o plan dodawania tagów:

* Tagi informacyjne są wdrożone zgodnie z udokumentowanymi pojęciami AZME.
* Zbierane są wszystkie potrzebne informacje (w tym informacje o aplikacji oraz dane dodatkowe).
* Nomenklatura jest zgodna z planem dodawania tagów.
* Nie są wysyłane zduplikowane tagi.

Należy dokładnie przetestować działanie wszystkich typów powiadomień, które zostały osadzone w aplikacji.

* Anonse, ankiety, dane wypychane z i do aplikacji.
* Widoki tekstowe/sieci Web
* Aktualizacja wskaźnika, kategorie

#### <a name="setup"></a>Konfiguracja
Konfigurowanie usługi Azure Mobile Engagement jest bardzo proste. Cała dokumentacja dotyczącą interfejsu użytkownika jest dostępna w artykule [How to navigate the user interface](mobile-engagement-user-interface-home.md) (Nawigowanie po interfejsie użytkownika) w witrynie usługi Azure Mobile Engagement w sieci Web.

Zaleca się rozpoczęcie pracy od skonfigurowania odpowiednich ról i przynależności do ról dla użytkowników projektu. Ułatwia to zarządzanie dostępem wszystkich użytkowników do platformy. Mogą to być następujące role:

* Administratorzy
* Deweloperzy
* Osoby przeglądające 

Następnie

* należy zarejestrować identyfikator urządzenia, aby móc przeprowadzać testy na swoim urządzeniu.
* W ustawieniach konta należy skonfigurować strefę czasową na potrzeby dostarczania wykresów i powiadomień.
* W ustawieniach aplikacji należy zarejestrować informacje o aplikacji potrzebne do kierowania powiadomień do użytkowników końcowych w obrębie modułu Reach.

Więcej informacji dotyczących przeprowadzania pierwszej kampanii wysyłania powiadomień wypychanych można znaleźć w artykule [How to get started using and managing pushes to reach out to your end users](mobile-engagement-how-tos.md) (Jak rozpocząć korzystanie z powiadomień wypychanych i zarządzanie nimi w celu dotarcia do użytkowników końcowych).

## <a name="conclusion"></a>Podsumowanie
Programy zaangażowania mają naturę iteracyjną, dlatego należy ciągle je usprawniać, eksperymentując z różnymi rozwiązaniami. 

Na początkowym etapie zdobywania doświadczenia z różnymi strategiami zaangażowania nie należy próbować tworzyć globalnej strategii. Warto przyjąć podejście „krok po kroku”, identyfikując poszczególne wskaźniki KPI i sposoby ich wykorzystania. Strategia zaangażowania jest unikatowa dla każdej aplikacji.

Po zdobyciu pewnego doświadczenia można rozważyć dodanie do programu zaangażowania następujących funkcji:

* Śledzenie: pozyskiwanie użytkowników zwykle idzie w parze z definiowaniem źródeł gromadzonych danych. Usługę Azure Mobile Engagement można połączyć z takimi źródłami, co pozwala na monitorowanie ich wydajności. Uzyskane informacje umożliwią zmaksymalizowanie inwestycji w pozyskiwanie nowych klientów. 
* Testy A/B: to fundamentalna część programu zaangażowania. Każda aplikacja ma własną specyfikę. Testy A/B pozwalają ulepszyć program zaangażowania.
* Geolokalizacja: to duża szansa dla marek. Dzięki tej funkcji komunikaty trafiają do użytkowników w odpowiednim miejscu i czasie. Przed rozpoczęciem używania geolokalizacji zaleca się sprawdzenie, czy zebrano wystarczającą ilość danych na temat zachowania użytkowników końcowych.
* Wypychanie danych: wypychane dane są niewidoczne. Wypychanie danych pozwala na dostosowywanie aplikacji na podstawie zachowania użytkowników końcowych. Na przykład jeśli pewna grupa użytkowników często sprawdza informacje o zaawansowanych technologicznie produktach, właściciel aplikacji może użyć wypychania danych w celu spersonalizowania strony głównej aplikacji pod kątem takich produktów.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie konta usługi Azure Mobile Engagement](mobile-engagement-create.md)
* Więcej informacji na temat definiowania strategii marketingowej na urządzeniach przenośnych można znaleźć w artykule [Define your Mobile Engagement strategy](mobile-engagement-define-your-mobile-engagement-strategy.md) (Definiowanie strategii marketingowej na urządzeniach przenośnych).

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
