---
title: Projekt Użyj wyszukiwania odpowiedzi i wyświetlić wymagania - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
description: Użyj i wyświetlić wymagania dla punktu końcowego projektu odpowiedzi wyszukiwania.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 6e8eaaaa2c83a1420f2de86b23e15f4f19f7a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348708"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Projekt Użyj wyszukiwania odpowiedzi i wyświetlić wymagania

Wymagania dotyczące użycia i wyświetlania dotyczą implementacji zawartości i skojarzonych informacji, na przykład relacje, metadane i innymi sygnałami, dostępne za pośrednictwem wywołania do wyszukiwania usługi Bing wiedzy, wyszukiwanie niestandardowe Bing, jednostek wyszukiwania, wyszukiwania obrazu Wyszukiwanie grup dyskusyjnych, wideo wyszukiwanie, Visual, wyszukiwanie w sieci Web sprawdzanie pisowni i interfejsy API automatycznego sugerowania. Szczegóły implementacji związane z tych wymagań znajdują się w dokumentacji dla określonych funkcji i wyników.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Moduł sprawdzania pisowni usługi Bing i Bing automatycznego sugerowania interfejsu API.

Nie:

- kopiowanie, przechowywania i pamięci podręcznej danych otrzymywanych z sprawdzania pisowni usługi Bing lub interfejsy API automatycznego sugerowania usługi Bing
- Użyj danych otrzymywanych z sprawdzania pisowni usługi Bing lub interfejsy API automatycznego sugerowania usługi Bing w ramach usługi machine learning lub podobnym działaniu algorytmicznego uczenie, ocenę lub poprawy nowych lub istniejących usług, które możesz lub stron trzecich mogą oferować.

## <a name="2-definitions"></a>2. Definicje

- "odpowiedzi" odwołuje się do kategorii wyników zwróconych w odpowiedzi. Na przykład odpowiedzi z interfejsu API wyszukiwania usługi Bing sieci Web mogą obejmować odpowiedzi w kategorii wyników strony sieci Web, obrazów, wideo i wiadomości;
- "odpowiedź" oznacza, że wszystkie odpowiedzi i skojarzone dane otrzymane w odpowiedzi na wywołanie interfejsu API Search;
- "wynik" odwołuje się do elementu informacji w odpowiedzi. Na przykład zestaw danych związanych z jednym artykuł jest wyniku w odpowiedzi na wiadomości.
- "Interfejsy API wyszukiwania" oznacza zbiorczo wyszukiwania usługi Bing niestandardowe, jednostek wyszukiwania, wyszukiwania obrazu, wyszukiwania wiadomości, wyszukiwania wideo, Visual wyszukiwania i interfejsów API sieci Web wyszukiwania. 


## <a name="3-search-apis"></a>3. interfejsy API wyszukiwania

Interfejsy API wyszukiwania mają zastosowanie wymagania w tej sekcji 3.

**. Internet wyników wyszukiwania.** Wszystkie dane zwrócone w odpowiedzi można używać tylko w ramach wyszukiwania internet. Środowisko wyszukiwania internet oznacza zawartość wyświetlane, gdy ma to zastosowanie: 
- ma zastosowanie i odbierać zapytania bezpośredniego użytkownika końcowego lub inne wskazanie użytkownika wyszukiwania odsetek i opcje (na przykład zapytania wyszukiwania wskazanych przez użytkownika); 
- Umożliwia użytkownikom znajdowanie i przejdź do źródła danych (na przykład podana adresy URL są zaimplementowane jako hiperłącza, autorstwa lub zawartości jest łączem sposób widoczny jest wyświetlana z danymi); lub jeśli z interfejsu API wyszukiwania jednostki Bing, widoczny łącze do adresu URL bing.com podanej w odpowiedzi, który umożliwia użytkownikowi nawigację do wyników wyszukiwania dla odpowiednich zapytania na bing.com;
- zawiera wiele wyników dla użytkownika końcowego można wybierać (na przykład, wyświetlanych jest kilka wyników z wiadomości odpowiedzi, lub zwracane są wszystkie wyniki, jeśli jest to mniej niż kilka); 
- jest ograniczona do wysokości odpowiednie w celu wyszukiwania (na przykład obraz miniatury są miniatury o rozmiarze proporcjonalnie do wyświetlania przez użytkownika); 
- obejmuje widoczne żadne oznaki dla użytkownika końcowego, że zawartość jest wyniki wyszukiwania w Internecie (na przykład oświadczenie czy zawartość jest "z sieci web"); i
- zawiera dowolną kombinację środków należy upewnić się, że korzystanie z danych otrzymywanych z interfejsy API wyszukiwania nie naruszanie obowiązujących przepisów lub praw innych firm (na przykład, jeśli polegania na licencją Creative Commons spełniające obowiązującej licencji warunki). Zapoznaj się przez użytkownika prawne doradcy do określenia, jakie środki mogą być odpowiednie.
Jedynym wyjątkiem od wymagań środowiska wyszukiwania internet jest odnajdywania adresu URL, zgodnie z opisem w sekcji 3E (inne niż wyświetlany adres URL odnajdywania) poniżej. 

