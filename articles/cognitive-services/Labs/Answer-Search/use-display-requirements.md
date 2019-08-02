---
title: Używanie i wyświetlanie wymagań — wyszukiwanie odpowiedzi projektu
titlesuffix: Azure Cognitive Services
description: Użyj i Wyświetl wymagania dla punktu końcowego wyszukiwania odpowiedzi projektu.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ROBOTS: NOINDEX
ms.openlocfilehash: 2b42d61fd887f166a08b78510d5eaacb8a7cdcb8
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706702"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Użycie wyszukiwania odpowiedzi projektu i wymagania dotyczące wyświetlania

Wymagania dotyczące użycia i wyświetlania mają zastosowanie do wszelkich implementacji zawartości i skojarzonych informacji, na przykład relacji, metadanych i innych sygnałów, dostępnych za pośrednictwem wywołań wyszukiwania w bazie wiedzy Bing, wyszukiwanie niestandardowe Bing, wyszukiwanie jednostek, wyszukiwanie obrazów Interfejsy API wyszukiwanie wiadomości, wyszukiwanie wideo, wyszukiwanie wizualne, wyszukiwanie w sieci Web, sprawdzanie pisowni i automatycznego sugerowania. Szczegóły implementacji związane z tymi wymaganiami można znaleźć w dokumentacji dotyczącej określonych funkcji i wyników.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Sprawdzanie pisowni Bing i interfejs API automatycznego sugerowania Bing.

Nie:

- Kopiowanie, przechowywanie lub buforowanie wszelkich danych otrzymywanych z sprawdzanie pisowni Bing lub automatyczne sugerowanie Bing interfejsów API
- Używaj danych otrzymywanych z sprawdzanie pisowni Bing lub automatyczne sugerowanie Bing interfejsów API jako części uczenia maszynowego lub podobnej aktywności algorytmu, aby szkolić, oszacować lub ulepszyć nowe lub istniejące usługi, które mogą być oferowane przez użytkownika lub inne osoby.

## <a name="2-definitions"></a>2. Definicje

- "odpowiedź" odnosi się do kategorii wyników zwróconych w odpowiedzi. Na przykład odpowiedź z interfejs API wyszukiwania w sieci Web Bing może obejmować odpowiedzi w kategoriach wyników, obrazów, wideo i wiadomości na stronie sieci Web;
- "odpowiedź" oznacza wszystkie odpowiedzi i skojarzone dane odebrane w odpowiedzi na jedno wywołanie interfejsu API wyszukiwania;
- "wynik" odnosi się do elementu informacji w odpowiedzi. Na przykład zestaw danych połączonych z pojedynczym artykułem z wiadomościami jest wynikiem odpowiedzi na wiadomość.
- "Wyszukiwania interfejsów API" oznacza zbiorczo wyszukiwanie niestandardowe Bing, wyszukiwanie jednostek, wyszukiwanie obrazów, wyszukiwanie wiadomości, wyszukiwanie wideo, wyszukiwanie wizualne i wyszukiwanie w sieci Web interfejsów API. 


## <a name="3-search-apis"></a>3. interfejsy API wyszukiwania

Wymagania zawarte w tej sekcji 3 dotyczą interfejsów API wyszukiwania.

