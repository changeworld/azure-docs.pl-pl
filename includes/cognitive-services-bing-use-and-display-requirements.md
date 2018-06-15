---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: MikeDodaro
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 04/19/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 174af83686eba665a729246be7a477b9a5054f30
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356076"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Interfejs API wyszukiwania usługi Bing użycia i wyświetlić wymagania

Wymagania dotyczące użycia i wyświetlania dotyczą implementacji zawartości i skojarzonych informacji. Na przykład relacje, metadane i innymi sygnałami dotyczą wymagania. Mogą to być dostępne za pośrednictwem wywołania API:

- Wyszukiwanie niestandardowe Bing
- Wyszukiwanie jednostek Bing
- Wyszukiwanie obrazów w usłudze Bing
- Wyszukiwanie wiadomości Bing
- Wyszukiwanie klipów wideo w usłudze Bing
- Wyszukiwanie wizualne Bing
- Wyszukiwanie w sieci Web (Bing)
- Sprawdzanie pisowni Bing
- Automatyczne sugerowanie Bing

Można znaleźć szczegóły implementacji związane z tych wymagań, w dokumentacji dotyczącej określonych funkcji i wyników.     

## <a name="bing-spell-check-and-bing-autosuggest-apis"></a>Interfejsy API automatycznego sugerowania sprawdzania pisowni usługi Bing i Bing

Nie:

- Kopiowanie, przechowywania i pamięci podręcznej danych otrzymywanych z sprawdzania pisowni usługi Bing lub interfejsy API automatycznego sugerowania usługi Bing.
- Użyj danych otrzymywanych z sprawdzania pisowni usługi Bing lub interfejsy API automatycznego sugerowania usługi Bing w ramach usługi machine learning lub podobnym działaniu algorytmicznego. Nie należy używać tych danych do uczenia, ocenę lub zwiększyć nowej lub istniejącej usługi, które można lub stron trzecich mogą oferować.

## <a name="definitions"></a>Definicje

- *Odpowiedzi* odwołuje się do kategorii wyników zwróconych w odpowiedzi. Na przykład w kategorii wyników strony sieci Web, obrazów, wideo, visual i wiadomości odpowiedzi można uwzględnić odpowiedzi z interfejsu API wyszukiwania usługi Bing sieci Web.   
- *Odpowiedź* oznacza, że wszystkie odpowiedzi i skojarzone dane otrzymane w odpowiedzi na wywołanie API wyszukiwania.
- *Wynik* odwołuje się do elementu informacji w odpowiedzi. Na przykład zestaw danych związanych z jednym artykuł jest wyniku w odpowiedzi na wiadomości.
- *Interfejsy API wyszukiwania* oznacza zbiorczo wyszukiwania usługi Bing niestandardowe, jednostek wyszukiwania, wyszukiwania obrazu, wyszukiwania wiadomości, wyszukiwania wideo, Visual wyszukiwania i interfejsów API sieci Web wyszukiwania. 


## <a name="search-apis"></a>interfejsy API wyszukiwania

Wymagania w tej sekcji dotyczą interfejsy API wyszukiwania. Interfejsy API wyszukiwania nie zawierają sprawdzania pisowni usługi Bing lub automatycznego sugerowania usługi Bing. Wymagania dotyczące tych dwóch interfejsów API zostały omówione w poprzedniej sekcji.

### <a name="internet-search-experience"></a>Internet wyników wyszukiwania