**B. Ograniczenia.** Nie:

- kopiowanie, przechowywania i pamięci podręcznej danych z odpowiedzi (z wyjątkiem przechowywania w zakresie dozwolonym przez następujących sekcji "Ciągłości usługi"); 
- Użyj danych otrzymywanych z interfejsy API wyszukiwania podczas uczenia maszynowego lub podobnym działaniu algorytmicznego uczenie, ocenę lub zwiększenia nowej lub istniejącej usługi, które możesz lub stron trzecich mogą oferować.
- Zmodyfikuj zawartość wyników (inne niż je sformatować w taki sposób, który narusza inne wymagania), chyba że jest to wymagane przez prawo lub ustaleniami firmy Microsoft. 
- Pomiń autorstwa i adres URL skojarzone z zawartością wynik;
- Zmień kolejność, m.in. przez pominięcie, wyniki wyświetlane w odpowiedzi, gdy została podana kolejności lub klasyfikacji (na interfejsie API wyszukiwania usługi Bing niestandardowe ta reguła dotyczy zmiana kolejności jest implementowane za pośrednictwem portalu customsearch.ai), chyba że jest to wymagane przez prawo lub uzgodnione przez firmę Microsoft ;
- Wyświetlanie innej zawartości w ramach dowolnej części odpowiedzi w taki sposób, który może spowodować użytkownikowi końcowemu podejrzeń, że innej zawartości jest częścią odpowiedzi; 
- wyświetlania reklam, który nie został dostarczony przez firmę Microsoft na każdej stronie wyświetlany dowolną część odpowiedzi; -wyświetlić anonsowaniu odpowiedzi (i) z usługi Bing obrazu, wiadomości lub interfejsy API wyszukiwania wideo; lub (ii) są filtrowane albo głównie (lub wyłącznie) do wyników obrazów, wiadomości i wideo.

**C. Uwagi i znakowania.** 

- Wyraźnie obejmują funkcjonalności hiperłącza do poufności informacji firmy Microsoft, dostępne pod adresem https://go.microsoft.com/fwlink/?LinkId=521839, prawie każdego punktu w środowisku użytkownika (UX), który umożliwia użytkownikowi wejściowych zapytania wyszukiwania. Etykieta hyperlink "Poufności informacji firmy Microsoft".
- Widocznym miejscu wyświetlić Bing znakowania, zgodne z wytycznymi dostępne pod adresem https://go.microsoft.com/fwlink/?linkid=833278, prawie każdego punktu w UX, który umożliwia użytkownikowi wejściowych zapytania wyszukiwania.  Takie znakowanie musi wyraźnie oznaczenia użytkownikowi czy firmy Microsoft jest włączanie obsługi wyszukiwania internet.
- Może atrybutu każdego odpowiedzi (lub część odpowiedzi) wyświetlany z Bing sieci Web, obrazów, wiadomości i wideo interfejsów API do firmy Microsoft zgodnie z opisem w https://go.microsoft.com/fwlink/?linkid=833278, chyba że Microsoft określi inaczej zapisywania do użycia. 
- Nie atrybutu odpowiedzi (lub części odpowiedzi) wyświetlany z interfejsu API wyszukiwania usługi Bing niestandardowe do firmy Microsoft, o ile nie określa firmy Microsoft, w przeciwnym razie podczas zapisywania do wykorzystania w szczególności.


**D. Przenoszenie odpowiedzi.** Po włączeniu użytkownikowi transfer odpowiedzią API wyszukiwania do innego użytkownika, takie jak za pomocą komunikatów aplikacji lub księgowanie mediów społecznościowych, zastosuj następujące: 
- Odpowiedzi przekazanych musi:
  - Składają się z zawartości, która pozostaje niezmieniona od zawartości odpowiedzi wyświetlane użytkownikowi przesyłania (zmiany formatowania są dopuszczalne);
  - Nie zawiera żadnych danych w postaci metadanych;
  - Na odpowiedzi z sieci Web usługi Bing, obraz, wiadomości i wideo interfejsów API, język wyświetlania wskazujące odpowiedź uzyskano za pomocą środowiska wyszukiwania internetowe obsługiwane przez usługę Bing (na przykład "Obsługiwane przez usługi Bing," "Dowiedz się więcej na temat tego obrazu na Bing", lub za pomocą Bing logo);
  - Odpowiedzi z interfejsu API wyszukiwania usługi Bing niestandardowe język wyświetlania wskazujące odpowiedź uzyskano za pomocą środowiska wyszukiwania internet (na przykład "Dowiedz się więcej na temat tego wyniku wyszukiwania");
  - Widocznym miejscu wyświetlić pełne zapytanie, które są używane do generowania odpowiedzi; i
  - Zawierać widoczne łącze lub podobne przypisanie do źródła odpowiedzi, bezpośrednio lub przez aparat wyszukiwania (bing.com, m.bing.com lub usługi wyszukiwania niestandardowej, jeśli ma zastosowanie).
