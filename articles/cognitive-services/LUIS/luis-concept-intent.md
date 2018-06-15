---
title: Opis opcji w aplikacjach LUIS na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co intencje znajdują się w aplikacji usługi inteligentnego opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/04/2018
ms.author: v-geberr
ms.openlocfilehash: 11aaa6c0891d7087556a82f3a818ef865869b766
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35356435"
---
# <a name="intents-in-luis"></a>Opcje w LUIS

Celem reprezentuje zadania lub akcji użytkownik chce wykonać. Cel lub celem wyrażone w użytkownika [utterance](luis-concept-utterance.md).

Zdefiniuj zestaw opcji, który odpowiada na akcje, które użytkownicy chcą w aplikacji. Na przykład aplikacja podróży definiuje kilka opcji:

Profile aplikacji w podróży   |   Przykład zniesławiających   | 
------|------|
 BookFlight     |   "Book mnie lot Rio następnym tygodniu" <br/> "Udać mnie do Rio na 24th" <br/> "Potrzebuję biletu płaszczyzny niedziela dalej do Rio de Janeiro"    |
 Pozdrowienie     |   "Hi" <br/>Tekst "hello" <br/>"Dobry"  |
 CheckWeather | "Co to jest pogody podobnie jak w Boston?" <br/> "Pokaż prognozy dla tego weekendowe" |
 Brak         | "W get mnie przepisu plik cookie"<br>"Czy Lakers win?" |

Wszystkie aplikacje dostarczane z wstępnie zdefiniowane opcje "[Brak](#none-intent-is-fallback-for-app)" który jest rezerwowej celem. 

## <a name="prebuilt-domains-provide-intents"></a>Wbudowane domeny zapewniają intencje
Oprócz intencje zdefiniowanych przez użytkownika można użyć opcji wbudowane z jednej z domen wbudowane. Aby uzyskać więcej informacji, zobacz [domeny wbudowane w aplikacjach LUIS](luis-how-to-use-prebuilt-domains.md) Aby dowiedzieć się więcej na temat dostosowywania intencje z wbudowane domeny do użycia w aplikacji.

