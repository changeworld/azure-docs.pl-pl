---
title: Użyj i wyświetlają wymagania dotyczące — wyszukiwanie odpowiedzi projektu
titlesuffix: Azure Cognitive Services
description: Użyj i wyświetlają wymagania dotyczące projektu, wyszukiwanie odpowiedzi punktu końcowego.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: 085cb20e4dad92ed55b5ba0914c677aa50f3ac97
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345757"
---
# <a name="project-answer-search-use-and-display-requirements"></a>Wyszukiwanie odpowiedzi projektu użycia i wyświetlają wymagania dotyczące

Wymagania dotyczące użycia i wyświetlania dotyczą implementacji zawartość i skojarzone informacje, na przykład relacje, metadane i innymi sygnałami, dostępne za pośrednictwem wywołania wyszukiwania wiedzy Bing, wyszukiwanie niestandardowe Bing, wyszukiwanie jednostek, wyszukiwanie obrazów Wyszukiwanie wiadomości, wyszukiwanie wideo, wyszukiwanie wizualne wyszukiwanie w sieci Web, sprawdzanie pisowni i interfejsy API automatycznego sugerowania. Szczegóły implementacji związane z tych wymagań można znaleźć w dokumentacji dla określonych funkcji i wyników.

## <a name="1-bing-spell-check-and-bing-autosuggest-api"></a>1. Sprawdzanie pisowni Bing i Bing interfejs API automatycznego sugerowania.

Nie:

- kopiowanie, przechowywania i wszelkie dane, otrzymanymi od sprawdzania pisowni Bing i interfejs API automatycznego sugerowania Bing z pamięci podręcznej
- Użyj danych otrzymanymi od sprawdzania pisowni Bing i interfejs API automatycznego sugerowania Bing w ramach dowolnej usługi machine learning lub podobnym działaniu algorytmicznego, uczenie, ocenę lub ulepszenia nowych lub istniejących usług, których użytkownik lub innych firm mogą oferować.

## <a name="2-definitions"></a>2. Definicje

- "" (odpowiedź) odwołuje się do kategorii wyników zwróconych w odpowiedzi. Na przykład odpowiedź z interfejsu API wyszukiwania w sieci Web Bing może obejmować odpowiedzi w kategoriach wyniki strony sieci Web, obrazów, wideo i wiadomości;
- "response" oznacza, że wszystkie odpowiedzi i skojarzone dane odebrane w odpowiedzi na jedno wywołanie interfejsu API wyszukiwania;
- "wynik" odnosi się do elementu informacji w odpowiedzi. Na przykład zestaw danych połączonych przy użyciu pojedynczego nowości jest wynik w odpowiedzi na wiadomości.
- "Interfejsy API wyszukiwania" oznacza zbiorczo, wyszukiwanie niestandardowe Bing, wyszukiwanie jednostek, wyszukiwanie obrazów, wyszukiwania wiadomości, wyszukiwania wideo, wyszukiwanie wizualne i interfejsy API wyszukiwania w sieci Web. 


## <a name="3-search-apis"></a>3. Interfejsy API wyszukiwania

Wymagania opisane w tej sekcji 3 dotyczą interfejsy API wyszukiwania.

