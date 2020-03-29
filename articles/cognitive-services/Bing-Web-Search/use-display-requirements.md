---
title: Wymagania dotyczące używania i wyświetlania interfejsów API wyszukiwania Bing
titleSuffix: Azure Cognitive Services
description: Wymagania dotyczące wyświetlania wyników wyszukiwania z interfejsów API wyszukiwania Bing w aplikacjach.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 5575668f164b97142e7c4b2ddb2608c3173426a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "60499889"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Wymagania dotyczące użycia i wyświetlania interfejsu API wyszukiwania Bing

Te wymagania dotyczące używania i wyświetlania mają zastosowanie do dowolnej implementacji zawartości i skojarzonych informacji z następujących interfejsów API wyszukiwania Bing, w tym relacji, metadanych i innych sygnałów.

- Wyszukiwanie niestandardowe Bing
- Wyszukiwanie jednostek Bing
- Wyszukiwanie obrazów w usłudze Bing
- Wyszukiwanie wiadomości (Bing)
- Wyszukiwanie klipów wideo w usłudze Bing
- Wyszukiwanie wizualne Bing
- Wyszukiwanie w sieci Web (Bing)
- Sprawdzanie pisowni Bing
- Automatyczne sugerowanie Bing

## <a name="definitions"></a>Definicje


|Termin  |Opis  |
|---------|---------|
|Odpowiedź     | Kategoria wyników zwrócona w odpowiedzi. Na przykład odpowiedź z interfejsu API wyszukiwania w sieci Web Bing może zawierać odpowiedzi w kategoriach wyników stron sieci Web, obrazu, wideo, wizualizacji i wiadomości. |
|Odpowiedź     | Wszelkie odpowiedzi i skojarzone dane odebrane w odpowiedzi na pojedyncze wywołanie interfejsu API wyszukiwania. |
|Wynik    | Element informacji w odpowiedzi. Na przykład zestaw danych połączonych z pojedynczym artykułem informacyjnym jest wynikiem odpowiedzi na wiadomości. |
|Interfejsy API wyszukiwania    | zbiorczo interfejsy API wyszukiwania niestandardowego Bing, wyszukiwania jednostek, wyszukiwania obrazów, wyszukiwania wiadomości, wyszukiwania wideo, wyszukiwania wizualnego, wyszukiwania lokalnego firmy i wyszukiwania w sieci Web. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Sprawdzanie pisowni bing i ograniczenia interfejsu API automatycznego przełączania umotywowania bing

Nie:

- Kopiowanie, przechowywanie lub buforowanie danych otrzymanych z funkcji sprawdzania pisowni bing lub automatycznych zasysania analogów interfejsu API usługi Bing.
- Użyj danych otrzymywanych z sprawdzania pisowni Bing lub automatycznego przełączania interfejsów API Bing w ramach uczenia maszynowego lub podobnej aktywności algorytmicznej. Nie używaj tych danych do szkolenia, oceny lub ulepszania nowych lub istniejących usług, które użytkownik lub osoby trzecie mogą oferować.

## <a name="bing-search-apis"></a>Interfejsy API wyszukiwania Bing

> [!NOTE]
> Wymagania w tej sekcji dotyczą tylko interfejsów API wyszukiwania, które nie obejmują sprawdzania pisowni bing lub autosuggest Bing. 

### <a name="internet-search-experience-requirements"></a>Wymagania dotyczące wyszukiwania w Internecie

Wszystkie dane zwracane w odpowiedziach mogą być wykorzystywane tylko w środowiskach wyszukiwania w Internecie. Wyszukiwanie w Internecie oznacza wyświetlaną zawartość: 

- Jest istotne i reaguje na bezpośrednie zapytanie użytkownika końcowego lub inne wskazanie jego zainteresowania wyszukiwania i intencji (na przykład zapytanie wyszukiwania wskazane przez użytkownika). 

- Pomaga użytkownikom znajdować źródła danych odpowiedzi i nawigować do ich źródeł. Na przykład podając klikalne łącza z hiperłączy w odpowiedzi.

