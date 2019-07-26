---
title: Typy jednostek
titleSuffix: Language Understanding - Azure Cognitive Services
description: 'Jednostki wyodrębniają dane z wypowiedź. Typy jednostek umożliwiają przewidywalną wyodrębnianie danych. Istnieją dwa typy jednostek: Uczenie maszynowe i niekomputerowe. Ważne jest, aby wiedzieć, który typ jednostki jest używany w wyrażenia długości.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: a5a3ba8c25107317e7c47ee358f9a6ebe7d4556f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479113"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>Typy jednostek i ich cele w LUIS

Jednostki wyodrębniają dane z wypowiedź. Typy jednostek umożliwiają przewidywalną wyodrębnianie danych. Istnieją dwa typy jednostek: Uczenie maszynowe i niekomputerowe. Ważne jest, aby wiedzieć, który typ jednostki jest używany w wyrażenia długości. 

## <a name="entity-compared-to-intent"></a>Jednostki w porównaniu do intencji

Jednostka reprezentuje wyraz lub frazę w polu wypowiedź, który ma zostać wyodrębniony. Wypowiedź może zawierać wiele jednostek lub brak wcale. Aplikacja kliencka może potrzebować jednostki do wykonania zadania lub użycia jej jako przewodnika po kilku opcjach, które mają być widoczne dla użytkownika. 

Jednostka:

* Reprezentuje klasę zawierającą kolekcję podobnych obiektów (miejsc, rzeczy, osób, zdarzeń lub koncepcji). 
* Opisuje informacje istotne dla zamiaru


