---
title: Skalowanie przesyłania strumieniowego przy użyciu usługi CDN
titleSuffix: Azure Media Services
description: Dowiedz się więcej o usłudze przesyłania strumieniowego, która dostarcza zawartość bezpośrednio do aplikacji odtwarzacza klienta lub do Content Delivery Network (CDN).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: 90fa3b06e2696e9b45c333c75c8a8e117d5c0c96
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563117"
---
# <a name="scaling-streaming-with-cdn"></a>Skalowanie przesyłania strumieniowego przy użyciu usługi CDN

Usługa Azure Content Delivery Network (CDN) oferuje deweloperom globalne rozwiązanie umożliwiające szybkie dostarczanie użytkownikom zawartości wymagającej wysokiej przepustowości przez zapisywanie zawartości w pamięci podręcznej w węzłach fizycznych strategicznie rozmieszczonych na całym świecie.  

Usługa CDN buforuje zawartość przesyłaną strumieniowo z [punktu końcowego Media Services streaming (Origin)](streaming-endpoint-concept.md) na koder-dekoder, na protokół przesyłania strumieniowego, za szybkość transmisji bitów na format kontenera oraz na szyfrowanie/DRM. Dla każdej kombinacji kodera-strumieniowego protokołu-Stream — format kontenera — szybkość transmisji bitów — szyfrowanie będzie mieć osobną pamięć podręczną usługi CDN. 

Popularna zawartość będzie obsługiwana bezpośrednio z pamięci podręcznej usługi CDN, o ile fragment wideo jest buforowany. Zawartość na żywo jest prawdopodobnie w pamięci podręcznej, ponieważ zazwyczaj masz wiele osób oglądających dokładnie te same rzeczy. Zawartość na żądanie może być trickier bitowym, ponieważ istnieje pewna zawartość, która jest najbardziej popularna, a niektóre z nich nie są. Jeśli masz miliony zasobów wideo, które nie są popularne (tylko jeden lub dwa osoby przeglądające w tygodniu), ale masz tysiące osób oglądających wszystkie różne wideo, Sieć CDN jest znacznie mniej skuteczna. 

Należy również wziąć pod uwagę sposób działania adaptacyjnego przesyłania strumieniowego. Każdy pojedynczy fragment wideo jest buforowany jako jego obiekt własny. Na przykład załóżmy, że po raz pierwszy oglądasz film wideo. Jeśli podgląd pominie około zaledwie kilku sekund, a tylko fragmenty wideo skojarzone z tym, co ktoś ogląda w pamięci podręcznej w usłudze CDN. Dzięki adaptacyjnemu przesyłaniu strumieniowym zazwyczaj masz od 5 do 7 różnych szybkości transmisji wideo. Jeśli jedna osoba ogląda jedną szybkość transmisji bitów, a inna osoba ogląda inną szybkość transmisji bitów, to każda z nich jest buforowana osobno w sieci CDN. Nawet jeśli dwie osoby oglądają tę samą szybkość transmisji bitów, mogą one być przesyłane strumieniowo za pośrednictwem różnych protokołów. Każdy protokół (HLS, MPEG-KRESKa, Smooth Streaming) jest buforowany osobno. Dlatego każda szybkość transmisji bitów i protokół są buforowane oddzielnie, a tylko te fragmenty wideo, które zostały żądane, są buforowane.

Przy podejmowaniu decyzji o tym, czy włączyć usługę CDN w [punkcie końcowym przesyłania strumieniowego](streaming-endpoint-concept.md)Media Services, weź pod uwagę liczbę zaplanowanych osób przeglądających. Sieć CDN jest pomocna tylko w przypadku, gdy przewidujesz wiele podglądów zawartości. Jeśli maksymalne współbieżność podglądu jest mniejsze niż 500, zaleca się wyłączenie usługi CDN, ponieważ usługa CDN jest optymalna dla współbieżności. 

