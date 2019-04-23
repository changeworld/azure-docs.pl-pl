---
title: Usługa Azure drzwiami frontowymi — pamięć podręczna | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak usługa drzwiami frontowymi Azure monitoruje kondycję usługi zaplecza
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
ms.openlocfilehash: fb00c09680c6a80bc093c6f02ccd8d7269b8748c
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149482"
---
# <a name="caching-with-azure-front-door-service"></a>Buforowanie przy użyciu usługi Azure drzwi
Poniższy dokument określa zachowanie drzwiami frontowymi za pomocą reguł routingu, które mają włączone buforowanie.

## <a name="delivery-of-large-files"></a>Dostarczanie dużych plików
Usługa drzwiami frontowymi zapewnia duże pliki bez limitu rozmiaru pliku. Drzwiami frontowymi wykorzystuje technikę o nazwie obiekt segmentu. Jeśli są żądane duże pliki, usługa Front Door pobiera mniejsze części pliku z zaplecza. Po otrzymaniu żądania dotyczącego całego pliku lub zakresu bajtów pliku usługa Front Door żąda pliku z zaplecza we fragmentach po 8 MB.

</br>Po fragmencie dociera do środowiska drzwiami frontowymi, pamięci podręcznej i obsłużonych natychmiast użytkownika. Następnie drzwiami frontowymi pobiera wstępnie dalej fragmentów w sposób równoległy. Ta pobierania wstępnego gwarantuje, że zawartość pozostaje w jednym fragmencie w przód od użytkownika, co zmniejsza opóźnienie. Ten proces jest kontynuowany aż do całego pobrany plik (jeśli jest to wymagane), wszystkich zakresów bajtów są dostępne (jeśli jest to wymagane), lub klient zakończy połączenie.