**A. Środowisko wyszukiwania w Internecie.** Wszystkie dane zwrócone w odpowiedzi można używać tylko w środowiska wyszukiwania w Internecie. Zawartość wyświetlana, jeśli ma to zastosowanie oznacza, że środowiska wyszukiwania w Internecie: 
- ma zastosowanie i reagują na zapytaniach bezpośrednich użytkownika końcowego lub inne wskazanie użytkownika, interesujące wyszukiwania i przeznaczenie (na przykład, zapytanie wyszukiwania wskazanych przez użytkownika); 
- ułatwia użytkownikom wyszukiwanie i przejdź do źródła danych (na przykład podane adresy URL są implementowane jako hiperłącza, więc zawartość lub autorstwa jest łączem sposób widoczny jest wyświetlana przy użyciu danych); lub, jeśli z interfejsu API wyszukiwania jednostek Bing, widoczny link do adresu URL bing.com podanego w odpowiedzi, który umożliwia użytkownikowi przejść do wyników wyszukiwania dla odpowiednich zapytań w witrynie bing.com;
- zawiera wiele wyników dla użytkownika końcowego, które można wybierać (na przykład, wyświetlanych jest kilka wyników z odpowiedzi wiadomości lub zwracane są wszystkie wyniki, jeśli jest to mniej niż kilka); 
- jest ograniczona do kwoty, które są odpowiednie w celu wyszukiwania (na przykład obraz miniatury są miniatury o rozmiarze proporcjonalnie do wyświetlania przez użytkownika); 
- obejmuje widocznego wskazania użytkownika końcowego, że zawartość jest wyniki wyszukiwania w Internecie (na przykład instrukcja czy zawartość jest "z sieci web"); i
- obejmuje inne kombinacje miary, należy upewnić się, że korzystanie z danych odebranych z interfejsów API wyszukiwania nie naruszać obowiązujących przepisów lub praw innych firm (na przykład, jeśli opierając się na licencji Creative Commons, zgodnie z obowiązującymi umowami warunki). Należy zapoznać się z, które stosowne usługi prawne doradców, aby ustalić, jakie środki mogą być.
Jedynym wyjątkiem od wymagań środowiska wyszukiwania internetowego jest adres URL odnajdywania, zgodnie z opisem w sekcji 3E (inne niż wyświetlany adres URL odnajdywania) poniżej. 

**B. Ograniczenia.** Nie:

- kopiowanie, przechowywania i pamięci podręcznej żadnych danych z odpowiedzi (z wyjątkiem przechowywania w zakresie dozwolonym przez po sekcji "Ciągłość działania usługi"); 
- uczenie, ocenę lub poprawić nowej lub istniejącej usługi, które użytkownik lub innych firm mogą oferować, należy użyć dane otrzymane z interfejsów API wyszukiwania w ramach usługi machine learning lub podobne działania w konsolidatorze.
- modyfikowanie zawartości wyników (inne niż można ponownie sformatować je w taki sposób, że nie narusza inne wymagania), chyba że jest to wymagane przepisami prawa lub ustaleniami firmy Microsoft. 
- Pominięto: uznanie autorstwa i adresy URL skojarzony z wynikiem zawartości;
- której kolejność chcesz zmienić, m.in. przez pominięcie, wyniki wyświetlane w odpowiedzi, gdy została podana zamówienia lub klasyfikacji (interfejsu API wyszukiwania niestandardowego Bing, ta reguła nie dotyczy zmiana kolejności implementowane za pomocą portalu customsearch.ai), chyba że jest to wymagane przepisami prawa lub uzgodnione przez firmę Microsoft ;
- Wyświetlanie innej zawartości w dowolnej części odpowiedzi w taki sposób, co może spowodować użytkownikowi końcowemu uważa, że inna zawartość jest częścią odpowiedzi; 
- wyświetlanie reklam, który nie został dostarczony przez firmę Microsoft na dowolnej stronie wyświetlającą dowolnej części odpowiedź; — wyświetlanie reklam z odpowiedziami (i) obraz Bing, grup dyskusyjnych lub interfejsów API wyszukiwania wideo; lub (ii), są filtrowane, lub ograniczone przede wszystkim (lub wyłącznie) do wyników obrazu, wiadomości i/lub wideo.

**C. Uwagi i znakowanie.** 