Na przykład aplikacji wyszukiwania wiadomości mogą obejmować jednostki, takie jak "tematu", "źródło", "słowo kluczowe" i "publikowania daty" będące kluczowych danych do wyszukiwania wiadomości. W aplikacji do księgowania podróży "lokalizacja", "Data", "linie lotnicze", "Klasa podróży" i "bilety" są kluczowymi informacjami dotyczącymi rezerwacji lotów (dotyczy to zamiaru "Lot" książki ").

Natomiast zamiar reprezentuje prognozowania całego wypowiedź. 

## <a name="entities-help-with-data-extraction-only"></a>Jednostki pomocy dotyczące tylko wyodrębniania danych

Etykiety i oznaczania jednostek do celów wyodrębniania jednostek nie ułatwiają przewidywania intencji.

## <a name="entities-represent-data"></a>Jednostki reprezentujące dane

Jednostki są dane, które chcesz ściągnąć z wypowiedź. Może to być nazwy, daty, nazwa produktu lub dowolną grupę słów. 

|Wypowiedź|Jednostka|Dane|
|--|--|--|
|Kup 3 bilety w Nowym Jorku|Liczbę wstępnie<br>Location.Destination|3<br>Nowy Jork|
|Kup biletu z nowego Jorku do Londynu na 5 marca|Location.Origin<br>Location.Destination<br>Wstępnie utworzone datetimeV2|Nowy Jork<br>Londyn<br>5 marca 2018 r.|

## <a name="entities-are-optional-but-highly-recommended"></a>Jednostki są opcjonalne, ale zdecydowanie zalecane

Mimo że intencjami wymagane, jednostki są opcjonalne. Nie trzeba tworzyć jednostek dla każdej koncepcji w aplikacji, ale tylko dla tych, które są wymagane do podjęcia działania przez aplikację kliencką. 

Swoje wypowiedzi nie ma szczegółów, czego potrzebuje bota, aby kontynuować, nie muszą je dodać. Jak dojrzewa aplikacji, możesz je dodać później. 

Jeśli nie masz pewności, jak korzystać z tych informacji, Dodaj kilka typowych wstępnie utworzonych jednostek, takich jak [datetimeV2](luis-reference-prebuilt-datetimev2.md), [porządkowa](luis-reference-prebuilt-ordinal.md), [adres e-mail](luis-reference-prebuilt-email.md)i [numer telefonu](luis-reference-prebuilt-phonenumber.md).

## <a name="label-for-word-meaning"></a>Etykieta znaczenie słowa

Jeśli wybór programu word lub rozmieszczeniu word jest taka sama, ale nie oznaczają to samo, nie przypisuj jej etykiety bez jednostki. 

Następujące wypowiedzi, wyraz `fair` jest Homogram. Została wpisana takie same, ale ma inne znaczenie:

|Wypowiedź|
|--|
|Jakiego rodzaju targach hrabstwa występują na terenie Seattle tego lata?|
|Jest bieżąca ocena do przeglądu Seattle uczciwe?|

Jeśli chce się jednostkę zdarzeń, aby znaleźć wszystkie dane zdarzeń, etykieta wyraz `fair` w pierwszym wypowiedź, ale nie w ciągu sekundy.

## <a name="entities-are-shared-across-intents"></a>Jednostki są współdzielone przez intencji

Jednostki są współużytkowane przez intencji. Nie należą one do dowolnego pojedynczego celem. Intencje i jednostki mogą być semantycznie skojarzone, ale nie jest to relacja wyłączna.

W wypowiedź "książka ode mnie bilet do Paryż", "Paryż" jest jednostką odwołującą się do lokalizacji. Uznając jednostki, które są wymienione w wypowiedź użytkownika, LUIS pomaga aplikacji klienckiej wybrać określone akcje, które należy wykonać w celu spełnienia żądania użytkownika.

## <a name="mark-entities-in-none-intent"></a>Oznacz jednostki w opcji Brak

Wszystkie intencje, łącznie z intencją **none** , powinny mieć oznaczone jednostki, jeśli jest to możliwe. Dzięki temu usługi LUIS, Dowiedz się więcej o których jednostek wypowiedzi i jakie słowa wokół jednostki. 

## <a name="entity-status-for-predictions"></a>Stan jednostki dla prognoz

Portal LUIS informuje o tym, kiedy jednostka w przykładowym wypowiedź różni się od oznaczonej jednostki lub jest zbyt blisko innej jednostki i dlatego jest niejasne. Jest to wskazane czerwonym podkreśleniem w przykładzie wypowiedź. 

Aby uzyskać więcej informacji, zobacz [przewidywania stanu jednostki](luis-how-to-add-example-utterances.md#entity-status-predictions). 

## <a name="types-of-entities"></a>Typy jednostek

LUIS oferuje wiele typów jednostek. Wybierz jednostkę na podstawie sposobu wyodrębniania danych i sposobu ich reprezentowania po wyodrębnieniu.

Jednostki można wyodrębnić przy użyciu uczenia maszynowego, co umożliwia LUISom kontynuowanie uczenia się, jak jednostka pojawia się w wypowiedź. Jednostki można wyodrębnić bez uczenia maszynowego, dopasowując dokładnego tekstu lub wyrażenia regularnego. Jednostki w wzorcach można wyodrębnić przy użyciu implementacji mieszanej. 

Po wyodrębnieniu jednostki dane jednostki mogą być reprezentowane jako jedna jednostka informacji lub połączone z innymi jednostkami w celu utworzenia jednostki informacji, które mogą być używane przez aplikację kliencką.

|Komputer — informacje|Można oznaczyć|Samouczek|Przykład<br>Odpowiedź|Typ jednostki|Cel|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**Złożone**](#composite-entity)|Grupowanie jednostek, niezależnie od typu jednostki.|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**Staw**](#list-entity)|Lista elementów i ich synonimy wyodrębnione z dokładnym dopasowaniem do tekstu.|
|Mieszany||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Wzorzec. any**](#patternany-entity)|Obiekt, na którym koniec jednostki jest trudny do określenia.|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**Prebuilt**](#prebuilt-entity)|Już przeprowadzono szkolenia w celu wyodrębnienia różnych rodzajów danych.|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**Wyrażenie regularne**](#regular-expression-entity)|Dopasowuje tekst za pomocą wyrażenia regularnego.|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**Ułatwia**](#simple-entity)|Zawiera pojedyncze koncepcje w wyrazie lub frazie.|

W przykładzie wyrażenia długości należy oznaczyć tylko jednostki, które są związane z maszyną. Jednostki poznanie maszyn działają najlepiej w przypadku przetestowania za pośrednictwem [zapytań punktu końcowego](luis-concept-test.md#endpoint-testing) i [przeglądania punktu końcowego wyrażenia długości](luis-how-to-review-endoint-utt.md). 

Wzorzec. wszystkie jednostki muszą być oznaczone jako przykłady szablonu [wzorca](luis-how-to-model-intent-pattern.md) , a nie przykłady użytkownika. 

Jednostki mieszane używają kombinacji metod wykrywania jednostek.

## <a name="machine-learned-entities-use-context"></a>Jednostki, na które nauczyne maszyny, używają kontekstu

Jednostki poznanie maszyn są zgodne z kontekstem w wypowiedź. Sprawia to, że zmiany położenia w przykładzie wyrażenia długości są istotne. 

## <a name="non-machine-learned-entities-dont-use-context"></a>Niezgodne z maszyną jednostki nie używają kontekstu

Następujące jednostki, które nie są obsługiwane przez maszynę, nie przyjmują wypowiedź kontekstu podczas dopasowywania jednostek: 

* [Wstępnie utworzonych jednostek](#prebuilt-entity)
* [Jednostki wyrażenia regularnego](#regular-expression-entity)
* [Lista jednostek](#list-entity) 

Te jednostki nie wymagają etykietowania ani uczenia modelu. Po dodaniu lub skonfigurowaniu jednostki zostaną wyodrębnione jednostki. Kompromisem jest to, że te jednostki mogą być niezgodne, gdzie w przypadku uwzględnienia kontekstu nie zostały wprowadzone. 

Dzieje się tak w przypadku, gdy często są używane jednostki listy dla nowych modeli. Tworzysz i testujesz model przy użyciu jednostki listy, ale po opublikowaniu modelu i otrzymaniu zapytań od punktu końcowego należy pamiętać, że model jest dopasowany ze względu na brak kontekstu. 

Jeśli chcesz dopasować wyrazy lub frazy i wziąć pod uwagę kontekst, masz dwie opcje. Pierwszym jest użycie prostej jednostki sparowanej z listą fraz. Lista fraz nie zostanie użyta do dopasowania, ale zamiast tego pomaga sygnalizować podobne słowa (lista zamienna). Jeśli musisz mieć dokładne dopasowanie zamiast odmian listy fraz, użyj jednostki listy z rolą, która została opisana poniżej.

### <a name="context-with-non-machine-learned-entities"></a>Kontekst z jednostkami niezgodnymi z maszyną

Jeśli chcesz, aby kontekst wypowiedź dla jednostek niezgodnych z maszyną, należy użyć [ról](luis-concept-roles.md).

Jeśli masz niezależną od komputera jednostkę, taką jak [wstępnie skompilowane](#prebuilt-entity)jednostki, jednostki [wyrażenia regularnego](#regular-expression-entity) lub jednostki [listy](#list-entity) , które są zgodne spoza wystąpienia, należy rozważyć utworzenie jednej jednostki z dwiema rolami. Jedna z ról będzie przechwycić to, czego szukasz, a jedna z nich będzie przechwycić to, czego szukasz. Obie wersje muszą mieć etykietę na przykład wyrażenia długości.  

## <a name="composite-entity"></a>Złożone jednostki

[Jednostka złożona](reference-entity-composite.md) składa się z innych jednostek, takich jak wstępnie zbudowane jednostki, proste, wyrażenie regularne i jednostki listy. Osobne jednostki tworzą całej jednostki. 

## <a name="list-entity"></a>Jednostka listy

[Jednostki listy](reference-entity-list.md) reprezentują stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Usługa LUIS nie wykrywa dodatkowe wartości dla jednostek z listy. Użyj **zaleca się** funkcji, aby zobaczyć sugestie dotyczące nowych słów na podstawie bieżącej listy. Jeśli istnieje więcej niż jednej jednostki listy z taką samą wartość, każdy obiekt jest zwracany w kwerendy punktu końcowego. 

## <a name="patternany-entity"></a>Jednostka Pattern.any

[Wzorzec. any](reference-entity-pattern-any.md) jest symbolem zastępczym o zmiennej długości używanym tylko w szablonie wzorca wypowiedź, aby oznaczyć, gdzie rozpoczyna się i kończą.  

## <a name="prebuilt-entity"></a>Wstępnie utworzone jednostki

Wstępnie skompilowane jednostki są wbudowanymi typami, które reprezentują typowe pojęcia, takie jak adres e-mail, adres URL i numer telefonu. Wstępnie utworzone jednostki nazwy są zarezerwowane. [Wszystkie wstępnie skompilowane jednostki](luis-prebuilt-entities.md) , które są dodawane do aplikacji są zwracane w zapytaniu prognozowania punktu końcowego, jeśli znajdują się w wypowiedź. 

Jednostka jest dobrym dopasowaniem w przypadku:

* Dane są zgodne ze wspólnym przypadkiem użycia obsługiwanym przez wstępnie utworzone jednostki dla kultury językowej. 

Wstępnie utworzone jednostki można dodawać i usuwać w dowolnym momencie.

![Liczba wstępnie utworzone jednostki](./media/luis-concept-entities/number-entity.png)

[Samouczek](luis-tutorial-prebuilt-intents-entities.md)<br>
[Przykładowa odpowiedź JSON dla jednostki](luis-concept-data-extraction.md#prebuilt-entity-data)

Niektóre z tych wstępnie skompilowanych jednostek są zdefiniowane w projekcie typu "Open Source [" — tekst](https://github.com/Microsoft/Recognizers-Text) . Jeśli z określoną kulturę lub jednostki nie jest obecnie obsługiwane, przyczyniają się do projektu. 

### <a name="troubleshooting-prebuilt-entities"></a>Rozwiązywanie problemów z prekompilowanymi jednostkami

Jeśli wstępnie utworzona jednostka jest oznaczona jako niestandardowa, w portalu LUIS masz kilka możliwości rozwiązania tego problemu.

Wstępnie skompilowane jednostki dodane do aplikacji _zawsze_ będą zwracane, nawet jeśli wypowiedź powinna wyodrębnić jednostki niestandardowe dla tego samego tekstu. 

#### <a name="change-tagged-entity-in-example-utterance"></a>Zmień otagowaną jednostkę w przykład wypowiedź

Jeśli wstępnie skonstruowany obiekt jest tym samym tekstem lub tokenem co jednostka niestandardowa, zaznacz tekst w przykładzie wypowiedź i Zmień oznakowaną wypowiedź. 

Jeśli wstępnie utworzona jednostka jest oznaczona za pomocą więcej tekstu lub tokenów niż jednostka niestandardowa, istnieje kilka opcji rozwiązania tego problemu:

* [Usuwanie przykładowej metody wypowiedź](#remove-example-utterance-to-fix-tagging)
* [Usuń](#remove-prebuilt-entity-to-fix-tagging) prekompilowaną metodę Entity

#### <a name="remove-example-utterance-to-fix-tagging"></a>Usuń przykład wypowiedź, aby naprawić znakowanie 

Pierwszy wybór polega na usunięciu przykładu wypowiedź. 

1. Usuń przykład wypowiedź.
1. Przeszkol ponownie aplikację. 
1. Dodaj tylko wyraz lub frazę, która jest obiektem, który jest oznaczony jako wstępnie zbudowaną jednostkę, jako kompletny przykład wypowiedź. Słowo lub fraza nadal będzie mieć utworzoną wstępnie utworzoną jednostkę. 
1. Wybierz jednostkę w przykładowej wypowiedź na stronie **cel** i przejdź do jednostki niestandardowej i ponownie nauczenie. Powinno to uniemożliwić LUIS od oznaczania tego dokładnego tekstu jako wstępnie skompilowanej jednostki w dowolnym przykładzie wyrażenia długości, który używa tego tekstu. 
1. Dodaj cały oryginalny przykład wypowiedź z powrotem do zamiaru. Jednostka niestandardowa powinna nadal być oznaczona zamiast wstępnie skompilowanej jednostki. Jeśli jednostka niestandardowa nie jest oznaczona, należy dodać więcej przykładów tego tekstu w wyrażenia długości.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>Usuń prekompilowaną jednostkę, aby naprawić znakowanie

1. Usuń prekompilowaną jednostkę z aplikacji. 
1. Na stronie **cel** Oznacz jednostkę niestandardową w przykładzie wypowiedź.
1. Przeszkol aplikację.
1. Dodaj wstępnie utworzoną jednostkę z powrotem do aplikacji i nauczenie aplikacji. Ta poprawka zakłada, że wstępnie utworzona jednostka nie jest częścią jednostki złożonej.

## <a name="regular-expression-entity"></a>Jednostka wyrażenia regularnego 

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) wyodrębnia jednostkę na podstawie podania wzorca wyrażenia regularnego.

## <a name="simple-entity"></a>Prosta jednostka

A [jednostki prostej](reference-entity-simple.md) jest wartością maszyny do opanowania. Można go wyrazu lub frazy.
## <a name="entity-limits"></a>Limity jednostek

Przegląd [limity](luis-boundaries.md#model-boundaries) Aby dowiedzieć się, ile poszczególnych typów obiektu można dodać do modelu.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>Jeśli potrzebujesz więcej niż maksymalna liczba jednostek 

Może być konieczne użycie jednostek złożonych w połączeniu z rolami jednostek.

Composite jednostek reprezentują części całości. Na przykład jednostka złożone o nazwie PlaneTicketOrder niewykluczone jednostki podrzędne linii lotniczych, miejsce docelowe, DepartureCity, DepartureDate i PlaneTicketClass.

LUIS udostępnia również typ jednostki listy, która nie jest poznania maszynowego, ale umożliwia aplikacji LUIS określenie ustalonej listy wartości. Zobacz [granice LUIS](luis-boundaries.md) odwołania, aby zapoznać się ograniczenia typu listy jednostek. 

Jeśli te jednostki są rozpatrywane i nadal wymagają więcej niż limit, skontaktuj się z pomocą techniczną. Aby to zrobić, należy zebrać szczegółowe informacje o systemie, przejdź do [LUIS](luis-reference-regions.md#luis-website) witryny sieci Web, a następnie wybierz **pomocy technicznej**. Jeśli Twoja subskrypcja platformy Azure obejmują usługi pomocy technicznej, skontaktuj się z [technicznej platformy Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Następne kroki

Pojęcia dotyczące dobrze [wypowiedzi](luis-concept-utterance.md). 

Zobacz [Dodaj jednostki](luis-how-to-add-entities.md) Aby dowiedzieć się więcej o sposobie dodawania jednostki z aplikacją usługi LUIS.
