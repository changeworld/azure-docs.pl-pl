---
title: Dodawanie analizatorów języka do pól ciągów
titleSuffix: Azure Cognitive Search
description: Wielojęzykowa analiza tekstu leksykalnego dla zapytań innych niż angielskie i indeksów w usłudze Azure Wyszukiwanie poznawcze.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78379704"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Dodawanie analizatorów języka do pól ciągów w indeksie Wyszukiwanie poznawcze platformy Azure

*Analizator języka* jest określonym typem [analizatora tekstu](search-analyzers.md) , który wykonuje analizę leksykalną przy użyciu reguł lingwistycznych języka docelowego. Każde pole z możliwością wyszukiwania ma właściwość **analizatora** . Jeśli indeks zawiera przetłumaczone ciągi, takie jak oddzielne pola w języku angielskim i chińskim, można określić analizatory języka dla każdego pola, aby uzyskać dostęp do zaawansowanych funkcji językowych tych analizatorów.  

Platforma Azure Wyszukiwanie poznawcze obsługuje analizatory 35 objęte przez Lucene i analizatory 50 objęte umową Microsoft Natural language processing, która została zastosowana w pakiecie Office i Bing.

## <a name="comparing-analyzers"></a>Porównywanie analizatorów

Niektórzy deweloperzy mogą preferować bardziej znane, proste i łatwe w użyciu rozwiązanie typu open source. Analizatory języka Lucene są szybsze, ale analizatory firmy Microsoft mają zaawansowane możliwości, takie jak Lematyzacja, rozliczanie wyrazów (w językach, takich jak niemiecki, duński, holenderski, szwedzki, norweski, estoński, wykończenie, węgierski, słowacki) i Entity Rozpoznawanie (adresy URL, wiadomości e-mail, daty, liczby). Jeśli to możliwe, należy uruchomić porównania obu analizatorów firmy Microsoft i Lucene, aby zdecydować, która z nich jest lepsza. 

Indeksowanie za pomocą analizatorów firmy Microsoft jest średnio dwa do trzech razy wolniejsze niż ich odpowiedniki w Lucene, w zależności od języka. W przypadku zapytań o średnim rozmiarze nie powinno się znacznie wpłynąć na wydajność wyszukiwania. 

### <a name="english-analyzers"></a>Analizatory angielskie

Domyślną analizatorem jest standardowa Lucene, które dobrze sprawdzają się w języku angielskim, ale być może nie jest to również program w języku angielskim lub Microsoft English Analyzer. 
 
+ Analizator w języku angielskim Luces rozszerza analizatora standardowego. Usuwa possessives (końcowe) z wyrazów, stosuje rdzenie zgodnie z algorytmem rdzenia Porter i usuwa słowa zatrzymywania w języku angielskim.  

+ Program Microsoft English Analyzer wykonuje Lematyzacja zamiast rdzeni. Oznacza to, że może obsłużyć nietypowe i nieregularne formy wyrazów znacznie lepiej, co daje bardziej odpowiednie wyniki wyszukiwania 

## <a name="configuring-analyzers"></a>Konfigurowanie analizatorów

Analizatory języka są używane jako-is. Dla każdego pola w definicji indeksu można ustawić właściwość **analizatora** na nazwę analizatora, która określa język i stosy (Microsoft lub Lucene). Ten sam analizator zostanie zastosowany podczas indeksowania i wyszukiwania tego pola. Można na przykład mieć osobne pola w języku angielskim, francuskim i hiszpańskim, które znajdują się obok siebie w tym samym indeksie.

> [!NOTE]
> Nie można użyć innego analizatora języka w czasie indeksowania niż w czasie zapytania dla pola. Ta możliwość jest zarezerwowana dla [analizatorów niestandardowych](index-add-custom-analyzers.md). Z tego powodu, jeśli spróbujesz ustawić właściwości **searchAnalyzer** lub **indexAnalyzer** jako nazwę analizatora języka, interfejs API REST zwróci odpowiedź na błąd. Zamiast tego należy użyć właściwości **Analizator** .

