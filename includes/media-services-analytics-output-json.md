---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66160957"
---
Zadanie tworzy plik danych wyjściowych JSON, który zawiera metadane dotyczące wykryte i śledzone twarzy. Metadane obejmują współrzędne wskazującą lokalizację twarzy, a także numer identyfikacyjny rozpoznawania twarzy, wskazując śledzenie tej osoby. Numery identyfikatorów twarzy są podatne na zresetować w sytuacjach, gdy czołowego twarzy zostanie utracony lub nakładających się w ramce, co w niektórych osób wprowadzenie przypisanych wiele identyfikatorów.

Dane wyjściowe JSON zawiera następujące elementy:

### <a name="root-json-elements"></a>Elementy katalogu głównego JSON

| Element | Opis |
| --- | --- |
| version |Odnosi się do wersji interfejsu API wideo. |
| Skala czasu |"Impulsów" na sekundę filmu wideo. |
| offset |Jest to przesunięcie czasu sygnatur czasowych. W wersji 1.0 interfejsów API Video ta będzie zawsze równa 0. W przyszłości scenariusze, które firma Microsoft obsługuje, ta wartość może ulec zmianie. |
| szerokość, wysoki |Szerokość i wysoki dane wyjściowe klatki wideo, w pikselach.|
| szybkość klatek |Liczba klatek na sekundę w wideo. |
| [fragments](#fragments-json-elements) |Metadane jest podzielony się w różnych segmentach fragmentów. Każdy fragment zawiera rozpoczęcie, czas trwania, wartość interwału i zdarzenia. |

### <a name="fragments-json-elements"></a>Elementy JSON fragmentów

|Element|Opis|
|---|---|
| start |Czas rozpoczęcia pierwsze zdarzenie "taktów." |
| Czas trwania |Długość fragmentu, w "taktów." |
| index | (Dotyczy tylko z usługi Azure Media Redactor) definiuje indeks ramka bieżącego zdarzenia. |
| interval |Interwał każdego wpisu zdarzenia w obrębie fragmentu, w "taktów." |
| zdarzenia |Każde zdarzenie zawiera twarzy wykryte i śledzenia w tym czas trwania. Istnieje szereg zdarzeń. Zewnętrzna tablica reprezentuje jeden interwał czasu. Wewnętrzna tablica składa się z 0 lub większej liczby zdarzeń, które wystąpiły w danym momencie. [] Nawiasu pusty oznacza, że nie wykryto żadnych twarzy. |
| id |Identyfikator twarzy, które jest śledzone. Ten numer spowodować niezamierzoną zmianę, jeśli twarzy staje się niewykryte. Danej osoby powinny mieć ten sam identyfikator w całym ogólną wideo, ale to nie można zagwarantować ze względu na ograniczenia w algorytm wykrywania (zamknięcia itp.). |
| x, y |Lewy górny róg X i współrzędne Y twarzy obwiedni na znormalizowaną skali od 0.0 do 1.0. <br/>-X i Y współrzędne są względne na poziomą zawsze, więc jeśli masz pionowa wideo (lub nogami w przypadku systemu iOS), musisz odpowiednio TRANSPONUJ współrzędne. |
| szerokość, wysokość |Szerokość i wysokość twarzy obwiedni na znormalizowaną skali od 0.0 do 1.0. |
| facesDetected |Znajduje się na końcu wyniki JSON, a zawiera podsumowanie liczby wyświetlanych twarzy algorytm znalezione w trakcie filmu wideo. Ponieważ identyfikatory można zresetować przypadkowo Jeśli twarzy staje się niewykryte (np. twarz wykracza poza ekranem, natychmiast wygląda), ta liczba nie zawsze równa true liczbę twarzy w filmie wideo. |