Wszystkie dane zwrócone w odpowiedzi można używać tylko w ramach wyszukiwania internet. Środowisko wyszukiwania internet oznacza zawartość wyświetlane, gdy ma to zastosowanie: 
- Ma zastosowanie i odbierać zapytania bezpośredniego użytkownika końcowego lub inne wskazanie użytkownika wyszukiwania odsetek i opcje (na przykład zapytania wyszukiwania wskazanych przez użytkownika). 
- Umożliwia użytkownikom znajdowanie i przejdź do źródła danych (na przykład podana adresy URL są zaimplementowane jako hiperłącza, więc autorstwa lub zawartości jest łączem sposób widoczny wyświetlane przy użyciu danych). Lub, jeśli przy użyciu interfejsu API Bing encji wyszukiwania, widoczny łącze do adresu URL bing.com podanej w odpowiedzi, który umożliwia użytkownikowi nawigację do wyników wyszukiwania dla odpowiednich zapytania na bing.com.
- Zawiera wiele wyników dla użytkownika można wybierać (na przykład, wyświetlanych jest kilka wyników z wiadomości odpowiedzi lub zwracane są wszystkie wyniki, jeśli jest to mniej niż kilka). 
- Jest ograniczona do wysokości odpowiednie w celu wyszukiwania (na przykład obraz miniatury są miniatury o rozmiarze proporcjonalnie do wyświetlania przez użytkownika). 
- Zawiera oznaczenie widoczny dla użytkownika, czy zawartość jest wyniki wyszukiwania w Internecie (na przykład instrukcja czy zawartość jest "z sieci web").
- Zawiera dowolną kombinację środków należy upewnić się, że korzystanie z danych otrzymywanych z interfejsy API wyszukiwania nie naruszanie obowiązujących przepisów lub praw innych firm. Na przykład jeśli są zależne licencją Creative Commons, zachować zgodność z warunkami obowiązującej licencji. Zapoznaj się przez użytkownika prawne doradcy do określenia, jakie środki mogą być odpowiednie.
Jedynym wyjątkiem od wymagań środowiska wyszukiwania internet jest adres URL odnajdywania, zgodnie z opisem w dalszej części tego artykułu. 

### <a name="restrictions"></a>Ograniczenia

Nie:

- Kopiowanie, przechowywania i pamięci podręcznej danych z odpowiedzi (z wyjątkiem przechowywania w zakresie dozwolonym przez sekcję "Ciągłości usługi" w dalszej części tego artykułu). 
- Użyj danych otrzymywanych z interfejsy API wyszukiwania podczas uczenia maszynowego lub podobnym działaniu algorytmicznego. Nie należy używać tych danych do uczenia, ocenę lub zwiększyć nowej lub istniejącej usługi, które można lub stron trzecich mogą oferować.
- Zmiany zawartości wyników (inne niż je sformatować w taki sposób, który narusza inne wymagania), chyba że jest to wymagane przez prawo lub Akceptuję przez firmę Microsoft. 
- Pomiń autorstwa i adres URL skojarzone z wyniku zawartości.
- Zmień kolejność, m.in. przez pominięcie, wyniki wyświetlane w odpowiedzi, gdy kolejności klasyfikacji pod warunkiem, o ile nie jest wymagane przez prawo lub Akceptuję przez firmę Microsoft. (Na interfejsie API wyszukiwania usługi Bing niestandardowe tej reguły nie dotyczą zmiana kolejności jest implementowane za pośrednictwem portalu customsearch.ai.)
- Wyświetlanie innej zawartości w ramach dowolnej części odpowiedzi w taki sposób, który doprowadzi użytkownika do podejrzeń, że innej zawartości jest częścią odpowiedzi. 
- Wyświetlać reklamy, który nie został dostarczony przez firmę Microsoft na każdej stronie wyświetlany dowolną część odpowiedzi. 
- Wyświetl anonsowaniu odpowiedzi (i) z obrazu Bing, wyszukiwanie grup dyskusyjnych, wyszukiwania wideo lub Visual interfejsy API wyszukiwania; lub (ii) są filtrowane albo głównie (lub wyłącznie) do obrazów, wiadomości i/lub wideo lub efekty wizualne.

### <a name="notices-and-branding"></a>Uwagi i znakowanie 

