---
title: Dodawanie analizatorów języka do pól ciągów
titleSuffix: Azure Cognitive Search
description: Wielojęzyczna leksykalne analizy tekstu dla kwerend i indeksów innych niż angielski w usłudze Azure Cognitive Search.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/10/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: a97bee27b74aa211b4d4d56547726555edefa87a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283149"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Dodawanie analizatorów języka do pól ciągów w indeksie usługi Azure Cognitive Search

*Analizator języka* jest określony typ [analizatora tekstu,](search-analyzers.md) który wykonuje analizę leksykalne przy użyciu reguł językowych języka docelowego. Każde pole z wyszukujem ma właściwość **analizatora.** Jeśli indeks zawiera przetłumaczone ciągi, takie jak oddzielne pola dla tekstu w języku angielskim i chińskim, można określić analizatory języka w każdym polu, aby uzyskać dostęp do bogatych możliwości językowych tych analizatorów.  

Usługa Azure Cognitive Search obsługuje 35 analizatorów wspieranych przez lucene i 50 analizatorów wspieranych przez zastrzeżoną technologię przetwarzania języka naturalnego firmy Microsoft używaną w pakiecie Office i usłudze Bing.

## <a name="comparing-analyzers"></a>Porównywanie analizatorów

Niektórzy deweloperzy mogą preferować bardziej znane, proste, otwarte rozwiązanie Lucene. Analizatory języka Lucene są szybsze, ale analizatory firmy Microsoft mają zaawansowane możliwości, takie jak lemmatyzacja, dekompilowanie wyrazów (w językach takich jak niemiecki, duński, holenderski, szwedzki, norweski, estoński, wykończenie, węgierski, słowacki) i podmiot (adresy URL, e-maile, daty, liczby). Jeśli to możliwe, należy uruchomić porównania analizatorów firmy Microsoft i Lucene, aby zdecydować, który z nich jest lepiej dopasowany. 

Indeksowanie za pomocą analizatorów firmy Microsoft jest średnio dwa do trzech razy wolniejsze niż ich odpowiedniki Lucene, w zależności od języka. Nie należy znacząco wpływać na wydajność wyszukiwania w przypadku zapytań o średnim rozmiarze. 

### <a name="english-analyzers"></a>Analizatory języka angielskiego

Domyślnym analizatorem jest Standardowy Lucene, który działa dobrze w języku angielskim, ale być może nie tak dobrze, jak angielski analizator Lucene lub analizator języka angielskiego firmy Microsoft. 
 
+ Analizator angielski Lucene rozszerza standardowy analizator. Usuwa zaborcze (końcowe s) ze słów, stosuje wynikające z algorytmu Portera i usuwa angielskie słowa stop.  

+ Analizator angielski firmy Microsoft wykonuje lemmatyzację zamiast liczącą. Oznacza to, że może obsługiwać odmieniane i nieregularne formy słów znacznie lepiej, co skutkuje bardziej trafnymi wynikami wyszukiwania 

## <a name="configuring-analyzers"></a>Konfigurowanie analizatorów

Analizatory języka są używane jako — jest. Dla każdego pola w definicji indeksu można ustawić właściwości **analizatora** na nazwę analizatora, która określa stos języka i lingwistyki (Microsoft lub Lucene). Ten sam analizator zostanie zastosowany podczas indeksowania i wyszukiwania tego pola. Na przykład w tym samym indeksie można mieć oddzielne pola dla opisów hoteli w języku angielskim, francuskim i hiszpańskim, które istnieją obok siebie.

> [!NOTE]
> Nie jest możliwe użycie innego analizatora języka w czasie indeksowania niż w czasie kwerendy dla pola. Ta możliwość jest zarezerwowana dla [analizatorów niestandardowych](index-add-custom-analyzers.md). Z tego powodu, jeśli spróbujesz ustawić **searchAnalyzer** lub **indexAnalyzer** właściwości do nazwy analizatora języka, interfejs API REST zwróci odpowiedź błędu. Zamiast tego należy użyć właściwości **analizatora.**