## <a name="return-all-intents-scores"></a>Zwraca wyniki wszystkich opcji
Utterance przypisać zamierzone jednym. LUIS odebrania utterance w punkcie końcowym zwraca jedną zamiar top dla tego utterance. Jeśli chcesz wyniki dla wszystkich lokalizacji docelowych dla utterance, możesz podać `verbose=true` Flaga w ciągu zapytania API [wywołania punktu końcowego](https://aka.ms/v1-endpoint-api-docs). 

## <a name="intent-compared-to-entity"></a>Opcje w porównaniu do jednostki
Celem reprezentuje akcję chatbot powinno zająć dla użytkownika i opierają się na cały utterance. Jednostka reprezentuje słów ani fraz znajdująca się wewnątrz utterance. Utterance może mieć tylko jeden top oceniania zamiar, ale może mieć wiele jednostek. 

<a name="how-do-intents-relate-to-entities"></a> Utwórz celem podczas użytkownika _zamiar_ spowoduje wywołanie akcji w aplikacji klienta, takich jak wywołanie funkcji checkweather(). Następnie utwórz jednostkę do przedstawiania parametrów wymaganych do wykonania akcji. 

|Przykład celem   | Jednostka | Jednostki w przykładzie zniesławiających   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | {"type": "Lokalizacja", "entity": "seattle"}<br>{"type": "builtin.datetimeV2.date","entity": "jutro", "Rozwiązanie": "2018-05-23"} | Pogody jak w `Seattle` `tomorrow`? |
| CheckWeather | {"type": "date_range", "entity": "to weekendowe"} | Pokaż prognozy dla `this weekend` | 

## <a name="custom-intents"></a>Profile niestandardowe

Podobnie tych [zniesławiających](luis-concept-utterance.md) odpowiadają zamierzone jednym. Zniesławiających w Twoich zamiarów można użyć dowolnego [jednostki](luis-concept-entity-types.md) w aplikacji, ponieważ jednostek nie są specyficzne dla celem. 

## <a name="prebuilt-domain-intents"></a>Intencje wbudowane domeny

[Wbudowane domen](luis-how-to-use-prebuilt-domains.md) ma intencje z zniesławiających.  

## <a name="none-intent-is-fallback-for-app"></a>Żaden konwersji nie jest bazowy dla aplikacji
**Brak** celem jest celem wychwytywania lub rezerwowej. Służy do nauki LUIS zniesławiających, które nie są ważne w domenie aplikacji (podmiotu obszaru). **Brak** zamiar powinny mieć od 10 do 20% całkowitej zniesławiających w aplikacji. Nie podawaj żadnej. 

### <a name="none-intent-helps-conversation-direction"></a>Brak konwersji pomaga kierunek konwersacji
Gdy utterance jest obliczana jako Brak konwersji i zwrócony do chatbot z tym prognozowania bot można zadawać pytania więcej lub Zapewnij menu przekierować użytkownika do prawidłowych wyborów w chatbot. 

### <a name="no-utterances-in-none-intent-skews-predictions"></a>Nie zniesławiających żadna konwersji pochyla prognoz
Jeśli nie dodasz żadnych zniesławiających dla **Brak** konwersji, LUIS wymusza utterance spoza domeny do jednej z opcji domeny. Spowoduje to pochylanie wyniki prognozowania przez nauczania LUIS nieprawidłowe opcje utterance. 

### <a name="add-utterances-to-the-none-intent"></a>Dodaj zniesławiających brak konwersji
**Brak** zamiar zostanie utworzony, ale w celu puste. Wypełnić go zniesławiających, które nie należą do domeny. Dobrym utterance dla **Brak** jest służy element całkowicie poza aplikacji, a także branży aplikacji. Na przykład aplikacja podróży nie należy używać żadnych zniesławiających dla **Brak** które można powiązać podróży, takie jak zastrzeżenia rozliczeń żywności, gościnność, ładunku, rozrywka porządkowych. 

Jakiego rodzaju zniesławiających pozostało dla brak konwersji? Rozpoczynać się od określonego elementu z bot nie należy odpowiedzieć, takich "jakiego rodzaju kolorowanka ma niebieski zębów?" Jest to bardzo konkretne pytanie daleko poza aplikacji podróży. 

### <a name="none-is-a-required-intent"></a>Nie jest wymagane opcje
**Brak** celem jest wymagane opcje i nie może być usunięte ani zmienić jego nazwy.

## <a name="negative-intentions"></a>Cele ujemna 
Jeśli chcesz określić cele ujemną i dodatnią, takich jak "I **mają** samochodu" i "I **nie** mają samochodu", można utworzyć dwa profile (jeden pozytywnych i jeden ujemna) i dodaj odpowiednie zniesławiających dla Każdy. Lub można utworzyć zamierzone jednym i oznacz dwa różne warunki dodatnie i ujemne jako jednostki.  

## <a name="intent-balance"></a>Saldo konwersji
Profile domeny aplikacji powinien mieć saldo zniesławiających między każdym zamiar. Nie masz zamierzone jednym z 10 zniesławiających i przeznaczeniu innego z zniesławiających 500. To jest niezrównoważone. Jeśli ta sytuacja, przejrzyj profil konwersji z 500 zniesławiających, aby zobaczyć, jeśli wiele lokalizacji docelowych można zreorganizować do [wzorzec](luis-concept-patterns.md). 

**Brak** celem jest niedostępna w równowagi. Celem tego powinien zawierać 10% całkowitej zniesławiających w aplikacji.

## <a name="intent-limits"></a>Limity konwersji
Przegląd [limity](luis-boundaries.md#model-boundaries) Aby zrozumieć, jak wiele lokalizacji docelowych można dodać do modelu. 

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>Jeśli potrzebujesz więcej niż maksymalna liczba lokalizacji docelowych 
Najpierw należy rozważyć, czy system używa zbyt wiele lokalizacji docelowych. 

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Wiele lokalizacji docelowych można łączyć w jednej zamiar z jednostkami 
Opcje, które są zbyt podobne może utrudnić dla LUIS w celu ich rozróżnienia. Opcje powinny być różne do przechwytywania głównych zadań, które pyta użytkownika, ale nie trzeba przechwytywania ścieżki, co ma swój kod. Na przykład oddzielnych lokalizacji docelowych w podróży aplikacji może być BookFlight i FlightCustomerService, ale BookInternationalFlight i BookDomesticFlight są zbyt podobne. Jeśli system musi odróżnić je, użyj jednostek lub innych logiki zamiast lokalizacji docelowych. 

### <a name="dispatcher-model"></a>Dyspozytor modelu
Dowiedz się więcej o łączeniu LUIS i — strona główna aplikacji maker za pomocą [modelu wysyłania](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps). 

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Prosić o pomoc dla aplikacji z duża liczba lokalizacji docelowych
Zmniejszenie liczby intencje lub podzielenie Twojej lokalizacji docelowych do wielu aplikacji nie działa, należy się z pomocą techniczną. Jeśli subskrypcja platformy Azure zawiera usług pomocy technicznej, skontaktuj się z [pomocy technicznej platformy Azure](https://azure.microsoft.com/support/options/). 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [jednostek](luis-concept-entity-types.md), które są ważne słowa istotne dla lokalizacji docelowych
* Dowiedz się, jak [Dodawanie i zarządzanie intencje](luis-how-to-add-intents.md) w aplikacji LUIS.
* Przejrzyj opcje [najlepsze rozwiązania](luis-concept-best-practices.md)

[LUIS]:luis-reference-regions.md#luis-website