Użyj parametru zapytania **searchFields** , aby określić, które pole specyficzne dla języka ma być wyszukiwane w zapytaniach. Przykłady zapytań, które obejmują Właściwość analizatora, można przejrzeć w [dokumentach wyszukiwania](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Aby uzyskać więcej informacji na temat właściwości indeksu, zobacz [Tworzenie indeksu &#40;Azure wyszukiwanie poznawcze&#41;REST API](https://docs.microsoft.com/rest/api/searchservice/create-index). Aby uzyskać więcej informacji na temat analizy w usłudze Azure Wyszukiwanie poznawcze, zobacz [analizatory na platformie azure wyszukiwanie poznawcze](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista analizatorów języka 
 Poniżej znajduje się lista obsługiwanych języków razem z nazwami Lucene i Microsoft Analyzer.  

|Język|Nazwa analizatora firmy Microsoft|Nazwa analizatora Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabski|ar.microsoft|ar. Lucene|  
|Armeński||HY. Lucene|  
|Bengalski|mld USD. Microsoft||  
|Baskijski||UE. Lucene|  
|Bułgarski|BG. Microsoft|BG. Lucene|  
|Kataloński|ca.microsoft|CA. Lucene|  
|Chiński uproszczony|zh-Hans.microsoft|zh-Hans. Lucene|  
|Chiński tradycyjny|zh-Hant.microsoft|zh-Hant. Lucene|  
|Chorwacki|hr.microsoft||  
|Czeski|cs.microsoft|cs. Lucene|  
|Duński|da.microsoft|da. Lucene|  
|Holenderski|nl.microsoft|NL. Lucene|  
|Polski|pl. Microsoft|pl. Lucene|  
|Estoński|et.microsoft||  
|Fiński|fi.microsoft|Fi. Lucene|  
|Francuski|fr.microsoft|fr. Lucene|  
|Galicyjski||GL. Lucene|  
|Niemiecki|de.microsoft|de. Lucene|  
|Grecki|el.microsoft|El. Lucene|  
|Gudżarati|gu.microsoft||  
|Hebrajski|he.microsoft||  
|Hindi|hi.microsoft|Witaj. Lucene|  
|Węgierski|hu.microsoft|HU. Lucene|  
|Islandzki|is.microsoft||  
|Indonezyjski (Bahasa)|id.microsoft|Identyfikator. Lucene|  
|Irlandzki||ga. Lucene|  
|Włoski|it.microsoft|IT. Lucene|  
|Japoński|ja. Microsoft|ja. Lucene|  
|Kannada|kn.microsoft||  
|Koreański|ko.microsoft|ko.lucene|  
|Łotewski|lv.microsoft|LV. Lucene|  
|Litewski|lt.microsoft||  
|Malajalam|ml.microsoft||  
|Malajski (łaciński)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norweski|nb.microsoft|nie. Lucene|  
|Perski||FA. Lucene|  
|Polski|pl.microsoft|pl. Lucene|  
|Portugalski (Brazylia)|pt-Br.microsoft|pt-br. Lucene|  
|Portugalski (Portugalia)|pt-Pt.microsoft|pt-Pt.lucene|  
|Pendżabski|pa.microsoft||  
|Rumuński|ro.microsoft|ro. Lucene|  
|Rosyjski|ru.microsoft|ru. Lucene|  
|Serbski (Cyrylica)|SR-cyrylica. Microsoft||  
|Serbski (łaciński)|sr-latin.microsoft||  
|Słowacki|SK. Microsoft||  
|Słoweński|sl.microsoft||  
|Hiszpański|es.microsoft|es. Lucene|  
|Szwedzki|sv.microsoft|OHR. Lucene|  
|Tamilski|ta.microsoft||  
|Telugu|te. Microsoft||  
|Tajlandzki|th. Microsoft|th. Lucene|  
|Turecki|tr.microsoft|TR. Lucene|  
|Ukraiński|uk.microsoft||  
|Urdu|ur.microsoft||  
|Wietnamski|vi.microsoft||  

 Wszystkie analizatory z nazwami oznaczonymi jako " **Lucene** " są obsługiwane przez [analizatory języka Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Zobacz też  

+ [Tworzenie indeksu &#40;interfejsu API REST w usłudze Azure wyszukiwanie poznawcze&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Analizatorname — Klasa](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