</br>Aby uzyskać więcej informacji na temat żądania zakresu bajtów, przeczytaj [RFC 7233](https://web.archive.org/web/20171009165003/ http://www.rfc-base.org/rfc-7233.html).
Drzwiami frontowymi buforuje wszelkie fragmenty, po odebraniu i dlatego nie trzeba być buforowane w pamięci podręcznej drzwiami frontowymi całego pliku. Kolejne żądania dla pliku lub bajt zakresy są udostępniane z pamięci podręcznej. Jeśli nie wszystkie fragmenty są buforowane, wstępnego pobierania jest używany do zażądania fragmentów z wewnętrznej bazy danych. Tego rodzaju optymalizacji opiera się na możliwość obsługi żądania zakresu bajtów; wewnętrznej bazy danych Jeśli wewnętrznej bazy danych nie obsługuje żądania zakresu bajtów, optymalizacja nie jest skuteczna.

## <a name="file-compression"></a>Kompresja plików
Drzwiami frontowymi można dynamicznie umożliwia kompresję zawartości na urządzeniach brzegowych, wynikiem mniejsze i szybsze odpowiedzi do klientów. Wszystkie pliki kwalifikują się do kompresji. Jednakże plik musi być typu MIME oznacza możliwość skorzystania z listy kompresji. Obecnie drzwiami frontowymi nie zezwala na tej listy, aby go zmienić. Bieżąca lista jest:</br>
- "aplikacja/eot"
- "aplikacja/czcionki"
- "aplikacja/font-sfnt"
- "application/javascript"
- "application/json"
- "aplikacja/opentype"
- "aplikacja/otf"
- "aplikacja/pkcs7-mime"
- "application/truetype"
- "aplikacja/ttf"
- "aplikacja/vnd.ms-fontobject"
- "aplikacja/xhtml i xml"
- "application/xml"
- "application/xml + rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "czcionki/eot"
- "czcionki/ttf"
- "czcionki/otf"
- "czcionki/opentype"
- "image/svg + xml"
- "tekst/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "tekst/js", "text/plain"
- "tekst/richtext"
- "text/tab-separated-values"
- "text/xml"
- "tekst/x skrypt"
- "tekst/x-component"
- "tekst/x-java-source"

Ponadto plik musi być również od 1 KB do 8 MB w rozmiarze włącznie.

Te profile obsługują następujące kodowania kompresji:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Jeśli żądanie obsługuje gzip i Brotli kompresji, pierwszeństwo ma Brotli kompresji.</br>
Gdy żądania dla zasobu określa to Chybienie pamięci podręcznej, kompresji i wyniki żądania, drzwiami frontowymi wykonuje kompresji zasobu bezpośrednio na serwer protokołu POP. W efekcie skompresowanego pliku jest obsługiwany z pamięci podręcznej. Wynikowy element zostaje zwrócony z transfer-encoding: fragmentaryczne.

## <a name="query-string-behavior"></a>Działanie ciągu kwerendy
Za pomocą drzwiami frontowymi można kontrolować, jak pliki są buforowane dla żądania sieci web, który zawiera ciąg zapytania. W żądaniu sieci web za pomocą ciągu zapytania ciąg zapytania jest część żądanie, która występuje po znaku zapytania (?). Ciąg zapytania może zawierać pary klucz wartość, w których nazwy pól i wartości są oddzielone znak równości (=). Każdej pary klucz wartość jest oddzielona handlowe "i" (&). Na przykład http://www.contoso.com/content.mov?field1=value1&field2=value2. Jeśli istnieje więcej niż jedną parę klucz wartość w ciągu zapytania żądania, ich kolejność nie ma znaczenia.
- **Ignoruj ciągi zapytań**: Tryb domyślny. W tym trybie drzwiami frontowymi przekazuje ciągi zapytań z obiekt żądający do wewnętrznej bazy danych na pierwsze żądanie i zapisuje w pamięci podręcznej elementu zawartości. Wszystkie kolejne żądania dla elementu zawartości, które są obsługiwane w środowisku drzwiami frontowymi Ignoruj ciągi zapytań, do momentu wygaśnięcia pamięci podręcznej elementu zawartości.

- **Buforuj każdy unikatowy adres URL**: W tym trybie każde żądanie z unikatowy adres URL, w tym ciągu zapytania jest traktowany jako unikatowy zasobu ze wszystkimi jego własnej pamięci podręcznej. Na przykład odpowiedź z wewnętrznej bazy danych na żądanie dla `www.example.ashx?q=test1` jest zbuforowana w środowisku drzwiami frontowymi i zwrócony dla kolejnych pamięci podręcznych przy użyciu tego samego ciągu zapytania. Żądanie dotyczące `www.example.ashx?q=test2` pamięci podręcznej jako oddzielny składnik aktywów swoje własne ustawienie czasu wygaśnięcia.

## <a name="cache-purge"></a>Wyczyść pamięć podręczną
Drzwi spowoduje zasobów w pamięci podręcznej do momentu wygaśnięcia zasobu czas wygaśnięcia (TTL). Po wygaśnięciu czasu wygaśnięcia zasobu, gdy klient zażąda zawartości, środowiska drzwiami frontowymi powoduje pobranie zaktualizowanych nową kopię zasobu ma obsługiwać żądania klienta i magazynu odświeżyć pamięć podręczną.
</br>Najlepszym rozwiązaniem jest, aby upewnić się, że użytkownicy zawsze uzyskać najnowszą kopię zawartości jest wersja zasobów dla każdej aktualizacji i opublikować je jako nowe adresy URL. Drzwiami frontowymi natychmiast pobierze nowe zasoby dla następnego żądania klienta. Czasami chcesz przeczyścić pamięci podręcznej zawartość ze wszystkich węzłów brzegowych i wymusić na nich wszystkich pobrać nowe zaktualizowane zasoby. Może to być spowodowane aktualizacje do aplikacji sieci web lub szybko zasobów aktualizacji, które zawierają nieprawidłowe informacje.

</br>Wybierz zasoby, które chcesz przeczyścić z węzłów krawędzi. Jeśli chcesz wyczyścić wszystkie zasoby, kliknij przycisk przeczyszczanie wszystkie pola wyboru. W przeciwnym razie wpisz ścieżkę do każdego elementu zawartości, który chcesz przeczyścić w polu tekstowym Ścieżka. Poniższe formaty są obsługiwane w ścieżce.
1. **Pojedynczy adres URL przeczyszczania**: Przeczyść poszczególnych zasobów, określając pełny adres URL z rozszerzeniem pliku, na przykład /pictures/strasbourg.png;
2. **Symbol wieloznaczny przeczyszczania**: Gwiazdka (\*) może być używana jako symbol wieloznaczny. Przeczyść wszystkich folderów, podfolderów i plików w ramach punktu końcowego za pomocą /\* w ścieżce lub Przeczyść wszystkie podfoldery i pliki w określonym folderze, określając folder następuje /\*, na przykład /zdjęcia/miasto.PNG\*.
3. **Przeczyszczanie domeny katalogu głównego**: Przeczyszczanie punktu końcowego "/" w ścieżce katalogu głównego.

Powoduje usunięcie pamięci podręcznej, na wejściu jest rozróżniana wielkość liter. Ponadto są niezależne od ciąg zapytania, co oznacza, że przeczyszczanie adresu URL spowoduje to wyczyszczenie wszystkie odmiany ciągu zapytania o. 

## <a name="cache-expiration"></a>Wygaśnięcia pamięci podręcznej
Kolejność następujące nagłówki jest używana w celu określenia, jak długo element będzie przechowywane w naszych pamięci podręcznej:</br>
1. Cache-Control: s-maxage =\<sekund >
2. Cache-Control: maxage =\<sekund >
3. Wygasa: \<Data http >

Nagłówki Cache-Control odpowiedzi, które wskazują, że odpowiedzi nie będą buforowane takich jak Cache-Control: Cache-Control prywatne: nie pamięci podręcznej i Cache-Control: nie-store są uznawane. Jednak w przypadku wielu żądań locie przez usługę POP dla tego samego adresu URL, może udostępniać odpowiedzi. Jeżeli Cache-Control ma domyślne zachowanie jest AFD będzie buforowania przez zasób dla X czas, gdzie X jest pobierana losowo od 1 do 3 dni.


## <a name="request-headers"></a>Nagłówki żądań

Następujące nagłówki żądania nie zostaną przekazane do wewnętrznej bazy danych, korzystając z buforowania.
- Autoryzacja
- Długość zawartości
- Transfer-Encoding

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