- Wyraźnie obejmują funkcjonalności hiperłącza do [zasady zachowania poufności informacji firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), prawie każdego punktu w środowisku użytkownika (UX), który umożliwia użytkownikowi wejściowych zapytania wyszukiwania. Etykieta hiperłącze **zasady zachowania poufności informacji firmy Microsoft**.
- Wyraźnie wyświetlanie znakowania, zgodne z usługi Bing [wytyczne korzystania z usługi Bing znak towarowy](https://go.microsoft.com/fwlink/?linkid=833278), prawie każdego punktu w UX, który umożliwia użytkownikowi wejściowych zapytania wyszukiwania. Takie znakowanie musi wyraźnie oznaczenia użytkownikowi czy firmy Microsoft jest włączanie obsługi wyszukiwania internet.
- Można atrybutu każdego odpowiedzi (lub część odpowiedzi) wyświetlane z wyszukiwania usługi Bing w sieci Web, wyszukiwanie obrazu, wyszukiwania wiadomości, wyszukiwania wideo i interfejsy API wyszukiwania Visual do firmy Microsoft, chyba że firmy Microsoft w przeciwnym razie określa się podczas zapisywania do użycia. Jest to opisane w [wytyczne korzystania z usługi Bing znak towarowy](https://go.microsoft.com/fwlink/?linkid=833278). 
- Nie atrybutu odpowiedzi (lub części odpowiedzi) wyświetlany z interfejsu API wyszukiwania usługi Bing niestandardowe do firmy Microsoft, o ile nie określa firmy Microsoft, w przeciwnym razie podczas zapisywania do wykorzystania w szczególności.

### <a name="transferring-responses"></a>Transferowanie odpowiedzi

Po włączeniu użytkownikowi transfer odpowiedzią API wyszukiwania do innego użytkownika, takie jak za pomocą komunikatów aplikacji lub księgowanie mediów społecznościowych, zastosuj następujące: 
- Odpowiedzi przekazanych musi:
  - Składają się z zawartości, która pozostaje niezmieniona od zawartości odpowiedzi wyświetlane użytkownikowi przesyłania. Dopuszczalne są zmiany formatowania.
  - Nie zawiera żadnych danych w postaci metadanych.
  - Na odpowiedzi z sieci Web usługi Bing, obraz, wiadomości, wideo i interfejsów API Visual język wyświetlania wskazujące odpowiedź uzyskano za pomocą środowiska wyszukiwania internetowe obsługiwane przez usługę Bing. Na przykład można wyświetlać języka, takie jak "Obsługiwane przez Bing" lub "Informacje" więcej informacji na temat tego obrazu na Bing lub można użyć Bing logo.
  - Odpowiedzi z interfejsu API wyszukiwania usługi Bing niestandardowe język wyświetlania wskazujące odpowiedź uzyskano za pośrednictwem Internetu wyników wyszukiwania. Na przykład można wyświetlić języka, takie jak "Dowiedz się więcej o tym wyników wyszukiwania."
  - Wyświetlanie wyraźnie pełne zapytanie, które są używane do generowania odpowiedzi.
  - Zawierać widoczne łącze lub podobne przypisanie do źródła odpowiedzi, bezpośrednio lub przez aparat wyszukiwania (bing.com, m.bing.com lub usługi wyszukiwania niestandardowej, jeśli ma zastosowanie).
- Nie może zautomatyzować przesyłanie odpowiedzi. Przeniesienie musi zostać zainicjowany przez akcję użytkownika wyraźnie uprawniające celem na przesyłanie odpowiedzi.
- Tylko może umożliwić użytkownikowi transfer odpowiedzi, które były wyświetlane w odpowiedzi na zapytanie przesyłania użytkownika.

### <a name="continuity-of-service"></a>Ciągłości świadczenia usług 

Nie Kopiuj, przechowywania lub wszystkie dane z interfejsu API Search odpowiedzi z pamięci podręcznej. Aby włączyć ciągłości usługi dostępu do danych i renderowania danych, może jednak przechowywać wyniki wyłącznie w następujących warunkach:

**urządzenie.** Można również użytkownika, aby zachować wyniki na urządzeniu dla starszej i 24 godzin od czasu zapytania lub (ii) do momentu użytkownik prześle inne zapytanie do zaktualizowanych wyników, pod warunkiem, że wyniki zachowanych może służyć tylko:

- Aby umożliwić użytkownikowi dostęp do wyników zwróconych wcześniej dla tego użytkownika z tego urządzenia (na przykład w przypadku przerw w obsłudze).
- Do przechowywania wyników zwróconych w aktywnej kwerendzie dostosować ją do przewidywania potrzeb użytkownika na sygnały tego użytkownika (na przykład w przypadku przewidywanych przerw w obsłudze).

**Serwer.** Możesz zachować wyniki specyficzne dla pojedynczego użytkownika bezpiecznie na serwerze, który kontrolujesz i wyświetlić tylko wyniki zachowanych:

- Aby umożliwić użytkownikowi dostęp do historycznych raportu dotyczącego wyników zwróconych wcześniej dla tego użytkownika w rozwiązaniu. Wyniki nie mogą być i przechowywane przez więcej niż 21 dni od czasu początkowej zapytania użytkownika końcowego i II wyświetlany w odpowiedzi na zapytania o nowych lub powtórzony użytkownika.
- Do przechowywania wyników zwróconych w aktywnej kwerendzie dostosować ją do przewidywania potrzeb użytkownika na sygnały tego użytkownika. Można przechowywać te wyniki dla starszej i 24 godzin od czasu zapytania lub (ii) do momentu użytkownik prześle inne zapytanie do zaktualizowanych wyników.

Zawsze, gdy zachowane, wyniki dla określonego użytkownika nie commingled z wynikami dla innego użytkownika. Oznacza to, że wyniki każdego użytkownika należy przechowywane i dostępne oddzielnie.

### <a name="general"></a>Ogólne 

Dla wszystkich prezentacji zachowanych wyników:

- To powiadomienie zrozumiałe i widoczne czasu wysłania zapytania.
- Występuje, oznacza użytkownika z przyciskiem lub podobny do ponownego wykonywania zapytań i uzyskać zaktualizowane wyniki. 
- Zachowaj Bing znakowania w prezentacji wyników.
- Usuń (i Odśwież z nowego zapytania w razie potrzeby) przechowywanych wyników w ramach harmonogramu określony.

### <a name="non-display-url-discovery"></a>Odnajdywanie adresu URL bez wyświetlania 

Odpowiedzi wyszukiwania można używać tylko w środowisku wyszukiwaniem w Internecie z systemem innym niż wyłącznie w celu odnajdywania adresów URL źródeł informacji odbierać kwerendy od użytkownika lub klienta. W raporcie lub podobne odpowiedzi, podane przez użytkownika, może kopiować adresów URL:

- Tylko do tego użytkownika lub klienta, w odpowiedzi na zapytania.
- Tylko wtedy, gdy zawiera znaczące dodatkowe cenne zawartości, istotne dla kwerendy.

Użyj w poprzednich sekcjach interfejsy API wyszukiwania i wymagania dotyczące wyświetlania nie dotyczą to zastosowanie — do wyświetlania, z wyjątkiem następujących czynności: 

- Nie buforuj, skopiować lub przechowywać żadnych danych lub zawartości, lub typ pochodzący od odpowiedzi wyszukiwania, innego niż ograniczone kopiowanie adresu URL opisanych powyżej.
- Upewnij się, że korzystanie z danych (w tym adresy URL) odebrane z interfejsy API wyszukiwania nie naruszanie obowiązujących przepisów lub praw innych firm.
- Nie należy używać (w tym adresy URL) odebrane dane z interfejsy API wyszukiwania w ramach wyszukiwania indeksu lub machine learning lub podobnym działaniu algorytmicznego. Nie należy używać tych danych utworzyć pociągu, ocenę lub poprawy usługi, które może zaoferować możesz ani podmiotów trzecich.

## <a name="gdpr-compliance"></a>GDPR zgodności  

Względem wszystkie dane osobowe, mogą ulec Unii Europejskiej ogólnego rozporządzenia ochrony danych (GDPR) oraz że jest przetwarzana w związku z wywołania interfejsy API wyszukiwania, API sprawdzania pisowni usługi Bing lub API automatycznego sugerowania usługi Bing należy zrozumieć, są Tobą a firmą Microsoft kontrolery niezależnych danych w obszarze GDPR. Jesteś niezależnie odpowiedzialny za przestrzegania GDPR.  