Użyj parametru zapytania **searchFields,** aby określić, które pole specyficzne dla języka ma być wyszukiwane w kwerendach. Można przejrzeć przykłady kwerend, które zawierają właściwość analizatora w [dokumentach wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Aby uzyskać więcej informacji na temat właściwości indeksu, zobacz [Tworzenie &#40;interfejsu API rest usługi Azure Cognitive Search&#41;. ](https://docs.microsoft.com/rest/api/searchservice/create-index) Aby uzyskać więcej informacji na temat analizy w usłudze Azure Cognitive Search, zobacz [Analizatory w usłudze Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista analizatorów języka 
 Poniżej znajduje się lista obsługiwanych języków wraz z nazwy Lucene i Microsoft analizatora.  

|Język|Nazwa analizatora firmy Microsoft|Lucene Analyzer Nazwa|  
|--------------|-----------------------------|--------------------------|  
|Arabski|ar.microsoft|ar.lucene|  
|Armeński||hy.lucene|  
|Bangla|bn.microsoft||  
|Baskijski||eu.lucene|  
|Bułgarski|bg.microsoft|bg.lucene|  
|Kataloński|ca.microsoft|ca.lucene|  
|Chiński (uproszczony)|zh-Hans.microsoft|zh-Hans.lucene|  
|Chiński (tradycyjny)|zh-Hant.microsoft|zh-Hant.lucene|  
|Chorwacki|hr.microsoft||  
|Czeski|cs.microsoft|cs.lucene|  
|duński|da.microsoft|da.lucene|  
|Niderlandzki|nl.microsoft|nl.lucene|  
|Polski|pl.microsoft|pl.lucene|  
|Estoński|et.microsoft||  
|fiński|fi.microsoft|fi.lucene|  
|Francuski|fr.microsoft|fr.lucene|  
|Galicyjski||gl.lucene|  
|Niemiecki|de.microsoft|de.lucene|  
|grecki|el.microsoft|el.lucene|  
|Gudżarati|gu.microsoft||  
|Hebrajski|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|węgierski|hu.microsoft|hu.lucene|  
|Islandzki|is.microsoft||  
|Indonezyjski (Bahasa)|id.microsoft|id.lucene|  
|Irlandzki||ga.lucene|  
|Włoski|it.microsoft|to.lucene|  
|Japoński|ja.microsoft|ja.lucene|  
|Kannada|kn.microsoft||  
|Koreański|ko.microsoft|ko.lucene|  
|Łotewski|lv.microsoft|lv.lucene|  
|Litewski|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malajski (łaciński)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norweski|nb.microsoft|no.lucene|  
|Perski||fa.lucene|  
|Polski|pl.microsoft|pl.lucene|  
|portugalski (Brazylia)|pt-Br.microsoft|pt-Br.lucene|  
|Portugalski (Portugalia)|pt-Pt.microsoft|pt-Pt.lucene|  
|Pendżabski|pa.microsoft||  
|Rumuński|ro.microsoft|ro.lucene|  
|Rosyjski|ru.microsoft|ru.lucene|  
|Serbski (cyrylica)|sr-cyrylica.microsoft||  
|Serbski (łaciński)|sr-latin.microsoft||  
|Słowacki|sk.microsoft||  
|Słoweński|sl.microsoft||  
|Hiszpański|es.microsoft|es.lucene|  
|szwedzki|sv.microsoft|sv.lucene|  
|Tamilski|ta.microsoft||  
|Telugu|te.microsoft||  
|Tajski|th.microsoft|th.lucene|  
|Turecki|tr.microsoft|tr.lucene|  
|Ukraiński|w wielkiej Brytanii||  
|Urdu|twój.microsoft||  
|Wietnamski|vi.microsoft||  

 Wszystkie analizatory z nazwami z adnotacjami **lucene** są zasilane przez [analizatory języka Apache Lucene.](https://lucene.apache.org/core/6_6_1/core/overview-summary.html )

## <a name="see-also"></a>Zobacz też  

+ [Tworzenie &#40;interfejsu API usługi Azure Cognitive Search REST&#41;&#40;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Klasa AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

