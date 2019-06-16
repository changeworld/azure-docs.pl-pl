---
title: Jak działa Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer korzysta z usługi machine learning dowiedzieć się, jakie działania do użycia w kontekście. Każdej pętli learning ma modelu, który jest uczony wyłącznie na dane, które zostało wysłane do niego za pośrednictwem rangę i wywołuje nagrody. Każdy pętla nauki jest całkowicie niezależna od siebie nawzajem.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: d0a0a6101d876493188426d19f2fa845d20ee274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055463"
---
# <a name="how-personalizer-works"></a>Jak działa usługa Personalizacja

Personalizer korzysta z usługi machine learning dowiedzieć się, jakie działania do użycia w kontekście. Każdej pętli learning ma modelu, który jest uczony wyłącznie na dane, które zostały wysłane do niego za pośrednictwem **ranga** i **nagradzania** wywołania. Każdy pętla nauki jest całkowicie niezależna od siebie nawzajem. Tworzenie pętli dla każdej części lub zachowanie aplikacji, który chcesz spersonalizować uczenia.

Dla każdej pętli **wywołania interfejsu API ranga z** na podstawie bieżącego kontekstu, za pomocą:

* Lista dostępnych akcji: zawartości elementów, z którego można wybrać akcję najważniejsze.
* Lista [funkcji kontekstu](concepts-features.md): kontekstowe odpowiednie dane, takie jak użytkownika, zawartości i kontekstu.

**Ranga** API zdecyduje się użyć:

* _Wykorzystać_: Bieżący model można określić najlepsze działanie na podstawie ostatnich danych.
* _Explore_: Wybierz inną akcję zamiast najważniejszych działań.

**Nagradzania** interfejsu API:

* Zbiera dane do nauczenia modelu, rejestrując funkcji i nagrody wyniki każdego wywołania rangi.
* Używa tych danych do aktualizacji modelu na podstawie ustawień określonych w _zasad Learning_.

## <a name="architecture"></a>Architektura

Na poniższej ilustracji przedstawiono przepływ architektury wywołania wywołania rangi i nagrody:

![tekst alternatywny](./media/how-personalizer-works/personalization-how-it-works.png "sposób działania personalizacji")

1. Personalizer używa wewnętrznego modeli sztucznej Inteligencji, aby określić pozycję akcji.
1. Usługa Określa, czy należy wykorzystać w bieżącym modelu lub Przejrzyj nowe opcje dla modelu.  
1. Wynik klasyfikacji są wysyłane do Centrum zdarzeń.
1. Gdy Personalizer otrzymuje nagrody, nagrody są wysyłane do Centrum zdarzeń. 
1. Ranga i osób trzecich są powiązane.
1. Model sztucznej Inteligencji jest aktualizowana, na podstawie wyników korelacji.
1. Aparat wnioskowania jest aktualizowany w nowym modelu. 

## <a name="research-behind-personalizer"></a>Badań za Personalizer

Personalizer opiera się na najnowocześniejsze nauki i badań w zakresie [uczenia przez wzmacnianie](concepts-reinforcement-learning.md) tym dokumenty, badania i bieżące obszary eksploracji w Microsoft Research.

## <a name="terminology"></a>Terminologia

* **Nauka pętli**: Pętla learning można utworzyć dla każdej części aplikacji, który może być korzystne personalizacji. Jeśli masz więcej niż jednym środowisku spersonalizować, Utwórz pętli dla każdego. 

* **Akcje**: Akcje są elementy zawartości, takie jak produkty lub promocji do wyboru. Personalizer wybiera najważniejsze akcji do przedstawienia użytkownikom, nazywanej _nagradzaj akcji_, za pomocą interfejsu API rangi. Każda akcja może mieć funkcje przekazana z żądaniem rangi.

* **Kontekst**: Aby zapewnić bardziej precyzyjne rangę, zawierają informacje kontekstu, na przykład:
    * Użytkownika.
    * Urządzenie, które znajdują się na. 
    * Bieżąca godzina.
    * Inne dane dotyczące bieżącej sytuacji.
    * Historyczne dane dotyczące użytkownika lub kontekstu.

    Konkretnej aplikacji może być informacje w innym kontekście. 

* **[Funkcje](concepts-features.md)** : Jednostka informacje kontekstu użytkownika lub zawartości elementu.