**Z. Środowisko wyszukiwania w Internecie.** Wszystkie dane zwrócone w odpowiedzi mogą być używane tylko w środowiskach wyszukiwania internetowego. Internetowa obsługa wyszukiwania oznacza zawartość wyświetlaną w odpowiedni sposób: 
- jest istotne i odpowiada na zapytanie bezpośrednie użytkownika końcowego lub inne wskazanie zainteresowania wyszukiwania użytkownika (na przykład zapytania wyszukiwania wskazanego przez użytkownika); 
- pomaga użytkownikom w znalezieniu źródeł danych i przejściu do nich (na przykład podane adresy URL są implementowane jako hiperlinki, dzięki czemu zawartość lub przypisanie jest w sposób niewidoczny, wyświetlany w danych). lub, jeśli interfejs API wyszukiwania jednostek Bing, w sposób widoczny link do adresu URL bing.com podany w odpowiedzi, który umożliwia użytkownikowi przechodzenie do wyników wyszukiwania dla odpowiedniego zapytania na bing.com;
- zawiera wiele wyników dla użytkownika końcowego, które mogą zostać wybrane (na przykład są wyświetlane różne wyniki odpowiedzi z wiadomościami lub wszystkie wyniki w przypadku, gdy zostanie zwróconych mniej niż kilka kilku); 
- jest ograniczona do ilości odpowiedniej do obsłużenia przeszukiwania (na przykład miniatury obrazu mają rozmiar miniatury proporcjonalnie do wyświetlania użytkownika). 
- zawiera widoczne wskazanie użytkownikowi końcowemu, że zawartość jest wynikami wyszukiwania w Internecie (na przykład, że zawartość jest "z sieci Web"); lub
- zawiera inne kombinacje miar, które są odpowiednie do zapewnienia, że korzystanie z danych otrzymanych z interfejsów API wyszukiwania nie narusza żadnych obowiązujących przepisów ani praw innych firm (na przykład w przypadku polegania na licencji Creative Commons Attribution, zgodnie z odpowiednią licencją). warunki). Zapoznaj się z doradcami prawnymi, aby określić, jakie środki mogą być odpowiednie.
Jedyny wyjątek od wymagania dotyczącego środowiska wyszukiwania w Internecie dotyczy odnajdowania adresów URL, zgodnie z opisem w sekcji 3E (Odnajdywanie adresów URL bez wyświetlania). 

**B. Dotyczących.** Nie:

- Skopiuj, przechowuj lub Buforuj dowolne dane z odpowiedzi (z wyjątkiem przechowywania w zakresie dozwolonym w sekcji "ciągłość usługi" poniżej). 
- Używaj danych otrzymanych z interfejsów API wyszukiwania jako części każdej uczenia maszynowego lub podobnej aktywności algorytmu, aby szkolić, oszacować lub ulepszyć nowe lub istniejące usługi, które mogą być oferowane przez użytkownika lub inne osoby.
- Zmodyfikuj zawartość wyników (inne niż w celu ich sformatowania w taki sposób, aby nie naruszać innych wymagań), chyba że jest to wymagane przez prawo ani przez firmę Microsoft; 
- Pomiń przypisanie i adresy URL skojarzone z zawartością wyniku;
- Zmiana kolejności, łącznie z pominięciem, wyniki wyświetlane w odpowiedzi w przypadku podaniu zamówienia lub klasyfikacji (dla interfejs API wyszukiwania niestandardowego Bing ta reguła nie dotyczy zmiany kolejności wdrożonej za pomocą portalu customsearch.ai), chyba że jest to wymagane przez prawo ani uzgodnione przez firmę Microsoft ;
- Wyświetlanie innej zawartości w ramach dowolnej części odpowiedzi w taki sposób, aby użytkownik końcowy mógł uznać, że druga zawartość jest częścią odpowiedzi; 
- Wyświetlaj reklamy, które nie są dostarczane przez firmę Microsoft na żadnej stronie, która wyświetla jakąkolwiek część odpowiedzi; — Wyświetlaj reklamy z odpowiedziami (i) z obrazu Bing, wiadomości lub wyszukiwanie wideo interfejsów API; lub (II), które są filtrowane lub ograniczone głównie (lub wyłącznie) do wyników obrazów, wiadomości i/lub wideo.

**S. Powiadomienia i znakowanie.** 

