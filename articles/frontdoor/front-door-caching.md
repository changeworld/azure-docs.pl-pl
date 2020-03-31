---
title: Drzwi frontowe platformy Azure — buforowanie | Dokumenty firmy Microsoft
description: Ten artykuł pomaga zrozumieć, jak usługi Azure Front Door monitoruje kondycję zaplecza
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: d4fed878e2c0b1430e963f43743fd772493d3270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471748"
---
# <a name="caching-with-azure-front-door"></a>Buforowanie za pomocą drzwi wejściowych platformy Azure
Poniższy dokument określa zachowanie drzwi ami dla linii frontowych z regułami routingu, które umożliwiły buforowanie. Drzwi frontowe to nowoczesna sieć dostarczania treści (CDN), a więc wraz z dynamicznym przyspieszaniem witryny i równoważeniem obciążenia, obsługuje również zachowania buforowania, tak jak każda inna sieć CDN.

## <a name="delivery-of-large-files"></a>Dostarczanie dużych plików
Usługa Azure Front Door dostarcza duże pliki bez ograniczenia rozmiaru pliku. Drzwi frontowe używa techniki zwanej fragmentowanie obiektu. Jeśli są żądane duże pliki, usługa Front Door pobiera mniejsze części pliku z zaplecza. Po otrzymaniu żądania dotyczącego całego pliku lub zakresu bajtów pliku usługa Front Door żąda pliku z zaplecza we fragmentach po 8 MB.

</br>Po fragment dociera do środowiska drzwi frontowych, jest buforowany i natychmiast serwowane do użytkownika. Drzwi przednie następnie wstępnie pobiera następny fragment równolegle. To pobieranie wstępne zapewnia, że zawartość pozostaje jeden fragment przed użytkownikiem, co zmniejsza opóźnienia. Ten proces jest kontynuowany do momentu pobrania całego pliku (jeśli jest to wymagane), wszystkie zakresy bajtów są dostępne (jeśli jest to wymagane) lub klient zakończy połączenie.

