---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 065cb4daa9501ee658d364dad43b9e03798e4083
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183645"
---
Zadanie tworzy plik wyjściowy JSON, który zawiera metadane dotyczące wykrytych i śledzonych twarzy. Metadane zawierają współrzędne wskazujące położenie twarzy, a także numer identyfikatora twarzy wskazujący śledzenie tej osoby. Numery identyfikatorów twarzy są podatne na resetowanie w warunkach utraty lub nakładania się czołowej ściany w ramce, co powoduje, że niektóre osoby otrzymują przypisane wiele identyfikatorów.

Wyjście JSON zawiera następujące elementy:

### <a name="root-json-elements"></a>Główne elementy JSON

| Element | Opis |
| --- | --- |
| version |Odnosi się to do wersji interfejsu API wideo. |
| skala czasu |"Kleszcze" na sekundę filmu. |
| przesunięcie |Jest to przesunięcie czasu dla znaczników czasu. W wersji 1.0 interfejsów API wideo zawsze będzie to 0. W przyszłych scenariuszach, które obsługujemy, ta wartość może ulec zmianie. |
| szerokość, wysokość |Szerokość i wysokość wyjściowej klatki wideo w pikselach.|
| Ilość klatek |Liczba klatek na sekundę w wideo. |
| [Fragmenty](#fragments-json-elements) |Metadane są podzielone na różne segmenty zwane fragmentami. Każdy fragment zawiera rozpoczęcie, czas trwania, wartość interwału i zdarzenia. |

### <a name="fragments-json-elements"></a>Fragmenty elementów JSON

|Element|Opis|
|---|---|
| rozpoczynanie |Godzina rozpoczęcia pierwszego zdarzenia w "kleszczach". |
| czas trwania |Długość fragmentu w "kleszczach". |
| indeks | (Dotyczy tylko usługi Azure Media Redactor) definiuje indeks ramki bieżącego zdarzenia. |
| interval |Interwał każdego wpisu zdarzenia w obrębie fragmentu w "kleszcze". |
| zdarzenia |Każde zdarzenie zawiera twarze wykryte i śledzone w tym czasie. Jest to tablica zdarzeń. Zewnętrzna tablica reprezentuje jeden interwał czasu. Wewnętrzna tablica składa się z 0 lub większej liczby zdarzeń, które wystąpiły w danym momencie. Pusty nawias [] oznacza, że nie wykryto żadnych ścian. |
| id |Identyfikator ściany, która jest śledzona. Liczba ta może przypadkowo zmienić, jeśli twarz staje się niewykryte. Dana osoba powinna mieć ten sam identyfikator w całym filmie, ale nie może to być zagwarantowane ze względu na ograniczenia w algorytmie wykrywania (okluzja, itp.). |
| x, y |Koordynujące się współrzędne X i Y górnej granicy powierzchni w znormalizowanej skali od 0,0 do 1,0. <br/>Współrzędne X i Y są zawsze względem krajobrazu, więc jeśli masz film portretowy (lub do góry nogami, w przypadku systemu iOS), będziesz musiał odpowiednio przetransponować współrzędne. |
| szerokość, wysokość |Szerokość i wysokość obwiedni ściany w znormalizowanej skali od 0,0 do 1,0. |
| twarzeDetected |Znajduje się to na końcu wyników JSON i podsumowuje liczbę twarzy, które algorytm wykryte podczas wideo. Ponieważ identyfikatory można zresetować przypadkowo, jeśli twarz zostanie niewykryta (np. twarz zgaśnie, odamiewa), liczba ta nie zawsze może być równa prawdziwej liczbie twarzy w filmie. |
