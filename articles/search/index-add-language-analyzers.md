---
title: Dodać analizatory języka — usługa Azure Search
description: Analiza wielojęzycznej tekstu leksykalne zapytań innych niż angielski i indeksów w usłudze Azure Search.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
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
ms.openlocfilehash: 39fbe30ccf4429003dc0c9f11165c5dd057a89cd
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549156"
---
# <a name="add-language-analyzers-to-an-azure-search-index"></a>Dodać analizatory języka do indeksu usługi Azure Search

A *analizatora języków* jest określonego typu [analizatora tekstu](search-analyzers.md) wykonująca analizie leksykalnej, używając reguł językowej języka docelowego. Każde pole wyszukiwania ma **analizatora** właściwości. Jeśli indeks zawiera przetłumaczone ciągi, takie jak oddzielnymi polami dla angielskiego i chińskiego tekstu, można określić dla każdego pola, aby dostęp do zaawansowanych funkcji językowej te analizatory analizatory języka.  

Usługa Azure Search obsługuje 35 analizatory objęta Lucene, 50 analizatory i wspierane przez własności firmy Microsoft przetwarzanie języka naturalnego w technologii używanej do pakietu Office i Bing.

## <a name="comparing-analyzers"></a>Porównywanie analizatorów

Niektórzy deweloperzy preferować lepiej, proste, typu open-source rozwiązania Lucene. Analizatory języków Lucene są szybsze, ale analizatory firmy Microsoft mają zaawansowane funkcje, takie jak Lematyzacja, word decompounding (w języku, takim jak niemiecki, duński, holenderski, szwedzki, norweski, estoński, zakończenia, węgierski, słowacki) i jednostki rozpoznawanie (adresy URL, wiadomości e-mail, dat, liczb). Jeśli to możliwe należy uruchomić porównania zarówno firmy Microsoft, jak i Lucene analizatory do określania, który z nich jest lepszym rozwiązaniem. 

Indeksowanie za pomocą analizatorów firmy Microsoft znajduje się na średnią dwóch do trzech razy wolniej niż ich odpowiedniki Lucene, w zależności od języka. Wydajność wyszukiwania nie powinny znacznie wpływać dla średniego rozmiaru zapytania. 

### <a name="english-analyzers"></a>Analizatory w języku angielskim

Analizator domyślny jest Lucene Standard, co sprawdza się dobrze sprawdza się w języku angielskim, ale niekoniecznie oraz angielskiej analizator Lucene firmy lub analizatorem angielskiej firmy Microsoft. 
 
+ Angielski analizator Lucene firmy rozszerza standardowe analizatora. Jego wycofanie Zaimki dzierżawcze (końcowe firmy) z słów, ma zastosowanie, analiza słowotwórcza zgodnie z algorytmu Porter wynikające oraz usunięcie słowa ignorowane w języku angielskim.  