</br>Aby uzyskać więcej informacji na temat żądania zakresu bajtów, przeczytaj [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
Drzwi frontowe buforują wszystkie fragmenty podczas ich odtworzenia, a więc cały plik nie musi być buforowany w pamięci podręcznej drzwi frontowych. Kolejne żądania dla zakresów plików lub bajtów są obsługiwane z pamięci podręcznej. Jeśli nie wszystkie fragmenty są buforowane, pobieranie wstępne jest używane do żądania fragmentów z wewnętrznej bazy danych. Ta optymalizacja opiera się na zdolności wewnętrznej bazy danych do obsługi żądań zakresu bajtów; Jeśli wewnętrznej bazy danych nie obsługuje żądań zakresu bajtów, ta optymalizacja nie jest skuteczna.

## <a name="file-compression"></a>Kompresja plików
Drzwi frontowe mogą dynamicznie kompresować zawartość na krawędzi, co przekłada się na mniejszą i szybszą reakcję na klientów. Wszystkie pliki kwalifikują się do kompresji. Jednak plik musi być typu MIME, który kwalifikuje się do listy kompresji. Obecnie drzwi frontowe nie zezwalają na zmianę tej listy. Bieżąca lista jest:</br>
- "aplikacja/eot"
- "aplikacja/czcionka"
- "aplikacja/font-sfnt"
- "aplikacja/javascript"
- „application/json”
- "aplikacja/opentype"
- "application/otf"
- "aplikacja/pkcs7-mime"
- "aplikacja/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "aplikacja/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "aplikacja/x-otf"
- "application/x-perl"
- "aplikacja/x-ttf"
- "application/x-javascript"
- "czcionka/eot"
- "czcionka/ttf"
- "font/otf"
- "czcionka/opentype"
- "image/svg+xml"
- "tekst/css"
- "text/csv"
- "tekst/html"
- "text/javascript"
- "text/js", "text/plain"
- "tekst/materiały sformatowe"
- "text/tab-separated-values" "text/tab-separated-values" "text/tab-separated-values" "text
- "tekst/xml"
- "text/x-script"
- "składnik tekstowy/x"
- "text/x-java-source"

Ponadto plik musi być również od 1 KB do 8 MB w rozmiarze włącznie.

Profile te obsługują następujące kodowania kompresji:
- [Gzip (zamek błyskawiczny GNU)](https://en.wikipedia.org/wiki/Gzip)
- [Okręg wyborczy Brotli](https://en.wikipedia.org/wiki/Brotli)

Jeśli żądanie obsługuje kompresję gzip i Brotli, pierwszeństwo ma kompresja Brotli.</br>
Gdy żądanie zasobu określa kompresję, a żądanie powoduje pominięcie w pamięci podręcznej, drzwiami frontowymi wykonują kompresję zasobu bezpośrednio na serwerze POP. Następnie skompresowany plik jest obsługiwany z pamięci podręcznej. Wynikowy element jest zwracany z kodowaniem transferu: fragmentowany.

## <a name="query-string-behavior"></a>Zachowanie ciągu zapytania
Za pomocą drzwi ami frontowymi można kontrolować sposób buforowania plików dla żądania sieci web zawierającego ciąg zapytania. W żądaniu sieci web z ciągiem zapytania ciąg zapytania jest tą częścią żądania, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać jedną lub więcej par klucz-wartość, w których nazwa pola i jego wartość są oddzielone znakiem równości (=). Każda para klucz-wartość jest oddzielona ampersand (&). Na przykład `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Jeśli istnieje więcej niż jedna para klucz-wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia.
- **Ignoruj ciągi zapytań:** Tryb domyślny. W tym trybie drzwiami frontowymi przekazuje ciągi zapytania z żądacza do wewnętrznej bazy danych w pierwszym żądaniu i buforuje zasób. Wszystkie kolejne żądania dla zasobu, które są obsługiwane ze środowiska drzwiami frontowymi ignorują ciągi zapytań, dopóki zasób w pamięci podręcznej nie wygaśnie.

- **Buforuj każdy unikatowy adres URL:** W tym trybie każde żądanie z unikatowym adresem URL, w tym ciągiem zapytania, jest traktowane jako unikatowy zasób z własną pamięcią podręczną. Na przykład odpowiedź z wewnętrznej bazy danych `www.example.ashx?q=test1` dla żądania jest buforowany w środowisku drzwiami frontowymi i zwracany dla kolejnych pamięci podręcznych z tym samym ciągiem zapytania. Żądanie `www.example.ashx?q=test2` jest buforowane jako oddzielny zasób z własnym ustawieniem czasu do żywo.

## <a name="cache-purge"></a>Przeczyszczanie pamięci podręcznej
Drzwi frontowe będą buforować zasoby do czasu wygaśnięcia czasu wygaśnięcia zasobu.Front Door will cache assets until the asset's time-to-live (TTL) expire. Po wygaśnięciu czasu wygaśnięcia zasobu, gdy klient zażąda zasobu, środowisko drzwi frontowych pobierze nową zaktualizowaną kopię zasobu, aby obsługiwać żądanie klienta i przechowywać odświeżanie pamięci podręcznej.
</br>Najlepszym rozwiązaniem, aby upewnić się, że użytkownicy zawsze uzyskać najnowszą kopię zasobów jest wersja zasobów dla każdej aktualizacji i opublikować je jako nowe adresy URL. Drzwi frontowe natychmiast pobierzą nowe zasoby dla następnych żądań klientów. Czasami można przeczyścić buforowaną zawartość ze wszystkich węzłów brzegowych i wymusić na nich wszystkie pobieranie nowych zaktualizowanych zasobów. Może to być spowodowane aktualizacjami aplikacji sieci web lub szybkim zaktualizowaniem zasobów zawierających nieprawidłowe informacje.

</br>Wybierz zasoby, które mają być przeczyszczane z węzłów krawędzi. Jeśli chcesz wyczyścić wszystkie zasoby, kliknij pole wyboru Przeczyść wszystkie. W przeciwnym razie wpisz ścieżkę każdego zasobu, który chcesz przeczyścić w pola tekstowym Ścieżka. Poniższe formaty są obsługiwane w ścieżce.
1. **Przeczyszczanie pojedynczej ścieżki**: Przeczyszcz poszczególne zasoby, określając pełną ścieżkę zasobu (bez protokołu i domeny), z rozszerzeniem pliku, na przykład /pictures/strasbourg.png;
2. **Przeczyszczanie symboli wieloznacznych**: Asterisk (\*) może być użyte jako symbol wieloznaczny. Przeczyść wszystkie foldery, podfoldery i pliki\* pod punktem końcowym z / w ścieżce lub przeczyść wszystkie\*podfoldery i pliki\*w określonym folderze, określając folder, po którym następuje / , na przykład /pictures/ .
3. **Przeczyszczanie domeny głównej**: Przeczyść katalog główny punktu końcowego z "/" w ścieżce.

Przeczyszczania pamięci podręcznej na drzwiach frontowych są niewrażliwe na argumenty. Ponadto są one niezależna od ciągów zapytań, co oznacza, że przeczyszczanie adresu URL spowoduje przeczyszczenie wszystkich jego odmian ciągów zapytań. 

## <a name="cache-expiration"></a>Wygaśnięcie pamięci podręcznej
Następująca kolejność nagłówków jest używana w celu określenia, jak długo element będzie przechowywany w naszej pamięci podręcznej:</br>
1. Cache-Control: s-maxage\<= sekundy>
2. Cache-Control: max-age\<= sekundy>
3. Wygasa: \<http-date>

Nagłówki odpowiedzi kontroli pamięci podręcznej, które wskazują, że odpowiedź nie będzie buforowana, takie jak Cache-Control: private, Cache-Control: no-cache, i Cache-Control: brak magazynu są honorowane. Jeśli jednak w locie znajduje się wiele żądań w locie dla tego samego adresu URL, mogą one udostępnić odpowiedź. Jeśli nie cache-control jest obecny domyślne zachowanie jest, że AFD będzie buforować zasób przez X ilość czasu, gdzie X jest losowo zbierane między 1 do 3 dni.

## <a name="request-headers"></a>Nagłówki żądań

Następujące nagłówki żądań nie zostaną przekazane do wewnętrznej bazy danych podczas buforowania.
- Długość zawartości
- Kodowanie transferu

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