* **Nagroda**: Miara jak użytkownika wysłanych jako odpowiedzi do interfejsu API ranga zwracane Akcja jako wynik od 0 do 1. Wartość od 0 do 1 jest ustawiana przez własną logikę biznesową, w oparciu o sposób wyboru pomogła osiągnąć cele biznesowe personalizacji. 

* **Eksploracja**: Usługa Personalizer rozważa, gdy zamiast zwracać najlepsze działanie, wybiera ona różne akcje dla użytkownika. Usługa Personalizer pozwala uniknąć odejściem od tego stanu stagnację i dostosowują się do zachowania użytkowników stałe, eksplorując szczegóły. 

* **Czas trwania eksperymentu**: Ilość czasu oczekiwania usługi Personalizer wynagrodzenia, już od momentu wywołania rangi wystąpiło dla tego zdarzenia.

* **Nieaktywne zdarzenia**: Nieaktywne zdarzenia jest jednym gdy wywołujesz rangę, ale nie masz pewności, że użytkownik nigdy nie zobaczy wynik, ze względu na decyzje dotyczące aplikacji klienta. Nieaktywne zdarzenia umożliwiają tworzenie i przechowywanie wyników personalizacji, a następnie zdecydować odrzucić je później, bez wywierania wpływu na model uczenia maszynowego.

* **Model**: Personalizer model przechwytuje wszystkie dane, które uzyskaliśmy informacje dotyczące zachowania użytkowników, pobieranie danych szkoleniowych z kombinacja argumentów, wysyłanych do rangi i nagrody wywołań i z zachowaniem szkolenia, określane przez polityki uczenia. 

## <a name="example-use-cases-for-personalizer"></a>Przykładowe przypadki użycia Personalizer

* Wyjaśnienia intencji & uściślania: użytkownicy mają lepiej, gdy ich celem jest pomoc usuwaj, podając opcję spersonalizowaną do poszczególnych użytkowników.
* Sugestie dotyczące menu i opcje domyślne: mają bot zasugerować najprawdopodobniej elementu w sposób spersonalizowany jako pierwszego kroku, zamiast prezentowanie impersonal menu lub listy alternatyw.
* Bot cech & ton: dla botów, które mogą się różnić tonu, poziom szczegółowości i styl pisania, należy wziąć pod uwagę różne cechy w sposób spersonalizowany.
* Zawartość powiadomień & alert: Zdecyduj, jaki tekst do użycia dla alertów w celu angażowania użytkowników więcej.
* Powiadomienie & chronometrażu alertu: zapewnić spersonalizowany uczenie się ich czas wysyłania powiadomień do użytkowników, aby skontaktować się z nimi więcej.

## <a name="checklist-for-applying-personalizer"></a>Lista kontrolna dotycząca stosowanie Personalizer

Personalizer można stosować w sytuacjach, gdzie:

* Masz firmy lub cel jego użyteczność aplikacji.
* Odbywa się się w Twojej aplikacji, gdzie udostępniających kontekstowych decyzji co do wyświetlenia użytkownikom poprawi tego celu.
* Najlepszy wybór można i powinny zostać uzyskane od wynik za wynagrodzeniem zachowanie i łączna liczba zbiorowe użytkownika.
* Korzystanie z uczenia maszynowego na potrzeby personalizacji następuje [wytycznych Użyj odpowiada](ethics-responsible-use.md) i opcje dla Twojego zespołu.
* Decyzja może być wyrażona jako klasyfikacji do najlepszej opcji ([akcji](concepts-features.md#actions-represent-a-list-of-options) z ograniczony zestaw opcji.
* Jak działało wybór może zostać obliczony przez logikę biznesową, mierząc niektóre aspekty zachowania użytkowników i przedstawienie jej numer z zakresu od -1 do 1.
* Wynik za wynagrodzeniem nie zachowa zbyt wiele czynników wpływu czynników mylących lub zewnętrzne, specjalnie czas trwania eksperymentu tyle niskie, że wynik za wynagrodzeniem może zostać obliczony, gdy są one nadal obowiązują.
* Kontekst dla ranga można wyrazić słownika zawierającego co najmniej 5 funkcje, które Twoim zdaniem może pomóc wybrać odpowiednie opcje, a które nie zawierają danych osobowych.
* Informacje o każdej akcji w formie słownika atrybutów co najmniej 5 lub funkcji, które Twoim zdaniem pomoże Personalizer dokonanie dobrego wyboru.
* Przechowywanie danych długich wystarczających do historii interakcji co najmniej 100 000.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Uwagi dotyczące stosowania Personalizer uczenia maszynowego

Personalizer opiera się na wzmocnienie uczenia podejście do usługi machine learning, która pobiera prowadzone przez opinii, że nadajesz mu. 

Personalizer dowiesz się najlepiej w sytuacjach, gdzie:
* Ma wystarczającej liczby zdarzeń na bieżąco optymalne personalizacji, jeśli problem drifts wraz z upływem czasu (takie jak preferencje w wiadomości lub sposób). Personalizer dostosuje się do ciągłych zmian w rzeczywistych warunkach, ale wyniki nie będą optymalne, jeśli nie ma wystarczającej liczby zdarzeń i danych, aby dowiedzieć się więcej z próbę odnalezienia i rozliczenia w nowych wzorców. Należy wybrać wystarczająco często wykonywanymi przypadek użycia. Należy wziąć pod uwagę, wyszukiwanie przypadków użycia, które zdarzają się co najmniej 500 razy dziennie.
* Kontekst akcji się wystarczająco dużo funkcje, aby ułatwić szkolenia.
* Ma mniej niż 50 akcje w przypadku rangi na wywołanie.
* Ustawienia przechowywania danych umożliwiają Personalizer zbierania wystarczającej ilości danych do wykonywania oceny w trybie offline i zasady optymalizacji. Jest to zazwyczaj punktów danych co najmniej 50 000.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Jak używać Personalizer w aplikacji sieci web

Dodawanie pętli do aplikacji sieci web obejmuje:

* Ustal, jakiego środowiska korzystania z spersonalizować, jakie akcje i funkcje masz, jakie funkcje kontekst do użycia, i jakie nagradzania zostanie ustawiony.
* Dodaj odwołanie do zestawu SDK personalizacji w aplikacji.
* Gdy wszystko jest gotowe spersonalizować, należy wywołać interfejs API rangi.
* Store identyfikator zdarzenia. Możesz wysłać nagradzania przy użyciu interfejsu API za wynagrodzeniem później.
1. Wywołaj Aktywuj zdarzenia po upewnieniu się, że użytkownik widział spersonalizowanej stronie.
1. Poczekaj, aż wyboru użytkownika dotyczącego rangi zawartości. 
1. Wywołaj interfejs API za wynagrodzeniem, aby określić, jak dobrze czy dane wyjściowe z interfejsu API rangi.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Jak używać Personalizer z czatbot

W tym przykładzie pojawi się, jak Wyraź sugestię domyślne, zamiast wysyłać każdym razem, gdy użytkownik dół szereg menu i opcji za pomocą personalizacji.

* Pobierz [kodu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) dla tego przykładu.
* Konfigurowanie rozwiązania programu botów. Upewnij się opublikować aplikację usługi LUIS. 
* Zarządzaj rangi i interfejsu API za wynagrodzeniem wywołania dla botów.
    * Dodaj kod, aby zarządzać intencję ora usługi LUIS. Jeśli **Brak** zwracany jest najważniejsze przeznaczenie lub oceny najważniejszych celem jest poniżej progu logiki Twojej firmy, wysyła do Personalizer do rangi intencji listy intencji.
    * Umożliwia wyświetlenie listy intencji użytkownika jako łącza można wybierać z pierwszym intencji, które są pozycjonowane górnej zamiar z odpowiedzi interfejsu API rangi.
    * Przechwytywanie wybranych przez użytkownika i Wyślij wiadomość w wywołaniu interfejsu API osób trzecich. 

### <a name="recommended-bot-patterns"></a>Wzorce bot zalecane

* Za każdym razem, gdy uściślania jest potrzebna, a nie buforowania wyniki dla każdego użytkownika, należy wykonywać wywołania interfejsu API programu Personalizer rangi. Wynik intencji usuwania niejednoznaczności w nazwach mogą ulec zmianie wraz z upływem czasu dla jednej osoby, a umożliwiając API ranga Eksplorowanie wariancji przyspieszy learning ogólnej.
* Wybierz interakcję z wielu użytkowników, aby mieć wystarczającej ilości danych, aby spersonalizować. Na przykład wstępne pytania może być lepsze dopasowanie niż mniejsze wyjaśnienia w wykresie konwersacji, który tylko kilku użytkowników może nawiązać połączenia z.
* Umożliwiają ranga interfejsu API "pierwsza sugestia jest odpowiednia" konwersacji, gdzie użytkownik pobiera monit "Czy chcesz X?" lub "Czy chodziło Ci o X?" i użytkownik może po prostu potwierdzić; w przeciwieństwie podanie opcji dla użytkownika, w którym należy wybrać z menu. Na przykład użytkownik: "Chcę uporządkować kawy" Bot: "Czy chcesz double espresso?". W ten sposób sygnału nagradzania również jest silne, ponieważ odnoszą się bezpośrednio do jednego sugestii.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Jak używać Personalizer dzięki rozwiązaniu zalecenia

Aby odfiltrować duże katalogu do kilku elementów, które następnie mogą być przedstawiane jako 30 możliwych działań wysyłanych do interfejsu API ranga za pomocą usługi aparatu rekomendacji.

Za pomocą aparaty rekomendacji Personalizer:

* Konfigurowanie [rozwiązania zalecenie](https://github.com/Microsoft/Recommenders/). 
* Podczas wyświetlania strony, należy wywołać Model zalecenie krótką listę zaleceń.
* Wywołaj personalizacji pozycji danych wyjściowych zalecenie rozwiązania.
* Wyślij opinię o akcję użytkownika przy użyciu wywołania interfejsu API osób trzecich.


## <a name="pitfalls-to-avoid"></a>Pułapki, których należy unikać

* Nie używaj Personalizer gdzie spersonalizowane zachowanie nie jest coś, co może być rozpoznana dla wszystkich użytkowników, ale raczej coś, co należy pamiętać, pod kątem określonych użytkowników lub pochodzi z listy alternatyw specyficzne dla użytkownika. Na przykład za pomocą Personalizer sugeruje w pierwszej kolejności pizza listę 20 elementów menu możliwe jest przydatna, ale lista kontaktów który kontakt do wywoływania z użytkowników, gdy wymagających pomocy dotyczącej dziećmi (na przykład "babcia") nie jest coś, co jest możliwa w podstawowym użytkownika.


## <a name="adding-content-safeguards-to-your-application"></a>Dodawanie zawartości zabezpieczeń do aplikacji

Jeśli aplikacja zezwala dla dużych wariancji w zawartość wyświetlaną dla użytkowników i niektórych, czy zawartość może być niebezpieczne lub nieodpowiednie dla niektórych użytkowników, należy zaplanować dalej upewnij się, że odpowiednie środki zabezpieczające znajdują się w miejscu, aby uniemożliwić użytkownikom dostępu do przyjęcia zawartość. Wzorzec najlepsze, aby zaimplementować zabezpieczenia jest: Wzorzec najlepsze, aby zaimplementować zabezpieczenia jest:
    * Uzyskaj listę akcji do rangi.
    * Odfiltruj te, które nie są do odbiorców.
    * Rank tylko te działania możliwego do użycia.
    * Wyświetlanie pierwszych randze spośród wszystkich akcji dla użytkownika.

W niektórych architekturach powyżej sekwencji może być trudne do zaimplementowania. W takiej sytuacji istnieje alternatywny sposób implementowania zabezpieczenia po klasyfikacji, ale należy się więc akcje, które znajduje się poza ochrony nie są używane do nauczenia modelu Personalizer.

* Uzyskaj listę akcji do rangi przy użyciu uczenia zdezaktywowane.
* Akcje rangi.
* Sprawdź, jeśli nie jest wygodną najważniejsze czynności.
    * Możliwego do użycia są najważniejsze czynności, aktywować nauki to RANK, a następnie wyświetl ją do użytkownika.
    * Jeśli najważniejsze akcja nie jest możliwe, nie Aktywuj uczenie dla tej klasyfikacji i podejmowania decyzji za pomocą własnej logiki lub alternatywne podejścia do wyświetlenia dla użytkownika. Nawet wtedy, gdy używasz opcji rangi drugie najlepsze, nie zostanie aktywowany uczenie dla tej klasyfikacji.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Sprawdzanie, czy odpowiednie skuteczności Personalizer

Można monitorować skuteczność Personalizer okresowo wykonując [ocen w trybie offline](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Kolejne kroki

Zrozumienie [którym używasz Personalizer](where-can-you-use-personalizer.md).