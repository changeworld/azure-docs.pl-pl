---
title: Prosta składnia zapytań
titleSuffix: Azure Cognitive Search
description: Odwołanie do składni kwerendy prostej używane dla kwerend wyszukiwania pełnotekstowego w usłudze Azure Cognitive Search.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/12/2020
ms.openlocfilehash: 066190ff6b735d30db351ff90c0b6e5173b7f583
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258868"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Składnia kwerend prostych w usłudze Azure Cognitive Search

Usługa Azure Cognitive Search implementuje dwa języki zapytań oparte na lucene: [analizator prostych zapytań](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) i [analizator zapytania lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). 

W usłudze Azure Cognitive Search składnia kwerendy prostej wyklucza operacje wyszukiwania rozmytego. Zamiast tego należy użyć pełnej składni Lucene do [wyszukiwania rozmytego](search-query-fuzzy.md).

> [!NOTE]
> Składnia kwerendy prostej jest używana dla wyrażeń kwerend przekazanych w parametrze **wyszukiwania** interfejsu API [dokumentów wyszukiwania,](https://docs.microsoft.com/rest/api/searchservice/search-documents) nie należy mylić z [składnią OData](query-odata-filter-orderby-syntax.md) używaną dla [$filter](search-filters.md) parametru tego interfejsu API. Te różne składnie mają własne reguły do konstruowania kwerend, ucieczki ciągów i tak dalej.
>
> Usługa Azure Cognitive Search udostępnia alternatywną [pełną składnię zapytania Lucene](query-lucene-syntax.md) dla bardziej złożonych zapytań w parametrze **wyszukiwania.** Aby dowiedzieć się więcej na temat architektury analizowania zapytań i zalet każdej składni, zobacz [Jak działa wyszukiwanie pełnotekstowe w usłudze Azure Cognitive Search.](search-lucene-query-architecture.md)

## <a name="invoke-simple-parsing"></a>Wywoływanie prostej analizy

Prosta składnia jest domyślna. Wywołanie jest konieczne tylko wtedy, gdy resetujesz składnię od pełnej do prostej. Aby jawnie ustawić składnię, `queryType` należy użyć parametru wyszukiwania. Prawidłowe `queryType=simple` wartości `queryType=full`obejmują `simple` lub , `full` gdzie jest ustawieniem domyślnym i wywołuje [pełną analizator zapytania Lucene](query-lucene-syntax.md) dla bardziej zaawansowanych zapytań. 

## <a name="syntax-fundamentals"></a>Podstawy składni

Każdy tekst z co najmniej jednym terminem jest uważany za prawidłowy punkt początkowy dla wykonywania kwerendy. Usługa Azure Cognitive Search będzie dopasowywanie dokumentów zawierających dowolne lub wszystkie terminy, w tym wszelkie odmiany znalezione podczas analizy tekstu.

Tak proste, jak to brzmi, istnieje jeden aspekt wykonywania zapytań w usłudze Azure Cognitive Search, które *mogą* powodować nieoczekiwane wyniki, zwiększenie, a nie zmniejsza wyniki wyszukiwania, jak więcej terminów i operatorów są dodawane do ciągu wejściowego. Czy to rozszerzenie rzeczywiście występuje zależy od włączenia NOT operatora, w połączeniu z **searchMode ustawienie parametru,** który określa, jak NIE jest interpretowany w kategoriach i lub lub zachowań. Aby uzyskać więcej informacji, zobacz [NOT operator](#not-operator).

### <a name="precedence-operators-grouping"></a>Operatory pierwszeństwa (grupowanie)

Nawiasy można używać do tworzenia podksyrii, w tym operatorów w instrukcji w nawiasie. Na przykład, `motel+(wifi||luxury)` będzie wyszukiwać dokumenty zawierające termin "motel" i "wifi" lub "luksus" (lub oba).

Grupowanie pól jest podobne, ale obejmuje grupowanie do jednego pola. Na przykład `hotelAmenities:(gym+(wifi||pool))` przeszukuje pole "hotelMenities" dla "siłownia" i "wifi", lub "siłownia" i "basen".  

### <a name="escaping-search-operators"></a>Ucieczka operatorów wyszukiwania  

Aby użyć dowolnego z operatorów wyszukiwania jako części wyszukiwanego tekstu, uniknij znaku, prefiksując go pojedynczym ukośnikiem odwrotnym (`\`). Na przykład w przypadku wyszukiwania `https://`symboli wieloznacznych w obszarze , gdzie `://` jest częścią ciągu zapytania, należy określić `search=https\:\/\/*`. Podobnie, wzór numeru telefonu uciekł może `\+1 \(800\) 642\-7676`wyglądać tak .

Znaki specjalne, które wymagają ucieczki, są następujące:`- * ? \ /`  

Aby ułatwić sprawy w bardziej typowych przypadkach, istnieją dwa wyjątki od tej reguły, w których ucieczka nie jest potrzebna:  

+ Operator `-` NOT musi być tylko wysunął się, jeśli jest to pierwszy znak po odstępach, a nie, jeśli jest w środku terminu. Na przykład następujący identyfikator GUID jest prawidłowy bez znaku ucieczki: `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`.

+ Operator `*` sufiksu musi zostać zmieniony tylko wtedy, gdy jest to ostatni znak przed odstępami, a nie jeśli znajduje się w środku terminu. Na przykład `4*4=16` nie wymaga ukośnika odwrotnego.

> [!NOTE]  
> Chociaż ucieczka utrzymuje tokeny razem, [leksykalne analizy](search-lucene-query-architecture.md#stage-2-lexical-analysis) podczas indeksowania może pozbawić je. Na przykład standardowy analizator Lucene będzie usuwać i przerywać wyrazy na łącznikach, odstępach i innych znakach. Jeśli potrzebujesz znaków specjalnych w ciągu zapytania, może być potrzebny analizator, który zachowuje je w indeksie. Niektóre opcje obejmują [analizatory języka](index-add-language-analyzers.md)naturalnego firmy Microsoft, który zachowuje wyrazy dzielenia wyrazów lub analizatora niestandardowego dla bardziej złożonych wzorców. Aby uzyskać więcej informacji, zobacz [Częściowe terminy, wzorce i znaki specjalne](search-query-partial-matching.md).

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Kodowanie niebezpiecznych i zastrzeżonych znaków w adresach URL

Upewnij się, że wszystkie niebezpieczne i zastrzeżone znaki są zakodowane w adresie URL. Na przykład "#" jest niebezpiecznym znakiem, ponieważ jest to identyfikator fragmentu/zakotwiczenia w adresie URL. Znak musi być zakodowany, `%23` jeśli jest używany w adresie URL. '&' i '=' są przykładami znaków zastrzeżonych, ponieważ rozdzielają parametry i określają wartości w usłudze Azure Cognitive Search. Zobacz [RFC1738: Uniform Resource Locators (URL), aby](https://www.ietf.org/rfc/rfc1738.txt) uzyskać więcej informacji.

Niebezpieczne znaki ``" ` < > # % { } | \ ^ ~ [ ]``to . Znaki zastrzeżone to `; / ? : @ = + &`.

###  <a name="query-size-limits"></a><a name="bkmk_querysizelimits"></a>Limity rozmiaru kwerendy

 Istnieje ograniczenie rozmiaru zapytań, które można wysłać do usługi Azure Cognitive Search. W szczególności można mieć co najwyżej 1024 klauzule (wyrażenia oddzielone AND, OR, i tak dalej). Istnieje również limit około 32 KB na rozmiar dowolnego pojedynczego terminu w kwerendzie. Jeśli aplikacja generuje zapytania wyszukiwania programowo, zaleca się projektowanie go w taki sposób, aby nie generować kwerend o rozmiarze nieograniczonym.  

## <a name="boolean-search"></a>Wyszukiwanie logiczne

Operatory logiczne (AND, OR, NOT) można osadzać w ciągu zapytania, aby utworzyć bogaty zestaw kryteriów, względem których znajdują się pasujące dokumenty. 

### <a name="and-operator-"></a>Operator I`+`

Operator AND jest znakiem plus. Na przykład, `wifi+luxury` będzie wyszukiwać `wifi` dokumenty `luxury`zawierające zarówno i .

### <a name="or-operator-"></a>Operator OR`|`

Operator OR jest pionowym znakiem pręta lub rury. Na przykład `wifi | luxury` będzie wyszukiwać dokumenty `wifi` zawierające `luxury` jedną lub obie strony.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NIE operator`-`

Operator NOT jest znakiem minus. Na przykład `wifi –luxury` wyszuka dokumenty, `wifi` które mają ten `luxury`termin i/lub nie mają .

Parametr **searchMode** w żądaniu zapytania określa, czy termin z operatorem NOT jest ANDed lub `+` ORed z innymi terminami w kwerendzie (przy założeniu, że nie ma lub `|` operatora na innych warunkach). Prawidłowe `any` wartości `all`obejmują lub .

`searchMode=any`zwiększa wycofywanie zapytań o więcej wyników, `-` a domyślnie będą interpretowane jako "LUB NIE". Na przykład `wifi -luxury` będzie pasować do dokumentów, które zawierają termin `wifi` `luxury`lub te, które nie zawierają terminu .

`searchMode=all`zwiększa precyzję zapytań, włączając mniejszą liczbę wyników, a domyślnie - będzie interpretowana jako "I NIE". Na przykład `wifi -luxury` będzie pasować do `wifi` dokumentów, które zawierają termin i nie zawierają terminu "luksus". Jest to prawdopodobnie bardziej intuicyjne zachowanie `-` dla operatora. W związku z tym `searchMode=all` należy `searchMode=any` rozważyć użycie zamiast, jeśli chcesz zoptymalizować wyszukiwania dla precyzji `-` zamiast odwołania, *a* użytkownicy często używają operatora w wyszukiwaniach.

Decydując się na **ustawienie searchMode,** należy wziąć pod uwagę wzorce interakcji użytkownika dla zapytań w różnych aplikacjach. Użytkownicy, którzy szukają informacji są bardziej prawdopodobne, aby uwzględnić operatora w kwerendzie, w przeciwieństwie do witryn e-commerce, które mają więcej wbudowanych struktur nawigacji.

<a name="prefix-search"></a>

## <a name="prefix-search"></a>Wyszukiwanie prefiksów

Operator sufiksu jest `*`gwiazdką . Na przykład `lingui*` znajdzie "językowe" lub "linguini", ignorując przypadek. 

Podobnie jak filtry, kwerenda prefiks szuka dopasowania dokładnego. W związku z tym nie ma znaczenia punktacji (wszystkie wyniki otrzymują wynik wyszukiwania 1.0). Kwerendy prefiksu może być powolny, zwłaszcza jeśli indeks jest duży, a prefiks składa się z niewielkiej liczby znaków. 

Jeśli chcesz wykonać kwerendę sufiks, pasujące w ostatniej części ciągu, użyj [wyszukiwania symboli wieloznacznych](query-lucene-syntax.md#bkmk_wildcard) i pełnej składni Lucene.

## <a name="phrase-search-"></a>Wyszukiwanie fraz`"`

Wyszukiwanie terminów to zapytanie o co najmniej jeden termin, w którym którykolwiek z terminów jest uznawany za zgodny. Wyszukiwanie fraz to dokładna fraza ujęta w cudzysłów `" "`. Na przykład `Roach Motel` podczas gdy (bez cudzysłowów) `Motel` będzie wyszukiwać `"Roach Motel"` dokumenty zawierające `Roach` i/lub w dowolnym miejscu w dowolnej kolejności (z cudzysłowami) będzie pasować tylko do dokumentów, które zawierają tę całość frazy razem i w tej kolejności (analiza tekstu nadal ma zastosowanie).

## <a name="see-also"></a>Zobacz też  

+ [Przykłady zapytań dotyczące wyszukiwania prostego](search-query-simple-examples.md)
+ [Przykłady zapytań dotyczące pełnego wyszukiwania lucene](search-query-lucene-examples.md)
+ [&#41;interfejsu API usługi Azure Cognitive Search REST &#40;dokumentów wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Składnia zapytań Lucene](query-lucene-syntax.md)
+ [Składnia wyrażenia OData](query-odata-filter-orderby-syntax.md) 