- Nie może zautomatyzować przesyłanie odpowiedzi. Przeniesienie musi zostać zainicjowany przez akcję użytkownika wyraźnie uprawniające celem na przesyłanie odpowiedzi.
- Tylko może umożliwić użytkownikowi transfer odpowiedzi, które były wyświetlane w odpowiedzi na zapytanie przesyłania użytkownika.

**E. Ciągłości świadczenia usług.** Nie Kopiuj, przechowywania lub wszystkie dane z interfejsu API Search odpowiedzi z pamięci podręcznej. Aby włączyć ciągłości usługi dostępu do danych i renderowania danych, może jednak przechowywać wyniki wyłącznie w następujących warunkach:

**Urządzenie.** Można umożliwić użytkownikowi końcowemu zachować wyniki na urządzeniu dla starszej i 24 godzin od czasu zapytania lub (ii) do momentu użytkownik końcowy prześle inne zapytanie do zaktualizowanych wyników, pod warunkiem, że wyniki zachowanych może służyć tylko:

- Aby umożliwić użytkownikom końcowym dostęp wyników zwróconych wcześniej dla użytkownika końcowego z tego urządzenia (na przykład w przypadku przerw w obsłudze); lub
- do przechowywania wyników zwróconych w aktywnej kwerendzie dostosować ją do przewidywania potrzeb użytkownika końcowego sygnały użytkownika końcowego (na przykład w przypadku przewidywanych przerw w obsłudze).

**Serwer.** Może przechowywać wyniki specyficzne dla pojedynczego użytkownika końcowego bezpiecznie na serwerze kontroli i wyświetlić tylko wyniki zachowanych:

- Aby umożliwić użytkownikom końcowym dostęp do historycznych raport wyników wcześniej zwrócony dla tego użytkownika w rozwiązaniu, pod warunkiem, że wyniki nie mogą być i przechowywane dłużej niż 21 dni od czasu początkowej zapytania użytkownika końcowego i II wyświetlany w odpowiedzi na końcu u zapytania nowych lub powtórzony na ser; lub
- do przechowywania wyników zwróconych w aktywnej kwerendzie dostosować ją do przewidywania potrzeb użytkownika końcowego na mniejszy i 24 godzin od czasu zapytania lub (ii) do momentu użytkownik końcowy prześle inne zapytanie do zaktualizowanych wyników sygnałami danego użytkownika.

W przypadku, gdy zachowane, wyniki dla określonego użytkownika nie commingled z wynikami dla innego użytkownika, tj., muszą być przechowywane i dostępne oddzielnie wyniki każdego użytkownika.

**Ogólne.** Dla wszystkich prezentacji zachowanych wyników:

- Wyczyść, widoczne powiadomienie czasu, jaki zapytania został wysłany, obejmują
- zaoferowania użytkownikowi przycisku lub w podobny sposób ponownego wykonywania zapytań i uzyskać zaktualizowanych wyników 
- Zachowaj Bing znakowania w wyniki, a
- Usuń (i Odśwież z nowego zapytania w razie potrzeby) przechowywanych wyników w ramach harmonogramu określony.

**F. Odnajdywanie adresu URL bez wyświetlania.** Odpowiedzi wyszukiwania można używać tylko w środowisku wyszukiwaniem w Internecie z systemem innym niż wyłącznie w celu odnajdywania adresów URL źródeł informacji odbierać kwerendy od użytkownika lub klienta. Może kopiować adresów URL w raporcie lub podobne odpowiedzi, podane przez użytkownika i tylko dla tego użytkownika lub klienta, w odpowiedzi na zapytania i (ii), która zawiera znaczące dodatkowe cenne zawartość dotyczy zapytanie. Wymagania te wymagania dotyczące użycia i wyświetlania w sekcjach 3A za pośrednictwem 3E nie dotyczą to zastosowanie — do wyświetlania, z wyjątkiem: 

- Nie są pamięci podręcznej, skopiować lub przechowywania danych lub zawartości lub pochodną odpowiedzi wyszukiwania, innego niż ograniczone kopiowanie adresu URL opisanych powyżej;
- Upewnij się, że korzystanie z danych (w tym adresy URL) odebrane z interfejsy API wyszukiwania nie naruszanie obowiązujących przepisów lub praw innych firm. i
- Nie wykorzystuje (w tym adresy URL) odebrane dane z interfejsy API wyszukiwania indeksu wyszukiwania lub uczenia maszynowego lub podobnym działaniu algorytmicznego w ramach tworzenia pociągu, ocenę lub poprawy usługi, które może zaoferować możesz ani podmiotów trzecich.

## <a name="next-steps"></a>Kolejne kroki
[Omówienie wyszukiwania odpowiedzi](overview.md)