- W widocznym miejscu dołączenie do zasad zachowania poufności informacji firmy https://go.microsoft.com/fwlink/?LinkId=521839 Microsoft, które są dostępne w tym miejscu w każdym punkcie środowiska użytkownika (UX), które oferuje użytkownikowi możliwość wprowadzenia zapytania wyszukiwania. Oznacz hiperłącze "zasady zachowania poufności informacji firmy Microsoft".
- W widocznym miejscu Wykorzystaj znakowanie Bing zgodne z wytycznymi dostępnymi w https://go.microsoft.com/fwlink/?linkid=833278 systemie, w każdym miejscu w środowisku użytkownika, które oferują użytkownikowi możliwość wprowadzania zapytania wyszukiwania.  Takie oznakowanie musi jasno wskazywać użytkownikowi, że firma Microsoft umożliwia korzystanie z funkcji wyszukiwania internetowego.
- Użytkownik może pogrupować każdą odpowiedź (lub część odpowiedzi) wyświetlaną w interfejsie API sieci Web, obrazów, wiadomości i wideo usługi Bing do firmy https://go.microsoft.com/fwlink/?linkid=833278 Microsoft zgodnie z opisem w artykule, chyba że firma Microsoft określi inaczej do użycia. 
- Nie należy określać odpowiedzi (lub fragmentów odpowiedzi), które są wyświetlane w interfejs API wyszukiwania niestandardowego Bing do firmy Microsoft, chyba że firma Microsoft postanowi pisać na potrzeby danego zastosowania.


**D. Przesyłanie odpowiedzi.** W przypadku umożliwienia użytkownikowi przetransferowania odpowiedzi z interfejsu API wyszukiwania do innego użytkownika, na przykład za pośrednictwem aplikacji do obsługi wiadomości lub ogłaszania mediów społecznościowych, należy zastosować następujące czynności: 
- Przesłane odpowiedzi muszą:
  - Składają się z zawartości, która nie została zmodyfikowana z zawartości odpowiedzi wyświetlanej dla użytkownika przekazującego (dopuszczalne są zmiany formatowania);
  - Nie dołączaj żadnych danych do formularza metadanych;
  - W przypadku odpowiedzi z interfejsów API sieci Web, obrazów, wiadomości i wideo Bing język wyświetlania wskazujący, że odpowiedź została uzyskana za pośrednictwem środowiska wyszukiwania internetowego obsługiwanego przez usługę Bing (na przykład "obsługiwane przez usługę Bing", Dowiedz się więcej na temat tego obrazu w usłudze Bing lub przy użyciu logo Bing);
  - W przypadku odpowiedzi z interfejs API wyszukiwania niestandardowego Bing, język wyświetlania wskazujący, że odpowiedź została uzyskana za pośrednictwem środowiska wyszukiwania internetowego (na przykład "Dowiedz się więcej o tym wyniku wyszukiwania");
  - W widoczny sposób wyświetlona zostanie pełna kwerenda użyta do wygenerowania odpowiedzi. lub
  - Dołącz widoczne łącze lub podobne przypisanie do bazowego źródła odpowiedzi, bezpośrednio lub za pomocą aparatu wyszukiwania (bing.com, m.bing.com lub niestandardowej usługi wyszukiwania).
- Nie można zautomatyzować transferu odpowiedzi. Przekazanie musi być inicjowane przez akcję użytkownika jasno evidencing zamiar przetransferowania odpowiedzi.
- Użytkownik może zezwolić tylko na transfer odpowiedzi, które były wyświetlane w odpowiedzi na kwerendę transferu użytkownika.

**E. Ciągłość usługi.** Nie Kopiuj, przechowuj ani nie Buforuj żadnych danych z odpowiedzi interfejsu API wyszukiwania. Jednak w celu zapewnienia ciągłości dostępu do usługi i renderowania danych można zachować wyniki wyłącznie w następujących warunkach:

**Pliku.** Użytkownik końcowy może zachowywać wyniki na urządzeniu przez krótsze (i) 24 godziny od czasu zapytania lub (II), dopóki użytkownik końcowy nie wyśle kolejnego zapytania pod kątem zaktualizowanych wyników, pod warunkiem, że zachowane wyniki mogą być używane tylko:

- Aby umożliwić użytkownikowi końcowemu dostęp do wyników, które wcześniej zwracały do tego użytkownika końcowego na tym urządzeniu (na przykład w przypadku przerwania działania usługi); oraz
- do przechowywania wyników zwróconych dla proaktywnego zapytania spersonalizowanych w oczekiwaniu na potrzeby użytkowników końcowych na podstawie sygnałów użytkowników końcowych (na przykład w przypadku przewidywanych przerw w działaniu usługi).

**Server.** Wyniki specyficzne dla jednego użytkownika końcowego mogą być bezpiecznie przechowywane na serwerze, który kontrolujesz i wyświetlają tylko zachowane wyniki:

- Aby umożliwić użytkownikowi końcowemu dostęp do historycznego raportu wyników, które zostały wcześniej zwrócone do tego użytkownika w rozwiązaniu, pod warunkiem, że wyniki mogą nie być (i) przechowywane przez ponad 21 dni od momentu początkowego zapytania użytkownika końcowego i (II) wyświetlane w odpowiedzi na koniec u nowe lub powtórzone zapytanie dotyczące użytkownika; oraz
- do przechowywania wyników zwróconych dla proaktywnego zapytania, które są spersonalizowane w przewidywaniu potrzeb użytkownika końcowego w zależności od tego, czy są to sygnały z tego samego (i) 24 godziny od momentu zapytania lub (II), dopóki użytkownik końcowy nie wyśle kolejnego zapytania pod kątem zaktualizowanych wyników.

Po każdym zatrzymaniu wyniki dla określonego użytkownika nie mogą być commingled z wynikami dla innego użytkownika, tj. wyniki poszczególnych użytkowników muszą być przechowywane i dostarczane oddzielnie.

**Główny.** Dla wszystkich prezentacji zachowanych wyników:

- Uwzględnij jasne, widoczne informacje o czasie wysłania zapytania,
- zaprezentowanie użytkownikowi przycisku lub podobnej metody w celu ponownego wykonania zapytania i uzyskania zaktualizowanych wyników 
- Zachowaj znakowanie Bing w prezentacji wyników i
- Usuń (i w razie konieczności Odświeżaj nowe zapytanie) zapisane wyniki w określonych przedziale czasowym.

**N. Odnajdywanie adresów URL bez wyświetlania.** Odpowiedzi wyszukiwania można używać tylko w przypadku wyszukiwania niepochodzącego z Internetu, tylko w celu odnajdywania adresów URL źródeł informacji, które odpowiadają na zapytanie od użytkownika lub klienta. Możesz skopiować takie adresy URL w raporcie lub podobnej odpowiedzi dostarczanej (i) tylko dla tego użytkownika lub klienta, w odpowiedzi na zapytanie i (II), które zawiera znaczną dodatkową zawartość istotną dla zapytania. Wymagania zawarte w sekcjach 3A przez 3E tego zastosowania i wymagania dotyczące wyświetlania nie mają zastosowania do tego niewyświetlanego użycia, z wyjątkiem: 

- Nie należy umieszczać w pamięci podręcznej, kopiować ani przechowywać danych ani zawartości z, ani pochodzić z odpowiedzi wyszukiwania, innych niż opisane powyżej ograniczonego kopiowania adresów URL;
- Upewnij się, że używane dane (w tym adresy URL) otrzymane z interfejsów API wyszukiwania nie naruszają obowiązujących przepisów lub praw innych firm; lub
- Nie korzystasz z danych (łącznie z adresami URL) otrzymywanych z interfejsów API wyszukiwania jako części dowolnego indeksu wyszukiwania lub uczenia maszynowego lub podobnej aktywności algorytmu, aby utworzyć uczenie, oszacować lub poprawić usługi, które mogą być oferowane przez użytkownika lub inne osoby.

## <a name="next-steps"></a>Następne kroki
[Wyszukiwanie odpowiedzi — Omówienie](overview.md)