- Zawiera wiele wyników dla użytkownika do wyboru. 

- Znajdują się w miejscu docelowym, które umożliwia użytkownikom wyszukiwanie.

- Zawiera widoczną wskazówkę, że zawartość jest wynikiem wyszukiwania w Internecie. Na przykład oświadczenie, że zawartość jest "z sieci Web".

- Zawiera wszelkie inne odpowiednie środki w celu zapewnienia, że dane interfejsu API wyszukiwania Bing nie naruszają żadnych obowiązujących przepisów ani praw osób trzecich. Skonsultuj się z doradcami prawnymi, aby ustalić, jakie środki mogą być odpowiednie.

Jedynym wyjątkiem od tych wymagań dotyczących środowiska wyszukiwania w Internecie jest odnajdowanie adresów URL, zgodnie z opisem w dalszej części tego artykułu. 

### <a name="restrictions"></a>Ograniczenia

Nie:

- Kopiowanie, przechowywanie lub buforowanie dowolnych danych z odpowiedzi (z wyjątkiem przechowywania w zakresie dozwolonym przez [ciągłość usługi](#continuity-of-service). 

- Użyj danych otrzymanych z interfejsów API wyszukiwania w ramach dowolnego uczenia maszynowego lub podobne działanie algorytmiczne. Nie używaj tych danych do szkolenia, oceny lub ulepszania nowych lub istniejących usług, które użytkownik lub osoby trzecie mogą oferować.

- Modyfikuj zawartość wyników (inne niż w celu ich sformatowania w sposób, który nie narusza żadnych innych wymogów), chyba że jest to wymagane przez prawo lub na które firma Microsoft zgodzi się. 

- Pomiń informacje o atrybucji i adresy URL skojarzone z treścią wyników.

- Kolejność, w tym przez pominięcie, wyniki wyświetlane w odpowiedzi, gdy zamówienie lub ranking jest dostarczany, chyba że jest to wymagane przez prawo lub uzgodnione przez firmę Microsoft. 

    > [!NOTE]
    > To wymaganie nie ma zastosowania do ponownego ustawienia zaimplementowanego za pośrednictwem portalu dla interfejsu API wyszukiwania niestandardowego Bing.

- Wyświetlanie innej zawartości w dowolnej części odpowiedzi w sposób, który doprowadziłby użytkownika do przekonania, że inna zawartość jest częścią odpowiedzi. 

- Wyświetlanie reklam, które nie są dostarczane przez firmę Microsoft na żadnej stronie, która wyświetla dowolną część odpowiedzi. 

- Wyświetlaj reklamy na stronach z odpowiedziami:
    - Z interfejsów API obrazów Bing, wyszukiwania wiadomości, wyszukiwania wideo lub wyszukiwania wizualnego
    - Filtrowane lub ograniczone głównie (lub wyłącznie) do wyników wyszukiwania obrazów, wiadomości i/lub filmów lub efektów wizualnych.

### <a name="notices-and-branding"></a>Uwagi i branding 
Zrobić:

- W widocznym miejscu należy dołączyć hiperłącze funkcjonalne do [zasad zachowania poufności informacji firmy Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839), w pobliżu każdego punktu środowiska użytkownika (UX), który oferuje użytkownikowi możliwość wprowadzania zapytania wyszukiwania. Oznacz hiperłącze **Zasad zachowania poufności informacji firmy Microsoft**.

- Wyraźnie wyświetla znakowanie Bing, zgodne z [wytycznymi dotyczącymi używania znaków towarowych Bing,](https://go.microsoft.com/fwlink/?linkid=833278)w pobliżu każdego punktu w UX, który oferuje użytkownikowi możliwość wprowadzania zapytania wyszukiwania. Takie marki muszą wyraźnie stwierdzić użytkownikowi, że Microsoft zasila środowisko wyszukiwania w Internecie.

- Każdą odpowiedź (lub część odpowiedzi) wyświetlaną z interfejsów API wyszukiwania w sieci Web Bing, wyszukiwania obrazów, wyszukiwania wiadomości, wyszukiwania wideo i wyszukiwania wizualnego można przypisać firmie Microsoft, chyba że firma Microsoft określi inaczej na piśmie do użytku użytkownika. Jest to opisane w [Wytycznych dotyczących używania znaków towarowych Bing](https://go.microsoft.com/fwlink/?linkid=833278). 

Nie:

- Odpowiedzi atrybutów (lub części odpowiedzi) wyświetlane z interfejsu API wyszukiwania niestandardowego Bing do firmy Microsoft, chyba że firma Microsoft określa inaczej na piśmie dla danego zastosowania.

### <a name="transferring-responses"></a>Przesyłanie odpowiedzi

Jeśli użytkownik może przenieść odpowiedź z interfejsu API wyszukiwania do innego użytkownika, na przykład za pośrednictwem aplikacji do obsługi wiadomości lub publikowania w mediach społecznościowych, zastosowanie mają następujące zasady: 

- Przekazane odpowiedzi muszą:
  - Składa się z zawartości, która nie jest zmieniana z zawartości odpowiedzi wyświetlanych użytkownikowi przesyłającemu. Zmiany formatowania są dopuszczalne.
  - Nie dołączaj żadnych danych w formularzu metadanych.
  - W przypadku odpowiedzi z interfejsów API usługi Bing, Image, News, Video i Visual API można wyświetlić język wskazujący, że odpowiedź została uzyskana za pośrednictwem środowiska wyszukiwania w Internecie obsługiwanego przez usługę Bing. Na przykład możesz wyświetlić język, taki jak "Powered by Bing" lub "Dowiedz się więcej o tym obrazie w u usługi Bing", lub możesz użyć logo Bing.
  - W przypadku odpowiedzi z interfejsu API wyszukiwania niestandardowego usługi Bing język wyświetlania wskazujący, że odpowiedź została uzyskana za pośrednictwem środowiska wyszukiwania w Internecie. Możesz na przykład wyświetlić język, taki jak "Dowiedz się więcej o tym wyniku wyszukiwania".
  - W widocznym miejscu wyświetlają pełną kwerendę używaną do wygenerowania odpowiedzi.
  - Dołącz widoczny link lub podobne przypisanie do źródłowego źródła odpowiedzi, bezpośrednio lub za pośrednictwem wyszukiwarki (bing.com, m.bing.com lub niestandardowej usługi wyszukiwania, stosownie do przypadku).
- Nie można zautomatyzować przesyłania odpowiedzi. Przeniesienie musi być zainicjowane przez akcję użytkownika wyraźnie potwierdzającą zamiar przeniesienia odpowiedzi.
- Użytkownik może włączyć tylko do przesyłania odpowiedzi, które zostały wyświetlone w odpowiedzi na kwerendę użytkownika przesyłającego.

### <a name="continuity-of-service"></a>Ciągłość świadczenia usług 

Nie należy kopiować, przechowywać ani buforować żadnych danych z odpowiedzi interfejsu API wyszukiwania. Aby jednak umożliwić ciągłość dostępu do usługi i renderowania danych, wyniki mogą być przechowywane wyłącznie pod następującymi warunkami:

#### <a name="device"></a>Urządzenie

Użytkownik może umożliwić użytkownikowi zachowanie wyników na urządzeniu przez mniej niż (i) 24 godziny od momentu kwerendy lub (ii) do momentu przesłania przez użytkownika kolejnego zapytania w celu uzyskania zaktualizowanych wyników, pod warunkiem że zachowane wyniki mogą być używane tylko:

- Aby umożliwić użytkownikowi dostęp do wyników wcześniej zwróconych do tego użytkownika na tym urządzeniu (na przykład w przypadku przerwy w świadczeniu usług).
- Aby przechowywać wyniki zwrócone dla proaktywnego zapytania spersonalizowane w oczekiwaniu na potrzeby użytkownika, na podstawie sygnałów tego użytkownika (na przykład w przypadku przewidywanej przerwy w świadczeniu usług).

#### <a name="server"></a>Serwer

Wyniki specyficzne dla pojedynczego użytkownika mogą być bezpiecznie przechowywane na kontrolowanym serwerze i wyświetlać tylko zachowane wyniki:

- Aby umożliwić użytkownikowi dostęp do raportu historycznego wyników wcześniej zwróconych do tego użytkownika w rozwiązaniu. Wyniki nie mogą być (i) przechowywane przez okres dłużej niż 21 dni od momentu początkowego zapytania użytkownika końcowego oraz (ii) wyświetlane w odpowiedzi na nowe lub powtarzające się zapytanie użytkownika.
- Aby przechowywać wyniki zwrócone dla proaktywnego zapytania spersonalizowane w oczekiwaniu na potrzeby użytkownika, na podstawie sygnałów tego użytkownika. Wyniki te można przechowywać przez mniej niż (i) 24 godziny od momentu kwerendy lub (ii) do momentu przesłania przez użytkownika innej kwerendy w celu uzyskania zaktualizowanych wyników.

Za każdym razem, gdy są zachowywane, wyniki dla określonego użytkownika nie mogą być mieszane z wynikami dla innego użytkownika. Oznacza to, że wyniki każdego użytkownika muszą być zachowane i dostarczone oddzielnie.

### <a name="general"></a>Ogólne 

Dla wszystkich prezentacji zachowanych wyników:

- Dołącz wyraźne, widoczne powiadomienie o czasie wysłania kwerendy.
- Przedstaw użytkownikowi przycisk lub podobne środki do ponownego zapytania i uzyskania zaktualizowanych wyników. 
- Zachowaj znakowanie Bing w prezentacji wyników.
- Usuń (i odśwież z nową kwerendą w razie potrzeby) zapisane wyniki w określonych ramach czasowych.

### <a name="non-display-url-discovery"></a>Odnajdowanie niechwerysji adresów URL 

Odpowiedzi wyszukiwania można używać wyłącznie w przypadku wyszukiwania w internecie wyłącznie w celu wykrywania adresów URL źródeł informacji reagujących na zapytanie od użytkownika lub klienta. Możesz skopiować takie adresy URL w raporcie lub podobnej odpowiedzi, którą podajesz:

- Tylko do tego użytkownika lub klienta, w odpowiedzi na to zapytanie.
- Tylko wtedy, gdy zawiera znaczne dodatkowe cenne treści, istotne dla kwerendy.

Poprzednie sekcje interfejsów API wyszukiwania używają i wyświetlania nie mają zastosowania do tego zastosowania nieeksłaniowego, z wyjątkiem następujących: 

- Nie buforuj, nie kopiuj ani nie przechowuj żadnych danych ani treści z odpowiedzi na wyszukiwanie lub z nich pochodzi, z innych niż opisane wcześniej ograniczone kopiowanie adresów URL.
- Upewnij się, że korzystasz z danych (w tym adresów URL) otrzymanych z interfejsów API wyszukiwania, nie narusza żadnych obowiązujących przepisów prawa ani praw osób trzecich.
- Nie należy używać danych (w tym adresów URL) otrzymanych z interfejsów API wyszukiwania w ramach indeksu wyszukiwania lub uczenia maszynowego lub podobnej aktywności algorytmicznej. Nie używaj tych danych do tworzenia pociągów, oceny lub ulepszania usług, które użytkownik lub osoby trzecie mogą oferować.

## <a name="gdpr-compliance"></a>Zgodność z RODO  

W odniesieniu do wszelkich danych osobowych podlegających ogólnemu rozporządzeniu o ochronie danych (RODO) Unii Europejskiej( RODO) i przetwarzanych w związku z połączeniami z interfejsami API wyszukiwania, interfejsami API sprawdzania pisowni Bing lub interfejsem API automatycznego przełączania Bing, użytkownik rozumie, że użytkownik i firma Microsoft są niezależnych administratorów danych zgodnie z RODO. Jesteś niezależnie odpowiedzialny za zgodność z RODO.  