+ Firmy Microsoft w języku angielskim analizatora wykonuje Lematyzacja zamiast wynikające. Oznacza to, że może obsługiwać formularze odmienione i nieregularne słów znacznie lepiej co powoduje bardziej odpowiednie wyniki wyszukiwania 

  > [!Tip]
  > [Wersja demonstracyjna analizatora wyszukiwania](https://alice.unearth.ai/) zapewnia porównania side-by-side wyników generowanych przez standardowy analizator Lucene, Lucene w języku angielskim analizator i procesora angielskiej języka naturalnego firmy Microsoft. Dla każdego dane wejściowe zostaną podane, wyniki z każdego analizatora są wyświetlane w sąsiadujących okienka.

## <a name="configuring-analyzers"></a>Konfigurowanie analizatorów

Analizatory języków są używane jako-to. Dla każdego pola w definicji indeksu, można ustawić **analizatora** Właściwość Nazwa analizatora, określający stos języka i odpowiednie (Microsoft lub Lucene). Ten sam analizator zostaną zastosowane podczas indeksowania i wyszukiwania dla tego pola. Na przykład można mieć oddzielnymi polami w języku angielskim, francuskim i hiszpańskim opisy hotelu znajdujące się obok siebie w tym samym indeksie. Alternatywnie zamiast z **analizatora**, możesz użyć **indexAnalyzer** i **searchAnalyzer** mieć reguły analizy różnych na indeksowanie czas i czas wykonywania zapytania. 

Użyj **searchFields** zapytania parametru, aby określić które pole specyficzne dla języka, aby przeprowadzać wyszukiwanie w zapytaniach. Możesz przejrzeć przykłady zapytania, które będą zawierać właściwości analizator w [wyszukiwania dokumentów](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Aby uzyskać więcej informacji na temat właściwości indeksu, zobacz [Create Index &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index). Aby uzyskać więcej informacji na temat analizy w usłudze Azure Search, zobacz [analizatory w usłudze Azure Search](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista analizatora języków 
 Poniżej przedstawiono listę obsługiwanych języków wraz z nazwami analizator Lucene i firmy Microsoft.  

|Język|Nazwa analizatora firmy Microsoft|Nazwa analizator Lucene|  
|--------------|-----------------------------|--------------------------|  
|Arabski|ar.microsoft|ar.lucene|  
|Armeński||HY.lucene|  
|Bengalski|bn.microsoft||  
|Baskijski||eu.lucene|  
|Bułgarski|bg.microsoft|BG.lucene|  
|Kataloński|ca.microsoft|CA.lucene|  
|Chiński (uproszczony)|zh-Hans.microsoft|zh-Hans.lucene|  
|Chiński tradycyjny|zh-Hant.microsoft|zh-Hant.lucene|  
|Chorwacki|hr.microsoft||  
|Czeski|cs.microsoft|cs.lucene|  
|Duński|da.microsoft|da.lucene|  
|Holenderski|nl.microsoft|nl.lucene|  
|Polski|en.microsoft|EN.lucene|  
|Estoński|et.microsoft||  
|Fiński|fi.microsoft|fi.lucene|  
|Francuski|fr.microsoft|fr.lucene|  
|Galicyjski||gl.lucene|  
|Niemiecki|de.microsoft|de.lucene|  
|Grecki|el.microsoft|el.lucene|  
|Gudżarati|gu.microsoft||  
|Hebrajski|he.microsoft||  
|Hindi|hi.microsoft|Hi.lucene|  
|Węgierski|hu.microsoft|HU.lucene|  
|Islandzki|is.microsoft||  
|Indonezyjski (Bahasa)|id.microsoft|id.lucene|  
|Irlandzki||ga.lucene|  
|Włoski|it.microsoft|it.lucene|  
|Japoński|ja.microsoft|ja.lucene|  
|Kannada|kn.microsoft||  
|Koreański|ko.microsoft|ko.lucene|  
|Łotewski|lv.microsoft|lv.lucene|  
|Litewski|lt.microsoft||  
|Malajalam|ml.microsoft||  
|Malajski (łaciński)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norweski|nb.microsoft|no.lucene|  
|Perski||fa.lucene|  
|Polski|pl.microsoft|pl.lucene|  
|Portugalski (Brazylia)|pt-Br.microsoft|pt-Br.lucene|  
|Portugalski (Portugalia)|pt-Pt.microsoft|pt-Pt.lucene|  
|Pendżabski|pa.microsoft||  
|Rumuński|ro.microsoft|ro.lucene|  
|Rosyjski|ru.microsoft|ru.lucene|  
|Serbski (cyrylica)|sr-cyrillic.microsoft||  
|Serbski (łaciński)|sr-latin.microsoft||  
|Słowacki|sk.microsoft||  
|Słoweński|sl.microsoft||  
|Hiszpański|es.microsoft|es.lucene|  
|Szwedzki|sv.microsoft|sv.lucene|  
|Tamilski|ta.microsoft||  
|Telugu|te.microsoft||  
|Tajlandzki|th.microsoft|th.lucene|  
|Turecki|tr.microsoft|TR.lucene|  
|Ukraiński|uk.microsoft||  
|Urdu|ur.microsoft||  
|Wietnamski|vi.microsoft||  

 Wszystkie analizatory z nazwami, oznaczony za pomocą **Lucene** są obsługiwane przez [analizatory języka Apache Lucene](https://lucene.apache.org/core/4_9_0/core/overview-summary.html ).

## <a name="see-also"></a>Zobacz także  
 [Tworzenie indeksu &#40;interfejsu API REST usługi Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
 [Klasa AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  
 [Wideo: Moduł 7 prezentacji usługi Azure Search w witrynie MVA](https://channel9.msdn.com/Series/Adding-Microsoft-Azure-Search-to-Your-Websites-and-Apps/07).  

