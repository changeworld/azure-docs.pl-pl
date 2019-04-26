---
title: Użyj i wyświetlają wymagania dotyczące interfejsów API wyszukiwania Bing
titleSuffix: Azure Cognitive Services
description: Wymagania związane z wyświetlaniem wyszukiwanie wyników z interfejsów API wyszukiwania Bing w swoich aplikacjach.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60499889"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Wymagania dotyczące użycia i wyświetlania interfejsu API wyszukiwania Bing

Wymagania w zakresie użycia i wyświetlania zastosowania do implementacji zawartość i informacje skojarzone z następujące interfejsy API wyszukiwania Bing, m.in. innymi sygnałami, relacje i metadanych.

- Wyszukiwanie niestandardowe Bing
- Wyszukiwanie jednostek Bing
- Wyszukiwanie obrazów Bing
- Wyszukiwanie wiadomości Bing
- Wyszukiwanie klipów wideo w usłudze Bing
- Wyszukiwanie wizualne Bing
- Wyszukiwanie w sieci Web (Bing)
- Sprawdzanie pisowni Bing
- Automatyczne sugerowanie Bing

## <a name="definitions"></a>Definicje


|Termin  |Opis  |
|---------|---------|
|Odpowiedź     | Kategoria wyników zwróconych w odpowiedzi. Na przykład odpowiedź z interfejsu API wyszukiwania w sieci Web Bing może zawierać odpowiedzi w kategoriach wyniki strony sieci Web, obrazów, wideo, wizualizacji i wiadomości. |
|Odpowiedź     | I wszystkie odpowiedzi i skojarzone dane odebrane w odpowiedzi na jedno wywołanie interfejsu API wyszukiwania. |
|Wynik    | Element informacje w odpowiedzi. Na przykład zestaw danych połączonych przy użyciu pojedynczego nowości jest wynik w odpowiedzi na wiadomości. |
|interfejsy API wyszukiwania    | zbiorczo wyszukiwanie niestandardowe Bing, wyszukiwanie jednostek, wyszukiwanie obrazów, wyszukiwania wiadomości, wyszukiwania wideo, wyszukiwania wizualnego, lokalnego wyszukiwania biznesowych i interfejsy API wyszukiwania w sieci Web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Ograniczenia dotyczące sprawdzania pisowni Bing i interfejs API automatycznego sugerowania Bing

Nie:

- Kopiowanie, przechowywania i wszelkie dane, otrzymanymi od sprawdzania pisowni Bing i interfejs API automatycznego sugerowania Bing z pamięci podręcznej.
- Użyj danych otrzymanymi od sprawdzania pisowni Bing i interfejs API automatycznego sugerowania Bing w ramach usługi machine learning lub podobne działania w konsolidatorze. Nie należy używać tych danych do nauczenia, oceny lub poprawić nowej lub istniejącej usługi, które może zaoferować użytkownik lub osób trzecich.

## <a name="bing-search-apis"></a>Interfejsy API wyszukiwania Bing

> [!NOTE]
> Wymagania opisane w tej sekcji dotyczą tylko interfejsy API wyszukiwania, której nie obejmuje sprawdzanie pisowni Bing i automatycznego sugerowania Bing. 

### <a name="internet-search-experience-requirements"></a>Wymagania środowiska wyszukiwania Internet

Wszystkie dane zwrócone w odpowiedzi można używać tylko w środowiska wyszukiwania w Internecie. Środowisko wyszukiwania w Internecie oznacza, że wyświetlana zawartość: 

- Ma zastosowanie i reagują na zapytaniach bezpośrednich użytkownika końcowego lub inne wskazanie ich zainteresowaniami wyszukiwania i intencji (na przykład, zapytanie wyszukiwania wskazanych przez użytkownika). 

- Ułatwia użytkownikom wyszukiwanie i przejdź do źródła danych odpowiedzi. Na przykład zapewniając możesz klikać łącza z hiperłącza w odpowiedzi.