- Zawsze zawierać odsyłacz funkcjonalności do zachowania poufności informacji firmy Microsoft, dostępne pod adresem https://go.microsoft.com/fwlink/?LinkId=521839, prawie każdy punkt interfejs użytkownika (UX), który umożliwia użytkownikowi wprowadź zapytanie wyszukiwania. Etykiety hyperlink "Microsoft Privacy Statement".
- Zawsze wyświetlane Bing znakowanie, zgodnie z wytycznymi dotyczącymi dostępne pod adresem https://go.microsoft.com/fwlink/?linkid=833278, prawie każdy punkt w Interfejsie, który umożliwia użytkownikowi wprowadź zapytanie wyszukiwania.  Takie znakowania musi wyraźnie oznaczyć użytkownikowi czy Microsoft jest najważniejsza środowiska wyszukiwania w Internecie.
- Może być atrybut każdej odpowiedzi (lub część odpowiedzi) wyświetlany z sieci Web Bing, obrazów, wiadomości i interfejsów API Video do firmy Microsoft zgodnie z opisem w https://go.microsoft.com/fwlink/?linkid=833278, chyba że Microsoft określono inaczej na piśmie, do Twojego użytku. 
- Nie atrybutu odpowiedzi (lub jej części odpowiedzi), wyświetlane z interfejsu API wyszukiwania niestandardowego Bing do firmy Microsoft, chyba że określa firmy Microsoft, w przeciwnym razie w pisaniu do określonego użycia.


**D. Przenoszenie odpowiedzi.** Po włączeniu użytkownikowi transfer odpowiedzi z interfejsu API wyszukiwania na innego użytkownika, takie jak aplikację do obsługi wiadomości lub ogłaszania mediów społecznościowych, mają zastosowanie następujące wymagania: 
- Przeniesione odpowiedzi musi:
  - Składają się z zawartości, która pozostaje niezmieniona z zawartości odpowiedzi, wyświetlana użytkownikowi przesyłania (zmiany formatowania są dozwolone);
  - Nie dołącza żadnych danych w postaci metadanych;
  - Na odpowiedzi z sieci Web Bing, obrazów, wiadomości i interfejsów API Video, język wyświetlania wskazujące odpowiedź została uzyskana za pośrednictwem środowiska wyszukiwania internetowych obsługiwane przez usługę Bing (na przykład "Obsługiwane przez usługi Bing," "Dowiedz się więcej na temat tego obrazu w witrynie Bing" lub za pomocą Bing logo);
  - Na odpowiedzi z interfejsu API wyszukiwania niestandardowego Bing język wyświetlania wskazujące odpowiedź została uzyskana za pośrednictwem Internetu środowisko wyszukiwania (na przykład "Dowiedz się więcej o te wyniki wyszukiwania");
  - Zawsze wyświetlić pełne zapytanie, które są używane do generowania odpowiedzi; i
  - Zawiera widoczne łącze lub podobne autorstwa do bazowego źródła odpowiedzi, bezpośrednio lub za pomocą aparatu wyszukiwania (bing.com, m.bing.com lub usługi wyszukiwania niestandardowego, jeśli ma to zastosowanie).
- Nie może zautomatyzować przesyłanie odpowiedzi. Przeniesienie musi zostać zainicjowany przez akcję użytkownika wyraźnie uprawniające intencji na przesyłanie odpowiedzi.
- Tylko mogą umożliwiać użytkownikowi przesyłanie odpowiedzi, które były wyświetlane w odpowiedzi na zapytanie przesyłania użytkownika.

**E. Ciągłość działania usługi.** Nie Kopiuj, przechowywania lub wszystkie dane z odpowiedzi interfejsu API wyszukiwania w pamięci podręcznej. Jednak aby włączyć ciągłość dostępu do usług i danych renderowania, może zachować wyniki wyłącznie w następujących warunkach:

**Urządzenie.** Mogą umożliwiać użytkownikowi końcowemu Zachowaj wyniki na urządzenie mniejsze (i) 24 godzin od momentu zapytania lub (ii) do momentu użytkownik końcowy prześle inne zapytanie do zaktualizowanych wyników, pod warunkiem, że zachowanej wyniki, które mogą być używane tylko:

- Aby umożliwić użytkownikowi dostęp do wyników wcześniej zwrócony do tego użytkownika końcowego z tego urządzenia (na przykład w przypadku przerw w działaniu usługi); lub
- do przechowywania wyników zwróconych aktywne zapytania spersonalizowane oczekując na potrzeby użytkownika końcowego oparciu o sygnały przez użytkownika końcowego (na przykład w przypadku przewidywanego zakłóceń).

**Serwer.** Może zachować wyniki specyficzne dla jednego użytkownika końcowego bezpiecznie na serwerze kontroli i wyświetlić tylko wyniki zachowanej:

- Aby umożliwić użytkownikowi dostęp do historycznych raportu wyników wcześniej zwrócony do tego użytkownika w rozwiązaniu, pod warunkiem, że wyniki nie mogą być (i) przechowywane dłużej niż 21 dni od momentu początkowego zapytania użytkownika końcowego oraz (ii) wyświetlany w odpowiedzi na koniec u nowe lub powtarzające zapytania przez użytkownika; lub
- do przechowywania wyników zwróconych aktywne zapytania spersonalizowane oczekując na potrzeby użytkownika końcowego w oparciu o mniejszej (i) 24 godzin od momentu zapytania lub (ii) do momentu użytkownik końcowy prześle inne zapytanie do zaktualizowanych wyników sygnałami przez użytkownika końcowego.

W przypadku, gdy zachowane, wyniki dla określonego użytkownika nie commingled z wynikami dla innego użytkownika, tj, wyniki każdy użytkownik musi być przechowywane i dostępne oddzielnie.

**Ogólne.** Aby obejrzeć prezentację wszystkie zachowane wyników:

- jest to zrozumiałe i widoczne powiadomienie czasu, przez który została wysłana kwerenda, obejmują
- przedstawić użytkownikowi przycisku lub podobne oznacza, że to ponowne odpytanie do uzyskania zaktualizowanych wyników 
- Zachowaj Bing znakowania w prezentacji wyników, a
- Usuwanie (i odświeżanie za pomocą nowego zapytania, jeśli to konieczne) przechowywane wyniki w obrębie harmonogramów określono.

**F. Odnajdywanie adresu URL bez wyświetlania.** Można używać tylko odpowiedzi wyszukiwania w ramach środowiska wyszukiwania w Internecie bez wyłącznie w celu odnajdywania adresów URL źródeł informacji reagują na kwerendy od użytkownika lub klienta. Może kopiować adresów URL w raporcie lub podobne odpowiedzi, których udzielasz (i) tylko dla tego użytkownika lub klienta, w odpowiedzi na zapytanie oraz (ii), która obejmuje znaczące dodatkowe przydatne zawartość istotne dla kwerendy. Wymagania wymagania w zakresie użycia i wyświetlania w sekcjach 3A za pośrednictwem 3E nie dotyczą to wykorzystania bez wyświetlania z wyjątkiem: 

- Nie są w pamięci podręcznej, skopiować lub przechowywania danych lub zawartości lub pochodną odpowiedzi wyszukiwania w innych niż ograniczone kopiowanie adresu URL opisane wcześniej;
- Upewnij się, że korzystanie z danych (w tym adresy URL) odebrane z interfejsów API wyszukiwania nie naruszać obowiązujących przepisów lub innych praw; i
- Do tworzenia szkolenie, oceny lub ulepszenia usług, których użytkownik lub innych firm mogą oferować nie wykorzystuje (w tym adresy URL) odebrane dane za pomocą interfejsów API wyszukiwania w ramach indeksu wyszukiwania lub uczenia maszynowego lub podobne działania w konsolidatorze.

## <a name="next-steps"></a>Kolejne kroki
[Odpowiedzi wyszukiwania w — omówienie](overview.md)