W tym temacie omówiono Włączanie integracji z siecią [CDN](#enable-azure-cdn-integration). Opisano w nim również wstępne pobieranie (aktywne buforowanie) i koncepcję z [wyprzedzeniem](#origin-assist-cdn-prefetch) dotyczącej usługi CDN.

## <a name="considerations"></a>Zagadnienia do rozważenia

* [Punkt końcowy przesyłania strumieniowego](streaming-endpoint-concept.md) `hostname` i adres URL przesyłania strumieniowego pozostają takie same niezależnie od tego, czy jest włączona sieć CDN.
* Jeśli potrzebujesz możliwości testowania zawartości z użyciem usługi CDN lub bez niej, Utwórz inny punkt końcowy przesyłania strumieniowego, który nie jest włączony w sieci CDN.

## <a name="enable-azure-cdn-integration"></a>Włącz integrację Azure CDN

> [!IMPORTANT]
> Nie można włączyć usługi CDN dla kont platformy Azure z wersji próbnej lub ucznia.
>
> Integracja z usługą CDN jest włączona we wszystkich centrach danych platformy Azure z wyjątkiem regionów federalnych i w Chinach.

Po udostępnieniu punktu końcowego przesyłania strumieniowego z włączoną usługą CDN jest zdefiniowany czas oczekiwania na Media Services przed aktualizacją usługi DNS w celu zamapowania punktu końcowego przesyłania strumieniowego na punkt końcowy usługi CDN.

Jeśli zechcesz później wyłączyć/włączyć usługę CDN, punkt końcowy przesyłania strumieniowego musi znajdować się w stanie **zatrzymanym** . Włączenie integracji Azure CDN może potrwać do dwóch godzin, a zmiany zostaną uaktywnione we wszystkich punktach pop usługi CDN. Można jednak uruchomić punkt końcowy przesyłania strumieniowego i strumień bez przerw w punkcie końcowym przesyłania strumieniowego, a po zakończeniu integracji zostanie dostarczony strumień z sieci CDN. W trakcie okresu aprowizacji punkt końcowy przesyłania strumieniowego będzie w stanie **uruchomienia** i może wystąpić spadek wydajności.

Po utworzeniu standardowego punktu końcowego przesyłania strumieniowego jest on konfigurowany domyślnie przy użyciu standardowego Verizon. Korzystając z interfejsów API REST, można skonfigurować Verizon Premium lub standardowego dostawcy Akamai.

Integracja Azure Media Services z Azure CDN jest zaimplementowana w **Azure CDN z Verizon** dla standardowych punktów końcowych przesyłania strumieniowego. Punkty końcowe przesyłania strumieniowego w warstwie Premium można skonfigurować przy użyciu wszystkich **Azure CDN warstw cenowych i dostawców**. 

> [!NOTE]
> Aby uzyskać szczegółowe informacje na temat Azure CDN, zobacz [Omówienie usługi CDN](../../cdn/cdn-overview.md).

## <a name="determine-if-a-dns-change-was-made"></a>Ustal, czy wprowadzono zmianę w systemie DNS

Można określić, czy zmiana DNS została wprowadzona w punkcie końcowym przesyłania strumieniowego (ruch jest kierowany do Azure CDN) przy użyciu https://www.digwebinterface.com. Jeśli w wynikach pojawią się azureedge.net nazwy domen, ruch jest teraz wskazywany do sieci CDN.

## <a name="origin-assist-cdn-prefetch"></a>Pochodzenie — pomoc w sieci CDN — pobieranie z wyprzedzeniem

Buforowanie sieci CDN jest procesem reaktywnym. Jeśli sieć CDN będzie mogła przewidzieć żądanie następnego obiektu, Sieć CDN może zażądać zażądania i przechować następny obiekt w pamięci podręcznej. Dzięki temu procesowi można uzyskać trafienie pamięci podręcznej dla wszystkich (lub większości) obiektów, a tym samym zwiększyć wydajność.

Koncepcja pobierania z wyprzedzeniem dąży do położenia obiektów na "krawędzi Internetu" w przewidywaniu, że zostaną one zlecone przez odtwarzacz, co skraca czas na dostarczenie tego obiektu do odtwarzacza.

Aby osiągnąć ten cel, punkt końcowy przesyłania strumieniowego (Źródło) i sieć CDN muszą być dostępne prawidłowo: 

- Pochodzenie Media Services musi mieć "inteligencję" (Źródło), aby poinformować sieć CDN o następnym obiekcie do pobrania z wyprzedzeniem, a 
- Usługa CDN umożliwia pobieranie z wyprzedzeniem i buforowanie (część usługi CDN — pobieranie z wyprzedzeniem). Usługa CDN musi również mieć "inteligencję", aby poinformować o pochodzeniu niezależnie od tego, czy jest to pobieranie z wyprzedzeniem, czy regularne pobieranie, obsługa odpowiedzi 404 i sposób uniknięcia nieskończonej pętli pobierania z wyprzedzeniem.

### <a name="benefits"></a>Korzyści

Zalety funkcji pobierania z *wyprzedzeniem usługi CDN* to:

- Pobieranie z wyprzedzeniem poprawia jakość odtwarzania wideo przez: wstępne pozycjonowanie przewidywanych segmentów wideo na krawędzi podczas odtwarzania, zmniejszanie opóźnień do podglądu i ulepszanie czasów pobierania segmentów wideo. Powoduje to przyspieszenie czasu uruchamiania wideo i zmniejszenie wystąpień ponownego buforowania.
- Koncepcja ta ma zastosowanie do ogólnego scenariusza sieci CDN, a nie do nośników.
- Akamai dodał tę funkcję do usługi [Akamai Cloud embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html).

> [!NOTE]
> Ta funkcja nie ma jeszcze zastosowania do usługi CDN Akamai zintegrowanej z punktem końcowym usługi Media Services Streaming. Jest to jednak dostępne dla Media Services klientów, którzy mają już istniejący kontrakt Akamai i wymagają niestandardowej integracji między Akamai CDN i pochodzeniem Media Services.

### <a name="how-it-works"></a>Jak to działa

Obsługa sieci CDN dla nagłówków pobranych za pomocą *usługi CDN (* w przypadku przesyłania strumieniowego na żywo i wideo na żądanie) jest dostępna dla klientów z bezpośrednią umową z usługą Akamai CDN. Funkcja obejmuje następujące wymiany nagłówka HTTP między Akamai CDN i pochodzeniem Media Services:

|Nagłówek HTTP|Wartości|Nadawca|Odbiornik|Przeznaczenie|
| ---- | ---- | ---- | ---- | ----- |
|Sieć CDN-pochodzenie-pomoc — z obsługą pobierania z wyprzedzeniem | 1 (wartość domyślna) lub 0 |CDN|Origin|Aby wskazać, że Usługa CDN jest włączona z wyprzedzeniem|
|CDN-Origin-Assist-Prefetch-Path| Przykład: <br/>Fragmenty (wideo = 1400000000, format = MPD-Time-CMAF)|Origin|CDN|Aby zapewnić ścieżkę pobierania z wyprzedzeniem do sieci CDN|
|Sieć CDN — pochodzenie-pomoc-pobieranie z wyprzedzeniem|1 (żądanie pobrania z wyprzedzeniem) lub 0 (regularne żądanie)|CDN|Origin|Aby wskazać żądanie z usługi CDN, należy wykonać pobieranie z wyprzedzeniem|

Aby wyświetlić część działania wymiany nagłówka, możesz spróbować wykonać następujące czynności:

1. Użyj programu Poster lub zwinięciea, aby wydać żądanie do pochodzenie Media Services dla segmentu audio lub wideo lub fragmentu. Pamiętaj o dodaniu nagłówka CDN-Origin-Assist-Prefetch-Enabled: 1 w żądaniu.
2. W odpowiedzi powinna zostać wyświetlona nagłówek CDN-Origin-Assist-Prefetch-Path z ścieżką względną jako wartość.

### <a name="supported-streaming-protocols"></a>Obsługiwane protokoły przesyłania strumieniowego 

Funkcja *pochodzenie* w ramach usługi CDN jest dostępna do obsługi następujących protokołów przesyłania strumieniowego na żywo i przesyłania strumieniowego na żądanie:

* HLS v3
* HLS v4
* HLS CMAF
* KRESKA (CSF)
* KRESKA (CMAF)
* Płynne przesyłanie strumieniowe

### <a name="faqs"></a>Często zadawane pytania

* Co zrobić, jeśli adres URL ścieżki pobierania z wyprzedzeniem jest nieprawidłowy, dzięki czemu pobieranie z wyprzedzeniem przez usługę CDN jest 404? 

    Usługa CDN będzie buforować w pamięci podręcznej odpowiedź 404 przez 10 sekund (lub inną skonfigurowaną wartość).
* Załóżmy, że masz wideo na żądanie. Jeśli Usługa CDN — pobieranie z wyprzedzeniem jest włączone, funkcja ta określa, że gdy klient zażąda pierwszego segmentu wideo, pobieranie z wyprzedzeniem spowoduje uruchomienie pętli dla wszystkich kolejnych segmentów wideo z tą samą szybkością transmisji bitów? 

    Nie, Usługa CDN — pobieranie z wyprzedzeniem jest wykonywane dopiero po zainicjowaniu żądania/odpowiedzi przez klienta. Usługa CDN — pobieranie z wyprzedzeniem nigdy nie jest wyzwalane przez pobieranie z wyprzedzeniem, aby uniknąć pętli pobierania z wyprzedzeniem. 
* Czy funkcja pobierania z wyprzedzeniem usługi CDN jest zawsze włączona? Jak można ją włączyć/wyłączyć? 

    Ta funkcja jest domyślnie wyłączona. Klienci muszą ją włączyć za pośrednictwem interfejsu API Akamai.
* W przypadku przesyłania strumieniowego na żywo, co się stało z pomocą, jeśli następny segment lub fragment nie jest jeszcze dostępny? 

    W takim przypadku pochodzenie Media Services nie będzie dostarczać nagłówka sieci CDN--------------------------
* Jak działa *pochodzenie* z użyciem usługi CDN z dynamicznymi filtrami manifestu? 

    Ta funkcja działa niezależnie od filtru manifestu. Gdy Następny fragment jest poza oknem filtru, jego adres URL będzie nadal znajdować się w manifeście pierwotnego klienta, a następnie zwracany jako nagłówek odpowiedzi pobierania z wyprzedzeniem usługi CDN. Oznacza to, że Usługa CDN pobierze adres URL fragmentu, który jest odfiltrowany od KRESKi/HLS/gładki manifest. Jednak odtwarzacz nigdy nie wyśle żądania GET do sieci CDN w celu pobrania tego fragmentu, ponieważ ten fragment nie jest uwzględniony w podzielce/HLS/gładkim manifeście przechowywanym przez odtwarzacz (gracz nie wie o istnieniu fragmentu).
* Czy jest możliwe pobieranie z listy odtwarzania PAUZ/HLS/gładkiego manifestu?

    Do nagłówka z wyprzedzeniem nie dodano ŁĄCZNIKa "No", "HLS Master Playlist", lista odtwarzania HLS Variant ani gładki adres URL manifestu.
* Czy adresy URL pobierania z wyprzedzeniem względne czy bezwzględne? 

    Podczas gdy Usługa CDN Akamai umożliwia obu, pierwotne adresy URL są dostępne tylko dla ścieżki pobierania Media Services z wyprzedzeniem, ponieważ nie ma żadnej widocznej korzyści przy użyciu bezwzględnych adresów URL.
* Czy ta funkcja działa z zawartością chronioną za pomocą technologii DRM?

    Tak, ponieważ ta funkcja działa na poziomie HTTP, nie dekoduje ani nie analizuje żadnego segmentu/fragmentu. Bez względu na to, czy zawartość jest zaszyfrowana, czy nie.
* Czy ta funkcja działa wraz z wstawianiem AD po stronie serwera (SSAI)?
    
    W przypadku zawartości oryginalnej/głównej (oryginalna zawartość wideo przed wstawieniem AD) działa, ponieważ SSAI nie zmienia sygnatury czasowej zawartości źródłowej ze źródła Media Services. Czy ta funkcja działa z zawartością usługi AD, zależy od tego, czy pochodzenie usługi AD obsługuje funkcję pochodzenie. Na przykład, jeśli zawartość usługi AD jest również hostowana w Azure Media Services (tego samego lub oddzielnego pochodzenia), zawartość usługi AD również zostanie pobrana.
* Czy ta funkcja działa z zawartością UHD/HEVC?

    Tak.

## <a name="ask-questions-give-feedback-get-updates"></a>Zadawaj pytania, Przekaż opinię, uzyskaj aktualizacje

Zapoznaj się z artykułem [community Azure Media Services](media-services-community.md) , aby zobaczyć różne sposoby zadawania pytań, przekazać Opinie i uzyskać aktualizacje dotyczące Media Services.

## <a name="next-steps"></a>Następne kroki

W przykładzie [w tym repozytorium](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) przedstawiono sposób uruchamiania domyślnego punktu końcowego przesyłania strumieniowego przy użyciu platformy .NET.