- Zawiera wiele wyników dla użytkownika, które można wybierać. 

- Czy podczas umieszczania, która umożliwia użytkownikom wyszukiwanie.

- Obejmuje widocznego wskazania, że zawartość jest wynik wyszukiwania w Internecie. Na przykład instrukcji, której zawartość "z sieci web".

- Zawiera wszelkie inne właściwe środki, aby upewnić się, że danych interfejsu API wyszukiwania Bing nie naruszać obowiązujących przepisów lub praw innych firm. Należy zapoznać się z, które stosowne usługi prawne doradców, aby ustalić, jakie środki mogą być.

Jedynym wyjątkiem od wymagań środowiska wyszukiwania internet jest adres URL odnajdywania, zgodnie z opisem w dalszej części tego artykułu. 

### <a name="restrictions"></a>Ograniczenia

Nie:

- Kopiowanie, przechowywania i wszystkie dane z odpowiedzi z pamięci podręcznej (z wyjątkiem przechowywania w zakresie dozwolonym przez [ciągłość działania usługi](#continuity-of-service). 

- Użyć dane otrzymane z interfejsów API wyszukiwania w ramach usługi machine learning lub podobne działania w konsolidatorze. Nie należy używać tych danych do nauczenia, oceny lub poprawić nowej lub istniejącej usługi, które może zaoferować użytkownik lub osób trzecich.

- Zmodyfikuj zawartość wyników (inne niż można ponownie sformatować je w taki sposób, że nie narusza inne wymagania), chyba że jest to wymagane przez prawo, lub zgodę na przez firmę Microsoft. 

- Pominięto: uznanie autorstwa informacji i adresy URL skojarzony z wynikiem zawartości.

- Zmień kolejność, m.in. przez pominięcie, wyniki wyświetlane w odpowiedzi, gdy zamówienie lub klasyfikacji jest dostarczany, chyba że jest to wymagane przez prawo, lub zgodę na przez firmę Microsoft. 

    > [!NOTE]
    > Ten wymóg nie ma zastosowania do zmiany kolejności implementowane za pośrednictwem portalu, interfejsu API wyszukiwania niestandardowego Bing.

- Wyświetlanie innej zawartości w dowolnej części odpowiedzi w sposób zapewniający doprowadzi użytkownika do przekonania, że inna zawartość jest częścią odpowiedzi. 

- Wyświetlanie reklam, który nie został dostarczony przez firmę Microsoft na dowolnym strona, wyświetlająca dowolną część odpowiedzi. 

- Wyświetl anonsowaniu na stronach z odpowiedzi:
    - Obraz Bing, wyszukiwanie wiadomości, wyszukiwania klipów wideo lub interfejsów API wyszukiwania wizualnego
    - Filtrowane lub ograniczone przede wszystkim (lub wyłącznie) do obrazu, wiadomości i/lub wyniki wyszukiwania wideo lub visual.

### <a name="notices-and-branding"></a>Uwagi i znakowanie 
Należy wykonać:

- Zawsze zawierać odsyłacz funkcjonalności do [zasady zachowania poufności informacji firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), prawie każdy punkt interfejs użytkownika (UX), który umożliwia użytkownikowi wprowadź zapytanie wyszukiwania. Etykiety, hiperłącza **zasady zachowania poufności informacji firmy Microsoft**.

- Ważnej wyświetlić Bing znakowanie, zgodne z [wytyczne dotyczące użycia znaków towarowych Bing](https://go.microsoft.com/fwlink/?linkid=833278), prawie każdy punkt w Interfejsie, który umożliwia użytkownikowi wprowadź zapytanie wyszukiwania. Takie znakowania musi wyraźnie state użytkownikowi przez firmę Microsoft jest najważniejsza środowiska wyszukiwania w Internecie.

- Można atrybutu każdej odpowiedzi (lub część odpowiedzi) wyświetlane z wyszukiwania w Internecie Bing, wyszukiwanie obrazów, wyszukiwania wiadomości, wideo i interfejsy API wyszukiwania wizualnego do firmy Microsoft, chyba że Microsoft określono inaczej na piśmie, do użytku. Jest to opisane w [wytyczne dotyczące użycia znaków towarowych Bing](https://go.microsoft.com/fwlink/?linkid=833278). 

Nie:

- Atrybut odpowiedzi (lub jej części odpowiedzi) wyświetlane z interfejsu API wyszukiwania niestandardowego Bing do firmy Microsoft, chyba że Microsoft określono inaczej na piśmie do określonego użycia.

### <a name="transferring-responses"></a>Transferowanie odpowiedzi

Po włączeniu użytkownikowi transfer odpowiedzi z interfejsu API wyszukiwania na innego użytkownika, takie jak aplikację do obsługi wiadomości lub ogłaszania mediów społecznościowych, mają zastosowanie następujące wymagania: 

- Przeniesione odpowiedzi musi:
  - Składają się z zawartości, która pozostaje niezmieniona z zawartości odpowiedzi, wyświetlana użytkownikowi przesyłania. Zmiany formatowania są dopuszczalne.
  - Nie dołącza żadnych danych w postaci metadanych.
  - Na odpowiedzi z sieci Web Bing, obrazów, wiadomości, wideo i Visual interfejsów API język wyświetlania wskazujące odpowiedź uzyskano przy użyciu środowisko wyszukiwania internetowych obsługiwane przez usługę Bing. Na przykład języka, takich jak "Obsługiwane przez usługi Bing" lub "Dowiedz się więcej" więcej informacji na temat tego obrazu można wyświetlić w witrynie Bing, lub można użyć Bing logo.
  - W przypadku odpowiedzi z interfejsu API wyszukiwania niestandardowego Bing wyświetlić języka wskazujący, że odpowiedź została uzyskana za pośrednictwem środowiska wyszukiwania w Internecie. Na przykład można wyświetlić języka, takich jak "Dowiedz się więcej o te wyniki wyszukiwania."
  - Zawsze wyświetlić pełne zapytanie, które są używane do generowania odpowiedzi.
  - Zawiera widoczne łącze lub podobne autorstwa do bazowego źródła odpowiedzi, bezpośrednio lub za pomocą aparatu wyszukiwania (bing.com, m.bing.com lub usługi wyszukiwania niestandardowego, jeśli ma to zastosowanie).
- Nie może zautomatyzować przesyłanie odpowiedzi. Przeniesienie musi zostać zainicjowany przez akcję użytkownika wyraźnie uprawniające intencji na przesyłanie odpowiedzi.
- Tylko mogą umożliwiać użytkownikowi przesyłanie odpowiedzi, które były wyświetlane w odpowiedzi na zapytanie przesyłania użytkownika.

### <a name="continuity-of-service"></a>Ciągłość działania usługi 

Nie Kopiuj, przechowywania lub wszystkie dane z odpowiedzi interfejsu API wyszukiwania w pamięci podręcznej. Jednak aby włączyć ciągłość dostępu do usług i danych renderowania, może zachować wyniki wyłącznie w następujących warunkach:

#### <a name="device"></a>Urządzenie

Mogą umożliwiać użytkownikowi Zachowaj wyniki na urządzenie mniejsze (i) 24 godzin od momentu zapytania lub (ii) do momentu użytkownik prześle inne zapytanie do zaktualizowanych wyników, pod warunkiem, że zachowanej wyniki, które mogą być używane tylko:

- Aby umożliwić użytkownikowi dostęp do wyników wcześniej zwrócony do tego użytkownika na tym urządzeniu (na przykład, jeśli przerwanie usługi).
- Do przechowywania wyników zwróconych aktywne zapytania spersonalizowane oczekując na potrzeby użytkownika, w oparciu o sygnały tego użytkownika (na przykład w przypadku przewidywanego zakłóceń).

#### <a name="server"></a>Serwer

Możesz zachować wyniki specyficzne dla jednego użytkownika bezpiecznie na serwerze, na które możesz kontrolować i wyświetlić tylko wyniki zachowanej:

- Aby umożliwić użytkownikowi dostęp do historycznych raportu wyników wcześniej zwrócony do tego użytkownika w rozwiązaniu. Wyniki nie może być (i) przechowywane przez ponad 21 dni od momentu początkowego zapytania użytkownika końcowego i (ii) wyświetlany w odpowiedzi na zapytanie użytkownika nowych lub powtarzające.
- Do przechowywania wyników zwróconych aktywne zapytania spersonalizowane oczekując na potrzeby użytkownika, w oparciu o sygnały tego użytkownika. Możesz zapisać te wyniki dla mniejszy z (i) 24 godzin od momentu zapytania lub (ii) do momentu użytkownik prześle inne zapytanie do zaktualizowanych wyników.

Zawsze, gdy zachowane, wyniki dla określonego użytkownika nie commingled z wynikami dla innego użytkownika. Oznacza to, że wyniki każdego użytkownika należy przechowywane i dostępne oddzielnie.

### <a name="general"></a>Ogólne 

Aby obejrzeć prezentację wszystkie zachowane wyników:

- Obejmują zrozumiałe i widoczne powiadomienie czasu, przez który została wysłana kwerenda.
- Obecność użytkownika za pomocą przycisku lub podobnych, oznacza to ponowne odpytanie i zaktualizować wyniki. 
- Zachowaj Bing znakowania w prezentacji wyników.
- Usuwanie (i odświeżanie za pomocą nowego zapytania, jeśli to konieczne) przechowywane wyniki w obrębie harmonogramów określono.

### <a name="non-display-url-discovery"></a>Odnajdywanie adresu URL bez wyświetlania 

Można używać tylko odpowiedzi wyszukiwania w ramach środowiska wyszukiwania w Internecie bez wyłącznie w celu odnajdywania adresów URL źródeł informacji reagują na kwerendy od użytkownika lub klienta. W raporcie lub podobne odpowiedzi, których udzielasz, może kopiować adresów URL:

- Tylko do tego użytkownika lub klienta w odpowiedzi na zapytanie.
- Tylko wtedy, gdy zawiera on znaczące dodatkowe przydatne zawartości, istotne dla kwerendy.

Użyj w poprzednich sekcjach interfejsy API wyszukiwania i wymagania dotyczące wyświetlania nie dotyczą to wykorzystania bez wyświetlania, z wyjątkiem następujących: 

- Nie w pamięci podręcznej, skopiować lub przechowywać żadnych danych lub zawartości, lub pochodzić od odpowiedzi wyszukiwania w innych niż ograniczone kopiowanie adresu URL opisanych powyżej.
- Upewnij się, że korzystanie z danych (w tym adresy URL) odebrane z interfejsów API wyszukiwania nie naruszać obowiązujących przepisów lub innych praw.
- Nie należy używać (w tym adresy URL) odebrane dane za pomocą interfejsów API wyszukiwania w ramach dowolnego wyszukiwania indeksu lub machine learning lub podobnym działaniu konsolidatorze. Nie należy używać tych danych do tworzenia train, oceny lub ulepszenia usług, które może zaoferować użytkownik lub osób trzecich.

## <a name="gdpr-compliance"></a>Zgodność z RODO  

W odniesieniu do żadnych danych osobowych Unii Europejskiej ogólne rozporządzenie o ochronie danych (RODO) i który jest przetwarzany w połączeniu z wywołania interfejsów API wyszukiwania, interfejs API sprawdzania pisowni Bing i interfejs API automatycznego sugerowania Bing z zastrzeżeniem dowiedzieć się, że klient i firma Microsoft są kontrolery niezależne dane w ramach RODO. Odpowiedzialność za niezależnie zgodność z RODO.  

